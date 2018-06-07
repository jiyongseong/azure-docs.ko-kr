---
title: Linux에서 MySQL 성능 최적화 | Microsoft Docs
description: Linux를 실행하는 Azure VM(가상 머신)에서 실행 중인 MySQL을 최적화하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 447532452a848c88fd927f42e4263cef4742dd89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Azure Linux VM에서 MySQL 성능 최적화
가상 하드웨어 선택과 소프트웨어 구성 모두에서 Azure의 MySQL 성능에 영향을 주는 많은 요소가 있습니다. 이 문서에서는 저장소, 시스템 및 데이터베이스 구성을 통해 성능을 최적화하는 방법에 중점을 둡니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 별도의 두 가지 배포 모델, 즉 [Azure Resource Manager](../../../resource-manager-deployment-model.md)와 클래식 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델을 사용한 Linux VM 최적화에 대한 내용은 [Azure에서 Linux VM 최적화](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Azure 가상 컴퓨터에서 RAID 활용
저장소는 클라우드 환경에서 데이터베이스 성능에 영향을 주는 핵심 요소입니다. 단일 디스크에 비해 RAID는 동시 연결을 통해 더욱 빠른 액세스를 제공할 수 있습니다. 자세한 내용은 [표준 RAID 수준](http://en.wikipedia.org/wiki/Standard_RAID_levels)(영문)을 참조하세요.   

Azure의 디스크 I/O 처리량 및 I/O 응답 시간은 RAID를 통해 향상될 수 있습니다. Microsoft의 랩 테스트에서는 RAID 디스크 수를 두 배로 늘리면(예: 2개에서 4개, 4개에서 8개 등으로) 평균적으로 디스크 I/O 처리량이 두 배로 늘어나고 I/O 응답 시간이 절반으로 줄어들 수 있음을 보여 주었습니다. 자세한 내용은 [부록 A](#AppendixA) 를 참조하세요.  

디스크 I/O 외에도 RAID 수준을 높일 경우 MySQL 성능이 향상됩니다.  자세한 내용은 [부록 B](#AppendixB) 를 참조하세요.  

또한 청크 크기를 고려할 수도 있습니다. 일반적으로 청크 크기가 크면 특히 대량 쓰기에서 오버헤드가 낮아집니다(특히 대량 쓰기의 경우). 그러나 청크 크기가 너무 크면 추가 오버헤드가 발생할 수 있으므로 RAID를 활용할 수 없습니다. 현재 기본 크기는 512KB이며, 대부분의 일반 프로덕션 환경에 가장 적합한 것으로 증명되었습니다. 자세한 내용은 [부록 C](#AppendixC)를 참조하세요.   

여러 유형의 가상 머신에 추가할 수 있는 디스크 수에는 제한이 있습니다. 이러한 제한은 [Azure의 가상 머신 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)에서 자세히 설명하고 있습니다. 더 적은 수의 디스크로 RAID를 설정하도록 선택할 수 있지만 이 문서의 RAID 예제를 따르려면 연결된 데이터 디스크 4개가 필요합니다.  

이 문서에서는 Linux 가상 컴퓨터를 이미 만들고 MYSQL을 설치 및 구성하는 것으로 가정합니다. 시작하는 방법에 대한 자세한 내용은 Azure에서 MySQL을 설치하는 방법을 참조하세요.  

### <a name="set-up-raid-on-azure"></a>Azure에서 RAID 설치
다음 단계에서는 Azure Portal을 사용하여 Azure에서 RAID를 만드는 방법을 보여 줍니다. 또한 Windows PowerShell 스크립트를 사용하여 RAID를 설치할 수도 있습니다.
이 예제에서는 디스크 4개를 사용하여 RAID 0을 구성합니다.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>가상 컴퓨터에 데이터 디스크 추가
Azure Portal에서 대시보드로 이동하고 데이터 디스크를 추가할 가상 머신을 선택합니다. 이 예제의 가상 컴퓨터는 mysqlnode1입니다.  

<!--![Virtual machines][1]-->

**디스크**를 클릭한 다음 **새로 연결**을 클릭합니다.

![가상 머신에서 디스크 추가](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

새 500GB 디스크를 만듭니다. **호스트 캐시 기본 설정**이 **None**으로 설정되었는지 확인합니다.  작업을 완료하면 **확인**을 클릭합니다.

![빈 디스크 연결](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


그러면 가상 머신에 빈 디스크 하나가 추가됩니다. RAID의 데이터 디스크가 4개가 되도록 이 단계를 세 번 더 반복합니다.  

커널 메시지 로그에서 가상 머신에 추가된 드라이브를 확인할 수 있습니다. 예를 들어 Ubuntu에서 이를 보려면 다음 명령을 사용합니다.  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>추가 디스크를 사용하여 RAID 만들기
다음 단계에서는 [Linux에서 소프트웨어 RAID를 구성](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하는 방법을 설명합니다.

> [!NOTE]
> XFS 파일 시스템을 사용하는 경우 RAID를 만든 후에 다음 단계를 실행합니다.
>
>

Debian, Ubuntu 또는 Linux Mint에서 XFS를 설치하려면 다음 명령을 사용합니다.  

    apt-get -y install xfsprogs  

Fedora, CentOS 또는 RHEL에서 XFS를 설치하려면 다음 명령을 사용합니다.  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>새 저장소 경로 설정
다음 명령을 사용하여 새 저장소 경로를 설정합니다.  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>새 저장소 경로에 원본 데이터 복사
다음 명령을 사용하여 새 저장소 경로에 데이터를 복사합니다.  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>MySQL에서 데이터 디스크에 액세스(읽기/쓰기)할 수 있도록 사용 권한 수정
다음 명령을 사용하여 권한을 수정합니다.  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>디스크 I/O 일정 알고리즘 조정
Linux에서는 다음 네 가지 유형의 I/O 일정 알고리즘을 구현합니다.  

* NOOP 알고리즘(작업 없음)
* Deadline 알고리즘(기한)
* Completely Fair Queuing 알고리즘(CFQ)
* Budget Period 알고리즘(예상)  

상황에 따라 다른 I/O 스케줄러를 선택하여 성능을 최적화할 수 있습니다. 완전한 임의 액세스 환경에서는 CFQ 알고리즘과 Deadline 알고리즘 간에 성능 면에서 큰 차이가 없습니다. MySQL 데이터베이스 환경은 안정성을 위해 Deadline으로 설정하는 것이 좋습니다. 순차적 I/O가 많은 경우 CFQ로 인해 디스크 I/O 성능이 저하될 수 있기 때문입니다.   

SSD 및 기타 장비의 경우 NOOP 또는 Deadline을 사용하면 기본 스케줄러보다 성능이 더 향상될 수 있습니다.   

커널 2.5 이전에는 Deadline이었지만, 커널 2.6.18부터 CFQ가 기본 I/O 스케줄링 알고리즘이 되었습니다.  커널 부팅 시 이 설정을 지정하거나 시스템이 실행 중일 때 이 설정을 동적으로 수정할 수 있습니다.  

다음 예제에서는 기본 스케줄러를 확인하여 Debian 배포판 제품군의 NOOP 알고리즘으로 설정하는 방법을 보여 줍니다.  

### <a name="view-the-current-io-scheduler"></a>현재 I/O 스케줄러 보기
스케줄러를 보려면 다음 명령을 실행합니다.  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

현재 스케줄러를 나타내는 다음 출력이 표시됩니다.  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>I/O 스케줄링 알고리즘의 현재 장치(/dev/sda) 변경
다음 명령을 실행하여 현재 디렉터리를 변경합니다.  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> /dev/sda에 대해서만 이를 설정하는 것은 유용하지 않습니다. 데이터베이스가 있는 모든 데이터 디스크에 설정해야 합니다.  
>
>

grub.cfg가 성공적으로 다시 빌드되었으며 기본 스케줄러가 NOOP로 업데이트되었음을 나타내는 다음 출력이 표시됩니다.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Red Hat 배포판 제품군의 경우 다음 명령만 필요합니다.

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>시스템 파일 작업 설정 구성
파일 시스템에서 *atime* 로깅 기능을 사용하지 않도록 설정하는 것이 좋습니다. Atime은 마지막 파일 액세스 시간입니다. 파일에 액세스할 때마다 파일 시스템에서 로그에 타임스탬프를 기록합니다. 그러나 이 정보는 거의 사용되지 않습니다. 필요하지 않은 경우 사용하지 않도록 설정하여 전체 디스크 액세스 시간을 단축할 수 있습니다.  

atime 로깅을 사용하지 않도록 설정하려면 파일 시스템 구성 파일 /etc/ fstab을 수정하고 **noatime** 옵션을 추가해야 합니다.  

예를 들어 vim/etc/fstab 파일을 편집하여 다음 샘플과 같이 noatime을 추가합니다.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

그런 후 다음 명령으로 파일 시스템을 다시 탑재합니다.  

    mount -o remount /RAID0

수정된 결과를 테스트합니다. 테스트 파일을 수정하면 액세스 시간이 업데이트되지 않습니다. 다음 예제에서는 수정 전후의 코드를 보여 줍니다.

이전:        

![액세스 수정 이전 코드][5]

이후:

![액세스 수정 이후 코드][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>동시성 향상을 위한 최대 시스템 핸들 수 증가
MySQL은 동시성이 뛰어난 데이터베이스입니다. 기본 동시 핸들 수는 Linux의 경우 1024개이며, 이것으로 부족한 경우가 있습니다. 다음 단계를 사용하여 MySQL의 뛰어난 동시성을 지원하도록 시스템의 최대 동시 핸들 수를 늘릴 수 있습니다.

### <a name="modify-the-limitsconf-file"></a>limits.conf 파일 수정
허용되는 최대 동시 처리량을 늘리려면 /etc/security/limits.conf 파일에 다음 네 줄을 추가합니다. 65536은 시스템에서 지원할 수 있는 최대 수입니다.   

    * soft nofile 65536
    * hard nofile 65536
    * soft nproc 65536
    * hard nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>새 제한에 대해 시스템 업데이트
시스템을 업데이트하려면 다음 명령을 실행합니다.  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>부팅 시 제한이 업데이트되도록 설정
부팅 시 적용되도록 /etc/rc.local 파일에 다음 시작 명령을 추가합니다.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>MySQL 데이터베이스 최적화
Azure에서 MySQL을 구성하려면 온-프레미스 시스템에서 사용하는 것과 동일한 성능 조정 전략을 사용할 수 있습니다.  

기본 I/O 최적화 규칙은 다음과 같습니다.   

* 캐시 크기 증가.
* I/O 응답 시간 감소  

MySQL 서버 설정을 최적화하려면 서버 컴퓨터와 클라이언트 컴퓨터 모두의 기본 구성 파일인 my.cnf 파일을 업데이트하면 됩니다.  

다음 구성 항목은 MySQL 성능에 영향을 주요 요소입니다.  

* **innodb_buffer_pool_size**: 버퍼 풀에는 버퍼된 데이터와 인덱스가 포함됩니다. 이 옵션은 일반적으로 실제 메모리의 70%로 설정됩니다.
* **innodb_log_file_size**: 다시 실행 로그 크기입니다. 다시 실행 로그를 사용하면 쓰기 작업이 빠르고 안정적이며 크래시 후 복구할 수 있게 됩니다. 이 옵션은 512MB로 설정되어 있으며, 쓰기 작업을 로깅하는 데 충분한 공간을 제공합니다.
* **max_connections**: 일부 응용 프로그램은 연결을 제대로 닫지 않습니다. 이 값이 클수록 서버에 유휴 상태의 연결을 재순환할 수 있는 시간이 더 많이 제공됩니다. 최대 연결 수는 10,000개이지만 권장되는 최대값은 5,000개입니다.
* **Innodb_file_per_table**: 이 설정은 InnoDB에서 테이블을 별도의 파일에 저장할 수 있는 기능을 사용하거나 사용하지 않도록 설정합니다. 이 옵션을 설정하면 여러 고급 관리 작업을 효율적으로 적용할 수 있습니다. 성능 면에서 테이블 공간 전송 속도를 높이고 잔류물 관리 성능을 최적화할 수 있습니다. 이 옵션의 권장 설정은 ON입니다.</br></br>
MySQL 5.6부터 기본 설정이 ON이므로 어떤 조치도 필요하지 않습니다. 이전 버전의 경우 기본 설정은 OFF입니다. 새로 만든 테이블만 영향을 받으므로 데이터가 로드되기 전에 설정을 변경해야 합니다.
* **innodb_flush_log_at_trx_commit**: 기본값은 1이며 범위는 0~2로 설정됩니다. 기본값은 독립 실행형 MySQL DB에 가장 적합한 옵션입니다. 2로 설정하면 데이터 무결성이 가장 많이 높아지며 MySQL 클러스터의 마스터에 적합합니다. 0으로 설정하면 데이터 손실이 발생하여 안정성에 영향을 줄 수 있으며(경우에 따라 성능이 향상될 수 있음) MySQL 클러스터의 슬레이브에 적합합니다.
* **Innodb_log_buffer_size**: 로그 버퍼는 트랜잭션이 커밋되기 전에 로그를 디스크에 플러시하지 않고도 트랜잭션이 실행되도록 해줍니다. 그러나 큰 이진 개체 또는 텍스트 필드가 있으면 캐시가 빠르게 소비되고 디스크 I/O가 자주 트리거됩니다. Innodb_log_waits 상태 변수가 0이 아닌 경우 버퍼 크기를 늘리는 것이 좋습니다.
* **query_cache_size**: 처음부터 사용하지 않도록 설정하는 것이 좋습니다. query_cache_size를 0(MySQL 5.6의 기본 설정)으로 설정하고 다른 방법을 사용하여 쿼리 속도를 높입니다.  

최적화 전후의 성능 비교는 [부록 D](#AppendixD)를 참조하세요.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>MySQL 느린 쿼리 로그를 설정하여 성능 병목 현상 분석
MySQL 느린 쿼리 로그를 사용하면 MySQL에 대한 느린 쿼리를 식별할 수 있습니다. MySQL 느린 쿼리 로그를 사용하도록 설정한 후 **mysqldumpslow** 와 같은 MySQL 도구를 사용하여 성능 병목 현상을 식별할 수 있습니다.  

기본적으로 이 옵션은 사용하지 않도록 설정됩니다. 느린 쿼리 로그를 설정하면 일부 CPU 리소스가 소모될 수 있습니다. 성능 병목 문제를 해결하기 위해 이 옵션을 일시적으로 사용하는 것이 좋습니다. 느린 쿼리 로그를 설정하려면 다음과 같이 수행합니다.

1. 다음 줄을 끝에 추가하여 my.cnf 파일을 수정합니다.

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. MySQL 서버를 다시 시작합니다.

        service  mysql  restart

3. **show** 명령을 사용하여 설정이 적용되고 있는지 확인합니다.

![Slow-query-log ON][7]   

![Slow-query-log results][8]

이 예제에서는 느린 쿼리 기능이 설정된 것을 볼 수 있습니다. **mysqldumpslow** 도구를 사용하여 성능 병목 현상을 확인하고 인덱스 추가와 같은 성능을 최적화할 수 있습니다.

## <a name="appendices"></a>부록
다음은 대상 랩 환경에서 생성된 샘플 성능 테스트 데이터입니다. 여기서는 서로 다른 성능 조정 방법을 사용하여 성능 데이터 추세에 대한 일반적인 배경을 제공합니다. 환경이나 제품 버전에 따라 결과가 다를 수 있습니다.

### <a name="AppendixA"></a>부록 A  
**RAID 수준별 디스크 성능(IOPS)**

![RAID 수준별 디스크 IOPS][9]

**테스트 명령**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> 이 테스트의 작업에서는 RAID 상한에 도달하기 위해 64개의 스레드를 사용합니다.
>
>

### <a name="AppendixB"></a>부록 B  
**RAID 수준별 MySQL 성능(처리량) 비교**   
(XFS 파일 시스템)

![RAID 수준별 MySQL 성능 비교][10]  
![RAID 수준별 MySQL 성능 비교][11]

**테스트 명령**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**RAID 수준별 MySQL 성능(OLTP) 비교**  
![RAID 수준별 MySQL 성능(OLTP) 비교][12]

**테스트 명령**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>부록 C   
**청크 크기별 디스크 성능(IOPS) 비교**  
(XFS 파일 시스템)

![][13]

**테스트 명령**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

이 테스트에 사용된 파일 크기는 각각 RAID 0(4개 디스크) XFS 파일 시스템이 있는 30GB 및 1GB입니다.

### <a name="AppendixD"></a>부록 D  
**최적화 전후의 MySQL 성능(처리량) 비교**  
(XFS 파일 시스템)

![최적화 전후의 MySQL 성능(처리량) 비교][14]

**테스트 명령**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**기본 및 최적화에 대한 구성 설정은 다음과 같습니다.**

| 매개 변수 | 기본값 | 최적화 |
| --- | --- | --- |
| **innodb_buffer_pool_size** |없음 |7 GB |
| **innodb_log_file_size** |5MB |512MB |
| **max_connections** |100 |5,000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8MB |128MB |
| **query_cache_size** |16MB |0 |

[최적화 구성 매개 변수](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)(영문)에 대한 자세한 내용은 [MySQL 공식 지침](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)(영문)을 참조하세요.  

  **테스트 환경**  

| 하드웨어 | 세부 정보 |
| --- | --- |
| CPU |AMD Opteron(tm) 프로세서 4171 HE/4 코어 |
| 메모리 |14 GB |
| 디스크 |10GB/디스크 |
| OS |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

