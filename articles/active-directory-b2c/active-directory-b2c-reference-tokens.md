---
title: 토큰 참조 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C에서 발급된 토큰의 형식입니다.
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
ms.openlocfilehash: 09d776b54941e33979d7969b25c35e67a53cf8f0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: 토큰 참조

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 각 [인증 흐름](active-directory-b2c-apps.md)을 처리할 때 여러 형식의 보안 토큰을 내보냅니다. 이 문서에서는 각 토큰 유형의 형식, 보안 특성 및 내용을 설명합니다.

## <a name="types-of-tokens"></a>토큰 형식
Azure AD B2C는 액세스 토큰 및 새로 고침 토큰 둘 다를 활용하는 [OAuth 2.0 인증 프로토콜](active-directory-b2c-reference-protocols.md)을 지원합니다. 또한 세 번째 토큰 유형인 id_token을 도입하는 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 통해 인증 및 로그인을 지원합니다. 이러한 토큰은 각각 전달자 토큰으로 표시됩니다.

전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 전달자는 토큰을 제공할 수 있는 당사자입니다. Azure AD B2C에서 전달자 토큰을 받으려면 먼저 당사자를 인증해야 합니다. 하지만 전송 및 저장에 토큰을 보호하는 데 필요한 단계를 수행하지 않으면 의도하지 않은 당사자가 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한이 없는 당사자가 토큰을 사용하는 것을 막는 메커니즘이 기본으로 제공되지만 전달자 토큰에는 이 메커니즘이 없습니다. 전송 계층 보안(HTTPS) 등의 보안 채널에서 전송해야 합니다.

전달자 토큰이 보안 채널 밖으로 전송되는 경우 악의적인 당사자가 메시지 가로채기(man-in-the-middle) 공격으로 토큰을 획득하고 이를 사용하여 보호된 리소스에 무단으로 액세스할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다.

전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

Azure AD B2C에서 발급된 토큰은 대부분 JSON 웹 토큰(JWT)으로 구현됩니다. JWT는 두 요소 간에 정보를 전송하는 URL로부터 안전한 간단한 수단입니다. JWT는 클레임이라고 하는 정보를 포함합니다. 이들은 토큰의 주체 및 전달자에 대한 정보의 어설션입니다. JWT의 클레임은 전송을 위해 인코드 및 직렬화된 JSON 개체입니다. Azure AD B2C에서 발급된 JWT가 서명되었지만 암호화되지 않았으므로 디버깅을 위해 JWT의 내용을 쉽게 검사할 수 있습니다. 이 작업을 수행하는 데 [jwt.ms](https://jwt.ms)를 포함하여 여러 도구를 사용할 수 있습니다. JWT에 대한 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요.

### <a name="id-tokens"></a>ID 토큰

ID 토큰은 Azure AD B2C `/authorize` 및 `/token` 끝점에서 앱이 수신하는 보안 토큰의 형태입니다. ID 토큰은 [JWT](#types-of-tokens)로 표시되며 사용자를 앱에서 식별하는 데 사용할 수 있는 클레임을 포함합니다. `/authorize` 끝점에서 ID 토큰을 획득하는 경우 JavaScript 기반 웹 응용 프로그램에 로그인하는 사용자가 자주 사용하는 [암시적 흐름](active-directory-b2c-reference-spa.md)을 사용하도록 합니다. `/token` 끝점에서 ID 토큰을 획득하는 경우 브라우저에서 숨겨진 토큰을 유지하는 [기밀 코드 흐름](active-directory-b2c-reference-oidc.md)을 사용하도록 합니다. 이렇게 하면 토큰을 동일한 응용 프로그램 또는 서비스의 두 구성 요소 간의 통신에 대한 HTTP 요청에 안전하게 보낼 수 있습니다. 필요에 따라 ID 토큰에서 클레임을 사용할 수 있습니다. 계정 정보를 표시하거나 앱에서 액세스 제어를 결정할 수 있도록 하는 데 일반적으로 사용됩니다.  

ID 토큰은 서명되어 있지만, 현재 암호화되지 않습니다. 앱 또는 API에서 ID 토큰을 받으면 [서명의 유효성을 검사](#token-validation) 하여 토큰이 인증되었음을 증명해야 합니다. 앱 또는 API도 토큰에서도 일부 클레임의 유효성을 검사하여 유효하다는 것을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다를 수 있지만 앱은 모든 시나리오에서 몇 가지 [일반적인 클레임 유효성 검사](#token-validation) 를 수행해야 합니다.

#### <a name="sample-id-token"></a>샘플 ID 토큰
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>액세스 토큰

액세스 토큰은 또한 Azure AD B2C `/authorize` 및 `/token` 끝점에서 앱이 수신하는 보안 토큰의 형태입니다. 액세스 토큰은 또한 [JWT](#types-of-tokens)로 표시되며 API에 대한 부여된 권한을 식별하는 데 사용할 수 있는 클레임을 포함합니다. 액세스 토큰은 서명되어 있지만, 현재 암호화되지 않습니다. API 및 리소스 서버에 대한 액세스를 제공하는 데 액세스 토큰을 사용해야 합니다. [액세스 토큰을 사용하는 방법](active-directory-b2c-access-tokens.md)에 대해 자세히 알아보세요. 

API에서 액세스 토큰을 받으면 [서명의 유효성을 검사](#token-validation) 하여 토큰이 인증되었음을 증명해야 합니다. API는 또한 토큰에서 일부 클레임의 유효성을 검사하여 유효하다는 것을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다를 수 있지만 앱은 모든 시나리오에서 몇 가지 [일반적인 클레임 유효성 검사](#token-validation) 를 수행해야 합니다.

### <a name="claims-in-id-and-access-tokens"></a>ID 및 액세스 토큰의 클레임

Azure AD B2C를 사용하는 경우 토큰의 내용에 대해 정교하게 세분화된 조정을 해야 합니다. [정책](active-directory-b2c-reference-policies.md) 을 구성하여 해당 작업에 대해 앱이 필요한 클레임에서 사용자 데이터의 특정 집합을 전송할 수 있습니다. 이러한 클레임은 사용자의 `displayName` 및 `emailAddress`와 같은 표준 속성을 포함할 수 있습니다. 또한 B2C 디렉터리에서 정의할 수 있는 [사용자 지정 특성](active-directory-b2c-reference-custom-attr.md) 도 포함할 수 있습니다. 받는 모든 ID 및 액세스 토큰에는 보안 관련 클레임의 특정 집합이 포함되어 있습니다. 응용 프로그램에서 이러한 클레임을 사용하여 사용자 및 요청을 안전하게 인증할 수 있습니다.

ID 토큰에 있는 클레임은 특정 순서로 반환되지 않습니다. 또한 언제든지 새 클레임을 ID 토큰에 도입할 수 있습니다. 새 클레임이 도입되므로 앱을 해제하지 말아야 합니다. 다음은 Azure AD B2C에서 발급하는 ID 및 액세스 토큰에 있어야 하는 클레임입니다. 모든 추가 클레임은 정책에 따라 결정됩니다. 연습을 위해 샘플 ID 토큰에 있는 클레임을 [jwt.ms](https://jwt.ms)에 붙여넣어 검사하세요. 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)에서 확인할 수 있습니다.

| Name | 클레임 | 예제 값 | 설명 |
| --- | --- | --- | --- |
| 대상 |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |대상 클레임은 토큰의 의도된 수신자를 식별합니다. Azure AD B2C의 경우 대상은 앱 등록 포털에서 앱에 할당된 앱의 응용 프로그램 ID입니다. 앱은 이 값의 유효성을 검사하고 일치하지 않을 경우 토큰을 거부해야 합니다. |
| 발급자 |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |이 클레임은 토큰을 생성하고 반환하는 STS(보안 토큰 서비스)를 식별합니다. 또한 사용자가 인증하는 Azure AD 디렉터리도 식별합니다. 앱에서 발급자 클레임의 유효성을 검사하여 Azure Active Directory v2.0 끝점에서 제공한 토큰인지 확인해야 합니다. |
| 발급 시간 |`iat` |`1438535543` |이 클레임은 epoch 시간으로 표시된, 토큰이 발급된 시간입니다. |
| 만료 시간 |`exp` |`1438539443` |만료 시간 클레임은 epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 이전이 아님 |`nbf` |`1438535543` |이 클레임은 epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 일반적으로 토큰이 발급된 시간과 같습니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 버전 |`ver` |`1.0` |Azure AD에서 정의된 ID 토큰의 버전입니다. |
| 코드 해시 |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |코드 해시는 OAuth 2.0 인증 코드와 함께 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 코드 해시는 인증 코드의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요.  |
| 액세스 토큰 해시 |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |액세스 토큰 해시는 OAuth 2.0 액세스 토큰과 함께 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 액세스 토큰 해시는 액세스 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요.  |
| nonce |`nonce` |`12345` |nonce는 토큰 재생 공격을 완화하는 데 사용된 전략입니다. 앱은 `nonce` 쿼리 매개 변수를 사용하여 권한 부여 요청에 nonce를 지정할 수 있습니다. 요청에 제공한 값은 수정되지 않고 ID 토큰 만의 `nonce` 클레임에 내보내집니다. 따라서 앱이 지정된 ID 토큰과 앱 세션을 연결하는 요청에 지정된 값과 비교하여 값을 확인할 수 있습니다. 앱은 ID 토큰 유효성 검사 프로세스 중에 이 유효성 검사를 수행해야 합니다. |
| 제목 |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |토큰에서 앱 사용자와 같은 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. 기본적으로 주체 클레임은 디렉터리에 있는 사용자의 개체 ID로 채워집니다. 자세한 내용은 [Azure Active Directory B2C: 토큰, 세션 및 Single Sign-On 구성](active-directory-b2c-token-session-sso.md)을 참조하세요. |
| 인증 컨텍스트 클래스 참조 |`acr` |해당 없음 |이전 정책의 경우를 제외하고는 현재 사용되지 않습니다. 자세한 내용은 [Azure Active Directory B2C: 토큰, 세션 및 Single Sign-On 구성](active-directory-b2c-token-session-sso.md)을 참조하세요. |
| 보안 프레임워크 정책 |`tfp` |`b2c_1_sign_in` |ID 토큰을 회득하는 데 사용된 정책의 이름입니다. |
| 인증 시간 |`auth_time` |`1438535543` |이 클레임은 epoch 시간으로 표시된, 사용자가 자격 증명을 마지막으로 입력한 시간입니다. |

### <a name="refresh-tokens"></a>새로 고침 토큰
새로 고침 토큰은 앱이 OAuth 2.0 흐름에서 새 ID 토큰 및 액세스 토큰을 획득하는 데 사용할 수 있는 보안 토큰입니다. 이를 통해 앱은 사용자 상호 작용을 요구하지 않고 사용자 대신 리소스에 장기적으로 액세스할 수 있습니다.

토큰 응답에서 토큰 새로 고침을 받으려면 앱이 `offline_acesss` 범위를 요청해야 합니다. `offline_access` 범위에 대한 자세한 내용은 [Azure AD B2C 프로토콜 참조](active-directory-b2c-reference-protocols.md)를 참조하세요.

새로 고침 토큰은 앱에 완전히 불투명하며 항상 그럴 것입니다. 새로 고침 토큰은 Azure AD에서 발급되며 Azure AD에서만 검사 및 해석될 수 있습니다. 또한 새로 고침 토큰은 수명이 길지만 특정 기간 지속될 것으로 예상하도록 앱을 작성해서는 안 됩니다. 다양한 이유로 언제든지 새로 고침 토큰이 무효화될 수 있기 때문입니다. 앱에서 새로 고침 토큰이 유효한지 확인하는 유일한 방법은 Azure AD에 대한 토큰 요청을 수행하여 교환하는 것입니다.

새로 고침 토큰을 새 토큰으로 교환할 때 앱에 `offline_access` 범위가 부여된 경우 토큰 응답에 새로운 새로 고침 토큰을 받게 됩니다. 새로 발급된 새로 고침 토큰을 저장해야 합니다. 이전에 요청에 사용된 새로 고침 토큰을 바꿔야 합니다. 이렇게 하면 새로 고침 토큰이 최대한 오랫동안 유효한 상태로 유지되도록 할 수 있습니다.

## <a name="token-validation"></a>토큰 유효성 검사
토큰의 유효성을 검사하려면 앱이 해당 토큰의 서명과 클레임을 모두 확인해야 합니다.

기본 설정의 언어에 따라 JWT의 유효성 검사에 다양한 오픈 소스 라이브러리를 사용할 수 있습니다. 고유한 유효성 검사 논리보다는 이러한 옵션을 탐색하는 것이 좋습니다. 이 가이드의 정보를 통해 해당 라이브러리를 올바르게 사용하는 방법을 알아볼 수 있습니다.

### <a name="validate-the-signature"></a>서명의 유효성을 검사
JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 *헤더*, 두 번째 세그먼트는 *본문*, 세 번째 세그먼트는 *서명*입니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

Azure AD B2C 토큰은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. 토큰의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` 클레임은 토큰을 서명하는 데 사용된 알고리즘을 나타냅니다. `kid` 클레임은 토큰을 서명하는 데 사용된 특정 공개 키를 나타냅니다.

Azure AD는 특정 시점에서 공개-개인 키 쌍의 특정 집합 중 하나를 사용하여 토큰에 서명할 수 있습니다. Azure AD는 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. Azure AD에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 24시간마다입니다.

Azure AD B2C에는 OpenID Connect 메타데이터 끝점이 있습니다. 이를 통해 앱은 런타임 시 Azure AD B2C에 대한 정보를 가져올 수 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 디렉터리에 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 정책에 대한 메타데이터 문서는 다음 위치에 있습니다.

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`은 사용자를 인증하는 데 사용되는 B2C 디렉터리이고 `b2c_1_sign_in`는 토큰을 가져오는 데 사용되는 정책입니다. 토큰을 서명하는 데 어떤 정책을 사용할지(그리고 메타데이터를 가져오기 위해 이동하는 위치)를 결정하기 위해 두 가지 옵션이 있습니다. 먼저 정책 이름은 토큰의 `acr` 클레임에 포함됩니다. base-64로 본문을 디코딩하고 결과 JSON 문자열을 역직렬화하여 JWT의 본문에서 클레임을 구문 분석할 수 있습니다. `acr` 클레임은 토큰을 발급하는 데 사용된 정책의 이름입니다.  다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 정책을 인코딩한 다음 이를 디코딩하여 어떤 정책을 사용할지 결정하는 것입니다. 두 방법 중 하나는 유효합니다.

메타데이터 문서는 몇 가지 유용한 정보를 포함하는 JSON 개체입니다. 여기에는 OpenID Connect 인증을 수행하는 데 필요한 끝점의 위치가 포함됩니다. 토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 `jwks_uri`도 포함합니다. 해당 위치는 여기에 제공되어 있지만 메타데이터 문서를 사용하고 `jwks_uri`를 구문 분석하여 위치를 동적으로 가져오는 것이 가장 좋습니다.

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

이 URL에 있는 JSON 문서에는 특정 시점에서 사용 중인 공개 키 정보가 모두 포함되어 있습니다. 앱은 JWT 헤더에 `kid` 클레임을 사용하여 특정 토큰 서명에 사용된 JSON 문서의 공개 키를 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행할 수 있습니다.

서명 유효성 검사를 수행하는 방법에 대한 설명은 이 문서의 범위를 벗어납니다. 많은 오픈 소스 라이브러리는 필요할 경우 도움이 될 수 있습니다.

### <a name="validate-the-claims"></a>클레임 유효성 검사
앱 또는 API가 ID 토큰을 받은 경우 ID 토큰의 클레임에 대해서도 몇 가지 검사를 수행해야 합니다. 포함하지만 다음과 같이 제한되지 않습니다.

* **대상** 클레임: ID 토큰이 앱에 제공된 것이 맞는지 확인합니다.
* **이전이 아님** 및 **만료 시간** 클레임: ID 토큰이 만료되지 않았는지 확인합니다.
* **발급자** 클레임: Azure AD에서 앱에 토큰을 발급했는지 확인합니다.
* **nonce**: 토큰 재생 공격 완화를 위한 전략입니다.

앱에서 수행해야 하는 유효성 검사의 전체 목록은 [OpenID Connect 사양](https://openid.net)을 참조하세요. 이러한 클레임의 예상 값에 대한 자세한 내용은 위의 [토큰 섹션](#types-of-tokens)에 포함되어 있습니다.  

## <a name="token-lifetimes"></a>토큰 수명
이해를 돕기 위해 다음 토큰 수명이 제공됩니다. 디버그 앱을 개발할 때 도움이 될 수 있습니다. 이러한 수명이 일정하게 유지된다고 예상하도록 앱을 작성해서는 안 됩니다. 언제든지 변경될 수 있고 변경됩니다. Azure AD B2C의 [토큰 수명 사용자 지정](active-directory-b2c-token-session-sso.md)에서 자세히 알아보세요.

| 신뢰 | 수명 | 설명 |
| --- | --- | --- |
| ID 토큰 |1시간 |ID 토큰은 일반적으로 1시간 동안 유효합니다. 웹앱은 이 수명을 사용하여 사용자와의 자체 세션을 유지 관리할 수 있습니다(권장). 다른 세션 수명을 선택할 수도 있습니다. 앱이 새 ID 토큰을 가져와야 하는 경우 Azure AD에 대해 새 로그인 요청을 만들면 됩니다. Azure AD를 통해 유효한 브라우저 세션이 사용자에게 있는 경우 해당 사용자는 자격 증명을 다시 입력하지 않아도 됩니다. |
| 새로 고침 토큰 |최대 14일 |단일 새로 고침 토큰이 최대 14일 동안 유효합니다. 그러나 새로 고침 토큰은 여러 가지 이유로 언제든지 무효화될 수 있습니다. 앱은 요청이 실패하거나 앱이 새로 고침 토큰을 새 것으로 바꿀 때까지 새로 고침 토큰을 사용하도록 계속 시도합니다. 또한 사용자가 마지막으로 자격 증명을 입력한 후 90일이 지나면 새로 고침 토큰이 무효화될 수 있습니다. |
| 권한 부여 코드 |5분 |권한 부여 코드는 의도적으로 수명이 단기입니다. 권한 부여 코드가 수신되면 액세스 토큰, ID 토큰 또는 새로 고침 토큰에 대해 즉시 교환해야 합니다. |

