---
title: 'Azure Active Directory B2C: Android 응용 프로그램을 사용하여 토큰 가져오기 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory B2C와 함께 AppAuth를 사용하는 Android 앱을 만들어 사용자 ID를 관리하고 사용자를 인증하는 방법을 보여 줍니다.
services: active-directory-b2c
documentationcenter: android
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.topic: article
ms.date: 03/06/2017
ms.author: davidmu
ms.openlocfilehash: 6c4c9359571882fbbea4e7701305e30e0f49f460
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Android 응용 프로그램을 사용하여 로그인

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 이를 통해 개발자는 서비스와 통합하려는 모든 라이브러리를 활용할 수 있습니다. 개발자가 플랫폼을 다른 라이브러리와 함께 사용할 수 있도록 돕기 위해, 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습 몇 가지를 마련했습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

> [!WARNING]
> Microsoft는 타사 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 이 샘플은 기본 시나리오에서 Azure AD B2C와의 호환성이 테스트된 AppAuth라는 타사 라이브러리를 사용합니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 참조하세요.  
>
>

OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. [여기서 추천한 프로토콜에 관한 개요](active-directory-b2c-reference-protocols.md)를 간략히 살펴볼 것을 추천합니다.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 계속하기 전에 [B2C 디렉터리를 만듭니다](active-directory-b2c-get-started.md) .

## <a name="create-an-application"></a>응용 프로그램 만들기

다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 모바일 앱을 만들려면 [이러한 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

* 응용 프로그램에 **네이티브 클라이언트**를 포함합니다.
* 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 ID는 나중에 필요합니다.
* 네이티브 클라이언트 **리디렉션 URI**(예: com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)를 설정합니다. 이 ID는 나중에도 필요합니다.

## <a name="create-your-policies"></a>정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 결합된 로그인 및 등록의 하나의 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)에서 설명한 대로 이 정책을 만들어야 합니다. 정책을 만들 때 다음을 확인합니다.

* 정책에서 **표시 이름**을 등록 특성으로 선택합니다.
* 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-sample-code"></a>샘플 코드 다운로드

[GitHub에서](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Azure AD B2C와 함께 AppAuth를 사용하는 작업 샘플이 제공됩니다. 코드를 다운로드하고 실행할 수 있습니다. [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)의 지침에 따라 사용자 고유의 Azure AD B2C 구성을 사용하는 자신만의 앱으로 빠르게 시작할 수 있습니다.

샘플은 [AppAuth](https://openid.github.io/AppAuth-Android/)에서 제공되는 샘플의 수정본입니다. AppAuth 및 기능에 대한 자세한 정보는 해당 페이지를 방문해 보세요.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>AppAuth와 함께 Azure AD B2C를 사용하도록 앱 수정

> [!NOTE]
> AppAuth는 Android API 16(Jellybean) 이상을 지원합니다. API 23 이상을 사용하는 것이 좋습니다.
>

### <a name="configuration"></a>구성

검색 URI를 지정하거나 권한 부여 끝점과 토큰 끝점 URI를 모두 지정하여 Azure AD B2C와의 통신을 구성할 수 있습니다. 두 경우 모두 다음 정보를 제공해야 합니다.

* 테넌트 ID(예: contoso.onmicrosoft.com)
* 정책 이름(예: B2C\_1\_SignUpIn)

권한 부여 및 토큰 끝점 URI를 자동으로 검색하려는 경우 검색 URI에서 정보를 가져와야 합니다. 검색 URI는 다음 URL에서 테넌트\_ID 및 정책\_이름을 바꿔서 만들 수 있습니다.

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

다음을 실행하면 권한 부여 및 토큰 끝점 URI를 획득하고 AuthorizationServiceConfiguration 개체를 만들 수 있습니다.

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

권한 부여 및 토큰 끝점 URI를 가져오기 위해 검색을 사용하는 대신 아래 URL에서 테넌트\_ID 및 정책\_이름을 바꾸면 명시적으로 지정할 수 있습니다.

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

다음 코드를 실행하여 AuthorizationServiceConfiguration 개체를 만듭니다.

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>권한 부여

권한 부여 서비스 구성을 구성하거나 검색하면 권한 부여 요청을 생성할 수 있습니다. 요청을 만들려면 다음 정보가 필요합니다.

* 클라이언트 ID(예: 00000000-0000-0000-0000-000000000000)
* 사용자 지정 스키마를 사용하는 리디렉션 URI(예: com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

두 항목 모두 [앱을 등록](#create-an-application)할 때 저장해야 합니다.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

프로세스의 나머지 단계를 완료하는 방법은 [AppAuth 가이드](https://openid.github.io/AppAuth-Android/)를 참조하세요. 작업 중인 앱으로 빠르게 시작해야 하는 경우에는 [샘플](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)을 확인하세요. [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)에 나온 단계에 따라 사용자 고유의 Azure AD B2C 구성을 입력합니다.

Microsoft는 사용자 의견 및 제안을 항상 환영합니다! 이 토픽을 완료하기가 어렵거나 이 콘텐츠를 개선할 사항이 있는 경우 페이지의 맨 아래에 의견을 보내주시면 감사하겠습니다. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에 추가해 주세요.

