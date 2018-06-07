---
title: 인증 코드 흐름 - Azure AD B2C | Microsoft Docs
description: Azure AD B2C 및 OpenID Connect 인증 프로토콜을 사용하여 웹앱 빌드 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d8ed5747f29f969535bbafc1624d9d02e54c8418
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: OAuth 2.0 인증 코드 흐름
장치에 설치된 앱에서 OAuth 2.0 인증 코드 권한 부여를 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. OAuth 2.0의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하면 모바일 및 데스크톱 앱에 등록, 로그인 및 기타 ID 관리 작업을 추가할 수 있습니다. 이 문서는 언어 독립적입니다. 이 문서에서는 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

<!-- TODO: Need link to libraries -->

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](http://tools.ietf.org/html/rfc6749)에서 설명합니다. [웹앱](active-directory-b2c-apps.md#web-apps) 및 [기본적으로 설치된 앱](active-directory-b2c-apps.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여에 사용할 수 있습니다. OAuth 2.0 인증 코드 흐름을 사용하여 [권한 부여 서버](active-directory-b2c-reference-protocols.md#the-basics)를 통해 보안된 리소스에 액세스하는 데 사용할 수 있는 *액세스 토큰*을 앱이 안전하게 획득할 수 있습니다.

이 문서에서는 **공용 클라이언트** OAuth 2.0 인증 코드 흐름을 중점적으로 다룹니다. 공용 클라이언트는 보안 암호의 무결성을 안전하게 유지하기 위해 신뢰할 수 없는 클라이언트 응용 프로그램입니다. 모바일 앱, 데스크톱 앱 및 기본적으로 장치에서 실행되고 액세스 토큰이 필요한 모든 응용 프로그램이 포함됩니다. 

> [!NOTE]
> Azure AD B2C를 사용하여 ID 관리를 웹앱에 추가하려면 OAuth 2.0 대신 [OpenID Connect](active-directory-b2c-reference-oidc.md)를 사용합니다.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OAuth 2.0의 흐름을 확장합니다. Azure AD B2C는 [정책 매개 변수](active-directory-b2c-reference-policies.md)를 도입했습니다. 기본 제공 정책을 사용하면 OAuth 2.0을 통해 등록, 로그인 및 프로필 관리와 같은 사용자 환경을 앱에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 및 정책을 사용하여 네이티브 응용 프로그램에서 이러한 환경을 각각 구현하는 방법을 설명합니다. 또한 Web API에 액세스하기 위한 액세스 토큰을 얻는 방법을 보여 줍니다.

이 문서의 예제 HTTP 요청에서는 샘플 Azure AD B2C 디렉터리인 **fabrikamb2c.onmicrosoft.com**을 사용합니다. 또한 응용 프로그램 예제 및 정책도 사용합니다. 이러한 값을 사용하여 직접 요청을 시도하거나 사용자 고유의 값으로 바꿀 수 있습니다.
[사용자 고유의 Azure AD B2C 디렉터리, 응용 프로그램 및 정책을 가져오는](#use-your-own-azure-ad-b2c-directory) 방법을 알아봅니다.

## <a name="1-get-an-authorization-code"></a>1. 권한 부여 코드 가져오기
인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 끝점으로 보내는 것으로 시작됩니다. 사용자가 조치를 취하는 흐름의 대화형 부분입니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 `scope` 매개 변수에 나타냅니다. `p` 매개 변수에서 실행할 정책을 나타냅니다. 다음 세 가지 예제(쉽게 읽을 수 있도록 줄 바꿈 적용)에서는 각각 다른 정책을 사용합니다.

### <a name="use-a-sign-in-policy"></a>로그인 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>등록 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>편집 프로필 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |응답 유형입니다. 인증 코드 흐름의 경우 `code`를 포함해야 합니다. |
| redirect_uri |필수 |앱이 인증 응답을 보내고 받는 앱의 리디렉션 URI입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청되는 사용 권한을 Azure AD(Azure Active Directory)에 둘 다 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 대한 장기 액세스를 위해 앱에 새로 고침 토큰이 필요함을 나타냅니다. `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| response_mode |권장 |결과로 생성된 인증 코드를 앱에 다시 보내는 데 사용하는 방법입니다. `query`, `form_post` 또는 `fragment`일 수 있습니다. |
| state |권장 |토큰 응답에 반환되는 요청에 포함된 값입니다. 사용하려는 임의 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보를 인코드하는 데 사용됩니다. 예를 들어 사용자가 보고 있던 페이지 또는 실행 중이었던 정책입니다. |
| p |필수 |실행되는 정책입니다. Azure AD B2C 디렉터리에 생성된 정책의 이름입니다. 정책 이름 값은 **b2c\_1\_**로 시작해야 합니다. 정책에 대한 자세한 내용은 [Azure AD B2C 기본 제공 정책](active-directory-b2c-reference-policies.md)을 참조하세요. |
| prompt |옵션 |필요한 사용자 상호 작용 유형입니다. 현재 유효한 값은 `login`뿐이며, 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 정책의 워크플로를 완료하도록 사용자에게 요청합니다. 이 경우 사용자 이름과 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리를 등록하거나, 다른 단계를 수행할 수도 있습니다. 사용자 작업은 정책을 정의한 방식에 따라 다릅니다.

사용자가 정책을 완료하면 Azure AD에서 앱에 대한 응답을 `redirect_uri`에 사용한 값으로 반환합니다. `response_mode` 매개 변수에 지정된 메서드를 사용합니다. 응답은 실행된 정책과 별개로, 각 사용자 작업 시나리오에서 정확히 동일합니다.

`response_mode=query`를 사용하는 성공적인 응답은 다음과 같습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 매개 변수 | 설명 |
| --- | --- |
| 코드 |앱이 요청한 권한 부여 코드입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 인증 코드는 수명이 매우 짧습니다. 일반적으로 약 10분 후에 만료됩니다. |
| state |전체 설명은 이전 섹션의 첫 번째 표를 참조하세요. 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 오류 응답을 리디렉션 URI로 전송할 수도 있습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state |잎의 표에 나와 있는 전체 설명을 참조하세요. 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

## <a name="2-get-a-token"></a>2. 토큰 가져오기
인증 코드를 받았으므로 이제 POST 요청을 `/token` 끝점으로 전송하여 `code`를 의도한 리소스에 대한 토큰으로 교환할 수 있습니다. Azure AD B2C에서 토큰을 요청할 수 있는 리소스는 앱 자체의 백 엔드 Web API뿐입니다. 자체 토큰을 요청하는 데 사용되는 규칙은 앱의 클라이언트 ID를 범위로 사용하는 것입니다.

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |권한 부여 코드를 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 *쿼리 문자열*에 추가해야 합니다. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 응용 프로그램 ID입니다. |
| grant_type |필수 |권한 부여 유형입니다. 인증 코드 흐름에서 권한 부여 유형은 `authorization_code`여야 합니다. |
| scope |권장 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 대한 장기 액세스를 위해 앱에 새로 고침 토큰이 필요함을 나타냅니다.  `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| 코드 |필수 |흐름의 첫 번째 단계에서 얻은 권한 부여 코드입니다. |
| redirect_uri |필수 |인증 코드를 받은 응용 프로그램의 리디렉션 URI입니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 매개 변수 | 설명 |
| --- | --- |
| not_before |epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT(JSON Web Token)입니다. |
| scope |토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| expires_in |토큰이 유효한 기간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 장기적으로 존재합니다. 토큰을 사용하여 오랜 시간 동안 리소스에 대한 액세스를 유지할 수 있습니다. 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="3-use-the-token"></a>3. 토큰 사용
액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. 토큰 새로 고침
액세스 토큰 및 ID 토큰은 수명이 짧습니다. 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 POST 요청을 `/token` 끝점에 제출합니다. 여기에서는 `code` 대신 `refresh_token`을 제공합니다.

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |원래의 새로 고침 토큰을 얻는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 *쿼리 문자열*에 추가해야 합니다. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 응용 프로그램 ID입니다. |
| client_secret |필수 |[Azure Portal](https://portal.azure.com)에서 client_id에 연결된 client_secret |
| grant_type |필수 |권한 부여 유형입니다. 이 인증 코드 흐름 레그에서 권한 부여 유형은 `refresh_token`여야 합니다. |
| scope |권장 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 대한 장기 액세스를 위해 앱에 새로 고침 토큰이 필요함을 나타냅니다.  `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| redirect_uri |옵션 |인증 코드를 받은 응용 프로그램의 리디렉션 URI입니다. |
| refresh_token |필수 |흐름의 두 번째 레그에서 얻은 원본 새로 고침 토큰입니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 매개 변수 | 설명 |
| --- | --- |
| not_before |epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT입니다. |
| scope |토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| expires_in |토큰이 유효한 기간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>사용자 고유의 Azure AD B2C 디렉터리 사용
이러한 요청을 직접 시도하려면 다음 단계를 완료합니다. 이 문서에서 사용한 예제 값을 사용자 고유의 값으로 바꿉니다.

1. [Azure AD B2C 디렉터리를 만듭니다](active-directory-b2c-get-started.md). 요청에 디렉터리의 이름을 사용합니다.
2. [응용 프로그램을 만들어](active-directory-b2c-app-registration.md) 응용 프로그램 ID 및 리디렉션 URI를 얻습니다. 앱에 네이티브 클라이언트를 포함합니다.
3. [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

