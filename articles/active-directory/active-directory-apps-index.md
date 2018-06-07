---
title: Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스 | Microsoft Azure
description: 페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 71c2463a38f2115bd3aeff82b100e3f2c20e7708
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스
이 페이지는 Azure AD(Azure Active Directory)에서 다양한 응용 프로그램 관련 기능에 대해 작성된 모든 문서의 전체 목록을 제공합니다.

원하는 정보에 따라 읽을 문서에 대한 지침 뿐만 아니라 각 주요 기능 영역에 대한 간략한 소개가 있습니다.

## <a name="overview-articles"></a>개요 문서
아래의 문서는 Azure AD 응용 프로그램 관리 기능에 대한 간략한 설명을 원하는 사람에게 좋은 방법입니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure AD가 해결하는 응용 프로그램 관리 문제 소개 |[Azure Active Directory(AD)와 응용 프로그램 관리](manage-apps/what-is-application-management.md) |
| Single Sign-On 사용, 앱에 액세스하는 사용자 정의 및 사용자가 앱을 시작하는 방법과 관련된 Azure AD의 다양한 기능 개요 |[Azure Active Directory에서 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md) |
| Azure AD에 앱을 통합하는 경우 관련된 여러 단계 살펴보기 |[Azure Active Directory와 응용 프로그램 통합](manage-apps/plan-an-application-integration.md)<br /><br />[Single Sign-On SaaS 앱 사용](active-directory-enterprise-apps-manage-sso.md)<br /><br />[앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md) |
| 앱이 Azure AD에서 나타나는 방법에 대한 기술 정보 |[응용 프로그램을 Azure AD에 추가하는 방법 및 이유](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>문제 해결 문서
이 섹션을 통해 관련 문제 해결 가이드에 빠르게 액세스할 수 있습니다. 각 기능 영역에 대한 자세한 내용은 이 페이지의 나머지 부분에서 찾을 수 있습니다.

| 기능 영역 |  |
|:---:| --- |
| 페더레이션된 Single Sign-On |[SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md) |
| 암호 기반 Single Sign-On |[Internet Explorer용 액세스 패널 확장 문제 해결](active-directory-saas-ie-troubleshooting.md) |
| 응용 프로그램 프록시 |[앱 프록시 문제 해결 가이드](active-directory-application-proxy-troubleshoot.md) |
| 온-프레미스 AD 및 Azure AD 간의 Single Sign-On |[암호 해시 동기화 문제 해결](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[비밀번호 쓰기 저장 문제 해결](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 동적 그룹 멤버 자격 |[동적 그룹 멤버 자격 문제 해결](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>SSO(Single Sign-On)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>페더레이션된 Single Sign-On: 하나의 ID를 사용하여 많은 앱에 로그인
Single Sign-On을 사용하면 사용자가 하나의 자격 증명 집합을 사용하여 다양한 앱 및 서비스에 액세스할 수 있습니다. 페더레이션은 Single Sign-On을 사용할 수 있는 하나의 메서드입니다. 사용자가 페더레이션된 앱에 로그인할 때 Azure Active Directory에서 렌더링된 해당 조직의 공식 로그인 페이지에 리디렉션되고 인증 성공 시 앱으로 다시 리디렉션됩니다.

| 문서 가이드 |  |
|:---:| --- |
| 페더레이션 및 다른 형식의 로그온 소개 |[Azure AD로 Single Sign-On](manage-apps/what-is-single-sign-on.md) |
| Single Sign-On 구성 단계로 단순하게 Azure AD와 사전 통합된 수천 개의 SaaS 앱 |[Azure AD 응용 프로그램 갤러리 시작](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[페더레이션을 지원하는 사전 통합된 앱의 전체 목록](active-directory-saas-tutorial-list.md)<br /><br />[Azure AD 앱 갤러리에 앱을 추가하는 방법](active-directory-app-gallery-listing.md) |
| [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md) 등과 같이 앱에 Single Sign-On을 구성하는 방법에 대한 150개 이상의 앱 자습서 |[Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md) |
| Single Sign-On 구성을 수동으로 설정하고 사용자 지정하는 방법 |[Azure Active Directory 응용 프로그램 갤러리에 없는 앱에 대한 Single Sign-On을 구성하는 방법](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[사전 통합된 앱에 대해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법](active-directory-saml-claims-customization.md) |
| SAML 프로토콜을 사용하는 페더레이션된 앱에 대한 문제 해결 가이드 |[SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md) |
| 앱 인증서의 만료 날짜를 구성하는 방법 및 인증서를 갱신하는 방법 |[Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](active-directory-sso-certs.md) |

페더레이션된 Single Sign-On은 사용자 당 최대 열 개의 앱에서 Azure AD의 모든 버전에 사용할 수 있습니다. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 은 응용 프로그램을 무제한 지원합니다. 조직에 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 또는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)이 있는 경우 [그룹을 사용하여 페더레이션된 응용 프로그램에 액세스 권한을 할당](#managing-access-to-applications)할 수 있습니다.

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>암호 기반 Single Sign-On: 페더레이션되지 않은 앱에 대한 계정 공유 및 SSO
페더레이션을 지원하지 않는 응용 프로그램에 Single Sign-On을 사용하려면 Azure AD는 SaaS 앱에 암호를 안전하게 저장하고 해당 앱에 자동으로 사용자를 로그인할 수 있도록 하는 암호 관리 기능을 제공합니다. 새로 만든 계정에 자격 증명을 쉽게 배포하고 팀 계정을 여러 사람과 공유할 수 있습니다. 사용자는 액세스가 지정된 계정에 대한 자격 증명을 알 필요가 없습니다.

| 문서 가이드 |  |
|:---:| --- |
| 암호 기반 SSO의 작동 방법 소개 및 간략한 기술 개요 |[Azure AD를 사용한 암호 기반 Single Sign-On](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| 계정 공유 및 Azure AD에서 이러한 문제를 해결할 방법과 관련된 시나리오의 요약 |[Azure AD와 계정 공유](active-directory-sharing-accounts.md) |
| 일정한 간격으로 특정 앱에 대한 암호 자동 변경 |[자동화된 암호 롤오버(미리 보기)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Azure AD 암호 관리 확장의 Internet Explorer 버전에 대한 배포 및 문제 해결 가이드 |[그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장을 배포하는 방법](active-directory-saas-ie-group-policy.md)<br /><br />[Internet Explorer용 액세스 패널 확장 문제 해결](active-directory-saas-ie-troubleshooting.md) |

암호 기반 Single Sign-On은 사용자 당 최대 열 개의 앱에서 Azure AD의 모든 버전에 사용할 수 있습니다. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 은 응용 프로그램을 무제한 지원합니다. 조직에 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 또는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)이 있는 경우 [그룹을 사용하여 응용 프로그램에 액세스 권한을 할당](#managing-access-to-applications)할 수 있습니다. 자동화된 암호 롤오버는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 기능입니다.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>앱 프록시: 온-프레미스 응용 프로그램에 Single Sign-On 및 원격 액세스
사용자가 액세스해야 하는 개인 네트워크의 응용 프로그램이 있고 네트워크 외부에 장치가 있는 경우 Azure AD 응용 프로그램 프록시를 사용하여 해당 앱에 안전한 원격 액세스를 사용하도록 설정할 수 있습니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure AD 응용 프로그램 프록시 및 작동 방식 개요 |[온-프레미스 응용 프로그램에 대한 보안 원격 액세스 제공](manage-apps/application-proxy.md) |
| 응용 프로그램 프록시를 구성하는 방법 및 첫 번째 앱을 게시하는 방법에 대한 자습서 |[Azure AD 앱 프록시를 설정하는 방법](manage-apps/application-proxy-enable.md)<br /><br />[앱 프록시 커넥터를 자동으로 설치하는 방법](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[앱 프록시를 사용하여 응용 프로그램을 게시하는 방법](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[고유한 도메인 이름을 사용하는 방법](manage-apps/application-proxy-configure-custom-domain.md) |
| 앱 프록시로 게시된 앱에 Single Sign-On 및 조건부 액세스를 사용하는 방법 |[앱 프록시를 사용하는 Single-Sign-On](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[조건부 액세스 및 응용 프로그램 프록시](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| 다음 시나리오에 응용 프로그램 프록시를 사용하는 방법에 대한 지침 |[네이티브 클라이언트 응용 프로그램을 지원하는 방법](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[클레임 인식 응용 프로그램을 지원하는 방법](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[별도 네트워크 및 위치에 게시된 응용 프로그램을 지원하는 방법](manage-apps/application-proxy-connector-groups.md) |
| 응용 프로그램 프록시에 대한 문제 해결 가이드 |[앱 프록시 문제 해결 가이드](active-directory-application-proxy-troubleshoot.md) |

응용 프로그램 프록시는 사용자 당 최대 열 개의 앱에서 Azure AD의 모든 버전에 사용할 수 있습니다. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 은 응용 프로그램을 무제한 지원합니다. 조직에 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 또는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)이 있는 경우 [그룹을 사용하여 응용 프로그램에 액세스 권한을 할당](#managing-access-to-applications)할 수 있습니다.

[Azure AD 도메인 서비스](../active-directory-domain-services/active-directory-ds-overview.md)에 관심이 있을 수 있으며 이를 사용하면 해당 응용 프로그램의 ID 요구를 만족하면서 Azure에 온-프레미스 응용 프로그램을 마이그레이션할 수 있습니다.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Azure AD와 온-프레미스 AD 간에 Single Sign-On 사용
조직이 클라우드에서 Azure Active Directory와 함께 Windows Server Active Directory 온-프레미스를 유지하는 경우 두 시스템 간에 Single Sign-On을 사용하려고 할 가능성이 있습니다. Azure AD Connect(이 두 시스템을 함께 통합하는 도구)는 Single Sign-On을 설정하기 위한 여러 옵션을 제공합니다. ADFS나 다른 페더레이션 공급자를 사용하여 페더레이션을 설정하거나 암호 동기화를 사용합니다.

| 문서 가이드 |  |
|:---:| --- |
| 하이브리드 환경 관리에 대한 정보 및 Azure AD Connect에 제공되는 Single Sign-On 옵션에 대한 개요 |[Azure AD Connect의 사용자 로그온 옵션](active-directory-aadconnect-user-signin.md) |
| 온-프레미스 Active Directory 및 Azure Active Directory로 환경을 관리하기 위한 일반적인 지침  |[Azure AD 하이브리드 ID 설계 고려 사항](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md) |
| 암호 동기화를 사용하여 SSO를 사용하도록 설정하는 방법에 대한 참고 자료 |[Azure AD Connect와 암호 동기화 구현](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[암호 동기화 문제 해결](https://support.microsoft.com/en-us/kb/2855271) |
| 비밀번호 쓰기 저장을 사용하여 SSO를 사용하도록 설정하는 방법에 대한 참고 자료 |[Azure AD에서 암호 관리 시작](authentication/quickstart-sspr.md)<br /><br />[비밀번호 쓰기 저장 문제 해결](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 타사 ID 공급자를 사용하여 SSO를 사용하도록 설정하는 방법에 대한 참고 자료 |[Single Sign-On을 사용하도록 설정하는 데 사용할 수 있는 호환 가능한 타사 ID 공급자 목록](https://aka.ms/ssoproviders) |
| Windows 10 사용자가 Azure AD 조인을 통해 Single Sign-On의 이점을 얻을 수 있는 방법 |[Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md) |

Azure AD Connect는 [모든 버전의 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)에 사용할 수 있습니다. Azure AD 셀프 서비스 암호 재설정은 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 및 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)에 사용할 수 있습니다. 온-프레미스 AD에 대한 비밀번호 쓰기 저장은 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 기능입니다.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>조건부 액세스: 위험도 높은 앱에 대한 추가 보안 요구 사항 적용
앱 및 리소스에 Single Sign-On을 설정하면 앱에 대한 모든 로그인에서 특정 보안 요구 사항을 적용하여 중요한 응용 프로그램을 보호할 수 있습니다. 예를 들어 Azure AD를 사용하여 원래 앱이 해당 기능을 지원하는지 여부에 관계 없이 특정 앱에 대한 모든 액세스가 항상 Multi-Factor Authentication을 요구하도록 할 수 있습니다. 조건부 액세스의 또 다른 일반적인 예는 특히 중요한 응용 프로그램에 액세스하기 위해 사용자가 조직의 신뢰할 수 있는 네트워크에 연결하도록 요구하는 점입니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure AD, Office365 및 Intune에 제공되는 조건부 액세스 기능 소개 |[조건부 액세스를 사용한 위험 관리](active-directory-conditional-access-azure-portal.md) |
| 다음 형식의 리소스에 조건부 액세스를 사용하는 방법 |[SaaS 앱에 대한 조건부 액세스](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Office 365 서비스에 대한 조건부 액세스](active-directory-conditional-access-device-policies.md)<br /><br />[온-프레미스 응용 프로그램에 대한 조건부 액세스](active-directory-conditional-access-azure-portal.md)<br /><br />[Azure AD 앱 프록시를 통해 게시된 온-프레미스 응용 프로그램에 대한 조건부 액세스](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| 장치 기반 조건부 액세스 정책을 사용하기 위해 Azure Active Directory로 장치를 등록하는 방법 |[Azure Active Directory 장치 등록 개요](active-directory-conditional-access-device-registration-overview.md)<br /><br />[도메인에 가입된 Windows 장치에 대한 자동 장치 등록을 사용하도록 설정하는 방법](active-directory-conditional-access-automatic-device-registration.md)<br />- [Windows 8.1 장치에 대한 단계](active-directory-conditional-access-automatic-device-registration-setup.md)<br />- [Windows 7 장치에 대한 단계](active-directory-conditional-access-automatic-device-registration-setup.md) |

| 2단계 확인에 Microsoft Authenticator 앱을 사용하는 방법 |[Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

조건부 액세스는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 기능입니다.

## <a name="apps--azure-ad"></a>앱 및 Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>클라우드 앱 검색: 조직에서 사용된 SaaS 앱 찾기
Cloud App Discovery는 IT 부서가 조직 전체에서 사용되고 있는 SaaS 앱을 배우는 데 도움이 됩니다. 앱 사용 및 인기를 측정할 수 있으므로 IT 부서는 IT 제어로 가져오고 Azure AD와 통합되는 과정에서 가장 이득을 얻을 응용 프로그램을 결정할 수 있습니다.

| 문서 가이드 |  |
|:---:| --- |
| 작동 방법의 일반적인 개요 |[클라우드 앱 검색을 사용하여 허용되지 않은 클라우드 응용 프로그램 찾기](manage-apps/cloud-app-discovery.md) |
| 개인 정보 취급 방침의 질문에 대한 답을 사용하여 작동 원리 자세히 알아보기 |[보안 및 개인정보 취급 방침 고려 사항](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| 질문과 대답 |[클라우드 앱 검색에 대한 FAQ](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Cloud App Discovery를 배포하기 위한 자습서 |[그룹 정책 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[System Center 배포 가이드](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[사용자 지정 포트로 프록시 서버에 설치](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| 클라우드 앱 검색 에이전트에 대한 업데이트의 변경 로그 |[변경 로그](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 기능입니다.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>SaaS 앱에서 사용자 계정 자동으로 프로비전 및 프로비전 해제
Dropbox, Salesforce, ServiceNow 등과 같은 SaaS 응용 프로그램의 사용자 ID 생성, 관리 및 제거를 자동화합니다. Azure AD와 SaaS 앱 간의 기존 ID를 일치시키고 동기화하며 사용자가 조직을 떠날 때 계정을 자동으로 비활성화하여 액세스를 제어합니다.

| 문서 가이드 |  |
|:---:| --- |
| 작동 방법에 대해 알아보고 일반적인 질문에 대한 답 찾기 |[SaaS 앱에 자동화된 사용자 프로비전 및 프로비전 해제](active-directory-saas-app-provisioning.md) |
| Azure AD와 SaaS 앱 간에 매핑되는 정보 방식 구성 |[특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)<br><br>[특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| SCIM 프로토콜을 지원하는 앱에 자동화된 프로비전을 사용하는 방법 |[SCIM 사용한 앱에 자동화된 사용자 프로비전 설정](active-directory-scim-provisioning.md) |
| 사용자 프로비전에 대해 보고하고 문제를 해결하는 방법 |[자동 사용자 프로비전 보고](active-directory-saas-provisioning-reporting.md)<br><br>[사용자 프로비전 문제 해결](active-directory-application-provisioning-content-map.md) |
| 특성 값에 따라 응용 프로그램에 프로비전된 제한 |[범위 지정 필터](active-directory-saas-scoping-filters.md) |

자동화된 사용자 프로비전은 사용자 당 최대 열 개의 앱에서 Azure AD의 모든 버전에 사용할 수 있습니다. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 은 응용 프로그램을 무제한 지원합니다. 조직에 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 또는 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)이 있는 경우 [그룹을 사용하여 프로비전되는 사용자를 관리](#managing-access-to-applications)할 수 있습니다.

### <a name="building-applications-that-integrate-with-azure-ad"></a>Azure AD와 통합되는 응용 프로그램 작성
조직이 기간 업무(LoB) 응용 프로그램 개발 또는 유지 관리하거나 Azure Active Directory를 사용하는 고객이 있는 개발자라면 다음 자습서는 Azure AD와 응용 프로그램을 통합하는 데 도움이 됩니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure AD와 앱 통합에 대한 IT 전문가 및 응용 프로그램 개발자를 위한 지침 |[Azure AD용 응용 프로그램 개발을 위한 IT 전문가 가이드](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Azure Active Directory에 대한 개발자의 가이드](active-directory-developers-guide.md) |
| 응용 프로그램이 Azure AD 앱 갤러리에 공급 업체를 추가할 수 있는 방법 |[Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열](active-directory-app-gallery-listing.md) |
| Azure Active Directory를 사용하여 개발된 응용 프로그램에 대한 액세스를 관리하는 방법 |[개발된 응용 프로그램에 대한 사용자 할당을 사용하도록 설정하는 방법](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[앱에 사용자 할당](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[앱에 그룹 할당](active-directory-applications-guiding-developers-assigning-groups.md) |

소비자 지향 응용 프로그램을 개발하는 경우 [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 를 사용하는 데 관심이 있을 수 있으므로 고유의 ID 시스템을 개발하여 사용자를 관리하지 않아도 됩니다. [자세히 알아봅니다](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>응용 프로그램에 대한 액세스 관리
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>그룹 및 셀프 서비스를 사용하여 앱에 대한 액세스 권한이 있는 사용자 관리
리소스에 액세스해야 하는 사용자를 관리하려는 경우 Azure Active Directory를 사용하면 그룹을 사용하여 할당 및 사용 권한을 설정할 수 있습니다. IT는 셀프 서비스 기능을 사용하도록 선택하여 필요할 때 사용자가 권한을 요청할 수 있도록 할 수 있습니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure AD 액세스 관리 기능의 개요 |[앱에 대한 액세스 관리 소개](active-directory-managing-access-to-apps.md)<br /><br />[Azure AD에서 액세스 관리가 작동하는 방식](active-directory-manage-groups.md)<br /><br />[그룹을 사용하여 SaaS 응용 프로그램에 대한 액세스를 관리하는 방법](active-directory-accessmanagement-group-saasapps.md) |
| 앱 및 그룹의 셀프 서비스 관리 사용 |[셀프 서비스 응용 프로그램 관리](active-directory-self-service-application-access.md)<br /><br />[셀프 서비스 그룹 관리](active-directory-accessmanagement-self-service-group-management.md) |
| Azure AD에서 그룹을 설정하기 위한 지침 |[보안 그룹을 만드는 방법](active-directory-groups-create-azure-portal.md)<br /><br />[그룹의 소유자를 지정하는 방법](active-directory-accessmanagement-managing-group-owners.md)<br /><br />["모든 사용자" 그룹을 사용하는 방법](active-directory-accessmanagement-dedicated-groups.md) |
| 동적 그룹을 사용하여 자동으로 특성 기반 멤버 자격 규칙을 사용하는 그룹 멤버 자격 채우기 |[동적 그룹 구성원: 고급 규칙](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[동적 그룹 멤버 자격 문제 해결](active-directory-accessmanagement-troubleshooting.md) |

그룹 기반 응용 프로그램 액세스 관리는 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 및 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)에 사용할 수 있습니다. 셀프 서비스 그룹 관리, 셀프 서비스 응용 프로그램 관리 및 동적 그룹은 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 기능입니다.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B 공동 작업: 응용 프로그램에 대한 파트너 액세스 사용
비즈니스가 다른 회사와 협력한 경우 회사 응용 프로그램에 대한 파트너 액세스를 관리해야 할 가능성이 있습니다. Azure Active Directory B2B 공동 작업은 파트너와 앱을 공유하는 쉽고 안전한 방법을 제공합니다.

| 문서 가이드 |  |
|:---:| --- |
| 다른 Azure AD의 개요는 파트너, 고객 등 외부 사용자를 관리할 수 있는 기능을 갖추고 있습니다. |[Azure AD에서 외부 ID 관리 기능 비교](active-directory-b2b-compare-external-identities.md) |
| B2B 공동 작업 및 시작하는 방법에 대한 소개 |[Azure AD와 간단하고 안전한 클라우드 통합](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B 공동 작업](active-directory-b2b-collaboration-overview.md) |
| Azure AD B2B 공동 작업 및 사용하는 방법 자세히 알아보기 |[B2B 공동 작업: 작동 방식](active-directory-b2b-how-it-works.md)<br /><br />[Azure AD B2B 공동 작업의 현재 제한 사항](active-directory-b2b-current-limitations.md)<br /><br />[Azure AD B2B 공동 작업 사용에 대한 자세한 연습](active-directory-b2b-detailed-walkthrough.md) |
| Azure AD B2B 공동 작업의 작동 방법에 대한 기술 세부 정보가 포함된 참조 문서 |[파트너 사용자를 추가하기 위한 CSV 파일 형식](active-directory-b2b-references-csv-file-format.md)<br /><br />[Azure AD B2B 공동 작업의 영향을 받는 사용자 특성](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[파트너 사용자에 대한 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md) |

B2B 공동 작업은 현재 [모든 Azure Active Directory 버전](https://azure.microsoft.com/pricing/details/active-directory/)에 사용할 수 있습니다.

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>액세스 패널: 앱 및 셀프 서비스 기능에 액세스하기 위한 포털
Azure AD 액세스 패널은 최종 사용자가 자신의 앱을 시작하고 해당 앱 및 그룹 멤버 자격을 관리할 수 있도록 하는 셀프 서비스 기능에 액세스하는 위치에 있습니다. 액세스 패널 외에도 SSO를 사용한 앱에 액세스하기 위한 다른 옵션이 아래 목록에 포함됩니다.

| 문서 가이드 |  |
|:---:| --- |
| 사용자에게 Single Sign-On 앱을 배포하는 데 사용할 수 있는 다양한 옵션의 비교 |[사용자에게 Azure AD 통합 응용 프로그램 배포](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| MyApps에 해당하는 액세스 패널 및 해당 모바일의 개요 |[액세스 패널 및 MyApps 소개](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Office 365 웹 사이트에서 Azure AD 앱에 액세스하는 방법 |[Office 365 앱 시작 관리자 사용](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Intune 관리된 브라우저 모바일 앱에서 Azure AD 앱에 액세스하는 방법 |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| 딥 링크를 사용하여 Single Sign-On을 시작하도록 Azure AD 앱에 액세스하는 방법 |[앱에 직접 로그온 링크 가져오기](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

액세스 패널은 [모든 버전의 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)에 사용할 수 있습니다.

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>보고서: 쉽게 앱 액세스 변경 감사 및 앱에 대한 로그인을 모니터링
Azure Active Directory는 여러 보고서 및 경고를 제공하여 응용 프로그램에 대한 조직의 액세스를 모니터링합니다. 앱에 대한 비정상적인 로그인이 발생했다는 경고를 받을 수 있고 응용 프로그램에 대한 사용자의 액세스 변경된 시기 및 이유를 추적할 수 있습니다.

| 문서 가이드 |  |
|:---:| --- |
| Azure Active Directory의 보고 기능 개요 |[Azure AD Reporting 시작](active-directory-reporting-getting-started.md) |
| 사용자의 로그인 및 앱 사용을 모니터링하는 방법 |[액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md) |
| 특정 응용 프로그램에 액세스할 수 있는 사용자에 대한 변경 내용 추적 |[Azure Active Directory 감사 보고서 이벤트](active-directory-reporting-audit-events.md) |
| 보고 API를 사용하여 원하는 도구에 이러한 보고서의 데이터 내보내기 |[Azure AD Reporting API 시작하기](active-directory-reporting-api-getting-started.md) |

Azure Active Directory의 다른 버전에 포함된 보고서를 보려면 [여기를 클릭](active-directory-view-access-usage-reports.md)합니다.

## <a name="see-also"></a>참고 항목
[Azure Active Directory란?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
