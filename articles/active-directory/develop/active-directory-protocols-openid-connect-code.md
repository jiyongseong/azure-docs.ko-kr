---
title: Azure AD의 OpenID Connect 인증 코드 흐름 이해 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory 및 OpenID Connect를 사용하여 테넌트에서 웹 응용 프로그램 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
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
ms.openlocfilehash: a5383776aa787a087fffe1ab06bb62c2b1df073d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157335"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>OpenID Connect 및 Azure Active Directory를 사용하여 웹 응용 프로그램에 대한 액세스 권한 부여
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 는 OAuth 2.0 프로토콜을 기반으로 하는 간단한 ID 계층입니다. OAuth 2.0은 보호된 리소스에 액세스하기 위해 **액세스 토큰** 을 가져오고 사용하는 메커니즘을 정의하지만 ID 정보를 제공하는 표준 메서드는 정의하지 않습니다. OpenID Connect는 OAuth 2.0 권한 부여 프로세스에 대한 확장으로 인증을 구현합니다. 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 제공하는 최종 사용자에 대한 정보를 `id_token` 형태로 제공합니다.

OpenID Connect는 서버에서 호스트되고 브라우저를 통해 액세스되는 웹 응용 프로그램을 빌드하는 경우 권장 사항입니다.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>OpenID Connect를 사용하는 인증 흐름
대부분의 기본 로그인 흐름은 다음 단계를 포함합니다. - 각 단계를 아래에서 자세히 설명합니다.

![OpenId Connect 인증 흐름](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect 메타데이터 문서

OpenID Connect는 앱이 로그인을 수행하는 데 필요한 대부분의 정보를 포함하는 메타데이터 문서를 설명합니다. 여기에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. OpenID Connect 메타데이터 문서는 다음 위치에서 찾을 수 있습니다.

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
메타데이터는 간단한 JSON(JavaScript Object Notation) 문서입니다. 예제를 보려면 다음 코드 조각을 참조하세요. 이 조각의 내용은 [OpenID Connect 사양](https://openid.net)에 자세히 설명되어 있습니다.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기
웹 응용 프로그램이 사용자를 인증해야 하는 경우 사용자를 `/authorize` 끝점으로 보내야 합니다. 이 요청은 몇 가지 중요한 차이점이 있지만 [OAuth 2.0 인증 코드 흐름](active-directory-protocols-oauth-code.md)의 첫 번째 레그와 유사합니다.

* 요청이 `openid` 범위를 `scope` 매개 변수에 포함해야 합니다.
* `response_type` 매개 변수는 `id_token`을(를) 포함해야 합니다.
* 요청은 `nonce` 매개 변수를 포함해야 합니다.

따라서 샘플 요청은 다음과 같습니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| tenant |필수 |요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 테넌트 독립 토큰에 대한 테넌트 식별자(예: `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com`, `common`)입니다. |
| client_id |필수 |Azure AD에 등록할 때 앱에 할당된 응용 프로그램 ID입니다. Azure Portal에서 이러한 값을 확인할 수 있습니다. **Azure Active Directory**를 클릭하고 **앱 등록**을 클릭하고 응용 프로그램을 선택하여 응용 프로그램 페이지에서 응용 프로그램 ID를 찾습니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. `code`와 같은 다른 response_types를 포함할 수도 있습니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인" 권한으로 해석되는 `openid`범위가 포함되어야 합니다. 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| nonce |필수 |결과 `id_token`에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열 또는 GUID입니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| response_mode |권장 |결과 authorization_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 지원되는 값은 *HTTP 폼 게시*의 경우 `form_post`이고, *URL 조각*의 경우 `fragment`입니다. 웹 응용 프로그램의 경우 응용 프로그램에 대한 가장 안전한 토큰 전송을 보장하기 위해 `response_mode=form_post`를 사용하는 것이 좋습니다. `response_mode`가 포함되지 않은 경우 기본값은 `fragment`입니다.|
| state |권장 |토큰 응답에 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt |선택 사항 |필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login', 'none', 'consent'뿐입니다. `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 끝점은 오류를 반환합니다. `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| login_hint |선택 사항 |사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다.

### <a name="sample-response"></a>샘플 응답
사용자를 인증한 후 샘플 응답은 다음과 같습니다.

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 매개 변수 | 설명 |
| --- | --- |
| id_token |앱이 요청한 `id_token` 입니다. `id_token` 을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| state |토큰 응답에도 반환되는 요청에 포함된 값입니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |

### <a name="error-response"></a>오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 끝점 오류에 대한 오류 코드
다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드 | 설명 | 클라이언트 작업 |
| --- | --- | --- |
| invalid_request |프로토콜 오류(예: 필수 매개 변수 누락). |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 테스트 중에 발견되는 개발 오류입니다. |
| unauthorized_client |클라이언트 응용 프로그램이 인증 코드를 요청할 수 없습니다. |이 오류는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| access_denied |리소스 소유자가 동의 거부 |클라이언트 응용 프로그램이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| unsupported_response_type |권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 테스트 중에 발견되는 개발 오류입니다. |
| server_error |서버에 예기치 않은 오류가 발생했습니다. |요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 응용 프로그램이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| temporarily_unavailable |서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. |요청을 다시 시도하십시오. 클라이언트 응용 프로그램이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| invalid_resource |대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. |리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |

## <a name="validate-the-idtoken"></a>id_token 유효성 검사
`id_token`을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. 서명의 유효성을 검사하고 앱의 요구 사항에 따라 `id_token`의 클레임을 확인해야 합니다. Azure AD 끝점은 JWT(JSON 웹 토큰) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

클라이언트 코드에서 `id_token`의 유효성을 검사하도록 선택할 수 있지만, 일반적으로 `id_token`을 백 엔드 서버에 보내서 그 곳에서 유효성 검사를 수행합니다. `id_token`의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록했는지 확인
* 사용자에게 적절한 권한 부여/권한이 있는지 확인
* 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

`id_token`의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 `id_token`의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다. 토큰 유형 및 클레임에 대한 자세한 내용은 [지원되는 토큰 및 클레임 유형](active-directory-token-and-claims.md)을 읽어 보세요.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기
앱에서 사용자를 로그아웃시키려는 경우 앱의 쿠키를 삭제하거나 그렇지 않은 경우 사용자로 세션을 지우는 것은 충분하지 않습니다. 또한 로그아웃의 경우 사용자를 `end_session_endpoint`로 리디렉션해야 합니다. 이렇게 하지 않으면 사용자는 Azure AD 끝점을 사용하여 유효한 Single Sign-On 세션이 있기 때문에 자격 증명을 다시 입력하지 않고 앱에 다시 인증할 수 있습니다.

OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint` 에 사용자를 단순히 리디렉션할 수 있습니다.

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| post_logout_redirect_uri |권장 |성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 포함되지 않은 경우 사용자에게 일반 메시지를 표시합니다. |

## <a name="single-sign-out"></a>Single Sign-Out
사용자를 `end_session_endpoint`에 리디렉션하는 경우 Azure AD는 브라우저에서 사용자의 세션을 지웁니다. 하지만 사용자는 인증을 위해 Azure AD를 사용하는 다른 응용 프로그램에 여전히 로그인되어 있을 수 있습니다. 사용자를 동시에 로그아웃하도록 해당 응용 프로그램을 활성화하려면 Azure AD는 현재 사용자가 로그인된 모든 응용 프로그램의 등록된 `LogoutUrl`로 HTTP GET 요청을 보냅니다. 응용 프로그램은 사용자를 식별하는 모든 세션을 지우고 `200` 요청을 반환하여 이 요청에 응답해야 합니다. 응용 프로그램에서 단일 로그아웃을 지원하려는 경우 응용 프로그램 코드에서 해당 `LogoutUrl`을 구현해야 합니다. Azure Portal에서 `LogoutUrl`을 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 페이지의 오른쪽 위 모서리에 있는 사용자 계정을 클릭하여 Active Directory를 선택합니다.
3. 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택한 다음, **앱 등록**을 선택하고 응용 프로그램을 선택합니다.
4. **설정**, **속성**을 차례로 클릭하고 **로그아웃 URL** 텍스트 상자를 찾습니다. 

## <a name="token-acquisition"></a>토큰 획득
대부분의 웹앱은 사용자를 로그인할 뿐만 아니라 OAuth를 사용하여 해당 사용자 대신 웹 서비스에 액세스해야 합니다. 이 시나리오에서는 사용자 인증을 위해 OpenID Connect를 결합하는 동시에 [OAuth 인증 코드 흐름](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)을 사용하여 `access_tokens`를 가져오는 데 사용할 수 있는 `authorization_code`를 획득합니다.

## <a name="get-access-tokens"></a>액세스 토큰 가져오기
액세스 토큰을 얻으려면 위에서 로그인 요청을 수정해야 합니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

`authorize` 끝점은 요청에 사용 권한 범위를 포함하고 `response_type=code+id_token`을(를) 사용하여 `scope` 쿼리 매개 변수에 표시된 사용 권한에 사용자가 동의했음을 보장하고 액세스 토큰과 교환할 인증 코드를 앱에 반환합니다.

### <a name="successful-response"></a>성공적인 응답
`response_mode=form_post` 를 사용한 성공적인 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 매개 변수 | 설명 |
| --- | --- |
| id_token |앱이 요청한 `id_token` 입니다. `id_token` 을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| 코드 |앱이 요청한 authorization_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| state |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

### <a name="error-response"></a>오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

가능한 오류 코드 및 권장되는 클라이언트 작업에 대한 설명은 [권한 부여 끝점 오류에 대한 오류 코드](#error-codes-for-authorization-endpoint-errors)를 참조하세요.

인증 `code` 및 `id_token`을 받은 후 사용자를 로그인하고 대신 액세스 토큰을 얻을 수 있습니다. 사용자를 로그인하려면 위에 설명된 대로 정확하게 `id_token` 의 유효성을 검사해야 합니다. 액세스 토큰을 얻으려면 [OAuth 프로토콜 설명서](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)의 "권한 부여 코드를 사용하여 액세스 토큰 요청" 섹션에 설명된 단계를 따르면 됩니다.
