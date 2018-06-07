---
title: 개발자용 Azure Active Directory | Microsoft Docs
description: 이 문서에서는 Azure Active Directory를 사용하여 Microsoft 회사 및 학교 계정에 로그인하는 방법의 개요를 제공합니다.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f3c0e93b20bbc570f4715318a49b502549ff295
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257552"
---
# <a name="azure-active-directory-for-developers"></a>개발자용 Azure Active Directory

Azure AD(Azure Active Directory)는 개발자가 Microsoft 회사 또는 학교 계정으로 사용자를 안전하게 로그인하는 앱을 빌드할 수 있는 클라우드 ID 서비스입니다. Azure AD는 단일 테넌트 및 기간 업무 앱을 모두 빌드하는 개발자와 다중 테넌트 앱을 개발하는 개발자를 지원합니다. 기본 로그인 외에도, Azure AD는 앱에서 [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) 같은 Microsoft API와 Azure AD 플랫폼에서 빌드되는 사용자 지정 API를 호출하는 것을 허용합니다. 이 설명서에서는 OAuth2.0 및 OpenID Connect 같은 산업 표준 프로토콜을 사용하여 응용 프로그램에 Azure AD 지원을 추가하는 방법을 보여줍니다.

> [!NOTE]
> 이 페이지의 콘텐츠는 대부분 Microsoft 회사 또는 학교 계정만 지원하는 Azure AD v1.0 엔드포인트에 집중합니다. 소비자 또는 개인 Microsoft 계정에 로그인하려면 [Azure AD v2.0 엔드포인트](active-directory-appmodel-v2-overview.md)에 대한 정보를 참조하세요. Azure AD v2.0 엔드포인트는 Azure AD 계정(회사 및 학교)과 개인용 Microsoft 계정으로 두 사용자를 모두 로그인해야 하는 앱을 위한 통합 개발자 환경을 제공합니다.

| | |
| --- | --- |
|[인증 기본 사항](active-directory-authentication-scenarios.md) | Azure AD를 사용하는 인증에 대한 소개입니다. |
|[응용 프로그램 유형](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD에서 지원하는 인증 시나리오에 대한 개요입니다. |      
| | |

## <a name="get-started"></a>시작하기
다음에 제공된 설정은 ADAL(Azure AD 인증 라이브러리) SDK를 사용하여 선호하는 플랫폼에서 앱을 빌드하는 과정을 안내합니다. MSAL(Microsoft 인증 라이브러리) 사용에 대한 내용은 [Azure AD v2.0 엔드포인트](active-directory-appmodel-v2-overview.md)에 대한 설명서를 참조하세요.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![모바일 데스크톱 앱](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />모바일 데스크톱 앱</center> | [개요](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET(WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![웹앱](./media/active-directory-developers-guide/Web_app.png)<br />웹앱</center> | [개요](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![단일 페이지 앱](./media/active-directory-developers-guide/SPA.png)<br />단일 페이지 앱</center> | [개요](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [개요](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![서비스 간](./media/active-directory-developers-guide/Service_App.png)<br />서비스 간</center> | [개요](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>방법 가이드
이 가이드는 Azure AD에서 가장 일반적인 작업 몇 가지를 안내합니다.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[응용 프로그램 등록](active-directory-integrating-applications.md)           | Azure AD에서 응용 프로그램을 등록하는 방법입니다. |
|[다중 테넌트 응용 프로그램](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft 회사 계정에 로그인하는 방법입니다. |
|[OAuth 및 OpenID Connect 프로토콜](active-directory-protocols-openid-connect-code.md)| Microsoft 인증 프로토콜을 사용하여 사용자를 로그인하고 웹 API를 호출하는 방법입니다. |
|  |  |

## <a name="reference-topics"></a>참조 항목
다음 문서는 API, 프로토콜 메시지 및 Azure AD에서 사용되는 용어에 대한 자세한 정보를 제공합니다.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [인증 라이브러리(ADAL)](active-directory-authentication-libraries.md)   | Azure AD에서 제공하는 라이브러리 및 SDK에 대한 개요입니다. |
| [코드 샘플](active-directory-code-samples.md)                                  | 모든 Azure AD 코드 샘플의 목록입니다. |
| [용어](active-directory-dev-glossary.md)                                      | 이 설명서에 사용된 용어 및 단어에 대한 정의입니다. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
