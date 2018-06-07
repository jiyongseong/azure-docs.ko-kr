---
title: Azure Active Directory 코드 샘플 | Microsoft Docs
description: 시나리오별로 구성된 Azure Active Directory(v1 엔드포인트) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157778"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Azure Active Directory 코드 샘플(V1 엔드포인트)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure AD(Azure Active Directory)를 사용하여 웹 응용 프로그램 및 Web API에 인증 및 권한 부여를 추가할 수 있습니다.

이 섹션에서는 Azure AD V1 엔드포인트에 대해 자세히 알아볼 수 있는 샘플 링크를 제공합니다. 이 샘플은 응용 프로그램에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 또한 코드는 중요한 섹션을 이해하는 데 도움이 되도록 주석으로 처리되어 있습니다.

> [!NOTE]
> Azure AD V2 코드 샘플에 관심이 있는 경우 [시나리오별 v2.0 코드 샘플](active-directory-v2-code-samples.md)을 참조하세요.

각 샘플 유형식에 대한 기본 시나리오를 이해하려면 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.

GitHub에서 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph 또는 Web API를 호출하는 데스크톱 및 모바일 공용 클라이언트의 응용 프로그램

다음 샘플은 사용자 이름으로 Microsoft Graph 또는 Web API에 액세스 하는 공용 클라이언트 응용 프로그램(데스크톱/모바일 응용 프로그램)을 보여줍니다.

클라이언트 응용 프로그램 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
------------------ | -------- | ---------- | -------------------- | -------------------------
데스크톱(WPF)           | .NET/C# | 대화형 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
모바일(UWP)            | .NET/C#  | 대화형 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store)(단일 테넌트 Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (multi-tenant Web API)|
모바일(Android, iOS, UWP)   | .NET/C#(Xamarin) | 대화형 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
모바일(Android)           | Android/Java | 대화형 |   [android](https://github.com/Azure-Samples/active-directory-android) |
모바일(iOS)           | iOS/Objective C | 대화형 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
데스크톱(콘솔)          | .NET/C# | 사용자 이름/암호 </p> Windows 통합 인증 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
데스크톱(콘솔)           | .NET Core/C# | 장치 프로필 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>웹 응용 프로그램

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>웹 응용 프로그램은 사용자 ID로 사용자를 로그인하고 Microsoft Graph 또는 Web API를 호출

 플랫폼 | 사용자만 로그인 | Microsoft Graph 또는 AAD Graph 호출| 또 다른 ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)(AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/)(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
파이썬 | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
자바 | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>역할 기반 액세스 제어(권한 부여)를 보여주는 웹 응용 프로그램

다음 샘플은 웹 응용 프로그램의 특정 기능 사용 권한을 특정 사용자로 제한하는 데 사용되는 역할 기반 액세스 제어 구현 방법을 보여줍니다. 사용자가 Azure AD 그룹 또는 역할에 속해 있는지 여부에 따라 권한이 부여됩니다.

플랫폼 | 샘플 | 설명
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Azure AD **그룹**을 권한 부여에 사용하는 .NET 4.5 MVC 웹앱
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD **역할**을 권한 부여에 사용하는 .NET 4.5 MVC 웹앱

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>디먼 응용 프로그램(응용 프로그램 ID를 사용하여 Web API에 액세스)

다음 샘플은 사용자 없이(응용 프로그램 ID로) Microsoft Graph 또는 web API에 액세스하는 데스크톱 또는 웹 응용 프로그램을 보여줍니다.

클라이언트 응용 프로그램 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
------------------ | -------- | ---------- | -------------------- | -------------------------
디먼 앱(콘솔)          | .NET/C#  | 앱 비밀 또는 인증서를 사용하는 클라이언트 자격 증명 | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
디먼 앱(콘솔)         | .NET Core/C# | 인증서를 사용하는 클라이언트 자격 증명| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
데스크톱            | 자바 | 클라이언트 자격 증명 |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET 웹앱  | .NET/C# | 클라이언트 자격 증명 |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory로 보호되는 Web API

다음 샘플은 Azure AD로 node.js web API를 보호하는 방법을 보여줍니다.

플랫폼 | 샘플 | 설명
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  Azure AD 및 OAuth 2.0 액세스 토큰을 사용하여 보호되는 NodeJS Web API입니다.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph 또는 다른 Web API를 호출하는 Web API

다음 샘플은 또 다른 web API를 호출하는 web API를 보여줍니다. 두 번째 샘플은 조건부 액세스를 처리하는 방법을 보여줍니다.

 플랫폼 |  Microsoft Graph 호출 | 또 다른 ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>단일 페이지 응용 프로그램

이 샘플에서는 Azure AD로 보안이 설정된 단일 페이지 응용 프로그램을 작성하는 방법을 보여 줍니다.

 플랫폼 |  Microsoft Graph 호출 | 자체 API 호출
 -------- |  --------------------- | -------------------------
JavaScript(Angular)/ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 샘플 및 자습서를 보려면 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

[Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
