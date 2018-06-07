---
title: 자주 묻는 질문과 대답 - Azure Active Directory B2C
description: Azure Active Directory B2C에 대한 FAQ(자주 묻는 질문과 대답)
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d213a3f8bed5af526efba39868855b6f6727a7c0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: FAQ(질문과 대답) 
이 페이지는 Azure AD(Azure Active Directory) B2C에 대한 자주 묻는 질문에 응답합니다. 업데이트를 계속 확인합니다.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>기존의 직원 기반 Azure AD 테넌트에서 Azure AD B2C 기능을 사용할 수 있나요?
Azure AD 및 Azure AD B2C는 별개 제품으로, 동일한 테넌트에서 함께 사용할 수 없습니다.  Azure AD 테넌트는 조직을 나타냅니다.  Azure AD B2C 테넌트는 신뢰 당사자 응용 프로그램에서 사용할 ID 컬렉션을 나타냅니다.  사용자 지정 정책(공개 미리 보기)을 통해 Azure AD B2C가 Azure AD로 페더레이션되어 조직의 직원을 인증할 수 있습니다.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Azure AD B2C를 사용하여 소셜 로그인(Facebook 및 Google+)을 Office 365에 제공할 수 있나요?
Azure AD B2C를 사용하여 Microsoft Office 365에 사용자를 인증할 수는 없습니다.  Azure AD는 SaaS 앱에 대한 직원 액세스를 관리하기 위한 Microsoft 솔루션으로, 이러한 목적으로 설계된 라이선스, 조건부 액세스 등의 기능이 있습니다.  Azure AD B2C는 웹 및 모바일 응용 프로그램을 빌드하기 위한 ID 및 액세스 관리 플랫폼을 제공합니다.  Azure AD B2C가 Azure AD 테넌트에 페더레이션되도록 구성된 경우 Azure AD 테넌트는 Azure AD B2C를 사용하는 응용 프로그램에 대한 직원 액세스를 관리합니다.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C에서 로컬 계정은 무엇인가요? Azure AD의 회사 또는 학교 계정과 어떻게 다른가요?
Azure AD 테넌트에서 테넌트에 속한 사용자는 `<xyz>@<tenant domain>` 형식의 메일 주소로 로그인합니다.  `<tenant domain>`은 테넌트의 확인된 도메인 중 하나이거나 초기 `<...>.onmicrosoft.com` 도메인입니다. 이 계정 유형은 회사 또는 학교 계정입니다.

Azure AD B2C 테넌트에서 대부분의 앱은 사용자가 임의의 메일 주소(예: joe@comcast.net, bob@gmail.com, sarah@contoso.com 또는 jim@live.com)로 로그인하도록 합니다. 이 계정 유형은 로컬 계정입니다.  또한 임의의 사용자 이름이 로컬 계정으로 지원됩니다(예: joe, bob, sarah 또는 jim). Azure Portal에서 Azure AD B2C에 대한 ID 공급자를 구성하는 경우 이러한 두 로컬 계정 형식 중 하나를 선택할 수 있습니다. Azure AD B2C 테넌트에서 **ID 공급자**를 클릭한 다음, 로컬 계정에서 **사용자 이름**을 선택합니다. 

응용 프로그램에 대한 사용자 계정은 항상 등록 정책, 등록 또는 로그인 정책을 통해 또는 Azure AD Graph API를 사용하여 만들어야 합니다. Azure Portal에서 만들어진 사용자 계정은 테넌트를 관리하는 데만 사용합니다.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>지금 어떤 소셜 ID 공급자를 지원하나요? 나중에 어떤 공급자를 지원하려는 계획입니까?
현재 Facebook, Google+, LinkedIn, Amazon, Twitter(미리 보기), WeChat(미리 보기), Weibo(미리 보기) 및 QQ(미리 보기)가 지원됩니다. 고객의 요구에 따라 다른 인기 있는 소셜 ID 공급자에 대한 지원을 추가합니다.

Azure AD B2C에서는 [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)에 대한 지원도 추가했습니다.  이러한 [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)을 통해 개발자는 [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 또는 SAML을 지원하는 모든 ID 공급자가 있는 사용자 고유의 정책을 만들 수 있습니다. 

[사용자 지정 정책 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)을 확인하여 사용자 지정 정책을 시작하세요.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>범위를 구성하여 다양한 소셜 ID 공급자에서 소비자에 대한 자세한 정보를 수집할 수 있나요?
아니요, 하지만 이 기능은 우리의 로드맵입니다. 지원되는 소셜 ID 공급자 집합에 사용되는 기본 범위는 다음과 같습니다.

* Facebook: 전자 메일
* Google+: 전자 메일
* Microsoft 계정: openid 메일 프로필
* Amazon: 프로필
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>내 응용 프로그램을 Azure AD B2C와 함께 작업하려면 Azure에서 실행해야 하나요?
아니요, 어디서나 응용 프로그램을 호스팅할 수 있습니다.(클라우드 또는 온-프레미스) 공개적으로 액세스할 수 있는 끝점에서 HTTP 요청을 보내고 받는 기능만 있으면 Azure AD B2C와 상호 작용할 수 있습니다.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>여러 개의 Azure AD B2C 테넌트가 있습니다. Azure Portal에서 관리하려면 어떻게 하나요?
Azure Portal의 왼쪽 메뉴에서 'Azure AD B2C'를 열기 전에 관리하려는 디렉터리로 전환해야 합니다.  Azure Portal의 오른쪽 위에 있는 사용자 ID를 클릭하여 디렉터리를 전환한 후 나타나는 드롭다운에서 디렉터리를 선택합니다.  이미지를 포함하는 단계별 지침을 보려면 [Azure AD B2C 설정으로 이동](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)을 참조하세요.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C에서 보낸 확인 메일(콘텐츠 및 "From:" 필드)을 어떻게 사용자 지정하나요?
[회사 브랜딩 기능](../active-directory/customize-branding.md)을 사용하여 확인 메일의 콘텐츠를 사용자 지정할 수 있습니다. 특히, 전자 메일의 다음 두 요소를 사용자 지정할 수 있습니다.

* **배너 로고**: 오른쪽 아래에 표시됩니다.
* **배경색**: 맨 위에 표시됩니다.

    ![사용자 지정된 확인 전자 메일의 스크린샷](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

전자 메일 서명에는 Azure AD B2C 테넌트를 처음 만들 때 제공한 Azure AD B2C 테넌트의 이름이 포함됩니다. 다음 지침을 사용하여 이름을 변경할 수 있습니다.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 블레이드를 엽니다.
1. **속성** 탭을 클릭합니다.
1. **이름** 필드를 변경합니다.
1. 페이지 위쪽에서 **저장**을 클릭합니다.

현재 전자 메일의 "받는 사람:" 필드를 변경할 방법은 없습니다. [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails)에서 확인 메일의 본문 사용자 지정에 관심이 있다고 투표하세요.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>데이터베이스에서 Azure AD B2C로 기존 사용자 이름, 암호 및 프로필을 어떻게 마이그레이션할 수 있나요?
Azure AD Graph API를 사용하여 마이그레이션 도구를 작성할 수 있습니다. 자세한 내용은 [사용자 마이그레이션 가이드](active-directory-b2c-user-migration.md)를 참조하세요.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C의 로컬 계정에 사용되는 암호 정책은 무엇인가요?
로컬 계정에 대한 Azure AD B2C 암호 정책은 Azure AD 정책을 기반으로 합니다. Azure AD B2C의 등록, 등록 또는 로그인 및 암호 재설정 정책은 "강력한" 암호 강도를 사용하며 어떠한 암호도 만료되지 않습니다. 자세한 내용은 [Azure AD 암호 정책](https://msdn.microsoft.com/library/azure/jj943764.aspx)을 참조하세요.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Azure AD Connect를 사용하여 온-프레미스 Active Directory에 저장된 소비자 ID를 Azure AD B2C에 마이그레이션할 수 있나요?
아니요, Azure AD Connect는 Azure AD B2C와 함께 작동하지 않습니다. [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용자 마이그레이션에 사용하는 것이 좋습니다.  자세한 내용은 [사용자 마이그레이션 가이드](active-directory-b2c-user-migration.md)를 참조하세요.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>앱의 Azure AD B2C 페이지가 iFrame 내에서 열릴 수 있나요?
아니요. 보안상의 이유로, Azure AD B2C 페이지는 iFrame 내에서 열릴 수 없습니다.  Microsoft 서비스에서 브라우저와 통신하여 iFrames를 금지합니다.  일반적인 보안 커뮤니티와 OAUTH2 사양에서는 클릭재킹(clickjacking) 위험 때문에 ID 환경에 iFrames를 사용하지 않도록 권장합니다.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C는 Microsoft Dynamics와 같은 CRM 시스템과 함께 작동합니까?
Microsoft Dynamics 365 포털과의 통합을 사용할 수 있습니다.  [인증에 Azure AD B2C를 사용하도록 Dynamics 365 포털 구성](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)을 참조하세요.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C가 SharePoint 온-프레미스 2016 또는 이전 버전과 함께 작동하나요?
Azure AD B2C는 SharePoint 외부 파트너 공유 시나리오에 해당하지 않습니다. 대신 [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx)를 참조하세요.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>외부 ID를 관리하려면 Azure AD B2C 또는 B2B를 사용해야 합니까?
외부 ID 시나리오에 적절한 기능을 적용하는 방법에 대한 자세한 내용은 이 문서에서 [외부 ID](../active-directory/active-directory-b2b-compare-external-identities.md) 를 참조하세요.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C가 제공하는 보고 및 감사 기능은 무엇인가요? Azure AD Premium과 동일한가요?
아니요, Azure AD B2C는 Azure AD Premium과 동일한 보고서 집합을 지원하지 않습니다. 하지만 많은 공통점이 있습니다.

* **로그인 보고서**는 Azure Portal(Azure Active Directory > 활동 > 로그인)에서만 사용 가능하며 그래프 API로 제공되지 않습니다. 요약 정보와 함께 각 로그인 레코드를 제공합니다.
* **감사 보고서**는 Azure Portal(Azure Active Directory > 활동 > 감사 로그)에서만 사용 가능하며 그래프 API로 제공되지 않습니다. 관리 작업 및 응용 프로그램 작업을 모두 포함합니다. 
* **사용 현황 보고서**는 [사용 현황 보고 API](active-directory-b2c-reference-usage-reporting-api.md)를 통해서만 제공되며 Azure Portal을 통해 사용할 수 없습니다. 사용자 수, 로그인 수 및 MFA 크기가 포함됩니다. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C에서 제공하는 페이지의 UI를 지역화할 수 있습니까? 어떤 언어가 지원되나요?
예!  공개 미리 보기로 제공되는 [언어 사용자 지정](active-directory-b2c-reference-language-customization.md)을 참조하세요.  36개 언어에 대한 번역이 제공되며, 모든 문자열을 요구에 맞게 재정의할 수 있습니다.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Azure AD B2C에서 제공하는 등록 및 로그인 페이지에 고유 URL을 사용할 수 있나요? 예를 들어 login.microsoftonline.com에서 login.contoso.com으로 URL을 변경할 수 있나요?
현재는 아닙니다. 이 기능은 우리의 로드맵입니다. Azure Portal의 **도메인** 탭에서 도메인을 확인할 경우 이 작업이 수행되지 않습니다.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트를 삭제하려면 어떻게 해야 하나요?
Azure AD B2C 테넌트를 삭제하려면 다음 단계를 수행합니다.

1. 다음 단계에 따라 Azure Portal의 [Azure AD B2C 설정으로 이동](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)합니다.
1. **응용 프로그램**, **ID 공급자** 및 **모든 정책**으로 이동하여 각 블레이드에서 모든 항목을 삭제합니다.
1. 이제 [Azure Portal](https://portal.azure.com/)에 구독 관리자로 로그인합니다. Azure에 등록하는 데 사용한 동일한 Microsoft 계정이나 동일한 회사 또는 학교 계정을 사용합니다.
1. 삭제하려는 Azure AD B2C 테넌트로 전환합니다.
2. 왼쪽에서 Active Directory 메뉴로 이동합니다.
3. **사용자 및 그룹**을 선택합니다.
4. 각 사용자를 차례로 선택합니다(현재 로그인에 사용한 구독 관리자 사용자 제외). 페이지의 아래쪽에서 **삭제**를 클릭한 다음 메시지가 나타나면 **예**를 클릭합니다.
5. **앱 등록**을 클릭합니다.
6. **b2c-extensions-app**이라는 응용 프로그램을 선택합니다. **삭제**를 클릭하고 대화 상자가 나타나면 **예**를 클릭합니다.
7. **개요**를 선택합니다.
8. **디렉터리 삭제**를 클릭합니다. 프로세스를 완료하려면 화면의 지시를 따릅니다.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite의 일부로 Azure AD B2C를 가져올 수 있나요?
아니요, Azure AD B2C는 종량제 Azure 서비스이며 Enterprise Mobility Suite의 일부가 아닙니다.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C에서 발생한 문제를 보고하려면 어떻게 해야 합니까?
[Azure Active Directory B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)을 참조하세요.
