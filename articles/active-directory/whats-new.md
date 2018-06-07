---
title: 새로운 기능 Azure Active Directory 릴리스 정보 | Microsoft Docs
description: Azure AD(Azure Active directory)의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용 등을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b7ad535976508cb195991c374995b0a0b6e45e10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159871"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능


> [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [피드](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)를 구독하여 Azure AD(Azure Active Directory)의 새로운 기능에 대한 최신 정보를 얻으세요.



Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

-   최신 릴리스
-   알려진 문제
-   버그 수정
-   사용되지 않는 기능
-   변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요.


## <a name="april-2018"></a>2018년 4월
 


### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C 액세스 토큰이 GA 상태입니다.

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 

이제 액세스 토큰을 사용하여 Azure AD B2C로 보호된 Web API에 액세스할 수 있습니다. 기능은 공개 미리 보기에서 GA로 전환되고 있습니다. Azure AD B2C 응용 프로그램 및 웹 API를 구성하는 UI 환경이 향상되었고 기타 사소한 개선 사항이 만들어졌습니다.
 
자세한 내용은 [Azure AD B2C: 액세스 토큰 요청](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)을 참조하세요.


---
 

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML 기반 응용 프로그램에 대한 Single Sign-On 구성 테스트

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 

SAML 기반 SSO 응용 프로그램을 구성하는 경우 구성 페이지에서 통합을 테스트할 수 있습니다. 로그인하는 동안 오류가 발생하면 테스트 환경에서 오류를 제공할 수 있으며 Azure AD는 특정 문제를 해결하는 해결 단계를 제공합니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)


---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>이제 Azure AD 사용 약관에는 사용자별 보고가 적용됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수
 

이제 관리자는 지정된 ToU를 선택하고 해당 ToU에 동의한 사용자 및 발생한 날짜/시간을 확인할 수 있습니다.


자세한 내용은 [Azure AD 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.
 

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS 엑스트라넷 잠금 보호를 위한 위험한 IP 

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 모니터링 및 보고
 

Connect Health는 이제 매시간 또는 매일 별로 실패한 U/P 로그인의 임계값을 초과하는 IP 주소를 검색하는 기능을 지원합니다. 이 기능에서 제공하는 기능은 다음과 같습니다.

- IP 주소 및 사용자 지정 가능한 임계값으로 매시간/매일 별로 생성된 로그인 실패 횟수를 보여주는 포괄적인 보고서
- 매시간/매일 별로 특정 IP 주소가 실패한 U/P 로그인의 임계값을 초과한 경우를 보여주는 이메일 기반 경고
- 데이터의 상세한 분석을 수행하는 다운로드 옵션


자세한 내용은 [위험한 IP 보고서](https://aka.ms/aadchriskyip)를 참조하세요.

 

---
 

### <a name="easy-app-config-with-metadata-file-or-url"></a>메타데이터 파일 또는 URL을 사용한 간편한 앱 구성

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 

Enterprise 응용 프로그램 페이지에서 관리자는 SAML 메타데이터 파일을 업로드하여 AAD 갤러리 및 비 갤러리 응용 프로그램에 대해 SAML 기반 로그온을 구성할 수 있습니다.

또한 Azure AD 응용 프로그램 페더레이션 메타데이터 URL을 사용하여 대상 응용 프로그램으로 SSO를 구성할 수 있습니다.

자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)을 참조하세요.
 

---
 

### <a name="azure-ad-terms-of-use-now-generally-available"></a>이제 Azure AD 사용 약관을 일반적으로 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수
 

Azure AD 사용 약관은 공개 미리 보기에서 일반 공급으로 이동했습니다.

자세한 내용은 [Azure AD 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.

 

---
 

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>특정 조직의 B2B 사용자 초대 허용 또는 차단

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

이제 Azure AD B2B 공동 작업에서 공유하고 공동 작업하려는 파트너 조직을 지정할 수 있습니다. 이를 위해 특정 허용 또는 거부 도메인의 목록을 만들도록 선택할 수 있습니다. 이러한 기능을 사용하여 도메인이 차단되면 직원은 해당 도메인의 사용자에게 더 이상 초대를 보낼 수 없습니다.

이렇게 하면 승인된 사용자를 위해 부드러운 환경을 활성화하는 동안 리소스에 대한 액세스를 제어할 수 있습니다.

이 B2B 공동 작업 기능은 모든 Azure Active Directory 고객에 대해 사용할 수 있으며 외부 비즈니스 사용자가 로그인하고 액세스를 획득하는 시기 및 방법의 더욱 세부적인 제어를 위해 조건부 액세스 및 ID 보호와 같은 Azure AD Premium 기능과 함께 사용할 수 있습니다.

자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)을 참조하세요.

 

---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에서 사용할 수 있는 신규 페더레이션 앱

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 

2018년 4월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 13개의 새 앱을 추가했습니다.



조건 HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [보안 서버(온-프레미스)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [동적 신호](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta 성능 모니터](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)



 여기에서 사용 가능한 자습서의 목록을 찾을 수 있습니다. [https://aka.ms/appstutorial](https://aka.ms/appstutorial)

자세한 내용은 [Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램을 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)을 참조하세요.


 

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Azure AD의 B2B 사용자에게 온-프레미스 응용 프로그램에 대한 액세스 권한 부여(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

Azure AD(Azure Active Directory) B2B 공동 작업 기능을 사용하여 파트너 조직의 게스트 사용자를 Azure AD로 초대하는 조직의 경우 이제 이러한 B2B 사용자에게 온-프레미스 앱에 대한 액세스를 제공할 수 있습니다. 이러한 온-프레미스 앱은 SAML 기반 인증 또는 KCD(Kerberos 제한 위임)과 함께 IWA(Windows 통합 인증)를 사용할 수 있습니다.

자세한 내용은 [Azure AD의 B2B 사용자에게 온-프레미스 응용 프로그램에 대한 액세스 권한 부여](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)를 참조하세요.
 

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Marketplace에서 SSO 통합 자습서 가져오기

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능**: 타사 통합
 

[Azure 마켓플레이스](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)에 나열된 응용 프로그램이 SAML 기반 Single Sign-On을 지원하는 경우 **지금 사용해 보세요**를 클릭하면 해당 응용 프로그램과 관련된 통합 자습서를 제공합니다. 


---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>더 빨라진 SaaS 응용 프로그램에 대한 Azure AD 자동 사용자 프로비저닝 성능

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 

이전에 SaaS 응용 프로그램용 Azure Active Directory 사용자 프로비저닝 커넥터(예: Salesforce, ServiceNow 및 Box)를 사용하는 고객은 해당 Azure AD 테넌트가 100,000 개 이상의 결합된 사용자 및 그룹을 포함한 경우 매우 느린 성능을 경험할 수 있었으며, 프로비전할 사용자를 결정하기 위해 사용자 및 그룹 할당을 사용했습니다.

4월 2일에 Azure Active Directory와 대상 SaaS 응용 프로그램 간의 초기 동기화를 수행하는 데 필요한 시간을 크게 줄이는 매우 중요한 성능 향상이 Azure AD 프로비저닝 서비스에 배포되었습니다.


따라서 앱에 대한 초기 동기화를 수행하는 데 수 일이 걸리거나 완료되지 않았던 많은 고객은 이제 몇 분 또는 몇 시간 내에 이를 완료합니다.

자세한 내용은 [프로비전하는 동안 어떻게 됩니까?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)를 참조하세요.

---
 

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>하이브리드 Azure AD 가입 컴퓨터에 대해 Windows 10 잠금 화면에서 셀프 서비스 암호 재설정

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증
 

하이브리드 Azure AD에 가입된 컴퓨터에 대한 지원을 포함하도록 Windows 10 SSPR 기능을 업데이트했습니다. 이 기능은 사용자가 Windows 10 컴퓨터의 잠금 화면에서 암호를 재설정하도록 허용하는 Windows 10 RS4에서 사용할 수 있습니다. 셀프 서비스 암호 재설정에 대해 활성화되거나 등록된 사용자는 이 기능을 활용할 수 있습니다.

자세한 내용은 [로그인 화면에서 Azure AD 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)을 참조하세요.
 

---



## <a name="march-2018"></a>2018년 3월
 

### <a name="certificate-expire-notification"></a>인증서 만료 알림

**유형:** 고정  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
Azure AD는 갤러리 및 비갤러리 응용 프로그램에 대한 인증서가 만료되려고 할 때 알림을 전송합니다. 

일부 사용자에게 SAML 기반 Single Sign On에 대해 구성된 엔터프라이즈 응용 프로그램에 대한 알림이 수신되지 않았습니다. 이 문제가 해결되었습니다. Azure AD는 7, 30 및 60일 후에 만료되는 인증서에 대한 알림을 보냅니다. 감사 로그에서 이 이벤트를 볼 수 있습니다. 

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Azure Active Directory 포털의 감사 활동 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C의 Twitter 및 GitHub ID 공급자

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2C에서 ID 공급자로 Twitter 또는 GitHub를 추가할 수 있습니다. Twitter는 공개 미리 보기에서 GA로 전환되고 있습니다. GitHub는 공개 미리 보기로 릴리스되고 있습니다.


자세한 내용은 [Azure AD B2B 공동 작업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 참조하세요.
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>iOS 및 Android용 Azure AD 응용 프로그램 기반 조건부 액세스와 함께 Intune Managed Browser를 사용하여 브라우저 액세스를 제한합니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 

**현재 공개 미리 보기 상태입니다.**

**Intune Managed Browser SSO:** 직원은 모든 Azure AD 연결 앱에 대한 기본 클라이언트(예: Microsoft Outlook)와 Intune Managed Browser에서 Single Sign-On을 사용할 수 있습니다.

**Intune Managed Browser 조건부 액세스 지원:** 이제 직원이 응용 프로그램 기반 조건부 액세스 정책을 사용하여 Intune Managed Browser를 사용하도록 할 수 있습니다.

[블로그 게시물](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)에서 이에 대해 자세히 읽어보세요.

자세한 내용은 다음을 참조하세요.

- [응용 프로그램 기반 조건부 액세스 설정](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Managed Browser 정책 구성](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Powershell GA 모듈의 앱 프록시 Cmdlet

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어
 
응용 프로그램 프록시 cmdlet에 대한 지원이 이제 Powershell GA 모듈에 포함되었습니다. 따라서 Powershell 모듈을 최신 상태로 유지해야 합니다. 버전이 1년보다 더 늦으면 일부 cmdlet이 작동하지 않을 수 있습니다. 


자세한 내용은 [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)를 참조하세요.
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 네이티브 클라이언트는 비대화형 프로토콜을 사용하여 Seamless SSO에서 지원됨

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Office 365 네이티브 클라이언트(버전 16.0.8730.xxxx 이상)를 사용하는 사용자는 Seamless SSO를 통해 자동 로그온 환경을 사용할 수 있게 됩니다. 이러한 지원은 비대화형 프로토콜(WS-Trust)을 Azure AD에 추가하여 제공됩니다.

자세한 내용은 [네이티브 클라이언트에서 Seamless SSO로 로그인하는 방식](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)을 참조하세요.

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>응용 프로그램이 Azure AD의 테넌트 끝점으로 로그인 요청을 전송할 경우 Seamless SSO를 사용하여 자동 로그온 환경을 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
응용 프로그램(예: `https://contoso.sharepoint.com`)이 Azure AD의 공통 끝점(`https://login.microsoftonline.com/common/<...>`) 대신 Azure AD의 테넌트 끝점(즉, `https://login.microsoftonline.com/contoso.com/<..>` 또는 `https://login.microsoftonline.com/<tenant_ID>/<..>`)으로 로그인 요청을 전송할 경우 사용자는 Seamless SSO를 사용하여 자동 로그온 환경을 사용할 수 있습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요. 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Seamless SSO를 롤아웃하기 위해 사용자의 인트라넷 영역 설정에 이전의 두 URL 대신 하나의 Azure AD URL만 추가해야 함

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
사용자에게 Seamless SSO를 롤아웃하려면 Active Directory의 그룹 정책을 사용하여 사용자의 인트라넷 영역 설정에 하나의 Azure AD URL만 추가해야합니다. `https://autologon.microsoftazuread-sso.com` 이전에는 고객이 두 개의 URL을 추가해야 했습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요. 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 있는 신규 페더레이션 앱

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2018년 3월에 앱 갤러리에 페더레이션 지원이 제공되는 다음과 같은 15개의 새 앱을 추가했습니다.

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
여기에서 모든 응용 프로그램에 대한 설명서를 찾을 수 있습니다. [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>Azure 리소스에 대한 PIM은 일반 공급됩니다.

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
디렉터리 역할에 대한 Azure AD Privileged Identity Management를 사용하는 경우, 이제 구독, 리소스 그룹, VIrtual Machines 등과 같은 Azure 리소스 역할 및 Azure Resource Manager에서 지원하는 기타 리소스에 대해 PIM의 시간 제한 액세스 및 할당 기능을 사용할 수 있습니다. Just-In-Time에 역할을 활성화할 경우 Multi-Factor Authentication을 적용하고 승인된 변경 기간에 맞춰 활성화를 예약합니다. 또한 이 릴리스에서는 업데이트 UI, 승인 워크플로 및 곧 만료되는 역할을 확장하고 만료된 역할을 갱신하는 기능을 비롯하여 공개 미리 보기 기간 동안 사용할 수 없는 개선 기능을 추가적으로 제공합니다.

자세한 내용은 [Azure 리소스에 대한 PIM(미리 보기)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)을 참조하세요.
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>앱 토큰에 선택적 클레임 추가(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이제 Azure AD 앱은 JWT 또는 SAML 토큰에서 사용자 지정 또는 선택적 클레임을 요청할 수 있습니다.  이러한 클레임은 크기 또는 적용 가능한 제약으로 인해 토큰에 기본적으로 포함되지 않는 사용자 또는 테넌트에 대한 클레임입니다.  현재는 v1.0 및 v2.0 끝점에서 Azure AD 앱에 대한 공개 미리 보기로 사용됩니다.  추가될 수 있는 클레임과 해당 클레임을 요청하도록 응용 프로그램 매니페스트를 편집하는 방법에 대한 내용은 해당 설명서를 참조하세요.  

자세한 내용은 [Azure AD의 선택적 클레임](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)을 참조하세요.
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD에서 보다 안전한 OAuth 흐름을 위해 PKCE를 지원함

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Azure AD 설명서가 OAuth 2.0 권한 부여 코드 부여 흐름 동안 좀 더 안전한 통신을 허용하는 PKCE에 대한 지원 내용을 포함하도록 업데이트되었습니다.  S256 및 일반 텍스트 code_challenges 둘 다 v1.0 및 v2.0 끝점에서 지원됩니다. 

자세한 내용은 권한 부여 코드 요청[](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)을 참조하세요. 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Workday Get_Workers API에서 사용할 수 있는 모든 사용자 특성 값을 프로비전하도록 지원

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능**: 타사 통합
 
Workday에서 Active Directory 및 Azure AD로의 인바운드 프로비전에 대한 공개 미리 보기는 이제 Workday Get_Workers API에서 사용할 수 있는 모든 특성 값을 추출하고 프로비전하는 기능을 지원합니다. 이를 통해 Workday 인바운드 프로비전 커넥터의 초기 버전에 제공된 특성 외에, 수백 개의 추가 표준 및 사용자 지정 특성이 추가적으로 지원됩니다.

자세한 내용은 [Workday 사용자 특성 목록 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)을 참조하세요.

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>그룹 멤버 자격을 동적에서 정적으로 또는 그 반대로 변경

**유형:** 새로운 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업
 
그룹에서 멤버 자격을 관리하는 방식을 변경할 수 있습니다. 이것은 시스템에 동일한 그룹 이름과 ID를 유지하려는 경우에 유용하므로 그룹에 대한 기존 참조는 여전히 유효합니다. 새 그룹을 만들면 해당 참조를 업데이트해야 합니다.
이 기능 지원을 추가하도록 Azure AD 관리 센터를 업데이트했습니다. 이제, 고객은 기존 그룹을 동적 멤버 자격에서 할당된 멤버 자격으로 또는 그 반대로 변환할 수 있습니다. 기존 PowerShell cmdlet도 계속 사용할 수 있습니다.

자세한 내용은 [동적 멤버 자격을 정적으로 또는 그 반대로 변경](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)을 참조하세요.

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Seamless SSO로 로그아웃 동작 개선

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
이전에는 사용자가 Azure AD로 보호되는 응용 프로그램에서 명시적으로 로그아웃하더라도 도메인에 가입된 장치에서 corpnet 내의 Azure AD 응용 프로그램에 다시 액세스하려고 하면 Seamless SSO를 사용하여 자동으로 다시 로그인되었습니다. 이러한 변경으로 인해 이제 로그아웃이 지원됩니다.  따라서 사용자는 Seamless SSO를 사용하여 자동으로 로그인되는 대신, 다시 로그인하는 데 사용할 같거나 다른 Azure AD 계정을 선택할 수 있습니다.

자세한 내용은 [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)을 참조하세요.

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>응용 프로그램 프록시 커넥터 버전 1.5.402.0이 릴리스됨

**유형:** 변경된 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** ID 보안 및 보호
 
이 커넥터 버전은 11월까지 점진적으로 롤아웃될 예정입니다. 이 새 커넥터 버전에는 다음과 같은 변경 내용이 포함되어 있습니다.

- 이제 커넥터는 하위 도메인 수준 대신, 도메인 수준 쿠키를 설정합니다. 이를 통해 SSO 환경이 좀 더 원활하게 진행되고 불필요한 인증 프롬프트가 방지됩니다.
- 청크 인코딩 요청 지원
- 향상된 커넥터 상태 모니터링 
- 몇 가지 버그 수정 및 안정성 개선

자세한 내용은 [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)를 참조하세요.

 
---
 

 



## <a name="february-2018"></a>2018년 2월
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>사용자 및 그룹 관리를 위한 향상된 탐색

**유형:** 변경 계획  
**서비스 범주:** 디렉터리 관리  
**제품 기능:** 디렉터리
 

사용자 및 그룹 관리를 위한 탐색 환경이 간소화되었습니다. 이제 디렉터리 개요에서 직접 모든 사용자 목록으로 이동하여 삭제된 사용자 목록에 더 쉽게 액세스할 수 있습니다. 디렉터리 개요에서 모든 그룹 목록으로 직접 이동하여 그룹 관리 설정에 쉽게 액세스할 수 있습니다. 또한 디렉터리 개요 페이지에서 사용자, 그룹, 엔터프라이즈 응용 프로그램 또는 앱 등록을 검색할 수 있습니다.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet(Azure China 21Vianet)에서 운영하는 Microsoft Azure의 로그인 및 감사 보고서에 대한 가용성

**유형:** 새로운 기능  
**서비스 범주:** 소버린 클라우드  
**제품 기능:** 모니터링 및 보고
 

Azure AD 활동 로그 보고서는 이제 21Vianet(Azure China 21Vianet) 인스턴스로 운영되는 Microsoft Azure에서 사용할 수 있습니다. 포함되는 로그는 다음과 같습니다.

- **로그인 활동 로그** - 테넌트와 관련된 모든 로그인 로그를 포함합니다.

- **셀프 서비스 암호 감사 로그** - 모든 SSPR 감사 로그를 포함합니다.

- **디렉터리 관리 감사 로그** - 사용자 관리, 앱 관리 등과 같은 디렉터리 관리와 관련된 모든 감사 로그를 포함합니다.

이러한 로그를 사용하면 환경이 작동하는 방식에 대한 통찰력을 얻을 수 있습니다. 제공된 데이터를 통해 다음을 수행할 수 있습니다.

- 사용자가 앱과 서비스를 활용하는 방식을 결정합니다.

- 사용자가 자신의 작업을 완료하지 못하게 하는 문제를 해결합니다.

이러한 보고서를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)를 참조하세요.
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"보고서 구독자" 역할(관리자가 아닌 역할)을 사용하여 Azure AD 활동 보고서 보기

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 

관리자가 아닌 역할을 통해 Azure AD 활동 로그에 액세스할 수 있도록 하는 고객 의견의 일환으로, "보고서 구독자" 역할에 속한 사용자가 Azure Portal 내에서 로그인 및 감사 활동에 액세스하고 Graph API를 사용할 수 있게 되었습니다. 

이러한 보고서를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)를 참조하세요. 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID 클레임을 사용자 특성 및 사용자 식별자로 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 

엔터프라이즈 응용 프로그램 UI의 SAML 기반 로그온 응용 프로그램에서 **EmployeeID**를 멤버 사용자 및 B2B 게스트에 대한 사용자 식별자 및 사용자 특성으로 구성할 수 있습니다.

자세한 내용은 [Azure Active Directory의 엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에서 와일드카드를 사용하여 간소화된 응용 프로그램 관리

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 사용자 인증
 

응용 프로그램을 더 쉽게 배포하고 관리 오버헤드를 줄이기 위해 이제 와일드카드를 사용하여 응용 프로그램을 게시하는 기능을 지원합니다. 와일드카드 응용 프로그램을 게시하려면 표준 응용 프로그램 게시 흐름을 따르지만 내부 및 외부 URL에 와일드카드를 사용할 수 있습니다.

자세한 내용은 [Azure Active Directory 응용 프로그램 프록시의 와일드카드 응용 프로그램](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)을 참조하세요.

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>응용 프로그램 프록시 구성을 지원하는 새로운 cmdlet

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 플랫폼
 

AzureAD PowerShell 미리 보기 모듈의 최신 릴리스에는 고객이 PowerShell을 사용하여 응용 프로그램 프록시 응용 프로그램을 구성할 수 있는 새로운 cmdlet이 포함되어 있습니다.

새로운 cmdlet은 다음과 같습니다. 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>그룹 구성을 지원하는 새로운 cmdlet

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 플랫폼
 

AzureAD PowerShell 모듈의 최신 릴리스에는 Azure AD에서 그룹을 관리하는 cmdlet이 포함되어 있습니다. 이러한 cmdlet은 이전에 AzureADPreview 모듈에서 사용할 수 있었지만, 이제는 AzureAD 모듈에 추가되었습니다

현재 일반 공급으로 릴리스된 Group cmdlet은 다음과 같습니다. 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect의 새 릴리스를 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼
 

Azure AD Connect는 Windows Server Active Directory 및 LDAP를 포함하여 Azure AD와 온-프레미스 데이터 원본 간에 데이터를 동기화하는 기본 설정 도구입니다.

**중요**
 
이 빌드는 스키마 및 동기화 규칙 변경 내용을 도입했습니다. Azure AD Connect 동기화 서비스는 업그레이드 후에 전체 가져오기 및 전체 동기화 단계를 트리거합니다. 이 동작을 변경하는 방법에 대한 자세한 내용은 [업그레이드 후 전체 동기화를 연기하는 방법](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)을 참조하세요.

이 릴리스에 포함된 업데이트 및 변경 내용은 다음과 같습니다.

**수정된 문제**

- 다음 페이지로 전환할 때 파티션 필터링 페이지에 대한 백그라운드 작업에 대한 시간 범위가 수정되었습니다.
- ConfigDB 사용자 지정 작업을 수행하는 동안 액세스 위반이 발생하는 버그가 수정되었습니다.
- SQL 연결 시간 제한에서 복구하는 버그가 수정되었습니다.
- SAN 와일드카드가 있는 인증서가 필수 구성 요소 검사에 실패하는 버그가 수정되었습니다.
- AAD 커넥터를 내보내는 동안 miiserver.exe가 충돌하는 버그가 수정되었습니다.
- AAD Connect 마법사를 실행하여 구성을 변경할 때 DC에 잘못된 암호 시도가 기록되는 버그가 수정되었습니다.

**새 기능 및 향상된 기능**

- GDPR의 경우 Microsoft와 공유하는 고객 데이터(원격 분석, 상태 등)의 종류를 나타내고, 자세한 온라인 설명서에 대한 링크를 제공하며, 기본 설정을 변경할 수 있는 방법을 제공해야 합니다.  이 체크 인은 다음을 추가합니다.
    - 새로 설치 EULA 페이지에 데이터 공유 및 개인 정보 알림.

    - 업그레이드 페이지에 데이터 공유 및 개인 정보 알림.

    - 사용자가 기본 설정을 변경할 수 있도록 새로 추가된 **개인 정보 설정** 작업
 
- 응용 프로그램 원격 분석 - 관리자가 이 데이터 클래스를 설정/해제할 수 있습니다.

- Azure AD 상태 데이터 - 관리자가 상태 포털을 방문하여 상태 설정을 제어해야 합니다. 서비스 정책이 변경되고 나면 에이전트에서 읽고 적용합니다.

- 장치 쓰기 저장 구성 작업 및 페이지 초기화에 대한 진행률 표시줄이 추가되었습니다.

- ZIP 텍스트/HTML 보고서에서 HTML 보고서 및 전체 데이터 수집을 통해 일반 진단 기능이 향상되었습니다.

- 자동 업그레이드의 안정성이 향상되고, 서버의 상태를 확인할 수 있도록 추가 원격 분석이 추가되었습니다.

- AD 커넥터 계정에서 권한 있는 계정에 사용할 수 있는 권한이 제한되었습니다. 새로 설치하는 경우 마법사는 MSOL 계정을 만든 후 MSOL 계정에 있는 권한 있는 계정이 갖는 권한을 제한합니다. 이 변경 내용은 자동 생성 계정을 통한 빠른 설치 및 사용자 지정 설치에 영향을 줍니다.

- AAD Connect를 새로 설치할 때 SA 권한이 필요하지 않도록 설치 관리자가 변경되었습니다.

- 특정 개체에 대한 동기화 문제를 해결하는 새로운 유틸리티가 추가되었습니다. 현재 유틸리티는 다음 사항을 확인합니다.

    - 동기화된 사용자 개체와 Azure AD 테넌트의 사용자 계정 간의 UserPrincipalName이 일치하지 않습니다.
  
    - 도메인 필터링으로 인해 개체가 동기화에서 필터링된 경우
  
    - OU(조직 구성 단위) 필터링으로 인해 개체가 동기화에서 필터링된 경우

- 특정 사용자 계정에 대해 온-프레미스 Active Directory에 저장된 현재 암호 해시를 동기화하는 새로운 유틸리티가 추가되었습니다. 유틸리티는 암호 변경이 필요하지 않습니다. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD 응용 프로그램 기반 조건부 액세스에서 사용하기 위해 추가된 Intune 앱 보호 정책을 지원하는 응용 프로그램

**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호
 

응용 프로그램 기반 조건부 액세스를 지원하는 응용 프로그램을 더 많이 추가했습니다. 이제 승인된 이러한 클라이언트 응용 프로그램을 사용하여 Office 365 및 기타 Azure AD 연결 클라우드 응용 프로그램에 액세스할 수 있습니다.

2월 말까지 추가될 응용 프로그램은 다음과 같습니다. 

- Microsoft PowerBI

- Microsoft Launcher

- Microsoft Invoicing

자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>모바일 환경에 대한 사용 약관 업데이트 

**유형:** 변경된 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수
 

사용 약관이 표시되면 **보는 데 문제가 있나요? 여기를 클릭하세요.** 를 클릭할 수 있습니다. 이 링크를 클릭하면 장치에서 기본적으로 사용 약관이 열립니다. 문서의 글꼴 크기 또는 장치의 화면 크기에 관계없이 필요에 따라 문서를 확대/축소하고 읽을 수 있습니다. 
 

---
 
## <a name="january-2018"></a>2018년 1월
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 있는 신규 페더레이션 앱 

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 

2018년 1월에, 페더레이션 지원을 포함하는 새 앱이 앱 갤러리에 추가되었습니다.

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) 및 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

사용 가능한 모든 자습서에 대한 개요는 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>추가 위험이 있는 로그인이 감지됨

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호
 

감지된 위험 이벤트에 대해 얻은 정보는 Azure AD 구독에 연결됩니다. Azure AD Premium P2 버전에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다.

Azure AD Premium P1 버전에서 라이선스가 적용되지 않는 검색 항목이 ‘추가 위험이 있는 로그인이 감지됨’이라는 위험 이벤트로 표시됩니다.

자세한 내용은 [Azure Active Directory 위험 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)를 참조하세요.
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>최종 사용자의 액세스 패널에서 Office 365 응용 프로그램 숨기기

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 

새로운 사용자 설정을 통해 Office 365 응용 프로그램을 사용자의 액세스 패널에 표시하는 방법을 보다 효과적으로 관리할 수 있습니다. Office 포털에 Office 응용 프로그램만 표시하려는 경우, 이 옵션은 사용자의 액세스 패널에 있는 앱을 줄이는 데 유용합니다. 이 설정은 **사용자 설정** 아래에 있으며 **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음**이라는 레이블이 지정됩니다.
 

자세한 내용은 [Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)를 참조하세요.

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>앱의 URL을 통해 암호 SSO가 가능한 앱에 간편하게 로그인 

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** SSO
 

내 앱 SSO(Single-Sign On) 기능을 브라우저의 바로 가기로 제공하는 간편한 도구를 통해 내 앱 브라우저 확장을 사용할 수 있습니다. 사용자의 앱을 설치하면 앱에 빠른 액세스를 제공하는 와플 아이콘이 브라우저에 표시됩니다. 사용자는 이제 다음과 같은 이점을 활용할 수 있습니다.

- 앱의 로그인 페이지에서 암호-SSO 기반 앱에 직접 로그인할 수 있는 기능
- 빠른 검색 기능을 사용하여 원하는 앱 실행
- 확장 프로그램의 최근 사용한 앱에 대한 바로 가기
- 확장 프로그램은 Edge, Chrome 및 Firefox에서 사용할 수 있습니다.
 
자세한 내용은 [내 앱 보안 로그인 확장](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension)을 참조하세요.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 클래식 포털에서 Azure AD 관리 환경 사용 중지

**유형:** 사용되지 않음   
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리
 

2018년 1월 8일부터 Azure 클래식 포털의 Azure AD 관리 환경 사용이 중지됩니다. Azure 클래식 포털의 사용 중지와 함께 적용됩니다. 앞으로 Azure AD의 포털 기반 관리를 위해서는 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용해야 합니다.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor 웹 포털의 사용이 중지됩니다.

**유형:** 사용되지 않음  
**서비스 범주:** Azure AD  
**제품 기능:** 디렉터리
 

2018년 1월 8일부터 PhoneFactor 웹 포털의 사용이 중지됩니다. 이 포털은 MFA 서버 관리에 사용되었으며, 해당 기능은 Azure Portal(portal.azure.com)로 이동되었습니다. 

MFA 구성의 위치는 **Azure Active Directory \> MFA 서버**입니다.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD 보고서 사용 중지


**유형:** 사용되지 않음  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  


새로운 Azure Active Directory 관리 콘솔이 일반 공급되고 활동 및 보안 보고서를 위해 새로운 API가 제공됨에 따라서, 2017년 12월 31일부로 "/reports" 엔드포인트에 있는 보고서 API 사용이 중지되었습니다.


**사용할 수 있는 기능은 무엇인가요?**

새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 검색할 수 있는 2개의 새로운 API를 만들었습니다. 새로운 API 집합은 보다 풍부한 감사 및 로그인 활동을 제공할 뿐만 아니라 보다 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 보고 API를 시작](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Azure Active Directory ID 보호 및 Microsoft Graph 시작](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017년 12월
 

### <a name="terms-of-use-in-the-access-panel"></a>액세스 패널의 사용 약관

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수
 
이제 액세스 패널로 이동하면 이전에 수락한 사용 약관을 볼 수 있습니다.

다음 단계를 수행하세요.

1. [MyApps 포털](https://myapps.microsoft.com)로 이동하고 로그인합니다.

2. 오른쪽 위 모서리에서 자신의 이름을 선택한 다음 목록에서 **프로필**을 선택합니다. 

3. **프로필**에서 **사용 약관 검토**를 클릭합니다. 

4. 이제 내가 동의한 사용 약관을 검토할 수 있습니다. 

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>새 Azure AD 로그인 환경

**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD와 Microsoft 계정 ID 시스템의 UI 모양과 느낌이 일관된 스타일로 새롭게 디자인되었습니다. 또한 Azure AD 로그인 페이지에서는 먼저 사용자 이름을 수집하고 두 번째 화면에서 자격 증명을 수집합니다.

자세한 내용은 [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)(새 Azure AD 로그인 환경을 공개 미리 보기로 제공)를 참조하세요.
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경

**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** 사용자 인증
 
Azure AD 로그인 페이지의 **로그인 상태 유지** 확인란을 사용자가 인증된 후에 표시되는 새 프롬프트로 바꾸었습니다. 

이 프롬프트에 사용자가 **예**로 응답하면 영구 새로 고침 토큰이 제공됩니다. 이 동작은 사용자가 이전 환경에서 **로그인 유지** 확인란을 선택할 때와 동일합니다. 페더레이션 된 테넌트에게는 페더레이션된 서비스로 인증이 성공한 후에 이 프롬프트가 표시됩니다.

자세한 내용은 [로그인 프롬프트 감소: Azure AD 로그인을 위한 새로운 "로그인 유지" 환경을 미리 보기로 제공](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)을 참조하세요. 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>동의하기 전에 사용 약관을 펼치도록 하는 구성 추가

**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수
 
사용 약관을 수락하기 전에 사용 약관을 펼치도록 사용자에게 요구하는 옵션이 관리자에게 필요합니다.

사용자에게 사용 약관을 펼치도록 요구하려면 **On** 또는 **Off** 중 하나를 선택합니다. **On** 설정은 사용 약관을 수락하기 전에 확인하도록 사용자에게 요구합니다.

자세한 내용은 [Azure AD 사용 약관 기능(미리 보기)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>적격 역할 할당에 대한 범위 지정 활성화

**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
범위가 지정된 활성화를 사용하면 원래 할당 기본값보다 낮은 자율성을 적용하여 적절한 Azure 리소스 역할 할당을 활성화할 수 있습니다. 예를 들어, 테넌트의 구독 소유자로 할당되는 경우가 있습니다. 범위가 지정된 활성화를 사용하면 구독 내에 포함된 리소스 최대 5개에 대해 소유자 역할을 활성화할 수 있습니다(예: 리소스 그룹, 가상 머신). 활성화의 범위를 지정하면 중요한 Azure 리소스가 원치 않게 변경될 가능성을 줄일 수 있습니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)을 참조하세요.
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리의 새로운 페더레이션된 앱

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능**: 타사 통합
 
2017년 12월에, 페더레이션 지원을 포함하는 다음과 같은 새 앱이 앱 갤러리에 추가되었습니다.

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

사용 가능한 모든 자습서에 대한 개요는 [Azure Active Directory와 SaaS 응용 프로그램 통합](https://aka.ms/appstutorial)을 참조하세요.

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 디렉터리 역할에 대한 승인 워크플로

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
Azure AD 디렉터리 역할의 승인 워크플로가 일반 공급됩니다.

승인 워크플로를 통해, 권한 있는 역할 관리자는 자격을 갖춘 역할 멤버가 권한 있는 역할을 사용하려면 그 전에 역할 활성화를 요청하도록 요청할 수 있습니다. 여러 사용자와 그룹이 승인 책임을 위임 받을 수 있습니다. 승인이 완료되고 역할이 활성화되면 자격을 갖춘 역할 멤버에게 알림이 전송됩니다.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>통과 인증: 비즈니스용 Skype 지원

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증


통과 인증은 사용자가 온라인 및 하이브리드 토폴로지를 비롯한 최신 인증을 지원하는 비즈니스용 Skype 클라이언트 응용 프로그램에 로그인하도록 지원합니다. 

자세한 내용은 [Skype for Business topologies supported with Modern Authentication](https://technet.microsoft.com/library/mt803262.aspx)(최신 인증에서 비즈니스용 Skype 토폴로지 지원됨)을 참조하세요.
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC에 대한 Azure AD Privileged Identity Management 업데이트(미리 보기)

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
Azure RBAC(Role-Based Access Control)에 대한 Azure AD PIM(Privileged Identity Management)의 공개 미리 보기 새로 고침으로 다음을 수행할 수 있습니다.

* Just Enough Administration을 사용할 수 있습니다.
* 리소스 역할을 활성화하기 위한 승인을 요청할 수 있습니다.
* Azure AD 및 Azure RBAC 역할 모두에 대한 승인이 필요한 역할의 향후 활성화를 예약할 수 있습니다.

 
자세한 내용은 [Azure 리소스용 Privileged Identity Management(미리 보기)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.

 
---
 
## <a name="november-2017"></a>2017년 11월
 
### <a name="access-control-service-retirement"></a>Access Control Service사용 중지



**유형:** 변경 계획  
**서비스 범주:** Access Control Service  
**제품 기능:** Access Control Service 


 Azure Active Directory Access Control(또는 Access Control Service)은 2018년 말에 사용이 중지됩니다. 자세한 일정과 개괄적인 마이그레이션 지침을 비롯한 자세한 정보가 몇 주 내에 제공될 예정입니다. Access Control Service에 대한 질문이 있으면 이 페이지에 의견을 남겨주세요. 팀 멤버가 연락 드리겠습니다.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Intune Managed Browser에 대한 브라우저 액세스 제한 


**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호




Intune Managed Browser를 승인된 앱으로 사용하면 Office 365 및 기타 Azure AD 연결 클라우드 앱에 대한 브라우저 액세스를 제한할 수 있습니다. 

응용 프로그램 기반 조건부 액세스에 대해 다음과 같은 조건을 구성할 수 있습니다.

**클라이언트 앱:** 브라우저

**변경되면 무엇이 달라지나요?**

현재는 이 조건을 사용하면 액세스가 차단됩니다. 미리 보기를 사용할 수 있게 되면 모든 액세스에는 관리되는 브라우저 응용 프로그램을 사용해야 합니다. 

앞으로 공개될 블로그와 릴리스 정보에서도 이 기능과 자세한 정보를 찾아볼 수 있습니다. 

자세한 내용은 [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 참조하세요.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호




[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)에 다음과 같은 앱이 추가될 계획입니다.

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>사용 약관을 여러 언어로 제공



**유형:** 새로운 기능    
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수





이제 관리자가 여러 PDF 문서가 포함된 새로운 사용 약관을 작성할 수 있습니다. PDF 문서에는 문서에서 사용된 언어를 태그로 지정할 수 있습니다. 사용자 기본 설정에 따라 사용자에게 일치하는 언어로 된 PDF가 표시됩니다. 일치하는 언어가 없으면 기본 언어가 표시됩니다.


---
 

### <a name="real-time-password-writeback-client-status"></a>실시간 비밀번호 쓰기 저장 클라이언트 상태



**유형:** 새로운 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증


 

이제 온-프레미스 비밀번호 쓰기 저장 클라이언트의 상태를 검토할 수 있습니다. 이 옵션은 [암호 재설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 페이지의 **온-프레미스 통합** 섹션에서 확인할 수 있습니다. 

온-프레미스 쓰기 저장 클라이언트와의 연결에 문제가 있는 경우 다음과 같은 정보를 제공하는 오류 메시지가 표시됩니다.

- 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없는 이유
- 문제 해결에 도움이 되는 설명서 링크 


자세한 내용은 [온-프레미스 통합](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)을 참조하세요.

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스 



 
**유형:** 새로운 기능  
**서비스 범주:** Azure AD  
**제품 기능:** ID 보안 및 보호





이제 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)를 사용하여 Intune 앱 보호 정책을 지원하는 [승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)으로 Office 365 및 기타 Azure AD에 연결된 클라우드 앱의 액세스를 제한할 수 있습니다. 승인된 클라이언트 응용 프로그램에서 기업 데이터를 구성하고 보호하는 데 Intune 앱 보호 정책이 사용됩니다.

[앱 기반](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) 조건부 액세스 정책과 [장치 기반](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) 조건부 액세스 정책이 결합됨으로써 개인 장치와 기업 장치의 데이터를 유연하게 보호할 수 있게 됩니다.

다음은 앱 기반 조건부 액세스에 사용 가능한 조건과 컨트롤입니다.

**지원되는 플랫폼 조건**

- iOS
- Android

**클라이언트 앱 조건**

- 모바일 앱 및 데스크톱 클라이언트

**액세스 제어**

- 승인된 클라이언트 앱 필요


자세한 내용은 [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)를 참조하세요.

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal에서 Azure AD 장치 관리



**유형:** 새로운 기능  
**서비스 범주:** 장치 등록 및 관리  
**제품 기능:** ID 보안 및 보호

 



이제 Azure AD에 연결된 장치와 장치 관련 활동을 모두 한 곳에서 확인할 수 있습니다. Microsoft는 Azure Portal에서 모든 장치 ID와 설정을 한 번에 관리할 수 있는 새로운 관리 환경을 선보입니다. 이 릴리스에서는 다음과 같은 기능을 수행할 수 있습니다.

- Azure AD의 조건부 액세스에 사용할 수 있는 모든 장치 보기
- 하이브리드 Azure AD 가입 장치를 비롯한 속성 보기
- Azure AD 가입 장치의 BitLocker 키 찾기, Intune을 사용하여 장치 관리하기
- Azure AD 장치 관련 설정 관리하기

자세한 내용은 [Azure Portal을 사용하여 장치 관리](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)를 참조하세요.



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD 조건부 액세스의 장치 플랫폼으로서 macOS 지원 



**유형:** 새로운 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호 
 

이제 Azure AD 조건부 액세스 정책에서 장치 플랫폼 조건으로서 macOS를 추가 또는 제외할 수 있습니다. 지원되는 장치 플랫폼에 macOS가 추가되어 다음과 같은 기능이 지원됩니다.

- **Intune을 사용하여 macOS 장치를 등록하고 관리합니다.** iOS, Android와 같은 여타 플랫폼과 마찬가지로 macOS의 경우에도 통합 등록을 수행하는 기업 포털 응용 프로그램이 제공됩니다. 새로운 macOS용 기업 포털 앱을 이용하면 Intune을 사용하여 장치를 등록하고 등록한 장치를 Azure AD에 등록할 수 있습니다.
- **macOS 장치가 Intune에 정의된 조직의 준수 정책을 준수하는지 확인합니다.** 이제 Azure Portal의 Intune에서 macOS 장치에 대한 준수 정책을 설정할 수 있습니다. 
- **Azure AD에서 응용 프로그램에 대한 액세스를 호환 가능한 macOS 장치로 제한합니다.** 조건부 액세스 정책에는 macOS가 별도의 장치 플랫폼 옵션으로 있습니다. 이제 Azure에 설정된 대상 응용 프로그램에 대해 macOS 전용 조건부 액세스 정책을 작성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Intune을 사용하여 macOS를 위한 장치 준수 정책 만들기](https://aka.ms/macoscompliancepolicy)
- [Azure AD의 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication용 네트워크 정책 서버 확장 


**유형:** 새로운 기능    
**서비스 범주:** Multi-Factor Authentication  
**제품 기능:** 사용자 인증




Azure Multi-Factor Authentication용 네트워크 정책 서버 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 다단계 인증 기능을 추가합니다. 네트워크 정책 서버 확장을 사용하면, 전화 통화, 문자 메시지 또는 휴대폰 앱 확인을 기존 인증 흐름에 추가할 수 있습니다. 새 서버를 설치, 구성 및 유지 관리할 필요가 없습니다. 

이 확장은 Azure Multi-Factor Authentication 서버를 배포하지 않고 가상 사설망 연결을 보호하려는 조직을 위해 작성되었습니다. 네트워크 정책 서버 확장은 RADIUS 및 클라우드 기반 Azure Multi-Factor Authentication 간에 어댑터로 작동하여 페더레이션 사용자 또는 동기화된 사용자를 위한 또 다른 인증을 제공합니다.


자세한 내용은 [기존 네트워크 정책 서버 인프라를 Azure Multi-Factor Authentication과 통합](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)을 참조하세요.

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>삭제된 사용자 복원 또는 영구 제거


**유형:** 새로운 기능    
**서비스 범주:** 사용자 관리  
**제품 기능:** 디렉터리 



이제 Azure AD 관리 센터에서 다음과 같은 기능을 수행할 수 있습니다.

- 삭제된 사용자 복원 
- 사용자 영구 삭제


**기능 사용해 보기:**

1. Azure AD 관리 센터의 **관리** 섹션에서 [모든 사용자](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)를 선택합니다. 

2. **표시** 목록에서 **최근 삭제된 사용자**를 선택합니다. 

3. 최근 삭제된 사용자를 하나 이상 선택한 다음 복원하거나 영구 삭제합니다.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD 앱 기반 조건부 액세스의 새로운 승인된 클라이언트 앱

 
**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호


[승인된 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)에 다음과 같은 앱이 추가되어 있습니다.

- Microsoft Planner
- Azure Information Protection 


자세한 내용은 다음을 참조하세요.

- [승인된 클라이언트 앱 요구 사항](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 여러 컨트롤 사이에 ‘OR’ 사용 


**유형:** 변경된 기능    
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

 
이제 조건부 액세스 제어에 "OR"(선택한 컨트롤 중 하나 필요) 연산자를 사용할 수 있습니다. 이 기능을 사용하여 액세스 제어 사이에 "OR"이 있는 정책을 만들 수 있습니다. 사용자가 다단계 인증을 사용하거나 규정을 준수하는 장치를 사용하도록 요구하려는 경우 "OR" 연산자를 사용하여 정책을 만들 수 있습니다.

자세한 내용은 [Azure AD 조건부 액세스의 컨트롤](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)을 참조하세요.

 
---

### <a name="aggregation-of-real-time-risk-events"></a>실시간 위험 이벤트의 집계


**유형:** 변경된 기능    
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 및 보호


Azure AD ID 보호에서는 특정 날짜에 동일한 IP 주소에서 발생한 모든 실시간 위험 이벤트가 위험 이벤트 유형별로 집계됩니다. 이 변경으로 인해 사용자 보안에 영향을 주지 않지만, 표시되는 위험 이벤트의 양이 제한됩니다.

사용자가 로그인할 때마다 기반 실시간 감지가 작동합니다. 다단계 인증이나 액세스 차단이 설정된 로그인 위험 보안 정책이 있어도 위험한 로그인이 있을 때마다 계속 트리거됩니다.

 
---
 




## <a name="october-2017"></a>2017년 10월


### <a name="deprecate-azure-ad-reports"></a>Azure AD 보고서 사용 중지


**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** ID 수명 주기 관리  



Azure Portal은 다음을 제공합니다.

- 새로운 Azure AD 관리 콘솔
- 활동 및 보안 보고서를 위한 새로운 API
 
새로운 기능으로 인해 /reports 엔드포인트의 보고서 API는 2017년 12월 10일에 사용 중지됩니다. 

---

### <a name="automatic-sign-in-field-detection"></a>자동 로그인 필드 감지


**유형:** 고정   
**서비스 범주:** 내 앱  
**제품 기능:** Single Sign-On  



Azure AD는 HTML 사용자 이름 및 암호 필드를 렌더링하는 응용 프로그램의 자동 로그인 필드 검색을 지원합니다. 이 단계는 [응용 프로그램에 대한 로그인 필드를 자동으로 캡처하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application)에 설명되어 있습니다. 이 기능은 [Azure Portal](http://aad.portal.azure.com)의 **엔터프라이즈 응용 프로그램** 페이지에 *비갤러리* 응용 프로그램을 추가하여 찾을 수 있습니다. 또한 이 새 응용 프로그램의 **Single Sign-On** 모드를 **암호 기반 Single Sign-on**으로 구성하고 웹 URL을 입력한 다음 페이지를 저장할 수 있습니다.
 
서비스 문제로 인해 이 기능은 일시적으로 사용하지 않도록 설정되어 있었습니다. 이제 문제가 해결되어 자동 로그인 필드 검색을 다시 사용할 수 있습니다.

---

### <a name="new-multi-factor-authentication-features"></a>새 다단계 인증 기능


**유형:** 새로운 기능  
**서비스 범주:** Multi-Factor Authentication  
**제품 기능:** ID 보안 및 보호  



MFA(Multi-Factor Authentication)는 조직을 보호하기 위한 필수적인 요소입니다. 자격 증명의 적응성을 향상시키고 MFA 환경을 더욱 원활하게 만들기 위해 다음과 같은 기능이 추가되어 있습니다. 

- 다단계 인증 결과는 Azure AD 로그인 보고서에 직접 통합되며, 여기에는 MFA 결과에 대한 프로그래밍 방식의 액세스가 포함됩니다.
- MFA 구성이 Azure Portal의 Azure AD 구성 환경에 보다 깊숙이 통합되었습니다.

이 공개 미리 보기에서는 핵심 Azure AD 구성 환경에 통합된 MFA 관리와 보고 기능을 경험할 수 있습니다. 이제 Azure AD 환경에서 MFA 관리 포털 기능을 관리할 수 있습니다.

자세한 내용은 [Azure Portal의 MFA보고에 대한 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)를 참조하세요. 


---

### <a name="terms-of-use"></a>사용 약관



**유형:** 새로운 기능  
**서비스 범주:** 사용 약관  
**제품 기능:** 규정 준수  



Azure AD 이용 약관을 사용하여 법률 요구 사항 또는 규정 준수 요구 사항에 대한 관련 면책 조항과 같은 정보를 사용자에게 제공할 수 있습니다.

다음 시나리오에서 Azure AD 사용 약관을 사용할 수 있습니다.

- 조직의 모든 사용자에 대한 일반 사용 약관
- 사용자의 특성에 기반한 특정 사용 약관(예: 의사 및 간호사 또는 국내 및 해외 직원, 동적 그룹에 의한 수행)
- Salesforce와 같은 영향력이 높은 비즈니스 앱에 액세스하기 위한 특정 이용 약관

자세한 내용은 [Azure AD 사용 약관](https://docs.microsoft.com/azure/active-directory/active-directory-tou)을 참조하세요.


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management의 기능 개선


**유형:** 새로운 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management  


이제 Azure AD Privileged Identity Management를 사용하여 조직에서 Azure Resources(미리 보기)에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다.

- 구독
- 리소스 그룹
- 가상 머신 

Azure Portal에서 Azure RBAC 기능을 사용하는 모든 리소스는 Azure AD Privileged Identity Management가 제공하는 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다.

자세한 내용은 [Azure 리소스용 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)를 참조하세요.


---

### <a name="access-reviews"></a>액세스 검토


**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 규정 준수  



조직은 액세스 검토(미리 보기)를 통해 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 

- 응용 프로그램 및 그룹 멤버 자격의 액세스 권한에 대한 액세스 검토를 사용하여 게스트 사용자 액세스를 다시 인증할 수 있습니다. 검토자는 액세스 검토로 제공되는 통찰력을 기반으로 게스트에게 계속 액세스를 허용할지를 효율적으로 결정할 수 있습니다.
- 액세스 검토를 사용하여 응용 프로그램 및 그룹 멤버 자격에 대한 직원 액세스를 다시 인증할 수 있습니다.

액세스 검토 컨트롤을 조직과 관련된 프로그램으로 수집하여 규정 준수 또는 위험 감지 응용 프로그램에 대한 검토를 추적할 수 있습니다.

자세한 내용은 [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)를 참조하세요.


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>내 앱 및 Office 365 앱 시작 관리자에서 타사 응용 프로그램 숨기기



**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** Single Sign-On  



이제 새 **앱 숨기기** 속성을 통해 사용자 포털에 표시되는 앱을 더 잘 관리할 수 있습니다. 백 엔드 서비스를 위해 앱 타일이 표시되거나 타일이 중복되고 사용자의 앱 시작 관리자가 복잡하게 표시되는 경우 앱을 숨기면 도움이 됩니다. 토글은 타사 앱 섹션의 **속성** 섹션에 있으며 **사용자가 볼 수 있습니까?** 라는 레이블이 지정되어 있습니다. PowerShell을 통해 프로그래밍 방식으로 앱을 숨길 수도 있습니다. 

자세한 내용은 [Hide a third-party application from user's experience in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)(Azure AD의 사용자 환경에서 타사 응용 프로그램 숨기기)를 참조하세요. 


**사용할 수 있는 기능은 무엇인가요?**

 새 관리 콘솔로 전환하면서 Azure AD 활동 로그를 가져오는 2개의 API가 새롭게 제공됩니다. 새로 적용된 API는 다양한 감사 및 로그인 활동에 더해 다양한 필터링 및 정렬 기능을 제공합니다. 이전에 보안 보고서를 통해 사용 가능했던 데이터를 이제 Microsoft Graph의 ID 보호 위험 이벤트 API를 통해 액세스할 수 있습니다.


## <a name="september-2017"></a>2017년 9월

### <a name="hotfix-for-identity-manager"></a>Identity Manager용 핫픽스


**유형:** 변경된 기능  
**서비스 범주:** Identity Manager  
**제품 기능:** ID 수명 주기 관리  



핫픽스 롤업 패키지(빌드 4.4.1642.0)는 2017년 9월 25일부터 Identity Manager 2016 서비스 팩 1용으로 사용할 수 있습니다. 이 롤업 패키지는 다음과 같습니다.

- 문제를 해결하고 개선 사항을 추가합니다.
- Identity Manager 2016용 빌드 4.4.1459.0까지 모든 Identity Manager 2016 서비스 팩 1 업데이트를 대체하는 누적 업데이트입니다. 
- Identity Manager 2016 빌드 4.4.1302.0이 필요합니다. 

자세한 내용은 [Identity Manager 2016 서비스 팩 1용 핫픽스 롤업 패키지(빌드 4.4.1642.0) 사용 가능](https://support.microsoft.com/help/4021562)을 참조하세요. 

---
