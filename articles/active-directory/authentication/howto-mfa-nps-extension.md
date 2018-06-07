---
title: 기존 NPS 서버를 사용하여 Azure MFA 기능 제공 | Microsoft Docs
description: 기존 인증 인프라에 클라우드 기반 2단계 검증 기능 추가
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 05/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 57bf8b81d8d7fee6eaee216b9a2e0c52aa625257
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868333"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>기존 NPS 인프라를 Azure Multi-Factor Authentication과 통합

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용하면 새 서버를 설치, 구성 및 유지할 필요 없이 전화 통화, 문자 메시지 또는 휴대폰 앱 인증을 기존 인증 흐름에 추가할 수 있습니다. 

이 확장은 Azure MFA 서버를 배포하지 않고 VPN 연결을 보호하려는 조직을 위해 작성되었습니다. NPS 확장은 RADIUS 및 클라우드 기반 Azure MFA 간에 어댑터로 작동하여 페더레이션 사용자 또는 동기화된 사용자를 위한 또 다른 인증을 제공합니다.

Azure MFA용 NPS 확장을 사용하면 인증 흐름에 다음 구성 요소가 포함됩니다. 

1. **NAS/VPN 서버** - VPN 클라이언트로부터 요청을 받고, 이 요청을 NPS 서버에 대한 RADIUS 요청으로 변환합니다. 
2. **NPS 서버** - Active Directory에 연결하여 RADIUS 요청에 대한 기본 인증을 수행하고, 성공하면 요청을 설치된 모든 확장에 전달합니다.  
3. **NPS 확장** - 보조 인증을 위해 Azure MFA에 요청을 트리거합니다. 확장에서 응답을 받고 MFA 요청이 성공하면 Azure STS에서 발급한 MFA 클레임이 포함된 보안 토큰을 NPS 서버에 제공하여 인증 요청을 완료합니다.  
4. **Azure MFA** - Azure Active Directory와 통신하여 사용자의 세부 정보를 검색하고 구성된 인증 방법을 사용하여 사용자에게 보조 인증을 수행합니다.

다음 다이어그램에서는 이러한 높은 수준의 인증 요청 흐름을 보여 줍니다. 

![인증 흐름 다이어그램](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>배포 계획

NPS 확장은 자동으로 중복을 처리하므로 특별한 구성이 필요하지 않습니다.

Azure MFA가 사용되는 NPS 서버를 필요한 만큼 많이 만들 수 있습니다. 여러 서버를 설치한 경우 중 각각에 대해 다른 클라이언트 인증서를 사용해야 합니다. 각 서버에 대한 인증서를 만드는 것은 각 인증서를 개별적으로 업데이트할 수 있고 모든 서버 간의 가동 중지 시간을 걱정하지 않아도 된다는 의미입니다.

VPN 서버는 인증 요청을 라우팅하므로 새로운 Azure MFA 사용 NPS 서버에 유의해야 합니다.

## <a name="prerequisites"></a>필수 조건

NPS 확장은 기존 인프라와 함께 사용할 수 있습니다. 시작하기 전에 다음 필수 조건을 갖추고 있는지 확인합니다.

### <a name="licenses"></a>라이선스

Azure AD Premium, EMS 또는 MFA 독립 실행형 라이선스에 포함된 [Azure Multi-Factor Authentication 라이선스](multi-factor-authentication.md)를 가진 고객은 Azure MFA용 NPS 확장을 사용할 수 있습니다. 사용자 기준 또는 인증 기준 라이선스와 같은 Azure MFA에 대한 사용량 기반 라이선스는 NPS 확장과 호환되지 않습니다. 

### <a name="software"></a>소프트웨어

Windows Server 2008 R2 SP1 이상

### <a name="libraries"></a>라이브러리

이러한 라이브러리는 확장으로 자동 설치됩니다.

-   [Visual Studio 2013(X64)용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Windows PowerShell용 Microsoft Azure Active Directory 모듈 버전 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Windows PowerShell용 Microsoft Azure Active Directory 모듈은 아직 설치되지 않은 경우 설치 프로세스의 일부로 실행되는 구성 스크립트를 통해 설치됩니다. 이 모듈은 아직 설치하지 않은 상태에서 미리 설치할 필요가 없습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

NPS 확장을 사용하는 모든 사용자는 Azure AD Connect를 사용하여 Azure Active Directory와 동기화되어야 하며 MFA에 등록되어야 합니다.

확장을 설치할 때 Azure AD 테넌트에 대한 디렉터리 ID와 관리자 자격 증명이 필요합니다. [Azure Portal](https://portal.azure.com)에서 디렉터리 ID를 찾을 수 있습니다. 관리자로 로그인하고 왼쪽의 **Azure Active Directory** 아이콘을 선택한 다음 **속성**을 선택합니다. **디렉터리 ID** 상자에서 GUID를 복사하고 저장합니다. NPS 확장을 설치할 때 이 GUID를 테넌트 ID로 사용합니다.

![Azure Active Directory 속성에서 디렉터리 ID 찾기](./media/howto-mfa-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>환경 준비

NPS 확장을 설치하기 전에 인증 트래픽을 처리하는 환경을 준비하는 것이 좋습니다.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>도메인에 가입된 서버에서 NPS 역할 사용

NPS 서버는 Azure Active Directory에 연결하고 MFA 요청을 인증합니다. 이 역할에 대한 서버를 한 개 선택하세요. NPS 확장이 RADIUS가 아닌 모든 요청에 대해 오류를 throw하므로 다른 서비스의 요청을 처리하지 않는 서버를 선택하는 것이 좋습니다. NPS 서버를 사용자 환경에 대한 기본 및 보조 인증 서버로 설정해야 합니다. RADIUS 요청을 다른 서버로 프록시할 수 없습니다.

1. 서버의 서버 관리자 빠른 시작 메뉴에서 **역할 및 기능 추가 마법사**를 엽니다.
2. 설치 유형에 대한 **역할 기반 또는 기능 기반 설치**를 선택합니다.
3. **네트워크 정책 및 액세스 서비스** 서버 역할을 선택합니다. 이 역할을 실행하기 위해 필요한 기능을 알리기 위해 팝업 창이 나타날 수 있습니다.
4. 확인 페이지가 나올 때까지 마법사를 계속 진행합니다. **설치**를 선택합니다.

NPS에 대해 지정된 서버를 가지게 되었으며, 이 서버 또한 VPN 솔루션에서 들어오는 RADIUS 요청을 처리하도록 구성해야 합니다.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN 솔루션이 NPS 서버와 통신하도록 구성

사용하는 VPN 솔루션에 따라 RADIUS 인증 정책을 구성하는 단계가 달라집니다. 이 정책이 RADIUS NPS 서버를 가리키도록 구성합니다.

### <a name="sync-domain-users-to-the-cloud"></a>도메인 사용자 클라우드로 동기화

이 단계는 테넌트에서 이미 완료되었을 수 있지만 Azure AD Connect가 최근에 데이터베이스를 동기화했는지 다시 한 번 확인하는 것이 좋습니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Azure AD Connect** 선택
3. 동기화 상태가 **사용**이고 마지막 동기화가 1시간 미만인지 확인합니다.

동기화의 새 라운드를 시작하는 경우 [Azure AD Connect 동기화: Scheduler](../connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) 지침을 사용합니다.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>사용자가 사용할 수 있는 인증 방법을 결정합니다.

어떤 인증 방법을 NPS 확장 배포와 함께 사용할 수 있는지에 영향을 미치는 두 가지 요소가 있습니다.

1. RADIUS 클라이언트(VPN, Netscaler 서버 또는 기타)와 NPS 서버 간에 사용되는 암호 암호화 알고리즘입니다.
   - **PAP**는 클라우드에서 전화 통화, 단방향 문자 메시지, 모바일 앱 알림 및 모바일 앱 확인 코드와 같은 Azure MFA의 모든 인증 방법을 지원합니다.
   - **CHAPV2** 및 **EAP**는 전화 통화 및 모바일 앱 알림을 지원합니다.
2. 클라이언트 응용 프로그램(VPN, Netscaler 서버 또는 기타)이 처리할 수 있는 입력 방법입니다. 예를 들어 VPN 클라이언트에 사용자가 텍스트 또는 모바일 앱의 인증 코드를 입력할 수 있는 몇 가지 방법이 있습니까?

NPS 확장을 배포하는 경우 이러한 요소를 사용하여 사용자가 사용할 수 있는 방법이 무엇인지 평가합니다. RADIUS 클라이언트가 PAP를 지원하지만, 클라이언트 UX에 확인 코드에 대한 입력 필드가 없는 경우에는 전화 통화 및 모바일 앱 알림의 두 옵션이 지원됩니다.

Azure에서 [지원되지 않는 인증 방법을 사용하지 않도록 설정](howto-mfa-mfasettings.md#selectable-verification-methods)할 수 있습니다.

### <a name="register-users-for-mfa"></a>MFA에 사용자 등록

NPS 확장 프로그램을 사용하고 배포하기 전에 2단계 인증을 수행해야 하는 사용자를 MFA에 등록해야 합니다. 즉시 배포한 확장을 테스트하려면 Multi-Factor Authentication에 완전히 등록된 하나 이상의 테스트 계정이 필요합니다.

테스트를 시작하는 계정을 가져오려면 다음의 단계를 사용합니다.
1. 테스트 계정으로 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에 로그인합니다. 
2. 지시에 따라 확인 방법을 설정합니다.
3. 조건부 액세스 정책을 만들거나 [사용자 상태를 변경](howto-mfa-userstates.md)하여 테스트 계정에 대한 2단계 인증을 요구합니다. 

사용자는 NPS 확장을 통해 인증을 받으려면 먼저 다음 단계에 따라 등록을 수행해야 합니다.

## <a name="install-the-nps-extension"></a>NPS 확장 설치

> [!IMPORTANT]
> VPN 액세스 지점 이외의 다른 서버에 NPS 확장을 설치합니다.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Azure MFA용 NPS 확장 다운로드 및 설치

1.  Microsoft 다운로드 센터에서 [NPS 확장을 다운로드합니다](https://aka.ms/npsmfa).
2.  이진 파일을 구성할 NPS(네트워크 정책 서버)에 복사합니다.
3.  *setup.exe*를 실행하고 설치 지침을 따릅니다. 오류가 발생하면 필수 조건 섹션의 두 라이브러리가 성공적으로 설치되었는지 다시 확인합니다.

### <a name="run-the-powershell-script"></a>PowerShell 스크립트 실행

설치 관리자에서 `C:\Program Files\Microsoft\AzureMfa\Config`(여기서 C: \는 설치 드라이브) 위치에 PowerShell 스크립트를 만듭니다. 이 PowerShell 스크립트는 다음 작업을 수행합니다.

-   자체 서명된 인증서를 만듭니다.
-   인증서의 공개 키를 Azure AD의 서비스 주체에 연결합니다.
-   로컬 컴퓨터 인증서 저장소에 인증서를 저장합니다.
-   네트워크 사용자에게 인증서의 개인 키에 대한 액세스 권한을 부여합니다.
-   NPS를 다시 시작합니다.

PowerShell 스크립트에서 생성하는 자체 서명된 인증서 대신 사용자 고유의 인증서를 사용하려는 경우가 아니면 PowerShell 스크립트를 실행하여 설치를 완료합니다. 여러 서버에 확장을 설치하는 경우 각각 자체 인증서가 있어야 합니다.

1. 관리자 권한으로 Windows PowerShell을 실행합니다.
2. 디렉터리를 변경합니다.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. 설치 관리자가 만든 PowerShell 스크립트를 실행합니다.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. 관리자 권한으로 Azure AD에 로그인합니다.
5. PowerShell이 테넌트 ID에 대해 메시지를 표시합니다. 필수 요소 섹션의 Azure Portal에서 복사한 디렉터리 ID GUID를 사용합니다.
6. PowerShell은 스크립트가 완료되면 성공 메시지를 표시합니다.  

부하 분산을 위해 설정하려는 추가 NPS 서버에서 이러한 단계를 반복합니다.

>[!NOTE]
>PowerShell 스크립트로 인증서를 생성하는 대신 자체 인증서를 사용할 경우 NPS 명명 규약을 따르도록 합니다. 주체 이름은 **CN=\<TenantID\>,OU=Microsoft NPS Extension**이어야 합니다. 

## <a name="configure-your-nps-extension"></a>NPS 확장 구성

이 섹션에는 성공적인 NPS 확장 배포를 위한 디자인 고려 사항 및 제안 사항이 포함되어 있습니다.

### <a name="configuration-limitations"></a>구성 제한 사항

- Azure MFA용 NPS 확장에는 사용자 및 설정을 MFA 서버에서 클라우드로 마이그레이션하는 도구가 없습니다. 이러한 이유로 기존 배포가 아닌 새 배포에 대한 확장을 사용하는 것이 좋습니다. 기존 배포에서 확장을 사용하는 경우 사용자는 증명을 다시 수행하여 클라우드에 MFA 세부 정보를 채워야 합니다.  
- NPS 확장은 온-프레미스 Active Directory의 UPN을 사용하여 Azure MFA에서 보조 인증을 수행하는 사용자를 식별합니다. 확장은 대체 로그인 ID 또는 UPN 이외의 사용자 지정 Active Directory 필드와 같은 다른 식별자를 사용하도록 구성할 수 있습니다. 자세한 내용은 [Multi-Factor Authentication에 대한 NPS 확장을 위한 고급 구성 옵션](howto-mfaserver-nps-vpn.md)을 참조하세요.
- 모든 암호화 프로토콜이 모든 확인 메서드를 지원하는 것은 아닙니다.
   - **PAP**는 전화 통화, 단방향 문자 메시지, 모바일 앱 알림 및 모바일 앱 확인 코드를 지원합니다.
   - **CHAPV2** 및 **EAP**는 전화 통화 및 모바일 앱 알림을 지원합니다.

### <a name="control-radius-clients-that-require-mfa"></a>MFA가 필요한 RADIUS 클라이언트 제어

NPS 확장을 사용하여 RADIUS 클라이언트에 대해 MFA를 사용하도록 설정하면 이 클라이언트에 대한 모든 인증에서 MFA를 수행해야 합니다. 일부 RADIUS 클라이언트에만 MFA를 사용하고 다른 클라이언트에는 MFA를 사용하지 않도록 설정하려면 두 개의 NPS 서버를 구성하고 그 중 하나에만 확장을 설치합니다. MFA에서 확장을 사용하여 구성된 NPS 서버로 요청을 보내고 다른 RADIUS 클라이언트는 확장을 사용하여 구성되지 않은 NPS 서버로 요청을 보내도록 RADIUS 클라이언트를 구성합니다.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록되지 않은 사용자를 위한 준비

MFA에 등록되지 않은 사용자가 있는 경우 인증을 시도할 때 수행할 작업을 결정할 수 있습니다. *HKLM\Software\Microsoft\AzureMFA* 레지스트리 경로에서 *REQUIRE_USER_MATCH* 레지스트리 설정을 사용하여 기능 동작을 제어합니다. 이 설정에는 다음과 같은 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 키가 없거나, 설정되지 않았거나, TRUE로 설정되고 사용자가 등록되지 않은 경우 확장은 MFA 요청에 실패합니다. 키가 FALSE로 설정되고 사용자가 등록되지 않은 경우 MFA를 수행하지 않은 채 인증이 진행됩니다. 사용자가 MFA에 등록된 경우 REQUIRE_USER_MATCH가 FALSE로 설정된 경우에도 MFA로 인증해야 합니다.

사용자가 등록 중이지만 Azure MFA에 아직 등록되지 않은 상태에서 이 키를 만들고 FALSE로 설정하도록 선택할 수 있습니다. 그렇지만 키를 설정하면 MFA에 등록되지 않은 사용자가 로그인할 수 있으므로 프로덕션 환경으로 이동하기 전에 이 키를 제거해야 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>클라이언트 인증서가 예상대로 설치되었는지 어떻게 확인합니까?

설치 관리자에서 만든 자체 서명된 인증서를 인증서 저장소에서 찾고, 사용자에게 부여된 **네트워크 서비스** 권한이 개인 키에 있는지 확인합니다. 인증서에는 **CN \<tenantid\>, OU = Microsoft NPS Extension** 주체 이름이 있습니다.

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>내 클라이언트 인증서가 Azure Active Directory에 있는 내 테넌트와 연결되어 있는지 어떻게 확인할 수 있습니까?

PowerShell 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

이 명령은 PowerShell 세션에서 NPS 확장의 인스턴스와 테넌트를 연결하는 인증서를 모든 출력합니다. 클라이언트 인증서를 개인 키 없이 "Base-64 encoded X.509(.cer)" 파일로 내보내 인증서를 찾고 PowerShell의 목록과 비교합니다.

명령이 둘 이상의 인증서를 반환하면 사람이 읽을 수 있는 형식의 Valid-From(유효 기간 시작) 및 Valid-Until(유효 기간) 타임스탬프를 사용하여 확실한 부적격 항목을 필터링할 수 있습니다.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>내 요청이 ADAL 토큰 오류로 인해 실패하는 이유는 무엇입니까?

이 오류는 몇 가지 이유 중 하나로 발생할 수 있습니다. 다음 단계를 사용하여 문제를 해결합니다.

1. NPS 서버를 다시 시작합니다.
2. 클라이언트 인증서가 예상대로 설치되었는지 확인합니다.
3. 인증서가 Azure AD의 테넌트와 연결되어 있는지 확인합니다.
4. 확장을 실행하는 서버에서 https://login.microsoftonline.com/에 액세스할 수 있는지 확인합니다.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>사용자를 찾을 수 없다고 하는 HTTP 로그 오류로 인해 인증이 실패하는 이유는 무엇입니까?

AD Connect가 실행 중이고 사용자가 Windows Active Directory와 Azure Active Directory 모두에 있는지 확인합니다.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>내 모든 인증이 실패한 상태의 로그에 HTTP 연결 오류가 표시되는 이유는 무엇입니까?

NPS 확장을 실행하는 서버에서 https://adnotifications.windowsazure.com에 액세스할 수 있는지 확인합니다.


## <a name="next-steps"></a>다음 단계

- [Multi-Factor Authentication에 대한 NPS 확장을 위한 고급 구성 옵션](howto-mfa-nps-extension-advanced.md)에서 2단계 확인을 수행하지 않아야 하는 IP 예외 목록 설정 또는 로그인에 대한 대체 ID 구성

- NPS 확장을 사용하여 [원격 데스크톱 게이트웨이](howto-mfa-nps-extension-rdg.md) 및 [VPN 서버](howto-mfa-nps-extension-vpn.md)를 통합하는 방법 알아보기

- [Azure Multi-factor Authentication용 NPS 확장의 오류 메시지 해결](howto-mfa-nps-extension-errors.md)
