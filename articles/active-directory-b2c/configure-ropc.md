---
title: Azure AD B2C에서 리소스 소유자 암호 자격 증명 흐름 구성 | Microsoft Docs
description: Azure AD B2C에서 리소스 소유자 암호 자격 증명 흐름을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158257"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC(리소스 소유자 암호 자격 증명) 흐름 구성

ROPC(리소스 소유자 암호 자격 증명) 흐름은 신뢰 당사자라고도 하는 응용 프로그램이 사용자 id 및 ID 토큰의 암호, 액세스 토큰, 새로 고침 토큰 등의 유효한 자격 증명을 교환하는 OAUTH 표준 인증 흐름입니다. 

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.

Azure AD B2C에서는 다음과 같은 옵션이 지원됩니다.

- **Native Client** – 인증 과정의 사용자 상호 작용은 사용자 쪽 장치에서 실행되는 코드를 사용하여 발생하며, 이러한 장치는 Android 같은 기본 운영 체제에서 실행되는 모바일 응용 프로그램일 수도 있고 Javascript 같은 브라우저에서 실행되는 응용 프로그램일 수도 있습니다.
- **공용 클라이언트 흐름** – 응용 프로그램에서 수집한 사용자 자격 증명만 API 호출에서 전송됩니다. 응용 프로그램의 자격 증명은 전송되지 않습니다.
- **새 클레임 추가** - ID 토큰 콘텐츠를 변경하여 새 클레임을 추가할 수 있습니다. 

다음과 같은 흐름은 지원되지 않습니다.

- **서버 간** - IDPS(ID 보호 시스템)는 상호 작용의 일부인 호출자(네이티브 클라이언트)에서 수집한 신뢰할 수 있는 IP 주소가 필요합니다.  서버 쪽 API 호출에서는 서버의 IP 주소만 사용되며, 실패한 인증의 동적 임계값을 초과할 경우 IDPS는 반복되는 IP 주소를 공격자로 인식할 수 있습니다.
- **기밀 클라이언트 흐름** - 응용 프로그램 클라이언트 ID의 유효성은 검사하지만 응용 프로그램 비밀의 유효성은 검사하지 않습니다.

##  <a name="create-a-resource-owner-policy"></a>리소스 소유자 정책 만들기

1. Azure AD B2C 테넌트의 전역 관리자로 Azure Portal에 로그인합니다.
2. Azure AD B2C 테넌트로 전환하려면 포털의 맨 위 오른쪽에서 B2C 디렉터리를 선택합니다.
3. **정책** 아래에서 **리소스 소유자 정책**을 선택합니다.
4. 정책 이름을 입력하고(예: *ROPC_Auth*) **응용 프로그램 클레임**을 클릭합니다.
5. *표시 이름*, *이메일 주소*, *ID 공급자* 등 응용 프로그램에 필요한 응용 프로그램 클레임을 선택합니다.
6. **확인**을 클릭한 다음, **만들기**를 클릭합니다.

다음 예제와 같은 엔드포인트가 표시됩니다.

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>응용 프로그램 등록

1. B2C 설정에서 **응용 프로그램**을 클릭한 다음 **+ 추가**를 클릭합니다.
2. *ROPC_Auth_app* 같은 응용 프로그램 이름을 입력합니다.
3. **웹앱/Web API**에 대해 **아니요**를 클릭하고 **네이티브 클라이언트**에 대해 **예**를 클릭합니다.
4. 나머지 값은 원래대로 두고 **만들기**를 클릭합니다.
5. 새 응용 프로그램을 선택하고 응용 프로그램 ID를 기록해 둡니다.

## <a name="test-the-policy"></a>정책 테스트

선호하는 API 개발 응용 프로그램을 사용하여 API 호출을 생성하고, 응답을 검토하여 정책을 디버그합니다. 다음 표의 정보를 POST 요청의 본문으로 사용하여 이와 같은 호출을 작성합니다.
- *yourtenant.onmicrosoft.com*을 B2C 테넌트의 이름으로 바꿉니다.
- *B2C_1A_ROPC_Auth*를 ROPC 정책의 전체 이름으로 바꿉니다.
- *bef2222d56-552f-4a5b-b90a-1988a7d634c3*을 등록의 응용 프로그램 ID로 바꿉니다.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| 키 | 값 |
| --- | ----- |
| 사용자 이름 | leadiocl@outlook.com |
| 암호 | Passxword1 |
| grant_type | 암호 |
| scope | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | 토큰 id_token |

*Client_id*는 앞에서 응용 프로그램 ID로 기록해 둔 값입니다. *Offline_access*는 새로 고침 토큰을 받고 싶은 경우에 사용하는 선택 사항입니다. 

실제 POST 요청은 다음과 같습니다.

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


오프라인 액세스의 성공적인 응답은 다음 예제와 비슷합니다.

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>새로 고침 토큰 교환

다음 표의 정보를 요청의 본문으로 사용하여 이와 같은 POST 호출을 작성합니다.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| 키 | 값 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* 및 *리소스*는 앞에서 응용 프로그램 ID로 기록해 둔 값입니다. *Refresh_token*은 앞에서 언급한 인증 통화에서 받은 토큰입니다.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>선호하는 네이티브 SDK 또는 앱 인증을 사용하여 구현


Azure AD B2C 구현은 OAuth 2.0 표준 또는 공용 클라이언트 ROPC를 충족하며 대부분의 클라이언트 SDK와 호환되어야 합니다.  Microsoft는 iOS용 AppAuth 및 Android용 AppAuth를 사용하여 프로덕션 환경에서 이 흐름을 광범위하게 테스트했습니다.  최신 정보는 https://appauth.io/를 참조하세요.

Android에 대한 https://aka.ms/aadb2cappauthropc 및 https://aka.ms/aadb2ciosappauthropc의 github에서 Azure AD B2C에 사용하도록 구성된 아직 작업 중인 샘플을 다운로드할 수 있습니다.




