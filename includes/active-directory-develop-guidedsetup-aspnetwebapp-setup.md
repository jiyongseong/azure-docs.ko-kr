---
title: 포함 파일
description: 포함 파일
services: active-directory
author: andretms
ms.service: active-directory
ms.topic: include
ms.date: 05/08/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 5d3af1800e18e3686e69d4a25131c68d3bdc805b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33949450"
---
## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 ASP.NET 프로젝트에서 OpenID Connect를 사용하여 OWIN 미들웨어를 통해 인증 파이프라인을 설치 및 구성하는 단계를 보여 줍니다. 

> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)하면 실행 전 코드 샘플을 구성하는 [구성 단계](#register-your-application)로 건너뛸 수 있습니다.

### <a name="create-your-aspnet-project"></a>ASP.NET 프로젝트 만들기

1. Visual Studio에서: `File` > `New` > `Project`
2. *Visual C#\Web*에서 `ASP.NET Web Application (.NET Framework)`을 선택합니다.
3. 응용 프로그램의 이름을 지정하고 *확인*을 클릭합니다.
4. `Empty`를 선택하고 확인란을 선택하여 `MVC` 참조를 추가합니다.

## <a name="add-authentication-components"></a>인증 구성 요소 추가

1. Visual Studio에서: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. [패키지 관리자 콘솔] 창에 다음 내용을 입력하여 [OWIN 미들웨어 NuGet 패키지]를 추가합니다.

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>이러한 라이브러리 정보
>위의 라이브러리는 쿠키 기반 인증을 통해 OpenID Connect를 사용하여 SSO(Single Sign-On)를 사용하도록 설정합니다. 인증이 완료되고 사용자를 나타내는 토큰이 응용 프로그램으로 전송되면 OWIN 미들웨어가 세션 쿠키를 생성합니다. 그러면 브라우저가 후속 요청 시 이 쿠키를 사용합니다. 따라서 사용자가 암호를 다시 입력할 필요가 없고 추가 확인이 필요하지 않습니다.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>인증 파이프라인 구성
아래 단계는 OpenID Connect 인증을 구성하기 위해 OWIN 미들웨어 Startup 클래스를 만드는 데 사용됩니다. 이 클래스는 IIS 프로세스 시작 시 자동으로 실행됩니다.

> [!TIP]
> 프로젝트의 루트 폴더에 `Startup.cs` 파일이 없는 경우:
> 1. 프로젝트의 루트 폴더를 마우스 오른쪽 단추로 클릭합니다. > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. 이름을 `Startup.cs`로 지정합니다.
>
>> 선택한 클래스가 표준 C# 클래스가 아니라 OWIN Startup 클래스인지 확인합니다. 네임스페이스 위에 `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`가 보이면 선택하여 확인합니다.

1. `Startup.cs`에 *OWIN* 및 *Microsoft.IdentityModel* 참조를 추가합니다.

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Startup 클래스를 다음 코드로 바꿉니다.

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>추가 정보
> *OpenIDConnectAuthenticationOptions*에 제공하는 매개 변수는 응용 프로그램이 Azure AD와 통신하기 위한 좌표로 사용됩니다. OpenID Connect 미들웨어는 백그라운드에 쿠키를 사용하므로 위의 코드에서처럼 쿠키 인증도 설정해야 합니다. *ValidateIssuer* 값은 OpenIdConnect에 특정 조직 하나로 액세스를 제한하지 않도록 지시합니다.
<!--end-collapse-->

