---
title: Azure AD에서 지원하는 다른 토큰 및 클레임 유형에 대해 알아보기 | Microsoft Docs
description: AAD(Azure Active Directory)에서 발급하는 SAML 2.0 및 JWT(JSON 웹 토큰)를 이해하고 평가하기 위한 가이드입니다.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 95ce83a3f1288d1b731aeeb8dcc32e58bcaefe21
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157924"
---
# <a name="azure-ad-token-reference"></a>Azure AD 토큰 참조
Azure AD(Azure Active Directory)는 각 인증 흐름의 처리 과정에서 여러 유형의 보안 토큰을 내보냅니다. 이 문서에서는 각 토큰 유형의 형식, 보안 특성 및 내용을 설명합니다. 

## <a name="types-of-tokens"></a>토큰 형식
Azure AD는 access_tokens 및 refresh_tokens 둘 다를 활용하는 [OAuth 2.0 인증 프로토콜](active-directory-protocols-oauth-code.md)을 지원합니다. 또한 세 번째 토큰 유형인 id_token을 도입하는 [OpenID Connect](active-directory-protocols-openid-connect-code.md)를 통해 인증 및 로그인을 지원합니다. 이러한 토큰은 각각 "전달자 토큰"으로 표시됩니다.

전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 전달자 토큰을 수신하기 위해 Azure AD 인증이 필요한 경우에 의도하지 않은 당사자가 가로채기를 방지하기 위해 토큰 보안 설정 단계를 수행해야 합니다. 전달자 토큰에는 권한이 없는 제삼자의 사용을 방지하기 위한 기본 제공 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 메시지 가로채기 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

Azure AD에서 발급된 토큰은 대부분 JSON 웹 토큰, 즉 JWT로 구현됩니다. JWT는 두 요소 간에 정보를 전송하는 URL로부터 안전한 간단한 수단입니다. JWT에 포함된 정보를 "클레임" 또는 토큰의 전달자 및 주체에 대한 정보 어설션이라고 합니다. JWT의 클레임은 전송을 위해 인코드 및 직렬화된 JSON 개체입니다. Azure AD에서 발급된 JWT가 서명되었지만 암호화되지 않았으므로 디버깅을 위해 JWT의 내용을 쉽게 검사할 수 있습니다. [jwt.ms](https://jwt.ms/) 등 이러한 작업에 사용할 수 있는 여러 도구가 있습니다. JWT에 대한 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요.

## <a name="idtokens"></a>Id_tokens
id_token은 [OpenID Connect](active-directory-protocols-openid-connect-code.md)를 사용하여 인증을 수행할 때 앱이 받는 로그인 보안 토큰의 한 형태입니다. [JWT](#types-of-tokens)로 표시되며 사용자를 앱에 로그인하는 데 사용할 수 있는 클레임을 포함합니다. id_token의 클레임을 적절하게 사용할 수 있습니다. 일반적으로 계정 정보를 표시하거나 앱에서 액세스 제어 결정을 내리는 데 사용됩니다.

지금은 Id_token이 서명되었지만 암호화되지 않았습니다. 앱이 id_token을 받으면 [서명의 유효성을 검사](#validating-tokens)하여 토큰의 신뢰성을 입증하고 토큰에 있는 몇 개 클레임의 유효성을 검사하여 유효성을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다르지만 앱이 모든 시나리오에서 수행해야 하는 몇 가지 [일반적인 클레임 유효성 검사](#validating-tokens) 가 있습니다.

샘플 id_token 뿐만 아니라 id_tokens 클레임에 대한 자세한 내용은 다음 섹션을 참조합니다. id_token의 클레임은 특정 순서로 반환되지 않습니다. 또한 언제든지 id_token에 새 클레임이 도입될 수 있으므로 새 클레임이 도입될 때 앱이 손상되지 않아야 합니다. 다음 목록에는 이 문서가 작성될 때 앱이 안정적으로 해석할 수 있는 클레임이 포함되어 있습니다. 필요한 경우 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)에서 자세한 내용을 확인할 수 있습니다.

#### <a name="sample-idtoken"></a>샘플 id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> 연습을 위해 샘플 id_token에 있는 클레임을 [jwt.ms](https://jwt.ms/)에 붙여넣어 검사하세요.
> 
> 

#### <a name="claims-in-idtokens"></a>id_token의 클레임
> [!div class="mx-codeBreakAll"]
| JWT 클레임 | Name | 설명 |
| --- | --- | --- |
| `aud` |대상 |토큰의 의도한 수신자입니다. 토큰을 받는 응용 프로그램에서는 대상 값이 올바른지 확인하여 대상이 다른 모든 토큰을 거부해야 합니다. <br><br> **SAML 값 예제**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **JWT 값 예제**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |응용 프로그램 인증 컨텍스트 클래스 참조 |클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값이 0입니다. 클라이언트 ID 및 클라이언트 암호가 사용되면 값이 1입니다. <br><br> **JWT 값 예제**: <br> `"appidacr": "0"` |
| `acr` |인증 컨텍스트 클래스 참조 |응용 프로그램 인증 컨텍스트 클래스 참조 클레임의 클라이언트와는 반대로 주체가 인증된 방법을 나타냅니다. 값 "0"은 최종 사용자 인증이 ISO/IEC 29115 요구 사항을 충족하지 못했다는 뜻입니다. <br><br> **JWT 값 예제**: <br> `"acr": "0"` |
| 인증 인스턴트 |인증이 발생한 날짜 및 시간을 기록합니다. <br><br> **SAML 값 예제**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |인증 방법 |토큰의 주체가 인증된 방법을 식별합니다. <br><br> **SAML 값 예제**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **JWT 값 예제**: `“amr”: ["pwd"]` |
| `given_name` |이름 |Azure AD 사용자 개체에 설정된 대로 사용자의 이름 또는 "지정된 이름"을 제공합니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"given_name": "Frank"` |
| `groups` |그룹 |주체의 그룹 멤버 자격을 나타내는 개체 ID를 제공합니다. 이러한 값은 고유하며(개체 ID 참조) 리소스 액세스 시 강제로 인증하게 하는 경우처럼 액세스 관리에 안전하게 사용할 수 있습니다. 그룹 클레임에 포함된 그룹은 응용 프로그램 매니페스트의 "groupMembershipClaims" 속성을 통해 응용 프로그램별로 구성됩니다. Null 값은 모든 그룹을 제외하고, "SecurityGroup" 값은 Active Directory 보안 그룹 멤버 자격만 포함하고, "All" 값은 보안 그룹과 Office 365 메일 그룹을 모두 포함합니다. <br><br> **참고**: <br> 암시적 권한 부여가 있는 `groups` 클레임 사용에 대한 자세한 내용은 아래 `hasgroups` 클레임을 참조하세요. <br> 다른 흐름에서 그룹 수가 한도(SAML은 150, JWT는 200)를 넘어설 경우 초과 클레임은 해당 사용자에 대한 그룹 목록을 포함하는 Graph 엔드포인트를 가리키는 클레임 원본에 추가됩니다. ( <br><br> **SAML 값 예제**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **JWT 값 예제**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | JWT 암시적 흐름 그룹 초과 지표| 있는 경우 항상 `true`로, 사용자 하나 이상의 그룹에 있음을 나타냅니다. 전체 그룹 클레임이 URI 조각을 URL 길이 한도(현재 6개 이상 그룹)를 초과하여 확장할 경우 암시적 권한 부여 흐름에서 JWT에 대해 `groups` 클레임 대신 사용됩니다. 클라이언트가 Graph를 사용하여 사용자 그룹(`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`)을 결정해야 함을 표시합니다. |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | 그룹 초과 지표 | 길이는 제한되지 않으나(위의 `hasgroups` 참조) 토큰에게는 너무 큰 토큰 요청의 경우 사용자의 전체 그룹 목록에 대한 링크가 포함됩니다. 분산된 클레임으로서의 JWT인 경우 SAML이 `groups` 클레임 대신에 새 클레임이 됩니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |ID 공급자 |토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 사용자 계정이 발급자가 아닌 다른 테넌트에 있는 경우를 제외하고 발급자 클레임의 값과 동일합니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |토큰이 발급된 시간을 저장합니다. 토큰 만료 전 시간을 측정하는 데 주로 사용됩니다. <br><br> **SAML 값 예제**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **JWT 값 예제**: <br> `"iat": 1390234181` |
| `iss` |발급자 |토큰을 생성하고 반환하는 STS(보안 토큰 서비스)를 식별합니다. Azure AD가 반환하는 토큰에서 발급자는 sts.windows.net입니다. 발급자 클레임 값의 GUID는 Azure AD 디렉터리의 테넌트 ID입니다. 테넌트 ID는 디렉터리의 변경 불가능하고 안정적인 식별자입니다. <br><br> **SAML 값 예제**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **JWT 값 예제**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |성 |Azure AD 사용자 개체에 정의된 사용자의 성을 제공합니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"family_name": "Miller"` |
| `unique_name` |Name |토큰의 주체를 식별하는, 사람이 인식할 수 있는 값을 제공합니다. 이 값은 테넌트 내에서 반드시 고유한 것은 아니며 표시 용도로만 사용하도록 디자인되었습니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |개체 ID |Azure AD 개체의 고유 식별자를 포함합니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. Azure AD에 대한 쿼리의 개체를 식별할 개체 ID를 사용하세요. <br><br> **SAML 값 예제**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |역할 |주체가 그룹 멤버 자격을 통해 직간접적으로 부여받은 모든 응용 프로그램 역할을 나타내며 역할 기반 액세스 제어를 적용하는 데 사용됩니다. 응용 프로그램 역할은 응용 프로그램 매니페스트의 `appRoles` 속성을 통해 응용 프로그램별로 정의됩니다. 각 응용 프로그램 역할의 `value` 속성은 역할 클레임에 표시되는 값입니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **JWT 값 예제**: <br> `“roles”: ["Admin", … ]` |
| `scp` |범위 |클라이언트 응용 프로그램에 부여된 권한을 가장함을 나타냅니다. 기본 권한은 `user_impersonation`입니다. 보안 리소스의 소유자는 Azure AD에서 추가 값을 등록할 수 있습니다. <br><br> **JWT 값 예제**: <br> `"scp": "user_impersonation"` |
| `sub` |제목 |응용 프로그램 사용자 등 토큰에서 정보를 어설션하는 보안 주체를 나타냅니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 따라서 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다. <br> `SubjectConfirmation` 클레임이 아닙니다. SubjectConfirmation은 토큰의 주체를 확인하는 방법을 설명합니다. `Bearer` 주체가 소유한 토큰을 통해 주체를 확인한다는 뜻입니다. <br><br> **SAML 값 예제**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **JWT 값 예제**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |테넌트 ID |토큰을 발급한 디렉터리 테넌트를 식별하는 변경할 수 없고 다시 사용할 수 없는 식별자입니다. 이 값을 사용하여 다중 테넌트 응용 프로그램의 테넌트별 디렉터리 리소스에 액세스할 수 있습니다. 예를 들어 이 값을 사용하여 Graph API 호출의 테넌트를 식별할 수 있습니다. <br><br> **SAML 값 예제**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **JWT 값 예제**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |토큰 수명 |토큰이 유효한 시간 간격을 정의합니다. 토큰의 유효성을 검사하는 서비스에서는 현재 날짜가 토큰 수명 내에 있는지 확인하고 수명 내에 없으면 토큰을 거부합니다. Azure AD와 서비스 간의 시계 시간 차이("시간차")를 고려하여 서비스에서 토큰 수명 범위를 벗어나 최대 5분까지 여유 시간을 허용 할 수 있습니다. <br><br> **SAML 값 예제**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **JWT 값 예제**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |사용자 계정 이름 |사용자 계정의 사용자 이름을 저장합니다.<br><br> **JWT 값 예제**: <br> `"upn": frankm@contoso.com` |
| `ver` |버전 |토큰의 버전 번호를 저장합니다. <br><br> **JWT 값 예제**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>액세스 토큰
인증 성공 시 Azure AD는 보호된 리소스에 액세스하는 데 사용할 수 있는 액세스 토큰을 반환합니다. 액세스 토큰은 base 64 인코딩 JWT(JSON Web Token)이며 디코더를 통해 실행하여 해당 콘텐츠를 검사할 수 있습니다.

앱이 액세스 토큰만 *사용하여* API에 액세스하는 경우, 액세스 토큰은 앱이 HTTP 요청의 리소스에 전달할 수 있는 문자열에 불과하므로 완전히 불투명한 것으로 간주할 수 있으며 이와 같이 간주해야 합니다.

액세스 토큰을 요청하는 경우 Azure AD는 사용할 수 있도록 액세스 토큰에 대한 일부 메타데이터도 반환합니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효한 범위가 포함됩니다. 따라서 앱이 액세스 토큰 자체를 구문 분석하지 않아도 액세스 토큰의 지능형 캐싱을 수행할 수 있습니다.

앱이 HTTP 요청에서 액세스 토큰을 필요로 하는 Azure AD로 보호된 API이면 수신하는 토큰의 유효성을 검사하고 조사해야 합니다. 리소스를 액세스하는 데 토큰을 사용하기 전에 앱에서 액세스 토큰의 유효성 검사를 수행해야 합니다. 유효성 검사에 대한 자세한 내용은 [토큰 유효성 검사](#validating-tokens)를 참조하세요. .NET에서 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure AD에서 전달자 토큰을 사용하여 Web API 보호](active-directory-devquickstarts-webapi-dotnet.md)를 참조하세요.

## <a name="refresh-tokens"></a>새로 고침 토큰

새로 고침 토큰은 앱이 OAuth 2.0 흐름에서 새 액세스 토큰을 획득하는 데 사용할 수 있는 보안 토큰입니다. 새로 고침 토큰을 통해 앱은 사용자 조작을 요구하지 않고 사용자 대신 리소스에 장기적으로 액세스할 수 있습니다.

새로 고침 토큰은 다중 리소스입니다. 즉, 한 리소스에 대한 토큰 요청 중에 받은 새로 고침 토큰을 완전히 다른 리소스에 대한 액세스 토큰으로 교환할 수 있습니다. 이를 수행하려면 요청의 `resource` 매개 변수를 대상 리소스로 설정합니다.

새로 고침 토큰은 앱에 완전히 불투명합니다. 또한 새로 고침 토큰은 수명이 길지만 일정 기간 지속될 것으로 예상하도록 앱을 작성해서는 안 됩니다. 다양한 이유로 언제든지 새로 고침 토큰이 무효화될 수 있기 때문입니다. 이러한 이유는 [토큰 해지](#token-revocation)를 참조하세요. 앱에서 새로 고침 토큰이 유효한지 확인하는 유일한 방법은 Azure AD 토큰 끝점에 대한 토큰 요청을 수행하여 교환하는 것입니다.

새로 고침 토큰을 새 액세스 토큰으로 교환할 때 앱에 토큰 응답에 새로운 새로 고침 토큰을 받게 됩니다. 새로 발급된 새로 고침 토큰을 저장하고 요청에 사용한 토큰을 대체해야 합니다. 이렇게 하면 새로 고침 토큰이 최대한 오랫동안 유효한 상태로 유지됩니다.

## <a name="validating-tokens"></a>토큰 유효성 검사

id_token 또는 access_token의 유효성을 검사하려면 앱이 토큰의 서명과 클레임의 유효성을 모두 검사해야 합니다. 액세스 토큰의 유효성을 검사하려면 앱에서 발급자, 대상 그룹 및 서명 토큰의 유효성을 검사해야 합니다. OpenID 검색 문서에 있는 값에 대해 유효성 검사를 수행해야 합니다. 예를 들어 문서의 테넌트 독립적 버전은 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)에 위치합니다. Azure AD 미들웨어에는 액세스 토큰의 유효성을 검사하는 기본 제공 기능이 있으며 [샘플](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples)을 탐색하여 원하는 언어로 기능을 찾을 수 있습니다. JWT 토큰의 유효성 검사를 명시적으로 수행하는 방법은 [수동 JWT 유효성 검사 샘플](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)을 참조하세요. 

토큰 유효성 검사를 쉽게 처리하는 방법을 보여 주는 라이브러리 및 코드 샘플이 제공됩니다. 아래 정보는 단순히 기본 프로세스를 이해하려는 사용자를 위한 것입니다. JWT 유효성 검사에 사용할 수 있는 여러 타사 오픈 소스 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대해 옵션이 하나 이상 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대한 자세한 내용은 [Azure AD 인증 라이브러리](active-directory-authentication-libraries.md)를 참조하세요.

#### <a name="validating-the-signature"></a>서명 유효성 검사

JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 **헤더**, 두 번째 세그먼트는 **본문**, 세 번째 세그먼트는 **서명**이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

Azure AD에서 발급된 토큰은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. JWT의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg` 클레임은 토큰 서명에 사용된 알고리즘을 나타내고 `x5t` 클레임은 토큰 서명에 사용된 특정 공개 키를 나타냅니다.

특정 시점에 Azure AD는 공개-개인 키 쌍의 특정 집합 중 하나를 사용하여 id_token에 서명할 수 있습니다. Azure AD는 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. Azure AD에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 24시간마다입니다.

다음 위치에 있는 OpenID Connect 메타데이터 문서를 사용하여 서명 유효성 검사에 필요한 서명 키 데이터를 얻을 수 있습니다.

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> 브라우저에서 이 URL을 사용해 보세요!
> 
> 

이 메타데이터 문서는 OpenID Connect 인증을 수행하는 데 필요한 다양한 끝점의 위치 등 여러 유용한 정보를 포함하는 JSON 개체입니다. 

토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 `jwks_uri`도 포함합니다. `jwks_uri` 에 있는 JSON 문서는 해당 특정 시점에 사용 중인 공개 키 정보를 모두 포함합니다. 앱은 JWT 헤더에 `kid` 클레임을 사용하여 토큰 서명에 사용된 공개 키를 이 문서에서 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행할 수 있습니다.

서명 유효성 검사는 이 문서의 범위를 벗어납니다. 필요한 경우 이 작업에 도움이 되는 다양한 오픈 소스 라이브러리가 있습니다.

#### <a name="validating-the-claims"></a>클레임 유효성 검사

앱이 토큰(사용자 로그인 시 id_token 또는 HTTP 요청의 전달자 토큰으로 사용될 경우 액세스 토큰)을 받으면 토큰의 클레임에 대해서도 몇 가지 검사를 수행해야 합니다. 포함하지만 다음과 같이 제한되지 않습니다.

* **대상** 클레임 - id_token이 앱에 제공하려고 의도했던 것이 맞는지 확인합니다.
* **이전이 아님** 및 **만료 시간** 클레임 - id_token이 만료되지 않았는지 확인합니다.
* **발급자** 클레임 - Azure AD에서 실제로 앱에 토큰을 발급했는지 확인합니다.
* **Nonce** - 토큰 재생 공격을 완화합니다.
* 공유할 수 있습니다.

앱이 ID 토큰에 대해 수행해야 하는 클레임 유효성 검사의 전체 목록은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)을 참조하세요. 이러한 클레임의 예상 값에 대한 자세한 내용은 위의 [id_token](#id-tokens) 섹션에 포함되어 있습니다.

## <a name="token-revocation"></a>토큰 해지

다양한 이유로 언제든지 새로 고침 토큰이 무효화되거나 취소될 수 있습니다. 이러한 이유는 시간 제한 및 해지 등 두 가지 주요 범주로 나뉩니다. 
* 토큰 제한 시간
  * MaxInactiveTime: MaxInactiveTime에 의해 결정된 시간 내에 새로 고침 토큰을 사용하지 않은 경우 새로 고침 토큰은 더 이상 유효하지 않습니다. 
  * MaxSessionAge: MaxAgeSessionMultiFactor 또는 MaxAgeSessionSingleFactor가 (해지될 때까지) 기본값 이외의 값으로 설정된 경우 MaxAgeSession *에 설정된 시간이 경과된 후에 다시 인증해야 합니다. 
  * 예제:
    * 테넌트에 5일의 MaxInactiveTime이 설정되고 사용자가 1주일 동안 휴가를 갔습니다. 따라서 AAD에는 7일 동안 사용자의 새 토큰 요청이 없습니다. 다음에 사용자가 새 토큰을 요청하는 경우 해당 새로 고침 토큰이 해지되었음을 발견하고 자격 증명을 다시 입력해야 합니다. 
    * 중요한 응용 프로그램에는 1일의 MaxAgeSessionSingleFactor가 지정됩니다. 월요일 및 화요일(25시간이 경과된 후에)에 사용자가 로그인하는 경우 다시 인증해야 합니다. 
* 해지
  * 자발적 암호 변경: 사용자가 암호를 변경하는 경우 토큰을 획득한 방식에 따라 해당 응용 프로그램 중 일부를 다시 인증해야 할 수 있습니다. 예외는 아래 메모를 참조하세요. 
  * 비자발적 암호 변경: 관리자가 사용자의 암호를 변경하거나 다시 설정하도록 강제하는 경우 해당 암호를 사용하여 수행하면 사용자의 토큰은 무효화됩니다. 예외는 아래 메모를 참조하세요. 
  * 보안 위반: 보안 위반(예: 암호의 온-프레미스 저장소 위반)이 발생할 때 관리자는 현재 발급된 모든 새로 고침 토큰을 해지할 수 있습니다. 그러면 모든 사용자를 다시 인증하도록 강제합니다. 

> [!NOTE]
>인증에 비암호 메서드를 사용(Windows Hello, Authenticator 앱, 얼굴 또는 지문과 같은 생체 인식)하여 토큰을 만드는 경우 사용자의 암호를 변경하면 사용자를 다시 인증하도록 강제하지 않습니다. (하지만 해당 Authenticator 앱을 다시 인증하도록 강제합니다.) 선택한 인증 입력(예: 얼굴)이 변경되지 않았기 때문입니다. 따라서 다시 인증하는 데 사용할 수 있습니다.
>
> 기밀 클라이언트는 암호 변경 해지의 영향을 받지 않습니다. 암호를 변경하기 전에 발급된 새로 고침 토큰이 있는 기밀 클라이언트에서 새로 고침 토큰을 계속 사용하여 더 많은 토큰을 얻을 수 있습니다. 

## <a name="sample-tokens"></a>샘플 토큰

이 섹션에서는 Azure AD가 반환하는 SAML 및 JWT 토큰 샘플을 보여 줍니다. 이러한 샘플을 통해 컨텍스트 내에서 클레임을 볼 수 있습니다.

### <a name="saml-token"></a>SAML 토큰

다음은 일반적인 SAML 토큰 샘플입니다.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT 토큰 - 사용자 가장
다음은 권한 부여 코드 부여 흐름에 사용되는 일반적인 JWT(JSON 웹 토큰) 샘플입니다.
클레임 외에도 토큰에는 클라이언트가 인증된 방법을 나타내는 인증 컨텍스트 클래스 참조 **ver** 및 **appidacr**에 버전 번호가 포함됩니다. 공용 클라이언트의 경우 값이 0입니다. 클라이언트 ID 또는 클라이언트 암호가 사용되면 값이 1입니다.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>관련 콘텐츠
* Azure AD Graph API 통해 토큰 수명 정책을 관리하는 방법에 대한 자세한 내용은 Azure AD 그래프 [정책 작업](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) 및 [정책 엔터티](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)를 참조하세요.
* 자세한 내용과 예제를 포함하여, PowerShell cmdlet를 통한 정책 관리 방법에 대한 샘플은 [Azure AD에서 구성 가능한 토큰 수명](../active-directory-configurable-token-lifetimes.md)을 참조하십시오. 
* [사용자 지정 및 선택적 클레임](active-directory-optional-claims.md)을 응용 프로그램의 토큰에 추가합니다. 
