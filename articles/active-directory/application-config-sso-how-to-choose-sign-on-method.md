---
title: 사용할 Single Sign-On 방법을 결정하는 방법 | Microsoft Docs
description: Azure AD에서 지원되는 Single Sign-On 모드 및 관심 있는 응용 프로그램에 대해 사용할 모드를 선택하는 방법을 이해합니다.
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
ms.openlocfilehash: 52f4045ebb30bf32989e9f7b946fd03d468a9de2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34070671"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>사용할 Single Sign-On 방법을 결정하는 방법

이 문서에서는 Azure AD에서 지원되는 Single Sign-On 모드 및 관심 있는 응용 프로그램에 대해 사용할 모드를 선택하는 방법을 이해하는 데 도움을 줍니다.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>특정 응용 프로그램 형식에서 지원되는 Single Sign-On 및 프로비전 모드

다음 표는 이전 각 응용 프로그램 형식에서 지원하는 서로 다른 Single Sign-On 및 프로비전 모드를 설명합니다. 이 표를 사용하여 특정 목표를 지원하기 위해 추가해야 하는 응용 프로그램을 이해할 수 있습니다.

  ![앱 형식 표](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Single Sign-On 모드를 선택하는 방법

다음은 Azure AD 응용 프로그램에 대해 지원되는 **Single Sign-On** 모드입니다.

-   **Azure AD Single Sign-On 사용 안 함** – 이 응용 프로그램을 Single Sign-On을 사용하여 Azure AD와 통합할 준비가 되지 않았거나 단순히 테스트하는 경우 Azure AD Single Sign-On 사용 안 함 **Single Sign-On 모드** 선택

-   **연결된 로그온** - 기존 Single Sign-On 솔루션에 연결된 응용 프로그램이 있거나 [응용 프로그램 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 또는 [Office 365 응용 프로그램 시작 관리자](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)에서 사용자에게 간단한 링크를 배포하려는 경우 [연결된 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **Single Sign-On 모드** 선택

-   **암호 기반 로그온** - 응용 프로그램이 HTML 사용자 이름 및 암호 필드를 렌더링하고 해당 사용자 이름 및 암호가 나중에 응용 프로그램에 재생되도록 안전하게 저장하려는 경우 [암호 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **Single Sign-On 모드** 선택

-   **SAML 기반 로그온** - 응용 프로그램이 SAML 또는 OpenID Connect 프로토콜을 지원하거나 사용자를 SAML 클레임에서 정의하는 규칙에 따라 특정 응용 프로그램 역할에 매핑할 수 있게 되기를 원하는 경우 [SAML 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) Single Sign-On 모드 선택 *(**참고:** 응용 프로그램 프록시가 응용 프로그램에 대해 구성되어 있으면 이 옵션을 사용할 수 없음)*

-   **헤더 기반 로그온** – Single Sign-On을 수행하려는 HTTP 헤더 기반 인증을 지원하는 PingAccess를 사용하는 응용 프로그램이 있는 경우 이 [헤더 기반 로그온](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) Single Sign-On 모드 선택 *(**참고:** 응용 프로그램 프록시 및 PingAccess가 응용 프로그램에 대해 구성되어 있을 때만 이 옵션을 사용할 수 있음)*

-   **Windows 통합 인증** - Single Sign-On을 수행하려는 온-프레미스 WIA 응용 프로그램을 노출하는 경우 [Windows 통합 인증](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) Single Sign-On 모드 선택 *(**참고:** 응용 프로그램 프록시가 응용 프로그램에 대해 구성되어 있을 때만 이 옵션을 사용할 수 있음)*

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

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 사용하여 앱에 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

