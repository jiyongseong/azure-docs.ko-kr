---
title: 응용 프로그램을 추가할 때 사용할 응용 프로그램 형식을 선택하는 방법 | Microsoft Docs
description: Azure AD와 통합할 수 있는 지원되는 응용 프로그램의 형식 및 해당 관련 구성 옵션 이해
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a34c3343b669cb80ad88c1b09fe95b1b1d9b5275
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>응용 프로그램을 추가할 때 사용할 응용 프로그램 형식을 선택하는 방법

이 문서는 Azure AD와 통합할 수 있는 네 가지 기본 응용 프로그램 형식을 이해하는 데 도움이 됩니다.

* 각 형식에 의해 지원되는 항목
* 해당 응용 프로그램을 선택할 수도 있는 이유
* 사용자가 **프로비전**되는 방법 또는 사용할 **Single Sign-On** 기술과 같은 해당 응용 프로그램의 핵심 속성을 구성하는 방법.

## <a name="supported-application-types-in-azure-ad"></a>Azure AD에서 지원되는 응용 프로그램 형식

Azure AD는 **엔터프라이즈 응용 프로그램** 아래에 있는 **추가** 기능을 사용하여 추가할 수 있는 네 가지 기본 응용 프로그램 형식을 지원합니다. 내용은 다음과 같습니다.

-   **Azure AD 갤러리 응용 프로그램** – Single Sign-On에 대해 Azure AD와 사전 통합된 응용 프로그램입니다.

-   **응용 프로그램 프록시 응용 프로그램** – 외부로 보안 Single Sign-On을 제공하려는 온-프레미스 환경에서 실행 중인 응용 프로그램입니다.

-   **사용자 지정 개발된 응용 프로그램** – 조직에서 Azure AD 응용 프로그램 개발 플랫폼에서 개발하고자 하지만 아직 존재하지 않을 수 있는 응용 프로그램입니다.

-   **비 갤러리 응용 프로그램** – 사용자의 응용 프로그램을 가져옵니다! 원하는 모든 웹 링크 또는 사용자 이름 및 암호 필드를 렌더링하는 모든 응용 프로그램은 SAML 또는 OpenID Connect 프로토콜을 지원하거나 Single Sign-On에 대해 Azure AD와 통합하려는 SCIM을 지원합니다.

## <a name="features-and-capabilities-supported-by-all-the-preceding-application-types"></a>모든 이전 응용 프로그램 형식에서 지원되는 기능 및 특징

다음 기능은 Azure AD에서 이전 4개의 응용 프로그램 형식 중 하나에서 지원됩니다.

-   **빠른 시작** – [간단한 배포 단계](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)를 수행하여 신속하게 응용 프로그램 시작

-   **일반 속성 관리** – 응용 프로그램에 [직접 딥 링크](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) 가져오기, 응용 프로그램의 [브랜딩 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) 또는 모든 사용자에 대한 [응용 프로그램을 사용하지 않도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

-   **사용자 및 그룹 관리** – 사용자 및 그룹을 응용 프로그램에 [할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) 또는 [제거](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) 및 경우에 따라 액세스를 가진 이러한 사용자 및 그룹에 특정 응용 프로그램 역할 할당

-   **셀프 서비스 응용 프로그램 액세스** - 사용자가 응용 프로그램을 직접 추가하거나 [셀프 서비스가 활성화된 그룹에 가입](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)하거나 경우에 따라 비즈니스 승인을 요구하여 응용 프로그램 액세스 패널에서 응용 프로그램에 대한 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)를 요청하도록 함

-   **로그인 로그** – [응용 프로그램에 대한 모든 로그인](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) 또는 모든 응용 프로그램 보기

-   **감사 로그** – [응용 프로그램 수정에 대한 자세한 감사 로그](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) 또는 모든 응용 프로그램 보기

-   **조건부 및 리스크 기반 액세스** - 사용자가 특정 응용 프로그램에 대한 로그인을 시도할 때 적용되는 강력한 [조건 기반 액세스 규칙](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) 설정

-   **사용 권한 보기** – 응용 프로그램이 단일 위치의 디렉터리에서 액세스를 갖는 [OAuth2 권한](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) 중 하나 보기

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>특정 응용 프로그램 형식에서 지원되는 Single Sign-On 및 프로비전 모드

다음 표는 이전 각 응용 프로그램 형식에서 지원하는 서로 다른 Single Sign-On 및 프로비전 모드를 설명합니다. 이 표를 사용하여 특정 목표를 지원하기 위해 추가해야 하는 응용 프로그램을 이해할 수 있습니다.

  ![앱 형식 테이블](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Single Sign-On 모드를 선택하는 방법

다음은 Azure AD 응용 프로그램에 대해 지원되는 **Single Sign-On** 모드입니다.

-   **Azure AD Single Sign-On 사용 안 함** – 이 응용 프로그램을 Single Sign-On을 사용하여 Azure AD와 통합할 준비가 되지 않았거나 단순히 테스트하는 경우 Azure AD Single Sign-On 사용 안 함 **Single Sign-On 모드** 선택

-   **연결된 로그온** - 기존 Single Sign-On 솔루션에 연결된 응용 프로그램이 있거나 [응용 프로그램 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 또는 [Office 365 응용 프로그램 시작 관리자](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)에서 사용자에게 간단한 링크를 배포하려는 경우 [연결된 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **Single Sign-On 모드** 선택

-   **암호 기반 로그온** - 응용 프로그램이 HTML 사용자 이름 및 암호 필드를 렌더링하고 해당 사용자 이름 및 암호가 나중에 응용 프로그램에 재생되도록 안전하게 저장하려는 경우 [암호 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **Single Sign-On 모드** 선택

-   **SAML 기반 로그온** - 응용 프로그램이 SAML 또는 OpenID Connect 프로토콜을 지원하거나 사용자를 SAML 클레임에서 정의하는 규칙에 따라 특정 응용 프로그램 역할에 매핑할 수 있게 되기를 원하는 경우 [SAML 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) Single Sign-On 모드 선택 *

   >[!NOTE]
   >응용 프로그램 프록시가 응용 프로그램에 대해 구성된 경우 이 옵션은 사용할 수 없습니다.
   >
   >

-   **헤더 기반 로그온** – Single Sign-On을 수행하려는 HTTP 헤더 기반 인증을 지원하는 PingAccess를 사용하는 응용 프로그램이 있는 경우 이 [헤더 기반 로그온](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) Single Sign-On 모드 선택 

   >[!NOTE]
   >응용 프로그램 프록시 및 PingAccess가 응용 프로그램에 대해 구성된 경우에만 이 옵션을 사용할 수 있습니다.
   >
   >

-   **Windows 통합 인증** - Single Sign-On을 수행하려는 온-프레미스 WIA 응용 프로그램을 노출하는 경우 [Windows 통합 인증](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) Single Sign-On 모드 선택 

   >[!NOTE]
   >응용 프로그램 프록시가 응용 프로그램에 대해 구성된 경우에만 이 옵션을 사용할 수 있습니다.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>사용자 지정 개발된 응용 프로그램에 대한 Single Sign-On 모드

[사용자 지정 개발된 응용 프로그램](#_Custom-Developed_Applications) 환경을 통해 사용자 지정 개발한 응용 프로그램은 이전에 나열되지 않은 추가 Single Sign-On 모드도 지원하며, 다음을 포함합니다.

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 기반 로그온

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 기반 로그온

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) 기반 로그온

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) 기반 로그온

이러한 Single Sign-On 모드를 지원하는 사용자 지정 개발된 응용 프로그램을 만드는 방법에 대해 자세히 알아보려면 [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)를 참조하세요.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>응용 프로그램의 Single Sign-On 모드를 설정하는 방법

응용 프로그램의 **Single Sign-On** 모드를 설정하려면 아래 지침을 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

## <a name="how-to-choose-a-provisioning-mode"></a>프로비전 모드를 선택하는 방법

-   **수동 프로비전** - 기존 계정이 있거나 Azure AD 외부에서 이 응용 프로그램에 대한 계정을 관리하려는 경우 [수동](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) 프로비전 모드 선택

-   **자동 프로비전** - 자동 API 기반 프로비전 및/또는 이 응용 프로그램에 사용자 계정의 프로비전 해제를 활성화하려면 [자동](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **프로비전 모드** 선택 

   >[!NOTE]
   >이 옵션은 [Azure AD 응용 프로그램 갤러리](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery)의 **주요** 범주 내의 응용 프로그램에만 사용할 수 있습니다.
   >
   >

-   **SCIM 기반 자동 프로비전** - 응용 프로그램이 Azure AD와 통합된 모든 응용 프로그램에 변경 내용을 자동으로 내보내는 사용자 및 그룹에 대한 변경 내용 감지에 SCIM 프로토콜을 지원하는 경우 [SCIM 기반 자동 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) 사용 

   >[!NOTE]
   >이 옵션은 특정 프로비전 모드로 나열되어 있지 않지만 Azure AD와 통합된 모든 응용 프로그램에 기본적으로 활성화됩니다.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>응용 프로그램의 프로비전 모드를 설정하는 방법

응용 프로그램의 **프로비전** 모드를 설정하려면 아래 지침을 따릅니다.

응용 프로그램의 **Single Sign-On** 모드를 설정하려면 아래 지침을 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  프로비전을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **프로비전**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)
