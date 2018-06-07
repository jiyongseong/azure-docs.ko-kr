---
title: OpenID Connect를 사용하는 웹 로그인 - Azure AD B2C | Microsoft Docs
description: OpenID Connect 인증 프로토콜의 Azure Active Directory 구현을 사용하여 웹 응용 프로그램을 빌드합니다.
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
ms.openlocfilehash: e787ea36ab5099705f151504385dd5dc97029e37
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: OpenID Connect로 웹 로그인
OpenID Connect는 웹 응용 프로그램에 사용자를 안전하게 로그인하는 데 사용할 수 있도록 OAuth 2.0을 기반으로 하여 빌드된 인증 프로토콜입니다. OpenID Connect의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하여 웹 응용 프로그램의 등록, 로그인 및 기타 ID 관리 환경을 Azure AD( Azure Active Directory)로 아웃소싱할 수 있습니다. 이 가이드에서는 언어에 관계 없이 이 작업을 수행하는 방법을 보여 줍니다. 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장합니다. OAuth를 통해 Single Sign-On을 수행할 수 있게 합니다. 클라이언트에서 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있게 하는 보안 토큰인 *ID 토큰*의 개념을 소개합니다.

또한 OAuth 2.0을 확장하므로 앱에서 *액세스 토큰*을 안전하게 얻을 수 있습니다. access_token을 사용하여 [권한 부여 서버](active-directory-b2c-reference-protocols.md#the-basics)로 보안이 유지되는 리소스에 액세스할 수 있습니다. OpenID Connect는 서버에서 호스트되고 브라우저를 통해 액세스되는 웹 응용 프로그램을 빌드하는 경우에 권장됩니다. Azure AD B2C를 사용하여 ID 관리를 모바일 또는 데스크톱 응용 프로그램에 추가하려는 경우 OpenID Connect 대신 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 을 사용해야 합니다.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OpenID Connect 프로토콜을 확장합니다. OpenID Connect를 사용하여 앱에 등록, 로그인 및 프로필 관리와 같은 사용자 환경을 추가할 수 있는 [정책 매개 변수](active-directory-b2c-reference-policies.md)를 소개합니다. 여기서는 OpenID Connect 및 정책을 사용하여 웹 응용 프로그램에서 이러한 환경을 각각 구현하는 방법 및 웹 API에 액세스하기 위한 액세스 토큰을 얻는 방법도 보여 줍니다.

다음 섹션의 예제 HTTP 요청은 샘플 B2C 디렉터리 fabrikamb2c.onmicrosoft.com뿐만 아니라 샘플 응용 프로그램 https://aadb2cplayground.azurewebsites.net 및 정책도 사용합니다. 이러한 값을 사용하여 직접 요청을 시도하거나 고유의 작업으로 바꿀 수 있습니다.
[사용자 고유의 B2C 테넌트, 응용 프로그램 및 정책을 가져오는](#use-your-own-b2c-directory)방법을 알아봅니다.

## <a name="send-authentication-requests"></a>인증 요청 보내기
웹앱이 사용자를 인증하고 정책을 실행해야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 이는 정책에 따라 사용자가 실제로 작업을 수행하는 흐름의 대화형 부분입니다.

이 요청에서 클라이언트는 `p` 매개 변수를 실행하기 위해 `scope` 매개 변수 및 정책에서 사용자로부터 가져와야 할 사용 권한을 나타냅니다. 각각 서로 다른 정책을 사용하는 세 가지 예제가 다음 섹션에서 제공됩니다(쉽게 읽을 수 있도록 줄 바꿈 적용). 각 요청의 작동 방식에 대해 이해하려면 요청을 브라우저에 붙여 넣고 실행합니다.

#### <a name="use-a-sign-in-policy"></a>로그인 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>등록 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>편집 프로필 정책 사용
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| client_id |필수 |[Azure 포털](https://portal.azure.com/) 이 앱에 할당된 응용 프로그램 ID입니다. |
| response_type |필수 |OpenID Connect의 ID 토큰을 포함해야 하는 응답 형식입니다. 웹 API를 호출하기 위한 토큰이 웹앱에 필요한 경우 여기서 수행한 대로 `code+id_token`을 사용할 수 있습니다. |
| redirect_uri |권장 |앱에서 인증 응답을 보내고 받을 수 있는 앱의 `redirect_uri` 매개 변수입니다. URL로 인코딩되어야 한다는 점을 제외하고는 포털에서 등록한 `redirect_uri` 매개 변수 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청되는 두 가지 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다(자세한 내용은 이 문서의 뒷부분 참조). `offline_access` 범위는 웹앱에 대한 선택 사항입니다. 리소스에 장기간 액세스하기 위한 *새로 고침 토큰*이 앱에 필요함을 나타냅니다. |
| response_mode |권장 |결과 권한 부여 코드를 앱에 다시 보내는 데 사용해야 하는 메서드입니다. `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다.  최상의 보안을 위해 `form_post` 응답 모드를 사용하는 것이 좋습니다. |
| state |권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코드하는 데에도 사용됩니다. |
| nonce |필수 |앱에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| p |필수 |실행할 정책입니다. B2C 테넌트에 생성된 정책의 이름입니다. 정책 이름 값은 `b2c\_1\_`로 시작해야 합니다. 정책에 대한 자세한 내용은 [확장할 수 있는 정책 프레임워크](active-directory-b2c-reference-policies.md)를 참조하세요. |
| prompt |옵션 |필요한 사용자 상호 작용 유형입니다. 현재 유효한 값은 `login`뿐이며, 이는 사용자가 해당 요청에 대한 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 정책의 워크플로를 완료하도록 사용자에게 요청합니다. 이 경우 정책을 정의한 방법에 따라 사용자 이름과 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리를 등록하거나, 다른 단계를 수행할 수도 있습니다.

사용자가 정책을 완료하면 Azure AD에서 `response_mode` 매개 변수에 지정된 메서드를 사용하여 표시된 `redirect_uri` 매개 변수에서 앱에 대한 응답을 반환합니다. 응답은 실행되는 정책과 관계 없이 앞의 경우 각각에 대해 동일합니다.

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --- | --- |
| id_token |앱이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에 포함되어 있습니다. |
| 코드 |`response_type=code+id_token`을 사용한 경우 앱에서 요청한 권한 부여 코드입니다. 앱에서는 권한 부여 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 인증 코드는 수명이 매우 짧습니다. 일반적으로 약 10분 후에 만료됩니다. |
| state |요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

앱에서 적절하게 처리할 수 있도록 `redirect_uri` 매개 변수에 오류 응답을 보낼 수도 있습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state |전체 설명은 이 섹션의 첫 번째 표를 참조하세요. 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사
ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. ID 토큰의 서명에 대한 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 고유한 유효성 검사 논리를 구현하는 것보다 이러한 옵션을 탐색하는 것이 좋습니다. 여기의 정보는 올바르게 해당 라이브러리를 사용하는 방법을 파악하는 데 도움이 됩니다.

Azure AD B2C에는 앱이 런타임에 Azure AD B2C에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 끝점이 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 정책에 대한 메타데이터 문서는 다음 위치에 있습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

이 구성 문서의 속성 중 하나가 `jwks_uri`이며, 동일한 정책에 대한 값은 다음과 같습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

ID 토큰에 서명하는 데 사용된 정책(및 메타데이터를 가져올 위치)을 결정하려면 두 가지 옵션이 있습니다. 먼저 정책 이름이 ID 토큰의 `acr` 클레임에 포함됩니다. ID 토큰에서 클레임을 구문 분석하는 방법에 대한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 정책을 인코딩한 다음 이를 디코딩하여 어떤 정책을 사용할지 결정하는 것입니다. 두 방법 중 하나는 유효합니다.

OpenID Connect 메타데이터 끝점에서 메타데이터 문서를 가져오면 이 끝점에 있는 RSA 256 공개 키를 사용하여 ID 토큰의 서명에 대한 유효성을 검사할 수 있습니다. 지정된 시점에 이 끝점에 나열된 키가 여러 개 있을 수 있으며, 각 키는 `kid` 클레임으로 식별됩니다. ID 토큰의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다. 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)(특히 [토큰 유효성 검사](active-directory-b2c-reference-tokens.md#token-validation) 섹션)를 참조하세요.
<!--TODO: Improve the information on this-->

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예:

* `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지해야 합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
* `aud` 클레임의 유효성을 검사하여 앱에 대한 ID 토큰이 발급되었는지 확인해야 합니다. 해당 값이 앱의 응용 프로그램  ID여야 합니다.
* `iat` 및 `exp` 클레임의 유효성을 검사하여 ID 토큰이 만료되지 않았는지 확인해야 합니다.

또한 수행해야 하는 몇 가지 추가 유효성 검사가 있습니다. 이러한 추가 유효성 검사는 [OpenID Connect 핵심 사양](http://openid.net/specs/openid-connect-core-1_0.html)(영문)에서 자세히 설명하고 있습니다.  시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록했는지 확인
* 사용자에게 적절한 권한 부여/권한이 있는지 확인
* Azure Multi-Factor Authentication과 같은 특정 강도의 인증이 발생했는지 확인

ID 토큰의 클레임에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.

ID 토큰의 유효성을 검사한 후에는 사용자가 포함된 세션을 시작할 수 있습니다. 앱에서 ID 토큰의 클레임을 사용하여 사용자 관련 정보를 가져올 수 있으며, 이 정보의 용도로 표시, 레코드 및 권한 부여가 있습니다.

## <a name="get-a-token"></a>토큰 가져오기
웹앱에서 정책만 실행해야 하는 경우 다음 몇 가지 섹션을 건너뛸 수 있습니다. 이러한 섹션은 Web API에 대해 인증된 호출을 수행해야 하며 Azure AD B2C로도 보호되는 웹앱에만 적용할 수 있습니다.

`response_type=code+id_token`을 사용하여 `POST` 요청을 `/token` 끝점으로 보내 원하는 리소스에 대한 토큰에 대해 얻은 권한 부여 코드를 사용할 수 있습니다. 현재 토큰을 요청할 수 있는 리소스는 앱 자체의 백 엔드 웹 API뿐입니다. 자신에게 토큰을 요청하는 데 사용된 규칙은 앱의 클라이언트 ID를 범위로 사용하는 것입니다.

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |권한 부여 코드를 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 `POST` 본문이 아니라 쿼리 문자열에 추가합니다. |
| client_id |필수 |[Azure 포털](https://portal.azure.com/) 이 앱에 할당된 응용 프로그램 ID입니다. |
| grant_type |필수 |권한 유형입니다. 인증 코드 흐름의 경우 `authorization_code`이어야 합니다. |
| scope |권장 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청되는 두 가지 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자에게 로그인하고 id_token 매개 변수의 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 클라이언트와 동일한 응용 프로그램 ID로 표시되는 앱 자체의 백 엔드 웹 API에 토큰을 가져오는 데 사용할 수 있습니다. `offline_access` 범위는 리소스에 장기간 액세스하기 위한 새로 고침 토큰이 앱에 필요함을 나타냅니다. |
| 코드 |필수 |흐름의 첫 번째 단계에서 얻은 권한 부여 코드입니다. |
| redirect_uri |필수 |권한 부여 코드를 받은 응용 프로그램의 `redirect_uri` 매개 변수입니다. |
| client_secret |필수 |[Azure 포털](https://portal.azure.com/)에서 생성한 응용 프로그램 암호입니다. 이 응용 프로그램 암호는 중요한 보안 아티팩트입니다. 서버에 안전하게 저장해야 합니다. 또한 이 클라이언트 비밀도 정기적으로 회전해야 합니다. |

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
| token_type |토큰 형식 값입니다. Azure AD는 `Bearer` 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT 토큰입니다. |
| scope |토큰이 유효한 범위입니다. 나중에 사용할 수 있도록 토큰 캐싱에 사용할 수 있습니다. |
| expires_in |액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 새로 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 `offline_access` 범위를 사용해야 합니다. |

오류 응답은 다음과 같습니다.

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="use-the-token"></a>토큰 사용
액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 웹 API에 대한 요청에서 토큰을 사용할 수 있습니다.

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>토큰 새로 고침
ID 토큰은 수명이 짧습니다. 리소스에 계속 액세스하려면 만료된 후 새로 고쳐야 합니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 끝점에 제출하면 됩니다. 이번에는 `code` 매개 변수 대신 `refresh_token` 매개 변수를 제공합니다.

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 매개 변수 | 필수 | 설명 |
| --- | --- | --- |
| p |필수 |원래의 새로 고침 토큰을 얻는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 쿼리 문자열에 추가합니다. |
| client_id |필수 |[Azure 포털](https://portal.azure.com/) 이 앱에 할당된 응용 프로그램 ID입니다. |
| grant_type |필수 |권한 부여 코드 흐름의 이 단계에 대한 새로 고침 토큰이어야 하는 권한 부여 유형입니다. |
| scope |권장 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청되는 두 가지 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 클라이언트와 동일한 응용 프로그램 ID로 표시되는 앱 자체의 백 엔드 웹 API에 토큰을 가져오는 데 사용할 수 있습니다. `offline_access` 범위는 리소스에 장기간 액세스하기 위한 새로 고침 토큰이 앱에 필요함을 나타냅니다. |
| redirect_uri |권장 |권한 부여 코드를 받은 응용 프로그램의 `redirect_uri` 매개 변수입니다. |
| refresh_token |필수 |흐름의 두 번째 단계에서 얻은 원래의 새로 고침 토큰입니다. 새로 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 `offline_access` 범위를 사용해야 합니다. |
| client_secret |필수 |[Azure 포털](https://portal.azure.com/)에서 생성한 응용 프로그램 암호입니다. 이 응용 프로그램 암호는 중요한 보안 아티팩트입니다. 서버에 안전하게 저장해야 합니다. 또한 이 클라이언트 비밀도 정기적으로 회전해야 합니다. |

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
| token_type |토큰 형식 값입니다. Azure AD는 `Bearer` 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT 토큰입니다. |
| scope |토큰이 유효한 범위이며, 나중에 사용할 수 있도록 토큰 캐싱에 사용할 수 있습니다. |
| expires_in |액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다.  새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기
앱에서 사용자를 로그아웃시키려는 경우 앱의 쿠키를 삭제하거나 그렇지 않은 경우 사용자로 세션을 지우는 것은 충분하지 않습니다. 또한 로그아웃할 Azure AD로 사용자를 리디렉션해야 합니다. 그러지 않으면 사용자가 자격 증명을 다시 입력하지 않고 앱을 다시 인증할 수 있습니다. 이는 Azure AD를 사용하는 유효한 Single Sign-On 세션이 있기 때문입니다.

"ID 토큰 유효성 검사" 섹션의 앞부분에서 설명한 OpenID Connect 메타데이터 문서에 나열된 `end_session` 끝점으로 사용자를 리디렉션할 수 있습니다.

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 매개 변수 | Required? | 설명 |
| --- | --- | --- |
| p |필수 |응용 프로그램에서 사용자를 로그아웃하는 데 사용하려는 정책입니다. |
| post_logout_redirect_uri |권장 |성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 포함되지 않은 경우 Azure AD B2C에서 사용자에게 일반 메시지를 표시합니다. |

> [!NOTE]
> 사용자를 `end_session` 끝점에 연결하면 Azure AD B2C를 사용하여 일부 사용자의 Single Sign-On 상태가 해제되지만, 사용자의 소셜 IDP(ID 공급자) 세션에서 해당 사용자를 로그아웃하지는 않습니다. 사용자가 다음 로그인하는 동안 같은 IDP를 선택하는 경우 자격 증명을 입력하지 않아도 다시 인증됩니다. 사용자가 B2C 응용 프로그램에서 로그아웃하려는 경우 반드시 Facebook 계정을 로그아웃하려는 것은 아닙니다. 그러나 로컬 계정의 경우 사용자의 세션이 올바르게 종료됩니다.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>사용자 고유의 B2C 테넌트 사용
이러한 요청을 스스로 시도하려면 먼저 다음 세 단계를 수행한 다음 앞에서 설명한 예제 값을 자신의 고유한 값으로 바꾸어야 합니다.

1. [B2C 테넌트를 만들고](active-directory-b2c-get-started.md), 요청에서 테넌트의 이름을 사용합니다.
2. 응용 프로그램 ID를 얻기 위해 [응용 프로그램을 만듭니다](active-directory-b2c-app-registration.md). 앱에 웹앱/웹 API를 포함합니다. 선택적으로 응용 프로그램 비밀을 만듭니다.
3. [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

