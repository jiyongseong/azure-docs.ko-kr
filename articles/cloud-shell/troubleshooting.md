---
title: Azure Cloud Shell 문제 해결 | Microsoft Docs
description: Azure Cloud Shell 문제 해결
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 3c01a31eae2b90ecb54cbfba7f565fd140db3773
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell의 문제 해결 및 제한 사항

Azure Cloud Shell의 문제 해결에 대해 알려진 해결 방법은 다음과 같습니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

### <a name="early-timeouts-in-firefox"></a>FireFox의 조기 시간 초과
- **세부 정보**: Cloud Shell은 개방형 WebSocket을 사용하여 브라우저에 입/출력을 전달합니다. FireFox에는 Cloud Shell에서 조기 시간 초과를 야기하는 WebSocket을 닫을 수 있는 미리 설정된 정책이 있습니다.
- **해결 방법**: FireFox를 열고 URL 상자의 "about:config"로 이동합니다. "network.websocket.timeout.ping.request"를 검색하고 값을 0에서 10으로 변경합니다.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>저장소 대화 상자 - 오류: 403 RequestDisallowedByPolicy
- **세부 정보**: Cloud Shell에서 저장소 계정을 만들 경우 관리자가 배치한 Azure 정책 때문에 실패합니다. 오류 메시지에는 다음이 포함됩니다. `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **해결 방법**: Azure 관리자에게 문의하여 저장소 생성을 거부하는 Azure 정책을 제거하거나 업데이트하세요.

### <a name="storage-dialog---error-400-disallowedoperation"></a>저장소 대화 상자 - 오류: 400 DisallowedOperation
 - **세부 정보**: Azure Active Directory 구독을 사용할 때는 저장소를 만들 수 없습니다.
 - **해결 방법**: 저장소 리소스를 만들 수 있는 Azure 구독을 사용하세요. Azure AD 구독으로는 Azure 리소스를 만들 수 없습니다.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>터미널 출력 - 오류: 터미널에 연결할 수 없습니다. websocket을 설정할 수 없습니다. `Enter` 키를 눌러 다시 연결하세요.
 - **세부 정보**: Cloud Shell에는 Cloud Shell 인프라에 대한 websocket 연결을 설정하기 위한 기능이 필요합니다.
 - **해결 방법**: *.console.azure.com의 도메인으로 https 요청 및 websocket 요청을 전송할 수 있게 네트워크 설정을 구성했는지 확인합니다.

## <a name="bash-troubleshooting"></a>Bash 문제 해결

### <a name="cannot-run-az-login"></a>az 로그인을 실행할 수 없음

- **세부 정보**: Cloud Shell 또는 Azure Portal에 로그인하는 데 사용된 계정으로 이미 인증되어 `az login` 실행이 작동하지 않습니다.
- **해결 방법**: 로그인 또는 로그아웃에 사용되는 계정을 활용하고 원하는 Azure 계정으로 다시 인증합니다.

### <a name="cannot-run-the-docker-daemon"></a>Docker 디먼을 실행할 수 없음

- **세부 정보**: Cloud Shell은 컨테이너를 활용하여 셸 환경을 호스트하므로 결과적으로 디먼 실행이 허용되지 않습니다.
- **해결 방법**: 기본적으로 설치된 [docker-machine](https://docs.docker.com/machine/overview/)을 활용하여 원격 Docker 호스트에서 docker 컨테이너를 관리합니다.

## <a name="powershell-troubleshooting"></a>PowerShell 문제 해결

### <a name="no-home-directory-persistence"></a>$Home 디렉터리 지속성 없음

- **세부 정보**: 응용 프로그램(예: git, vim 및 기타)이 `$Home`에 쓰는 모든 데이터가 PowerShell 세션 간에 유지되지 않습니다.
- **해결 방법**: PowerShell 프로필에서 `clouddrive`에 있는 응용 프로그램 특정 폴더인 $Home에 대한 바로 가기 링크를 만듭니다.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C는 Cmdlet 프롬프트를 종료하지 않습니다.

- **세부 정보**: Cmdlet 프롬프트를 종료하고자 할 때 `Ctrl+C`은 프롬프트를 종료하지 않습니다.
- **해결 방법**: 프롬프트를 종료하려면 `Ctrl+C` 다음 `Enter`를 누릅니다.

### <a name="gui-applications-are-not-supported"></a>GUI 응용 프로그램은 지원되지 않습니다.

- **세부 정보**: 사용자가 GUI 앱을 시작하는 경우 프롬프트는 반환하지 않습니다. 예를 들어 사용자가 2단계 인증을 사용할 수 있는 개인 GitHub 리포지토리를 복제할 경우 2단계 인증을 완료하기 위한 대화 상자가 표시됩니다.  
- **해결 방법**: 셸을 닫고 다시 엽니다.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-help-온라인은 도움말 페이지를 열지 않습니다.

- **세부 정보**: 사용자가 `Get-Help Find-Module -online`을 입력하는 경우 `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`와 같은 오류 메시지를 볼 수 있습니다.
- **해결 방법**: url을 복사하여 브라우저에서 수동으로 엽니다.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM의 원격 관리 문제 해결

- **세부 정보**: WinRM에 대한 기본 Windows 방화벽 설정으로 인해 사용자가 다음 오류를 보게 될 수도 있습니다.`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **해결 방법**: VM이 실행되고 있는지 확인합니다. VM 상태를 알아보려면 `Get-AzureRmVM -Status`을 실행할 수 있습니다.  다음으로 모든 서브넷에서 WinRM 연결을 허용하도록 원격 VM에 새 방화벽 규칙을 추가합니다. 예를 들어

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 [Azure 사용자 지정 스크립트 확장](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)을 사용하면 새 방화벽 규칙을 추가하기 위해 원격 VM에 로그온하는 것을 피할 수 있습니다.
 위의 스크립트를 예를 들어 `addfirerule.ps1`이란 파일에 저장하고, Azure Storage 컨테이너로 업로드할 수 있습니다.
 그런 후에 다음 명령을 시돱니다.

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir`은 결과를 Azure 드라이브로 캐시합니다.

- **세부 정보**: `dir`의 결과가 Azure 드라이브로 캐시됩니다.
- **해결**: Azure 드라이브 보기에서 리소스를 만들거나 제거한 후 `dir -force`를 실행하여 업데이트합니다.

## <a name="general-limitations"></a>일반적인 제한 사항
Azure Cloud Shell에는 다음과 같이 알려진 제한 사항이 있습니다.

### <a name="system-state-and-persistence"></a>시스템 상태 및 지속성

Cloud Shell 세션을 제공하는 컴퓨터는 일시적이며 세션이 20분 동안 비활성화된 후 재순환됩니다. Cloud Shell은 Azure 파일 공유를 탑재해야 합니다. 따라서 Cloud Shell에 액세스하도록 구독에서 저장소 리소스를 설정할 수 있어야 합니다. 기타 고려 사항은 다음과 같습니다.

* 탑재된 저장소에서 `clouddrive` 디렉터리 내 수정 사항만 유지됩니다. Bash에서 `$Home` 디렉터리도 유지됩니다.
* Azure 파일 공유는 [할당된 지역](persisting-shell-storage.md#mount-a-new-clouddrive) 내에서만 탑재될 수 있습니다.
  * Bash에서 `ACC_LOCATION`로 설정된 해당 지역을 찾으려면 `env`을 실행합니다.
* Azure 파일은 로컬 중복 저장소 및 지역 중복 저장소 계정만 지원합니다.

### <a name="browser-support"></a>브라우저 지원

Cloud Shell은 Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox 및 Apple Safari의 최신 버전을 지원합니다. Safari는 개인 모드에서 지원되지 않습니다.

### <a name="copy-and-paste"></a>복사 및 붙여넣기

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>지정된 사용자에 대해 셸이 하나만 활성화될 수 있습니다.

사용자는 **Bash** 또는 **PowerShell**을 이용하여 한 번에 한 가지 유형의 셸만을 시작할 수 있습니다. 그러나 PowerShell 또는 Bash의 인스턴스는 동시에 여러 개 실행할 수 있습니다. PowerShell 또는 Bash 간에 교환이 일어나면 Azure Cloud Shell이 다시 시작되어 기존 세션이 종료됩니다.

### <a name="usage-limits"></a>사용 제한

Cloud Shell은 대화형 사용 사례를 위한 것입니다. 따라서 비대화형 세션을 오래 실행하면 경고 없이 종료됩니다.

## <a name="bash-limitations"></a>Bash 제한 사항

### <a name="user-permissions"></a>사용자 권한

권한은 sudo 액세스 권한이 없는 일반 사용자로 설정됩니다. 사용자 `$Home` 디렉터리 외부에서의 설치는 유지되지 않습니다.

### <a name="editing-bashrc"></a>.bashrc 편집

.bashrc를 편집할 때는 Cloud Shell에 예기치 않은 오류가 발생할 수 있으니 주의하세요.

## <a name="powershell-limitations"></a>PowerShell 제한 사항

### <a name="slow-startup-time"></a>느린 시작 시간

Azure Cloud Shell의 PowerShell(미리 보기)은 미리 보기 중에 초기화하는 데 최대 60초가 걸릴 수 있습니다.

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure 드라이브에서 만들 때 기본 파일 위치:

PowerShell cmdlet을 사용하여 사용자가 Azure 드라이브 아래에 파일을 만들 수 없습니다. 사용자가 vim 또는 nano 등의 다른 도구를 사용하여 새 파일을 만들 때 파일은 기본적으로 C:\Users 폴더에 저장됩니다. 

### <a name="gui-applications-are-not-supported"></a>GUI 응용 프로그램은 지원되지 않습니다.

사용자가 Windows 대화 상자를 만드는 명령을 실행할 경우(예: `Connect-AzureAD` 또는 `Connect-AzureRmAccount`) `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` 같은 오류 메시지가 표시됩니다.

## <a name="gdpr-compliance-for-cloud-shell"></a>Cloud Shell에 대한 GDPR 규정 준수

Azure Cloud Shell은 개인 데이터를 중대하게 사용하며, Azure Cloud Shell 서비스에 의해 캡처되고 저장되는 데이터는 가장 최근에 사용된 셸, 기본 설정된 글꼴 크기, 기본 설정된 글꼴 유형 및 clouddrive로 백업하는 파일 공유 세부 정보와 같은 환경에 대한 기본값을 제공하는 데 사용됩니다. 이 데이터를 내보내거나 삭제하려는 경우 다음 지침을 포함합니다.

### <a name="export"></a>내보내기
사용자 설정을 **내보내기** 위해 Cloud Shell은 다음 명령을 실행하여 기본 설정된 셸, 글꼴 크기 및 글꼴 종류 등을 저장합니다.

1. Cloud Shell의 Bash 시작
2. 다음 명령을 실행합니다.
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>삭제
사용자 설정을 **삭제하기** 위해 Cloud Shell은 다음 명령을 실행하여 기본 설정된 셸, 글꼴 크기 및 글꼴 종류 등을 저장합니다. 다음 번에 Cloud Shell을 시작하면 파일 공유를 다시 등록할지 묻는 메시지가 표시됩니다. 

실제 Azure Files 공유는 사용자 설정을 삭제하는 경우 삭제되지 않으며, 해당 작업을 완료하려면 Azure Files로 이동합니다.

1. Cloud Shell의 Bash 시작
2. 다음 명령을 실행합니다.
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
