---
title: Azure AD v 2.0을 사용하여 사용자 상호 작용 없이 보안 리소스에 액세스 | Microsoft Docs
description: OAuth 2.0 인증 프로토콜의 Azure AD 구현을 사용하여 웹 응용 프로그램을 빌드합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: db466a3ae416c47f86bb66b3bb8ba4bcd7741f5f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157339"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 및 OAuth 2.0 클라이언트 자격 증명 흐름
응용 프로그램의 ID를 사용하여 웹 호스팅 리소스에 액세스하기 위해 RFC 6749에 명시된 [OAuth 2.0 클라이언트 자격 증명 권한 부여](http://tools.ietf.org/html/rfc6749#section-4.4)(때로는 *2단계 OAuth*라고도 함)를 사용할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 응용 프로그램은 종종 *디먼* 또는 *서비스 계정*이라고 합니다.

> [!NOTE]
> v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
>
>

더 일반적인 *삼각 OAuth*에서 클라이언트 응용 프로그램에는 특정 사용자를 대신하여 리소스에 액세스할 수 있는 사용 권한이 부여됩니다. 사용 권한은 일반적으로 [동의](active-directory-v2-scopes.md) 프로세스 동안 사용자에게서 응용 프로그램에 위임됩니다. 그러나 클라이언트 자격 증명 흐름에서는 응용 프로그램 자체에 직접 사용 권한이 부여됩니다. 앱이 리소스에 대한 토큰을 제공하는 경우 리소스는 사용자에게 권한을 부여하지 않고 앱 자체에 작업을 수행할 수 있는 권한을 부여합니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램
전체 클라이언트 자격 증명 흐름은 다음 다이어그램과 유사합니다. 이 문서의 뒷부분에 각 단계에 대한 설명이 나와 있습니다.

![클라이언트 자격 증명 흐름](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>직접 권한 부여 가져오기
일반적으로 앱은 리소스에서 ACL(액세스 제어 목록)을 통하거나 Azure AD(Azure Active Directory)에서 응용 프로그램 사용 권한 할당을 통하는 두 가지 방법 중 하나로 리소스에 액세스할 수 있는 직접 권한을 부여받습니다. 이 두 메서드는 Azure AD에서 가장 일반적이며 클라이언트 자격 증명 흐름을 수행하는 클라이언트 및 리소스에 사용하는 것이 좋습니다. 그러나 리소스가 다른 방식으로 해당 클라이언트를 인증하도록 선택할 수 있습니다. 각 리소스 서버가 해당 응용 프로그램에 가장 적합한 방법을 선택할 수 있습니다.

### <a name="access-control-lists"></a>액세스 제어 목록
리소스 공급자는 특정 수준의 액세스를 알고 권한을 부여하는 응용 프로그램 ID 목록에 따라 권한 부여 확인을 적용할 수 있습니다. 리소스가 v2.0 끝점에서 토큰을 받으면 토큰을 디코딩하고 `appid` 및 `iss` 클레임에서 클라이언트의 응용 프로그램 ID를 추출할 수 있습니다. 그런 다음 유지 관리하는 ACL에 대해 응용 프로그램을 비교합니다. ACL의 세분성 및 메서드는 리소스 간에 크게 달라질 수 있습니다.

일반적인 사용 사례는 ACL을 사용하여 웹 응용 프로그램 또는 Web API에 대한 테스트를 실행하는 것입니다. Web API는 특정 클라이언트에 대한 모든 권한의 하위 집합만 부여할 수 있습니다. API에서 종단 간 테스트를 실행하려면 v2.0 끝점에서 토큰을 획득하여 API에 전송하는 테스트 클라이언트를 만듭니다. 그러면 API는 API의 전체 기능에 대한 모든 권한에 대해 ACL에서 테스트 클라이언트의 응용 프로그램 ID를 확인합니다. 이 종류의 ACL을 사용하는 경우 호출자의 `appid` 값에 대한 유효성을 검사할 뿐만 아니라 토큰의 `iss` 값을 신뢰할 수 있는지도 확인해야 합니다.

이 종류의 권한 부여는 개인 Microsoft 계정을 가진 소비자 사용자가 소유한 데이터에 액세스해야 하는 디먼 및 서비스 계정에 일반적입니다. 조직에서 소유한 데이터의 경우 응용 프로그램 사용 권한을 통해 필요한 권한 부여를 획득하는 것이 좋습니다.

### <a name="application-permissions"></a>응용 프로그램 사용 권한
ACL을 사용하는 대신 API를 사용하여 응용 프로그램 사용 권한 집합을 노출할 수 있습니다. 응용 프로그램 사용 권한은 조직의 관리자가 응용 프로그램에 부여하며 해당 조직 및 그 직원이 소유한 데이터 액세스에만 사용할 수 있습니다. 예를 들어 Microsoft Graph는 다음을 수행할 수 있는 몇 가지 응용 프로그램 사용 권한을 노출합니다.

* 모든 사서함에서 메일 읽기
* 모든 사서함에서 메일 읽기 및 쓰기
* 모든 사용자로 메일 보내기
* 디렉터리 데이터 읽기

응용 프로그램 사용 권한에 대한 자세한 내용은 [Microsoft Graph](https://graph.microsoft.io)를 참조하세요.

앱에서 응용 프로그램 사용 권한을 사용하려면 다음 섹션에서 설명하는 단계를 수행합니다.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>앱 등록 포털에서 사용 권한 요청
1. [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 응용 프로그램으로 이동하거나 응용 프로그램이 없는 경우 [응용 프로그램을 만듭니다](active-directory-v2-app-registration.md). 응용 프로그램을 만들 때 하나 이상의 응용 프로그램 암호를 사용해야 합니다.
2. **Microsoft Graph 권한** 섹션을 찾은 다음, 앱에 필요한 **응용 프로그램 권한**을 추가합니다.
3. 앱 등록을 **저장**합니다.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>사용자가 앱에 로그인하는 것이 좋습니다.
일반적으로 응용 프로그램 사용 권한을 사용하는 응용 프로그램을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지 또는 보기가 필요합니다. 이 페이지는 앱 로그인 흐름의 일부, 앱 설정의 일부 또는 전용 "연결" 흐름일 수 있습니다. 대부분의 경우에 사용자가 회사 또는 학교 Microsoft 계정으로 로그인한 후에 앱은 이 "연결" 보기만을 표시하게 됩니다.

사용자가 앱에 로그인하면 사용자에게 응용 프로그램 사용 권한을 승인하도록 요청하기 전에 사용자가 속해 있는 조직을 식별할 수 있습니다. 반드시 필요하지는 않지만 사용자를 위한 보다 직관적인 환경을 만드는 것이 유용할 수 있습니다. 사용자가 로그인하려면 [v2.0 프로토콜 자습서](active-directory-v2-protocols.md)를 수행합니다.

#### <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청
조직의 관리자에게 사용 권한을 요청할 준비가 되면 v 2.0 *관리 동의 끝점*에 사용자를 리디렉션할 수 있습니다.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| tenant |필수 |사용 권한을 요청하려는 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다. 사용자가 속한 테넌트가 무엇인지 모르고 테넌트를 사용하여 로그인하지 않으려는 경우 `common`을 사용합니다. |
| client_id |필수 |[응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| redirect_uri |필수 |리디렉션 URI는 처리할 앱에 응답을 전송하려는 위치입니다. URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 하며 추가 경로 세그먼트가 있을 수 있습니다. |
| state |권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |

이 시점에서 Azure AD는 테넌트 관리자가 요청을 완료하기 위해 로그인하도록 강제합니다. 관리자에게는 앱 등록 포털에서 앱에 요청한 응용 프로그램 직접 사용 권한을 모두 승인하라는 메시지가 표시됩니다.

##### <a name="successful-response"></a>성공적인 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| --- | --- | --- |
| tenant |디렉터리 테넌트는 GUID 형식으로 요청한 권한을 응용 프로그램에 부여합니다. |
| state |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| admin_consent |**true**로 설정합니다. |

##### <a name="error-response"></a>오류 응답
관리자가 응용 프로그램에 대한 사용 권한을 승인하지 않는 경우 실패한 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| --- | --- | --- |
| error |오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

앱 프로비전 끝점에서 성공적인 응답을 받았다면 앱은 요청한 응용 프로그램 직접 사용 권한을 얻게 됩니다. 이제 원하는 리소스에 대한 토큰을 요청할 수 있습니다.

## <a name="get-a-token"></a>토큰 가져오기
응용 프로그램에 필요한 권한을 부여받은 후에는 API에 대한 액세스 토큰을 획득하는 과정을 진행합니다. 클라이언트 자격 증명 권한 부여를 사용하여 토큰을 가져오려면 POST 요청을 `/token` v2.0 끝점에 보냅니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| tenant |필수 | 응용 프로그램에서 GUID 또는 도메인 이름 형식으로 작동하도록 계획하는 디렉터리 테넌트입니다. |
| client_id |필수 |[응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| scope |필수 |이 요청에서 `scope` 매개 변수에 전달된 값은 원하는 리소스의 리소스 식별자(응용 프로그램 ID URI)여야 하고 `.default` 접미사가 붙어 있어야 합니다. Microsoft Graph 예제의 경우 값은 `https://graph.microsoft.com/.default`입니다. 이 값은 앱에 구성한 모든 응용 프로그램 직접 사용 권한의 v2.0 끝점을 알려주며 사용하려는 리소스와 연결된 사용 권한의 토큰을 발급해야 합니다. |
| client_secret |필수 |앱 등록 포털에서 앱에 대해 생성한 응용 프로그램 암호입니다. |
| grant_type |필수 |`client_credentials`이어야 합니다. |

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| tenant |필수 | 응용 프로그램에서 GUID 또는 도메인 이름 형식으로 작동하도록 계획하는 디렉터리 테넌트입니다. |
| client_id |필수 |[응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| scope |필수 |이 요청에서 `scope` 매개 변수에 전달된 값은 원하는 리소스의 리소스 식별자(응용 프로그램 ID URI)여야 하고 `.default` 접미사가 붙어 있어야 합니다. Microsoft Graph 예제의 경우 값은 `https://graph.microsoft.com/.default`입니다. 이 값은 앱에 구성한 모든 응용 프로그램 직접 사용 권한의 v2.0 끝점을 알려주며 사용하려는 리소스와 연결된 사용 권한의 토큰을 발급해야 합니다. |
| client_assertion_type |필수 |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |필수 | 응용 프로그램의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요.|
| grant_type |필수 |`client_credentials`이어야 합니다. |

client_secret 매개 변수가 두 개의 매개 변수 client_assertion_type 및 client_assertion으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 거의 동일합니다.

### <a name="successful-response"></a>성공적인 응답
성공적인 응답은 다음과 같습니다.

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 매개 변수 | 설명 |
| --- | --- |
| access_token |요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다. |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 `bearer` 유형만 지원합니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |

### <a name="error-response"></a>오류 응답
오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수 | 설명 |
| --- | --- |
| error |발생한 오류 유형을 분류하고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error_codes |진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp |오류가 발생한 시간입니다. |
| trace_id |진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation_id |전체 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

## <a name="use-a-token"></a>토큰 사용
토큰을 획득했으므로 해당 토큰을 사용하여 리소스에 요청합니다. 토큰이 만료되면 `/token` 끝점에 대한 요청을 반복하여 새 액세스 토큰을 획득합니다.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>코드 샘플
관리 동의 끝점을 사용하여 클라이언트 자격 증명 권한 부여를 구현하는 응용 프로그램의 예를 보려면 [v2.0 디먼 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 참조하세요.
