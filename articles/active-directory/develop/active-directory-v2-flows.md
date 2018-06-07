---
title: Azure Active Directory v2.0 끝점에 대한 앱 형식 | Microsoft Docs
description: Azure Active Directory v2.0 끝점에서 지원되는 앱 형식 및 시나리오입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 134199c879f6793cb4ed0a88cf0593786341f6d8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157689"
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Azure Active Directory v2.0 끝점에 대한 앱 형식
Azure AD(Azure Active Directory) v2.0 끝점은 모두 업계 표준 프로토콜 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다. 이 문서에서는 기본 설정 언어 또는 플랫폼에 관계없이 Azure AD v2.0을 사용하여 빌드할 수 있는 앱 형식을 설명합니다. 이 문서의 정보는 [코드 작업을 시작](active-directory-appmodel-v2-overview.md#getting-started)하기 전에 개략적인 시나리오를 이해하는 데 도움이 됩니다.

> [!NOTE]
> v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
> 
> 

## <a name="the-basics"></a>기본 사항
v2.0 끝점을 사용하는 각 앱을 [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com)에 등록해야 합니다. 앱 등록 프로세스는 앱에 대한 다음 값을 수집하고 할당합니다.

* 앱을 고유하게 식별하는 **응용 프로그램 ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
* 다른 몇 가지 시나리오 관련 값.

자세한 내용은 [앱 등록](active-directory-v2-app-registration.md)방법을 참조하세요.

앱을 등록하면 앱에서 Azure AD v2.0 끝점으로 요청을 보내 Azure AD와 통신합니다. Microsoft에서는 이러한 요청에 대한 세부 정보를 처리하는 오픈 소스 프레임워크 및 라이브러리를 제공합니다. 또한 이러한 끝점에 대한 요청을 만들어 인증 논리를 직접 구현할 수 있습니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>웹 앱
사용자가 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, Node)의 경우 사용자 로그인에 [OpenID Connect](active-directory-v2-protocols.md)를 사용할 수 있습니다. OpenID Connect에서는 웹앱이 ID 토큰을 받습니다. ID 토큰은 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰입니다.

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

[v2.0 토큰 참조](active-directory-v2-tokens.md)에서 앱이 사용할 수 있는 모든 토큰 및 클레임 형식에 대해 알아볼 수 있습니다.

웹 서버 앱에서 로그인 인증 흐름은 다음과 같은 높은 수준의 단계를 수행합니다.

![웹앱 인증 흐름](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

v2.0 끝점에서 받은 공개 서명 키로 ID 토큰의 유효성을 검사하여 사용자 ID를 확인할 수 있습니다. 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키가 설정됩니다.

이 시나리오의 작동 방식을 확인하려면 v2.0 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션의 웹앱 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 REST API와 같은 다른 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹 서버 앱은 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)을 사용하여 결합된 OpenID Connect 및 OAuth 2.0 흐름에 참여합니다. 이 시나리오에 대한 자세한 내용은 [웹앱 및 Web API 시작](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)을 참조하세요.

## <a name="web-apis"></a>Web API
v2.0 끝점을 사용하여 앱의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. ID 토큰 및 세션 쿠키 대신 Web API는 OAuth 2.0 액세스 토큰을 사용하여 데이터 보안을 유지하고 들어오는 요청을 인증합니다. Web API 호출자는 다음과 같이 HTTP 요청의 인증 헤더에 액세스 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API는 액세스 토큰을 사용하여 API 호출자의 ID를 확인하고 액세스 토큰에 인코딩된 클레임에서 호출자에 대한 정보를 추출합니다. 앱에 사용할 수 있는 모든 토큰 및 클레임 형식에 대한 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

Web API를 통해 사용자는 [범위](active-directory-v2-scopes.md)라고도 하는 사용 권한을 노출하여 특정 기능이나 데이터를 옵트인(opt-in)하거나 옵트아웃(opt-out)할 수 있습니다. 호출 앱이 범위에 대한 사용 권한을 얻으려면 사용자가 흐름 중 범위에 동의해야 합니다. v2.0 끝점은 사용자에게 사용 권한을 요청한 다음 Web API가 받은 모든 액세스 토큰에 이러한 사용 권한을 기록합니다. Web API는 각 호출에서 받은 액세스 토큰의 유효성을 검사하고 권한 부여 검사를 수행합니다.

Web API는 웹 서버 앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 Web API까지 포함하여 모든 유형의 앱에서 액세스 토큰을 받을 수 있습니다. Web API에 대한 개략적인 흐름은 다음과 같습니다.

![Web API 인증 흐름](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

OAuth 2.0 액세스 토큰을 사용하여 Web API 보안을 유지하는 방법을 알아보려면 [시작 섹션](active-directory-appmodel-v2-overview.md#getting-started)에서 Web API 코드 샘플을 확인하세요.

대부분의 경우 웹 API는 Azure Active Directory로 보호되는 다른 다운스트림 API에 대해 아웃바운드 요청도 해야 합니다. 이렇게 하기 위해 웹 API는 Azure AD의 **On Behalf Of** 흐름을 활용하여 웹 API가 아웃바운드 요청에서 사용할 다른 액세스 토큰에 대해 들어오는 액세스 토큰을 교환하도록 할 수 있습니다. v2.0 끝점의 On Behalf Of 흐름은 [여기서 자세히 설명](active-directory-v2-protocols-oauth-on-behalf-of.md)합니다.

## <a name="mobile-and-native-apps"></a>모바일 및 네이티브 앱
모바일 및 데스크톱 앱과 같은 장치 설치 앱은 데이터를 저장하고 사용자 대신 기능을 수행하는 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 이러한 앱은 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols-oauth-code.md)을 사용하여 백 엔드 서비스에 로그인 및 권한 부여를 추가할 수 있습니다.

이 흐름에서 앱은 사용자 로그인 시 v2.0 끝점에서 권한 부여 코드를 받습니다. 권한 부여 코드는 현재 로그인한 사용자를 대신해 백 엔드 서비스를 호출할 앱의 사용 권한을 나타냅니다. 앱은 백그라운드에서 권한 부여 코드를 OAuth 2.0 액세스 토큰 및 새로 고침 토큰으로 교환할 수 있습니다. 앱은 액세스 토큰을 사용하여 HTTP 요청 시 Web API에 인증할 수 있고 새로 고침 토큰을 사용하여 이전 액세스 토큰 만료 시 새 액세스 토큰을 가져올 수 있습니다.

![네이티브 앱 인증 흐름](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>단일 페이지 앱(JavaScript)
대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 단일 페이지 앱 프런트 엔드가 있습니다. Azure AD v2.0 끝점은 [OAuth 2.0 암시적 흐름](active-directory-v2-protocols-implicit.md)을 사용하여 이러한 앱을 지원합니다.

이 흐름에서 앱은 서버 간 교환 없이 v 2.0 인증 끝점에서 직접 토큰을 수신합니다. 모든 인증 논리 및 세션 처리가 추가 페이지 리디렉션 없이 전적으로 JavaScript 클라이언트에서 발생합니다.

![암시적 인증 흐름](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션의 단일 페이지 앱 코드 샘플 중 하나를 시도해 보세요.

## <a name="daemons-and-server-side-apps"></a>디먼 및 서버 쪽 앱
장기 실행 프로세스가 있거나 사용자와의 상호 작용 없이 작동하는 앱은 Web API와 같은 보안 리소스에 액세스하는 방법도 필요로 합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다.

이 흐름에서 앱은 `/token` 끝점과 직접 상호 작용하여 끝점을 가져옵니다.

![디먼 앱 인증 흐름](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

디먼 앱을 빌드하려면 [클라이언트 자격 증명 설명서](active-directory-v2-protocols-oauth-client-creds.md)를 참조하거나 [.NET 샘플 앱](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 사용해 보세요.
