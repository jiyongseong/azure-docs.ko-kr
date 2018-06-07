---
title: 'Azure AD Connect: Seamless Single Sign-On - 빠른 시작 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory Seamless Single Sign-On을 시작하는 방법을 설명합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: billmath
ms.openlocfilehash: d026009151f85a01f14ce4dd8a510f60ff407da1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory Seamless Single Sign-On: 빠른 시작

## <a name="deploy-seamless-single-sign-on"></a>Seamless Single Sign-On 배포

Azure AD(Active Directory) Seamless SSO(Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 데스크톱에 있을 때 사용자를 자동으로 서명합니다. Seamless SSO는 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 응용 프로그램에 쉽게 액세스할 수 있습니다.

Seamless SSO를 배포하려면 다음 단계를 수행합니다.

## <a name="step-1-check-the-prerequisites"></a>1단계: 필수 구성 요소 확인

다음 필수 조건이 충족되는지 확인합니다.

* **Azure AD Connect 서버 설정**: 로그인 방법으로 [통과 인증](active-directory-aadconnect-pass-through-authentication.md)을 사용하는 경우 추가 필수 구성 요소 확인이 필요하지 않습니다. 로그인 방법으로 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md)를 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.
   - Azure AD Connect 버전 1.1.644.0 이상을 사용합니다. 
   - 방화벽이나 프록시에서 DNS 허용 목록을 허용하면 포트 443을 통해 **\*.msappproxy.net** URL에 대한 연결을 허용 목록에 추가합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다. 이 필수 조건은 해당 기능을 사용하도록 설정한 경우에만 적용할 수 있습니다. 실제 사용자 로그인에서는 필요하지 않습니다.

    >[!NOTE]
    >Azure AD Connect 버전 1.1.557.0, 1.1.558.0, 1.1.561.0 및 1.1.614.0에는 암호 해시 동기화와 관련된 문제가 있습니다. 암호 해시 동기화를 통과 인증과 함께 사용하지 _않으려는_ 경우 자세한 내용은 [Azure AD Connect 릴리스 정보](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470)를 참조하세요.

* **도메인 관리자 자격 증명 설정**: 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명이 있어야 합니다.
    * Azure AD Connect를 통해 Azure AD에 동기화합니다.
    * Seamless SSO를 사용하도록 설정할 사용자를 포함합니다.

## <a name="step-2-enable-the-feature"></a>2단계: 기능 활성화

[Azure AD Connect](active-directory-aadconnect.md)를 통해 Seamless SSO를 사용하도록 설정합니다.

Azure AD Connect를 새로 설치하는 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **Single Sign-On 사용** 옵션을 선택합니다.

![Azure AD Connect: 사용자 로그인](./media/active-directory-aadconnect-sso/sso8.png)

Azure AD Connect가 이미 설치되어 있는 경우 Azure AD Connect에서 **사용자 로그인 변경** 페이지를 선택하고 **다음**을 선택합니다.

![Azure AD Connect: 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

**Single Sign-On 사용** 페이지에 도달할 때까지 마법사를 계속 진행합니다. 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 제공합니다.
    * Azure AD Connect를 통해 Azure AD에 동기화합니다.
    * Seamless SSO를 사용하도록 설정할 사용자를 포함합니다.

마법사를 완료하면 테넌트에서 SSO Seamless를 사용하도록 설정됩니다.

>[!NOTE]
> 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않습니다. 이 기능을 사용하도록 설정하는 데에만 사용됩니다.

다음 지침에 따라 Seamless SSO를 올바르게 설정했는지 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**를 선택합니다.
4. **Seamless Single Sign-On** 기능이 **설정**으로 표시되는지 확인합니다.

![Azure Portal: Azure AD Connect 창](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>3단계: 기능 배포

기능을 사용자에게 롤아웃하려면 Active Directory의 그룹 정책을 사용하여 사용자의 인트라넷 영역 설정에 다음 Azure AD URL을 추가해야 합니다.

- https://autologon.microsoftazuread-sso.com


또한 그룹 정책을 통해 **스크립트를 통해 상태 표시줄에 대한 업데이트 허용**이라는 인트라넷 영역 정책 설정을 활성화해야 합니다. 

>[!NOTE]
> 다음 지침은 Windows의 Internet Explorer 및 Google Chrome(Internet Explorer와 신뢰할 수 있는 사이트 URL 집합을 공유하는 경우)에서만 작동합니다. Mac에서 Mozilla Firefox 및 Google Chrome을 설정하는 방법에 대한 지침은 다음 섹션을 참조하세요.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>사용자의 인트라넷 영역 설정을 수정해야 하는 이유

기본적으로 브라우저는 특정 URL에서 올바른 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 “http://contoso/”은 인트라넷 영역에 매핑되지만, "http://intranet.contoso.com/"는 인터넷 영역에 매핑됩니다(URL에 마침표가 포함되어 있기 때문). 브라우저는 URL이 브라우저의 인트라넷 영역에 명시적으로 추가되지 않는 한 클라우드 엔드포인트(예: Azure AD URL)에 Kerberos 티켓을 보내지 않습니다.

### <a name="detailed-steps"></a>자세한 단계

1. 그룹 정책 관리 편집기 도구를 엽니다.
2. 일부 또는 모든 사용자에게 적용되는 그룹 정책을 편집합니다. 예를 들어 **기본 도메인 정책**은 다음과 같습니다.
3. **사용자 구성** > **관리 템플릿** > **Windows 구성 요소** > **Internet Explorer** > **인터넷 제어판** > **보안 페이지**를 찾아봅니다. 그런 다음 **영역에 사이트 할당 목록**을 선택합니다.
    ![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)
4. 정책을 사용하도록 설정한 다음, 대화 상자에서 다음 값을 입력합니다.
   - **값 이름**: Kerberos 티켓이 전달되는 Azure AD URL입니다.
   - **값**(데이터): **1**은 인트라넷 영역을 나타냅니다.

    결과는 다음과 유사하게 표시됩니다.

    값: https://autologon.microsoftazuread-sso.com
  
    Data 1

   >[!NOTE]
   > 일부 사용자가 공유 키오스크에 로그인하는 경우와 같이 이러한 사용자가 Seamless SSO를 사용하지 못하게 하려면 이전 값을 **4**로 설정합니다. 이 작업에서는 Azure AD URL이 제한된 영역에 추가되고 Seamless SSO가 항상 실패하게 됩니다.
   >

5. **확인**을 선택한 다음, 다시 **확인**을 선택합니다.

    ![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso7.png)

6. **사용자 구성** > **관리 템플릿** > **Windows 구성 요소** > **Internet Explorer** > **인터넷 제어판** > **보안 페이지** > **인트라넷 영역**을 찾아봅니다. 그런 다음 **스크립트를 통해 상태 표시줄에 대한 업데이트 허용**을 선택합니다.

    ![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso11.png)

7. 정책 설정을 활성화한 다음, **확인**을 선택합니다.

    ![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>브라우저 고려 사항

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox(모든 플랫폼)

Mozilla Firefox는 Kerberos 인증을 자동으로 사용하지 않습니다. 각 사용자는 다음 단계에 따라 Firefox 설정에 Azure AD URL을 수동으로 추가해야 합니다.
1. Firefox를 실행하고 주소 표시줄에 `about:config`를 입력합니다. 표시되는 모든 알림을 해제합니다.
2. **network.negotiate-auth.trusted-uris** 기본 설정을 검색합니다. 이 기본 설정은 Firefox의 신뢰할 수 있는 Kerberos 인증 사이트를 나열합니다.
3. 마우스 오른쪽 단추로 클릭하고 **수정**을 선택합니다.
4. 필드에 https://autologon.microsoftazuread-sso.com을 입력합니다.
5. **확인**을 선택한 다음, 브라우저를 다시 엽니다.

#### <a name="safari-mac-os"></a>Safari(Mac OS)

Mac OS를 실행하는 컴퓨터가 AD에 가입되어 있는지 확인합니다. AD 가입에 지침은 [Active Directory와 통합 OS X에 대한 모범 사례](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf)를 참조하세요.

#### <a name="google-chrome-all-platforms"></a>Google Chrome(모든 플랫폼)

환경에서 [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) 또는 [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) 정책 설정을 재정의한 경우 Azure AD의 URL(https://autologon.microsoftazuread-sso.com))도 해당 정책 설정에 추가해야 합니다.

#### <a name="google-chrome-mac-os-only"></a>Google Chrome(Mac OS에만 해당)

Mac OS 및 기타 Windows가 아닌 플랫폼에서 Google Chrome의 경우 통합 인증을 위해 Azure AD URL을 허용 목록에 추가하는 방법은 [Chromium 프로젝트 정책 목록](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)을 참조하세요.

타사 Active Directory 그룹 정책 확장을 사용하여 Mac 사용자의 Firefox 및 Google Chrome에 Azure AD URL을 롤아웃하는 방법은 이 문서에 포함되어 있지 않습니다.

#### <a name="known-browser-limitations"></a>알려진 브라우저 제한 사항

Firefox 및 Microsoft Edge 브라우저의 개인 검색 모드에서는 Seamless SSO가 작동하지 않습니다. 또한 브라우저가 고급 보호 모드에서 실행 중인 경우 Internet Explorer에서 작동하지 않습니다.

## <a name="step-4-test-the-feature"></a>4단계: 기능 테스트

특정 사용자에 대한 기능을 테스트하려면 다음 조건이 제대로 갖추어져 있는지 확인합니다.
  - 사용자가 회사 장치에 로그인합니다.
  - 장치가 Active Directory 도메인에 가입되어 있습니다.
  - 장치가 회사의 유선/무선 네트워크 또는 원격 액세스 연결(예: VPN 연결)을 통해 DC(도메인 컨트롤러)에 직접 연결되어 있습니다.
  - 그룹 정책을 통해 해당 사용자에게 [기능을 롤아웃](##step-3-roll-out-the-feature)했습니다.

사용자가 암호가 아니라 사용자 이름만 입력하는 시나리오를 테스트하려면 다음을 수행합니다.
   - 새 개인 브라우저 세션에서 https://myapps.microsoft.com/에 로그인합니다.

사용자가 사용자 이름이나 암호를 입력할 필요가 없는 시나리오를 테스트하려면 다음 중 하나를 수행합니다. 
   - 새 개인 브라우저 세션에서 https://myapps.microsoft.com/contoso.onmicrosoft.com에 로그인합니다. *contoso*를 테넌트의 이름으로 바꿉니다.
   - 새 개인 브라우저 세션에서 https://myapps.microsoft.com/contoso.com에 로그인합니다. *contoso.com*을 테넌트에서 확인된 도메인(페더레이션 도메인이 아님)으로 바꿉니다.

## <a name="step-5-roll-over-keys"></a>5단계: 키 롤오버

2단계에서, Azure AD Connect는 Seamless SSO를 사용하도록 설정한 모든 Active Directory 포리스트에서 컴퓨터 계정(Azure AD를 나타냄)을 만듭니다. 더 자세히 알아보려면 [Azure Active Directory Seamless Single Sign-On: 기술 심층 분석](active-directory-aadconnect-sso-how-it-works.md)을 참조하세요. 향상된 보안을 위해 이러한 컴퓨터 계정의 Kerberos 암호 해독 키를 주기적으로 롤오버하는 것이 좋습니다. 키를 롤오버하는 방법에 대한 지침은 [Azure Active Directory Seamless Single Sign-On: 질문과 대답](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)을 참조하세요.

>[!IMPORTANT]
>이 기능을 사용하도록 설정한 후에는 이 단계를 _즉시_ 수행할 필요가 없습니다. 적어도 30일마다 Kerberos 암호 해독 키를 롤오버합니다.

## <a name="next-steps"></a>다음 단계

- [기술 심층 분석](active-directory-aadconnect-sso-how-it-works.md): Seamless Single Sign-On 기능의 작동 방식을 이해합니다.
- [질문과 대답](active-directory-aadconnect-sso-faq.md): Azure Active Directory Seamless Single Sign-On에 대한 질문과 대답입니다.
- [문제 해결](active-directory-aadconnect-troubleshoot-sso.md): Seamless Single Sign-On 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.
