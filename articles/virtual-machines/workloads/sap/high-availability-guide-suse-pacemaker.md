---
title: Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정 | Microsoft Docs
description: Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: c82380c20c9ec631d9fea338404a25f167277701
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Azure에서 Pacemaker 클러스터를 설정하는 옵션에는 두 가지가 있습니다. Azure API를 통해 실패한 노드를 다시 시작하는 펜싱 에이전트를 사용하거나 SBD 장치를 사용할 수 있습니다.

SBD 장치에는 iSCSI 대상 서버 역할을 하고 SBD 장치를 제공하는 추가 가상 머신이 하나 필요합니다. 단, 이 iSCSI 대상 서버를 다른 Pacemaker 클러스터와 공유할 수 있습니다. SBD 장치를 사용하는 이점은 빠른 장애 조치(failover) 시간이며 온-프레미스에서 SBD 장치를 사용하는 경우에는 pacemaker 클러스터 작동 방식을 변경할 필요가 없습니다. iSCSI 대상 서버를 사용할 수 없는 경우 SBD 펜싱은 Azure 펜스 에이전트를 백업 펜싱 메커니즘으로 사용할 수 있습니다.

가상 머신 하나를 추가로 투자하지 않으려면 Azure 펜스 에이전트를 사용할 수도 있습니다. 단점은 리소스 중지가 실패하거나 클러스터 노드가 더 이상 서로 통신할 수 없는 경우 장애 조치(failover)에 10~15분이 걸릴 수 있습니다.

![SLES의 Pacemaker 개요](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>SBD 펜싱

펜싱에 SBD 장치를 사용하려면 다음 단계를 수행하세요.

### <a name="set-up-an-iscsi-target-server"></a>iSCSI 대상 서버 설정

iSCSI 대상 가상 머신이 없는 경우 우선 iSCSI 대상 가상 머신을 만들어야 합니다. iSCSI 대상 서버는 여러 Pacemaker 클러스터와 공유할 수 있습니다.

1. 새 SLES 12 SP1 이상의 가상 머신을 배포하고 ssh를 통해 컴퓨터에 연결합니다. 컴퓨터는 클 필요가 없습니다. Standard_E2s_v3 또는 Standard_D2s_v3과 같은 가상 머신 크기이면 충분합니다.

1. SLES 업데이트

   <pre><code>
   sudo zypper update
   </code></pre>

1. 패키지 제거

   targetcli 및 SLES 12 SP3의 알려진 문제를 방지하려면 다음 패키지를 제거합니다. 찾을 수 없는 패키지에 대한 오류는 무시해도 됩니다.
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. iSCSI 대상 패키지 설치

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. iSCSI 대상 서비스를 사용하도록 설정

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>iSCSI 대상 서버에 iSCSI 장치 만들기

이 클러스터에 사용할 수 있는 iSCSI 대상 가상 머신에 새 데이터 디스크를 연결합니다. 데이터 디스크는 1GB 정도로 작을 수 있으며 [단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)의 혜택을 받으려면 Premium Storage 계정이나 프리미엄 관리 디스크에 배치해야 합니다.

**iSCSI 대상 VM**에서 다음 명령을 실행하여 새 클러스터에 대한 iSCSI 디스크를 만듭니다. 다음 예에서 **cl1**은 새 클러스터를 식별하는 데 사용되고 **prod-cl1-0** 및 **prod-cl1-1**은 클러스터 노드의 호스트 이름입니다. 이것을 클러스터 노드의 호스트 이름으로 바꿉니다.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>SBD 장치 설정

클러스터의 마지막 단계에서 만든 iSCSI 장치에 연결합니다.
새로 만들 클러스터의 노드에서 다음 명령을 실행합니다.
다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** iSCSI 장치에 연결

   먼저 iSCSI 및 SBD 서비스를 사용하도록 설정합니다.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 첫 번째 노드에서 초기자 이름 변경

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI 대상 서버에서 iSCSI 장치를 만들 때 사용한 ACL과 일치하도록 파일의 콘텐츠 변경

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]** 두 번째 노드에서 초기자 이름 변경

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI 대상 서버에서 iSCSI 장치를 만들 때 사용한 ACL과 일치하도록 파일의 콘텐츠 변경

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** iSCSI 서비스 다시 시작

   이제 iSCSI 서비스를 다시 시작하여 변경 내용 적용
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   iSCSI 장치를 연결합니다. 아래 예에서 10.0.0.17은 iSCSI 대상 서버의 IP 주소이고 3260은 기본 포트입니다. <b>iqn.2006-04.cl1.local:cl1</b>은 첫 번째 명령을 실행할 때 나열되는 대상 이름입니다.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   iSCSI 장치를 사용할 수 있는지 확인하고 장치 이름 기록(다음 예의 경우 /dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   이제 iSCSI 장치의 ID를 검색합니다.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   이 명령은 세 가지 장치 ID를 나열합니다. scsi-3으로 시작하는 ID를 사용하는 것이 좋습니다. 위의 예에서 해당 ID는 다음과 같습니다.
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** SBD 장치 만들기

   iSCSI 장치의 장치 ID를 사용하여 첫 번째 클러스터 노드에 새 SBD 장치를 만듭니다.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** SBD 구성 조정

   SBD 구성 파일 열기

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   SBD 장치의 속성을 변경하고, Pacemaker 통합을 활성화하고, SBD의 시작 모드를 변경합니다.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   softdog 구성 파일 만들기

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   모듈 로드하기

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>클러스터 설치

다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** SLES 업데이트

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** ssh 액세스 사용

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** ssh 액세스 사용

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** ssh 액세스 사용

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** HA 확장 설치
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** 호스트 이름 확인 설정   

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다. /etc/hosts를 사용하는 장점은 클러스터가 단일 실패 지점이 될 수 있는 DNS와 무관하다는 점입니다.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** 클러스터 설치
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** 클러스터에 노드 추가
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** hacluster 암호를 동일한 암호로 변경

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** 다른 전송을 사용하고 nodelist를 추가하도록 corosync 구성. 그렇지 않으면 클러스터가 작동하지 않습니다.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   값이 없거나 다른 경우 파일에 다음과 같이 굵게 표시된 콘텐츠를 추가합니다.
   
   <pre><code> 
   [...]
     <b>token:          5000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   그런 다음 corosync 서비스를 다시 시작합니다.

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Pacemaker 기본 설정 변경

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>STONITH 장치 만들기

STONITH 장치에서는 서비스 주체를 사용하여 Microsoft Azure에 대해 권한을 부여합니다. 다음 단계에 따라 서비스 주체를 만듭니다.

1. <https://portal.azure.com>으로 이동합니다.
1. Azure Active Directory 블레이드 열기  
   속성으로 이동하여 Directory ID 기록 이 ID는 **테넌트 ID**입니다.
1. 앱 등록 클릭
1. 추가를 클릭합니다.
1. 이름을 입력하고 응용 프로그램 유형 "웹앱/API"를 선택한 다음, 로그온 URL(예: http://localhost))을 입력하고 만들기를 클릭
1. 로그온 URL이 사용되지 않으며, 이 URL은 임의의 올바른 URL이 될 수 있음
1. 새 앱을 선택하고 설정 탭에서 키 클릭
1. 새 키의 설명을 입력하고 “만료되지 않음”을 선택한 다음 저장을 클릭
1. 값을 기록해 둡니다. 서비스 주체의 **암호**로 사용됨
1. 응용 프로그램 ID를 적어둡니다. 서비스 주체의 사용자 이름(아래 단계의 **로그인 ID**)으로 사용됨

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 펜스 에이전트에 대한 사용자 지정 역할 만들기

서비스 주체에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다. 서비스 주체에 클러스터의 모든 가상 머신을 시작 및 중지(할당 취소)하기 위한 권한을 제공해야 합니다. 사용자 지정 역할을 아직 만들지 않은 경우 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) 또는 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)를 사용하여 만들 수 있습니다.

입력 파일에 다음 콘텐츠를 사용합니다. 구독에 맞게 콘텐츠를 조정해야 합니다. 즉 c276fc76-9cd4-44c9-99a7-4fd71546436e 및 e91d47c4-76f3-4271-a796-21b4ecfe3624를 구독의 ID로 교체해야 합니다. 구독이 하나만 있는 경우 AssignableScopes에서 두 번째 항목을 제거합니다.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** 서비스 주체에 사용자 지정 역할 할당

마지막 단원에서 만든 사용자 지정 역할인 "Linux 펜스 에이전트 역할"을 서비스 주체에 할당합니다. 소유자 역할을 더 이상 사용하지 마십시오!

1. https://portal.azure.com으로 이동합니다.
1. 모든 리소스 블레이드 열기
1. 첫 번째 클러스터 노드의 가상 머신 선택
1. 액세스 제어(IAM) 클릭
1. 추가를 클릭합니다.
1. "Linux 펜스 에이전트 역할"이라는 역할 선택
1. 위에서 만든 응용 프로그램의 이름 입력
1. 확인 클릭

두 번째 클러스터 노드에 위 단계 반복

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH 장치 만들기

가상 머신의 권한을 편집하고 나면 클러스터의 STONITH 장치를 구성할 수 있습니다.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]** SBD 펜싱에 대한 펜스 토폴로지 만들기

SBD 장치를 사용하려면 iSCSI 대상 서버를 사용할 수 없는 경우 Azure 펜스 에이전트를 백업으로 사용하는 것이 좋습니다.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** STONITH 장치를 사용하도록 설정

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>다음 단계
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
