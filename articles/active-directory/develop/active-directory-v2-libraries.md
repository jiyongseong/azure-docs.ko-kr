---
title: Azure Active Directory v2.0 인증 라이브러리 | Microsoft Docs
description: Azure Active Directory v2.0 끝점에 대한 호환 가능한 클라이언트 라이브러리 및 서버 미들웨어 라이브러리 및 관련된 라이브러리, 원본 및 샘플 링크.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8fe3db09acbdec606f25d0bc81300bc4f5e87411
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157397"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 인증 라이브러리

[Azure AD(Azure Active Directory) v2.0 끝점](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare)은 산업 표준 OAuth 2.0 및 OpenID Connect 1.0 프로토콜을 지원합니다. MSAL(Microsoft Authentication Library)은 Azure AD v2.0 엔드포인트에서 작동하도록 설계되었습니다. 또한 OAuth 2.0 및 OpenID Connect 1.0을 지원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

[Microsoft에서 따르는 방법론][Microsoft-SDL]과 같은 SDL(보안 개발 수명 주기) 방법론을 따르는 프로토콜 도메인 전문가가 작성한 라이브러리를 사용하는 것이 좋습니다. 프로토콜에 대한 코드를 직접 지원하려는 경우 Microsoft의 SDL 방법론을 따르고 각 프로토콜에 대한 표준 사양의 보안 고려 사항에 세심한 주의를 기울입니다.

> [!NOTE]
> Azure AD v1.0 라이브러리(ADAL)에 대한 내용은 [ADAL 라이브러리 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)를 참조하세요.
>
>

## <a name="types-of-libraries"></a>라이브러리 유형

Azure AD v2.0 끝점에서는 두 가지 유형의 라이브러리를 사용할 수 있습니다.

* **클라이언트 라이브러리** 네이티브 클라이언트 및 서버는 클라이언트 라이브러리를 사용하여 Microsoft Graph와 같은 리소스를 호출하기 위한 액세스 토큰을 가져옵니다.
* **서버 미들웨어 라이브러리** 웹앱은 사용자 로그인에 서버 미들웨어 라이브러리를 사용합니다. 웹 API는 서버 미들웨어 라이브러리를 사용하여 네이티브 클라이언트 또는 다른 서버에서 전송되는 토큰의 유효성을 검사합니다.

## <a name="library-support"></a>라이브러리 지원

V 2.0 끝점을 사용하는 경우 모든 표준 호환 라이브러리를 선택할 수 있기 때문에 지원하려는 위치를 알아야 합니다. 라이브러리 코드에서 문제 및 기능 요청은 라이브러리 소유자에게 문의합니다. 서비스 측 프로토콜 구현에서 문제 및 기능 요청은 Microsoft로 문의합니다. 프로토콜에서 확인하려는 추가 기능에 대한 [기능 요청을 작성합니다](https://feedback.azure.com/forums/169401-azure-active-directory). Azure AD v2.0 엔드포인트가 OAuth 2.0 또는 OpenID Connect 1.0과 호환되지 않는 문제가 발견되면 [지원 요청을 만듭니다](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request).

라이브러리는 두 지원 범주로 제공됩니다.

* **Microsoft 지원** Microsoft는 이러한 라이브러리에 대한 수정 프로그램을 제공하며, 이러한 라이브러리에 대한 SDL 실사를 완료했습니다.
* **호환 가능** Microsoft는 기본 시나리오에서 이러한 라이브러리를 테스트하고 v2.0 끝점에서 작동하는지를 확인했습니다. Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다.

v2.0 끝점을 사용하는 라이브러리 목록은 이 문서의 다음 섹션을 참조하세요.

## <a name="microsoft-supported-client-libraries"></a>Microsoft 지원 클라이언트 라이브러리

> [!IMPORTANT]
> MSAL 미리 보기 라이브러리는 프로덕션 환경에서 사용하기에 적합합니다. Microsoft는 이러한 라이브러리에 대해 현재 프로덕션 라이브러리(ADAL)와 동일한 프로덕션 수준의 지원을 제공합니다. 미리 보기 중에는 버그 수정 또는 향상된 기능과 함께 사용하는 데 필요한 이러한 라이브러리의 MSAL API, 내부 캐시 형식 및 다른 메커니즘이 예고 없이 변경될 수 있습니다. 이러한 변경은 응용 프로그램에 영향을 줄 수 있습니다. 예를 들어 캐시 형식을 변경하면 사용자가 다시 로그인해야 할 수 있습니다. API 변경으로 인해 코드를 업데이트해야 할 수 있습니다. GA (일반 공급) 릴리스를 사용할 수 있게 되면 라이브러리의 미리 보기 버전을 사용하는 모든 응용 프로그램이 6개월 이내에 업데이트되거나 작동하지 않을 수 있습니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET 클라이언트, Windows 스토어, UWP, Xamarin iOS 및 Android | MSAL .NET(미리 보기) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [데스크톱 앱](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js(미리 보기) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL(미리 보기) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 앱](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL(미리 보기) | [중앙 리포지토리](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 앱](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 지원 서버 미들웨어 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect 미들웨어 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[MVC 앱](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | AzureAD용 OWIN OAuth Bearer 미들웨어 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | .NET 4.5 JWT 핸들러 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect 미들웨어 |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC 앱](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth Bearer 미들웨어 |[Microsoft.AspNetCore.Authentication.OAuth(NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | .NET Core JWT 핸들러  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [웹앱](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>호환 가능한 클라이언트 라이브러리

| 플랫폼 | 라이브러리 이름 | 테스트 버전 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[네이티브 앱 샘플](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[네이티브 앱 샘플](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| 자바 | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [버전 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [버전 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>관련 콘텐츠
Azure AD v2.0 끝점에 대한 자세한 내용은 [Azure AD 앱 모델 v2.0 개요][AAD-App-Model-V2-Overview]를 참조하세요.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
