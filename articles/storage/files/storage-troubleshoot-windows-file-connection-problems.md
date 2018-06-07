---
title: Windows에서 Azure Files 문제 해결 | Microsoft Docs
description: Windows에서 Azure Files 문제 해결
services: storage
documentationcenter: ''
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 78170df99e9ced304bebd9112d79b5c7e402f218
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Windows에서 Azure Files 문제 해결

이 문서에서는 Windows 클라이언트에서 연결할 때 Microsoft Azure Files와 관련하여 발생하는 일반적인 문제를 보여 줍니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 제공합니다. 이 문서의 문제 해결 단계 외에도 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)를 사용하여 Windows 클라이언트 환경의 필수 구성 요소가 올바른지 확인할 수 있습니다. AzFileDiagnostics는 이 문서에서 설명하는 대부분의 현상을 자동으로 감지하고 최적의 성능을 얻도록 환경을 설정하는 데 도움이 됩니다. 이 정보는 Azure 파일 공유 연결/매핑/탑재 관련 문제에 도움이 되는 단계를 제공하는 [Azure 파일 공유 문제 해결사](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)에서도 찾을 수 있습니다.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Azure 파일 공유를 탑재 또는 탑재 해제하는 경우 오류 53, 오류 67 또는 오류 87 발생

온-프레미스 또는 다른 데이터 센터에서 파일 공유를 탑재하려고 할 때 다음과 같은 오류가 발생할 수 있습니다.

- 시스템 오류 53이 발생했습니다. 네트워크 경로를 찾을 수 없습니다.
- 시스템 오류 67이 발생했습니다. 네트워크 이름을 찾을 수 없습니다.
- 시스템 오류 87이 발생했습니다. 매개 변수가 올바르지 않습니다.

### <a name="cause-1-unencrypted-communication-channel"></a>원인 1: 암호화되지 않은 통신 채널

보안상 이유로, 통신 채널이 암호화되지 않고 Azure 파일 공유가 있는 같은 데이터 센터에서 연결 시도가 이루어지지 않을 경우 Azure 파일 공유에 대한 연결이 차단됩니다. 사용자의 클라이언트 OS가 SMB 암호화를 지원하는 경우에만 통신 채널 암호화가 제공됩니다.

각 시스템의 Windows 8, Windows Server 2012 이후 버전은 암호화를 지원하는 SMB 3.0이 포함된 요청을 협상합니다.

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법

다음 중 하나를 수행하는 클라이언트에서 연결합니다.

- Windows 8 및 Windows Server 2012 이후 버전의 요구 사항을 충족합니다.
- Azure 파일 공유에 사용되는 Azure Storage 계정과 동일한 데이터 센터의 가상 컴퓨터에서 연결합니다.

### <a name="cause-2-port-445-is-blocked"></a>원인 2: 포트 445 차단

시스템 오류 53 또는 시스템 오류 67은 Azure Files 데이터 센터에 대한 포트 445 아웃바운드 통신이 차단될 경우 발생할 수 있습니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)으로 이동합니다.

이 이유로 "시스템 오류 53" 메시지가 수신되었는지 이해하려면 Portqry를 사용하여 TCP:445 끝점을 쿼리할 수 있습니다. TCP:445 끝점이 필터링됨으로 표시될 경우 TCP 포트가 차단됩니다. 다음은 예제 쿼리입니다.

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

TCP 포트 445가 네트워크 경로를 따라 규칙을 통해 차단될 경우 다음 출력이 표시됩니다.

  `TCP port 445 (microsoft-ds service): FILTERED`

Portqry 사용 방법에 대한 자세한 내용은 [Portqry.exe 명령줄 유틸리티에 대한 설명](https://support.microsoft.com/help/310099)을 참조하세요.

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법

IT 부서와 협력하여 [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대한 포트 445 아웃바운드를 엽니다.

### <a name="cause-3-ntlmv1-is-enabled"></a>원인 3: NTLMv1 사용

클라이언트에서 NTLMv1 통신이 사용될 경우 시스템 오류 53 또는 시스템 오류 87이 발생할 수 있습니다. Azure Files는 NTLMv2 인증만 지원합니다. NTLMv1을 사용하도록 설정하면 클라이언트 보안이 약화됩니다. 따라서 Azure Files에 대한 통신이 차단됩니다. 

이것이 오류의 원인인지 확인하려면 다음 레지스트리 하위 키가 3의 값으로 설정되어 있는지 확인합니다.

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

자세한 내용은 TechNet에서 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 항목을 참조하세요.

### <a name="solution-for-cause-3"></a>원인 3의 해결 방법

**LmCompatibilityLevel** 값을 다음 레지스트리 하위 키의 기본값인 3으로 되돌립니다.

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Azure 파일 공유에 복사하는 경우 오류 1816 "이 명령을 처리할 수 있는 할당량이 충분하지 않습니다." 발생

### <a name="cause"></a>원인

파일 공유가 탑재되어 있는 컴퓨터의 파일에 허용되는 동시 오픈 핸들의 상한값에 도달하는 경우 오류 1816이 발생합니다.

### <a name="solution"></a>해결 방법

일부 핸들을 닫아 동시 열린 핸들 수를 줄이고 다시 시도하세요. 자세한 내용은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows에서 Azure Files와 서로 파일을 복사하는 속도 느림

Azure File 서비스에 파일을 전송하려고 하면 성능 저하가 발생할 수 있습니다.

- 최소 I/O 크기에 대한 특정 요구 사항이 없을 경우 최적 성능을 위해 I/O 크기로 1MB를 사용하는 것이 좋습니다.
-   쓰기를 사용하여 확장 중인 파일의 최종 크기를 알고 파일에 아직 기록되지 않은 꼬리에 0이 포함될 때 소프트웨어에 호환성 문제가 발생하지 않는다면 모든 쓰기를 확장 쓰기로 설정하는 대신 파일 크기를 미리 설정합니다.
-   copy 메서드를 다음과 같이 올바르게 사용합니다.
    -   두 파일 공유 간의 전송에는 [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 사용합니다.
    -   온-프레미스 컴퓨터와 파일 공유 간에는 [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)를 사용합니다.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 또는 Windows Server 2012 R2에 대한 고려 사항

Windows 8.1 또는 Windows Server 2012 R2를 실행 중인 클라이언트에서는 핫픽스 [KB3114025](https://support.microsoft.com/help/3114025)가 설치되어 있는지 확인합니다. 이 핫픽스는 핸들 만들기 및 닫기 성능을 개선합니다.

다음 스크립트를 실행하여 핫픽스가 설치되었는지 확인할 수 있습니다.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

핫픽스가 설치된 경우 다음 출력이 표시됩니다.

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 2015년 12월부터 Azure Marketplace의 Windows Server 2012 R2 이미지에는 핫픽스 KB3114025가 기본적으로 설치되어 있습니다.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>**내 컴퓨터**에 드라이브 문자를 포함한 폴더가 없습니다.

net use를 사용하여 관리자 권한으로 Azure 파일 공유를 매핑하는 경우 공유가 누락될 수 있습니다.

### <a name="cause"></a>원인

기본적으로 Windows File Explorer는 관리자 권한으로 실행되지 않습니다. 관리자 명령 프롬프트에서 net use를 실행할 경우 네트워크 드라이브를 관리자 권한으로 매핑합니다. 매핑된 드라이브는 사용자 중심이므로 다른 사용자 계정으로 탑재될 경우 로그인된 사용자 계정에 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법
비관리자 명령줄에서 공유를 탑재하세요. 또는 [이 TechNet 항목](https://technet.microsoft.com/library/ee844140.aspx)에 따라 **EnableLinkedConnections** 레지스트리 값을 구성할 수 있습니다.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>내 저장소 계정에 슬래시(/)가 포함되는 경우 net use 명령이 실패합니다.

### <a name="cause"></a>원인

net use 명령은 슬래시(/)를 명령줄 옵션으로 해석합니다. 사용자 계정 이름이 슬래시로 시작되면 드라이브 매핑에 실패합니다.

### <a name="solution"></a>해결 방법

다음 단계 중 하나를 사용하여 문제를 해결할 수 있습니다.

- 다음 PowerShell 명령을 실행합니다.

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  배치 파일에서 다음과 같은 방식으로 명령을 실행할 수 있습니다.

  `Echo new-smbMapping ... | powershell -command –`

- 슬래시(/)가 첫 번째 문자가 아닐 경우 키 주위에 큰따옴표를 배치하여 이 문제를 해결합니다. 슬래시(/)가 첫 번째 문자일 경우 대화형 모드를 사용하여 암호를 개별적으로 입력하거나 키를 다시 생성하여 슬래시(/) 문자로 시작되지 않는 키를 얻습니다.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>응용 프로그램 또는 서비스가 탑재된 Azure Files 드라이브에 액세스할 수 없습니다.

### <a name="cause"></a>원인

드라이브는 사용자별로 탑재됩니다. 응용 프로그램 또는 서비스가 드라이브를 탑재한 계정이 아닌 다른 사용자 계정으로 실행되는 경우 응용 프로그램에는 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법

다음 해결 방법 중 하나를 사용합니다.

-   응용 프로그램을 포함하는 동일한 사용자 계정에서 드라이브를 탑재합니다. PsExec와 같은 도구를 사용할 수 있습니다.
- net use 명령의 사용자 이름 및 암호 매개 변수에 저장소 계정 이름 및 키를 전달합니다.
- cmdkey 명령을 사용하여 자격 증명 관리자에 자격 증명을 추가합니다. 대화형 로그인을 통하거나 runas를 사용하여 서비스 계정 컨텍스트에 있는 명령줄에서 이를 수행합니다.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- 매핑된 드라이브 문자를 사용하지 않고 직접 공유를 매핑합니다. 일부 응용 프로그램은 드라이브 문자에 제대로 연결되지 않을 수 있으므로 전체 UNC 경로를 사용하는 것이 더 안정적일 수 있습니다. 

  `net use * \\storage-account-name.file.core.windows.net\share`

다음과 같은 지침을 따르고 시스템/네트워크 서비스 계정에 net use를 실행하면 다음과 같은 오류 메시지가 표시될 수 있습니다. "시스템 오류 1312가 발생했습니다. 지정된 로그온 세션이 없습니다. 이미 종료되었을 수 있습니다." 이 문제가 발생하면 net use에 전달되는 사용자 이름에 도메인 정보(예: "[저장소 계정 이름].file.core.windows.net")가 포함되는지 확인합니다.

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>“암호화를 지원하지 않는 대상에 파일을 복사하는 중임” 오류 발생

네트워크를 통해 파일이 복사되면 파일은 원본 컴퓨터에서 암호를 해독하고, 일반 텍스트로 전송되어 대상에서 다시 암호화됩니다. 하지만 암호화된 파일을 복사하려 하는 경우 다음 오류가 발생할 수 있습니다. "암호화를 지원하지 않는 대상에 파일을 복사하고 있습니다."

### <a name="cause"></a>원인
EFS(파일 시스템 암호화)를 사용하는 경우 이 문제가 발생할 수 있습니다. BitLocker로 암호화된 파일을 Azure Files로 복사할 수 있습니다. 하지만 Azure Files는 NTFS EFS를 지원하지 않습니다.

### <a name="workaround"></a>해결 방법
파일을 네트워크로 복사하려면 먼저 파일을 암호 해독해야 합니다. 다음 방법 중 하나를 사용합니다.

- **copy /d** 명령을 사용합니다. 암호화된 파일을 대상에서 암호 해독된 파일로 저장할 수 있습니다.
- 다음 레지스트리 키를 설정합니다.
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

레지스트리 키를 설정하면 네트워크 공유에 만들어진 모든 복사 작업에 적용됩니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
