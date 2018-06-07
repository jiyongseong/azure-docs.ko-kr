---
title: 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법
description: Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법을 설명합니다.
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
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c5f706e6e9402bfc404c370a0d1a45fc07656a9e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법

요즈음 직원은 어디서나 언제든지 어느 장치에서나 생산성을 높이기를 원합니다. 태블릿, 휴대폰 또는 랩톱을 막론하고 자신의 장치에서 일하기를 원합니다. 그리고 해당하는 모든 응용 프로그램인 클라우드의 SaaS 앱 및 회사 앱 온-프레미스 모두에도 액세스할 수 있다고 예상합니다. 온-프레미스 응용 프로그램에 대한 액세스를 제공하려면 일반적으로 가상 사설망(VPN) 또는 완충 영역(DMZ)이 필요했습니다. 이러한 솔루션은 복잡하고 안전하게 만들기도 어려울 뿐만 아니라 설정과 관리에도 비용이 많이 듭니다.

더 나은 방법이 있습니다!

모바일 중심, 클라우드 중심의 최신 인력에게는 최신 원격 액세스 솔루션을 필요합니다. Azure AD 응용 프로그램 프록시는 Azure Active Directory의 기능이며 원격 액세스 서비스를 제공합니다. 즉, 배포, 사용 및 관리하기 쉽습니다.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Azure Active Directory 응용 프로그램 프록시란?
Azure AD 응용 프로그램 프록시는 온-프레미스에 호스트된 웹 응용 프로그램에 대한 SSO(Single Sign-On) 및 보안된 원격 액세스를 제공합니다. 게시하려는 일부 앱은 SharePoint 사이트, Outlook Web Access 또는 사용자가 가지고 있는 다른 LOB 웹 응용 프로그램을 포함합니다. 이러한 온-프레미스 웹 응용 프로그램은 Azure AD, 동일한 ID 및 O365에서 사용되는 제어 플랫폼과 통합됩니다. 최종 사용자는 O365 및 Azure AD와 통합된 다른 SaaS 앱에 액세스할 때와 같은 방식으로 온-프레미스 응용 프로그램에 액세스할 수 있습니다. 사용자에게 이 솔루션을 제공하기 위해 네트워크 인프라를 변경하거나 VPN을 요구할 필요가 없습니다.

## <a name="why-is-application-proxy-a-better-solution"></a>응용 프로그램 프록시가 더 나은 솔루션인 이유
Azure AD 응용 프로그램 프록시는 모든 온-프레미스 응용 프로그램에 대해 단순하고 보안되고 비용 효율적인 원격 액세스 솔루션을 제공합니다.

Azure AD 응용 프로그램 프록시는:

* **간단**
   * 응용 프로그램 프록시를 사용하도록 응용 프로그램을 변경하거나 업데이트할 필요가 없습니다. 
   * 사용자는 일관된 인증 환경을 제공 받습니다. MyApps 포털을 사용하여 클라우드 및 앱 온-프레미스의 SaaS 앱에 Single Sign-On을 가져올 수 있습니다. 
* **보안**
   * Azure AD 응용 프로그램 프록시를 사용하여 앱을 게시하면 Azure의 다양한 권한 부여 제어 및 보안 분석을 이용할 수 있습니다. 조건부 액세스 및 2단계 인증과 같은 클라우드 규모 보안 및 Azure 보안 기능을 제공 받습니다.
   * 사용자에게 원격 액세스를 제공하도록 방화벽을 통해 모든 인바운드 연결을 열 필요가 없습니다. 
* **비용 효율성**
   * 응용 프로그램 프록시는 클라우드에서 작업하므로 시간과 비용을 절약할 수 있습니다. 온-프레미스 솔루션을 사용하려면 일반적으로 DMZ, 에지 서버 또는 기타 복잡한 인프라를 설정하고 유지 관리해야 합니다.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>어떤 종류의 응용 프로그램이 응용 프로그램 프록시에서 작동합니까?
Azure AD 응용 프로그램 프록시를 사용하면 다양한 유형의 내부 응용 프로그램에 액세스할 수 있습니다.

* 인증을 위해 [Windows 통합 인증](application-proxy-configure-single-sign-on-with-kcd.md)을 사용하는 웹 응용 프로그램  
* 폼 기반 또는 [헤더 기반](application-proxy-configure-single-sign-on-with-ping-access.md) 액세스를 사용하는 웹 응용 프로그램  
* 여러 장치에서 다양한 응용 프로그램을 표시하려는 웹 API  
* [원격 데스크톱 게이트웨이](application-proxy-integrate-with-remote-desktop-services.md) 뒤에서 호스트되는 응용 프로그램  
* ADAL(Active Directory 인증 라이브러리)과 통합되는 리치 클라이언트 앱

## <a name="how-does-application-proxy-work"></a>응용 프로그램 프록시는 어떻게 작동합니까?
응용 프로그램 프록시가 작동하도록 구성해야 하는 두 가지 구성 요소는 커넥터 및 외부 끝점입니다. 

커넥터는 네트워크 내부의 Windows Server에 상주하는 간단한 에이전트입니다. 커넥터는 클라우드의 응용 프로그램 프록시 서비스에서 응용 프로그램 온-프레미스로 트래픽 흐름을 지원합니다. 아웃바운드 연결만 사용하므로 인바운드 포트를 열거나 DMZ에 항목을 저장할 필요가 없습니다. 커넥터는 상태를 저장하지 않으며 필요에 따라 클라우드에서 정보를 가져옵니다. 커넥터에 대한 정보 및 부하 분산 및 인증하는 방법은 [Azure AD 응용 프로그램 프록시 커넥터 이해](application-proxy-connectors.md)를 참조하세요. 

외부 끝점은 사용자가 네트워크 외부에서 응용 프로그램에 도달하는 방법입니다. 결정하는 외부 URL로 직접 이동하거나 MyApps 포털을 통해 응용 프로그램에 액세스할 수 있습니다. 사용자가 이러한 끝점 중 하나로 이동하면 Azure AD에서 인증한 다음 커넥터를 통해 온-프레미스 응용 프로그램에 라우팅됩니다.

 ![Azure Ad 응용 프로그램 프록시 다이어그램](./media/application-proxy/azureappproxxy.png)

1. 사용자는 응용 프로그램 프록시 서비스를 통해 응용 프로그램에 액세스하고 인증을 위해 Azure AD 로그인 페이지로 전달됩니다.
2. 성공적인 로그인 후에 토큰을 생성하고 클라이언트 장치에 보냅니다.
3. 클라이언트는 토큰에서 UPN(사용자 주체 이름) 및 SPN(보안 주체 이름)을 검색한 다음 응용 프로그램 프록시 커넥터에 요청을 전달하는 응용 프로그램 프록시 서비스에 토큰을 보냅니다.
4. Single Sign-On을 구성한 경우 커넥터는 사용자를 대신하는 데 필요한 모든 추가 인증을 수행합니다.
5. 커넥터는 온-프레미스 응용 프로그램에 요청을 보냅니다.  
6. 응답은 응용 프로그램 프록시 서비스 및 커넥터를 통해 사용자에게 전송됩니다.

### <a name="single-sign-on"></a>SSO(Single sign-on)
Azure AD 응용 프로그램 프록시는 Windows 통합 인증(IWA) 또는 클레임 인식 응용 프로그램을 사용하는 응용 프로그램에 SSO(Single Sign-On)를 제공합니다. 응용 프로그램에서 IWA를 사용하는 경우 응용 프로그램 프록시는 SSO를 제공하는 Kerberos 제한 위임을 사용하여 사용자를 가장합니다. Azure Active Directory를 신뢰하는 클레임 인식 응용 프로그램이 있는 경우에는 사용자가 이미 Azure AD에 의해 인증되었으므로 SSO가 작동합니다.

Kerberos에 대한 자세한 내용은 [KCD(Kerberos Constrained Delegation)에 대해 확인하려는 모든 정보](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)를 참조하세요.

### <a name="managing-apps"></a>앱 관리
응용 프로그램 프록시를 사용하여 앱이 게시되면 Azure Portal에서 다른 엔터프라이즈 앱처럼 관리할 수 있습니다. 조건부 액세스 및 2단계 인증과 같은 Azure Active Directory 보안 기능을 사용하고, 사용자 권한을 제어하고, 앱에 대한 브랜딩을 사용자 지정할 수 있습니다. 

## <a name="get-started"></a>시작하기

응용 프로그램 프록시를 구성하기 전에 지원되는 [Azure Active Directory 버전](https://azure.microsoft.com/pricing/details/active-directory/) 및 전역 관리자 권한이 있는 Azure AD 디렉터리가 있는지 확인합니다.

두 단계에서 응용 프로그램 프록시 시작:

1. [응용 프로그램 프록시를 사용하도록 설정하고 커넥터 구성](application-proxy-enable.md)    
2. [응용 프로그램 게시](application-proxy-publish-azure-portal.md) - 쉽고 빠른 마법사를 사용하여 온-프레미스 앱을 게시하고 원격으로 액세스할 수 있도록 합니다.

## <a name="whats-next"></a>다음 작업
첫 번째 앱을 게시하면 응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

* [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* [고유한 도메인 이름을 사용하여 응용 프로그램 게시](application-proxy-configure-custom-domain.md)
* [Azure AD 응용 프로그램 프록시 커넥터에 대해 알아보기](application-proxy-connectors.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md) 
* [사용자 지정 홈페이지 설정](application-proxy-configure-custom-home-page.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)

