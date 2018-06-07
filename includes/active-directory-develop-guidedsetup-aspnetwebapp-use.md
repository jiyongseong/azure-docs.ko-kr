---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 4b29d3383d97a6f8e10e4733ffe4b0e896e6cb60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>로그인 및 로그아웃 요청을 처리하는 컨트롤러 추가

이 단계에서는 로그인 및 로그아웃 메서드를 노출하는 새 컨트롤러를 만드는 방법을 보여 줍니다.

1.  `Controllers` 폴더를 마우스 오른쪽 단추로 클릭하고 `Add` > `Controller`를 선택합니다.
2.  `MVC (.NET version) Controller – Empty`를 선택합니다.
3.  *추가*를 클릭합니다.
4.  이름을 `HomeController`로 지정하고 *추가*를 클릭합니다.
5.  클래스에 *OWIN* 참조를 추가합니다.

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. 코드를 통해 인증 질문을 시작하여 로그인 및 로그아웃을 처리하는 아래의 두 메서드를 컨트롤러에 추가합니다.
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>로그인 단추를 통해 사용자를 로그인하는 앱의 홈페이지 만들기

Visual Studio에서 로그인 단추를 추가하고 인증 후 사용자 정보를 표시하는 새 보기를 만듭니다.

1.  `Views\Home` 폴더를 마우스 오른쪽 단추로 클릭하고 `Add View`를 선택합니다.
2.  이름을 `Index`로 지정합니다.
3.  로그인 단추를 포함하는 다음 HTML을 파일에 추가합니다.

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign-In with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>추가 정보
> 이 페이지는 SVG 형식으로 검은색 배경의 로그인 단추를 추가합니다.<br/>![Microsoft로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> 추가 로그인 단추는 [이 페이지](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "브랜딩 지침")를 참조하세요.
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>사용자의 클레임을 표시하는 컨트롤러 추가
이 컨트롤러는 컨트롤러를 보호하는 `[Authorize]` 특성의 사용을 보여 줍니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러에 대한 액세스를 제한합니다. 아래 코드에서는 이 특성을 활용하여 로그인의 일부로 검색된 사용자 클레임을 표시합니다.

1.  `Controllers` 폴더를 마우스 오른쪽 단추로 클릭하고 `Add` > `Controller`를 선택합니다.
2.  `MVC {version} Controller – Empty`를 선택합니다.
3.  *추가*를 클릭합니다.
4.  이름을 `ClaimsController`로 지정합니다.
5.  컨트롤러 클래스의 코드를 아래 코드로 바꿉니다. 그러면 클래스에 `[Authorize]` 특성이 추가됩니다.

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>추가 정보
> `[Authorize]` 특성을 사용하므로 이 컨트롤러의 모든 메서드는 사용자가 인증된 경우에만 실행할 수 있습니다. 사용자가 인증되지 않은 경우 컨트롤러에 액세스하려고 하면 OWIN에서 인증 질문을 시작하고 사용자가 강제로 인증하도록 합니다. 위의 코드는 사용자의 id 토큰에 포함된 특정 사용자 특성의 클레임 목록을 확인합니다. 이러한 특성에는 사용자의 전체 이름과 사용자 이름 및 전역 사용자 식별자 주체가 포함됩니다. 사용자의 조직에 대한 ID를 나타내는 *테넌트 ID*도 포함됩니다. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>사용자의 클레임을 표시하는 보기 만들기

Visual Studio에서 새 보기를 만들어 사용자의 클레임을 웹 페이지에 표시합니다.

1.  `Views\Claims` 폴더를 마우스 오른쪽 단추로 클릭하고 `Add View`를 선택합니다.
2.  이름을 `Index`로 지정합니다.
3.  다음 HTML을 파일에 추가합니다.

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign-In with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
