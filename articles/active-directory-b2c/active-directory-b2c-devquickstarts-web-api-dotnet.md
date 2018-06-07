---
title: 보호된 웹 API 호출 ASP.NET Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C 및 OAuth 2.0 액세스 토큰을 사용하여 .NET 웹앱을 빌드하고 웹 API를 호출하는 방법입니다.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 75ae88898d3d6b275ec462e865347056305fd666
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: .NET 웹앱에서 .NET 웹 API 호출

Azure AD B2C를 사용하여 강력한 ID 관리 기능을 웹앱 및 웹 API에 추가할 수 있습니다. 이 문서에서는 액세스 토큰을 요청하고 .NET "할 일 모음" 웹앱에서 .NET 웹 API로 호출하는 방법을 설명합니다.

이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 하지 않은 경우 다음을 수행해야 합니다.

* [.NET 웹앱](active-directory-b2c-devquickstarts-web-dotnet-susi.md) 시작
* [.NET 웹 API](active-directory-b2c-devquickstarts-api-dotnet.md) 시작

## <a name="prerequisite"></a>필수 요소

웹 API를 호출하는 웹 응용 프로그램을 빌드하려면 다음을 수행해야 합니다.

1. [Azure AD B2C 테넌트를 만듭니다](active-directory-b2c-get-started.md).
2. [웹 API를 등록합니다](active-directory-b2c-app-registration.md#register-a-web-api).
3. [웹앱을 등록합니다](active-directory-b2c-app-registration.md#register-a-web-app).
4. [정책을 설정합니다](active-directory-b2c-reference-policies.md).
5. [웹 API를 사용하도록 웹앱 권한을 부여합니다](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> 클라이언트 응용 프로그램 및 웹 API는 동일한 Azure AD B2C 디렉터리를 사용해야 합니다.
>

## <a name="download-the-code"></a>코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)에서 유지 관리됩니다. 다음을 실행하여 샘플을 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다. 이제 솔루션에는 `TaskWebApp`과 `TaskService`, 2개의 프로젝트가 있습니다. `TaskWebApp`은 사용자와 상호 작용하는 MVC 웹 응용 프로그램입니다. `TaskService` 는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 Web API입니다. 이 문서에서는 `TaskWebApp` 웹앱 또는 `TaskService` 웹 API를 구축하는 방법을 다루지 않습니다. Azure AD B2C를 사용하여 .NET 웹앱을 구축하는 방법을 알아보려면 [.NET 웹앱 자습서](active-directory-b2c-devquickstarts-web-dotnet-susi.md)를 참조하세요. Azure AD B2C를 사용하여 보안된 .NET 웹 API를 구축하는 방법을 알아보려면 [.NET 웹 API 자습서](active-directory-b2c-devquickstarts-api-dotnet.md)를 참조하세요.

### <a name="update-the-azure-ad-b2c-configuration"></a>Azure AD B2C 구성 업데이트

샘플은 데모 테넌트의 정책 및 클라이언트 ID를 사용하도록 구성되어 있습니다. 자신의 테넌트를 사용하려는 경우:

1. `TaskService` 프로젝트에서 `web.config`를 열고 다음 값을 바꿉니다.

    * `ida:Tenant`를 테넌트 이름으로 바꿉니다.
    * `ida:ClientId`를 웹 API 응용 프로그램 ID로 바꿉니다.
    * `ida:SignUpSignInPolicyId`를 "등록 또는 로그인" 정책 이름으로 바꿉니다.

2. `TaskWebApp` 프로젝트에서 `web.config`를 열고 다음 값을 바꿉니다.

    * `ida:Tenant`를 테넌트 이름으로 바꿉니다.
    * `ida:ClientId`를 웹앱 응용 프로그램 ID로 바꿉니다.
    * `ida:ClientSecret`을 웹앱 비밀 키로 바꿉니다.
    * `ida:SignUpSignInPolicyId`를 "등록 또는 로그인" 정책 이름으로 바꿉니다.
    * `ida:EditProfilePolicyId`를 "프로필 편집" 정책 이름으로 바꿉니다.
    * `ida:ResetPasswordPolicyId`를 "암호 재설정" 정책 이름으로 바꿉니다.



## <a name="requesting-and-saving-an-access-token"></a>액세스 토큰 요청 및 저장

### <a name="specify-the-permissions"></a>사용 권한 지정

웹 API에 대한 호출을 만들려면 사용자를 인증하고(등록/로그인 정책 사용) Azure AD B2C에서 [액세스 토큰을 받아야](active-directory-b2c-access-tokens.md) 합니다. 액세스 토큰을 받으려면 먼저 액세스 토큰을 부여하려는 권한을 지정해야 합니다. `/authorize` 끝점에 대한 요청을 만들 때 `scope` 매개 변수에서 권한이 지정됩니다. 예를 들어 `https://contoso.onmicrosoft.com/tasks`의 앱 ID URI를 가진 리소스 응용 프로그램에 대한 "읽기" 권한으로 액세스 토큰을 얻으려면 범위는 `https://contoso.onmicrosoft.com/tasks/read`가 됩니다.

이 샘플의 범위를 지정하려면 `App_Start\Startup.Auth.cs` 파일을 열고 OpenIdConnectAuthenticationOptions에서 `Scope` 변수를 정의합니다.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>액세스 토큰에 대한 인증 코드 교환

사용자가 등록 또는 로그인 환경을 완료한 후 앱은 Azure AD B2C에서 인증 코드를 받습니다. OWIN OpenID Connect 미들웨어는 코드를 저장하지만 액세스 토큰에 대해 교환하지 않습니다. [MSAL 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)를 사용하여 교환할 수 있습니다. 샘플에서는 권한 부여 코드를 받을 때마다 OpenID Connect 미들웨어로 알림 콜백을 구성합니다. 콜백에서 MSAL을 사용하여 토큰에 대한 코드를 교환하고 토큰을 캐시에 저장합니다.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>웹 API 호출

이 섹션에서는 웹 API에 액세스하기 위해 Azure AD B2C를 사용하여 등록/로그인 중에 받은 토큰을 사용하는 방법을 설명합니다.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>컨트롤러에서 저장된 토큰 검색

`TasksController`은 HTTP 요청을 API로 보내 작업을 읽고 만들고 삭제하도록 웹 API와 통신을 담당합니다. API는 Azure AD B2C에 의해 보안되므로, 먼저 위의 단계에서 저장한 토큰을 검색해야 합니다.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Web API로부터 작업 읽기

이제 토큰이 있으므로 `Authorization` 헤더에서 HTTP `GET` 요청에 토큰을 연결하여 `TaskService`를 안전하게 호출할 수 있습니다.

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Web API에서 작업 만들기 및 삭제

캐시에서 액세스 토큰을 검색하기 위해 `POST` 및 `DELETE` 요청을 MSAL을 사용하여 웹 API에 보낼 때 동일한 패턴을 따릅니다.

## <a name="run-the-sample-app"></a>샘플 앱 실행

마지막으로 두 앱을 빌드 및 실행합니다. 등록하고 로그인하여, 로그인된 사용자에 대한 작업을 만듭니다. 로그아웃했다가 다른 사용자로 로그인합니다. 해당 사용자에 대한 작업을 만듭니다. API 가 받는 토큰에서 사용자의 ID를 추출하므로 API에 사용자별 작업이 저장됩니다. 또한 범위 다루기를 시도해 볼 수도 있습니다. "쓰기"에 대한 권한을 제거한 다음 작업을 추가합니다. 범위를 변경할 때마다 로그아웃해야 합니다.

