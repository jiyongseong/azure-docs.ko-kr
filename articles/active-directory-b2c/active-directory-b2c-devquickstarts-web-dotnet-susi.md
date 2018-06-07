---
title: 인증, 등록, 암호 재설정 ASP.NET Azure Active Directory B2C
description: Azure Active Directory B2C를 사용하여 등록/로그인, 프로필 편집 및 암호 다시 설정을 포함하는 웹 응용 프로그램을 빌드하는 방법입니다.
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
ms.openlocfilehash: 052d81f8170273bb0e40572322349550e7219363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Azure Active Directory B2C 등록, 로그인, 프로필 편집 및 암호 재설정을 사용하여 ASP.NET 웹앱 만들기

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 웹앱에 Azure AD B2C ID 기능 추가
> * Azure AD B2C 디렉터리에 웹앱 등록
> * 웹앱에 대한 사용자 등록/로그인, 프로필 편집 및 암호 재설정 정책 만들기

## <a name="prerequisites"></a>필수 조건

- Azure 계정에 B2C 테넌트를 연결해야 합니다. [여기](https://azure.microsoft.com/)에서 Azure 계정을 만들 수 있습니다.
- 샘플 코드를 보고 수정하기 위해 [Microsoft Visual Studio](https://www.visualstudio.com/) 또는 비슷한 프로그램이 필요합니다.

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 만들기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 B2C 디렉터리를 만든 후에 이 가이드를 계속 진행합니다.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Azure 구독에 B2C 테넌트를 연결해야 합니다. **만들기**를 선택한 후 **Azure 구독에 기존 Azure AD B2C 테넌트 연결** 옵션을 선택한 다음 **Azure AD B2C 테넌트** 드롭다운에서 연결하려는 테넌트를 선택합니다.

## <a name="create-and-register-an-application"></a>응용 프로그램 만들기 및 등록

다음으로 B2C 디렉터리에서 앱을 만들고 등록해야 합니다. Azure AD B2C가 앱과 안전하게 통신하는 데 필요한 정보를 제공합니다. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

완료되면 응용 프로그램 설정에 API 및 네이티브 응용 프로그램 모두를 갖습니다.

## <a name="create-policies-on-your-b2c-tenant"></a>B2C 테넌트에 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 코드 샘플은 **등록 및 로그인**, **프로필 편집** 및 **암호 재설정**의 세 가지 ID 환경을 포함합니다.  [정책 참조 문서](active-directory-b2c-reference-policies.md)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 각 정책의 경우 표시 이름 특성 또는 클레임을 선택하고 나중에 사용할 정책 이름을 복사해야 합니다.

### <a name="add-your-identity-providers"></a>ID 공급자 추가

설정에서 **ID 공급자**를 선택하고 사용자 이름 등록 또는 전자 메일 등록을 선택합니다.

### <a name="create-a-sign-up-and-sign-in-policy"></a>등록 및 로그인 정책 만들기

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>프로필 편집 정책 만들기

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-sample-code"></a>샘플 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)에서 유지 관리됩니다. 다음을 실행하여 샘플을 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다. 이제 솔루션에는 `TaskWebApp`과 `TaskService`, 2개의 프로젝트가 있습니다. `TaskWebApp`은 사용자와 상호 작용하는 MVC 웹 응용 프로그램입니다. `TaskService` 는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 Web API입니다. 이 문서에서는 `TaskWebApp` 응용 프로그램만을 설명합니다. Azure AD B2C를 사용하여 `TaskService`을 구축하는 방법을 알아보려면 [.NET 웹 API 자습서](active-directory-b2c-devquickstarts-api-dotnet.md)를 참조하세요.

## <a name="update-code-to-use-your-tenant-and-policies"></a>테넌트 및 정책을 사용하는 코드 업데이트

샘플은 데모 테넌트의 정책 및 클라이언트 ID를 사용하도록 구성되어 있습니다. 자신의 테넌트에 연결하려면 `TaskWebApp` 프로젝트에서 `web.config`를 열고 다음 값을 바꿔야 합니다.

* `ida:Tenant`를 테넌트 이름으로 바꿉니다.
* `ida:ClientId`를 웹앱 응용 프로그램 ID로 바꿉니다.
* `ida:ClientSecret`을 웹앱 비밀 키로 바꿉니다.
* `ida:SignUpSignInPolicyId`를 "등록 또는 로그인" 정책 이름으로 바꿉니다.
* `ida:EditProfilePolicyId`를 "프로필 편집" 정책 이름으로 바꿉니다.
* `ida:ResetPasswordPolicyId`를 "암호 재설정" 정책 이름으로 바꿉니다.

## <a name="launch-the-app"></a>앱 시작
Visual Studio 내에서 앱을 시작합니다. 할 일 목록 탭으로 이동합니다. URl는 https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*&client_id=*YourclientID*.....입니다.

메일 주소 또는 사용자 이름을 사용하여 앱에 등록합니다. 로그아웃한 다음 다시 로그인하고 프로필을 편집하거나 암호를 다시 설정합니다. 로그아웃했다가 다른 사용자로 로그인합니다. 

## <a name="add-social-idps"></a>소셜 IDP 추가

현재 앱은 **로컬 계정**을 사용하여 사용자 등록 및 로그인을 지원합니다. 사용자 이름 및 암호를 사용하는 B2C 디렉터리에 저장된 계정입니다. Azure AD B2C를 사용하면 코드를 변경하지 않고도 다른 **IDP(ID 공급자)** 에 대한 지원을 추가할 수 있습니다.

소셜 IDP를 앱에 추가하려면 이 문서 중에서 상세한 지침을 수행하여 시작합니다. 지원하려는 각 IDP의 경우 해당 시스템에서 응용 프로그램을 등록하고 클라이언트 ID를 얻어야 합니다.

* [Facebook을 IDP로 설정](active-directory-b2c-setup-fb-app.md)
* [Google을 IDP로 설정](active-directory-b2c-setup-goog-app.md)
* [Amazon을 IDP로 설정](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn을 IDP로 설정](active-directory-b2c-setup-li-app.md)

B2C 디렉터리에 ID 공급자를 추가한 후 [정책 참조 문서](active-directory-b2c-reference-policies.md)에서 설명한 대로 새 IDP를 포함하도록 세 가지 정책을 각각 편집합니다. 정책을 저장한 후 앱을 다시 실행합니다.  ID 환경 각각에서 로그인 및 등록으로 추가된 새 IDP가 표시되어야 합니다.

정책을 실험하고 샘플 앱에서 영향을 확인할 수 있습니다. IDP를 추가 또는 제거하거나 응용 프로그램 클레임을 조작하거나 등록 특성을 변경합니다. 어떻게 정책, 인증 요청 및 OWIN을 모두 함께 연결하는지 확인할 수 있을 때까지 실험해 보세요.

## <a name="sample-code-walkthrough"></a>샘플 코드 연습
다음 섹션에서는 샘플 응용 프로그램 코드를 구성하는 방법을 보여 줍니다. 이후 앱 개발에서 지침으로 사용할 수 있습니다.

### <a name="add-authentication-support"></a>인증 지원 추가

이제 Azure AD B2C를 사용하여 앱을 구성할 수 있습니다. 앱은 OpenID Connect 인증 요청을 보내 Azure AD B2C와 통신합니다. 요청은 앱에서 정책을 지정하여 실행하려는 사용자 경험을 나타냅니다. Microsoft의 OWIN 라이브러리를 사용하여 해당 요청을 보내고, 정책을 실행하고, 사용자의 세션을 관리할 수 있습니다.

#### <a name="install-owin"></a>OWIN 설치

시작하려면 Visual Studio 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>OWIN 시작 클래스 추가

`Startup.cs`라는 API에 OWIN 시작 클래스를 추가합니다.  프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** 및 **새 항목**을 선택한 다음 OWIN을 검색합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(…)` 메서드를 호출합니다.

이 샘플에서는 `public partial class Startup`에 대한 클래스 선언을 변경하고 `App_Start\Startup.Auth.cs`에서 클래스의 다른 부분을 구현했습니다. `Configuration` 메서드 내에서 `ConfigureAuth`에 호출을 추가했습니다. 이는 `Startup.Auth.cs`에서 정의됩니다. 수정 후에 `Startup.cs`는 다음과 같습니다.

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>인증 미들웨어 구성

`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(...)` 메서드를 구현합니다. `OpenIdConnectAuthenticationOptions`에 제공하는 매개 변수는 앱이 Azure AD B2C와 통신하기 위한 좌표로 사용됩니다. 특정 매개 변수를 지정하지 않으면 기본값이 사용됩니다. 예를 들어 샘플에 `ResponseType`을 지정하지 않으므로 기본값 `code id_token`이 Azure AD B2C로 보내는 각 요청에 사용됩니다.

쿠키 인증도 설정해야 합니다. 무엇보다도 OpenID Connect 미들웨어는 사용자 세션을 유지하기 위해 쿠키를 사용합니다.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

위의 `OpenIdConnectAuthenticationOptions`에서 OpenID Connect 미들웨어에서 수신되는 특정 알림에 대한 콜백 함수 집합을 정의합니다. 이러한 동작은 `OpenIdConnectAuthenticationNotifications` 개체를 사용하여 정의되고 `Notifications` 변수에 저장됩니다. 샘플에서는 이벤트에 따라 서로 다른 3개의 콜백을 정의합니다.

### <a name="using-different-policies"></a>다른 정책 사용

Azure AD B2C에 대한 요청이 만들어질 때마다 `RedirectToIdentityProvider` 알림이 트리거됩니다. 콜백 함수 `OnRedirectToIdentityProvider`에서 다른 정책을 사용하려는 경우 나가는 호출을 확인합니다. 암호 재설정 또는 프로필 편집을 수행하려면 기본 "등록 또는 로그인" 정책 대신 암호 재설정 정책과 같은 해당 정책을 사용해야 합니다.

샘플에서는 사용자가 암호를 재설정하거나 프로필을 편집하려고 하는 경우 OWIN 컨텍스트로 사용하려는 정책을 추가합니다. 다음을 수행하여 수행할 수 있습니다.

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

다음을 수행하여 콜백 함수 `OnRedirectToIdentityProvider`를 구현할 수 있습니다.

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>권한 부여 코드 처리

권한 부여 코드를 받을 때 `AuthorizationCodeReceived` 알림이 트리거됩니다. OpenID Connect 미들웨어는 액세스 토큰에 대한 코드 교환을 지원하지 않습니다. 콜백 함수에서 토큰에 대한 코드를 수동으로 교환할 수 있습니다. 자세한 내용은 방법을 설명하는 [설명서](active-directory-b2c-devquickstarts-web-api-dotnet.md)를 참조하세요.

### <a name="handling-errors"></a>오류 처리

인증이 실패한 경우 `AuthenticationFailed` 알림이 트리거됩니다. 해당 콜백 메서드에서 원하는 대로 오류를 처리할 수 있습니다. 그러나 오류 코드 `AADB2C90118`에 대한 확인을 추가해야 합니다. “등록 또는 로그인” 정책 실행 동안 사용자에게 **암호를 잊으셨나요?** 링크를 선택할 기회가 있습니다. 이 이벤트에서 Azure AD B2C는 앱에 암호 재설정 정책을 대신 사용하여 요청을 만들어야 한다는 것을 나타내는 해당 오류 코드를 전송합니다.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Azure AD로 인증 요청 보내기

이제 앱은 OpenID Connect 인증 프로토콜을 사용하여 Azure AD B2C와 통신하도록 올바르게 구성되었습니다. OWIN은 인증 메시지를 작성하고, Azure AD B2C에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 세부 과정을 관리합니다. 이제 각 사용자 흐름을 시작하기만 하면 됩니다.

사용자가 웹앱에서 **등록/로그인**, **프로필 편집** 또는 **암호 재설정**을 선택하는 경우 연결된 작업이 `Controllers\AccountController.cs`에서 호출됩니다.

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

또한 앱에서 사용자를 로그아웃하는 데 OWIN을 사용할 수도 있습니다. `Controllers\AccountController.cs`에서 다음을 가집니다.

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

정책을 명시적으로 호출하는 것 외에도 사용자가 로그인하지 않은 경우 정책을 실행할 컨트롤러에서 `[Authorize]` 태그를 사용할 수 있습니다. `Controllers\HomeController.cs`를 열고 클레임 컨트롤러에 `[Authorize]` 태그를 추가합니다.  OWIN은 `[Authorize]` 태그를 눌렀을 때 구성되는 마지막 정책을 선택합니다.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>사용자 정보 표시

OpenID Connect를 사용하여 사용자를 인증할 때 Azure AD B2C는 **클레임**을 포함하는 ID 토큰을 앱에 반환합니다. 이는 사용자에 대한 어설션입니다. 클레임을 사용하여 앱 개인 설정을 수행할 수 있습니다.

`Controllers\HomeController.cs` 파일을 엽니다. `ClaimsPrincipal.Current` 보안 주체 개체를 통해 컨트롤러의 사용자 클레임에 액세스할 수 있습니다.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

동일한 방식으로 응용 프로그램에서 수신하는 클레임을 액세스할 수 있습니다.  앱이 수신하는 모든 클레임 목록은 **클레임** 페이지에서 사용할 수 있습니다.
