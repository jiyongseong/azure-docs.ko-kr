---
title: 'Azure Backup 오류 문제 해결: 게스트 에이전트 상태 사용할 수 없음 | Microsoft Docs'
description: 에이전트, 확장명 및 디스크와 관련된 Azure Backup 오류의 증상, 원인 및 해결 방법
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Azure 백업; VM 에이전트; 네트워크 연결;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: 17f4f832af0177ad588058833672c0986adeb3fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196766"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup 오류 문제 해결: 에이전트 또는 확장 관련 문제

이 문서에서는 VM 에이전트 및 확장과의 통신에 관련된 Azure Backup 오류를 해결하는 데 도움이 될 수 있는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM 에이전트가 Azure Backup과 통신할 수 없음

오류 메시지: “VM 에이전트가 Azure Backup과 통신할 수 없습니다.”<br>
오류 코드: "UserErrorGuestAgentStatusUnavailable"

Backup 서비스에 대한 VM을 등록하고 예약하면 Backup은 VM 에이전트와 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않을 수 있습니다. 스냅숏이 트리거되지 않으면 백업이 실패할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.

**원인 1: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**  
**원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 4: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**원인 5: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>가상 컴퓨터가 네트워크에 연결되어 있지 않으므로 스냅숏 작업이 실패합니다.

오류 메시지: “가상 컴퓨터에 네트워크 연결이 없으므로 스냅숏 작업이 실패했습니다.”<br>
오류 코드: "ExtensionSnapshotFailedNoNetwork"

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않을 수 있습니다. 스냅숏이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.    
**원인 1: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**  
**원인 2: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 3: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 확장 작업이 실패합니다.

오류 메시지: “VMSnapshot 확장 작업이 실패했습니다.”<br>
오류 코드: "ExtentionOperationFailed"

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않을 수 있습니다. 스냅숏이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 2: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 3: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 4: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>VM 에이전트가 응답하지 않으므로 백업이 실패합니다.

오류 메시지: “VM 에이전트가 응답하지 않으므로 작업을 수행할 수 없습니다.” <br>
오류 코드: "GuestAgentSnapshotTaskStatusError"

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않을 수 있습니다. 스냅숏이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>내부 오류와 함께 백업이 실패합니다.

오류 메시지: “내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요.” <br>
오류 코드: "BackUpOperationFailed"/ "BackUpOperationFailedV2"

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅숏을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅숏이 트리거되지 않을 수 있습니다. 스냅숏이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [VM이 인터넷에 액세스할 수 없습니다.](#the-vm-has-no-internet-access)**  
**원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 4: [스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 5: [백업 확장을 업데이트 또는 로드할 수 없습니다.](#the-backup-extension-fails-to-update-or-load)**  
**원인 6: [리소스 그룹이 잠겨 있으므로 Backup 서비스에 이전 복원 지점을 삭제할 수 있는 권한이 없습니다.](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-vm-has-no-internet-access"></a>VM이 인터넷에 액세스할 수 없습니다.
배포 요구와 맞지 않게 VM이 인터넷에 액세스할 수 없습니다. 또는 제한으로 인해 Azure 인프라에 액세스하지 못할 수 있습니다.

제대로 작동하려면 Backup 확장이 Azure 공용 IP 주소에 연결되어야 합니다. 확장이 Azure Storage 끝점(HTTP URL)에 명령을 보내 VM의 스냅숏을 관리합니다. 확장이 공용 인터넷에 액세스할 수 없는 경우 백업은 결국 실패합니다.

####  <a name="solution"></a>해결 방법
이 문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>지역에 해당하는 Azure Storage에 대한 액세스 허용

[서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하는 특정 지역의 저장소에 대한 연결을 허용할 수 있습니다. 인터넷 액세스를 차단하는 규칙보다 저장소 계정에 대한 액세스를 허용하는 규칙에 높은 우선 순위가 있는지 확인합니다. 

![지역에 대한 저장소 태그가 있는 네트워크 보안 그룹](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

서비스 태그를 구성하는 단계별 절차를 이해하려면 [이 비디오](https://youtu.be/1EjLQtbKm1M)를 시청합니다.

> [!WARNING]
> 저장소 서비스 태그는 미리 보기 상태입니다. 따라서 특정 지역에서만 사용할 수 있습니다. 지역 목록은 [저장소의 서비스 태그](../virtual-network/security-overview.md#service-tags)를 참조하세요.

##### <a name="create-a-path-for-http-traffic"></a>HTTP 트래픽에 대한 경로 만들기

1. 네트워크 제한이 있는 경우(예를 들어 네트워크 보안 그룹) 트래픽을 라우트하도록 HTTP 프록시 서버를 배포합니다.
2. HTTP 프록시 서버에서 인터넷에 액세스하도록 허용하려면 네트워크 보안 그룹이 있는 경우 규칙을 추가합니다.

VM 백업에 대한 HTTP 프록시를 설정하는 방법을 알아보려면 [Azure Virtual Machines를 백업하기 위한 환경 준비](backup-azure-arm-vms-prepare.md#establish-network-connectivity)를 참조하세요.

Azure Managed Disks를 사용하는 경우 방화벽에서 열려 있는 추가 포트(포트 8443)가 필요할 수 있습니다.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution"></a>해결 방법
VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Windows 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows 게스트 에이전트 서비스를 다시 시작하고 백업을 시작해 보세요.    
2. 서비스에서 Windows 게스트 에이전트 서비스가 표시되지 않으면 제어판에서 **프로그램 및 기능**으로 이동한 후 Windows 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
4. Windows 게스트 에이전트에 **프로그램 및 기능**에 표시되면 Windows 게스트 에이전트를 제거합니다.
5. [최신 버전의 에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
6. Windows 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
7. 다음과 같이 주문형 백업을 실행합니다. 
    * 포털에서 **지금 백업**을 선택합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. .NET 4.5는 VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md)의 지침을 따르세요.

 > [!NOTE]
 > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

 이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

 * Ubuntu의 경우: `service walinuxagent start`
 * 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 새 테스트 백업을 실행합니다. 오류가 계속되면 VM에서 다음 로그를 수집합니다.

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent의 자세한 로깅이 필요한 경우 다음 단계를 따르세요.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다. **자세한 정보 로깅 사용(y|n)**
2. **Logs.Verbose** 값을 *n*에서 *y*로 변경합니다.
3. 변경 내용을 저장하고 이 섹션 앞부분에 설명된 단계를 완료하여 waagent를 다시 시작합니다.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.
VM 백업은 기본 저장소 계정에 대한 스냅숏 명령 실행을 사용합니다. 저장소 계정에 액세스할 수 없거나 스냅숏 작업의 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

#### <a name="solution"></a>해결 방법
다음 조건으로 인해 스냅숏 작업이 실패할 수 있습니다.

| 원인 | 해결 방법 |
| --- | --- |
| VM이 RDP(원격 데스크톱 프로토콜)에서 종료되므로 VM 상태가 잘못 보고됩니다. | RDP에서 VM을 종료하는 경우 VM 상태가 올바른지 여부를 확인하려면 포털을 확인합니다. 올바르지 않으면 VM 대시보드의 **종료** 옵션을 사용하여 포털에서 VM을 종료합니다. |
| VM이 DHCP에서 호스트 또는 패브릭 주소를 가져올 수 없습니다. | IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. VM이 DHCP 응답 245에서 호스트 또는 패브릭 주소를 가져올 수 없는 경우에는 어떠한 확장도 다운로드하거나 실행할 수 없습니다. 고정 개인 IP가 필요한 경우 플랫폼을 통해 구성합니다. VM 내 DHCP 옵션은 사용 가능한 상태로 두어야 합니다. 자세한 내용은 [고정 내부 개인 IP 설정](../virtual-network/virtual-networks-reserved-private-ip.md)을 참조하세요. |

### <a name="the-backup-extension-fails-to-update-or-load"></a>백업 확장을 업데이트 또는 로드할 수 없습니다.
확장을 로드할 수 없는 경우 스냅숏을 만들 수 없기 때문에 백업이 실패합니다.

#### <a name="solution"></a>해결 방법

확장을 제거하여 강제로 VMSnapshot 확장을 다시 로드합니다. 다음 백업 시도 시 확장이 다시 로드됩니다.

확장을 제거하려면

1. [Azure Portal](https://portal.azure.com/)에서 백업 실패가 발생하는 VM으로 이동합니다.
2. **설정**을 선택합니다.
3. **확장**을 섡택합니다.
4. **Vmsnapshot 확장**을 선택합니다.
5. **제거**를 선택합니다.

Linux VM의 경우 VMSnapshot 확장이 Azure Portal에 표시되지 않으면 [Azure Linux 에이전트를 업데이트](../virtual-machines/linux/update-agent.md)한 다음, 백업을 실행합니다. 

이러한 단계를 완료하면 다음 백업 동안 확장을 다시 설치해야 합니다.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>리소스 그룹이 잠겨 있으므로 Backup 서비스에 이전 복원 지점을 삭제할 수 있는 권한이 없습니다.
이 문제는 사용자가 리소스 그룹을 잠그는 관리되는 VM에만 해당됩니다. 이 경우 Backup 서비스는 이전 버전 복원 지점을 삭제할 수 없습니다. 복원 지점 수는 18개로 제한되므로 새 백업을 시작할 수 없습니다.

#### <a name="solution"></a>해결 방법

문제를 해결하려면 리소스 그룹에서 잠금을 제거하고 Azure Backup 서비스에서 복구 지점 컬렉션 및 다음 백업의 기본 스냅숏을 지우도록 합니다.
완료되면 VM 리소스 그룹에 잠금을 다시 넣을 수 있습니다. 

