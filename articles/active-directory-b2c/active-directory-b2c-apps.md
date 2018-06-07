---
title: 응용 프로그램의 형식 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C에서 빌드할 수 있는 응용 프로그램의 유형입니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: davidmu1
ms.openlocfilehash: 0329cb9e49196f77ba12940e5987487eb2b6fda9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: 응용 프로그램 유형
Azure AD(Azure Active Directory) B2C는 다양한 최신 앱 아키텍처의 인증을 지원합니다. 모두 업계 표준 프로토콜인 [OAuth 2.0](active-directory-b2c-reference-protocols.md) 또는 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 기반으로 합니다. 이 문서에서는 선호하는 언어 또는 플랫폼에 독립적으로 빌드할 수 있는 앱 유형에 대해 간략하게 설명합니다. 또한 [응용 프로그램 빌드를 시작](active-directory-b2c-overview.md)하기 전에 대략적인 시나리오에 대한 이해를 돕습니다

## <a name="the-basics"></a>기본 사항
Azure AD B2C를 사용하는 모든 앱은 [Azure Portal](https://portal.azure.com/)을 통해 [B2C 디렉터리](active-directory-b2c-get-started.md)에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

* 앱을 고유하게 식별하는 **응용 프로그램 ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
* 다른 모든 시나리오 관련 값. 자세한 내용은 [앱 등록](active-directory-b2c-app-registration.md)방법을 참조하세요.

Azure AD B2C에 전송되는 각 요청은 **정책**을 지정합니다. 정책은 Azure AD의 동작을 제어합니다. 또한 이러한 끝점을 사용하여 사용자 환경에 대해 고도로 사용자 지정 가능한 집합을 만들 수 있습니다. 공통 정책에는 등록 정책, 로그인 정책 및 프로필 편집 정책이 포함됩니다. 정책에 익숙하지 않은 경우 계속 읽기 전에 Azure AD B2C의 [확장할 수 있는 정책 프레임워크](active-directory-b2c-reference-policies.md) 에 대해 읽어야 합니다.

모든 앱의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 앱이 사용자를 v2.0 끝점으로 보내어 [정책](active-directory-b2c-reference-policies.md)을 실행합니다.
2. 사용자는 정책 정의에 따라 정책을 완료합니다.
3. 앱이 v2.0 끝점에서 일종의 보안 토큰을 받습니다.
4. 앱이 보안 토큰을 사용하여 보호된 정보 또는 보호된 리소스에 액세스합니다.
5. 리소스 서버가 보안 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
6. 앱이 주기적으로 보안 토큰을 새로 고칩니다.

<!-- TODO: Need a page for libraries to link to -->
이러한 단계는 빌드 중인 앱의 유형에 따라 약간씩 다를 수 있습니다.

## <a name="web-apps"></a>웹 앱
서버에서 호스팅되며 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, Node.js 등)의 경우 Azure AD B2C는 모든 사용자 환경에 [OpenID Connect](active-directory-b2c-reference-protocols.md) 를 지원합니다. 여기에는 로그인, 등록 및 프로필 관리가 포함됩니다. OpenID Connect의 Azure AD B2C 구현에서 웹앱은 Azure AD로 인증 요청을 발급하여 이러한 사용자 환경을 시작합니다. 요청의 결과는 `id_token`입니다. 이 보안 토큰은 사용자의 ID를 나타냅니다. 또한 클레임 형태로 사용자에 대한 정보를 제공합니다.

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

[B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 앱이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

웹앱에서 [정책](active-directory-b2c-reference-policies.md) 의 각 실행에서는 이러한 수준 높은 단계를 수행합니다.

![웹앱 스윔 레인 이미지](./media/active-directory-b2c-apps/webapp.png)

Azure AD에서 수신한 공개 서명 키를 사용하여 `id_token` 의 유효성을 확인하는 것으로 사용자의 ID를 충분히 확인할 수 있습니다. 또한 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키도 설정합니다.

이 시나리오의 작동 방식을 확인하려면 [시작 섹션](active-directory-b2c-overview.md)의 웹앱 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 백 엔드 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹앱은 약간 다른 [OpenID Connect 흐름](active-directory-b2c-reference-oidc.md) 을 수행하고 권한 부여 코드를 사용하여 토큰을 획득하며 토큰을 새로 고칠 수 있습니다. 이 시나리오는 다음 [Web API 섹션](#web-apis)에서 설명합니다.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Web API
Azure AD B2C을 사용하여 앱의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. Web API는 토큰을 사용하는 들어오는 HTTP 요청을 인증하여 해당 데이터를 보호하는 데 OAuth 2.0을 사용할 수 있습니다. Web API 호출자는 HTTP 요청의 권한 부여 헤더에 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

그러면 Web API는 토큰을 사용하여 API 호출자의 ID를 확인하고 토큰에 인코드된 클레임에서 호출자에 대한 정보를 추출할 수 있습니다. [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 앱이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

> [!NOTE]
> Azure AD B2C는 현재 고유한 잘 알려진 클라이언트에서 액세스하는 Web API만을 지원합니다. 예를 들어 전체 앱이 iOS 앱, Android 앱 및 백 엔드 Web API를 포함할 수도 있습니다. 이 아키텍처를 완전히 지원합니다. 다른 iOS 앱과 같은 파트너 클라이언트에서 동일한 Web API에 액세스하는 것은 현재 지원되지 않습니다. 전체 앱의 모든 구성 요소는 단일 응용 프로그램 ID를 공유해야 합니다.
>
>

Web API는 웹앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 Web API까지 포함하여 많은 유형의 클라이언트에서 토큰을 받을 수 있습니다. 다음은 Web API를 호출하는 웹앱에 대한 전체 흐름을 보여주는 예입니다.

![웹앱 Web API 스윔 레인 이미지](./media/active-directory-b2c-apps/webapi.png)

권한 부여 코드, 새로 고침 토큰 및 토큰을 가져오는 단계에 대한 자세한 내용은 [OAuth 2.0 프로토콜](active-directory-b2c-reference-oauth-code.md)을 참조하세요.

Azure AD B2C를 사용하여 Web API를 보호하는 방법을 알아보려면 [시작 섹션](active-directory-b2c-overview.md)에서 Web API 자습서를 확인하세요.

## <a name="mobile-and-native-apps"></a>모바일 및 네이티브 앱
모바일 및 데스크톱 앱과 같은 장치에 설치된 앱은 사용자 대신 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 네이티브 앱에 사용자 지정된 ID 관리 환경을 추가하고 Azure AD B2C 및 [OAuth 2.0 권한 부여 코드 흐름](active-directory-b2c-reference-oauth-code.md)을 사용하여 안전하게 백 엔드 서비스를 호출할 수 있습니다.  

이 흐름에서 앱은 [정책](active-directory-b2c-reference-policies.md)을 실행하고 사용자가 정책을 완료하면 Azure AD에서 `authorization_code`을(를) 수신합니다. `authorization_code` 는 현재 로그인한 사용자를 대신하여 백 엔드 서비스를 호출할 앱의 사용 권한을 나타냅니다. 그러면 앱은 백그라운드에서 `id_token` 및 `refresh_token`에 대한 `authorization_code`을(를) 교환할 수 있습니다.  앱은 HTTP 요청에서 백 엔드 Web API를 인증하는 데 `id_token` 을 사용할 수 있습니다. 또한 이전 항목이 만료된 경우 `refresh_token`을 사용하여 새 `id_token`을 가져올 수도 있습니다.

> [!NOTE]
> Azure AD B2C는 현재 앱 자체의 백 엔드 웹 서비스에 액세스하는 데 사용되는 토큰만을 지원합니다. 예를 들어 전체 앱이 iOS 앱, Android 앱 및 백 엔드 Web API를 포함할 수도 있습니다. 이 아키텍처를 완전히 지원합니다. iOS 앱이 OAuth 2.0 액세스 토큰을 사용하여 파트너 Web API에 액세스하는 작업은 현재 지원되지 않습니다. 전체 앱의 모든 구성 요소는 단일 응용 프로그램 ID를 공유해야 합니다.
>
>

![네이티브 앱 스윔 레인 이미지](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>현재 제한 사항
Azure AD B2C는 다음과 같은 유형의 앱을 현재 지원하지 않지만 로드맵에 있습니다. 

### <a name="daemonsserver-side-apps"></a>디먼/서버 쪽 앱
장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 앱은 Web API와 같은 보안 리소스에 액세스하는 방법도 필요로 합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID를 사용하여 인증하고 토큰을 가져올 수 있습니다.

이 흐름은 Azure AD B2C에서 현재 지원되지 않습니다. 이러한 앱은 대화형 사용자 흐름이 발생한 후에만 토큰을 가져올 수 있습니다.

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API 체인(On-Behalf-Of 흐름)
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 Web API 백 엔드를 가지고 있는 네이티브 클라이언트에서 일반적입니다. 그런 다음 Azure AD Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다.  그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.
