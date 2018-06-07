---
title: 테넌트를 제한하여 클라우드 앱에 대한 액세스 관리 - Azure | Microsoft Docs
description: 테넌트 제한을 사용하여 Azure AD 테넌트를 기준으로 앱에 액세스할 수 있는 사용자를 관리하는 방법입니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: yossib
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: barbkess
ms.reviewer: richagi
ms.openlocfilehash: 6584db755a586fcf5fd7318f9396b797edeaf0ad
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>테넌트 제한을 사용하여 SaaS 클라우드 응용 프로그램에 대한 액세스 관리

보안을 강조하는 대규모 조직에서는 Office 365와 같은 클라우드 서비스로 이동하려고 하지만, 사용자가 승인된 리소스에만 액세스할 수 있다는 사실을 인식하고 있어야 합니다. 일반적으로 회사에서는 액세스를 관리하려는 경우에 도메인 이름 또는 IP 주소를 제한합니다. 이 방법은 SaaS 앱이 공용 클라우드에서 호스트되고 outlook.office.com 및 login.microsoftonline.com와 같은 공유 도메인 이름으로 실행되는 환경에서는 실패합니다. 이러한 주소를 차단하면 사용자가 단순히 승인된 ID 및 리소스로만 제한되는 것이 아니라 웹상의 Outlook에 완전히 액세스할 수 없게 됩니다.

이러한 경우에 대한 Azure Active Directory 해결 방법은 테넌트 제한이라는 기능입니다. 테넌트 제한은 응용 프로그램이 Single Sign-On에 대해 사용하는 사용할 Azure AD 테넌트에 따라, 조직이 SaaS 클라우드 응용 프로그램에 대한 액세스를 제어할 수 있도록 합니다. 예를 들어 이러한 동일한 응용 프로그램의 다른 조직 인스턴스에 대한 액세스를 차단하면서 조직의 Office 365 응용 프로그램에 대한 액세스를 허용하려고 할 수 있습니다.  

테넌트 제한은 조직 사용자에게 액세스가 허용된 테넌트 목록을 지정할 수 있는 기능을 제공합니다. 그러면 Azure AD는 이러한 허용된 테넌트에 대해서만 액세스를 허가합니다.

이 문서는 Office 365에 대한 테넌트 제한을 중점적으로 설명하지만 Single Sign-On을 위해 Azure AD와 함께 최신 인증 프로토콜을 사용하는 모든 SaaS 클라우드 앱에도 이 기능을 사용할 수 있습니다. Office 365에서 사용되는 테넌트와는 다른 Azure AD 테넌트에서 SaaS 앱을 사용하는 경우 필요한 모든 테넌트가 허용되는지 확인합니다. 클라우드 SaaS 앱에 대한 자세한 내용은 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)를 참조하세요.

## <a name="how-it-works"></a>작동 방법

전체 솔루션은 다음 구성 요소로 구성됩니다. 

1. **Azure AD** – `Restrict-Access-To-Tenants: <permitted tenant list>`가 있는 경우 Azure AD는 허용되는 테넌트에 대한 보안 토큰만 발급합니다. 

2. **온-프레미스 프록시 서버 인프라** – SSL 검사를 수행할 수 있고 허용되는 테넌트 목록이 포함된 헤더를 Azure AD로 지정된 트래픽에 삽입하도록 구성된 프록시 장치입니다. 

3. **클라이언트 소프트웨어** – 테넌트 제한을 지원하기 위해 클라이언트 소프트웨어는 Azure AD에서 직접 토큰을 요청해야 하므로 프록시 인프라에서 트래픽을 가로챌 수 있습니다. 최신 인증(예: OAuth 2.0)이 사용될 경우 브라우저 기반 Office 365 응용 프로그램 및 Office 클라이언트에서 테넌트 제한을 지원합니다. 

4. **최신 인증** – 클라우드 서비스는 테넌트 제한을 사용하고 허용되지 않는 모든 테넌트로의 액세스를 차단하기 위해 최신 인증을 사용해야 합니다. Office 365 클라우드 서비스는 기본적으로 최신 인증 프로토콜을 사용하도록 구성되어야 합니다. 최신 인증에 대한 Office 365 지원과 관련된 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)을 참조하세요.

다음 다이어그램은 고급 트래픽 흐름을 보여 줍니다. SSL 검사는 Office 365 클라우드 서비스로의 트래픽이 아닌 Azure AD로의 트래픽에만 필요합니다. Azure AD에서 인증을 받으려는 트래픽 볼륨이 일반적으로 Exchange Online 및 SharePoint Online과 같은 SaaS 응용 프로그램에 대한 트래픽 볼륨보다 훨씬 작기 때문에 이러한 구분이 필요합니다.

![테넌트 제한 트래픽 흐름 - 다이어그램](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>테넌트 제한 설정

테넌트 제한을 시작하는 방법은 두 단계로 구성됩니다. 첫 번째 단계는 클라이언트가 올바른 주소에 연결할 수 있도록 하는 것입니다. 두 번째는 프록시 인프라를 구성하는 것입니다.

### <a name="urls-and-ip-addresses"></a>URL 및 IP 주소

테넌트 제한을 사용하려면 클라이언트는 인증을 받기 위해 Azure AD URL login.microsoftonline.com, login.microsoft.com 및 login.windows.net에 연결할 수 있어야 합니다. 또한 Office 365에 액세스하려면 클라이언트는 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에 정의된 FQDN/URL에 연결할 수 있어야 합니다. 

### <a name="proxy-configuration-and-requirements"></a>프록시 구성 및 요구 사항

프록시 인프라를 통해 테넌트 제한을 적용하려면 다음과 같은 구성이 필요합니다. 이 지침은 일반적인 것이므로 보다 구체적인 구현 단계는 프록시 공급업체의 설명서를 참조차세요.

#### <a name="prerequisites"></a>필수 조건

- 프록시는 FQDN/URL을 사용하여 SSL 가로채기, HTTP 헤더 삽입 및 대상 필터링을 수행할 수 있어야 합니다. 

- 클라이언트는 SSL 통신을 위해 프록시에서 제공한 인증서 체인을 신뢰해야 합니다. 예를 들어 내부 PKI에서 발급한 인증서가 사용될 경우 내부 발급 루트 인증 기관 인증서를 신뢰할 수 있어야 합니다.

- 이 기능은 Office 365 구독에 포함되어 있지만 테넌트 제한을 사용하여 다른 SaaS 앱에 대한 액세스를 제어하려는 경우 다음 Azure AD Premium 1 라이선스가 필요합니다.

#### <a name="configuration"></a>구성

Login.microsoftonline.com, login.microsoft.com 및 login.windows.net으로 들어오는 각 요청에 대해 두 개의 HTTP 헤더인 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context*를 삽입합니다.

헤더에는 다음 요소가 포함되어야 합니다. 
- *Restrict-Access-To-Tenants*의 경우 사용자가 액세스할 수 있도록 허용하려는 쉼표로 구분된 테넌트 목록인 \<허용되는 테넌트 목록\> 값. 테넌트에 등록된 모든 도메인은 이 목록의 테넌트를 식별하는 데 사용할 수 있습니다. 예를 들어 Contoso 및 Fabrikam 테넌트 둘 다에 대한 액세스를 허용하기 위해 이름/값 쌍은 다음과 같이 지정됩니다. `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- *Restrict-Access-Context*의 경우 테넌트 제한을 설정하는 테넌트를 선언하는 단일 디렉터리 ID 값. 예를 들어 Contoso를 테넌트 제한 정책을 설정하는 테넌트로 선언하기 위해 이름/값 쌍이 다음과 같이 지정됩니다. `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> [Azure Portal](https://portal.azure.com)에서 디렉터리 ID를 찾을 수 있습니다. 관리자로 로그인한 후 **Azure Active Directory**를 선택하고 **속성**을 선택합니다.

사용자가 승인되지 않은 테넌트에 와 자체 HTTP 헤더를 삽입하지 못하도록 하기 위해 프록시는 Restrict-Access-To-Tenants 헤더가 들어오는 요청에 이미 있는 경우 바꾸어야 합니다. 

클라이언트는 Login.microsoftonline.com, login.microsoft.com 및 login.windows.net에 대한 모든 요청에 강제로 해당 프록시를 사용하도록 지정되어야 합니다. 예를 들어 PAC 파일을 사용하여 클라이언트가 프록시를 사용하도록 하면 최종 사용자는 PAC 파일을 편집하거나 사용하지 않도록 설정할 수 없습니다.

## <a name="the-user-experience"></a>사용자 환경

이 섹션에는 최종 사용자와 관리자를 위한 환경이 나와 있습니다.

### <a name="end-user-experience"></a>최종 사용자 환경

예제 사용자는 Contoso 네트워크에 있지만 Outlook 온라인과 같은 공유 SaaS 응용 프로그램의 Fabrikam 인스턴스에 액세스하려고 합니다. Contoso가 해당 인스턴스에 대해 허용되지 않는 테넌트인 경우 다음과 같은 페이지가 표시됩니다.

![허용되지 않는 테넌트의 사용자에 대한 액세스 거부 페이지](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>관리자 환경

기업 프록시 인프라에서 테넌트 제한 구성이 완료되면 관리자는 Azure Portal의 테넌트 제한 보고서에 직접 액세스할 수 있습니다. 이러한 보고서를 보려면 Azure Active Directory 개요 페이지로 이동한 후 '기타 기능'을 확인합니다.

Restricted-Access-Context 테넌트로 지정된 테넌트의 관리자는 이 보고서를 사용하여 사용된 ID 및 대상 디렉터리 ID를 비롯하여 테넌트 제한 정책으로 인해 차단된 모든 로그인을 확인할 수 있습니다.

![Azure Portal을 사용하여 제한된 로그인 시도 확인](./media/active-directory-tenant-restrictions/portal-report.png)

Azure Portal의 다른 보고서와 마찬가지로, 필터를 사용하여 보고서의 범위를 지정할 수 있습니다. 특정 사용자, 응용 프로그램, 클라이언트 또는 시간 간격에 따라 필터링을 수행할 수 있습니다.

## <a name="office-365-support"></a>Office 365 지원

Office 365 응용 프로그램은 테넌트 제한을 완전히 지원하기 위해 다음과 같은 두 가지 조건을 충족해야 합니다.

1. 사용되는 클라이언트에서 최신 인증을 지원해야 합니다.
2. 최신 인증이 클라우드 서비스에 대한 기본 인증 프로토콜로 사용되어야 합니다.

현재 최신 인증을 지원하는 Office 클라이언트에 대한 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)을 참조하세요. 해당 페이지에는 특정 Exchange Online 및 비즈니스용 Skype Online 테넌트에 대해 최신 인증을 사용하도록 설정하기 위한 지침으로 연결되는 링크도 포함되어 있습니다. 최신 인증은 기본적으로 SharePoint Online에서 사용되도록 설정됩니다.

테넌트 제한은 현재, Office 365 브라우저 기반 응용 프로그램(Office 포털, Yammer, SharePoint 사이트, 웹용 Outlook 등)에서 지원됩니다. 씩 클라이언트(Outlook, 비즈니스용 Skype, Word, Excel, PowerPoint 등) 테넌트 제한은 최신 인증을 사용하는 경우에만 적용할 수 있습니다.  

최신 인증을 지원하는 Outlook 및 비즈니스용 Skype 클라이언트는 최신 인증이 사용되도록 설정되지 않은 테넌트에 대해 레거시 프로토콜을 사용하여 테넌트 제한을 효과적으로 우회할 수 있습니다. 레거시 프로토콜을 사용하는 응용 프로그램은 인증하는 동안 login.microsoftonline.com, login.microsoft.com 또는 login.windows.net에 접속하는 경우 테넌트 제한으로 차단될 수 있습니다.

Windows의 Outlook에서 고객은 최종 사용자가 프로필에 승인되지 않은 메일 계정을 추가하는 것을 방지하는 제한을 구현하도록 선택할 수 있습니다. 예제를 보려면 [비기본 Exchange 계정 추가 금지](http://gpsearch.azurewebsites.net/default.aspx?ref=1) 그룹 정책 설정을 참조하세요. Windows가 아닌 플랫폼의 Outlook과 모든 플랫폼의 비즈니스용 Skype에서는 현재 테넌트 제한을 완벽하게 지원하지 않습니다.

## <a name="testing"></a>테스트

전체 조직을 위해 구현하기 전에 테넌트 제한을 시험해보려면 Fiddler와 같은 도구를 사용한 호스트 기반 접근 방법과 프록시 설정의 단계별 롤아웃 방법 중에서 선택할 수 있습니다.

### <a name="fiddler-for-a-host-based-approach"></a>호스트 기반 접근 방식을 위한 Fiddler

Fiddler는 HTTP 헤더 삽입을 비롯하여 HTTP/HTTPS 트래픽을 캡처하고 수정하는 데 사용할 수 있는 무료 웹 디버깅 프록시입니다. 테넌트 제한을 테스트하도록 Fiddler를 구성하려면 다음 단계를 수행합니다.

1.  [Fiddler 다운로드하고 설치합니다](http://www.telerik.com/fiddler).
2.  [Fiddler 도움말 설명서](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)에 따라 HTTPS 트래픽 암호를 해독하도록 Fiddler를 구성합니다.
3.  사용자 지정 규칙을 사용하여 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context* 머리글을 삽입하도록 Fiddler를 구성합니다.
  1. Fiddler Web Debugger 도구에서 **Rules** 메뉴를 선택하고 **Customize Rules...** 를 선택하여 CustomRules 파일을 엽니다.
  2. 다음 줄은 *OnBeforeRequest* 함수 맨 앞에 추가합니다. \<tenant domain\>을 테넌트에 등록된 도메인(예: contoso.onmicrosoft.com)으로 바꿉니다. \<directory ID\>를 테넌트의 Azure AD GUID 식별자로 바꿉니다.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  여러 테넌트를 허용해야 하는 경우 쉼표로 테넌트 이름을 구분합니다. 예: 

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. CustomRules 파일을 저장한 후 닫습니다.

Fiddler를 구성한 후 **파일** 메뉴로 이동하고 **트래픽 캡처**를 선택하여 트래픽을 캡처할 수 있습니다.

### <a name="staged-rollout-of-proxy-settings"></a>프록시 설정의 단계별 롤아웃

프록시 인프라의 기능에 따라 사용자에게 설정을 단계별로 롤아웃할 수 있습니다. 다음은 고려해야 할 몇 가지 고급 옵션입니다.

1.  일반 사용자는 프로덕션 프록시 인프라를 계속 사용할 수 있지만 PAC 파일을 사용하여 테스트 사용자는 테스트 프록시 인프라를 가리키도록 합니다.
2.  일부 프록시 서버는 그룹을 사용하여 다양한 구성을 지원할 수 있습니다.

특정 세부 정보에 대해서는 프록시 서버 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [업데이트된 Office 365 최신 인증](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) 참조

- [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 검토
