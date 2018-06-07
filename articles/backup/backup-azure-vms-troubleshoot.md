---
title: Azure Virtual Machines의 백업 오류 문제 해결 | Microsoft Docs
description: Azure 가상 머신의 백업 및 복원 문제 해결
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2018
ms.author: trinadhk;markgal;jpallavi;sogup
ms.openlocfilehash: 25008736dbff87aafe2f2ef2d13bbaf746e95e4d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 가상 머신 백업 문제 해결
아래 표에 나열된 정보를 참조하여 Azure Backup을 사용하는 동안 발생하는 오류를 해결할 수 있습니다.

| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| VM이 더 이상 존재하지 않기 때문에 작업을 수행할 수 없습니다. - 백업 데이터를 삭제하지 않고 가상 머신의 보호를 중지합니다. http://go.microsoft.com/fwlink/?LinkId=808124의 자세한 내용 |이는 주 VM이 삭제되었지만 백업 정책이 백업을 수행하기 위해 계속 VM을 검색할 때 발생합니다. 이 오류를 해결하려면  <ol><li> 동일한 이름 및 동일한 리소스 그룹 이름[클라우드 서비스 이름]으로 가상 머신을 다시 만듭니다.<br>또는</li><li> 백업 데이터를 삭제하거나 삭제하지 않고 가상 머신의 보호를 중지합니다. [자세한 내용](http://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| 가상 머신에 네트워크 연결이 없으므로 스냅숏 작업이 실패했습니다. VM이 네트워크에 액세스할 수 있는지 확인하세요. 스냅숏이 성공하기 위해서는 Azure 데이터 센터 IP 범위를 허용 목록에 추가하거나 네트워크 액세스를 위해 프록시 서버를 설정하세요. 자세한 내용은 http://go.microsoft.com/fwlink/?LinkId=800034을 참조하세요. 이미 프록시 서버를 사용 중인 경우 프록시 서버 설정이 제대로 구성되어 있는지 확인합니다. | 이 오류는 가상 머신에서 아웃바운드 인터넷 연결을 거부하는 경우 throw됩니다. 가상 컴퓨터의 기본 디스크 스냅숏을 만들기 위해서는 VM 스냅숏 확장에 인터넷 연결이 필요합니다. 네트워크 액세스 차단으로 인한 스냅숏 오류를 해결하는 방법에 대해 [자세히 알아보세요](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine). |
| VM 에이전트가 Azure Backup 서비스와 통신할 수 없습니다. - VM이 네트워크에 연결되어 있고 VM 에이전트가 최신이며 실행 중인지 확인합니다. 자세한 내용은 http://go.microsoft.com/fwlink/?LinkId=800034를 참조하세요. |이 오류는 VM 에이전트에 문제가 있거나 Azure 인프라에 대한 네트워크 액세스가 어떤 방식으로든 차단된 경우에 발생합니다. VM 스냅숏 문제 디버깅에 대해 [자세히 알아봅니다](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup).<br> VM 에이전트가 아무 문제도 유발하지 않으면, VM을 다시 시작합니다. 가끔 잘못된 VM 상태가 문제를 일으킬 수 있으며 VM을 다시 시작하여 “잘못된 상태”를 초기화합니다. |
| VM이 실패한 프로비전 상태임 - VM을 다시 시작하고 VM이 실행 중이거나 백업을 위해 종료 상태인지 확인하세요. | 확장 오류 중 하나로 인해 VM 상태가 실패한 프로비전 상태로 이어질 때 이러한 현상이 발생합니다. 확장 목록으로 이동하여 실패한 확장이 있는지 확인하고 제거한 후 가상 머신을 다시 시작해 보세요. 모든 확장이 실행 중 상태인 경우 VM 에이전트 서비스가 실행 중인지 확인합니다. 실행 중이 아니면 VM 에이전트 서비스를 다시 시작합니다. | 
| 관리 디스크에 대해 VMSnapshot 확장 작업이 실패함 - 백업 작업을 다시 시도하세요. 문제가 반복되면 'http://go.microsoft.com/fwlink/?LinkId=800034'의 지침에 따릅니다. 그래도 실패하면 Microsoft 지원에 문의하시기 바랍니다. | Azure Backup 서비스에서 스냅숏을 트리거하는 데 실패한 경우 이 오류가 발생합니다. VM 스냅숏 디버깅 문제에 대해 [자세히 알아보세요](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed). |
| 저장소 계정에 사용 가능한 공간이 부족하여 가상 머신의 스냅숏을 복사할 수 없음 - 저장소 계정에 가상 머신에 연결된 프리미엄 저장소 디스크에 있는 데이터에 해당하는 만큼의 여유 공간이 있는지 확인합니다. | 프리미엄 VM의 경우 저장소 계정에 스냅숏을 복사합니다. 이 작업은 스냅숏에서 작동하는 백업 관리 트래픽이 프리미엄 디스크를 사용하는 응용 프로그램에서 사용 가능한 IOPS 수를 제한하지 않도록 하기 위한 것입니다. Azure Backup 서비스에서 저장소 계정에 스냅숏을 복사하고 저장소 계정의 복사된 위치에서 자격 증명 모음으로 데이터를 전송할 수 있도록 총 저장소 계정 공간의 50%만 할당하는 것이 좋습니다. | 
| VM 에이전트가 응답하지 않으므로 작업을 수행할 수 없습니다. |이 오류는 VM 에이전트에 문제가 있거나 Azure 인프라에 대한 네트워크 액세스가 어떤 방식으로든 차단된 경우에 발생합니다. Windows VM의 경우 서비스의 VM 에이전트 서비스 상태 및 에이전트가 제어판의 프로그램에 표시되는 여부를 확인합니다. 제어판에서 프로그램을 제거하고 [아래](#vm-agent) 설명에 따라 에이전트를 다시 설치합니다. 에이전트를 다시 설치한 후 임시 백업을 트리거하여 확인합니다. |
| 복구 서비스 확장 작업이 실패했습니다. - 최신 가상 머신 에이전트가 가상 머신에 표시되고 에이전트 서비스가 실행되도록 하세요. 백업 작업을 다시 시도하고 실패한 경우 Microsoft 지원에 문의하세요. |VM 에이전트가 만료된 경우에 이 오류가 throw됩니다. 아래의 "VM 에이전트 업데이트" 섹션을 참조하여 VM 에이전트를 업데이트합니다. |
| 가상 머신이 존재하지 않습니다. - 해당 가상 컴퓨터가 존재하는지 확인하거나 다른 가상 컴퓨터를 선택하세요. |이는 주 VM이 삭제되었지만 백업 정책이 백업을 수행하기 위해 계속 VM을 검색할 때 발생합니다. 이 오류를 해결하려면  <ol><li> 동일한 이름 및 동일한 리소스 그룹 이름[클라우드 서비스 이름]으로 가상 머신을 다시 만듭니다.<br>또는<br></li><li>백업 데이터를 삭제하지 않고 가상 컴퓨터의 보호를 중지합니다. [자세한 내용](http://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| 명령을 실행하지 못했습니다. - 현재 이 항목에 대해 다른 작업이 진행 중입니다. 이전 작업이 완료될 때까지 기다린 후 다시 시도하세요. |VM에 대한 기존 백업이 실행 중이며, 기존 작업이 실행되는 동안에는 새 작업을 시작할 수 없습니다. |
| 백업 자격 증명 모음에서 VHD를 복사하는 작업이 시간 초과되었습니다. 몇 분 후에 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. | 저장소 쪽에서 일시적인 오류가 발생하거나 백업 서비스가 제한 시간 내에 VM을 호스트하는 저장소 계정에서 Vault로 데이터를 전송하기 위한 충분한 IOPS를 얻지 못하는 경우에 이러한 문제가 발생합니다. 백업을 설정하는 동안 [모범 사례](backup-azure-vms-introduction.md#best-practices)를 따랐는지 확인하세요. VM을 로드되지 않은 다른 저장소 계정으로 이동한 후 백업을 다시 시도하세요.|
| 내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |이 오류는 다음 두 가지 이유로 발생할 수 있습니다. <ol><li> VM 저장소에 액세스하는 데 일시적인 문제가 있습니다. [Azure 상태](https://azure.microsoft.com/status/)를 확인하여 하위 지역의 계산, 저장소 또는 네트워크와 관련하여 진행 중인 문제가 있는지 확인합니다. 문제가 해결되면 백업 작업을 다시 시도합니다. <li>원래 VM이 삭제되었으므로 복구 지점을 가져올 수 없습니다. 삭제된 VM의 백업 데이터를 유지하지만 백업 오류를 제거하려면 VM의 보호를 해제하고 데이터 유지 옵션을 선택합니다. 이 작업을 수행하면 예약된 백업 작업 및 되풀이 오류 메시지가 중지됩니다. |
| 선택한 항목에 Azure Recovery Services 서비스 확장을 설치하지 못했습니다. Azure Recovery Services 확장의 필수 조건인 VM 에이전트가 있어야 합니다. Azure VM 에이전트를 설치하고 등록 작업 다시 시작 |<ol> <li>VM 에이전트가 제대로 설치되었는지 확인합니다. <li>VM 구성의 플래그가 올바르게 설정되었는지 확인합니다.</ol> VM 에이전트 설치 및 VM 에이전트 설치의 유효성을 검사하는 방법에 대해 [자세히 알아보세요](#validating-vm-agent-installation). |
| “COM+” 오류로 인해 확장 설치가 실패하면 Microsoft Distributed Transaction Coordinator와 통신할 수 없습니다. |이는 대개 COM+ 서비스가 실행되고 있지 않음을 의미합니다. 이 문제 해결에 대한 도움은 Microsoft 지원에 문의하세요. |
| “이 드라이브는 BitLocker 드라이브 암호화로 잠겨 있습니다.”라는 VSS 작업 오류와 함께 스냅숏 작업이 실패했습니다. 제어판에서 이 드라이브의 잠금을 해제해야 합니다. |VM에 있는 모든 드라이브의 BitLocker를 끄고 VSS 문제가 해결 되었는지 관찰합니다. |
| VM이 백업을 허용하지 않는 상태입니다. |<ul><li>VM이 실행 중 및 종료 사이의 일시적 상태인지 확인합니다. 그럴 경우 VM 상태가 이러한 상태 중 하나가 될 때까지 기다렸다가 백업을 다시 트리거하세요. <li> VM이 Linux 에이전트이고 [Security Enhanced Linux] 커널 모듈을 사용하는 경우 보안 정책에서 Linux 에이전트 경로(_/var/lib/waagent_)를 제외하여 백업 확장이 설치되도록 해야 합니다.  |
| Azure Virtual Machine을 찾을 수 없습니다. |이는 주 VM이 삭제되었지만 백업 정책이 백업을 수행하기 위해 계속 VM을 검색할 때 발생합니다. 이 오류를 해결하려면  <ol><li>동일한 이름 및 동일한 리소스 그룹 이름[클라우드 서비스 이름]으로 가상 머신을 다시 만듭니다. <br>또는 <li> 이 VM에 대한 보호를 사용하지 않도록 설정하여 백업 작업이 생성되지 않게 합니다. </ol> |
| 가상 머신에는 가상 머신 에이전트가 존재하지 않습니다. 필수 구성 요소 및 VM 에이전트를 설치하고 작업을 다시 시작하세요. |[자세히 알아보세요](#vm-agent) . |
| VSS 기록기가 오류 상태이므로 스냅숏 작업이 실패함 |오류 상태인 VSS(볼륨 섀도 복사본 서비스) 기록기를 다시 시작해야 합니다. 이를 위해 관리자 권한 명령 프롬프트에서 _vssadmin list writers_를 실행합니다. 출력에는 모든 VSS 기록기와 해당 상태가 포함됩니다. “[1] 안정” 상태가 아닌 모든 VSS 기록기는 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 다시 시작합니다.<br> _net stop serviceName_ <br> _net start serviceName_|
| 구성의 구문 분석 실패로 인해 스냅숏 작업이 실패함 |이 문제는 MachineKeys 디렉터리 _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_에 대한 권한 변경 때문에 발생합니다. <br>아래 명령을 실행하고 MachineKeys 디렉터리에 대한 권한이 기본 권한인지 확인합니다.<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> 기본 권한은 다음과 같습니다.<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>MachineKeys 디렉터리에 대해 기본값과 다른 권한이 표시되면 아래 단계에 따라 권한을 수정하고, 인증서를 삭제한 후 백업을 트리거하세요.<ol><li>MachineKeys 디렉터리에 대한 권한을 수정합니다.<br>디렉터리에 대해 탐색기 보안 속성 및 고급 보안 설정을 사용하여 권한을 기본값으로 다시 설정하고, 디렉터리에서 추가(기본값 이외) 사용자 개체를 제거하고, 'Everyone ' 권한이 다음에 대해 특수한 액세스 권한을 갖는지 확인합니다.<br>-폴더 나열/데이터 읽기 <br>-특성 읽기 <br>-확장된 특성 읽기 <br>-파일 만들기/데이터 쓰기 <br>-폴더 만들기/데이터 추가<br>-특성 쓰기<br>-확장된 특성 쓰기<br>- 읽기 권한<br><br><li>‘Issued To’ 필드 = "Windows Azure Service Management for Extensions" 또는 "Windows Azure CRP Certificate Generator"가 지정된 인증서를 모두 삭제합니다.<ul><li>[인증서(로컬 컴퓨터) 콘솔 열기](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>‘Issued To’ 필드 = "Windows Azure Service Management for Extensions" 또는 "Windows Azure CRP Certificate Generator"가 지정된 인증서를 모두 삭제합니다(개인 -> 인증서).</ul><li>VM 백업을 트리거합니다. </ol>|
| Azure Backup 서비스에는 암호화된 Virtual Machines의 백업을 위한 Key Vault에 대해 충분한 권한이 없습니다. |Backup 서비스에는 [PowerShell 설명서](backup-azure-vms-automation.md)의 **백업 사용** 섹션에 설명된 단계를 사용하여 PowerShell에서 이러한 권한을 제공해야 합니다. |
|COM+ 오류로 인해 스냅숏 확장 설치가 실패하면 Microsoft Distributed Transaction Coordinator와 통신할 수 없습니다. | "COM+ 시스템 응용 프로그램" Windows 서비스를 시작하세요(관리자 권한 명령 프롬프트에서 _net start COMSysApp_). <br>시작하는 동안 실패하면 아래 단계를 수행하세요.<ol><li> "Distributed Transaction Coordinator" 서비스의 로그온 계정이 "Network Service"인지 확인합니다. 그렇지 않으면 "Network Service"로 변경하고 이 서비스를 다시 시작한 후 "COM+ 시스템 응용 프로그램" 서비스를 시작합니다.<li>여전히 시작할 수 없으면 아래 단계에 따라 "Distributed Transaction Coordinator" 서비스를 제거한 후 설치합니다.<br> - MSDTC 서비스 중지<br> - 명령 프롬프트 열기(cmd) <br> - “msdtc -uninstall” 명령 실행 <br> - “msdtc -install” 명령 실행 <br> - MSDTC 서비스 시작<li>Windows 서비스 "COM+ 시스템 응용 프로그램"을 시작한 후 포털에서 백업을 트리거합니다.</ol> |
|  COM+ 오류로 인해 스냅숏 작업이 실패했습니다. | 권장 조치는 Windows 서비스 "COM+ 시스템 응용 프로그램"을 다시 시작하는 것입니다(관리자 권한 명령 프롬프트에서 _net start COMSysApp_ 실행). 문제가 지속되면 VM을 다시 시작합니다. VM을 다시 시작해도 문제가 해결되지 않으면 [VMSnapshot 확장을 제거](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load)하고 수동으로 백업을 트리거합니다. |
| 파일-시스템의 일관된 스냅숏을 생성하기 위해 VM의 탑재 지점 하나 이상을 고정하지 못함 | 다음 단계를 사용하세요. <ol><li>_'tune2fs'_ 명령을 사용하여 탑재된 모든 장치의 파일-시스템 상태를 확인합니다.<br> 예: tune2fs -l /dev/sdb1 \| grep "Filesystem state" <li>_'umount'_ 명령을 사용하여 파일 시스템 상태가 정리되지 않은 장치를 탑재 해제합니다. <li> _'fsck'_ 명령을 사용하여 이러한 장치에 대해 FileSystemConsistency Check를 실행합니다. <li> 장치를 다시 탑재하고 백업을 시도합니다.</ol> |
| 보안 네트워크 통신 채널을 생성하지 못하여 스냅숏 작업이 실패함 | <ol><Li> 관리자 권한 모드에서 regedit.exe를 실행하여 레지스트리 편집기를 엽니다. <li> 시스템에 있는 모든 버전의 .NetFramework를 파악합니다. 이러한 버전은 레지스트리 키 "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" 아래에 있습니다. <li> 레지스트리 키에 있는 각 .NetFramework에 대해 다음 키를 추가합니다. <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Visual Studio 2012용 Visual C++ 재배포 가능 패키지의 설치 실패로 인해 스냅숏 작업이 실패함 | C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion으로 이동한 후 vcredist2012_x64를 설치합니다. 이 서비스 설치를 허용하는 레지스트리 키 값이 올바른 값으로 설정되어 있는지 확인합니다. 예를 들어 레지스트리 키 _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_의 값은 4가 아닌 3으로 설정되어야 합니다. 설치하는 데 여전히 문제가 발생할 경우 관리자 권한 명령 프롬프트에서 _MSIEXEC /UNREGISTER_를 실행한 후 _MSIEXEC /REGISTER_를 실행하여 설치 서비스를 다시 시작합니다.  |


## <a name="jobs"></a>교육
| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| 이 작업 유형에 대해서는 취소가 지원되지 않습니다. 작업이 완료될 때까지 기다려주세요. |없음 |
| 작업이 취소 가능한 상태에 있지 않습니다. 작업이 완료될 때까지 기다려주세요. <br>또는<br> 선택한 작업이 취소 가능한 상태가 아닙니다. 작업이 완료될 때까지 기다려주세요. |작업이 거의 완료되었습니다. 작업이 완료될 때까지 기다리세요.|
| 진행되고 있지 않기 때문에 작업을 취소할 수 없습니다. 취소는 작업이 진행 중인 경우에만 지원됩니다. 진행 중인 작업에 대해 취소를 시도하세요. |이는 일시적인 상태 때문에 발생합니다. 잠시 기다렸다가 취소 작업을 다시 시도하세요. |
| 작업을 취소하지 못했습니다. 작업이 완료될 때까지 기다려주세요. |없음 |

## <a name="restore"></a>복원
| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| 클라우드 내부 오류로 인해 복원 실패 |<ol><li>복원하려는 클라우드 서비스가 DNS 설정을 사용하여 구성되었습니다. 다음을 확인하면 알 수 있습니다. <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>구성된 주소가 있으면 DNS 설정이 구성되었다는 의미입니다.<br> <li>복원하려는 클라우드 서비스가 ReservedIP를 사용하여 구성되고 클라우드 서비스의 기존 VM이 중단된 상태에 있습니다.<br>다음 powershell cmdlet을 사용하여 클라우드 서비스에 예약된 IP가 있는지 확인할 수 있습니다.<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>동일한 클라우드 서비스에 다음과 같이 특수한 네트워크 구성을 사용하여 가상 컴퓨터를 복원하려고 시도하고 있습니다. <br>- 부하 분산 장치 구성의 가상 머신(내부 및 외부)<br>- 여러 개의 예약된 IP를 사용하는 가상 머신<br>- 여러 NIC가 있는 가상 머신<br>특수한 네트워크 구성을 가진 VM의 경우 [복원 고려 사항](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)을 참조하거나 UI에서 새 클라우드 서비스를 선택하세요</ol> |
| 선택한 DNS 이름이 이미 사용 되었습니다. 다른 DNS 이름을 지정하고 다시 시도하세요. |여기에서 DNS 이름은 클라우드 서비스 이름을 가리킵니다(일반적으로 cloudapp.net로 끝남). 이름은 고유한 것이어야 합니다. 이러한 오류가 발생하는 경우, 복원하는 동안 다른 VM 이름을 선택해야 합니다. <br><br> 이 오류는 Azure 포털의 사용자에게만 표시됩니다. PowerShell 통한 복원 작업은 디스크만 복원하고 VM을 만들지 않기 때문에 성공합니다. 디스크 복원 작업 후 사용자가 명시적으로 VM를 만들 경우 오류가 발생합니다. |
| 지정된 가상 네트워크 구성이 올바르지 않습니다. 다른 가상 네트워크 구성에 지정하고 다시 시도하세요. |없음 |
| 지정된 클라우드 서비스는 복원 중인 가상 머신의 구성과 일치하지 않는 예약된 IP를 사용하고 있습니다. 예약된 IP를 사용하지 않는 다른 클라우드 서비스를 지정하거나 복원하려면 다른 복구 지점을 선택하세요. |없음 |
| 클라우드 서비스가 입력된 끝점 제한 수에 도달 했습니다. 다른 클라우드 서비스를 지정하거나 기존 끝점을 사용하여 작업을 다시 시도합니다. |없음 |
| 서로 다른 두 지역에 Backup 저장소와 대상 저장소 계정이 있습니다. 복원 작업에서 지정된 저장소 계정이 백업 저장소와 동일한 Azure 지역에 있는지 확인합니다. |없음 |
| 복원 작업에 대해 지정된 Storage 계정은 지원되지 않습니다. 로컬 중복 또는 지리적 중복 복제 설정이 있는 기본/표준만 Storage 계정만 지원됩니다. 지원되는 저장소 계정을 선택하세요. |없음 |
| 복원 작업에 지정된 Storage 계정 유형은 온라인 상태가 아닙니다. 복원 작업에 지정된 Storage 계정이 온라인 상태인지 확인하세요. |이는 Azure Storage의 일시적인 오류 또는 가동 중지로 인해 발생할 수 있습니다. 다른 저장소 계정을 선택하세요. |
| 리소스 그룹 할당량에 도달했습니다. Azure 포털의 일부 리소스 그룹을 삭제하거나 Azure 지원에 문의하여 제한을 늘리세요. |없음 |
| 선택한 서브넷이 존재하지 않습니다. 존재하는 서브넷을 선택하세요. |없음 |
| Backup 서비스는 구독의 리소스에 액세스할 수 있는 권한이 없습니다. |이 문제를 해결하려면 먼저 [VM 복원 구성 선택](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration)의 **백업된 디스크 복원** 섹션에 제공된 단계에 따라 디스크를 복원합니다. 그 후에는 [복원된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)에 설명된 PowerShell 단계를 사용하여 복원된 디스크에서 전체 VM을 만듭니다. |

## <a name="backup-or-restore-taking-time"></a>Backup 또는 복원에 시간이 걸림
백업(12시간 초과) 또는 복원 소요 시간(6시간 초과)이 표시되는 경우
* [백업 시간에 영향을 주는 요인](backup-azure-vms-introduction.md#total-vm-backup-time) 및 [복원 시간에 영향을 주는 요인](backup-azure-vms-introduction.md#total-restore-time)을 파악합니다.
* [Backup 모범 사례](backup-azure-vms-introduction.md#best-practices)를 따라야 합니다. 

## <a name="vm-agent"></a>VM 에이전트
### <a name="setting-up-the-vm-agent"></a>VM 에이전트 설정
일반적으로, Azure 갤러리에서 만든 VM에는 VM 에이전트가 이미 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션한 가상 머신에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 VM의 경우 VM 에이전트를 명시적으로 설치해야 합니다.

Windows VM의 경우

* [에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다.
* 클래식 가상 머신의 경우 [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. 리소스 관리자 가상 머신의 경우 이 단계가 필요하지 않습니다.

Linux VM의 경우

* 배포 리포지토리에서 최신 버전을 설치합니다. 배포 리포지토리를 사용할 때만 에이전트를 설치할 것을 **강력히 권장**합니다. 패키지 이름에 대한 자세한 내용은 [Linux 에이전트 리포지토리](https://github.com/Azure/WALinuxAgent)를 참조하세요. 
* 클래식 VM의 경우 [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. 리소스 관리자 가상 머신의 경우 이 단계가 필요하지 않습니다.

### <a name="updating-the-vm-agent"></a>VM 에이전트 업데이트
Windows VM의 경우

* VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. 그러나 VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 해야 합니다.

Linux VM의 경우

* [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침을 따르세요.
배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 **강력히 권장**합니다. github에서 에이전트 코드를 직접 다운로드한 후 업데이트하는 것은 바람직하지 않습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 최신 에이전트를 설치하는 방법에 대한 지침을 얻으세요. github 리포지토리에서 최신 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 정보를 확인할 수 있습니다.

### <a name="validating-vm-agent-installation"></a>VM 에이전트 설치의 유효성 검사
Windows VM에서 VM 에이전트 버전을 확인하는 방법

1. Azure Virtual Machine에 로그온하고 *C:\WindowsAzure\Packages* 폴더로 이동합니다. WaAppAgent.exe 파일을 찾습니다.
2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다.

## <a name="troubleshoot-vm-snapshot-issues"></a>VM 스냅숏 문제 해결
VM 백업은 기본 저장소에 대한 스냅숏 명령 실행을 사용합니다. 저장소에 액세스할 수 없거나 스냅숏 작업 실행이 지연되는 경우 백업 작업이 실패할 수 있습니다. 다음으로 인해 스냅숏 작업이 실패할 수 있습니다.

1. NSG를 사용하여 저장소에 대한 네트워크 액세스 차단<br>
    IP의 허용 목록을 사용하거나 프록시 서버를 통해 저장소에 대한 [네트워크 액세스를 허용](backup-azure-arm-vms-prepare.md#establish-network-connectivity)하는 방법을 자세히 알아봅니다.
2. Sql Server 백업이 구성된 VM이 스냅숏 작업을 지연시킬 수 있습니다. <br>
   기본적으로 VM 백업은 Windows VM에서 VSS 전체를 백업합니다. VM에서 Sql Server를 실행하고 있으며 Sql Server 백업이 구성된 경우 스냅숏 실행 시 지연이 발생할 수 있습니다. 스냅숏 문제로 인해 백업이 실패하면 다음 레지스트리 키를 설정하세요.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. VM이 RDP에서 종료되므로 VM 상태가 잘못 보고됩니다.  <br>
   RDP에서 가상 머신을 종료하는 경우 VM 상태가 올바르게 반영되는지 포털에서 다시 확인하세요. 그렇지 않은 경우 VM 대시보드의 '종료' 옵션을 사용하여 포털에서 VM을 종료하세요.
4. 4개를 초과하는 VM이 동일한 클라우드 서비스를 공유하는 경우에는, 4개를 초과하는 VM 백업이 한 번에 시작되지 않게 백업 시간을 준비하도록 여러 백업 정책을 구성합니다. 백업 시작 시간을 정책 사이에 한 시간 분산시키도록 합니다.
5. VM이 높은 CPU/메모리에서 실행 중입니다.<br>
   가상 머신이 높은 CPU/메모리 사용량(&amp;gt;90%)에서 실행 중인 경우 스냅숏 작업이 큐 대기되고 지연되어 결국 시간이 초과됩니다. 이러한 상황에서는 주문형 백업을 시도하세요.

<br>

## <a name="networking"></a>네트워킹
모든 확장과 마찬가지로, Backup 확장이 작동하려면 공용 인터넷에 액세스해야 합니다. 공용 인터넷에 액세스할 수 없는 경우는 다음과 같은 여러 방법으로 확인할 수 있습니다.

* 확장 설치가 실패할 수 있습니다.
* 백업 작업(예: 디스크 스냅숏)이 실패할 수 있습니다.
* 백업 작업의 상태가 표시되지 않을 수 있습니다.

공용 인터넷 주소를 확인해야 하는 이유는 [여기](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)에 명시되어 있습니다. VNET에 대한 DNS 구성을 검사하고 Azure URI를 확인할 수 있는지 확인해야 합니다.

이름 확인이 올바르게 완료된 후에는 Azure IP에 대한 액세스 권한을 부여해야 합니다. Azure 인프라에 대한 액세스 차단을 해제하려면 다음 단계 중 하나를 따르세요.

1. Azure 데이터 센터 IP 범위를 허용 목록에 추가합니다.
   * 허용 목록에 추가할 [Azure datacenter IP](https://www.microsoft.com/download/details.aspx?id=41653) 목록을 가져옵니다.
   * [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) cmdlet을 사용하여 IP 차단을 해제합니다. 관리자 권한 PowerShell 창(관리자 권한으로 실행)을 통해 Azure VM 내에서 이 cmdlet을 실행합니다.
   * IP에 대한 액세스를 허용하도록 NSG(있는 경우)에 규칙을 추가합니다.
2. HTTP 트래픽을 보내는 경로 만들기
   * 일부 네트워크 제한이 있는 경우(예: 네트워크 보안 그룹) 트래픽을 라우팅하는 HTTP 프록시 서버를 배포합니다. HTTP 프록시 서버를 배포하는 단계는 [여기](backup-azure-arm-vms-prepare.md#establish-network-connectivity)에서 찾을 수 있습니다.
   * HTTP 프록시에서 인터넷에 액세스할 수 있도록 NSG(있는 경우)에 규칙을 추가합니다.

> [!NOTE]
> IaaS VM Backup이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다.  고정 개인 IP가 필요한 경우 플랫폼을 통해 구성해야 합니다. VM 내 DHCP 옵션은 사용 가능한 상태로 두어야 합니다.
> [고정 내부 개인 IP 설정](../virtual-network/virtual-networks-reserved-private-ip.md)에 대한 자세한 내용을 확인합니다.
>
>
