---
title: 부하 분산된 집합으로 MySQL 클러스터화 | Microsoft Docs
description: Azure에서 클래식 배포 모델을 사용하여 만든 부하 분산된 고가용성 Linux MySQL 클러스터를 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: bureado
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: e2671def47879e3d4eae000c9084cd458e29b933
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>부하 분산 집합을 사용하여 Linux에서 MySQL 클러스터화
> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 별도의 두 가지 배포 모델, 즉 [Azure Resource Manager](../../../resource-manager-deployment-model.md)와 클래식 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 템플릿](https://azure.microsoft.com/documentation/templates/mysql-replication/)은 MySQL 클러스터를 배포해야 하는 경우에 사용할 수 있습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 문서에서는 Microsoft Azure에서 고가용성 Linux 기반 서비스를 배포하는 데 사용할 수 있는 다양한 접근 방법을 살펴보고, MySQL 서버 고가용성을 입문서로 살펴봅니다. 이 접근 방법을 보여 주는 비디오는 [채널 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)(영문)에서 사용할 수 있습니다.

여기서는 DRBD, Corosync 및 Pacemaker에 기반한 비공유 2개 노드 단일 마스터 MySQL 고가용성 솔루션을 개괄적으로 설명합니다. 한 번에 하나의 노드만 MySQL을 실행합니다. DRBD 리소스를 쓰고 이 리소스에서 읽는 작업도 한 번에 하나의 노드로 제한됩니다.

Microsoft Azure에서 부하 분산 집합을 사용하여 라운드 로빈 기능과 VIP(가상 IP)의 끝점 검색, 제거 및 안정적 복구 기능을 제공하므로 LVS와 같은 VIP 솔루션이 필요하지 않습니다. VIP는 클라우드 서비스를 처음 만들 때 Microsoft Azure에서 할당한 전역으로 라우팅 가능한 IPv4 주소입니다.

MySQL(NBD Cluster, Percona 및 Galera 포함) 및 여러 개의 미들웨어 솔루션([VM Depot](http://vmdepot.msopentech.com)에서 VM으로 사용할 수 있는 하나 이상의 솔루션 포함)에 가능한 다른 아키텍처가 있습니다. 이러한 솔루션이 유니캐스트 및 멀티캐스트/브로드캐스트에서 복제 가능하고 공유 저장소나 다중 네트워크 인터페이스에 의존하지 않는 한, 이러한 시나리오는 Microsoft Azure에 배포하기 쉽습니다.

이러한 클러스터링 아키텍처는 비슷한 방식으로 PostgreSQL 및 OpenLDAP와 같은 다른 제품으로 확장할 수 있습니다. 예를 들어 공유되지 않은 이러한 부하 분산 절차는 다중 마스터 OpenLDAP를 사용하여 성공적으로 테스트되었으며, 채널 9 블로그에서 볼 수 있습니다.

## <a name="get-ready"></a>준비
다음과 같은 리소스와 기능이 필요합니다.

  - 유효한 구독으로 둘 이상의 VM을 만들 수 있는 Microsoft Azure 계정(이 예제에서는 XS를 사용했음)
  - 네트워크 및 서브넷
  - 선호도 그룹
  - 가용성 집합
  - 클라우드 서비스와 동일한 지역에 VHD를 만들고 Linux VM에 연결할 수 있는 기능

### <a name="tested-environment"></a>테스트된 환경
* Ubuntu 13.10
  * DRBD
  * MySQL Server
  * Corosync 및 Pacemaker

### <a name="affinity-group"></a>선호도 그룹
Azure Portal에 로그인하고 **설정**을 선택하고 선호도 그룹을 만들어 솔루션에 대한 선호도 그룹을 만듭니다. 나중에 만들어진 할당된 리소스는 이 선호도 그룹에 할당됩니다.

### <a name="networks"></a>네트워크
새 네트워크가 만들어지고 네트워크 내에 서브넷이 만들어집니다. 이 예제에서는 내부에 /24 서브넷 하나만 있는 10.10.10.0/24 네트워크를 사용합니다.

### <a name="virtual-machines"></a>가상 머신
첫 번째 Ubuntu 13.10 VM이 Endorsed Ubuntu Gallery 이미지를 사용하여 만들어지며 `hadb01`이라고 합니다. 새 클라우드 서비스가 hadb라는 프로세스에서 만들어집니다. 이 이름은 더 많은 리소스가 추가될 때 서비스에서 갖추게 될 공유 부하 분산 특성을 보여 줍니다. `hadb01`을 만드는 작업은 특별하지 않으며 포털을 사용하여 완료됩니다. SSH에 대한 끝점이 자동으로 만들어지고 새 네트워크가 선택됩니다. 이제 VM에 대한 가용성 집합을 만들 수 있습니다.

첫 번째 VM을 만든 후에(기술적으로 클라우드 서비스를 만들 때) 두 번째 VM, `hadb02`를 만듭니다. 두 번째 VM의 경우 포털을 사용하여 갤러리에서 Ubuntu 13.10 VM을 사용하지만 새 클라우드 서비스가 아니라 기존의 `hadb.cloudapp.net` 클라우드 서비스를 사용합니다. 네트워크 및 가용성 집합도 자동으로 선택됩니다. SSH 끝점도 만들어집니다.

두 VM을 모두 만든 후에는 `hadb01`(TCP 22) 및 `hadb02`(Azure에서 자동 할당)에 대한 SSH 포트를 적어둡니다.

### <a name="attached-storage"></a>연결된 저장소
새 디스크를 두 VM에 연결하고 프로세스에서 5GB 디스크를 만듭니다. 디스크는 주 운영 체제 디스크로 사용되는 VHD 컨테이너에 호스팅됩니다. 디스크를 만들어 연결하면 커널에 새 장치가 표시되므로 Linux를 다시 시작할 필요가 없습니다. 이 장치는 대개 `/dev/sdc`입니다. 출력에 대해서는 `dmesg`를 확인합니다.

각 VM에서 `cfdisk`를 사용하여 파티션(기본, Linux 파티션)을 만들고 새 파티션 테이블을 작성합니다. 이 파티션에는 파일 시스템을 만들면 안 됩니다.

## <a name="set-up-the-cluster"></a>클러스터 설정
APT를 사용하여 두 Ubuntu VM에 Corosync, Pacemaker 및 DRBD를 설치합니다. `apt-get`을 사용하여 그렇게 하려면 다음 코드를 실행합니다.

    sudo apt-get install corosync pacemaker drbd8-utils.

지금 MySQL을 설치하면 안 됩니다. Debian 및 Ubuntu 설치 스크립트는 `/var/lib/mysql`에 있는 MySQL 데이터 디렉터리를 초기화하지만, 이 디렉터리가 DRBD 파일 시스템으로 대체되므로 나중에 MySQL을 설치해야 합니다.

`/sbin/ifconfig`를 사용하여 두 VM이 모두 10.10.10.0/24 서브넷의 주소를 사용하고 서로 이름을 사용하여 ping할 수 있는지 확인합니다. 또한 `ssh-keygen` 및 `ssh-copy-id`를 사용하여 두 VM이 암호를 요구하지 않고 SSH를 통해 통신할 수 있는지도 확인할 수 있습니다.

### <a name="set-up-drbd"></a>DRBD 설치
기본 `/dev/sdc1` 파티션을 사용하여 ext3을 통해 포맷되고 기본 노드와 보조 노드 모두에서 사용될 수 있는 `/dev/drbd1` 리소스를 생성하는 DRBD 리소스를 만듭니다.

1. `/etc/drbd.d/r0.res`를 열고 두 VM에서 다음 리소스 정의를 복사합니다.

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. 두 VM에서 `drbdadm`을 사용하여 리소스를 초기화합니다.

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. 기본 VM(`hadb01`)에서 DRBD 리소스의 소유권(기본)에 대해 다음을 강제로 실행합니다.

        sudo drbdadm primary --force r0

두 VM에서 /proc/drbd(`sudo cat /proc/drbd`)의 내용을 검토할 경우 여기서 `hadb01`의 `Primary/Secondary`와 `hadb02`의 `Secondary/Primary`가 솔루션과 일치하는 것을 확인할 수 있습니다. 5GB 디스크는 고객에게 무료로 10.10.10.0/24 네트워크를 통해 동기화됩니다.

디스크가 동기화되면 `hadb01`에 파일 시스템을 만들 수 있습니다. 테스트를 위해 ext2를 사용했지만 다음 코드는 ext3 파일 시스템을 만듭니다:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>DRBD 리소스 탑재
이제 `hadb01`에 DRBD 리소스를 탑재할 준비가 되었습니다. Debian 및 파생 배포판은 `/var/lib/mysql` 을 MySQL 데이터 디렉터리로 사용합니다. MySQL을 설치하지 않았으므로 디렉터리를 만들고 DRBD 리소스를 탑재합니다. 이 옵션을 수행하려면 `hadb01`에서 다음 코드를 실행합니다.

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>MySQL 설치
이제 `hadb01`에서 MySQL을 설치할 준비가 되었습니다.

    sudo apt-get install mysql-server

`hadb02`의 경우 두 가지 옵션이 있습니다. mysql-server를 설치하면 /var/lib/mysql을 만들고 새 데이터 디렉터리로 채운 다음 내용을 제거합니다. 이 옵션을 수행하려면 `hadb02`에서 다음 코드를 실행합니다.

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

두 번째 옵션은 `hadb02`로 장애 조치(failover)한 다음 mysql-server를 여기에 설치하는 것입니다. 설치 스크립트에서 기존 설치를 인식하고 이 설치를 수정하지 않습니다.

`hadb01`에서 다음 코드를 실행합니다.

    sudo drbdadm secondary –force r0

`hadb02`에서 다음 코드를 실행합니다.

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

지금 DRBD를 장애 조치(Failover)할 계획이 아닌 경우 첫 번째 옵션이 더 쉽지만 확실히 덜 깔끔합니다. 설정이 완료되면 MySQL 데이터베이스에 대해 작업을 시작할 수 있습니다. `hadb02`(또는 DRBD에 따라 활성 중인 서버 중 하나)에서 다음 코드를 실행합니다.

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> 이 마지막 문은 이 표의 루트 사용자에 대한 인증을 사용할 수 없게 설정합니다. 이 문은 프로덕션급 GRANT 문으로 바꾼 후 예시 용도로만 포함해야 합니다.

또한 VM 외부에서 쿼리를 수행하려면(이 가이드의 목적) MySQL을 위한 네트워킹을 사용하도록 설정해야 합니다. 두 VM에서 `/etc/mysql/my.cnf`를 열고 `bind-address`로 이동합니다. 주소를 127.0.0.1에서 0.0.0.0으로 변경합니다. 이 파일을 저장한 후에 현재 기본 노드에 대해 `sudo service mysql restart` 를 실행합니다.

### <a name="create-the-mysql-load-balanced-set"></a>MySQL 부하 분산 집합 만들기
포털로 돌아가서 `hadb01`로 이동하고 **끝점**을 선택합니다. 끝점을 만들려면 드롭다운 목록에서 MySQL(TCP 3306)을 선택하고 **새 부하 분산 집합 만들기**를 선택합니다. 부하 분산 끝점의 이름을 `lb-mysql`로 지정합니다. **시간**을 5초 이상으로 설정합니다.

끝점을 만든 후 `hadb02`로 이동하여 **끝점**을 선택하고 끝점을 만듭니다. `lb-mysql`을 선택한 다음 드롭다운 목록에서 MySQL을 선택합니다. 이 단계를 위해 Azure CLI를 사용할 수도 있습니다.

이제 클러스터의 수동 조작에 필요한 모든 항목이 있습니다.

### <a name="test-the-load-balanced-set"></a>부하 분산 집합 테스트
MySQL 클라이언트를 사용하거나 Azure 웹 사이트로 실행되는 phpMyAdmin과 같은 특정 응용 프로그램을 사용하여 외부 컴퓨터에서 테스트를 수행할 수 있습니다. 이 경우 다른 Linux 상자에서 다음과 같은 MySQL의 명령줄 도구를 사용했습니다.

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>수동 장애 조치(Failover)
MySQL을 종료하고 DRBD의 기본 노드를 전환한 다음 MySQL을 다시 시작하여 장애 조치를 시뮬레이션할 수 있습니다.

이 작업을 수행하려면 hadb01에서 다음 코드를 실행합니다.

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

그런 후 hadb02에서 다음을 실행합니다.

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

수동으로 장애 조치를 수행한 후에는 원격 쿼리를 반복할 수 있으며 완벽하게 작동합니다.

## <a name="set-up-corosync"></a>Corosync 설치
Corosync는 Pacemaker 작동에 필요한 기본 클러스터 인프라입니다. Heartbeat(및 Ultramonkey와 같은 다른 방법)의 경우 Corosync는 CRM 기능에서 분할된 것이지만 Pacemaker는 기능적인 측면에서 Hearbeat와 더 비슷합니다.

Azure에서 나타나는 Corosync의 기본적인 제약 조건은 Corosync가 유니캐스트 통신보다는 브로드캐스트를 통한 멀티캐스트 통신 방식을 선호한다는 것입니다. 그렇지만 Microsoft Azure 네트워킹에서는 유니캐스트만 지원합니다.

다행히도 Corosync에는 작동 가능한 유니캐스트 모드가 있습니다. 유일한 실질적 제약 조건은 모든 노드가 서로 통신하지 않기 때문에 IP 주소를 포함하여 구성 파일에 노드를 정의해야 한다는 것입니다. 유니캐스트에 대한 Corosync 예제 파일을 사용하고, 바인드 주소, 노드 목록 및 로깅 디렉터리를 변경하고(Ubuntu는 `/var/log/corosync`를 사용하지만 예제 파일은 `/var/log/cluster`를 사용함), 쿼럼 도구를 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 다음 `transport: udpu` 지시문과 두 노드에 대해 수동으로 정의된 IP 주소를 사용합니다.

두 노드에 대해 `/etc/corosync/corosync.conf`에서 다음 코드를 실행합니다.

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

두 VM에서 이 구성 파일을 복사하고 다음과 같이 두 노드에서 Corosync를 시작합니다.

    sudo service start corosync

서비스를 시작한 직후 클러스터가 현재 링에 설정되고 쿼럼이 구성되어야 합니다. 로그를 검토하거나 다음 코드를 실행하여 이 기능을 확인할 수 있습니다.

    sudo corosync-quorumtool –l

다음 이미지와 비슷한 출력이 표시됩니다.

![corosync-quorumtool -l sample output](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Pacemaker 설치
Pacemaker는 클러스터를 사용하여 리소스를 모니터링하고, 기본 노드가 작동 중단될 때를 정의하고, 해당 리소스를 보조 노드로 전환합니다. 리소스는 여러 방법이 있지만 사용 가능한 스크립트 집합이나 LSB(init-like) 스크립트에서 정의할 수 있습니다.

Pacemaker에서 DRBD 리소스, 탑재 지점 및 MySQL 서비스를 "소유"하기를 원합니다. 기본 노드에 좋지 않은 상황이 발생할 때 Pacemaker에서 올바른 순서로 DRBD를 켜고 끌 수 있고, 탑재/분리할 수 있고, MySQL을 시작/중지할 수 있으면 설치가 완료된 것입니다.

Pacemaker를 처음 설치할 때는 구성이 다음과 같이 단순합니다.

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. `sudo crm configure show`를 실행하여 구성을 확인합니다.
2. 이제 다음 리소스를 사용하여 파일(예: `/tmp/cluster.conf`)을 만듭니다.

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. 구성에 파일을 로드합니다. 하나의 노드에서만 이 작업을 수행해야 합니다.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. 두 노드에서 부팅할 때 Pacemaker가 시작되는지 확인합니다.

        sudo update-rc.d pacemaker defaults

5. `sudo crm_mon –L`을 사용하여 노드 중 하나가 클러스터의 마스터가 되고 모든 리소스를 실행하고 있는지 확인합니다. mount 및 ps를 사용하여 리소스가 실행 중인지 확인할 수 있습니다.

다음 스크린샷에서는 한 노드가 중지된 `crm_mon`을 보여 줍니다(종료하려면 Ctrl+C 사용).

![crm_mon node stopped](./media/mysql-cluster/image002.png)

다음 스크린샷에서는 두 노드, 즉 하나의 마스터와 하나의 슬레이브를 보여 줍니다.

![crm_mon operational master/slave](./media/mysql-cluster/image003.png)

## <a name="testing"></a>테스트
자동 장애 조치(Failover) 시뮬레이션을 위한 준비가 되었습니다. 이 작업을 수행하는 방법에는 소프트 방법과 하드 방법이 있습니다.

소프트 방법은 클러스터의 종료 함수인 ``crm_standby -U `uname -n` -v on``을 사용합니다. 마스터에서 이 함수를 사용하면 슬레이브가 대신합니다. 이 값을 다시 off로 설정해야 합니다. 이렇게 하지 않으면 crm_mon에서 대기 중인 노드 하나를 표시합니다.

하드 방법은 포털을 통하거나 VM의 실행 수준(즉 중지, 종료)을 변경하여 기본 VM(hadb01)을 종료하는 것입니다. 이렇게 하면 마스터가 작동 중단되었음을 알리는 신호를 보냄으로써 Corosync 및 Pacemaker에 도움이 됩니다. 이러한 동작을 테스트할 수 있지만(유지 관리 창에 유용함) VM을 동결하여 이 시나리오를 강제로 실행할 수도 있습니다.

## <a name="stonith"></a>STONITH
실제 장치를 제어하는 STONITH 스크립트 대신, Azure CLI를 통해 VM 종료를 실행할 수 있습니다. `/usr/lib/stonith/plugins/external/ssh` 를 기반으로 사용하고 클러스터의 구성에서 STONITH를 사용하도록 설정할 수 있습니다. Azure CLI는 전역적으로 설치해야 하며 클러스터 사용자를 위해 게시 설정과 프로필을 로드해야 합니다.

리소스의 샘플 코드는 [GitHub](https://github.com/bureado/aztonith)에서 사용할 수 있습니다. `sudo crm configure`에 다음을 추가하여 클러스터 구성을 변경합니다.

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> 이 스크립트는 up/down 검사를 수행하지 않습니다. 원래의 SSH 리소스는 ping 검사를 15번 수행했지만 Azure VM의 복구 시간은 좀 더 가변적일 수 있습니다.

## <a name="limitations"></a>제한 사항
다음 제한 사항이 적용됩니다.

* Pacemaker에서 DRBD를 리소스로 관리하는 linbit DRBD 리소스 스크립트는 노드가 대기 상태일 때도 노드를 종료할 때 `drbdadm down`을 사용합니다. 슬레이브는 마스터가 쓰기를 받는 동안 DRBD 리소스를 동기화하지 않기 때문에 이러한 방식은 이상적이지 않습니다. 다행히도 마스터가 실패하지 않으면 슬레이브에서 이전 파일 시스템 상태를 인계받을 수 있습니다. 이러한 문제를 해결할 수 있는 방법에는 다음 두 가지가 있습니다.
  * 로컬(클러스터화되지 않은) Watchdog을 통해 모든 클러스터 노드에서 `drbdadm up r0` 적용
  * linbit DRBD 스크립트를 편집하여 `/usr/lib/ocf/resource.d/linbit/drbd`에서 `down`이 호출되지 않았는지 확인
* 부하 분산 장치는 응답하는 데 5초 이상 필요하므로 응용 프로그램에서 클러스터를 인식하고 시간 제한을 더 늘려야 합니다. 앱 내 큐 및 쿼리 미들웨어와 같은 다른 아키텍처도 도움이 될 수 있습니다.
* MySQL 튜닝을 통해 쓰기가 관리 가능한 속도로 수행되고 캐시가 가능한 한 자주 디스크로 플러시되어 메모리 손실을 최소화하도록 보장해야 합니다.
* 쓰기 성능은 가상 스위치의 VM 상호 연결에 따라 달라집니다. 이는 DRBD에서 장치를 복제하는 데 사용하는 메커니즘이기 때문입니다.
