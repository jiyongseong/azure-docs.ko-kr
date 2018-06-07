---
title: Azure Active Directory 코드 샘플 | Microsoft Docs
description: 시나리오별로 구성된 사용 가능한 Azure Active Directory(V2 엔드포인트) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b7a894ccd27ddcda2ab4b98c69333d37de077863
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156078"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Azure Active Directory 코드 샘플(V2 엔드포인트)

Microsoft Azure AD(Azure Active Directory)를 사용하여 다음 작업을 수행할 수 있습니다.

- 웹 응용 프로그램 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Azure AD V2 엔드포인트에 대해 간략하게 설명하고 샘플 링크를 제공합니다. 이 샘플은 응용 프로그램에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 코드 안에 있는 주석은 중요한 섹션을 이해하는 데 도움을 주기 위한 용도입니다.

> [!NOTE]
> V1 샘플에 관심이 있는 경우 [Azure AD 코드 샘플(V1 엔드포인트)](active-directory-code-samples.md)을 참조하세요.

각 샘플 형식에 대한 기본 시나리오를 이해하려면 [Azure Active Directory v2.0 엔드포인트에 대한 앱 형식](active-directory-v2-flows.md)을 참조하세요.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플은 사용자 이름으로 Microsoft Graph 또는 Web API에 액세스 하는 공용 클라이언트 응용 프로그램(데스크톱/모바일 응용 프로그램)을 보여줍니다.

클라이언트 응용 프로그램 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core 2.0 Web API 호출
------------------ | -------- | ---------- | -------------------- | -------------------------
데스크톱(WPF)      | .NET/C#  | 대화형 | [dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
모바일(UWP)   | .NET/C#(UWP) | 대화형 | [dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
모바일(Android, iOS, UWP)   | .NET/C#(Xamarin) | 대화형 | [xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
모바일(iOS)       | iOS/Objective C 또는 swift | 대화형 | [ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
모바일(Android)   | Android/Java | 대화형 |   [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>웹 응용 프로그램

다음 샘플에서는 사용자 ID로 사용자를 로그인하고, Microsoft Graph를 호출하고, web API를 호출하는 웹 응용 프로그램을 보여줍니다.

 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [aspnet-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>디먼 응용 프로그램

다음 샘플은 응용 프로그램 ID로(사용자 없음) Microsoft Graph 또는 web API에 액세스하는 데스크톱 또는 웹 응용 프로그램을 보여줍니다.

클라이언트 응용 프로그램 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 
------------------ | -------- | ---------- | -------------------- 
웹앱 | .NET/C#  | 클라이언트 자격 증명 | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>SPA(단일 페이지 응용 프로그램)

이 샘플에서는 Azure AD로 보안이 설정된 단일 페이지 응용 프로그램을 작성하는 방법을 보여 줍니다.

 플랫폼 |  Microsoft Graph 호출 
 -------- |  --------------------- 
JavaScript(msal.js)  | [javascript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript(msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript(Hello.JS)  | [javascript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript(hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-ad"></a>Azure AD로 보호되는 Web API

다음 샘플은 Azure AD V2 엔드포인트로 web API를 보호하는 방법을 보여줍니다.

플랫폼 | 샘플 | 설명
 -------- | ------------------- | ---------------------
Node.js | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Azure AD V2를 사용하여 WPF 응용 프로그램에서 ASP.NET Core Web API를 호출합니다.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph 또는 다른 Web API를 호출하는 Web API

이 샘플은 아직 사용할 수 없습니다.

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

[Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
