---
title: 액세스 토큰 요청 - Azure AD B2C | Microsoft Docs
description: 이 문서에서는 클라이언트 응용 프로그램을 설정하고 액세스 토큰을 획득하는 방법을 보여 줍니다.
services: active-directory-b2c
documentationcenter: android
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.openlocfilehash: bd919543072a8d2bf5fb0ebba17e69ba2f467218
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: 액세스 토큰 요청

액세스 토큰(Azure AD B2C의 응답에서 **액세스\_토큰**으로 표시됨)은 클라이언트가 웹 API와 같은 [권한 부여 서버](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)로 보호되는 리소스에 액세스하는 데 사용할 수 있는 보안 토큰의 형식입니다. 액세스 토큰은 [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens)로 표시되고 원하는 리소스 서버에 대한 정보 및 서버에 부여된 사용 권한을 포함합니다. 리소스 서버를 호출할 때 액세스 토큰은 HTTP 요청에 있어야 합니다.

이 문서에서는 **액세스\_토큰**을 얻기 위해 클라이언트 응용 프로그램 및 웹 API를 구성하는 방법을 설명합니다.

> [!NOTE]
> **Azure AD B2C에서는 Web API 체인(On-Behalf-Of)을 지원하지 않습니다.**
>
> 많은 아키텍처에는 다른 다운스트림 웹 API를 호출해야 하는 웹 API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 웹 API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Azure AD Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.
>
> On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 웹 API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

## <a name="register-a-web-api-and-publish-permissions"></a>웹 API 등록 및 권한 게시

액세스 토큰을 요청하기 전에 먼저 웹 API를 등록하고 클라이언트 응용 프로그램에 부여할 수 있는 권한(범위)을 게시해야 합니다.

### <a name="register-a-web-api"></a>웹 API 등록

1. Azure Portal의 Azure AD B2C 기능 메뉴에서 **응용 프로그램**을 클릭합니다.
1. 메뉴의 위쪽에서 **+추가**를 클릭합니다.
1. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso API"를 입력할 수 있습니다.
1. **웹앱/ 웹 API 포함** 스위치를 **예**로 설정합니다.
1. **회신 URL**에 대한 임의 값을 입력합니다. 예를 들어 `https://localhost:44316/`을 입력합니다. API가 Azure AD B2C에서 직접 토큰을 받지 않아야 하므로 값은 중요하지 않습니다.
1. **앱 ID URI**를 입력합니다. Web API에 사용되는 식별자입니다. 예를 들어 상자에 '참고'를 입력합니다. **앱 ID URI**는 `https://{tenantName}.onmicrosoft.com/notes`가 됩니다.
1. **만들기** 를 클릭하여 응용 프로그램을 등록합니다.
1. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.

### <a name="publishing-permissions"></a>권한 게시

권한과 유사한 범위는 앱에서 API를 호출하는 경우에 필요합니다. 범위의 일부 예는 "읽기" 또는 "쓰기"입니다. 웹 또는 네이티브 앱이 API에서 "읽기"를 원한다고 가정합니다. 앱은 Azure AD B2C를 호출하고 범위 "읽기"에 대한 액세스를 제공하는 액세스 토큰을 요청합니다. Azure AD B2C가 이러한 액세스 토큰을 내보내기 위해 앱은 특정 API에서 "읽기"에 대한 권한을 부여 받아야 합니다. 이렇게 하려면 API는 먼저 "읽기" 범위를 게시해야 합니다.

1. Azure AD B2C **응용 프로그램** 메뉴 내에서 웹 API 응용 프로그램("Contoso API")을 엽니다.
1. **게시된 범위**를 클릭합니다. 다른 응용 프로그램에 부여할 수 있는 사용 권한(범위)을 정의한 위치입니다.
1. 필요에 따라 **범위 값**을 추가합니다(예: "읽기"). 기본적으로 "user_impersonation" 범위를 정의합니다. 원하는 경우 이를 무시할 수 있습니다. **범위 이름** 열에 범위에 대한 설명을 입력합니다.
1. **저장**을 클릭합니다.

> [!IMPORTANT]
> **범위 이름**은 **범위 값**에 대한 설명입니다. 범위를 사용할 때 **범위 값**을 사용해야 합니다.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>웹 API에 네이티브 또는 웹앱 권한 부여

API가 범위를 게시하도록 구성되면 클라이언트 응용 프로그램은 Azure Portal을 통해 해당 범위 권한을 부여 받아야 합니다.

1. Azure AD B2C 기능 메뉴의 **응용 프로그램** 메뉴로 이동합니다.
1. 이미 없는 클라이언트 응용 프로그램([웹앱](active-directory-b2c-app-registration.md#register-a-web-app) 또는 [네이티브 클라이언트](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app))을 등록합니다. 시작 지점부터 이 가이드를 따르는 경우 클라이언트 응용 프로그램을 등록해야 합니다.
1. **API 액세스**를 클릭합니다.
1. **추가**를 클릭합니다.
1. 웹 API 및 부여하려는 범위(권한)를 선택합니다.
1. **확인**을 클릭합니다.

> [!NOTE]
> Azure AD B2C는 클라이언트 응용 프로그램 사용자의 동의를 묻지 않습니다. 대신 모든 동의는 위에서 설명한 응용 프로그램 간에 구성된 권한에 따라 관리자에 의해 제공됩니다. 응용 프로그램에 대한 권한 부여가 해지되면 이전에 해당 권한을 획득할 수 있었던 모든 사용자는 더 이상 권한을 획득할 수 없습니다.

## <a name="requesting-a-token"></a>토큰 요청

액세스 토큰을 요청할 때 클라이언트 응용 프로그램에서 요청의 **범위** 매개 변수에서 원하는 권한을 지정해야 합니다. 예를 들어 `https://contoso.onmicrosoft.com/notes`의 **앱 ID URI**가 있는 API에 대한 **범위 값** "읽기"를 지정하기 위해 범위는 `https://contoso.onmicrosoft.com/notes/read`가 됩니다. 다음은 `/authorize` 끝점에 대한 인증 코드 요청의 예입니다.

> [!NOTE]
> 현재 사용자 지정 도메인은 액세스 토큰과 함께 지원되지 않습니다. 요청 URL에서 tenantName.onmicrosoft.com 도메인을 사용해야 합니다.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

같은 요청에 여러 권한을 얻기 위해 단일 **범위** 매개 변수에 공백으로 구분된 여러 항목을 추가할 수 있습니다. 예: 

디코딩된 URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

인코딩된 URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

클라이언트 응용 프로그램에 부여된 것보다 더 많은 범위(권한)를 리소스에 대해 요청할 수 있습니다. 이 경우 하나 이상의 권한이 부여되면 호출이 성공합니다. 결과 **액세스\_토큰**은 성공적으로 부여된 권한으로 채워진 해당 "scp" 클레임을 갖습니다.

> [!NOTE] 
> 동일한 요청에 두 개의 다른 웹 리소스에 대한 권한 요청을 지원하지 않습니다. 이러한 종류의 요청은 실패합니다.

### <a name="special-cases"></a>특수 사례

OpenID Connect 표준은 몇 가지 특별한 "범위" 값을 지정합니다. 다음과 같은 특별한 범위는 "사용자의 프로필에 액세스"에 대한 권한을 나타냅니다.

* **openid**: ID 토큰 요청
* **오프라인\_액세스**: 새로 고침 토큰을 요청합니다([인증 코드 흐름](active-directory-b2c-reference-oauth-code.md) 사용).

`/authorize` 요청의 `response_type` 매개 변수가 `token`을 포함하는 경우 `scope` 매개 변수는 권한이 부여될 하나 이상의 리소스 범위(`openid` 및 `offline_access` 이외)를 포함해야 합니다. 그렇지 않은 경우 `/authorize` 요청은 실패와 함께 종료됩니다.

## <a name="the-returned-token"></a>반환된 토큰

성공적으로 생성된 **액세스\_토큰**(`/authorize` 또는 `/token` 끝점에서)에 다음 클레임이 표시됩니다.

| Name | 클레임 | 설명 |
| --- | --- | --- |
|대상 |`aud` |토큰에서 액세스를 부여하는 단일 리소스의 **응용 프로그램 ID**입니다. |
|범위 |`scp` |리소스에 부여된 권한입니다. 여러 부여된 권한은 공백으로 구분됩니다. |
|권한 있는 주체 |`azp` |요청을 시작한 클라이언트 응용 프로그램의 **응용 프로그램 ID**입니다. |

API에서 **액세스\_토큰**을 받으면 [토큰의 유효성을 검사](active-directory-b2c-reference-tokens.md)하여 토큰이 인증되었으며 올바른 클레임을 가졌음을 증명해야 합니다.

Microsoft는 사용자 의견 및 제안을 항상 환영합니다! 이 항목에 문제가 있는 경우 태그 ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c)를 사용하여 Stack Overflow에 게시하세요. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에 추가해 주세요.

## <a name="next-steps"></a>다음 단계

* [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)를 사용하여 웹 API 빌드
* [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)를 사용하여 웹 API 빌드
