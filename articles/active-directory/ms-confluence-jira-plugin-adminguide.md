---
title: Azure Active Directory SSO 플러그 인에 대한 관리자 가이드 | Microsoft Docs
description: Azure Active Directory 및 Jira/Confluence 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO 플러그 인에 대한 관리자 가이드

## <a name="overview"></a>개요

Azure AD(Azure Active Directory) SSO(Single Sign-On) 플러그 인을 통해 Microsoft Azure AD 고객은 Atlassian Jira 및 Confluence 서버 기반 제품에 로그인하는 데 해당 회사 또는 학교 계정을 사용할 수 있습니다. SAML 2.0 기반 SSO를 구현합니다.

## <a name="how-it-works"></a>작동 방법

사용자가 Atlassian Jira 또는 Confluence 응용 프로그램에 로그인하려는 경우 로그인 페이지에 **Azure AD로 로그인** 단추가 보입니다. 해당 단추를 선택하면 Azure AD 조직 로그인 페이지(즉, 회사 또는 학교 계정)를 사용하여 로그인해야 합니다.

사용자가 인증되면 응용 프로그램에 로그인할 수 있습니다. 회사 또는 학교 계정의 ID 및 암호를 사용하여 이미 인증된 경우 응용 프로그램에 직접 로그인합니다. 

로그인은 Jira 및 Confluence에서도 작동합니다. 사용자가 Jira 응용 프로그램에 로그인하고 같은 브라우저 창에서 Confluence도 열리는 경우 다른 앱에 자격 증명을 제공할 필요가 없습니다. 

사용자는 회사 또는 학교 계정으로 My Apps를 통해 Atlassian 제품을 얻을 수도 있습니다. 자격 증명을 입력하지 않고 로그인되어야 합니다.

> [!NOTE]
> 사용자 프로비전은 플러그 인을 통해 수행되지 않습니다.

## <a name="audience"></a>대상

Jira 및 Confluence 관리자는 Azure AD를 사용하여 SSO를 활성화하도록 플러그 인 사용할 수 있습니다.

## <a name="assumptions"></a>가정

* Jira 및 Confluence 인스턴스는 HTTPS를 사용하도록 설정됩니다.
* 사용자는 Jira 또는 Confluence에서 이미 생성되었습니다.
* 사용자는 Jira 또는 Confluence에서 할당된 역할을 갖습니다.
* 관리자는 플러그 인 구성에 필요한 정보에 액세스할 수 있습니다.
* 회사 네트워크 외부에서도 Jira 또는 Confluence를 사용할 수 있습니다.
* 플러그 인은 Jira 및 Confluence의 온-프레미스 버전에서만 작동합니다.

## <a name="prerequisites"></a>필수 조건

플러그 인을 설치하기 전에 다음 정보를 참조하세요.

* Windows 64비트 버전에 Jira 및 Confluence가 설치되어 있습니다.
* Jira 및 Confluence 버전에서 HTTPS가 사용됩니다.
* Jira 및 Confluence는 인터넷에서 사용 가능합니다.
* 관리자 자격 증명은 Jira 및 Confluence에 대해 준비되어 있습니다.
* 관리자 자격 증명은 Azure AD에 대해 준비되어 있습니다.
* WebSudo는 Jira 및 Confluence에서 비활성화됩니다.

## <a name="supported-versions-of-jira-and-confluence"></a>지원되는 Jira 및 Confluence 버전

플러그 인에서는 다음 버전의 Jira 및 Confluence를 지원합니다.

* Jira Core 및 소프트웨어: 6.0 ~ 7.2.0
* Jira Service Desk: 3.0 ~ 3.2
* Confluence: 5.0 ~ 5.10

## <a name="installation"></a>설치

플러그 인을 설치하려면 다음 단계를 따르세요.

1. Jira 또는 Confluence 인스턴스에 관리자로 로그인합니다.
    
2. Jira/Confluence 관리 콘솔로 이동하고 **추가 기능**을 선택합니다.
    
3. Atlassian Market place에서 **Microsoft SAML SSO 플러그 인**을 검색합니다.
 
   적절한 버전의 플러그 인이 검색 결과에 나타납니다.
 
5. 플러그 인을 선택하면 UPM(범용 플러그 인 관리자)에서 설치합니다.
 
플러그 인이 설치되면 **추가 기능 관리**의 **사용자가 설치한 추가 기능** 섹션에 표시됩니다.
    
## <a name="plug-in-configuration"></a>플러그 인 구성

플러그 인 사용을 시작하기 전에 구성해야 합니다. 플러그 인을 선택하고, **구성** 단추를 선택하고, 구성 세부 정보를 제공합니다.

다음 이미지는 Jira 및 Confluence의 구성 화면을 보여줍니다.
    
![플러그 인 구성 화면](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **메타데이터 URL**: Azure AD에서 페더레이션 메타데이터를 가져오는 URL입니다.
 
*   **식별자**: Azure AD가 요청의 원본 유효성을 검사하는 데 사용하는 URL입니다. Azure AD의 **식별자** 요소에 매핑됩니다. 플러그 인은 https://*<domain:port>*/로 이 URL을 자동으로 파생합니다.
 
*   **회신 URL**: SAML 로그인을 시작하는 IdP(ID 공급자)의 회신 URL입니다. Azure AD의 **회신 URL** 요소에 매핑됩니다. 플러그 인은 https://*<domain:port>*/plugins/servlet/saml/auth로 이 URL을 자동으로 파생합니다.
 
*   **로그온 URL**: SAML 로그인을 시작하는 IdP의 로그온 URL입니다. Azure AD의 **로그온** 요소에 매핑됩니다. 플러그 인은 https://*<domain:port>*/plugins/servlet/saml/auth로 이 URL을 자동으로 파생합니다.
 
*   **IdP 엔터티 ID**: IdP에서 사용하는 엔터티 ID입니다. 이 상자는 메타데이터 URL을 확인할 때 채워집니다.
 
*   **로그인 URL**: IdP의 로그인 URL입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
*   **로그아웃 URL**: IdP의 로그아웃 URL입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
*   **X.509 인증서**: IdP의 X.509 인증서입니다. 이 상자는 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
*   **로그인 단추 이름**: 조직이 사용자에게 로그인 페이지에 표시하려는 로그인 단추의 이름입니다.
 
*   **SAML 사용자 ID 위치**: SAML 응답에서 Jira 또는 Confluence 사용자 ID가 필요한 위치입니다. **NameID**에 있을 수도 있고 사용자 지정 특성 이름에 있을 수도 있습니다.
 
*   **특성 이름**: 사용자 ID가 필요한 특성 이름입니다.
 
*   **홈 영역 검색 활성화**: 회사에서 AD FS(Active Directory Federation Services) 기반 로그인을 사용하는 경우 선택합니다.
 
*   **도메인 이름**: 로그인이 AD FS 기반인 경우 도메인 이름입니다.
 
*   **Single Signout 활성화**: 사용자가 Jira 또는 Confluence에서 로그아웃할 때 Azure AD에서 로그아웃하려는 경우 선택합니다.

## <a name="troubleshooting"></a>문제 해결

* **여러 인증서 오류를 가져오는 경우**: Azure AD에 로그인하고 앱에 대해 사용할 수 있는 여러 인증서를 제거합니다. 인증서를 하나만 남겨 둡니다.

* **인증서가 Azure AD에서 곧 만료되는 경우**: 추가 기능은 인증서의 자동 롤오버를 처리합니다. 인증서가 곧 만료되는 경우 새 인증서를 활성으로 표시하고 사용하지 않는 인증서를 삭제해야 합니다. 이 시나리오에서 사용자가 Jira에 로그인하려고 시도하면 플러그 인은 새 인증서를 페치하고 저장합니다.

* **WebSudo를 사용하지 않으려는 경우(보안 관리자 세션 해제)**:
    
  * Jira의 경우 보안 관리자 세션(즉, 관리 기능에 액세스하기 전에 암호 확인)이 기본적으로 사용됩니다. Jira 인스턴스에서 이 기능을 제거하려는 경우 jira-config.properties 파일에서 다음 줄을 지정합니다. `ira.websudo.is.disabled = true`
    
  * Confluence의 경우 [Confluence 지원 사이트](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)의 단계를 따릅니다.

* **메타데이터 URL에서 채우는 필드가 채워지지 않는 경우**:
    
  * URL이 올바른지 확인합니다. 올바른 테넌트 및 앱 ID를 매핑했는지 확인합니다.
    
  * 브라우저에서 URL을 입력하고 페더레이션 메타데이터 XML을 수신하는지 확인합니다.

* **내부 서버 오류가 있는 경우**: 설치의 로그 디렉터리에서 로그를 검토합니다. 사용자가 Azure AD SSO를 사용하여 로그인하려고 시도할 때 오류가 발생하는 경우 지원 팀과 함께 로그를 공유할 수 있습니다.

* **사용자가 로그인을 시도할 때 "사용자 ID를 찾을 수 없음" 오류가 있는 경우**: Jira 또는 Confluence에서 사용자 ID를 만듭니다.

* **Azure AD에 "앱을 찾을 수 없음" 오류가 있는 경우**: Azure AD에서 적절한 URL이 앱에 매핑되는지 확인합니다.

* **지원이 필요한 경우**: [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 연락합니다. 팀은 24-48 업무 시간 안에 응답합니다.
    
  또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다.