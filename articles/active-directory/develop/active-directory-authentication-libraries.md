---
title: Azure Active Directory 인증 라이브러리 | Microsoft Docs
description: Azure AD 인증 라이브러리(ADAL)는 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 Active Directory(AD)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
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
ms.openlocfilehash: 143ee99c581db052c1dbcbf46decce356ee96fa6
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258059"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 인증 라이브러리

Azure ADAL(Active Directory 인증 라이브러리) v1.0은 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 AD(Active Directory)에 사용자를 인증하고 API 호출 보안을 위한 토큰을 가져올 수 있게 합니다. ADAL은 개발자가 다음과 같은 기능을 통해 더 쉽게 인증하도록 합니다.

- 액세스 토큰 및 새로 고침 토큰을 저장하는 구성 가능한 토큰 캐시
- 액세스 토큰이 만료되고 새로 고침 토큰을 사용할 수 있을 때 자동 토큰 새로 고침
- 비동기 메서드 호출에 대한 지원

> [!NOTE]
> Azure AD v2.0 라이브러리(MSAL)에 대한 내용은 [MSAL 라이브러리 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 참조하세요.
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft 지원 클라이언트 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET 클라이언트, Windows 스토어, UWP, Xamarin iOS 및 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [데스크톱 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[참조](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET 클라이언트, Windows 스토어, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [데스크톱 앱](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [참조](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[중앙 리포지토리](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js 웹앱](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)| |
| 자바 |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java 웹앱](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | |
| 파이썬 |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python 웹앱](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | [참조](http://adal-python.readthedocs.io/en/latest/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft 지원 서버 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET |AzureAD용 OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OpenIDConnect용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[웹앱](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |WS-Federation용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 웹앱](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |.NET 4.5용 ID 프로토콜 확장 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |.NET 4.5 JWT 핸들러 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [앱 API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>시나리오

다음은 원격 리소스에 액세스하는 클라이언트에서 ADAL을 사용하는 세 가지 일반적인 시나리오입니다.

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>장치에서 실행되는 네이티브 클라이언트 응용 프로그램의 사용자 인증

이 시나리오에서는 개발자가 web API 같은 원격 리소스에 액세스해야 하는 모바일 클라이언트 또는 데스크톱 응용 프로그램을 갖고 있습니다. Web API는 익명 호출을 허용하지 않으므로 인증된 사용자의 컨텍스트에서 호출해야 합니다. web API는 특정 Azure AD 테넌트에서 발급한 액세스 토큰을 신뢰하도록 미리 구성됩니다. Azure AD는 해당 리소스에 대한 액세스 토큰을 발급하도록 미리 구성됩니다. 클라이언트에서 web API를 호출하기 위해 개발자는 ADAL을 사용하여 Azure AD를 보다 쉽게 인증합니다. ADAL을 사용하는 가장 안전한 방법은 사용자 인터페이스를 렌더링하여 사용자 자격 증명을 수집하게 하는 것입니다(브라우저 창으로 렌더링).

ADAL을 사용하면 쉽게 사용자를 인증하고 Azure AD에서 액세스 토큰 및 새로 고침 토큰을 가져오고, 액세스 토큰을 사용하여 웹 API를 호출할 수 있습니다.

Azure AD에 대한 인증을 사용하여 이 시나리오를 설명하는 코드 샘플의 경우 [웹 API에 네이티브 클라이언트 WPF 응용 프로그램](https://github.com/azureadsamples/nativeclient-dotnet)을 참조하세요.

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>웹 서버에서 실행되는 비밀 클라이언트 응용 프로그램 인증

이 시나리오에서는 개발자가 web API 같은 원격 리소스에 액세스해야 하는 서버에서 실행되는 응용 프로그램을 갖고 있습니다. web API는 원격 호출을 허용하지 않으므로 권한이 부여된 서비스에서 호출해야 합니다. web API는 특정 Azure AD 테넌트에서 발급한 액세스 토큰을 신뢰하도록 미리 구성됩니다. Azure AD는 클라이언트 자격 증명(클라이언트 ID 및 비밀)이 있는 서비스에 해당 리소스의 액세스 토큰을 발급하도록 미리 구성됩니다. ADAL을 사용하면 간편하게 서비스를 Azure AD에 인증할 수 있으며 web API를 호출하는 데 사용할 수 있는 액세스 토큰을 반환합니다. ADAL은 또한 액세스 토큰을 캐시화하고 필요한 대로 갱신하여 액세스 토큰의 수명 관리도 처리합니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 디먼 콘솔 응용 프로그램](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>사용자를 대신하여 서버에서 실행되는 비밀 클라이언트 응용 프로그램 인증

이 시나리오에서는 개발자가 web API 같은 원격 리소스에 액세스해야 하는 서버에서 실행되는 웹 응용 프로그램을 갖고 있습니다. web API는 원격 호출을 허용하지 않으므로 인증된 사용자를 대신하여 권한이 부여된 서비스에서 호출해야 합니다. web API는 특정 Azure AD 테넌트에서 발급한 액세스 토큰을 신뢰하도록 미리 구성되고, Azure AD는 클라이언트 자격 증명이 있는 서비스에 해당 리소스의 액세스 토큰을 발급하도록 미리 구성됩니다. 사용자가 웹 응용 프로그램에 인증되면 응용 프로그램이 Azure AD에서 사용자에 대한 권한 부여 코드를 가져올 수 있습니다. 그러면 웹 응용 프로그램에서 ADAL을 사용하여 Azure AD의 응용 프로그램과 연결된 클라이언트 자격 증명과 인증 코드를 사용하는 사용자 대신 액세스 토큰과 새로고침 토큰을 얻을 수 있습니다. 웹 응용 프로그램을 액세스 토큰에서 소유하게 되면 토큰이 만료될 때까지 웹 API를 호출할 수 있습니다. 토큰이 만료되면 웹 응용 프로그램이 ADAL을 사용하여 이전에 받은 새로고침 토큰을 사통해 새 액세스 토큰을 가져올 수 있습니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 네이티브 클라이언트](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)
- [Azure Active directory 인증 시나리오](active-directory-authentication-scenarios.md)
- [Azure Active Directory 코드 샘플](active-directory-code-samples.md)
