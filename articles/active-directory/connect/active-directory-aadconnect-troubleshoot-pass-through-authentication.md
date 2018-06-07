---
title: "Azure AD Connect: 통과 인증 문제 해결 | Microsoft 문서"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 문제를 해결하는 방법에 대해 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증 문제 해결, Active Directory 설치, Azure AD에 필요한 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: billmath
ms.openlocfilehash: b842791be74094c87643528c0b4d3a65be6b3cb1
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증 문제 해결

이 문서에서는 Azure AD 통과 인증과 관련된 일반적인 문제에 대한 문제 해결 정보를 찾을 수 있습니다.

>[!IMPORTANT]
>통과 인증에서 사용자 로그인 문제가 발생하는 경우 클라우드 전용 전역 관리자 계정을 다시 사용하지 않고 해당 기능을 사용하지 않도록 설정하거나 통과 인증 에이전트를 제거하지 마세요. [클라우드 전용 전역 관리자 계정 추가](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다. 이 단계를 수행하는 것이 중요하며 테넌트가 잠기지 않도록 합니다.

## <a name="general-issues"></a>일반적인 문제

### <a name="check-status-of-the-feature-and-authentication-agents"></a>기능 및 인증 에이전트의 상태 확인

테넌트에서 통과 인증 기능이 여전히 **사용**으로 설정되고 인증 에이전트의 상태가 **비활성**이 아닌 **활성**으로 표시되는지 확인합니다. [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)의 **Azure AD Connect** 블레이드로 이동하여 상태를 확인할 수 있습니다.

![Azure Active Directory 관리 센터 - Azure AD Connect 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 관리 센터 - 통과 인증 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>사용자 관련 로그인 오류 메시지

사용자가 통과 인증을 통해 로그인할 수 없는 경우 Azure AD 로그인 화면에서 다음과 같은 사용자 관련 오류 메시지 중 하나가 표시될 수 있습니다. 

|오류|설명|해결 방법
| --- | --- | ---
|AADSTS80001|Active Directory에 연결할 수 없음|에이전트 서버가 자신의 암호에 대한 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버이고 Active Directory에 연결할 수 있는지 확인합니다.  
|AADSTS8002|Active Directory에 연결하는 동안 시간 초과 발생|Active Directory를 사용할 수 있고 에이전트의 요청에 응답하는지 확인합니다.
|AADSTS80004|에이전트에 전달된 사용자 이름이 유효하지 않음|사용자가 올바른 사용자 이름을 사용하여 로그인을 시도하는지 확인합니다.
|AADSTS80005|유효성 검사 중 예측할 수 없는 WebException 발생|일시적인 오류입니다. 요청을 다시 시도하십시오. 계속 실패할 경우 Microsoft 지원에 문의하세요.
|AADSTS80007|Active Directory와 통신 중 오류 발생|에이전트 로그에서 자세한 정보를 확인하고 Active Directory가 예상대로 작동하는지 확인합니다.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Azure Active Directory 관리 센터에서 로그인이 실패한 이유(프리미엄 라이선스 필요)

테넌트에 연결된 Azure AD Premium 라이선스가 있는 경우 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 [로그인 활동 보고서](../active-directory-reporting-activity-sign-ins.md)를 볼 수도 있습니다.

![Azure Active Directory 관리 센터 - 로그인 보고서](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

[Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 **Azure Active Directory** -> **로그인**으로 차례로 이동하고 특정 사용자의 로그인 활동을 클릭합니다. **로그인 오류 코드** 필드를 찾습니다. 다음 표를 사용하여 해당 필드의 값을 실패 이유 및 해결에 매핑합니다.

|로그인 오류 코드|로그인 실패 이유|해결 방법
| --- | --- | ---
| 50144 | 사용자의 Active Directory 암호가 만료되었습니다. | 온-프레미스 Active Directory에서 사용자의 암호를 다시 설정합니다.
| 80001 | 사용할 수 있는 인증 에이전트가 없습니다. | 인증 에이전트를 설치하고 등록합니다.
| 80002 | 인증 에이전트의 암호 유효성 검사 요청 시간이 초과되었습니다. | 인증 에이전트에서 Active Directory에 연결할 수 있는지 확인합니다.
| 80003 | 인증 에이전트에서 받은 응답이 잘못되었습니다. | 여러 사용자 간에 일관되게 재현될 수 있는 문제이면 Active Directory 구성을 확인합니다.
| 80004 | 로그인 요청에 잘못된 UPN(사용자 계정 이름)이 사용되었습니다. | 사용자에게 올바른 사용자 이름으로 로그인하도록 요청합니다.
| 80005 | 인증 에이전트: 오류가 발생했습니다. | 일시적인 오류입니다. 나중에 다시 시도하십시오.
| 80007 | 인증 에이전트에서 Active Directory에 연결할 수 없습니다. | 인증 에이전트에서 Active Directory에 연결할 수 있는지 확인합니다.
| 80010 | 인증 에이전트에서 암호를 해독할 수 없습니다. | 일관되게 재현될 수 있는 문제이면 새 인증 에이전트를 설치하고 등록합니다. 그리고 현재의 인증 에이전트는 제거합니다. 
| 80011 | 인증 에이전트에서 암호 해독 키를 검색할 수 없습니다. | 일관되게 재현될 수 있는 문제이면 새 인증 에이전트를 설치하고 등록합니다. 그리고 현재의 인증 에이전트는 제거합니다.

## <a name="authentication-agent-installation-issues"></a>인증 에이전트 설치 문제

### <a name="an-unexpected-error-occurred"></a>예기치 않은 오류가 발생함

서버에서 [에이전트 로그를 수집](#collecting-pass-through-authentication-agent-logs)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

## <a name="authentication-agent-registration-issues"></a>인증 에이전트 등록 문제

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>차단된 포트로 인해 인증 에이전트 등록에 실패함

인증 에이전트가 설치된 서버가 [여기](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>토큰 또는 계정 권한 부여 오류로 인해 인증 에이전트 등록에 실패함

모든 Azure AD Connect 또는 독립 실행형 인증 에이전트 설치 및 등록 작업에는 클라우드 전용 전역 관리자 계정을 사용해야 합니다. MFA 사용 전역 관리자 계정에 알려진 문제점이 있습니다. 해결 방법으로 MFA를 일시적으로 해제하세요(작업 완료를 위해서만).

### <a name="an-unexpected-error-occurred"></a>예기치 않은 오류가 발생함

서버에서 [에이전트 로그를 수집](#collecting-pass-through-authentication-agent-logs)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

## <a name="authentication-agent-uninstallation-issues"></a>인증 에이전트 제거 문제

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Azure AD Connect 제거 시 나타나는 경고 메시지

테넌트에서 통과 인증을 사용하도록 설정했고 Azure AD Connect를 제거하려고 하면 "다른 통과 인증 에이전트가 다른 서버에 설치되어 있지 않으면 사용자가 Azure AD에 로그인할 수 없습니다."라는 경고 메시지가 표시됩니다.

사용자 로그인이 중단되지 않도록 방지하려면 Azure AD Connect를 제거하기 전에 설정이 [고가용성](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)이어야 합니다.

## <a name="issues-with-enabling-the-feature"></a>기능 사용 관련 문제

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>사용 가능한 인증 에이전트가 없어 기능을 사용하도록 설정하지 못함

테넌트에서 통과 인증을 사용하도록 설정하려면 하나 이상의 활성 인증 에이전트가 있어야 합니다. Azure AD Connect 또는 독립 실행형 인증 에이전트를 설치하여 인증 에이전트를 설치할 수 있습니다.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>차단된 포트로 인해 기능을 사용하도록 설정하지 못함

Azure AD Connect가 설치된 서버가 [여기](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>토큰 또는 계정 권한 부여 오류로 인해 기능을 사용하도록 설정하지 못함

기능을 활성화할 때는 클라우드 전용 전역 관리자 계정을 사용해야 합니다. MFA(Multi-Factor Authentication) 사용 전역 관리자 계정에 알려진 문제점이 있습니다. 해결 방법으로 MFA를 일시적으로 해제하세요(작업 완료를 위해서만).

## <a name="exchange-activesync-configuration-issues"></a>Exchange ActiveSync 구성 문제

이들은 통과 인증에 대한 Exchange ActiveSync 지원을 구성할 때 일반적인 문제입니다.

### <a name="exchange-powershell-issue"></a>Exchange PowerShell 문제

“**'PerTenantSwitchToESTSEnabled' 매개 변수 이름과 일치하는 매개 변수를 찾을 수 없습니다\.**” 오류(`Set-OrganizationConfig` Exchange PowerShell 명령을 실행할 때)가 표시되는 경우 Microsoft 지원에 문의하세요.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync가 작동하지 않음

구성을 적용하는 데 다소 시간이 걸립니다. 시간은 사용자 환경에 따라 달라집니다. 오랜 시간 동안 상황이 지속되면 Microsoft 지원에 문의합니다.

## <a name="collecting-pass-through-authentication-agent-logs"></a>통과 인증 에이전트 로그 수집

발생하는 문제의 유형에 따라 다른 위치에서 통과 인증 에이전트 로그를 확인해야 합니다.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect 로그

설치와 관련된 오류에 대해서는 **%ProgramData%\AADConnect\trace-\*.log**에서 Azure AD Connect 로그를 확인하세요.

### <a name="authentication-agent-event-logs"></a>인증 에이전트 이벤트 로그

인증 에이전트 관련 오류의 경우 서버에서 이벤트 뷰어 응용 프로그램을 열고 **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**에서 확인합니다.

자세한 분석을 위해 "세션" 로그를 활성화합니다. 정상 작동 중에는 이 로그를 활성화한 상태에서 인증 에이전트를 실행하지 마세요. 문제 해결에만 이 로그를 사용하세요. 로그 내용은 로그를 다시 비활성화한 후에만 볼 수 있습니다.

### <a name="detailed-trace-logs"></a>자세한 추적 로그

사용자 로그인 실패 문제를 해결하려면 **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**에서 추적 로그를 찾습니다. 이러한 로그에는 통과 인증 기능을 통해 특정 사용자 로그인이 실패한 이유가 포함되어 있습니다. 이러한 오류는 이전의 [표](#sign-in-failure-reasons-on-the-Azure-portal)에 나오는 로그인 실패 이유에도 매핑됩니다. 다음은 로그 항목의 예제입니다.

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

명령 프롬프트를 열고 다음 명령을 실행하면 오류(위 예제의 경우 '1328')에 대한 자세한 설명을 얻을 수 있습니다. 참고: '1328'을 로그에 표시되는 실제 오류 번호로 바꾸세요.

`Net helpmsg 1328`

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

감사 로깅이 사용되는 경우에는 도메인 컨트롤러의 보안 로그에서도 추가 정보를 찾을 수 있습니다. 통과 인증 에이전트에서 보낸 로그인 요청을 쿼리하는 간단한 방법은 다음과 같습니다.

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>성능 모니터 카운터

인증 에이전트를 모니터링하는 다른 방법은 인증 에이전트가 설치된 각 서버에서 특정 성능 모니터 카운터를 추적하는 것입니다. 다음 전역 카운터(**# PTA 인증**, **#PTA 실패 인증** 및 **#PTA 성공 인증**) 및 오류 카운터(**# PTA 인증 오류**)를 사용합니다.

![통과 인증 성능 모니터 카운터](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>통과 인증은 여러 인증 에이전트를 사용하여 고가용성을 제공하지만 부하 분산 기능은 제공하지 _않습니다_. 구성에 따라 모든 인증 에이전트는 요청과 _동일한_ 수를 수신하지 _않습니다_. 특정 인증 에이전트는 트래픽을 전혀 수신하지 않을 수도 있습니다.
