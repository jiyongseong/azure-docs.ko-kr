---
title: "Azure API Management 정책에서 오류 처리 | Microsoft Docs"
description: "Azure API Management에서 요청을 처리하는 동안 발생할 수 있는 오류 조건에 대응하는 방법을 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2017
---
# <a name="error-handling-in-api-management-policies"></a>API Management 정책에서 오류 처리
Azure API Management를 통해 게시자는 `ProxyError` 개체를 제공하여 프록시에 대한 요청을 처리하는 동안 발생할 수 있는 오류 조건에 대응할 수 있습니다. `ProxyError` 개체는 [context.LastError](api-management-policy-expressions.md#ContextVariables) 속성을 통해 액세스하며 `on-error` 정책 섹션에서 정책에 의해 사용될 수 있습니다. 이 항목은 Azure API Management에서 오류 처리 기능에 대한 참조를 제공합니다.  
  
## <a name="error-handling-in-api-management"></a>API Management에서 오류 처리  
 Azure API Management의 정책은 다음 예에 표시된 것처럼 `inbound`, `backend`, `outbound` 및 `on-error` 섹션으로 나뉩니다.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 요청을 처리하는 동안 기본 제공된 단계는 요청에 대한 범위에 있는 정책과 함께 실행됩니다. 오류가 발생하는 경우 처리가 `on-error` 정책 섹션으로 즉시 이동합니다. `on-error` 정책 섹션은 모든 범위에서 사용할 수 있으며 API 게시자는 이벤트 허브에 오류 기록 또는 호출자에 반환할 새 응답 작성과 같은 사용자 지정 동작을 구성할 수 있습니다.  
  
> [!NOTE]
>  `on-error` 섹션은 기본적으로 정책에 나타나지 않습니다. `on-error` 섹션을 정책에 추가하려면 정책 편집기에서 원하는 정책을 찾아 추가합니다. 정책 구성에 대한 자세한 내용은 [API Management에서 정책](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)을 참조하세요.  
>   
>  `on-error` 섹션이 없는 경우 오류 조건이 발생하면 호출자는 400 또는 500 HTTP 응답 메시지를 수신하게 됩니다.  
  
### <a name="policies-allowed-in-on-error"></a>오류 발생 시 허용되는 정책  
 다음 정책을 `on-error` 정책 섹션에서 사용할 수 있습니다.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 오류가 발생하고 제어가 `on-error` 정책 섹션으로 이동하는 경우 오류가 `on-error` 섹션에 있는 정책에서 액세스할 수 있고 다음 속성을 포함하는 [context.LastError](api-management-policy-expressions.md#ContextVariables) 속성에 저장됩니다.  
  
|이름|형식|설명|필수|  
|----------|----------|-----------------|--------------|  
|원본|string|오류가 발생한 요소 이름을 지정합니다. 정책 또는 기본 제공 파이프라인 단계 이름일 수 있습니다.|예|  
|이유|string|오류 처리에 사용될 수 있는 컴퓨터에 익숙한 오류 코드입니다.|아니요|  
|Message|string|사람이 읽을 수 있는 오류 설명입니다.|예|  
|범위|string|오류가 발생한 범위 이름으로 "global", "product", "api" 또는 "operation" 중 하나일 수 있습니다.|아니요|  
|섹션|string|오류가 발생한 섹션 이름으로 "inbound", "backend", "outbound" 또는 "on-error" 중 하나일 수 있습니다.|아니요|  
|Path|string|중첩된 정책(예: "choose[3]/when[2]")을 지정합니다.|아니요|  
|PolicyId|string|오류가 발생한 정책에서 `id` 특성 값(고객이 지정한 경우)|아니요|  
  
> [!NOTE]
>  모든 정책에는 정책의 루트 요소에 추가할 수 있는 선택적 `id` 특성이 포함됩니다. 오류 조건 발생 시 정책이 이 특성이 있으면 `context.LastError.PolicyId` 속성을 사용하여 특성 값을 검색할 수 있습니다.  
  
## <a name="predefined-errors-for-built-in-steps"></a>기본 제공 단계에 대해 미리 정의된 오류  
 기본 제공 처리 단계를 평가하는 동안 발생할 수 있는 오류 조건에 대해 다음 오류가 미리 정의됩니다.  
  
|원본|조건|이유|Message|  
|------------|---------------|------------|-------------|  
|구성|Uri가 Api 또는 작업과 일치하지 않음|OperationNotFound|들어오는 요청을 작업과 일치시킬 수 없습니다.|  
|권한 부여|구독 키가 제공되지 않음|SubscriptionKeyNotFound|구독 키가 누락되어 액세스가 거부되었습니다. 이 API에 요청을 수행할 때 구독 키를 포함해야 합니다.|  
|권한 부여|구독 키 값이 잘못됨|SubscriptionKeyInvalid|잘못된 구독 키로 인해 액세스가 거부되었습니다. 활성 구독에 대해 유효한 키를 제공해야 합니다.|  
  
## <a name="predefined-errors-for-policies"></a>정책에 대해 미리 정의된 오류  
 정책 평가 중에 발생할 수 있는 오류 조건에 대해 다음 오류가 미리 정의됩니다.  
  
|원본|조건|이유|Message|  
|------------|---------------|------------|-------------|  
|rate-limit|속도 제한 초과|RateLimitExceeded|속도 제한을 초과했습니다.|  
|quota|할당량이 초과됨|QuotaExceeded|호출 볼륨 할당량을 초과했습니다. 할당량이 xx:xx:xx에서 보충됩니다. 또는 대역폭 할당량을 초과했습니다. 할당량이 xx:xx:xx에서 보충됩니다.|  
|jsonp|콜백 매개 변수 값이 잘못되었습니다(잘못된 문자 포함).|CallbackParameterInvalid|콜백 매개 변수 {callback-parameter-name} 값이 올바른 JavaScript 식별자가 아닙니다.|  
|ip-filter|요청에서 호출자 IP를 구문 분석하지 못했음|FailedToParseCallerIP|호출자에 대한 IP 주소를 설정하지 못했습니다. 액세스가 거부되었습니다.|  
|ip-filter|호출자 IP가 허용 목록에 없음|CallerIpNotAllowed|호출자 IP 주소 {ip-address}이(가) 허용되지 않습니다. 액세스가 거부되었습니다.|  
|ip-filter|호출자 IP가 차단 목록에 있음|CallerIpBlocked|호출자 IP 주소가 차단되었습니다. 액세스가 거부되었습니다.|  
|check-header|필수 헤더가 없거나 값이 누락됨|HeaderNotFound|헤더 {header-name}이(가) 요청에 없습니다. 액세스가 거부되었습니다.|  
|check-header|필수 헤더가 없거나 값이 누락됨|HeaderValueNotAllowed|{header-value}의 헤더 {header-name} 값이 허용되지 않습니다. 액세스가 거부되었습니다.|  
|validate-jwt|요청에 Jwt 토큰이 없음|TokenNotFound|JWT가 요청에 없습니다. 액세스가 거부되었습니다.|  
|validate-jwt|서명 유효성 검사에 실패|TokenSignatureInvalid|<jwt 라이브러리의 메시지\>. 액세스가 거부되었습니다.|  
|validate-jwt|잘못된 대상 그룹|TokenAudienceNotAllowed|<jwt 라이브러리의 메시지\>. 액세스가 거부되었습니다.|  
|validate-jwt|잘못된 발급자|TokenIssuerNotAllowed|<jwt 라이브러리의 메시지\>. 액세스가 거부되었습니다.|  
|validate-jwt|토큰 만료됨|TokenExpired|<jwt 라이브러리의 메시지\>. 액세스가 거부되었습니다.|  
|validate-jwt|서명 키가 ID로 확인되지 않았음|TokenSignatureKeyNotFound|<jwt 라이브러리의 메시지\>. 액세스가 거부되었습니다.|  
|validate-jwt|토큰에서 필수 클레임이 누락됨|TokenClaimNotFound|JWT 토큰에 다음 클레임이 누락됨: <c1\>, <c2\>, … 액세스가 거부되었습니다.|  
|validate-jwt|클레임 값이 일치하지 않음|TokenClaimValueNotAllowed|{claim-value}의 클레임 {claim-name} 값이 허용되지 않습니다. 액세스가 거부되었습니다.|  
|validate-jwt|기타 유효성 검사 실패|JwtInvalid|<jwt 라이브러리의 메시지\>|

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ 정책 명령문 및 설정의 전체 목록에 대한 [정책 참조](api-management-policy-reference.md)
+ [정책 샘플](policy-samples.md)   