---
title: 'Azure Active Directory B2C: 암시적 흐름을 사용하는 단일 페이지 앱 | Microsoft Docs'
description: Azure Active Directory B2C에서 OAuth 2.0 암시적 흐름을 사용하여 단일 페이지 앱을 직접 빌드하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 3347eac16e447091ffcaaf403e1291e2c7175a2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: OAuth 2.0 암시적 흐름을 사용하여 단일 페이지 앱 로그인

대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 앱은 종종 AngularJS, Ember.js 또는 Durandal과 같은 프레임워크를 사용하여 작성됩니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증에 대한 몇 가지 추가 과제가 있습니다.

* 이러한 앱의 보안 특성은 기존의 서버 기반 웹 응용 프로그램과 상당히 다릅니다.
* 많은 권한 부여 서버 및 ID 공급자에서 CORS(원본 간 리소스 공유) 요청을 지원하지 않습니다.
* 앱에서 전체 페이지 브라우저를 리디렉션하면 사용자 환경에 상당히 침투할 수 있습니다.

이러한 응용 프로그램을 지원하기 위해 Azure AD B2C(Azure Active Directory B2C)에서는 OAuth 2.0 암시적 흐름을 사용합니다. OAuth 2.0 암시적 권한 부여 흐름은 [OAuth 2.0 사양의 4.2 섹션](http://tools.ietf.org/html/rfc6749)(영문)에서 설명하고 있습니다. 암시적 흐름에서 앱은 서버 간 교환 없이 Azure AD(Azure Active Directory) 권한 부여 끝점에서 직접 토큰을 받습니다. 모든 인증 논리 및 세션 처리는 추가 페이지 리디렉션 없이 JavaScript 클라이언트에서 전적으로 수행됩니다.

Azure AD B2C는 표준 OAuth 2.0 암시적 흐름을 확장하여 단순한 인증 및 권한 부여보다 더 많은 작업을 수행합니다. Azure AD B2C는 [정책 매개 변수](active-directory-b2c-reference-policies.md)를 도입했습니다. 정책 매개 변수를 사용하면 OAuth 2.0을 통해 등록, 로그인 및 프로필 관리와 같은 사용자 환경을 앱에 추가할 수 있습니다. 여기서는 암시적 흐름과 Azure AD를 사용하여 단일 페이지 응용 프로그램에서 이러한 환경 각각을 구현하는 방법을 보여 줍니다. 시작하는 데 도움이 되도록 [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) 및 [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) 샘플을 살펴보세요.

이 문서의 예제 HTTP 요청에서는 샘플 Azure AD B2C 디렉터리인 **fabrikamb2c.onmicrosoft.com**을 사용합니다. 또한 고유한 샘플 응용 프로그램 및 정책도 사용합니다. 이러한 값을 사용하여 요청을 직접 시도하거나 사용자 고유의 값으로 바꿀 수 있습니다.
[사용자 고유의 Azure AD B2C 디렉터리, 응용 프로그램 및 정책을 가져오는](#use-your-own-b2c-tenant)방법을 알아보세요.


## <a name="protocol-diagram"></a>프로토콜 다이어그램

암시적 로그인 흐름은 다음 그림과 비슷합니다. 각 단계는 이 문서의 뒷부분에서 자세히 설명합니다.

![OpenID Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>인증 요청 보내기
웹앱에서 사용자를 인증하고 정책을 실행해야 하는 경우 사용자를 `/authorize` 끝점으로 지정합니다. 이는 정책에 따라 사용자가 실제로 작업을 수행하는 흐름의 대화형 부분입니다. 사용자는 Azure AD 끝점에서 ID 토큰을 가져옵니다.

이 요청에서 클라이언트는 사용자로부터 얻어야 하는 권한을 `scope` 매개 변수에 표시합니다. `p` 매개 변수에서 실행할 정책을 나타냅니다. 다음 세 가지 예제(쉽게 읽을 수 있도록 줄 바꿈 적용)에서는 각각 다른 정책을 사용합니다. 각 요청의 작동 방식에 대해 이해하려면 요청을 브라우저에 붙여 넣고 실행합니다.

### <a name="use-a-sign-in-policy"></a>로그인 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>등록 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>편집 프로필 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. `token` 응답 형식이 포함될 수도 있습니다. `token`을 사용하면 앱에서 권한 부여 끝점에 대한 두 번째 요청을 수행하지 않고도 권한 부여 끝점에서 액세스 토큰을 즉시 받을 수 있습니다.  `token` 응답 형식을 사용하는 경우 `scope` 매개 변수에는 토큰을 발급할 리소스를 나타내는 범위가 포함되어야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩해야 한다는 점을 제외하고는 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| response_mode |권장 |결과 토큰을 앱으로 다시 보내는 데 사용할 메서드를 지정합니다.  암시적 흐름의 경우 `fragment`를 사용합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. (이 문서의 뒷부분에서 자세히 알아보겠습니다.) `offline_access` 범위는 웹앱에 대한 선택 사항입니다. 리소스에 장기간 액세스하기 위한 새로 고침 토큰이 앱에 필요함을 나타냅니다. |
| state |권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 사용하려는 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 state(상태)는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코딩하는 데에도 사용됩니다. |
| nonce |필수 |앱에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| p |필수 |실행할 정책입니다. Azure AD B2C 테넌트에서 생성된 정책의 이름입니다. 정책 이름 값은 **b2c\_1\_**로 시작해야 합니다. 자세한 내용은 [Azure AD B2C 기본 제공 정책](active-directory-b2c-reference-policies.md)을 참조하세요. |
| prompt |옵션 |필요한 사용자 상호 작용의 형식입니다. 현재 유효한 값은 `login`뿐이며, 이는 사용자가 해당 요청에 대한 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 정책의 워크플로를 완료하도록 사용자에게 요청합니다. 이 경우 사용자 이름과 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리를 등록하거나, 다른 단계를 수행할 수도 있습니다. 사용자 작업은 정책을 정의한 방식에 따라 다릅니다.

사용자가 정책을 완료하면 Azure AD에서 앱에 대한 응답을 `redirect_uri`에 사용한 값으로 반환합니다. `response_mode` 매개 변수에 지정된 메서드를 사용합니다. 실행된 정책과 관계 없이 사용자 작업 시나리오 각각에 대한 응답은 정확히 동일합니다.

### <a name="successful-response"></a>성공적인 응답
`response_mode=fragment` 및 `response_type=id_token+token`을 사용하는 성공적인 응답은 다음과 같으며, 쉽게 읽을 수 있도록 줄 바꿈이 적용되었습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 매개 변수 | 설명 |
| --- | --- |
| access_token |앱에서 요청한 액세스 토큰입니다.  액세스 토큰은 디코딩하거나 다른 방법으로 검사하지 않아야 하며, 불투명 문자열로 처리할 수 있습니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| expires_in |액세스 토큰이 유효한 시간(초)입니다. |
| scope |토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| id_token |앱이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |
| state |요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

### <a name="error-response"></a>오류 응답
오류 응답은 앱에서 적절히 처리할 수 있도록 리디렉션 URI에 보낼 수도 있습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생한 오류의 유형을 분류하는 데 사용되는 오류 코드 문자열입니다. 오류 코드는 오류를 처리하는 데 사용할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state |잎의 표에 나와 있는 전체 설명을 참조하세요. 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다.|

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사
ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. ID 토큰의 서명에 대한 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

사용하려는 언어에 따라 JWT의 유효성을 검사하는 데 사용할 수 있는 다양한 오픈 소스 라이브러리가 있습니다. 사용자 고유의 유효성 검사 논리를 구현하는 대신 사용 가능한 오픈 소스 라이브러리를 탐색하는 것이 좋습니다. 이 문서의 정보를 통해 이러한 라이브러리를 올바르게 사용하는 방법을 알아볼 수 있습니다.

Azure AD B2C에는 OpenID Connect 메타데이터 끝점이 있습니다. 앱에서 런타임에 Azure AD B2C에 대한 정보를 가져오는 데 끝점을 사용할 수 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. Azure AD B2C 테넌트의 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 fabrikamb2c.onmicrosoft.com 테넌트의 b2c_1_sign_in 정책에 대한 메타데이터 문서는 다음 위치에 있습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

이 구성 문서의 속성 중 하나가 `jwks_uri`입니다. 동일한 정책에 대한 값은 다음과 같습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

ID 토큰에 서명하는 데 사용된 정책(및 메타데이터를 가져올 위치)을 결정하려면 두 가지 옵션이 있습니다. 먼저 `id_token`의 `acr` 클레임에 정책 이름을 포함합니다. ID 토큰에서 클레임을 구문 분석하는 방법에 대한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 다른 옵션은 요청을 발급할 때 정책을 `state` 매개 변수의 값으로 인코딩한 다음, `state` 매개 변수를 디코딩하여 어떤 정책이 사용되었는지 확인하는 것입니다. 두 방법 중 하나는 유효합니다.

OpenID Connect 메타데이터 끝점에서 메타데이터 문서를 가져오면 이 끝점에 있는 RSA-256 공개 키를 사용하여 ID 토큰의 서명에 대한 유효성을 검사할 수 있습니다. 지정된 시간에 이 끝점에 나열된 키가 여러 개 있을 수 있으며, 각 키는 `kid`로 식별됩니다. `id_token`의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다. [토큰 유효성을 검사하는 방법](active-directory-b2c-reference-tokens.md)을 포함하여 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md#token-validation)를 참조하세요.
<!--TODO: Improve the information on this-->

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예: 

* `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
* `aud` 클레임의 유효성을 검사하여 앱에 대한 ID 토큰이 발급되었는지 확인합니다. 해당 값이 앱의 응용 프로그램  ID여야 합니다.
* `iat` 및 `exp` 클레임의 유효성을 검사하여 ID 토큰이 만료되지 않았는지 확인합니다.

수행해야 하는 몇 가지 추가 유효성 검사는 [OpenID Connect 핵심 사양](http://openid.net/specs/openid-connect-core-1_0.html)(영문)에서 자세히 설명하고 있습니다. 시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자 또는 조직이 앱에 등록했는지 확인합니다.
* 사용자에게 적절한 권한 부여 및 권한이 있는지 확인합니다.
* Azure Multi-Factor Authentication을 사용하는 것과 같이 특정 강도의 인증이 발생했는지 확인합니다.

ID 토큰의 클레임에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.

ID 토큰의 유효성을 완전히 검사한 후에는 사용자와 세션을 시작할 수 있습니다. 앱에서 ID 토큰의 클레임을 사용하여 사용자 관련 정보를 가져올 수 있으며, 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## <a name="get-access-tokens"></a>액세스 토큰 가져오기
웹앱에서 정책만 실행해야 하는 경우 다음 몇 가지 섹션을 건너뛸 수 있습니다. 다음 섹션의 정보는 Web API에 대해 인증된 호출을 수행해야 하며 Azure AD B2C로 보호되는 웹앱에만 적용할 수 있습니다.

사용자가 단일 페이지 앱에 로그인되도록 했으므로 Azure AD를 통해 보안이 유지되는 웹 API를 호출하기 위한 액세스 토큰을 가져올 수 있습니다. `token` 응답 형식을 사용하여 토큰을 이미 받았더라도 이 메서드를 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고도 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 웹앱 흐름에서는 `/token` 끝점에 요청하여 이 작업을 수행합니다.  하지만 끝점은 CORS 요청을 지원하지 않으므로 AJAX 호출을 통해 토큰을 가져오고 새로 고치는 것은 옵션이 아닙니다. 대신 숨겨진 HTML iFrame 요소에서 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다. 다음은 쉽게 읽을 수 있도록 줄 바꿈을 적용한 예제입니다.

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다.  `token` 응답 형식이 포함될 수도 있습니다. 여기서 `token`을 사용하면 앱에서 권한 부여 끝점에 대한 두 번째 요청을 수행하지 않고도 권한 부여 끝점에서 액세스 토큰을 즉시 받을 수 있습니다. `token` 응답 형식을 사용하는 경우 `scope` 매개 변수에는 토큰을 발급할 리소스를 나타내는 범위가 포함되어야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩해야 한다는 점을 제외하고는 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다.  토큰을 가져오려면 원하는 리소스에 필요한 모든 범위를 포함합니다. |
| response_mode |권장 |결과 토큰을 앱으로 다시 보내는 데 사용되는 메서드를 지정합니다.  `query`, `form_post` 또는 `fragment`일 수 있습니다. |
| state |권장 |토큰 응답에 반환되는 요청에 포함된 값입니다.  사용하려는 콘텐츠의 문자열일 수 있습니다.  일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다.  또한 state(상태)는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보를 인코딩하는 데에도 사용됩니다. 예를 들어 사용자가 있던 페이지 또는 보기입니다. |
| nonce |필수 |앱에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다.  그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| prompt |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 `prompt=none`을 사용하여 iFrame이 로그인 페이지에 머물지 않고 즉시 반환되는지 확인합니다. |
| login_hint |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 사용자가 지정된 시간에 가질 수 있는 여러 세션을 구분하기 위해 이 힌트에 해당 사용자의 사용자 이름을 포함합니다. `preferred_username` 클레임을 사용하여 이전 로그인에서 사용자 이름을 추출할 수 있습니다. |
| domain_hint |필수 |`consumers` 또는 `organizations`일 수 있습니다.  숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 요청에 `domain_hint` 값을 포함해야 합니다.  이전 로그인의 ID 토큰에서 `tid` 클레임을 추출하여 사용할 값을 결정합니다.  `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용합니다.  그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

`prompt=none` 매개 변수를 설정하면 이 요청이 즉시 성공하거나 실패하고 응용 프로그램으로 돌아갑니다.  성공적인 응답은 `response_mode` 매개 변수에 지정된 메서드를 사용하여 표시된 리디렉션 URI에서 앱으로 보내집니다.

### <a name="successful-response"></a>성공적인 응답
`response_mode=fragment`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 매개 변수 | 설명 |
| --- | --- |
| access_token |앱이 요청한 토큰입니다. |
| token_type |토큰 형식은 항상 Bearer(전달자)입니다. |
| state |요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| scope |access_token이 유효한 범위입니다. |

### <a name="error-response"></a>오류 응답
오류 응답은 앱에서 적절히 처리할 수 있도록 리디렉션 URI에 보낼 수도 있습니다.  `prompt=none`의 경우 예상되는 오류는 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다. 응용 프로그램에 적합한 방식으로 이 작업을 처리할 수 있습니다.

## <a name="refresh-tokens"></a>새로 고침 토큰
ID 토큰 및 액세스 토큰은 모두 짧은 기간 후에 만료됩니다. 이러한 토큰을 정기적으로 새로 고치려면 앱을 준비해야 합니다.  두 가지 형식의 토큰을 새로 고치려면 `prompt=none` 매개 변수를 통해 Azure AD 단계를 제어하여 이전 예제에서 사용한 것과 동일한 숨겨진 iFrame 요청을 수행합니다.  새 `id_token` 값을 받으려면 `response_type=id_token`, `scope=openid` 및 `nonce` 매개 변수를 사용해야 합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기
사용자를 앱에서 로그아웃하도록 하려면 사용자를 Azure AD로 리디렉션하여 로그아웃시킵니다. 이 작업을 수행하지 않으면 사용자가 자격 증명을 다시 입력하지 않고 앱을 다시 인증할 수 있습니다. 이는 Azure AD를 사용하는 유효한 Single Sign-On 세션이 있기 때문입니다.

[ID 토큰 유효성 검사](#validate-the-id-token)에서 설명한 동일한 OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint`로 사용자를 리디렉션할 수 있습니다. 예: 

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |사용자가 응용 프로그램에서 로그아웃하도록 하는 데 사용하는 정책입니다. |
| post_logout_redirect_uri |권장 |성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 포함되어 있지 않으면 Azure AD B2C에서 사용자에게 일반 메시지를 표시합니다. |

> [!NOTE]
> 사용자를 `end_session_endpoint`로 전달하면 Azure AD B2C로 사용자의 Single Sign-On 상태 중 일부를 지웁니다. 그러나 사용자의 소셜 ID 공급자 세션에서 사용자를 서명하지 않습니다. 사용자가 후속 로그인 중에 동일한 ID 공급자를 선택하면 자격 증명을 입력하지 않고도 다시 인증됩니다. 사용자가 Azure AD B2C 응용 프로그램에서 로그아웃하려는 경우 반드시 Facebook과 같은 계정에서 완전히 로그아웃하려는 것은 아닙니다. 그러나 로컬 계정의 경우 사용자의 세션이 제대로 종료됩니다.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>사용자 고유의 Azure AD B2C 테넌트 사용
이러한 요청을 직접 시도하려면 다음 세 단계를 완료합니다. 이 문서에서 사용한 예제 값을 사용자 고유의 값으로 바꿉니다.

1. [Azure AD B2C 테넌트를 만듭니다](active-directory-b2c-get-started.md). 요청에 테넌트의 이름을 사용합니다.
2. 응용 프로그램 ID 및 `redirect_uri` 값을 얻기 위해 [응용 프로그램을 만듭니다](active-directory-b2c-app-registration.md). 앱에 웹앱 또는 Web API를 포함합니다. 필요에 따라 응용 프로그램 비밀을 만들 수 있습니다.
3. [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

## <a name="samples"></a>샘플

* [Node.js를 사용하여 단일 페이지 앱 만들기](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)(영문)
* [.NET을 사용하여 단일 페이지 앱 만들기](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)(영문)

