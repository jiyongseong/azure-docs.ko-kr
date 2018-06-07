---
title: Azure 가상 머신 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신용 양방향 직렬 콘솔입니다.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: e891e9c9fd87f370f0c98639ff0c6fc5b8cc81af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>가상 머신 직렬 콘솔(미리 보기) 


Azure의 가상 머신 직렬 콘솔은 Linux 및 Windows 가상 머신용 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 직렬 연결은 가상 머신의 COM1 직렬 포트에 연결되며, 가상 머신에 대한 액세스를 제공하고 가상 머신의 네트워크/운영 체제 상태와 관련이 없습니다. 가상 머신의 직렬 콘솔에 대한 액세스는 현재 Azure Portal을 통해서만 가능하며 가상 머신에 대해 VM 참가자 이상의 액세스 권한이 있는 사용자에게만 허용됩니다. 

> [!Note] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관]을 참조하세요. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 현재 이 서비스는 **공개 미리 보기** 상태이며 가상 머신의 직렬 콘솔에 대한 액세스는 글로벌 Azure 지역에서 사용할 수 있습니다.) 현재 Azure Government, Azure 독일 및 Azure 중국 클라우드에서는 직렬 콘솔을 사용할 수 없습니다.

 

## <a name="prerequisites"></a>필수 조건 

* 가상 머신에 [부트 진단](boot-diagnostics.md)을 사용하도록 설정되어 있어야 합니다. 
* 직렬 콘솔을 사용하는 계정에는 VM에 대한 [참가자 역할](../../role-based-access-control/built-in-roles.md)과 [부트 진단](boot-diagnostics.md) 저장소 계정이 있어야 합니다. 

## <a name="open-the-serial-console"></a>직렬 콘솔 열기
가상 머신의 직렬 콘솔은 [Azure Portal](https://portal.azure.com)을 통해서만 액세스할 수 있습니다. 다음은 포털을 통해 가상 머신의 직렬 콘솔에 액세스하는 단계입니다. 

  1. Azure 포털 열기
  2. 왼쪽 메뉴에서 가상 머신을 선택합니다.
  3. 목록에서 VM을 클릭합니다. VM에 대한 개요 페이지가 열립니다.
  4. 지원 및 문제 해결 섹션까지 아래로 스크롤하여 직렬 콘솔(미리 보기) 옵션을 클릭합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>기능 사용 중지
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔 기능을 비활성화할 수 있습니다.

## <a name="serial-console-security"></a>직렬 콘솔 보안 

### <a name="access-security"></a>액세스 보안 
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [VM 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스가 있는 사용자로 제한됩니다. AAD 테넌트에 Multi-Factor Authentication이 필요한 경우에는 직렬 콘솔에 대한 액세스 권한에도 MFA가 필요합니다. 해당 액세스가 [Azure Portal](https://portal.azure.com)을 통해 진행되기 때문입니다.

### <a name="channel-security"></a>채널 보안
주고 받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.  

>[!CAUTION] 
콘솔에 대한 액세스 암호가 기록되지 않은 상태에서 콘솔 내에서 실행되는 명령에 암호, 비밀, 사용자 이름 또는 기타 다른 형태의 PII(개인 식별 정보)가 포함되거나 출력되면, 표시되는 다른 모든 텍스트와 함께 해당 정보가 가상 머신 부트 진단 로그에 기록됩니다. 이것은 직렬 콘솔에 구현된 스크롤백 기능의 일부입니다. 이러한 로그는 순환되며 진단 저장소 계정에 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 하지만, 비밀 및/또는 PII가 포함될 수 있는 모든 작업에 대해서는 원격 데스크톱을 사용하는 것이 가장 좋습니다. 

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우에는, 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 연결됩니다. 이것은 첫 번째 사용자가 자리에서 일어나서 물리적 콘솔을 떠나고 새로운 사용자가 자리에 앉는 것과 유사한 방식입니다.

>[!CAUTION] 
즉, 연결이 끊긴 사용자는 로그아웃되지 않습니다. 연결 해제 시 로그아웃을 강제 적용하는 기능은(SIGHUP 또는 유사한 메커니즘을 통해) 아직 계획 중입니다. Windows의 경우 SAC에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 


## <a name="accessing-serial-console-for-windows"></a>Windows의 직렬 콘솔에 액세스 
Azure의 최신 Windows Server 이미지에는 기본적으로 SAC([Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx))가 사용되도록 설정되어 있습니다. SAC는 서버 버전의 Windows에서 지원되지만, 클라이언트 버전(예: Windows 10, Windows 8 또는 Windows 7)에서는 사용할 수 없습니다. Feb2018 이하의 이미지를 사용하여 만든 Windows 가상 머신용 직렬 콘솔을 사용하도록 설정하려면 다음 단계를 사용하세요. 

1. 원격 데스크톱을 통해 Windows 가상 머신에 연결
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 시스템을 다시 부팅하여 SAC 콘솔을 사용하도록 설정

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

필요한 경우 오프라인에서도 SAC를 사용하도록 설정할 수 있습니다. 

1. SAC를 데이터 디스크용으로 구성하려는 Windows 디스크를 기존 VM에 연결 
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>SAC이 사용되도록 설정되어 있는지를 확인하는 방법 

[SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)이 사용되도록 설정되어 있지 않으면 직렬 콘솔에 SAC 프롬프트가 표시되지 않습니다. 경우에 따라 VM 상태 정보가 표시될 수도 있고 비어 있을 수도 있습니다.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>부팅 메뉴를 사용하도록 설정하여 직렬 콘솔에 표시 

Windows 부팅 로더 프롬프트를 사용하도록 설정하여 직렬 콘솔에 표시해야 할 경우 Windows 부팅 로더에 대해 다음과 같은 추가 옵션을 추가할 수 있습니다.

1. 원격 데스크톱을 통해 Windows 가상 머신에 연결
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 시스템을 다시 부팅하여 부팅 메뉴를 사용하도록 설정

> [!NOTE] 
> 지금은 함수 키에 대한 지원이 사용되도록 설정되어 있지 않습니다. 고급 부팅 옵션이 필요한 경우 bcdedit /set {current} onetimeadvancedoptions on을 사용하세요. 자세한 내용은 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)을 참조하세요.

## <a name="windows-commands---cmd"></a>Windows 명령-CMD 

이 섹션에는 RDP 연결 오류 문제를 해결해야 할 때와 같이 VM에 액세스하는 데 SAC를 사용할 수 있는 시나리오에서 일반적인 작업 수행에 대한 명령의 예를 포함합니다.

SAC는 Windows Server 2003만 기본적으로 사용할 수 없게 된 이래 모든 버전의 Windows에 포함되었습니다. SAC는 `sacdrv.sys` 커널 드라이버, `Special Administration Console Helper` 서비스(`sacsvr`) 및 `sacsess.exe` 프로세스에 의존합니다. 자세한 내용은 [응급 관리 서비스 도구 및 설정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))을 참조합니다.

SAC는 직렬 포트를 통해 실행 중인 OS에 연결할 수 있습니다. SAC에서 CMD를 시작할 때 `sacsess.exe`은 실행 중인 OS 내에서 `cmd.exe`를 시작합니다. 작업 관리자에서 RDP가 VM에 연결된 경우 동시에 직렬 콘솔 기능을 통해 SAC에 연결돼 있음을 확인할 수 있습니다. RDP를 통해 연결된 경우 SAC를 통해 액세스하는 CMD는 사용하는 동일한 `cmd.exe`입니다. 해당 CMD 인스턴스에서 PowerShell을 시작하는 기능을 포함하여 모두 동일한 명령 및 도구를 사용할 수 있습니다. SAC와 WinRE(Windows 복구 환경) 사이의 주요 차이점은 WinRE가 다른 Minimal OS로 부팅하는 경우 SAC가 실행 중인 OS를 관리할 수 있게 한다는 것입니다. Azure VM은 직렬 콘솔 기능을 사용하여 WinRE에 액세스하는 기능을 지원하지 않지만 SAC를 통해 Azure VM을 관리할 수 있습니다.

SAC는 스크롤 백 기능이 없는 80x24 화면 버퍼로 제한되므로 명령에 `| more`을 추가해 한 번에 한 페이지씩 출력을 표시합니다. `<spacebar>`를 사용하여 다음 페이지를 보거나 `<enter>`를 사용하여 다음 줄을 봅니다.  

`SHIFT+INSERT`은 직렬 콘솔 창에 대한 바로 가기 붙여넣기입니다.

SAC의 제한된 화면 버퍼 때문에 더 긴 명령은 로컬 텍스트 편집기에 입력한 다음, SAC에 붙여넣기가 더 쉬울 수 있습니다.

### <a name="view-and-edit-windows-registry-settings"></a>Windows 레지스트리 설정 보기 및 편집
#### <a name="verify-rdp-is-enabled"></a>RDP를 사용하도록 설정했는지 확인
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

관련 그룹 정책 설정이 구성되는 경우 두 번째 키(under\Policies)만 존재하게 됩니다.

#### <a name="enable-rdp"></a>RDP를 사용하도록 설정
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

관련 그룹 정책 설정이 구성된 경우 두 번째 키(under\Policies)만 필요하게 됩니다. 값이 그룹 정책에서 구성된 경우 다음 그룹 정책 새로 고침에서 다시 쓰여집니다.

### <a name="manage-windows-services"></a>Windows 서비스 관리

#### <a name="view-service-state"></a>서비스 상태 보기
`sc query termservice`
####  <a name="view-service-logon-account"></a>서비스 로그온 계정 보기
`sc qc termservice`
#### <a name="set-service-logon-account"></a>서비스 로그온 계정 설정 
`sc config termservice obj= "NT Authority\NetworkService"`

등호 기호 뒤에 공백이 필요합니다.
#### <a name="set-service-start-type"></a>서비스 시작 유형 설정
`sc config termservice start= demand` 

등호 기호 뒤에 공백이 필요합니다. 가능한 시작 값은 `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`을 포함합니다.
#### <a name="set-service-dependencies"></a>서비스 종속성 설정
`sc config termservice depend= RPCSS`

등호 기호 뒤에 공백이 필요합니다.
#### <a name="start-service"></a>서비스 시작
`net start termservice`

또는

`sc start termservice`
#### <a name="stop-service"></a>서비스 중지
`net stop termservice`

또는

`sc stop termservice`
### <a name="manage-networking-features"></a>네트워킹 기능 관리
#### <a name="show-nic-properties"></a>NIC 속성 표시
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>IP 속성 표시
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>IPSec 구성 표시
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>NIC 사용
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>NIC가 DHCP를 사용하도록 설정
`netsh interface ip set address name="<interface name>" source=dhcp`

Azure VM은 IP 주소를 얻는 데 DHCP를 사용하려면 항상 게스트 운영 체제에서 구성돼야 합니다. Azure 고정 IP 설정은 여전히 DHCP를 사용하여 고정 IP를 VM에 제공합니다.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>포트 ping  
텔넷 클라이언트 설치

`dism /online /Enable-Feature /FeatureName:TelnetClient`

연결 테스트

`telnet bing.com 80`

텔넷 클라이언트를 제거하려면

`dism /online /Disable-Feature /FeatureName:TelnetClient`

기본적으로 Windows에서 사용할 수 있는 방법으로 제한된 경우 PowerShell은 포트 연결을 테스트하기 위한 더 좋은 방법이 될 수 있습니다. 예제는 아래 PowerShell 섹션을 참조하십시오.
#### <a name="test-dns-name-resolution"></a>DNS 이름 확인 테스트
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Windows 방화벽 규칙 표시
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Windows 방화벽 사용 안 함
`netsh advfirewall set allprofiles state off`

Windows 방화벽을 일시적으로 제외하는 문제를 해결하려면 이 명령을 사용할 수 있습니다. 다음에 다시 시작할 때 또는 아래 명령을 통해 사용할 때 사용할 수 있게 됩니다. Windows 방화벽을 제외하기 위한 방법으로 Windows 방화벽 서비스(MPSSVC) 또는 기본 필터링 엔진(BFE) 서비스를 중지하지 마십시오. MPSSVC 또는 BFE 중지하면 모든 연결이 차단되게 됩니다.
#### <a name="enable-windows-firewall"></a>Windows 방화벽 사용
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>사용자 및 그룹 관리
#### <a name="create-local-user-account"></a>로컬 사용자 계정 만들기
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>로컬 그룹에 로컬 사용자 추가
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>사용자 계정이 사용하도록 설정되어 있는지 확인
`net user <username> | find /i "active"`

일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
#### <a name="enable-user-account"></a>사용자 계정 사용
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>사용자 계정 속성 보기
`net user <username>`

로컬 관리자 계정에서 관심있는 예제 줄:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>로컬 그룹 보기
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Windows 이벤트 로그 관리
#### <a name="query-event-log-errors"></a>이벤트 로그 오류 쿼리
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

`/c:10`을 반환하기 원하는 이벤트 수로 변경하거나 이를 이동하여 필터와 일치하는 모든 이벤트를 반환합니다.
#### <a name="query-event-log-by-event-id"></a>이벤트 ID로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>이벤트 ID와 공급자로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>지난 24시간 동안 이벤트 ID와 공급자로 이벤트 로그 쿼리
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000`을 사용하여 24시간 대신 7일을 되돌아봅니다.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>지난 7 일 이내 이벤트 ID, 공급자 및 EventData로 이벤트 로그 쿼리
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>설치된 응용 프로그램 보기 또는 제거
#### <a name="list-installed-applications"></a>설치된 응용 프로그램 나열
`wmic product get Name,InstallDate | sort /r | more`

`sort /r`은 설치 날짜를 기준으로 내림차순으로 정렬해 최근에 설치된 것을 쉽게 알 수 있습니다. `<spacebar>`를 사용하여 출력의 다음 페이지로 이동하거나 `<enter>`를 사용하여 한 줄씩 이동합니다.
#### <a name="uninstall-an-application"></a>응용 프로그램 제거
`wmic path win32_product where name="<name>" call uninstall`

제거하려는 응용 프로그램에 대해 위의 명령에서 반환된 이름으로 `<name>`을 대체합니다.

### <a name="file-system-management"></a>파일 시스템 관리
#### <a name="get-file-version"></a>파일 버전 가져오기
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

이 예제에서는 Windows 버전에 따라 netvsc.sys, netvsc63.sys 또는 netvsc60.sys인 가상 NIC 드라이버의 파일 버전을 반환합니다.
#### <a name="scan-for-system-file-corruption"></a>시스템 파일 손상에 대한 검사
`sfc /scannow`

[Windows 이미지 복구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)도 참조합니다.
#### <a name="scan-for-system-file-corruption"></a>시스템 파일 손상에 대한 검사
`dism /online /cleanup-image /scanhealth`

[Windows 이미지 복구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)도 참조합니다.
#### <a name="export-file-permissions-to-text-file"></a>텍스트 파일에 파일 사용 권한 내보내기
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>ACL 파일에 파일 사용 권한 저장하기
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>ACL 파일에서 파일 사용 권한 복원하기
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

`/restore`을 사용할 때의 경로는 `/save`를 사용할 때 지정한 폴더의 상위 폴더여야 합니다. 이 예제에서 `\RSA`은 위의 예제 `/save`에서 지정한 `\MachineKeys` 폴더의 상위 폴더입니다.
#### <a name="take-ntfs-ownership-of-a-folder"></a>폴더의 NTFS 소유권 획득
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>재귀적으로 폴더에 NTFS 소유권 부여하기
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>장치 관리
#### <a name="remove-non-present-pnp-devices"></a>없는 PNP 장치 제거
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>그룹 정책 관리
#### <a name="force-group-policy-update"></a>그룹 정책 업데이트 강제
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>기타 작업
#### <a name="show-os-version"></a>OS 버전 표시
`ver`

또는 

`wmic os get caption,version,buildnumber /format:list`

또는 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>OS 설치 날짜 보기
`systeminfo | find /i "original"`

또는 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>마지막 부팅 시간 보기
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>표준 시간대 보기
`systeminfo | find /i "time zone"`

또는

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Windows 다시 시작
`shutdown /r /t 0`

`/f`을 추가하면 사용자에게 경고 없이 실행 중인 응용 프로그램을 강제로 닫습니다.
#### <a name="detect-safe-mode-boot"></a>안전 모드 부팅 검색
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows 명령 - PowerShell

SAC에서 PowerShell을 실행하려면 CMD 프롬프트가 표시된 후 다음을 입력합니다.

`powershell <enter>`

>[!CAUTION]
다른 모든 PowerShell 명령을 실행하기 전에 PowerShell 세션에서 PSReadLine 모듈을 제거합니다. PSReadLine이 SAC의 PowerShell 세션에서 실행 중인 경우 클립보드에서 붙여넣은 텍스트에 추가 문자가 도입될 수 있는 알려진 문제가 있습니다.

먼저 PSReadLine이 로드되었는지 확인합니다. 기본적으로 Windows Server 2016, Windows 10 및 이후 버전의 Windows에서 로드됩니다. 수동으로 설치한 경우 이전 Windows 버전에만 존재합니다. 

이 명령이 출력 없이 프롬프트로 반환되는 경우 모듈이 로드되지 않아 정상적으로 SAC에서 PowerShell 세션을 계속 사용할 수 있습니다.

`get-module psreadline`

위의 명령이 PSReadLine 모듈 버전을 반환하는 경우 해당 버전을 언로드하려면 다음 명령을 실행합니다. 이 명령은 모듈을 삭제하거나 제거하지 않고 현재 PowerShell 세션에서 해당 모듈을 언로드만 합니다.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Windows 레지스트리 설정 보기 및 편집
#### <a name="verify-rdp-is-enabled"></a>RDP를 사용하도록 설정했는지 확인
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

관련 그룹 정책 설정이 구성되는 경우 두 번째 키(under\Policies)만 존재하게 됩니다.
#### <a name="enable-rdp"></a>RDP를 사용하도록 설정
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

관련 그룹 정책 설정이 구성된 경우 두 번째 키(under\Policies)만 필요하게 됩니다. 값이 그룹 정책에서 구성된 경우 다음 그룹 정책 새로 고침에서 다시 쓰여집니다.
### <a name="manage-windows-services"></a>Windows 서비스 관리
#### <a name="view-service-details"></a>서비스 정보 보기
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`은 사용할 수 있지만 서비스 로그온 계정을 포함하지는 않습니다. `Get-WmiObject win32-service`은 포함합니다.
#### <a name="set-service-logon-account"></a>서비스 로그온 계정 설정
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

`NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` 또는 `LocalSystem` 이외의 서비스 계정을 사용하는 경우 계정 이름 뒤의 마지막(8) 인수로 계정 암호를 지정합니다.
#### <a name="set-service-startup-type"></a>서비스 시작 유형 설정
`set-service termservice -startuptype Manual`

`Set-service`은 시작 유형으로 `Automatic`, `Manual` 또는 `Disabled`를 허용합니다.
#### <a name="set-service-dependencies"></a>서비스 종속성 설정
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>서비스 시작
`start-service termservice`
#### <a name="stop-service"></a>서비스 중지
`stop-service termservice`
### <a name="manage-networking-features"></a>네트워킹 기능 관리
#### <a name="show-nic-properties"></a>NIC 속성 표시
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

또는 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있으며, 2008R2에 대해서는 `Get-WmiObject`를 사용합니다.
#### <a name="show-ip-properties"></a>IP 속성 표시
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>NIC 사용
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

또는

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있으며, 2008R2에 대해서는 `Get-WmiObject`를 사용합니다.
#### <a name="set-nic-to-use-dhcp"></a>NIC가 DHCP를 사용하도록 설정
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. Azure VM은 IP 주소를 얻는 데 DHCP를 사용하려면 항상 게스트 운영 체제에서 구성돼야 합니다. Azure 고정 IP 설정은 여전히 DHCP를 사용하여 IP를 VM에 제공합니다.
#### <a name="ping"></a>Ping
`test-netconnection`

또는

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

매개 변수가 없는 `Test-Netconnection`은 `internetbeacon.msedge.net`를 ping하려 합니다. 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 두 번째 예제와 같이 `Get-WmiObject`을 사용합니다.
#### <a name="port-ping"></a>포트 Ping
`test-netconnection -ComputerName bing.com -Port 80`

또는

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Net.Sockets.TcpClient` 사용
#### <a name="test-dns-name-resolution"></a>DNS 이름 확인 테스트
`resolve-dnsname bing.com` 

또는 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `System.Net.DNS`을 사용합니다.
#### <a name="show-windows-firewall-rule-by-name"></a>이름으로 Windows 방화벽 규칙 표시
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>포트로 Windows 방화벽 규칙 표시
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

또는

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `hnetcfg.fwpolicy2` COM 개체를 사용합니다. 
#### <a name="disable-windows-firewall"></a>Windows 방화벽 사용 안 함
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 위의 CMD 섹션에서 참조한 대로 `netsh advfirewall`을 사용합니다.
### <a name="manage-users-and-groups"></a>사용자 및 그룹 관리
#### <a name="create-local-user-account"></a>로컬 사용자 계정 만들기
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>사용자 계정이 사용하도록 설정되어 있는지 확인
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

또는 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. 이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 보여줍니다. 일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
#### <a name="add-local-user-to-local-group"></a>로컬 그룹에 로컬 사용자 추가
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>로컬 사용자 계정 사용
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 사용할 수 있습니다. 일반화된 이미지에서 만든 Azure VM에는 VM 프로비전 중에 지정한 이름으로 바꾼 로컬 관리자 계정이 있습니다. 따라서 일반적으로 `Administrator`이 되지 않습니다.
#### <a name="view-user-account-properties"></a>사용자 계정 속성 보기
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

또는 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다. 이 예제에서는 항상 SID `S-1-5-21-*-500`이 있는 기본 제공 로컬 관리자 계정을 보여줍니다.
#### <a name="view-local-groups"></a>로컬 그룹 보기
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`은 2012 이상에서 사용할 수 있습니다. 2008R2에 대해서는 `Get-WmiObject`을 사용합니다.
### <a name="manage-the-windows-event-log"></a>Windows 이벤트 로그 관리
#### <a name="query-event-log-errors"></a>이벤트 로그 오류 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

`/c:10`을 반환하기 원하는 이벤트 수로 변경하거나 이를 이동하여 필터와 일치하는 모든 이벤트를 반환합니다.
#### <a name="query-event-log-by-event-id"></a>이벤트 ID로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>이벤트 ID와 공급자로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>지난 24시간 동안 이벤트 ID와 공급자로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000`을 사용하여 24시간 대신 7일을 되돌아봅니다. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>지난 7 일 이내 이벤트 ID, 공급자 및 EventData로 이벤트 로그 쿼리
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>설치된 응용 프로그램 보기 또는 제거
#### <a name="list-installed-software"></a>설치된 소프트웨어 나열하기
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>소프트웨어 제거
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>파일 시스템 관리
#### <a name="get-file-version"></a>파일 버전 가져오기
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

이 예제에서는 Windows 버전에 따라 netvsc.sys, netvsc63.sys 또는 netvsc60.sys로 명명된 가상 NIC 드라이버의 파일 버전을 반환합니다.
#### <a name="download-and-extract-file"></a>파일 다운로드 및 추출
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

이 예제에서는 `c:\bin` 폴더를 만든 다음, Sysinternals 도구 모음을 `c:\bin`로 다운로드하고 추출합니다.
### <a name="miscellaneous-tasks"></a>기타 작업
#### <a name="show-os-version"></a>OS 버전 표시
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>OS 설치 날짜 보기
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>마지막 부팅 시간 보기
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Windows 작동 시간 보기
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

`49:16:48:00.00`의 예를 들어 작동 시간을 `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`로 반환합니다. 
#### <a name="restart-windows"></a>Windows 다시 시작
`restart-computer`

`-force`을 추가하면 사용자에게 경고 없이 실행 중인 응용 프로그램을 강제로 닫습니다.
### <a name="instance-metadata"></a>인스턴스 메타데이터

osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress 및 publicIpAddress 같은 세부 정보를 보려면 Azure VM 내에서 Azure 인스턴스 메타데이터를 쿼리할 수 있습니다.

인스턴스 메타데이터를 쿼리하려면 Azure 호스트를 통해 인스턴스 메타데이터 서비스로 REST 호출하기 때문에 정상 게스트 네트워크 연결이 필요합니다. 따라서 인스턴스 메타데이터를 쿼리할 수 있으면 게스트는 네트워크를 통해 Azure 호스팅 서비스와 통신할 수 있습니다.

자세한 내용은 [Azure Instance Metadata 서비스](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)를 참조하세요.

#### <a name="instance-metadata"></a>인스턴스 메타데이터
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>OS 유형(인스턴스 메타데이터)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>위치(인스턴스 메타데이터)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>크기(인스턴스 메타데이터)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>VM ID(인스턴스 메타데이터)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>VM 이름(인스턴스 메타데이터)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>리소스 그룹 이름(인스턴스 메타데이터)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>구독 ID(인스턴스 메타데이터)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>태크(인스턴스 메타데이터)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>배치 그룹 ID(인스턴스 메타데이터)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>플랫폼 장애 도메인(인스턴스 메타데이터)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>플랫폼 업데이트 도메인(인스턴스 메타데이터)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 개인 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 공용 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 서브넷 주소 / 접두사(인스턴스 메타데이터)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP 주소(인스턴스 메타데이터)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC 주소(인스턴스 메타데이터)
`$im.network.interface.macAddress`

## <a name="errors"></a>오류
대부분의 오류는 본래 일시적이며 연결을 다시 시도하면 문제가 해결됩니다. 아래 표에는 오류 및 해결 방법 목록이 있습니다. 

오류                            |   해결 방법 
:---------------------------------|:--------------------------------------------|
'<VMNAME>'에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다. 
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 가상 머신이 시작된 상태여야 합니다.
직렬 콘솔 VM을 사용하는 데 필요한 권한이 없습니다. VM 참가자 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.
부트 진단 저장소 계정인 '<STORAGEACCOUNTNAME>'에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.

## <a name="known-issues"></a>알려진 문제 
직렬 콘솔 액세스는 아직 미리 보기 단계이며, 알려진 문제를 해결하기 위해 노력하고 있습니다. 아래에는 이러한 문제가 가능한 해결 방법이 나열되어 있습니다. 

문제                           |   해결 방법 
:---------------------------------|:--------------------------------------------|
가상 머신 확장 집합 인스턴스 직렬 콘솔에 옵션이 없습니다. | 미리 보기 단계에서는 가상 머신 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.
연결 배너가 표시된 후 Enter를 눌러도 로그인 프롬프트가 표시되지 않습니다. | [Enter를 누르면 아무 작업도 수행되지 않습니다.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Windows VM에 연결할 때 상태 정보만 표시됩니다.| [Windows 상태 신호](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
커널 디버깅을 사용할 수 있으면 SAC 프롬프트에 입력할 수 없음 | VM에 RDP하고 관리자 권한 명령 프롬프트에서 `bcdedit /debug {current} off`을 실행합니다. RDP할 수 없는 경우 대신 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`을 이용하여 데이터 디스크로 연결하는 동안 다른 Azure VM에 OS 디스크를 연결하고 수정한 다음, 다시 디스크를 교체합니다.

## <a name="frequently-asked-questions"></a>질문과 대답 
**Q. 피드백을 보내려면 어떻게 해야 하나요?**

a. 으로 이동하여 피드백을 제공해 주세요 https://aka.ms/serialconsolefeedback . 또는(대안으로) 을 통해서 또는 azserialhelp@microsoft.com의 가상 머신 범주에 피드백을 보내주세요 http://feedback.azure.com 

**Q. “기존 콘솔의 OS 유형 Windows가 요청된 Linux의 OS 유형과 충돌됩니다.”라는 오류가 발생합니다**.

a. 이것은 알려진 문제이며, 해결하려면 bash 모드에서 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 열고 다시 시도하세요.

**Q. 직렬 콘솔에 액세스할 수 없습니다. 지원 사례를 어디에서 제출할 수 있나요?**

a. 이 미리 보기 기능은 Azure 미리 보기 약관을 통해 제공됩니다. 이 기능에 대한 지원은 위에 언급된 채널을 통해 처리하는 것이 가장 좋습니다. 

## <a name="next-steps"></a>다음 단계
* [Linux](../linux/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기
