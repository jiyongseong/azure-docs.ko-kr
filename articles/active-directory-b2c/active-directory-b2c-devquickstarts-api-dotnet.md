---
title: Web API 보호 - ASP.NET - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 .NET Web API를 빌드하는 방법이며 인증을 위해 OAuth 2.0 액세스 토큰을 사용하여 보호됩니다.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/14/2018
ms.author: davidmu
ms.openlocfilehash: c64ca3546c5c2d21b00b379c297c9265b264cb9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: .NET Web API 빌드

Azure AD(Azure Active Directory) B2C로 OAuth 2.0 액세스 토큰을 사용하여 Web API를 보호할 수 있습니다. 이러한 토큰을 통해 클라이언트 앱이 API에 인증할 수 있습니다. 이 문서에서는 클라이언트 응용 프로그램의 사용자에게 CRUD 태스크를 허용하는 .NET MVC "할 일 모음" API를 만드는 방법을 보여 줍니다. Web API는 Azure AD B2C를 사용하여 보호되며 사용자가 해당 할 일 목록을 관리하도록 인증할 수 있습니다.

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 만들기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

> [!NOTE]
> 클라이언트 응용 프로그램 및 웹 API는 동일한 Azure AD B2C 디렉터리를 사용해야 합니다.
>

## <a name="create-a-web-api"></a>Web API 만들기

다음으로 B2C 디렉터리에서 Web API 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

* 응용 프로그램에서 **웹앱** 또는 **Web API**를 포함합니다.
* 웹앱의 **리디렉션 URI** `https://localhost:44332/`를 사용합니다. 이 코드 샘플에 대한 웹앱 클라이언트의 기본 위치입니다.
* 앱에 할당된 **응용 프로그램 ID** 를 복사합니다. 나중에 필요합니다.
* 앱 식별자를 **앱 ID URI**에 입력합니다. 전체 **앱 ID URI**를 복사합니다. 나중에 필요합니다.
* **게시된 범위** 메뉴를 통해 사용 권한을 추가합니다.

## <a name="create-your-policies"></a>정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. Azure AD B2C와 통신하는 정책을 만들어야 합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md)에 설명된 대로 결합된 등록/로그인 정책을 사용하는 것이 좋습니다. 정책을 만들 때 다음을 확인합니다.

* 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
* 모든 정책에 대한 응용 프로그램 클레임으로 **표시 이름** 및 **개체 ID** 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름**을 복사합니다. 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

정책을 성공적으로 만들면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-code"></a>코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)에서 유지 관리됩니다. 다음을 실행하여 샘플을 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다. 이제 솔루션에는 `TaskWebApp`과 `TaskService`, 2개의 프로젝트가 있습니다. `TaskWebApp` 은 사용자와 상호 작용하는 MVC 웹 응용 프로그램입니다. `TaskService` 는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 Web API입니다. 이 문서에서는 `TaskService` 응용 프로그램만을 설명합니다. Azure AD B2C를 사용하여 `TaskWebApp`을 구축하는 방법을 알아보려면 [.NET 웹앱 자습서](active-directory-b2c-devquickstarts-web-dotnet-susi.md)를 참조하세요.

### <a name="update-the-azure-ad-b2c-configuration"></a>Azure AD B2C 구성 업데이트

샘플은 데모 테넌트의 정책 및 클라이언트 ID를 사용하도록 구성되어 있습니다. 고유한 테넌트를 사용하려는 경우 다음을 수행해야 합니다.

1. `TaskService` 프로젝트에서 `web.config`을 열고 다음 값을 바꿉니다.
    * `ida:Tenant`을 테넌트 이름으로 바꿉니다.
    * `ida:ClientId`을 Web API 응용 프로그램 ID로 바꿉니다.
    * `ida:SignUpSignInPolicyId`을 "등록 또는 로그인" 정책 이름으로 바꿉니다.

2. `TaskWebApp` 프로젝트에서 `web.config`를 열고 다음 값을 바꿉니다.
    * `ida:Tenant`를 테넌트 이름으로 바꿉니다.
    * `ida:ClientId`를 웹앱 응용 프로그램 ID로 바꿉니다.
    * `ida:ClientSecret`을 웹앱 비밀 키로 바꿉니다.
    * `ida:SignUpSignInPolicyId`를 "등록 또는 로그인" 정책 이름으로 바꿉니다.
    * `ida:EditProfilePolicyId`를 "프로필 편집" 정책 이름으로 바꿉니다.
    * `ida:ResetPasswordPolicyId`를 "암호 재설정" 정책 이름으로 바꿉니다.
    * `api:ApiIdentifier`를 “앱 ID URI”로 바꿉니다.


## <a name="secure-the-api"></a>API 보호

API를 호출하는 클라이언트가 있는 경우 OAuth 2.0 전달자 토큰을 사용하여 API(예: `TaskService`)를 보호할 수 있습니다. 이렇게 하면 요청에 전달자 토큰이 있는 경우 API에 대한 각 요청이 유효해 집니다. API는 Microsoft OWIN(Open Web Interface for .NET)을 사용하여 전달자 토큰을 허용하고 유효성을 검사할 수 있습니다.

### <a name="install-owin"></a>OWIN 설치

OAuth OWIN 인증 파이프라인을 설치하거나 Visual Studio 패키지 관리자 콘솔을 사용하여 시작합니다.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

전달자 토큰을 허용하고 유효성을 검사하는 OWIN 미들웨어를 설치합니다.

### <a name="add-an-owin-startup-class"></a>OWIN 시작 클래스 추가

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

### <a name="configure-oauth-20-authentication"></a>OAuth 2.0 인증 구성

`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(...)` 메서드를 구현합니다. 예를 들어 다음과 같아야 합니다.

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>작업 컨트롤러 보호

앱을 OAuth 2.0 인증을 사용하도록 구성한 후 작업 컨트롤러에 `[Authorize]` 태그를 추가하여 Web API를 보호할 수 있습니다. 할 일 모음 조작이 발생하는 컨트롤러이므로 클래스 수준에서 전체 컨트롤러를 보호해야 합니다. 보다 세분화한 제어를 위해 개별 작업에 `[Authorize]` 태그를 추가할 수도 있습니다.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>토큰에서 사용자 정보 가져오기

`TasksController` 는 데이터베이스에 작업을 저장하며, 여기서 각 작업에는 작업을 "소유"하는 연결된 사용자가 있습니다. 소유자는 사용자의 **개체 ID**로 식별됩니다. (따라서 모든 정책에 응용 프로그램 클레임으로 개체 ID를 추가해야 했습니다.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>토큰에서 사용 권한 확인

웹 API에 대한 일반적인 요구 사항은 토큰에 있는 "범위"를 확인하는 것입니다. 이렇게 하여 사용자가 할 일 목록 서비스에 액세스하는 데 필요한 권한에 동의했음을 확인합니다.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

마지막으로 `TaskWebApp`과 `TaskService`를 모두 빌드하고 실행합니다. 사용자의 할 일 모음에 일부 작업을 만들고 클라이언트를 중지하고 다시 시작한 후에 API에서 어떻게 유지할지 확인합니다.

## <a name="edit-your-policies"></a>청책 편집

Azure AD B2C를 사용하여 API를 보호한 후에 로그인/등록 정책을 시험해 보고 API에서 효과(또는 부족)를 확인할 수 있습니다. 정책에서 응용 프로그램 클레임을 조작하고 Web API에서 사용할 수 있는 사용자 정보를 변경할 수 있습니다. 이 문서의 앞에서 설명한 것처럼 추가한 모든 클레임은 `ClaimsPrincipal` 개체의 .NET MVC Web API에서 사용할 수 있습니다.
