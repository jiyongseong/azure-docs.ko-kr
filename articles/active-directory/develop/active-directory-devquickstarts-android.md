---
title: Azure AD Android 시작 | Microsoft Docs
description: 로그인을 위해 Azure AD와 통합되고 OAuth 2.0을 사용하여 Azure AD로 보호되는 API를 호출하는 Android 응용 프로그램 빌드 방법
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155697"
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android 시작
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Android 응용 프로그램을 개발하는 분들은 Azure AD(Azure Active Directory) 사용자를 간단하게 로그인할 수 있습니다. Azure AD를 사용하면 응용 프로그램이 Microsoft Graph를 통해 또는 개발자 고유의 보호되는 web API를 통해 사용자 데이터에 액세스할 수 있습니다. 

Azure ADAL(AD 인증 라이브러리) Android 라이브러리는 산업 표준 OAuth2 및 OpenID Connect를 사용하는 [Microsoft Azure Active Directory 계정](https://azure.microsoft.com/services/active-directory/)을 지원함으로써 앱에 [Microsoft Azure Cloud](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) 사용을 시작할 수 있는 기능을 제공합니다. 이 샘플은 다음을 포함하여 응용 프로그램에서 발생하게 될 모든 기본 주기를 보여줍니다.

* Microsoft Graph에 대한 토큰 가져오기
* 토큰 새로 고침
* Microsoft Graph 호출
* 사용자 로그아웃

시작하려면 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트가 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>시나리오: 사용자를 로그인하고 Microsoft Graph를 호출

![토폴로지](./media/active-directory-android-topology.png)

이 앱은 모든 Azure AD 계정에 사용할 수 있습니다. 단일 및 다중 조직 시나리오를 모두 지원합니다(단계별로 설명). 개발자가 Microsoft Graph를 통해 엔터프라이즈 사용자와 연결하고 Azure + O365 데이터에 액세스하는 앱 빌드 방법을 보여줍니다. 인증 흐름에서 최종 사용자는 응용 프로그램에 로그인하고 사용 권한에 동의해야 하며, 경우에 따라 관리자가 앱에 동의해야 할 수도 있습니다. 이 샘플에 포함된 대부분의 논리는 최종 사용자를 인증하고 기본적인 Microsoft Graph 호출 방법을 보여줍니다.

## <a name="example-code"></a>예제 코드

전체 샘플 코드는 [Github](https://github.com/Azure-Samples/active-directory-android)에서 찾을 수 있습니다. 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>실행할 단계

### <a name="register-and-configure-your-app"></a>앱 등록 및 구성 
[Azure Portal](https://portal.azure.com)을 사용하여 네이티브 클라이언트 응용 프로그램을 Microsoft에 등록해야 합니다. 

1. 앱 등록
    - [Azure Portal](https://aad.portal.azure.com)로 이동합니다. 
    - ***Azure Active Directory*** > ***앱 등록***을 선택합니다. 

2. 앱 만들기
    - **새 응용 프로그램 등록**을 선택합니다. 
    - **이름** 필드에 앱 이름을 입력합니다. 
    - **응용 프로그램 유형**에서 **네이티브**를 선택합니다. 
    - **리디렉션 URI**로 `http://localhost`를 입력합니다. 

3. Microsoft Graph 구성
    - **설정 > 필요한 권한**을 선택합니다.
    - **추가**를 선택하고, **API 선택** 안에서 ***Microsoft Graph***를 선택합니다. 
    - **로그인 및 사용자 프로필 읽기** 권한을 선택한 다음, **선택**을 눌러 저장합니다. 
        - 이 권한은 `User.Read` 범위에 매핑됩니다. 
    - 선택 사항: **필요한 권한 > Windows Azure Active Directory** 안에서, 선택한 **로그인 및 사용자 프로필 읽기** 권한을 제거합니다. 이렇게 하면 사용자 동의 페이지에 권한이 두 번 나열되지 않습니다. 

4. 축하합니다! 앱이 구성되었습니다. 그 다음 섹션에서는 다음 작업을 수행해야 합니다.
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>샘플 코드 가져오기

1. 코드를 복제합니다.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Android Studio에서 샘플을 엽니다.
    - **기존 Android Studio 프로젝트 열기**를 선택합니다.

### <a name="configure-your-code"></a>코드 구성

이 코드 샘플에 대한 모든 구성은 ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** 파일에서 찾을 수 있습니다. 

1. 상수 `CLIENT_ID`를 `ApplicationID`로 바꿉니다.
2. 상수 `REDIRECT URI`를 앞에서 구성한 `Redirect URI`(`http://localhost`)로 바꿉니다. 

### <a name="run-the-sample"></a>샘플 실행

1. **빌드 > 프로젝트 정리**를 선택합니다. 
2. **실행 > 앱 실행**을 선택합니다. 
3. 앱이 빌드되고 몇 가지 기본 사용자 환경이 표시됩니다. `Call Graph API` 단추를 클릭하면 로그인하라는 메시지가 표시된 후 새 토큰을 사용하여 자동으로 Microsoft Graph API가 호출됩니다. 

## <a name="important-info"></a>중요한 정보

1. [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)에서 라이브러리 메커니즘과 새로운 시나리오 및 기능을 구성하는 방법에 대한 정보를 확인하세요. 
2. 네이티브 시나리오에서는 앱이 Webview를 사용하므로 앱에서 나가지 않습니다. `Redirect URI`는 임의로 지정될 수 있습니다. 
3. 문제가 있거나 궁금한 점이 있나요? Stackoverflow에서 `azure-active-directory` 태그를 사용하여 문제를 작성하거나 게시할 수 있습니다. 

### <a name="cross-app-sso"></a>앱 간 SSO
[ADAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법](active-directory-sso-android.md)을 알아봅니다. 

### <a name="auth-telemetry"></a>인증 원격 분석
ADAL 라이브러리는 앱 개발자가 앱의 동작을 이해하여 보다 나은 환경을 빌드할 수 있도록 인증 원격 분석을 노출합니다. 따라서 개발자는 로그인 성공, 활성 사용자 및 기타 여러 가지 흥미로운 인사이트를 캡처할 수 있습니다. 인증 원격 분석을 사용하려면 앱 개발자는 이벤트를 집계하고 저장하는 원격 분석 서비스를 설정해야 합니다.

인증 원격 분석에 대한 자세한 내용은 [ADAL Android 인증 원격 분석](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry)을 확인하세요. 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
