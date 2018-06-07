---
title: Azure AD 응용 프로그램에 선택적 클레임을 제공하는 방법 알아보기 | Microsoft Docs
description: Azure Active Directory에서 발급하는 SAML 2.0 및 JWT(JSON 웹 토큰)에 사용자 지정 또는 추가 클레임을 추가하기 위한 한 가이드입니다.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c4670a7e957970acea54ff69d56edcd45092c8fe
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157234"
---
# <a name="optional-claims-in-azure-ad-preview"></a>Azure AD의 선택적 클레임(미리 보기)

이 기능은 응용 프로그램 개발자가 응용 프로그램에 전송된 토큰에서 원하는 클레임을 지정하는 데 사용합니다. 선택적 클레임을 사용하여 다음을 수행할 수 있습니다.
-   응용 프로그램에 대한 토큰에 포함할 추가 클레임을 선택합니다.
-   Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
-   응용 프로그램에 대한 사용자 지정 클레임을 추가하고 액세스합니다. 

> [!Note]
> 이 기능은 현재 공개 미리 보기로 제공되고 있습니다. 변경 내용을 되돌리거나 제거할 준비를 해야 합니다. 이 기능은 공개 미리 보기 동안 모든 Azure AD 구독에서 사용할 수 있습니다. 그러나 기능이 일반 공급되면 일부 기능에는 Azure AD Premium 구독이 필요할 수도 있습니다.

표준 클레임 목록과 토큰에 사용되는 방법을 보려면 [Azure AD에서 발급하는 토큰 기본 사항](active-directory-token-and-claims.md)을 참조하세요. 

[v2.0 Azure AD 끝점](active-directory-appmodel-v2-overview.md)의 목표 중 하나는 클라이언트의 최적 성능을 보장하기 위해 토큰 크기를 좀 더 작게 유지하는 것입니다.  따라서, 이전에 액세스 및 ID 토큰에 포함되어 있던 일부 클레임이 v 2.0 토큰에는 더 이상 존재하지 않으며, 응용 프로그램 기준으로 특수하게 요청되어야 합니다.  

**표 1: 적용 가능성**

| 계정 유형 | V1.0 끝점                      | V2.0 끝점  |
|--------------|------------------------------------|----------------|
| MSA          | 해당 없음 - 대신 RPS 티켓이 사용됩니다. | 지원 예정 |
| AAD          | 지원됨                          | 지원됨      |

## <a name="standard-optional-claims-set"></a>표준 선택적 클레임 집합
기본적으로 응용 프로그램에서 사용할 수 있는 선택적 클레임의 집합은 아래와 같습니다.  응용 프로그램에 대한 선택적 사용자 지정 클레임을 추가하려면 아래의 [디렉터리 확장](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions)을 참조하세요. 

> [!Note]
>이러한 클레임 대부분은 토큰 유형 열에 명시된 경우를 제외하고 SAML 토큰이 아닌 JWT에 포함될 수 있습니다.  또한 현재, 선택적 클레임이 AAD 사용자에 대해서만 지원되는 동안 MSA 지원이 추가됩니다.  v2.0 끝점에서 MSA에 선택적 클레임 지원이 있는 경우 사용자 유형 열은 AAD 또는 MSA 사용자에 대해 클레임을 사용할 수 있는지를 나타냅니다.  

**표 2: 표준 선택적 클레임 집합**

| Name                     | 설명                                                                                                                                                                                     | 토큰 형식 | 사용자 유형 | 메모                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | 사용자가 마지막으로 인증받은 시간입니다.  OpenID Connect 사양을 참조하세요.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | 리소스 테넌트의 지역입니다.                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | 로그인 상태 클레임입니다.                                                                                                                                                                             | JWT        |           | 6개의 플래그 반환 값:<br> "dvc_mngd": 장치가 관리됩니다.<br> "dvc_cmp": 장치가 규격입니다.<br> "dvc_dmjd": 장치가 도메인에 가입되어 있습니다.<br> "dvc_mngd_app": 장치가 MDM을 통해 관리됩니다.<br> "inknownntwk": 장치가 알려진 네트워크 내부에 있습니다.<br> "kmsi": 로그인 유지가 사용되었습니다. <br> |
| `controls`                 | 조건부 액세스 정책에 의해 적용되는 세션 컨트롤이 포함된 다중 값 클레임입니다.                                                                                                       | JWT        |           | 3개 값:<br> "app_res": 앱이 좀 더 세분화된 제한 사항을 적용해야 합니다. <br> "ca_enf": 조건부 액세스 적용이 지연되었으며 여전히 필요합니다. <br> "no_cookie": 이 토큰은 브라우저에서 쿠키를 교환하는 데 충분하지 않습니다. <br>                              |
| `home_oid`                 | 게스트 사용자의 경우 사용자의 홈 테넌트에 있는 사용자의 개체 ID입니다.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | 세션 기준 사용자 로그아웃에 사용되는 세션 ID입니다.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | 장치 플랫폼입니다.                                                                                                                                                                                 | JWT        |           | 장치 유형을 확인할 수 있는 관리 장치로 제한됩니다.                                                                                                                                                                                                                              |
| `verified_primary_email`   | 사용자의 PrimaryAuthoritativeEmail에서 소싱됩니다.                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | 사용자의 SecondaryAuthoritativeEmail에서 소싱됩니다.                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | 강제 적용된 정책 ID입니다. 현재 사용자에 대해 평가된 정책 ID의 목록입니다.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | VNET 지정자 정보입니다.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | IP 주소입니다.  요청 클라이언트의 원래 IPv4 주소를 추가합니다(VNET 내에 있는 경우).                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | 사용자의 국가입니다.                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | 리소스의 테넌트 국가입니다.                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `acct`    | 테넌트의 사용자 계정 상태입니다.  사용자가 테넌트의 구성원인 경우 값은 `0`입니다.  게스트인 경우 값은 `1`입니다.  | JWT, SAML | | |
| `upn`                      | UserPrincipalName 클레임입니다.  이 클레임은 자동으로 포함되지만, 추가 속성을 연결하여 게스트 사용자 사례에서 해당 동작을 수정하기 위해 선택적 클레임으로 지정할 수 있습니다. | JWT, SAML  |           | 추가 속성: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |

### <a name="v20-optional-claims"></a>V2.0 선택적 클레임
이러한 클레임은 항상 v1.0 토큰에 포함되지만, 요청되지 않을 경우 v2.0 토큰에서 제거됩니다.  이러한 클레임은 JWT(ID 토큰 및 액세스 토큰)에 대해서만 적용 가능합니다.  

**표 3: V2.0 전용 선택적 클레임**

| JWT 클레임     | Name                            | 설명                                                                                                                    | 메모 |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP 주소                      | 클라이언트가 로그인한 IP 주소입니다.                                                                                      |       |
| `onprem_sid`  | 온-프레미스 보안 식별자 |                                                                                                                                |       |
| `pwd_exp`     | 암호 만료 시간        | 암호가 만료되는 날짜/시간입니다.                                                                                    |       |
| `pwd_url`     | 암호 변경 URL             | 사용자가 암호 변경을 위해 방문할 수 있는 URL입니다.                                                                        |       |
| `in_corp`     | 기업 네트워크 내부        | 클라이언트가 회사 네트워크에서 로그인하는 경우 알립니다. 그러지 않은 경우, 클레임이 포함되지 않습니다.                     |       |
| `nickname`    | 애칭                        | 이름 및 성과는 별개인 사용자의 추가 이름입니다.                                                             |       |                                                                                                                |       |
| `family_name` | 성                       | Azure AD 사용자 개체에 정의된 사용자의 성을 제공합니다. <br>"family_name":"Miller" |       |
| `given_name`  | 이름                      | Azure AD 사용자 개체에 설정된 대로 사용자의 이름 또는 "지정된 이름"을 제공합니다.<br>"given_name": "Frank"                   |       |

### <a name="additional-properties-of-optional-claims"></a>선택적 클레임의 추가 속성

일부 선택적 클레임은 클레임이 반환되는 방식을 변경하도록 구성할 수 있습니다.  대개 이러한 추가 속성을 사용하여 다른 데이터 기대가 포함된 온-프레미스 응용 프로그램을 마이그레이션할 수 있습니다(예: UPN에서 `include_externally_authenticated_upn_without_hash`를 통해 해시 표시(`#`)를 처리할 수 없는 클라이언트에 사용할 수 있음).

**표 4: 표준 선택적 클레임을 구성하기 위한 값**

| 속성 이름                                     | 추가 속성 이름                                                                                                             | 설명 |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | 리소스 테넌트에 저장된 게스트 UPN을 포함합니다.  예를 들어 `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | 해시 표시(`#`)가 밑줄(`_`)로 바뀐다는 점을 제외하고 위와 같습니다(예: `foo_hometenant.com_EXT_@resourcetenant.com`). |             

> [!Note]
>추가 속성 없이 UPN 선택적 클레임을 지정하면 어떤 동작도 변경되지 않습니다. 토큰에서 발급된 새 클레임을 보려면 하나 이상의 추가 속성을 추가해야 합니다. 


#### <a name="additional-properties-example"></a>추가 속성 예제:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

이 OptionalClaims 개체를 통해 ID 토큰이 클라이언트에 반환되어 추가 홈 테넌트 및 리소스 테넌트 정보와 함께 다른 UPN을 포함하게 됩니다.  

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

응용 프로그램 매니페스트를 수정하여 응용 프로그램에 대한 선택적 클레임을 구성할 수 있습니다(아래 예제 참조). 자세한 내용은 [Azure AD 응용 프로그램 매니페스트 이해 문서](active-directory-application-manifest.md)를 참조하세요.

**샘플 스키마:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims 형식

응용 프로그램에서 요청한 선택적 클레임을 선언합니다. 응용 프로그램은 선택적 클레임이 보안 토큰 서비스에서 수신할 수 있는 3가지 토큰 유형(ID 토큰, 액세스 토큰, SAML 2 토큰)으로 반환되도록 구성할 수 있습니다. 응용 프로그램은 다른 선택적 클레임 집합이 각 토큰 유형으로 반환되도록 구성할 수 있습니다. Application 엔터티의 OptionalClaims 속성은 OptionalClaims 개체입니다.

**표 5: OptionalClaims 형식 속성**

| Name        | type                       | 설명                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 컬렉션(OptionalClaim) | ID JWT 토큰에서 반환된 선택적 클레임입니다.     |
| `accessToken` | 컬렉션(OptionalClaim) | JWT 액세스 토큰에서 반환된 선택적 클레임입니다. |
| `saml2Token`  | 컬렉션(OptionalClaim) | SAML 토큰에서 반환된 선택적 클레임입니다.       |


### <a name="optionalclaim-type"></a>OptionalClaim 형식

응용 프로그램 또는 서비스 사용자와 연결된 선택적 클레임을 포함합니다. [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) 형식의 idToken, accessToken 및 saml2Token 속성은 OptionalClaim의 컬렉션입니다.
특정 클레임에서 지원될 경우, AdditionalProperties 필드를 사용하여 OptionalClaim의 동작을 수정할 수도 있습니다.

**표 6: OptionalClaims 형식 속성**

| Name                 | type                    | 설명                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 선택적 클레임의 이름입니다.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 클레임의 원본(디렉터리 개체)입니다. 확장 속성에서 가져온 미리 정의된 클레임 및 사용자 정의 클레임이 있습니다. 원본 값이 null이면 클레임은 미리 정의된 선택적 클레임입니다. 원본 값이 user이면 name 속성의 값은 user 개체의 확장 속성입니다. |
| `essential`            | Edm.Boolean             | 이 값이 True이면 클라이언트가 지정한 클레임은 최종 사용자가 요청된 특정 태스크에 대한 원활한 권한 부여 환경을 보장하는 데 필요합니다. 기본값은 False입니다.                                                                                                                 |
| `additionalProperties` | 컬렉션(Edm.String) | 클레임의 추가 속성입니다. 속성이 이 컬렉션에 있으면 name 속성에 지정된 선택적 클레임의 동작을 수정합니다.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>디렉터리 확장을 통해 사용자 지정 클레임 구성

표준 선택적 클레임 집합 외에도, 디렉터리 스키마 확장을 포함하도록 토큰을 구성할 수도 있습니다(자세한 내용은 [디렉터리 스키마 확장 문서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) 참조).  이 기능은 앱이 사용할 수 있는 추가 사용자 정보(예: 추가 식별자 또는 사용자가 설정한 중요 구성 옵션)를 추가하는 데 유용합니다. 

> [!Note]
> 디렉터리 스키마 확장은 AAD 전용 기능이므로, 프로그램 매니페스트가 사용자 지정 확장을 요청하고, MSA 사용자가 앱에 로그인하는 경우 이러한 확장이 반환되지 않습니다. 

### <a name="values-for-configuring-additional-optional-claims"></a>선택적 추가 클레임을 구성하기 위한 값 

확장 특성의 경우 응용 프로그램 매니페스트에 확장의 전체 이름을 사용합니다(`extension_<appid>_<attributename>` 형식). `<appid>`는 클레임을 요청하는 응용 프로그램의 ID와 일치해야 합니다. 

JWT 내에서 이러한 클레임은 `extn.<attributename>` 이름 형식을 사용하여 내보내집니다.

SAML 토큰 내에서 이러한 클레임은 `http://schemas.microsoft.com/identity/claims/extn.<attributename>` URI 형식을 사용하여 내보내집니다.

## <a name="optional-claims-example"></a>선택적 클레임 예제

이 섹션에서는 시나리오를 진행하면서 응용 프로그램에 대한 선택적 클레임 기능을 사용하는 방법을 확인할 수 있습니다.
응용 프로그램 ID 구성에서 속성을 업데이트하여 선택적 클레임을 사용하도록 설정하고 구성하는 데 여러 옵션을 사용할 수 있습니다.
-   응용 프로그램 매니페스트를 수정할 수 있습니다. 다음 예제에서는 이 방법을 사용하여 구성을 수행합니다. 먼저 매니페스트를 소개하는 [Azure AD 응용 프로그램 매니페스트 이해 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)를 읽으세요.
-   또한 [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)를 사용하는 응용 프로그램을 작성하여 응용 프로그램을 업데이트할 수 있습니다. Graph API 참조 가이드의 [엔터티 및 복합 형식 참조](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type)는 선택적 클레임을 구성하는 데 도움이 될 수 있습니다.

**예제:** 아래 예제에서는 응용 프로그램용 액세스, ID 및 SAML 토큰에 클레임을 추가하도록 응용 프로그램의 매니페스트를 수정합니다.
1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  인증한 후에 페이지의 오른쪽 위 모서리에서 Azure AD 테넌트를 선택합니다.
3.  왼쪽 탐색 패널에서 **Azure AD 확장**을 선택하고 **앱 등록**을 클릭합니다.
4.  목록에서 선택적 클레임을 구성하려는 응용 프로그램을 찾아서 클릭합니다.
5.  응용 프로그램 페이지에서 **매니페스트**를 클릭하여 인라인 매니페스트 편집기를 엽니다. 
6.  이 편집기를 사용하여 매니페스트를 직접 편집할 수 있습니다. 매니페스트는 [응용 프로그램 엔터티](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)의 스키마를 따르며, 저장된 매니페스트의 서식을 자동으로 지정합니다. 새 요소가 `OptionalClaims` 속성에 추가됩니다.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      이 경우에는 응용 프로그램에서 수신할 수 있는 각 토큰 유형에 서로 다른 선택적 클레임이 추가되었습니다. 이제 ID 토큰에는 페더레이션 사용자의 UPN이 전체 형식으로 포함됩니다(`<upn>_<homedomain>#EXT#@<resourcedomain>`). 이제 액세스 토큰은 auth_time 클레임을 수신합니다. SAML 토큰은 skypeId 디렉터리 스키마 확장을 포함합니다(이 예제에서 이 앱의 앱 ID는 ab603c56068041afb2f6832e2a17e237임).  SAML 토큰은 Skype ID를 `extension_skypeId`로 노출합니다.

7.  매니페스트 업데이트가 끝나면 **저장**을 클릭하여 매니페스트를 저장합니다.


## <a name="related-content"></a>관련 콘텐츠
* Azure AD에서 제공하는 [표준 클레임](active-directory-token-and-claims.md)에 대해 알아봅니다. 
