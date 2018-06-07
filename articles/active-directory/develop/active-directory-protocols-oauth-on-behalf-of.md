---
title: OAuth2.0 On-Behalf-Of 초안 사양을 사용하여 Azure AD 서비스 간 인증 | Microsoft Docs
description: 이 문서는 OAuth 2.0 On-Behalf-Of 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2f7566bc696d07ad3a8003b3493a382f494c4599
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157217"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>On-Behalf-Of 흐름에서 위임된 사용자 ID를 사용하여 서비스 간 호출
OAuth 2.0 On-Behalf-Of 흐름은 응용 프로그램이 서비스/웹 API를 호출하고 차례로 다른 서비스/웹 API를 호출해야 하는 사용 사례를 제공합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 대신하여 Azure AD(Azure Active Directory)에서 액세스 토큰을 보호해야 합니다.

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of 흐름 다이어그램
사용자가 [OAuth 2.0 권한 부여 코드 부여 흐름](active-directory-protocols-oauth-code.md)을 사용하여 응용 프로그램에 대해 인증되었다고 가정합니다. 이 시점에서 응용 프로그램은 사용자의 클레임이 있는 액세스 토큰(토큰 A)을 포함하고 중간 계층 웹 API(API A)에 액세스하는 데 동의합니다. 이제 API A는 다운스트림 웹 API(API B)에 대해 인증된 요청을 해야 합니다.

다음 단계는 On-Behalf-Of 흐름을 구성하며 다음 다이어그램을 통해 쉽게 이해할 수 있습니다.

![OAuth2.0 On-Behalf-Of 흐름](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. 클라이언트 응용 프로그램은 토큰 A와 함께 API A에 요청합니다.
2. API A는 Azure AD 토큰 발급 끝점을 인증하고 API B에 액세스하기 위해 토큰을 요청합니다.
3. Azure AD 토큰 발급 끝점은 토큰 A와 함께 API A의 자격 증명의 유효성을 검사하고 API B(토큰 B)에 대한 액세스 토큰을 발급합니다.
4. 토큰 B는 API B에 대한 요청의 권한 부여 헤더에 설정됩니다.
5. 보안 리소스의 데이터가 API B에 의해 반환됩니다.

## <a name="register-the-application-and-service-in-azure-ad"></a>Azure AD에서 응용 프로그램 및 서비스 등록
Azure AD에 클라이언트 응용 프로그램 및 중간 계층 서비스를 모두 등록합니다.
### <a name="register-the-middle-tier-service"></a>중간 계층 서비스 등록
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 오른쪽 위에서 계정을 클릭하고 **디렉터리** 목록에서 응용 프로그램을 등록하려는 Active Directory 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭하고 **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **새 응용 프로그램 등록**을 선택합니다.
5. 응용 프로그램의 이름을 입력하고 응용 프로그램 형식을 선택합니다. 응용 프로그램 형식에 따라 로그온 URL 또는 리디렉션 URL을 기본 URL로 설정합니다. **만들기**를 클릭하여 응용 프로그램을 만듭니다.
6. Azure Portal에서 응용 프로그램을 선택하고 **설정**을 클릭합니다. 설정 메뉴에서 **키**를 선택하고 키를 추가합니다. 키 기간으로는 1년 또는 2년을 선택합니다. 이 페이지를 저장하면 키 값이 표시되고 값을 안전한 위치에 복사 및 저장합니다. 나중에 구현 시 응용 프로그램 설정을 구성하는 데 이 키가 필요합니다. 이 키 값은 다시 표시되지 않으며 다른 방법으로 검색할 수 없으므로 Azure Portal에 표시되는 즉시 기록해 두세요.

### <a name="register-the-client-application"></a>클라이언트 응용 프로그램 등록
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 오른쪽 위에서 계정을 클릭하고 **디렉터리** 목록에서 응용 프로그램을 등록하려는 Active Directory 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭하고 **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **새 응용 프로그램 등록**을 선택합니다.
5. 응용 프로그램의 이름을 입력하고 응용 프로그램 형식을 선택합니다. 응용 프로그램 형식에 따라 로그온 URL 또는 리디렉션 URL을 기본 URL로 설정합니다. **만들기**를 클릭하여 응용 프로그램을 만듭니다.
6. 응용 프로그램에 대한 권한 구성 - 설정 메뉴에서 **필수 권한** 섹션을 선택하고 **추가**, **API 선택**을 차례로 클릭한 다음 텍스트 상자에 중간 계층 서비스의 이름을 입력합니다. 그런 다음 **권한 선택**을 클릭하고 '*서비스 이름* 액세스'를 선택합니다.

### <a name="configure-known-client-applications"></a>알려진 클라이언트 응용 프로그램 구성
이 시나리오에서는 중간 계층 서비스에서 다운스트림 API에 액세스하기 위해 사용자 동의를 얻기 위한 사용자 상호 작용이 없습니다. 따라서 다운스트림 API에 대한 액세스를 부여할 수 있는 옵션이 인증 과정에서 동의 단계 중 일부로 미리 제공되어야 합니다.
이를 위해 아래 단계에 따라 Azure AD에서 클라이언트 앱의 등록을 중간 계층 서비스의 등록과 명시적으로 바인딩합니다. 이렇게 하면 클라이언트와 중간 계층 모두에 필요한 동의가 단일 대화 상자로 병합됩니다.
1. 중간 계층 서비스 등록으로 이동하고 **매니페스트**를 클릭하여 매니페스트 편집기를 엽니다.
2. 매니페스트에서 `knownClientApplications` 배열 속성을 찾고 클라이언트 응용 프로그램의 클라이언트 ID를 요소로 추가합니다.
3. [저장] 단추를 클릭하여 매니페스트를 저장합니다.

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청
액세스 토큰을 요청하려면 다음 매개 변수로 테넌트별 Azure AD 끝점에 HTTP POST를 만듭니다.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
클라이언트 응용 프로그램이 공유 암호 또는 인증서 중에서 어떤 방식으로 보호되도록 선택되는지에 따라 두 가지 사례가 있습니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청
공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| 어설션 |필수 | 요청에 사용된 토큰 값입니다. |
| client_id |필수 | Azure AD에 등록하는 동안 호출 서비스에 할당된 앱 ID입니다. Azure 관리 포털에서 앱 ID를 찾으려면, **Active Directory**를 클릭하고, 디렉터리를 클릭한 후 응용 프로그램 이름을 클릭합니다. |
| client_secret |필수 | Azure AD에서 서비스를 호출하기 위해 등록된 키입니다. 이 값은 등록 시 메모해 두어야 합니다. |
| resource |필수 | 수신 서비스(보안 리소스)의 앱 ID URI입니다. Azure 관리 포털에서 앱 ID URI을 찾으려면, **Active Directory**, 디렉터리를 차례로 클릭하고, 응용 프로그램 이름을 클릭한 후 **모든 설정**, **속성**을 클릭합니다. |
| requested_token_use |필수 | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| scope |필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우, 범위 **openid**를 지정해야 합니다.|

#### <a name="example"></a>예
다음 HTTP POST는 https://graph.windows.net 웹 API용 액세스 토큰을 요청합니다. `client_id`는 액세스 토큰을 요청하는 서비스를 식별합니다.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청
인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| 어설션 |필수 | 요청에 사용된 토큰 값입니다. |
| client_id |필수 | Azure AD에 등록하는 동안 호출 서비스에 할당된 앱 ID입니다. Azure 관리 포털에서 앱 ID를 찾으려면, **Active Directory**를 클릭하고, 디렉터리를 클릭한 후 응용 프로그램 이름을 클릭합니다. |
| client_assertion_type |필수 |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |필수 | 응용 프로그램의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요.|
| resource |필수 | 수신 서비스(보안 리소스)의 앱 ID URI입니다. Azure 관리 포털에서 앱 ID URI을 찾으려면, **Active Directory**, 디렉터리를 차례로 클릭하고, 응용 프로그램 이름을 클릭한 후 **모든 설정**, **속성**을 클릭합니다. |
| requested_token_use |필수 | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| scope |필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우, 범위 **openid**를 지정해야 합니다.|

client_secret 매개 변수가 두 개의 매개 변수 client_assertion_type 및 client_assertion으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 거의 동일합니다.

#### <a name="example"></a>예
다음 HTTP POST는 인증서가 있는 https://graph.windows.net 웹 API용 액세스 토큰을 요청합니다. `client_id`는 액세스 토큰을 요청하는 서비스를 식별합니다.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답
성공 응답은 다음 매개 변수가 있는 JSON OAuth 2.0 응답입니다.

| 매개 변수 | 설명 |
| --- | --- |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| scope |토큰에 부여된 액세스 범위입니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| resource |수신 서비스(보안 리소스)의 앱 ID URI입니다. |
| access_token |요청된 액세스 토큰입니다. 호출 서비스는 이 토큰을 사용하여 수신 서비스에 인증할 수 있습니다. |
| id_token |요청된 ID 토큰입니다. 호출 서비스는 ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| refresh_token |요청된 액세스 토큰에 대한 새로 고침 토큰입니다. 호출 서비스는 이 토큰을 사용하여 현재 액세스 토큰이 만료된 후 다른 액세스 토큰을 요청할 수 있습니다. |

### <a name="success-response-example"></a>성공 응답 예제
다음 예제에서는 https://graph.windows.net 웹 API용 액세스 토큰 요청에 대한 성공 응답을 보여 줍니다.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>오류 응답 예제
다운스트림 API에 설정된 Multi-Factor Authentication과 같은 조건부 액세스 정책이 있는 경우, 다운스트림 API에 대한 액세스 토큰을 얻으려고 할 때 Azure AD 토큰 끝점에서 오류 응답이 반환됩니다. 중간 계층 서비스는 이 오류를 클라이언트 응용 프로그램에 전달하여 클라이언트 응용 프로그램이 조건부 액세스 정책을 충족시키기 위해 사용자 상호 작용을 제공할 수 있도록 해야 합니다.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>보안 리소스에 액세스하는 데 액세스 토큰 사용
이제 중간 계층 서비스는 위에서 획득한 토큰을 사용하고 `Authorization` 헤더에서 토큰을 설정하여 다운스트림 웹 API에 대해 인증된 요청을 할 수 있습니다.

### <a name="example"></a>예
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>다음 단계
OAuth 2.0 프로토콜 및 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행하는 다른 방법에 대해 자세히 알아보세요.
* [Azure AD에서 OAuth 2.0 클라이언트 자격 증명 부여를 사용하여 서비스 간 인증](active-directory-protocols-oauth-service-to-service.md)
* [Azure AD의 OAuth 2.0](active-directory-protocols-oauth-code.md)
