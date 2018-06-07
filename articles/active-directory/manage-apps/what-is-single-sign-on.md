---
title: Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까? | Microsoft Docs
description: Azure Active Directory를 사용하여 비즈니스에 필요한 모든 SaaS 및 웹 응용 프로그램에 Single Sign-On을 사용하도록 설정합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: article
ms.date: 09/11/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: b185f53f8f13ebbe1efc57a99b7d65269f5559c0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354533"
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?
Single Sign-On이란 단일 사용자 계정을 사용하여 한 번만 로그인함으로써 비즈니스를 수행하는데 필요한 모든 응용 프로그램 및 리소스에 액세스할 수 있음을 의미합니다. 로그인하면 다시 인증(예: 암호 입력)을 수행하지 않아도 필요한 모든 응용 프로그램에 액세스할 수 있습니다.

대부분의 조직에서는 최종 사용자 생산성을 위해 Office 365, Box, Salesforce와 같은 SaaS(Software as a Service) 응용 프로그램에 의존합니다. 지금까지 IT 담당자는 각 SaaS 응용 프로그램에서 사용자 계정을 개별적으로 만들고 업데이트해야 했으며, 사용자는 각 SaaS 응용 프로그램에 대한 암호를 기억해야 했습니다. 

Azure Active Directory는 온-프레미스 Active Directory를 클라우드로 확장하여, 사용자가 자신의 기본 조직 계정을 사용하여 해당 도메인에 가입된 장치 및 회사 리소스뿐만 아니라 작업에 필요한 모든 웹 및 SaaS 응용 프로그램에 로그인할 수 있도록 합니다. 

따라서 사용자가 여러 사용자 이름과 암호를 관리할 필요가 없을 뿐만 아니라, 조직 그룹 구성원 및 직원의 상태에 따라 응용 프로그램 액세스가 자동으로 프로비저닝되거나 프로비전 해제될 수 있습니다. Azure Active Directory는 SaaS 응용 프로그램에 대한 사용자의 액세스를 중앙에서 관리할 수 있는 보안 및 액세스 관리 제어를 도입했습니다. 

Azure AD를 사용하면 현재 널리 사용하고 있는 SaaS 응용 프로그램에 쉽게 통합할 수 있습니다. Azure AD는 ID 및 액세스 관리 기능을 제공하므로 사용자가 응용 프로그램에 직접 Single Sign-On하거나, Office 365와 같은 포털이나 Azure AD 액세스 패널에서 응용 프로그램을 검색하여 통합할 수 있습니다.

통합 아키텍처는 다음 네 가지 기본 문서 블록으로 구성됩니다.

* Single Sign-On을 사용하여 사용자가 Azure AD에서 조직 계정을 기반으로 해당 SaaS 응용 프로그램에 액세스할 수 있습니다. Single Sign-On을 통해 사용자가 자신의 단일 조직 계정을 사용하여 응용 프로그램에 인증할 수 있습니다.
* 사용자 프로비저닝은 대상 SaaS 응용 프로그램에 사용자 프로비저닝 및 프로비저닝 해제를 가능하게 합니다.  Single Sign-On을 통해 인증한 후에는, 사용자에게 프로비전된 계정으로 응용 프로그램을 사용하도록 권한을 부여할 수 있습니다.
* Azure Portal의 중앙 집중식 응용 프로그램 액세스 관리를 사용하면, 응용 프로그램 액세스 의사 결정 및 승인을 조직의 모든 사용자에게 위임할 수 있으며 SaaS 응용 프로그램 액세스 및 관리를 단일 지점에서 할 수 있습니다.
* Azure AD에 사용자 동작 보고 및 모니터링 기능이 통합되었습니다.

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Azure Active Directory에서 Single Sign-On이 작동하는 방식
사용자가 응용 프로그램에 "로그인"할 때 본인이 누구인지를 증명해야 하는 인증 프로세스를 수행합니다. Single Sign-On을 사용하지 않을 경우 일반적으로 응용 프로그램에 저장된 암호를 입력하여 수행하며 사용자가 이 암호를 알고 있어야 합니다. 

Azure AD는 응용 프로그램에 로그인하는 세 가지 방법을 지원합니다.

* **페더레이션된 Single Sign-On**을 사용하면 사용자 인증을 위해 암호를 묻는 메시지를 표시하지 않고 응용 프로그램에서 Azure AD로 리디렉션할 수 있습니다. 이 기능은 SAML 2.0, WS-Federation 또는 OpenID Connect와 같은 프로토콜을 지원하는 응용 프로그램에 대해 지원되며, 가장 강력한 Single Sign-On 모드입니다.
* **암호 기반 Single Sign-On**을 사용하면 웹 브라우저 확장 또는 모바일 응용 프로그램을 사용하여 안전한 응용 프로그램 암호 저장 및 재생이 가능합니다. 이 기능은 응용 프로그램에서 제공하는 기존 로그인 프로세스를 사용하지만, 관리자가 암호를 관리할 수 있으므로 사용자는 암호를 몰라도 됩니다.
* **기존 Single Sign-On**을 사용하면 Azure AD에서 응용 프로그램에 대해 설정된 기존의 Single Sign-On을 활용하여, 응용 프로그램을 Office 365 또는 Azure AD 액세스 패널 포털에 연결할 수 있습니다. 또한 해당 위치에서 응용 프로그램을 시작할 때 Azure AD에서 추가적인 보고가 가능합니다.

사용자가 응용 프로그램을 통해 인증을 받으면 응용 프로그램에서 계정 레코드가 프로비전되어 있어야 합니다. 계정 레코드는 응용 프로그램 내의 사용 권한 및 액세스 수준이 있는 위치를 응용 프로그램에 알려줍니다. 이 계정 레코드의 프로비저닝은 자동으로 되거나, 사용자에게 Single Sign-On 액세스를 제공하기 전에 관리자에 의해 수동으로 발생할 수 있습니다. 

 이러한 Single Sign-On 모드 및 프로비저닝에 대한 자세한 내용은 아래에서 이어집니다. 

### <a name="federated-single-sign-on"></a>페더레이션된 Single Sign-On
페더레이션된 Single Sign-On을 사용하면 조직의 사용자가 Azure AD 사용자 계정 정보를 사용하여 타사 SaaS 응용 프로그램에 자동으로 로그인할 수 있습니다.

이 시나리오에서는 Azure AD에 이미 로그인된 사용자가 타사 SaaS 응용 프로그램에서 제어하는 리소스에 액세스하려는 경우, 페더레이션으로 인해 사용자를 다시 인증할 필요가 없습니다.

Azure AD는 SAML 2.0, WS-Federation 또는 OpenID Connect 프로토콜을 지원하는 응용 프로그램에 페더레이션된 Single Sign-On을 지원합니다. 

참고 항목: [페더레이션된 Single Sign-On에 대한 인증서 관리](../active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>암호 기반 Single Sign-On
암호 기반 Single Sign-On을 구성하면 조직의 사용자가 타사 SaaS 응용 프로그램의 사용자 계정 정보를 사용하여, Azure AD에서 타사 SaaS 응용 프로그램에 자동으로 로그인할 수 있습니다. 이 기능을 사용하면 Azure AD가 사용자 계정 정보 및 관련된 암호를 수집하고 안전하게 저장합니다. 

Azure AD는 HTML 기반 로그인 페이지가 있는 모든 클라우드 기반 앱에 암호 기반 Single Sign-On을 지원할 수 있습니다. 사용자 지정 브라우저 플러그인을 사용하여, AAD는 디렉터리에서 사용자 이름 및 암호와 같은 응용 프로그램 자격 증명을 안전하게 검색하고 이러한 자격 증명을 사용자 대신 응용 프로그램의 로그인 페이지에 입력함으로써 사용자의 로그인 프로세스를 자동화합니다. 두 가지 사용 사례가 있습니다. 

1. **관리자의 
2. 자격 증명 관리** – 관리자가 응용 프로그램 자격 증명을 만들고 관리하며, 그러한 자격 증명을 응용 프로그램에 액세스해야 하는 사용자나 그룹에 할당할 수 있습니다. 이러한 경우 최종 사용자가 자격 증명을 알 필요가 없지만 해당 액세스 패널에서 클릭하거나 제공된 링크를 통해 응용 프로그램에 Single Sign-On 액세스할 수 있습니다. 이 경우 관리자가 자격 증명의 수명 주기를 관리할 수 있으며, 최종 사용자의 편의를 위해 사용자가 앱 특정 암호를 기억하거나 관리할 필요가 없습니다. 자격 증명은 자동 로그인 프로세스 중에 최종 사용자에게 난독 처리되지만 기술적으로 웹 디버깅 도구를 사용하여 사용자가 찾아낼 수 있습니다. 따라서 사용자와 관리자는 사용자가 직접 자격 증명을 제시하는 것과 동일한 보안 정책을 따라야 합니다. 관리자가 제공한 자격 증명은 소셜 미디어나 문서 공유 응용 프로그램과 같이 많은 사용자가 공유하는 계정 액세스를 제공할 때 유용합니다.
2. **사용자의 자격 증명 관리** – 관리자가 응용 프로그램을 최종 사용자나 그룹에 할당할 수 있고, 최종 사용자가 자신의 액세스 패널에 있는 응용 프로그램에 처음 액세스할 때 고유한 자격 증명을 직접 입력하도록 할 수 있습니다. 이렇게 하면 최종 사용자가 응용 프로그램에 액세스할 때마다 앱 특정 암호를 계속 입력하지 않아도 되므로 편리합니다. 사용자는 필요에 따라 해당 암호를 업데이트 또는 삭제하여 계속해서 관리할 수 있습니다. 이 사용 사례를 자격 증명을 관리하는 중간 단계로 사용할 수 있으므로 관리자가 이후에 사용자의 앱 액세스 환경을 변경하지 않고 응용 프로그램에 대한 새 자격 증명을 설정할 수 있습니다.

두 경우 모두 자격 증명이 암호화된 상태로 디렉터리에 저장되며, 자동화된 로그인 프로세스 중에 HTTPS를 통해서만 전달됩니다. Azure AD는 암호 기반 Single Sign-On을 사용하여, 페더레이션 프로토콜을 지원할 수 없는 앱을 위한 편리한 ID 액세스 관리 솔루션을 제공합니다.

암호 기반 SSO는 브라우저 확장에 의존하여 Azure AD에서 응용 프로그램 및 사용자 관련 정보를 안전하게 검색하고 서비스에 적용합니다. Azure AD에서 지원하는 대부분의 타사 SaaS 응용 프로그램은 이 기능을 지원합니다.

암호 기반 SSO의 경우 최종 사용자 브라우저는 다음 중 하나일 수 있습니다.
* Windows 7 이상의 Internet Explorer 11
* Windows 10 Anniversary Edition 이상 Edge 
* Chrome - Windows 7 이상 및 Mac OS X 이상
* Firefox 26.0 이상 - Windows XP SP2 이상 및 Mac OS X 10.6 이상

### <a name="existing-single-sign-on"></a>기존 Single Sign-On
응용 프로그램에 대한 Single Sign-On을 구성할 때 Azure Portal은 "기존 Single Sign-On”이라는 세 번째 옵션을 제공합니다. 이 옵션을 사용하면 관리자가 응용 프로그램에 대한 링크를 만들어 선택된 사용자의 액세스 패널에 배치할 수 있습니다. 

예를 들어 Active Directory Federation Services 2.0을 사용하여 사용자를 인증하도록 구성된 응용 프로그램이 있는 경우, 관리자가 "기존 Single Sign-On" 옵션을 사용하여 액세스 패널에 이에 대한 링크를 만들 수 있습니다.  사용자가 이 링크에 액세스하는 경우 Active Directory Federation Services 2.0을 사용하거나 응용 프로그램에서 제공하는 기존 Single Sign-On 솔루션을 사용하여 인증을 받습니다.

### <a name="user-provisioning"></a>사용자 프로비전
선택된 응용 프로그램에 한해, Azure AD를 사용하면 Windows Server Active Directory 또는 Azure AD ID 정보를 사용하여 Azure 관리 포털 내에서 타사 SaaS 응용 프로그램에 대한 자동화된 사용자 프로비저닝 및 프로비저닝 해제가 가능합니다. 이러한 응용 프로그램 중 하나에 대해 Azure AD의 사용자 권한을 사용자에게 제공하면 대상 SaaS 응용 프로그램에 계정을 자동으로 만들거나 프로비전할 수 있습니다.

Azure AD에서 사용자가 삭제되거나 사용자 정보가 변경되면 변경 내용은 SaaS 응용 프로그램에도 반영됩니다. 즉, 자동화된 ID 수명 주기 관리를 구성하면 관리자가 SaaS 응용 프로그램에서 자동화된 프로비저닝 및 프로비저닝 해제를 제어하고 제공할 수 있습니다.  Azure AD에서 이러한 ID 수명 주기 관리의 자동화는 사용자 프로비저닝에 의해 활성화됩니다. 

자세한 내용은 [SaaS 응용 프로그램에 자동화된 사용자 프로비저닝 및 프로비저닝 해제](../active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리 시작
시작할 준비가 되셨습니까? 조직에서 사용하는 Azure AD와 SaaS 응용 프로그램 간에 Single Sign-On을 배포하려면 다음 지침을 따르세요.

### <a name="using-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리 사용
[Azure Active Directory 응용 프로그램 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/) 는 Azure Active Directory와의 Single Sign-On 형식을 지원하는 것으로 알려진 응용 프로그램의 목록을 제공합니다. 

![Azure 온라인 앱 갤러리](media/what-is-single-sign-on/onlineappgallery.png)

다음은 이들이 지원하는 기능별 앱 찾기에 대한 몇 가지 팁입니다.

* Azure AD는 [Azure Active Directory 응용 프로그램 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)에서 모든 "기능을 갖춘" 앱에 대한 자동 프로비저닝 및 프로비저닝 해제를 지원합니다.
* SAML, WS-Federation 또는 OpenID Connect와 같은 프로토콜을 사용하여 페더레이션된 Single Sign-On을 지원하는 페더레이션된 응용 프로그램 목록은 [여기](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx)서 확인할 수 있습니다.

Azure AD 응용 프로그램 갤러리에서 사용할 응용 프로그램을 찾으면, 응용 프로그램 갤러리와 Azure 관리 포털에서 Single Sign-On 활성화에 대해 제공된 단계별 지침을 따라 시작할 수 있습니다. 

### <a name="application-not-in-the-gallery"></a>응용 프로그램이 갤러리에 없습니까?
응용 프로그램이 Azure AD 응용 프로그램 갤러리에 없으면 다음과 같은 옵션이 제공됩니다.

* **사용 중이지만 목록에 없는 앱 추가** - Azure 관리 포털 내에 있는 앱 갤러리에서 사용자 지정 범주를 사용하여, 조직에서 사용 중이지만 목록에 없는 응용 프로그램을 연결합니다.  SAML 2.0을 지원하는 페더레이션된 앱이나 HTML 기반 로그인 페이지가 있고 암호기반 SSO가 구성된 앱을 추가할 수 있습니다. 자세한 내용은 [고유한 응용 프로그램 추가](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)에 대한 이 문서를 참조하세요.
* **개발 중인 고유한 앱 추가** - 사용자가 직접 응용 프로그램을 개발한 경우 Azure AD 개발자 설명서의 지침에 따라 페더레이션된 Single Sign-On 또는 프로비저닝을 구현할 수 있습니다. 이러한 응용 프로그램은 Azure AD Graph API를 사용할 수 있습니다. 자세한 내용은 다음 리소스를 참조하세요. 
  
  * [Azure AD의 인증 시나리오](../active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **앱 통합 요청** - [Azure AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 사용하여 필요한 응용 프로그램에 대한 지원을 요청합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용
Azure Portal에서 Active Directory 확장을 사용하여 응용 프로그램 Single Sign-On을 구성할 수 있습니다. 첫 번째 단계로, 포털의 Active Directory 섹션에서 디렉터리를 선택해야 합니다.

![](./media/what-is-single-sign-on/azuremgmtportal.png)

타사 SaaS 응용 프로그램을 관리하려면 선택한 디렉터리의 응용 프로그램 탭으로 이동할 수 있습니다.  응용 프로그램 탭에서는 관리자가 다음을 수행할 수 있습니다. 

* Azure AD 갤러리의 새 응용 프로그램 혹은 개발 중인 앱 추가 
* 통합된 응용 프로그램 삭제
* 이미 통합된 응용 프로그램 관리

타사 SaaS 응용 프로그램에 대한 일반적인 관리 작업은 다음과 같습니다.

* Azure AD에서 Single Sign-On을 사용하도록 설정. 암호 SSO나 (대상 SaaS에서 가능한 경우) 페더레이션된 SSO 사용 가능. 
* 필요에 따라 사용자 프로비저닝 및 프로비저닝 해제에 대해 사용자 프로비저닝을 사용하도록 설정(ID 수명 주기 관리)
* 사용자 프로비저닝을 사용하도록 설정된 응용 프로그램에 대해 해당 응용 프로그램에 액세스할 수 있는 사용자 선택

페더레이션된 Single Sign-On을 지원하는 갤러리 앱에서 타사 앱과 Azure AD 간에 페더레이션된 트러스트를 만들려면 구성에 인증서 및 메타데이터와 같은 추가 구성 설정을 제공해야 합니다. 구성 마법사는 세부 정보를 단계별로 안내하고 SaaS 응용 프로그램 관련 데이터 및 지침에 쉽게 액세스할 수 있도록 합니다.

자동 사용자 프로비저닝을 지원하는 갤러리 앱의 경우 이 마법사를 사용하려면 SaaS 응용 프로그램에서 사용자 계정을 관리할 Azure AD 권한을 제공해야 합니다. 대상 응용 프로그램에 대해 인증할 때 최소한 Azure AD에서 사용해야 하는 자격 증명을 제공해야 합니다. 추가 구성 설정을 제공해야 하는지 여부는 응용 프로그램의 요구 사항에 따라 달라집니다.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>사용자에게 Azure AD 통합 응용 프로그램 배포
Azure AD는 조직의 최종 사용자에게 응용 프로그램을 배포하는 몇 가지 사용자 지정 가능한 방법을 제공합니다.

* Azure AD 액세스 패널
* Office 365 응용 프로그램 실행 프로그램
* 페더레이션된 앱에 직접 로그온
* 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

조직에 배포할 방법을 선택하는 것은 여러분의 몫입니다. 

### <a name="azure-ad-access-panel"></a>Azure AD 액세스 패널
https://myapps.microsoft.com의 액세스 패널은 최종 사용자가 Azure Active Directory의 조직 계정을 사용하여 Azure AD 관리자가 액세스 권한을 부여한 클라우드 기반의 응용 프로그램을 보고 시작할 수 있도록 하는 웹 기반 포털입니다. [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)의 최종 사용자는 액세스 패널을 통해 셀프 서비스 그룹 관리 기능을 활용할 수도 있습니다.

![Azure AD 액세스 패널](media/what-is-single-sign-on/azure-ad-access-panel.png)

액세스 패널은 Azure Portal과 별개이며, 사용자에게 Azure 구독이나 Office 365 구독이 필요하지 않습니다.

Azure AD 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요.

### <a name="office-365-application-launcher"></a>Office 365 응용 프로그램 실행 프로그램
Office 365를 배포한 조직에서 Azure AD를 통해 사용자에게 할당된 응용 프로그램은 Office 365 포털(https://portal.office.com/myapps)에도 표시됩니다. 이렇게 하면 조직의 사용자가 두 번째 포털을 사용하지 않고도 해당 앱을 쉽고 편리하게 시작할 수 있으므로 Office 365를 사용하는 조직에 권장되는 앱 시작 솔루션입니다.

![](./media/what-is-single-sign-on/officeapphub.png)

Office 365 응용 프로그램 실행 프로그램에 대한 자세한 내용은 [Office 365 앱 실행 프로그램에 해당 앱 표시](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)(영문)를 참조하세요.

### <a name="direct-sign-on-to-federated-apps"></a>페더레이션된 앱에 직접 로그온
SAML 2.0, WS-Federation 또는 OpenID Connect를 지원하는 대부분의 페더레이션된 응용 프로그램은 사용자가 응용 프로그램에서 시작한 다음 자동 리디렉션을 통하거나 로그인 링크를 클릭하여 Azure AD로 로그인하는 기능을 지원합니다. 이것을 서비스 공급자에서 시작한 로그온이라고 하며, Azure AD 응용 프로그램 갤러리에서 페더레이션된 대부분의 응용 프로그램이 이 기능을 지원합니다(자세한 내용은 Azure 관리 포털에서, 앱의 Single Sign-On 구성 마법사에서 연결된 설명서를 참조). 

![](./media/what-is-single-sign-on/workdaymobile.png)

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 직접 로그온 링크
Azure AD는 암호 기반 Single Sign-On, 기존 Single Sign-On 및 모든 형태의 페더레이션된 Single Sign-On을 지원하는 개별 응용 프로그램에 대한 직접 Single Sign-On 링크를 지원합니다. 

이러한 링크는 사용자가 Azure AD 액세스 패널이나 Office 365에서 시작하지 않아도 Azure AD 로그인 프로세스를 통해 사용자를 특정 응용 프로그램으로 보내는, 특별히 제작된 URL입니다. 이러한 Single Sign-On URL은 아래 스크린샷에 표시된 것처럼, Azure 관리 포털의 Active Directory 섹션에서 사전 통합된 응용 프로그램의 대시보드 탭 아래에 있습니다. 

![](./media/what-is-single-sign-on/deeplink.png)

이러한 링크는 복사하여 선택한 응용 프로그램에 대한 로그인 링크를 제공할 위치에 붙여 넣을 수 있습니다. 이 링크는 전자 메일 또는 사용자 응용 프로그램 액세스에 대해 설정한 사용자 지정 웹 기반 포털에 있을 수 있습니다. 다음은 Twitter에 대한 Azure AD Single Sign-On URL의 예제입니다.

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

액세스 패널에 대한 조직과 관련된 URL과 마찬가지로 추가로 myapps.microsoft.com 뒤에 디렉터리에 대한 활성 또는 확인된 도메인 중 하나를 추가하여 이 URL을 사용자 지정할 수 있습니다. 이렇게 하면 사용자가 자신의 ID를 먼저 입력하지 않아도 조직 브랜딩이 로그인 페이지에 즉시 로드됩니다.

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

권한이 있는 사용자가 이러한 응용 프로그램 관련 링크 중 하나를 클릭하면 조직의 로그인 페이지가 먼저 표시되며(아직 로그인하지 않았다고 가정), 로그인 과정이 액세스 패널에서 먼저 중지되지 않고 해당 앱으로 리디렉션됩니다.  사용자에게 암호 기반 Single Sign-On 브라우저 확장과 같은, 응용 프로그램에 액세스하기 위한 필수 구성 요소가 없는 경우 확장을 설치할 것인지 묻는 링크가 표시됩니다. 응용 프로그램에 대한 Single Sign-On 구성이 변경되는 경우에도 링크 URL은 그대로 유지됩니다. 

이러한 링크는 액세스 패널 및 Office 365와 동일한 액세스 제어 메커니즘을 사용하며, Azure 관리 포털에서 응용 프로그램에 할당된 해당 사용자나 그룹만 성공적으로 인증할 수 있습니다. 그러나 권한이 없는 사용자에게는 액세스 권한이 부여되지 않았음을 설명하는 메시지가 표시되고, 액세스할 수 있는 사용 가능한 응용 프로그램을 볼 수 있는 액세스 패널 로드에 대한 링크가 제공됩니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../active-directory-saas-tutorial-list.md)
* [클라우드 앱 검색을 사용하여 허용되지 않은 클라우드 응용 프로그램 찾기](cloud-app-discovery.md)
* [앱에 대한 액세스 관리 소개](../active-directory-managing-access-to-apps.md)
* [Azure AD에서 외부 ID 관리 기능 비교](../active-directory-b2b-compare-b2c.md)


