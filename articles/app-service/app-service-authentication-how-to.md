---
title: Azure App Service의 인증 및 권한 부여 사용자 지정 | Microsoft Docs
description: App Service의 인증 및 권한 부여를 사용자 지정하고, 사용자 클레임 및 서로 다른 토큰을 가져오는 방법을 보여 줍니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: c41cb3ef2939fe7271b1f8738fcf0cb95c4b1111
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Azure App Service의 인증 및 권한 부여 사용자 지정

이 문서에서는 [App Service의 인증 및 권한 부여](app-service-authentication-overview.md)를 사용자 지정하고 응용 프로그램에서 ID를 관리하는 방법을 보여 줍니다. 

지금 바로 시작하려면 다음 자습서 중 하나를 참조하세요.

* [자습서: Azure App Service에서 종단 간 사용자 인증 및 권한 부여(Windows)](app-service-web-tutorial-auth-aad.md)
* [자습서: Azure App Service에서 Linux용 종단 간 사용자 인증 및 권한 부여](containers/tutorial-auth-aad.md)
* [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook 로그인을 사용하도록 앱을 구성하는 방법](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google 로그인을 사용하도록 앱을 구성하는 방법](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter 로그인을 사용하도록 앱을 구성하는 방법](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>다중 로그인 옵션 구성

포털 구성은 사용자에게 다중 로그인 옵션을 표시하는 턴키 방법을 제공하지 않습니다(예: Facebook 및 Twitter). 그러나 웹앱에 기능을 추가하기는 어렵지 않습니다. 단계는 다음과 같이 간략히 설명됩니다.

먼저 Azure Portal의 **인증/권한 부여** 페이지에서 사용하도록 설정하려는 각 ID 공급자를 구성합니다.

**요청이 인증되지 않은 경우 수행할 작업**에서 **익명 요청 허용(작업 없음)** 을 선택합니다.

로그인 페이지, 탐색 모음 또는 웹앱의 다른 위치에서 사용하도록 설정한 각 공급자에 로그인 링크를 추가합니다(`/.auth/login/<provider>`). 예: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

사용자가 링크 중 하나를 클릭하면 사용자가 로그인할 수 있는 각 로그인 페이지가 열립니다.

## <a name="access-user-claims"></a>사용자 클레임 액세스

App Service는 특수 헤더를 사용하여 사용자 클레임을 응용 프로그램에 전달합니다. 외부 요청은 이러한 헤더를 설정하도록 허용되지 않았으므로 App Service에서 설정한 경우에만 표시됩니다. 다음은 이러한 헤더의 예입니다.

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

모든 언어로 작성된 코드 또는 프레임워크는 이러한 헤더에서 필요한 정보를 가져올 수 있습니다. ASP.NET 4.6 앱의 경우 **ClaimsPrincipal** 이 적절한 값으로 자동 설정됩니다.

또한 응용 프로그램에서 `/.auth/me`를 호출하여 인증된 사용자에 대한 추가 세부 사항을 가져올 수 있습니다. Mobile Apps 서버 SDK는 이 데이터를 사용하기 위한 도우미 메서드를 제공합니다. 자세한 내용은 [Azure Mobile Apps Node.js SDK를 사용하는 방법](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) 및 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)을 참조하세요.

## <a name="retrieve-tokens-in-app-code"></a>앱 코드에서 토큰 검색

서버 코드에서 공급자별 토큰은 쉽게 액세스할 수 있도록 요청 헤더에 주입됩니다. 다음 표에 가능한 토큰 헤더 이름이 나와 있습니다.

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 토큰 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 계정 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

클라이언트 코드(예: 모바일 앱 또는 브라우저 내 JavaScript)에서 HTTP `GET` 요청을 `/.auth/me`에 전송합니다. 반환된 JSON에는 공급자별 토큰이 있습니다.

> [!NOTE]
> 액세스 토큰은 공급자 리소스에 액세스하기 위한 것이므로 클라이언트 암호를 사용하여 공급자를 구성하는 경우에만 표시됩니다. 새로 고침 토큰을 가져오는 방법을 알아보려면 [액세스 토큰 새로 고침](#refresh-access-tokens)을 참조하세요.

## <a name="refresh-access-tokens"></a>액세스 토큰 새로 고침

공급자의 액세스 토큰이 만료되면 사용자를 다시 인증해야 합니다. 응용 프로그램의 `/.auth/refresh` 엔드포인트에 대한 `GET` 호출을 수행하면 토큰 만료를 방지할 수 있습니다. 호출되면 App Service에서는 인증된 사용자에 대한 토큰 저장소의 액세스 토큰을 자동으로 새로 고칩니다. 이후에 앱 코드에서 토큰을 요청하면 새로 고쳐진 토큰을 가져옵니다. 단, 토큰 새로 고침을 실행하기 위해서는 토큰 저장소에 사용자 공급자에 대한 [토큰 새로 고침](https://auth0.com/learn/refresh-tokens/)이 포함되어야 합니다. 새로 고침 토큰을 얻는 방법은 각 공급자에서 제공하는 문서에 나와 있으며, 다음 목록은 간단한 요약입니다.

- **Google**: `access_type=offline` 쿼리 문자열 매개 변수를 `/.auth/login/google` API 호출에 추가합니다. Mobile Apps SDK를 사용하는 경우 `LogicAsync` 오버로드 중 하나에 매개 변수를 추가할 수 있습니다([Google 새로 고침 토큰](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) 참조).
- **Facebook**: 새로 고침 토큰을 제공하지 않습니다. 수명이 긴 토큰은 60일 후에 만료됩니다([액세스 토큰의 Facebook 만료 및 확장](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) 참조).
- **Twitter**: 액세스 토큰이 만료되지 않습니다([Twitter OAuth FAQ](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq) 참조).
- **Microsoft 계정**: [Microsoft 계정 인증 설정을 구성](app-service-mobile-how-to-configure-microsoft-authentication.md)할 때 `wl.offline_access` 범위를 선택합니다.
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com)에서 다음 단계를 수행합니다.
    1. 페이지의 위쪽에서 **읽기/쓰기**를 선택합니다.
    1. 왼쪽 브라우저에서 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **사이트** > _**\<app\_name>**_ > **config** > **authsettings**로 이동합니다. 
    1. **편집**을 클릭합니다.
    1. 다음 속성을 수정합니다. _\<app\_id>_ 를 액세스하려는 서비스의 Azure Active Directory 응용 프로그램 ID로 바꿉니다.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. **배치**를 클릭합니다. 

공급자가 구성되면 `/.auth/me`를 호출하여 새로 고침 토큰이 토큰 저장소에 있는지 확인할 수 있습니다. 

언제든지 액세스 토큰을 새로 고치려면 아무 언어에서나 `/.auth/refresh`를 호출하면 됩니다. 다음 코드 조각은 jQuery를 사용하여 JavaScript 클라이언트에서 액세스 토큰을 새로 고칩니다.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

사용자가 사용자 앱에 부여된 사용 권한을 취소하는 경우 `/.auth/me`에 대한 호출이 `403 Forbidden` 응답과 함께 실패할 수도 있습니다. 오류를 진단하려면 세부 정보에 대한 응용 프로그램 로그를 확인합니다.

## <a name="extend-session-expiration-grace-period"></a>세션 만료 유예 기간 연장

인증된 세션이 만료된 후에는 기본적으로 72시간의 유예 기간이 있습니다. 이 유예 기간 내에 사용자를 다시 인증하지 않고 App Service를 통해 세션 쿠키 또는 세션 토큰을 새로 고칠 수 있습니다. 세션 쿠키 또는 세션 토큰이 무효화됐을 때 `/.auth/refresh`를 호출하기만 하면 사용자가 직접 토큰 만료를 추적할 필요가 없습니다. 72시간의 유예 기간이 소멸되면 사용자는 다시 로그인하여 유효한 세션 쿠키 또는 세션 토큰을 가져와야 합니다.

72시간이 충분치 않은 경우 이 만료 기간을 확장할 수 있습니다. 오랜 기간 동안 만료를 연장하면 중요한 보안 결과가 발생할 수 있습니다(예: 인증 토큰이 유출되거나 도난당하는 경우). 따라서 기본 72시간으로 유지하거나 확장 기간을 가장 작은 값으로 설정해야 합니다.

기본 만료 기간을 확장하려면 [Cloud Shell](../cloud-shell/overview.md)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 유예 기간은 ID 공급자의 토큰이 아닌 App Service 인증된 세션에만 적용됩니다. 만료된 공급자 토큰에 대한 유예 기간은 없습니다. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>로그인 계정의 도메인 제한

Microsoft 계정과 Azure Active Directory는 모두 여러 도메인에서 로그인할 수 있습니다. 예를 들어 Microsoft 계정은 _outlook.com_, _live.com_ 및 _hotmail.com_ 계정을 허용합니다. Azure Active Directory는 로그인 계정에 대해 여러 사용자 지정 도메인을 허용합니다. 이 동작은 _outlook.com_ 계정을 사용하는 사람이 액세스하는 것을 원치 않는 내부 앱의 경우 바람직하지 않을 수도 있습니다. 로그인 계정의 도메인 이름을 제한하려면 다음 단계를 수행합니다.

[https://resources.azure.com](https://resources.azure.com)에서 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **사이트** > _**\<app\_name>**_ > **config** > **authsettings**로 이동합니다. 

**편집**을 클릭하고 다음 속성을 수정한 다음, **배치**를 클릭합니다. _\<domain\_name>_ 을 원하는 도메인으로 바꿨는지 확인합니다.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 종단 간 사용자 인증 및 권한 부여(Windows)](app-service-web-tutorial-auth-aad.md)
> [자습서: 종단 간 사용자 인증 및 권한 부여(Linux)](containers/tutorial-auth-aad.md)