---
title: Azure Active Directory v2.0 토큰 참조 | Microsoft Docs
description: Azure AD v2.0 끝점에서 내보내는 토큰 및 클레임 형식
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7b9ad5c76b0e20a3c58bddcc4947482b237fb8f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164461"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 토큰 참조
Azure AD(Azure Active Directory) v2.0 끝점은 각 [인증 흐름](active-directory-v2-flows.md)에서 여러 유형의 보안 토큰을 내보냅니다. 이 참조에서는 각 토큰 유형의 형식, 보안 특성 및 내용을 설명합니다.

> [!NOTE]
> v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
>
>

## <a name="types-of-tokens"></a>토큰 형식
v2.0 끝점은 액세스 토큰 및 새로 고침 토큰을 사용하는 [OAuth 2.0 인증 프로토콜](active-directory-v2-protocols.md)을 지원합니다. 또한 [OpenID Connect](active-directory-v2-protocols.md)를 통한 인증 및 로그인을 지원합니다. OpenID Connect는 세 번째 유형의 토큰인 ID 토큰을 소개합니다. 이러한 토큰은 각각 *전달자* 토큰으로 표시됩니다.

전달자 토큰은 보호된 리소스에 대한 전달자 액세스 권한을 부여하는 간단한 보안 토큰입니다. 전달자는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하기 위해 Azure AD에 인증해야 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한이 없는 당사자가 토큰을 사용하지 못하게 하는 메커니즘이 기본 제공되지만 전달자 토큰에는 이 메커니즘이 없습니다. 전달자 토큰은 전송 계층 보안(HTTPS) 등의 보안 채널에서 전송해야 합니다. 전달자 토큰이 이 유형의 보안 없이 전송되는 경우 악의적인 당사자가 “메시지 가로채기(man-in-the-middle)” 공격으로 토큰을 획득하고 이를 사용하여 보호된 리소스에 무단으로 액세스할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 전달자 토큰을 안전하게 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

v2.0 끝점에서 발급된 토큰은 대부분 JWT(JSON Web Token)로 구현됩니다. JWT는 두 당사자 간에 정보를 전송하는 URL로부터 안전한 간단한 방법입니다. JWT의 정보를 *클레임*이라고 합니다. 이는 토큰의 주체 및 전달자에 대한 정보의 어설션입니다. JWT의 클레임은 전송을 위해 인코드 및 직렬화된 JSON(JavaScript Object Notation) 개체입니다. V2.0 끝점에 의해 발급된 JWT가 서명되었지만 암호화되지 않았으므로 디버깅을 위해 JWT의 내용을 쉽게 검사할 수 있습니다. JWT에 대한 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요.

### <a name="id-tokens"></a>ID 토큰
ID 토큰은 [OpenID Connect](active-directory-v2-protocols.md)를 사용하여 인증을 수행할 때 앱이 받는 로그인 보안 토큰의 한 형태입니다. ID 토큰은 [JWT](#types-of-tokens)로 표시되며 사용자로 앱에 로그인하는 데 사용할 수 있는 클레임을 포함합니다. 다양한 방법으로 ID 토큰에서 클레임을 사용할 수 있습니다. 일반적으로 관리자는 ID 토큰을 사용하여 계정 정보를 표시하거나 앱에서 액세스 제어를 결정합니다. v2.0 끝점은 로그인한 사용자 유형에 관계없이 일관된 클레임 집합을 포함하는 하나의 ID 토큰 유형만 발급합니다. ID 토큰의 형식 및 내용은 개인 Microsoft 계정 사용자와 회사 또는 학교 계정에 동일합니다.

ID 토큰은 현재 서명되었지만 암호화되지 않았습니다. 앱이 ID 토큰을 받으면 [서명의 유효성을 검사](#validating-tokens)하여 토큰의 신뢰성을 입증하고 토큰에 있는 몇 개 클레임의 유효성을 검사하여 유효성을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다를 수 있지만 앱은 모든 시나리오에서 몇 가지 [일반적인 클레임 유효성 검사](#validating-tokens) 를 수행해야 합니다.

ID 토큰 이외에 다음 섹션에도 ID 토큰의 클레임에 대한 자세한 내용이 나와 있습니다. ID 토큰의 클레임은 특정 순서로 반환되지 않습니다. 또한 언제든지 새 클레임을 ID 토큰에 도입할 수 있습니다. 새 클레임이 도입될 때 앱을 중단하지 않아야 합니다. 다음 목록에는 앱이 현재 안정적으로 해석할 수 있는 클레임이 포함되어 있습니다. 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)에서 확인할 수 있습니다.

#### <a name="sample-id-token"></a>샘플 ID 토큰
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> 연습을 위해 샘플 ID 토큰에 있는 클레임을 검사하려면 해당 토큰을 [jwt.ms](http://jwt.ms/)에 붙여넣으세요.
>
>

#### <a name="claims-in-id-tokens"></a>ID 토큰의 클레임
| Name | 클레임 | 예제 값 | 설명 |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |토큰의 의도한 수신자를 식별합니다. ID 토큰에서 대상은 Microsoft 응용 프로그램 등록 포털에서 앱에 할당된 앱의 응용 프로그램 ID입니다. 앱은 이 값의 유효성을 검사하고 값이 일치하지 않을 경우 토큰을 거부해야 합니다. |
| 발급자 |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |토큰을 생성하고 반환하는 STS(보안 토큰 서비스) 및 사용자가 인증된 Azure AD 테넌트를 식별합니다. 앱은 발급자 클레임의 유효성을 검사하여 토큰이 v2.0 끝점에서 제공된 것인지 확인해야 합니다. 또한 클레임의 GUID 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한할 수 있습니다. 사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 GUID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. |
| 발급 시간 |`iat` |`1452285331` |epoch 시간으로 표시된, 토큰이 발급된 시간입니다. |
| 만료 시간 |`exp` |`1452289231` |epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 이전이 아님 |`nbf` |`1452285331` |epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 발급 시간과 일반적으로 동일합니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 버전 |`ver` |`2.0` |Azure AD에서 정의된 ID 토큰의 버전입니다. V2.0 끝점의 경우 값은 `2.0`입니다. |
| 테넌트 ID |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |사용자가 속해 있는 Azure AD 테넌트를 나타내는 GUID입니다. 회사 및 학교 계정의 경우 GUID는 사용자가 속해 있는 조직의 변경 불가능 테넌트 ID입니다. 개인 계정의 경우 이 값은 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| 코드 해시 |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |코드 해시는 OAuth 2.0 권한 부여 코드와 함께 ID 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 인증 코드의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| 액세스 토큰 해시 |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |액세스 토큰 해시는 OAuth 2.0 액세스 토큰과 함께 ID 토큰이 발급된 경우에만 ID 토큰에 포함됩니다. 액세스 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| nonce |`nonce` |`12345` |nonce는 토큰 재생 공격을 완화하기 위한 전략입니다. 앱은 `nonce` 쿼리 매개 변수를 사용하여 권한 부여 요청에 nonce를 지정할 수 있습니다. 요청에 제공한 값은 수정되지 않고 ID 토큰의 `nonce` 클레임에 내보내집니다. 앱은 특정 ID 토큰과 앱 세션을 연결하는 요청에 지정된 값과 비교하여 값을 확인할 수 있습니다. 앱은 ID 토큰 유효성 검사 프로세스 중에 이 유효성 검사를 수행해야 합니다. |
| 이름 |`name` |`Babe Ruth` |이름 클레임은 토큰의 주체를 식별하는, 사람이 읽을 수 있는 값을 제공합니다. 이 값은 반드시 고유한 것은 아니며 변경 가능하고 표시 용도로만 사용하도록 디자인되었습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| email |`email` |`thegreatbambino@nyy.onmicrosoft.com` |만약 있다면 사용자 계정과 연결된 기본 전자 메일 주소입니다. 해당 값은 변경 가능하며 시간이 지남에 따라 변경될 수 있습니다. `email` 범위는 이 클레임을 받기 위해 필요합니다. |
| 기본 설정된 사용자 이름 |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |v2.0 끝점에서 사용자를 나타내는 기본 사용자 이름입니다. 메일 주소, 전화 번호 또는 지정된 형식이 없는 일반 사용자 이름일 수 있습니다. 해당 값은 변경 가능하며 시간이 지남에 따라 변경될 수 있습니다. 해당 값은 변경 가능하므로 권한 부여 결정을 내리는 데 사용되지 않아야 합니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어, 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행하고 데이터베이스 테이블에서 키로 사용할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다. 그러나 주체는 쌍으로 된 식별자이며 특정 응용 프로그램 ID에 고유합니다. 따라서 단일 사용자가 두 개의 다른 클라이언트 ID를 사용하여 두 개의 다른 앱에 로그인하는 경우 해당 앱은 주체 클레임에 두 개의 다른 값을 받게 됩니다. 아키텍처 및 개인 정보 보호 요구 사항에 따라 적합할 수도 있고 적합하지 않을 수도 있습니다. |
| 개체 ID |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | 이 경우에 Microsoft ID 시스템에 있는 개체의 변경할 수 없는 식별자는 사용자 계정입니다. 데이터베이스 테이블의 키로써 안전하게 권한 부여 검사를 수행하는 데 사용할 수도 있습니다. 이 ID는 응용 프로그램에서 사용자를 고유하게 식별합니다. 동일한 사용자가 로그인한 두 개의 다른 응용 프로그램은 `oid` 클레임에서 동일한 값을 받습니다. 즉, Microsoft Graph와 같은 Microsoft Online Services에 대한 쿼리를 수행할 때 사용할 수 있습니다. Microsoft Graph는 이 ID를 지정된 사용자 계정에 대한 `id` 속성으로 반환합니다. `oid`를 사용하면 여러 앱에서 사용자의 상관 관계를 지정하기 때문에 이 클레임을 수신하기 위해 `profile` 범위가 필요합니다. 단일 사용자가 여러 테넌트에 존재하는 경우 사용자는 각 테넌트에서 다른 개체 ID를 포함합니다. 사용자가 동일한 자격 증명으로 각 계정에 로그인하더라도 서로 다른 계정으로 간주됩니다. |

### <a name="access-tokens"></a>액세스 토큰

v2.0 엔드포인트는 Azure AD에 등록된 타사 앱이 Web API와 같은 안전한 리소스에 대한 액세스 토큰을 발급할 수 있도록 허용합니다. 액세스 토큰을 발급하도록 응용 프로그램을 설정하는 데 관한 자세한 내용은 [v2.0 엔드포인트로 앱을 등록하는 방법](active-directory-v2-app-registration.md)을 참조하세요. v2.0 엔드포인트로 응용 프로그램을 등록하면 개발자는 액세스 토큰이 발급되는 **범위**라고 하는 액세스의 수준을 지정할 수 있습니다. 예를 들어 Microsoft Graph API에 정의된 **calendars.read** 범위는 사용자의 일정을 읽을 수 있는 권한을 부여합니다. 응용 프로그램이 v2.0 엔드포인트에서 액세스 토큰을 받으면 시나리오에 따라 토큰의 서명, 발급자, 대상, 만료 시간 및 다른 클레임의 유효성을 검사해야 합니다. 

v2.0 끝점의 액세스 토큰을 요청하는 경우 앱이 사용할 수 있도록 액세스 토큰에 대한 메타데이터도 v2.0 끝점에서 반환됩니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효한 범위가 포함됩니다. 앱은 이 메타데이터를 사용하여 액세스 토큰 자체를 구문 분석하지 않고도 액세스 토큰의 지능형 캐싱을 수행할 수 있습니다.

### <a name="refresh-tokens"></a>새로 고침 토큰
새로 고침 토큰은 앱이 OAuth 2.0 흐름에서 새 액세스 토큰을 가져오는 데 사용할 수 있는 보안 토큰입니다. 앱은 새로 고침 토큰을 사용하여 사용자 상호 작용을 요구하지 않고도 사용자 대신 리소스에 장기적으로 액세스할 수 있습니다.

새로 고침 토큰은 다중 리소스입니다. 한 리소스에 대한 토큰 요청 중에 받은 새로 고침 토큰을 완전히 다른 리소스에 대한 액세스 토큰으로 교환할 수 있습니다.

토큰 응답에서 새로 고침을 받으려면 앱이 `offline_access` 범위를 요청하고 부여 받아야 합니다. `offline_access` 범위에 대한 자세한 내용은 [동의 및 범위](active-directory-v2-scopes.md) 문서를 참조하세요.

새로 고침 토큰은 앱에 완전히 불투명하며 항상 그럴 것입니다. 새로 고침 토큰은 Azure AD v2.0 끝점에서 발급되며 Azure AD v2.0 끝점에서만 검사되고 해석됩니다. 또한 새로 고침 토큰은 수명이 길지만 일정 기간 지속될 것으로 예상하도록 앱을 작성해서는 안 됩니다. 다양한 이유로 언제든지 새로 고침 토큰이 무효화될 수 있기 때문입니다. 자세한 내용은 [토큰 해지](active-directory-token-and-claims.md#token-revocation)를 참조하세요. 앱에서 새로 고침 토큰이 유효한지 확인하는 유일한 방법은 v2.0 끝점에 대한 토큰 요청을 수행하여 교환하는 것입니다.

새로 고침 토큰을 새 액세스 토큰으로 교환할 때 앱에 `offline_access` 범위가 부여된 경우 토큰 응답에 새로운 새로 고침 토큰을 받게 됩니다. 새로 발급된 새로 고침 토큰을 저장하여 요청에 사용한 토큰을 대체해야 합니다. 이렇게 하면 새로 고침 토큰이 최대한 오랫동안 유효한 상태로 유지되도록 할 수 있습니다.

## <a name="validating-tokens"></a>토큰 유효성 검사
현재 앱이 수행해야 하는 토큰 유효성 검사는 ID 토큰 유효성 검사뿐입니다. ID 토큰의 유효성을 검사하려면 앱이 ID 토큰의 서명과 ID 토큰에 있는 클레임의 유효성을 모두 검사해야 합니다.

<!-- TODO: Link -->
Microsoft는 토큰 유효성 검사를 쉽게 처리하는 방법을 보여 주는 라이브러리 및 코드 샘플을 제공합니다. 다음 섹션에서는 기본 프로세스를 설명합니다. JWT 유효성 검사에 사용할 수 있는 여러 타사 오픈 소스 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대해 하나 이상의 옵션이 있습니다.

### <a name="validate-the-signature"></a>서명의 유효성을 검사
JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 *헤더*, 두 번째 세그먼트는 *본문*, 세 번째 세그먼트는 *서명*이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 ID 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

ID 토큰은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. ID 토큰의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 있습니다. 예: 

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` 클레임은 토큰을 서명하는 데 사용된 알고리즘을 나타냅니다. `kid` 클레임은 토큰을 서명하는 데 사용된 공개 키를 나타냅니다.

언제든지 v2.0 끝점은 공개-개인 키 쌍의 특정 집합 중 하나를 사용하여 ID 토큰을 서명할 수 있습니다. v2.0 끝점은 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. v2.0 끝점에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 매 24시간입니다.

다음 위치에 있는 OpenID Connect 메타데이터 문서를 사용하여 서명 유효성 검사에 필요한 서명 키 데이터를 얻을 수 있습니다.

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 브라우저에서 이 URL을 사용해 보세요!
>
>

이 메타데이터 문서는 OpenID Connect 인증에 필요한 다양한 끝점의 위치 등 여러 유용한 정보가 있는 JSON 개체입니다. 이 문서에는 토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 *jwks_uri*도 포함합니다. jwks_uri에 있는 JSON 문서에는 현재 사용 중인 모든 공개 키 정보가 있습니다. 앱은 JWT 헤더에 `kid` 클레임을 사용하여 토큰 서명에 사용된 공개 키를 이 문서에서 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행합니다.

서명 유효성 검사 수행은 이 문서의 범위를 벗어납니다. 많은 오픈 소스 라이브러리가 도움이 될 수 있습니다.

### <a name="validate-the-claims"></a>클레임 유효성 검사
사용자 로그인 시 앱이 ID 토큰을 받은 경우 ID 토큰의 클레임에 대해서도 몇 가지 검사를 수행해야 합니다. 포함하지만 다음과 같이 제한되지 않습니다.

* **대상** 클레임 - ID 토큰이 앱에 제공하려고 의도했던 것이 맞는지 확인합니다.
* **이전이 아님** 및 **만료 시간** 클레임 - ID 토큰이 만료되지 않았는지 확인합니다.
* **발급자** 클레임 - v2.0 끝점이 앱에 토큰을 발급했는지 확인합니다.
* **nonce** - 토큰 재생 공격 완화로 사용됩니다.

앱이 수행해야 하는 클레임 유효성 검사의 전체 목록은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)을 참조하세요.

이러한 클레임의 예상 값에 대한 자세한 내용은 [ID 토큰](# ID tokens) 섹션에 포함되어 있습니다.

## <a name="token-lifetimes"></a>토큰 수명
다음 토큰 수명은 앱 개발 및 디버그에 유용할 수 있으므로 순수하게 이해를 돕기 위해 제공되었습니다. 이러한 수명이 일정하게 유지된다고 예상하도록 앱을 작성해서는 안 됩니다. 토큰 수명은 언제든지 변경될 수 있고 변경됩니다.

| 신뢰 | 수명 | 설명 |
| --- | --- | --- |
| ID 토큰(회사 또는 학교 계정) |1시간 |ID 토큰은 일반적으로 1시간 동안 유효합니다. 웹앱은 이 동일한 수명을 사용하여 사용자와의 자체 세션을 유지 관리하거나 완전히 다른 세션 수명을 선택할 수 있습니다. 앱이 새 ID 토큰을 가져와야 하는 경우 v2.0 권한 부여 끝점에 대한 새 로그인 요청을 만들어야 합니다. 사용자에게 v2.0 끝점과의 유효한 브라우저 세션이 있는 경우 자격 증명을 다시 입력할 필요가 없을 수도 있습니다. |
| ID 토큰(개인 계정) |24시간 |개인 계정에 대한 ID 토큰은 일반적으로 24시간 동안 유효합니다. 웹앱은 이 동일한 수명을 사용하여 사용자와의 자체 세션을 유지 관리하거나 완전히 다른 세션 수명을 선택할 수 있습니다. 앱이 새 ID 토큰을 가져와야 하는 경우 v2.0 권한 부여 끝점에 대한 새 로그인 요청을 만들어야 합니다. 사용자에게 v2.0 끝점과의 유효한 브라우저 세션이 있는 경우 자격 증명을 다시 입력할 필요가 없을 수도 있습니다. |
| 액세스 토큰(회사 또는 학교 계정) |1시간 |토큰 메타데이터의 일부로 토큰 응답에 표시됩니다. |
| 액세스 토큰(개인 계정) |1시간 |토큰 메타데이터의 일부로 토큰 응답에 표시됩니다. 개인 계정 대신 발급된 액세스 토큰은 다른 수명으로 구성할 수도 있지만 1시간이 일반적입니다. |
| 새로 고침 토큰(회사 또는 학교 계정) |최대 14일 |단일 새로 고침 토큰이 최대 14일 동안 유효합니다. 그러나 새로 고침 토큰은 다양한 이유로 언제든지 무효화될 수 있으므로 실패하거나 앱이 새로운 새로 고침 토큰으로 대체할 때까지 앱에서 계속 새로 고침 토큰을 시도하고 사용해야 합니다. 사용자가 자격 증명을 입력한 후 90일이 경과한 경우에도 새로 고침 토큰이 무효화됩니다. |
| 새로 고침 토큰(개인 계정) |최대 1년 |단일 새로 고침 토큰이 최대 1년 동안 유효합니다. 그러나 새로 고침 토큰은 다양한 이유로 언제든지 무효화될 수 있으므로 실패할 때까지 앱에서 계속 새로 고침 토큰을 시도하고 사용해야 합니다. |
| 권한 부여 코드(회사 또는 학교 계정) |10분 |권한 부여 코드는 의도적으로 수명이 짧으므로 받는 즉시 액세스 토큰 및 새로 고침 토큰으로 교환해야 합니다. |
| 권한 부여 코드(개인 계정) |5분 |권한 부여 코드는 의도적으로 수명이 짧으므로 받는 즉시 액세스 토큰 및 새로 고침 토큰으로 교환해야 합니다. 개인 계정 대신 발급된 권한 부여 코드는 일회용으로 사용됩니다. |
