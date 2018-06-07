---
title: Azure Active Directory에서 엔터프라이즈 앱에 대한 Single Sign-On 관리 | Microsoft Docs
description: Azure Active Directory 응용 프로그램 갤러리에서 조직 내 엔터프라이즈 앱에 대한 Single Sign-On 설정 관리
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 548bd4ca9e8ec66e4c50ea078dc0bccc1788095a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>엔터프라이즈 앱에 대한 Single Sign-On 관리

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 엔터프라이즈 응용 프로그램에 대한 Single Sign-On 설정을 관리하는 방법을 설명합니다. 엔터프라이즈 앱은 조직 내에서 배포 및 사용되는 앱입니다. 이 문서는 [Azure Active Directory 응용 프로그램 갤러리](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)에서 추가된 앱에 특별히 적용됩니다. 

## <a name="finding-your-apps-in-the-portal"></a>포털에서 앱 찾기
Single Sign-On에 대해 설정되어 있는 모든 엔터프라이즈 앱은 Azure Portal에서 보고 관리할 수 있습니다. 응용 프로그램은 포털의 **모든 서비스** &gt; **엔터프라이즈 응용 프로그램** 섹션에서 찾을 수 있습니다. 

![엔터프라이즈 응용 프로그램 블레이드](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.png)

**모든 응용 프로그램**을 선택하여 구성된 모든 앱의 목록을 봅니다. 앱을 선택하면 해당 앱에 대한 리소스가 표시되며 여기에서 해당 앱에 대한 보고서를 볼 수 있고 다양한 설정을 관리할 수 있습니다.

Single Sign-On 설정을 관리하려면 **Single Sign-On**을 선택합니다.

![응용 프로그램 리소스 블레이드](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Single Sign-On 모드
**Single Sign-On**은 Single Sign-On 모드를 구성할 수 있는 **모드** 메뉴로 시작합니다. 사용 가능한 옵션은 다음과 같습니다.

* **SAML 기반 로그온** - 응용 프로그램에서 SAML 2.0 프로토콜, WS-Federation 또는 OpenID 연결 프로토콜을 사용하여 Azure Active Directory로 전체 페더레이션된 Single Sign-On을 지원하는 경우에 사용할 수 있습니다.
* **암호 기반 로그온** - 이 옵션은 Azure AD가 이 응용 프로그램에 대해 입력하는 암호 양식을 지원하는 경우 사용할 수 있습니다.
* **연결된 로그온** - 이전에 “기존 Single Sign-On”이었던 이 옵션을 사용하면 관리자가 해당 사용자의 Azure AD 액세스 패널 또는 Office 365 응용 프로그램 시작 관리자에서 이 응용 프로그램에 대한 링크를 배치할 수 있습니다.

이러한 모드에 대한 자세한 내용은 [Azure Active Directory에서 Single Sign-On이 작동하는 방식](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)을 참조하세요.

## <a name="saml-based-sign-on"></a>SAML 기반 로그온
**SAML 기반 로그온** 옵션은 4개의 섹션으로 나뉩니다.

### <a name="domains-and-urls"></a>도메인 및 URL
여기에서 응용 프로그램의 도메인 및 URL에 대한 모든 세부 정보가 Azure AD 디렉터리에 추가됩니다. Single Sign-On 작업 앱을 만드는 데 필요한 모든 입력이 화면에 직접 표시되지만 모든 선택 사항 입력은 **Show advanced URL settings** (고급 URL 설정 표시) 확인란을 선택하여 볼 수 있습니다. 지원되는 입력의 전체 목록에는 다음이 포함됩니다.

* **로그온 URL** – 사용자가 이 응용 프로그램에 로그인하는 위치입니다. 응용 프로그램이 서비스 공급자에서 시작된 Single Sign-On을 수행하도록 구성되면 사용자가 이 URL을 열 경우 서비스 공급자는 사용자를 인증하고 로그인하기 위해 Azure AD로 리디렉션합니다. 
  * 이 필드가 채워지면 Azure AD는 URL을 사용하여 Office 365 및 Azure AD 액세스 패널에서 응용 프로그램을 시작합니다.
  * 이 필드가 생략되면 해당 앱이 Office 365, Azure AD 액세스 패널 또는 Azure AD Single Sign-On URL에서 시작할 경우 Azure AD는 ID 공급자에서 시작된 로그인을 대신 수행합니다.
* **식별자** - 이 URI는 구성될 Single Sign-On에 대해 응용 프로그램을 고유하게 식별해야 합니다. 이는 Azure AD가 SAML 토큰의 대상 매개 변수로서 응용 프로그램에 다시 전송하는 값이며, 응용 프로그램의 유효성을 검사하게 됩니다. 또한 이 값은 응용 프로그램에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다.
* **회신 URL** - 회신 URL은 응용 프로그램이 SAML 토큰을 수신해야 하는 위치입니다. 이 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. 이러한 내용을 입력한 후에 다음을 클릭하여 다음 화면으로 진행합니다. 이 화면은 응용 프로그램쪽에서 구성되어야 하는 사항에 대한 정보를 제공하여 Azure AD에서 SAML 토큰을 수락하도록 설정합니다.
* **릴레이 상태** - 릴레이 상태는 인증이 완료된 후 사용자를 리디렉션할 위치를 응용 프로그램에 알릴 수 있는 선택적 매개 변수입니다. 일반적으로 해당 값은 응용 프로그램에서 유효한 URL이지만 일부 응용 프로그램은 이 필드를 다르게 사용합니다(자세한 내용은 앱의 Single Sign-On 설명서 참조). 릴레이 상태를 설정하는 기능은 새 Azure Portal에 고유한 새 기능입니다.

### <a name="user-attributes"></a>사용자 특성
여기에서 관리자는 사용자 로그인마다 Azure AD가 응용 프로그램에 발행하는 SAML 토큰에서 전송되는 특성을 보고 편집할 수 있습니다.

편집 가능하며 지원되는 유일한 특성은 **사용자 ID** 특성입니다. 이 특성의 값은 응용 프로그램 내에서 각 사용자를 고유하게 식별하는 Azure AD의 필드입니다. 예를 들어 "메일 주소"를 사용자 이름 및 고유 식별자로 사용하여 앱이 배포된 경우 해당 값은 Azure AD의 "user.mail" 필드로 설정됩니다.

### <a name="saml-signing-certificate"></a>SAML 서명 인증서
이 섹션에서는 Azure AD가 사용자 인증마다 응용 프로그램에 발급된 SAML 토큰을 서명하는 데 사용하는 인증서의 세부 정보를 보여 줍니다. 이를 통해 만료 날짜를 포함하여 현재 인증서의 속성을 검사할 수 있습니다.

### <a name="application-configuration"></a>응용 프로그램 구성
마지막 섹션에서는 Azure Active Directory를 ID 공급자로 사용하도록 응용 프로그램 자체를 구성하는 데 필요한 설명서 및/또는 컨트롤을 제공합니다.

**응용 프로그램 구성** 플라이아웃 메뉴에서 응용 프로그램을 구성하기 위해 포함된 간결한 새 지침을 제공합니다. 이는 새 Azure Portal에 고유한 또 다른 새 기능입니다.

> [!NOTE]
> 포함된 설명서의 전체 예제를 보려면 Salesforce.com 응용 프로그램을 참조하세요. 추가 앱에 대한 설명서는 계속 추가됩니다.
> 
> 

![포함된 문서](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>암호 기반 로그온
응용 프로그램을 지원하는 경우 암호 기반 SSO 모드를 선택하고 **저장** 을 선택하면 즉시 구성되어 암호 기반 SSO를 수행합니다. 암호 기반 SSO를 배포하는 방법은 [Azure Active Directory에서 Single Sign-On이 작동하는 방식](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)을 참조하세요.

![암호 기반 로그온](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>연결된 로그온
응용 프로그램을 지원하는 경우 연결된 SSO 모드를 선택하면 사용자가 이 앱을 클릭할 때 Azure AD 액세스 패널 또는 Office 365가 리디렉션하는 URL을 입력할 수 있습니다. 연결된 SSO(이전의 "기존 SSO")에 대한 자세한 내용은 [Azure Active Directory에서 Single Sign-On이 작동하는 방식](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)을 참조하세요.

![연결된 로그온](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>사용자 의견

향상된 Azure AD 환경 사용이 사용자의 마음에 들기를 바랍니다. 사용자 의견을 계속 보내주세요! [피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)의 **관리자 포털Admin Portal** 섹션에서 개선을 위한 의견과 아이디어를 게시합니다.  매일 멋진 새로운 기능을 구축하는 방법을 기대하며, 사용자의 지침에 따라 다음에 구축할 기능을 구체화하고 정의하겠습니다.

