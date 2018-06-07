---
title: Azure AD v2.0 OAuth2.0 On-Behalf-Of 흐름 | Microsoft Docs
description: 이 문서는 OAuth 2.0 On-Behalf-Of 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2aa1c33f138619283a8785aaf3772465df6c9aee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156826"
---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Azure Active Directory v2.0 및 OAuth 2.0 On-Behalf-Of 흐름
OAuth 2.0 On-Behalf-Of 흐름은 응용 프로그램이 서비스/웹 API를 호출하고 차례로 다른 서비스/웹 API를 호출해야 하는 사용 사례를 제공합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 대신하여 Azure AD(Azure Active Directory)에서 액세스 토큰을 보호해야 합니다.

> [!NOTE]
> v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
>
>

## <a name="protocol-diagram"></a>프로토콜 다이어그램
사용자가 [OAuth 2.0 권한 부여 코드 부여 흐름](active-directory-v2-protocols-oauth-code.md)을 사용하여 응용 프로그램에 대해 인증되었다고 가정합니다. 이 시점에서 응용 프로그램에는 사용자의 클레임 및 중간 계층 웹 API(API A) 액세스에 대한 동의가 있는 *API A용* 액세스 토큰(토큰 A)이 있습니다. 이제 API A는 다운스트림 웹 API(API B)에 대해 인증된 요청을 해야 합니다.

> [!IMPORTANT]
> [암시적 권한 부여](active-directory-v2-protocols-implicit.md)를 사용하여 획득한 토큰은 On-Behalf-Of 흐름에 사용할 수 없습니다. 암시적 흐름의 클라이언트는 클라이언트 비밀 등을 통해 인증되지 않으므로 다른 토큰, 아마도 더 강력한 토큰으로 부트스트랩할 수 없습니다.

다음 단계는 On-Behalf-Of 흐름을 구성하며 다음 다이어그램을 통해 쉽게 이해할 수 있습니다.

![OAuth2.0 On-Behalf-Of 흐름](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. 클라이언트 응용 프로그램은 토큰 A(API A의 `aud` 클레임 포함)를 사용하여 API A에 요청합니다.
2. API A는 Azure AD 토큰 발급 끝점을 인증하고 API B에 액세스하기 위해 토큰을 요청합니다.
3. Azure AD 토큰 발급 끝점은 토큰 A와 함께 API A의 자격 증명의 유효성을 검사하고 API B(토큰 B)에 대한 액세스 토큰을 발급합니다.
4. 토큰 B는 API B에 대한 요청의 권한 부여 헤더에 설정됩니다.
5. 보안 리소스의 데이터가 API B에 의해 반환됩니다.

> [!NOTE]
> 이 시나리오에서는 중간 계층 서비스에서 다운스트림 API에 액세스하기 위해 사용자 동의를 얻기 위한 사용자 상호 작용이 없습니다. 따라서 다운스트림 API에 대한 액세스를 부여할 수 있는 옵션이 인증 과정에서 동의 단계 중 일부로 미리 제공됩니다.
>

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청
액세스 토큰을 요청하려면 다음 매개 변수로 테넌트별 Azure AD v2.0 끝점에 HTTP POST를 만듭니다.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

클라이언트 응용 프로그램이 공유 암호 또는 인증서 중에서 어떤 방식으로 보호되도록 선택되는지에 따라 두 가지 사례가 있습니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청
공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| client_id |필수 | [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| client_secret |필수 | 응용 프로그램 등록 포털에서 앱에 대해 생성한 응용 프로그램 비밀입니다. |
| 어설션 |필수 | 요청에 사용된 토큰 값입니다. |
| scope |필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. 자세한 내용은 [범위](active-directory-v2-scopes.md)를 참조하세요.|
| requested_token_use |필수 | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |

#### <a name="example"></a>예
다음 HTTP POST는 `user.read` 범위의 https://graph.microsoft.com 웹 API용 액세스 토큰과 새로 고침 토큰을 요청합니다.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청
인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| grant_type |필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| client_id |필수 | [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱에 할당한 응용 프로그램 ID입니다. |
| client_assertion_type |필수 |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |필수 | 응용 프로그램의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요.|
| 어설션 |필수 | 요청에 사용된 토큰 값입니다. |
| requested_token_use |필수 | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| scope |필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. 자세한 내용은 [범위](active-directory-v2-scopes.md)를 참조하세요.|

client_secret 매개 변수가 두 개의 매개 변수 client_assertion_type 및 client_assertion으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 거의 동일합니다.

#### <a name="example"></a>예
다음 HTTP POST는 인증서가 있는 `user.read` 범위의 https://graph.microsoft.com 웹 API용 액세스 토큰을 요청합니다.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답
성공 응답은 다음 매개 변수가 있는 JSON OAuth 2.0 응답입니다.

| 매개 변수 | 설명 |
| --- | --- |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| scope |토큰에 부여된 액세스 범위입니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| access_token |요청된 액세스 토큰입니다. 호출 서비스는 이 토큰을 사용하여 수신 서비스에 인증할 수 있습니다. |
| refresh_token |요청된 액세스 토큰에 대한 새로 고침 토큰입니다. 호출 서비스는 이 토큰을 사용하여 현재 액세스 토큰이 만료된 후 다른 액세스 토큰을 요청할 수 있습니다. 새로 고침 토큰은 `offline_access` 범위가 요청된 경우에만 제공됩니다.|

### <a name="success-response-example"></a>성공 응답 예제
다음 예제에서는 https://graph.microsoft.com 웹 API용 액세스 토큰 요청에 대한 성공 응답을 보여 줍니다.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> 위의 액세스 토큰은 V1 형식의 토큰입니다. 이는 토큰이 액세스되는 리소스에 따라 제공되기 때문입니다. Microsoft Graph는 V1 토큰을 요청하므로 클라이언트에서 Microsoft Graph용 토큰을 요청할 때 Azure AD에서 V1 액세스 토큰을 생성합니다. 응용 프로그램만 액세스 토큰을 검사해야 하며 클라이언트는 검사할 필요가 없습니다. 


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
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="next-steps"></a>다음 단계
OAuth 2.0 프로토콜 및 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행하는 다른 방법에 대해 자세히 알아보세요.
* [Azure AD v2.0에서 OAuth 2.0 클라이언트 자격 증명 부여](active-directory-v2-protocols-oauth-client-creds.md)
* [Azure AD v2.0의 OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
