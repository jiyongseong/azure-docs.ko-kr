---
title: Azure에서 MariaDB(MySQL) 클러스터 실행 | Microsoft Docs
description: Azure 가상 머신에 MariaDB + Galera MySQL 클러스터를 만듭니다.
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 4a3eede532345f8628af1722a06531571f01afbf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB(MySQL) 클러스터: Azure 자습서
> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 별도의 두 가지 배포 모델, 즉 [Azure Resource Manager](../../../resource-manager-deployment-model.md)와 클래식 모델이 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 대부분의 새로운 배포에서는 Azure Resource Manager 모델을 사용하는 것이 좋습니다.

> [!NOTE]
> Azure Marketplace에 MariaDB 엔터프라이즈 클러스터가 출시되었습니다. 새로운 이 서비스는 Azure Resource Manager에 MariaDB Galera 클러스터를 자동으로 배포합니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/)에서 이 서비스를 사용해야 합니다.
>
>

이 문서에서는 Azure 가상 머신의 고가용성 환경에서 작업할 수 있도록 강력하고, 확장성과 안정성이 뛰어나며, 예약할 필요가 없는 MySQL 대체 도구인 [MariaDB](https://mariadb.org/en/about/)의 다중 마스터 [Galera](http://galeracluster.com/products/) 클러스터를 만드는 방법에 대해 설명합니다.

## <a name="architecture-overview"></a>아키텍처 개요
이 항목에서는 다음 단계를 수행하는 방법에 대해 설명합니다.

- 3개 노드 클러스터를 만듭니다.
- OS 디스크에서 데이터 디스크를 분리합니다.
- RAID-0/스트라이프 설정으로 데이터 디스크를 만들어 IOPS를 높입니다.
- Azure Load Balancer를 사용하여 3개 노드의 부하를 분산시킵니다.
- 반복 작업을 최소화하기 위해 MariaDB+Galera가 포함된 VM 이미지를 만들고 이 이미지를 사용하여 다른 클러스터 VM을 만듭니다.

![시스템 아키텍처](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> 이 항목에서는 [Azure CLI](../../../cli-install-nodejs.md) 도구를 사용하므로 도구를 다운로드하고 지침에 따라 Azure 구독에 연결해야 합니다. Azure CLI에서 사용할 수 있는 명령에 대한 참조가 필요하면 [Azure CLI 명령 참조](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)를 참조하세요. 또한 [인증에 사용할 SSH 키를 만들고] pem 파일 위치를 적어 두어야 합니다.
>
>

## <a name="create-the-template"></a>템플릿 만들기
### <a name="infrastructure"></a>인프라
1. 리소스를 포함할 선호도 그룹을 만듭니다.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. 가상 네트워크를 만듭니다.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. 모든 디스크를 호스팅할 저장소 계정을 만듭니다. 20,000 IOPS 저장소 계정 한도를 초과하지 않도록 동일한 저장소 계정에 자주 사용되는 디스크를 40개 이상 배치하지 않도록 합니다. 여기서는 한도보다 훨씬 작으므로 간단하게 동일한 계정에 모든 것을 저장하겠습니다.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. CentOS 7 가상 머신 이미지의 이름을 찾습니다.

        azure vm image list | findstr CentOS
   출력은 `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`과 같습니다.

   다음 단계에서 해당 이름을 사용합니다.
5. VM 템플릿을 만들고 생성된 .pem SSH 키를 저장한 경로로 /path/to/key.pem을 바꿉니다.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. RAID 구성에서 사용할 VM에 4개의 500GB 데이터 디스크를 연결합니다.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. SSH를 사용하여 mariadbhatemplate.cloudapp.net:22에서 만든 템플릿 VM에 로그인하고 개인 키를 사용하여 연결합니다.

### <a name="software"></a>소프트웨어
1. 루트를 가져옵니다.

        sudo su

2. RAID 지원을 설치합니다.

    a. mdadm을 설치합니다.

              yum install mdadm

    나. EXT4 파일 시스템으로 RAID0/스트라이프 구성을 만듭니다.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    다. 탑재 지점 디렉터리를 만듭니다.

              mkdir /mnt/data
    d. 새로 만든 RAID 장치의 UUID를 검색합니다.

              blkid | grep /dev/md0
    e. /etc/fstab을 편집합니다.

              vi /etc/fstab
    f. UUID를 이전 **blkid** 명령에서 가져온 값으로 바꾸고, 장치를 추가하여 재부팅할 때 자동 탑재를 사용하도록 설정합니다.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. 새 파티션을 탑재합니다.

              mount /mnt/data

3. MariaDB를 설치합니다.

    a. MariaDB.repo 파일을 만듭니다.

                vi /etc/yum.repos.d/MariaDB.repo

    나. repo 파일을 다음 내용으로 채웁니다.

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    다. 충돌을 방지하기 위해 기존 접미사와 mariadb-lib를 제거합니다.

           yum remove postfix mariadb-libs-*
    d. Galera와 함께 MariaDB를 설치합니다.

           yum install MariaDB-Galera-server MariaDB-client galera

4. MySQL 데이터 디렉터리를 RAID 블록 장치로 이동합니다.

    a. 현재 MySQL 디렉터리를 새 위치에 복사하고 이전 디렉터리를 제거합니다.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    나. 이에 따라 새 디렉터리에 대한 권한을 설정합니다.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    다. 이전 디렉터리를 RAID 파티션의 새 위치로 가리키는 symlink를 만듭니다.

           ln -s /mnt/data/mysql /var/lib/mysql

5. [SELinux는 클러스터 작업을 방해](http://galeracluster.com/documentation-webpages/configuration.html#selinux)하기 때문에 현재 세션에서 사용하지 않도록 설정해야 합니다. `/etc/selinux/config`를 편집하여 이후의 다시 시작에서 사용하지 않도록 설정합니다.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. MySQL 실행의 유효성을 검사합니다.

   a. MySQL을 시작합니다.

           service mysql start
   나. MySQL 설치 보안을 유지하고, 루트 암호를 설정하며, 익명 사용자를 제거하여 원격 루트 로그인을 사용하지 않도록 설정하고, 테스트 데이터베이스를 제거합니다.

           mysql_secure_installation
   다. 클러스터 작업 및 선택적으로 응용 프로그램을 위해 데이터베이스에 사용자를 만듭니다.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. MySQL을 중지합니다.

            service mysql stop
7. 구성 자리 표시자를 만듭니다.

   a. MySQL 구성을 편집하여 클러스터 설정에 대한 자리 표시자를 만듭니다. 지금은 **`<Variables>`** 를 바꾸거나 주석을 제거하지 마세요. 이 템플릿에서 VM을 만든 후에 그렇게 됩니다.

            vi /etc/my.cnf.d/server.cnf
   나. **[galera]** 섹션을 편집하여 지웁니다.

   다. **[mariadb]** 섹션을 편집합니다.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. CentOS 7에서 FirewallD를 사용하여 방화벽에서 필요한 포트를 엽니다.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * 방화벽 다시 로드: `firewall-cmd --reload`

9. 성능에 대해 시스템을 최적화합니다. 자세한 내용은 [성능 조정 전략](optimize-mysql.md)을 참조하세요.

   a. MySQL 구성 파일을 다시 편집합니다.

            vi /etc/my.cnf.d/server.cnf
   나. **[mariadb]** 섹션을 편집하고 다음 내용을 추가합니다.

   > [!NOTE]
   > innodb\_buffer\_pool_size를 VM 메모리의 70%로 설정하는 것이 좋습니다. 이 예제에서는 3.5GB RAM을 갖춘 중간 Azure VM에 대해 2.45GB로 설정되었습니다.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. MySQL을 중지하고, 시작할 때 MySQL 서비스가 실행되지 않도록 설정하여 노드를 추가할 때 클러스터가 중단되지 않게 하고, 컴퓨터 프로비전을 해제합니다.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. 포털을 통해 VM을 캡처합니다. (현재 [Azure CLI 도구 문제 #1268](https://github.com/Azure/azure-xplat-cli/issues/1268)(영문)에서는 Azure CLI 도구로 캡처한 이미지가 연결된 데이터 디스크를 캡처하지 않는다는 사실을 설명하고 있습니다.)

    a. 포털을 통해 컴퓨터를 종료합니다.

    나. **캡처**를 클릭하고 이미지 이름을 **mariadb-galera-image**로 지정합니다. 설명을 입력하고 "waagent를 실행했습니다." 확인란을 선택합니다.
      
      ![가상 머신 캡처](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>클러스터 만들기
만든 템플릿으로 세 개의 VM을 만든 다음 클러스터를 구성하고 시작합니다.

1. 만든 mariadb-galera-image 이미지에서 첫 번째 CentOS 7 VM을 만들고 다음 정보를 제공합니다.

 - 가상 네트워크 이름: mariadbvnet
 - 서브넷: mariadb
 - 컴퓨터 크기: medium
 - 클라우드 서비스 이름: mariadbha(또는 mariadbha.cloudapp.net을 통해 액세스하려는 이름)
 - 컴퓨터 이름: mariadb1
 - 사용자 이름: azureuser
 - SSH 액세스: enabled
 - SSH 인증서 .pem 파일을 전달하고 생성된 .pem SSH 키를 저장한 경로로 /path/to/key.pem을 바꿉니다.

   > [!NOTE]
   > 다음 명령은 명확하게 나타내기 위해 여러 줄로 나누어져 있지만 각각 한 줄로 입력해야 합니다.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. mariadbha 클라우드 서비스에 연결하여 두 개의 가상 머신을 추가로 만듭니다. VM 이름과 SSH 포트를 동일한 클라우드 서비스의 다른 VM과 충돌하지 않는 고유한 포트로 변경합니다.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  MariaDB3의 경우:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. 다음 단계를 위해 세 개 VM 각각의 내부 IP 주소를 가져와야 합니다.

    ![IP 주소 가져오기](./media/mariadb-mysql-cluster/IP.png)
4. SSH를 사용하여 세 개 VM에 로그인하고 각각의 구성 파일을 편집합니다.

        sudo vi /etc/my.cnf.d/server.cnf

    줄의 시작 부분에서 **#** 을 제거하여 **`wsrep_cluster_name`** 및 **`wsrep_cluster_address`** 의 주석을 제거합니다.
    또한 **`wsrep_node_address`** 의 **`<ServerIP>`** 및 **`wsrep_node_name`** 의 **`<NodeName>`** 을 각각 VM의 IP 주소와 이름으로 바꾸고 해당 줄의 주석도 제거합니다.
5. MariaDB1에서 클러스터를 시작하고 시작 시 실행되도록 합니다.

        sudo service mysql bootstrap
        chkconfig mysql on
6. MariaDB2 및 MariaDB3에서 MySQL을 시작하고 시작 시 실행되도록 합니다.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>클러스터 부하 분산
클러스터된 VM을 만들 때 해당 VM을 ‘clusteravset’라는 가용성 집합에 추가하여 다른 장애 도메인 및 업데이트 도메인에 배치하도록 했으며 Azure를 통해 모든 컴퓨터에서 유지 관리를 동시에 수행하지 않도록 했습니다. 이 구성은 Azure SLA(서비스 수준 약정)에서 지원해야 하는 요구 사항을 충족합니다.

이제 Azure Load Balancer를 사용하여 3개 노드 간에 요청을 분산시킵니다.

Azure CLI를 사용하여 컴퓨터에서 다음 명령을 실행합니다.

명령 매개 변수 구조는 다음과 같습니다. `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

CLI에서 부하 분산 장치 프로브 간격을 15초로 설정합니다. 이 간격이 너무 길 수도 있습니다. VM 중 하나에 대한 **끝점**의 포털에서 변경합니다.

![끝점 편집](./media/mariadb-mysql-cluster/Endpoint.PNG)

**부하 분산 집합 다시 구성**을 선택합니다.

![부하 분산 집합 다시 구성](./media/mariadb-mysql-cluster/Endpoint2.PNG)

**프로브 간격**을 5초로 변경하고 변경 내용을 저장합니다.

![프로브 간격 변경](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>클러스터 유효성 검사
하드 작업이 완료되었습니다. 클러스터는 이제 `mariadbha.cloudapp.net:3306`에서 액세스할 수 있어야 합니다. 여기서는 부하 분산 장치를 실행하여 세 VM 간의 요청을 원활하고 효율적으로 처리합니다.

원하는 MySQL 클라이언트를 사용하여 VM 중 하나에 연결하거나 연결을 끊어 해당 클러스터가 작동하는지 확인합니다.

     mysql -u cluster -h mariadbha.cloudapp.net -p

그런 다음 데이터베이스를 만들고 일부 데이터로 채웁니다.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

만든 데이터베이스는 다음 테이블을 반환합니다.

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
이 문서에서는 CentOS 7을 실행하는 Azure 가상 머신에 3개 노드 MariaDB + Galera 고가용성 클러스터를 만들었습니다. VM에서 Azure Load Balancer를 사용하여 부하를 분산했습니다.

[Linux에서 MySQL을 클러스터링하는 다른 방법](mysql-cluster.md) 및 [Azure Linux VM에서 MySQL 성능 최적화 및 테스트](optimize-mysql.md)하는 방법을 살펴보는 것이 좋습니다.

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[인증에 사용할 SSH 키를 만들고]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
