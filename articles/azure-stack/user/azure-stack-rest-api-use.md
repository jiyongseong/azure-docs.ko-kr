---
title: Azure 스택 API를 사용 하 여 | Microsoft Docs
description: Azure 스택 API 요청을 Azure에서 인증을 검색 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e8a9489a3f487a45303bac45f805381b41427b4b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Azure 스택 API를 사용 하 여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

마켓플레이스 항목을 게시 하기 등의 작업을 자동화 하는 Azure 스택 API 응용 프로그래밍 인터페이스 ()를 사용할 수 있습니다.

API에는 Microsoft Azure 로그인 끝점에 대해 인증 하 여 클라이언트가 필요 합니다. 끝점에 사용할 Azure 스택 API에 전송 하는 모든 요청 헤더에 토큰을 반환 합니다. Microsoft Azure에서는 Oauth 2.0을 사용 합니다.

이 문서에서는 사용 하는 예제는 **cURL** Azure 스택 요청을 만드는 유틸리티입니다. CURL을 응용 프로그램은 데이터를 전송 하기 위한 라이브러리와 명령줄 도구입니다. 이 예제에서는 Azure 스택 API에 액세스할 수 있는 토큰을 검색 하는 과정을 안내 합니다. 대부분의 프로그래밍 언어는 이러한 토큰 새로 고침 토큰으로 실행 되는 강력한 핸들 및 관리 태스크를 포함 하는 Oauth 2.0 라이브러리를 제공 합니다.

와 같은 제네릭 REST 클라이언트와 Azure 스택 REST API를 사용 하는 전체 과정 **cURL**, 요청, 및에 응답 페이로드를 받을 예상할 수를 보여 줍니다. 기본 쉽게 이해할 수 있도록 합니다.

이 문서는 대화형 로그인 같은 토큰을 검색 하거나 전용된 응용 프로그램 Id를 만드는에 사용할 수 있는 모든 옵션을 탐색 하지 않습니다. 이러한 항목에 대 한 정보를 얻으려면 참조 [Azure REST API 참조](https://docs.microsoft.com/rest/api/)합니다.

## <a name="get-a-token-from-azure"></a>Azure에서 토큰을 가져옵니다.

사용 하 여 지정 된 콘텐츠 형식 x-www-형식-urlencoded 액세스 토큰을 가져오는 요청 본문을 만듭니다. Azure REST 인증 및 로그인 끝점에 요청을 게시 합니다.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**테 넌 트 ID** 입니다.

 - 테 넌 트 도메인을와 같은 `fabrikam.onmicrosoft.com`
 - 테 넌 트 ID와 같은 `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - 테 넌 트 독립적 키에 대 한 기본값: `common`

### <a name="post-body"></a>게시 본문

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

각 값:

 - **grant_type**  
    사용 하는 인증 체계의 형식입니다. 이 예제에서는 값이 `password`

 - **resource**  
    리소스 토큰에 액세스합니다. Azure 스택 관리 메타 데이터 끝점을 쿼리하여 리소스를 찾을 수 있습니다. 확인 된 **대상** 섹션

 - **Azure 스택 관리 끝점**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > 테 넌 트 API에 액세스 하려고 하는 관리자는 다음 수행 해야 예를 들어 테 넌 트 끝점을 사용 하십시오. `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  예를 들어 끝점으로 Azure 스택 Development Kit와:

    ```bash
    curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
    ```

  응답:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>예

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  이 값은 기본 값으로 하드 코드 되어 있습니다.

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  대체 옵션을 특정 시나리오에 사용할 수 있습니다.

  
  | 응용 프로그램 | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **사용자 이름**

  예를 들어 Azure 스택 AAD 계정:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **암호**

  Azure 스택 AAD 관리자 암호입니다.

### <a name="example"></a>예

요청:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

응답:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API 쿼리

액세스 토큰을 발생 하면 각 API 요청으로 헤더를 추가 해야 합니다. 이 위해 헤더를 만들어야 할 **권한 부여** 값을 가진: `Bearer <access token>`합니다. 예: 

요청:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

응답:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL의 구조와 쿼리 구문

일반 요청 URI, 이루어져: {URI 체계}:// {URI 호스트} / {리소스 경로}? {query-string}

- **URI 체계**:  
URI를 요청을 보내는 데 사용 되는 프로토콜을 나타냅니다. 예를 들면 `http` 또는 `https`과 같습니다.
- **URI 호스트**:  
도메인 이름 또는 REST 서비스 끝점 호스팅되와 같은 서버의 IP 주소를 지정 하는 호스트 `graph.microsoft.com` 또는 `adminmanagement.local.azurestack.external`합니다.
- **리소스 경로**:  
리소스 또는 리소스 컬렉션을 결정 하는 데 해당 리소스를 선택 하는 서비스에서 사용 되는 여러 세그먼트를 포함할 수 있는 경로 지정 합니다. 예를 들어: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` 목록을 쿼리 하는 응용 프로그램 컬렉션 내에서 특정 응용 프로그램의 소유자를 사용할 수 있습니다.
- **쿼리 문자열**:  
문자열은 API 버전 또는 리소스 선택 조건과 같은 추가 단순 매개 변수를 제공합니다.

## <a name="azure-stack-request-uri-construct"></a>Azure 스택 요청 URI 구문

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI 구문

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>쿼리 URI 예제

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>다음 단계

RESTful Azure 끝점을 사용 하는 방법에 대 한 자세한 내용은 참조 [Azure REST API 참조](https://docs.microsoft.com/rest/api/)합니다.
