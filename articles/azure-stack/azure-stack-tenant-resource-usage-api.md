---
title: "리소스 사용량 API 테 넌 트 | Microsoft Docs"
description: "Azure 스택 사용 정보를 검색 하는 리소스 사용량 API에 대 한 참조입니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: bc0b9993119342f07c28ed0384c11ae0f15bc439
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="tenant-resource-usage-api"></a>테 넌 트 리소스 사용량 API

테 넌 트가 테 넌 트 API를 사용 테 넌 트가 자체 리소스 사용 현황 데이터를 볼 수 있습니다. 이 API는 (현재 비공개 미리 보기)는에서 Azure 사용량 API와 일치 합니다.

Windows PowerShell cmdlet을 사용 하 여 **Get UsageAggregates** Azure에서와 같은 사용 현황 데이터를 가져올 수 있습니다.

## <a name="api-call"></a>API 호출
### <a name="request"></a>요청
요청 요청 된 구독에 대 한 및 요청 된 시간 프레임에 대 한 사용량 세부 정보를 가져옵니다. 요청 본문이 없습니다.

| **메서드** | **요청 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>인수
| **인수** | **설명** |
| --- | --- |
| *Armendpoint* |Azure 스택 환경을 azure 리소스 관리자 끝점입니다. Azure 스택 규칙은 Azure 리소스 관리자 끝점의 이름이 형식에서 `https://management.{domain-name}`합니다. 예를 들어 개발 키트에 대 한 도메인 이름 local.azurestack.external에 다음 리소스 관리자 끝점은 `https://management.local.azurestack.external`합니다. |
| *subId* |호출 하는 사용자의 구독 ID입니다. 단일 구독 사용에 대 한 쿼리에만이 API를 사용할 수 있습니다. 공급자 모든 테 넌 트에 대 한 쿼리 사용 하는 공급자 리소스 사용량 API를 사용할 수 있습니다. |
| *reportedStartTime* |쿼리를 시작 합니다. 에 대 한 값 *DateTime* utc에서 및 예를 들어 13시 시간 시작 부분에 있어야 합니다. 매일 집계에 대 한이 값을 UTC 자정으로 설정 합니다. 형식은 *이스케이프* ISO 8601, 예를 들어 2015-06-16T18% 3a53% 3a11% 2b00 %3a00Z, %3a 콜론은 이스케이프 하는 및와 uri를 %2b으로 이스케이프 합니다. |
| *reportedEndTime* |쿼리의 종료 시간입니다. 에 적용 되는 제약 조건은 *reportedStartTime* 이 인수에도 적용 합니다. 에 대 한 값 *reportedendtime의* 는 미래 날짜 일 수 없습니다. |
| *aggregationGranularity* |두 불연속 잠재적인 값을 포함 하는 선택적 매개 변수: 매일, 매시간 합니다. 값을 제안 하는 대로 매일 세분성의 데이터를 반환 하나 고 다른 하나는 시간별 해상도입니다. 일별 옵션이 기본 옵션입니다. |
| *api-version* |이 요청에 사용 되는 프로토콜의 버전입니다. 2015-06-01-미리 보기를 사용 해야 합니다. |
| *continuationToken* |사용량 API 공급자에 대 한 마지막 호출에서 토큰이 검색 합니다. 이 토큰 응답 줄 1, 000 보다 크면 하 고 역할을 진행률에 대 한 책갈피가 필요 합니다. 가 없으면 일의 시작 부분에서 데이터를 검색 또는 시간, 세분성에 따라 전달 합니다. |

### <a name="response"></a>response
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>응답 세부 정보
| **인수** | **설명** |
| --- | --- |
| *id* |사용 현황 집계의 고유 ID |
| *name* |사용 현황 집계의 이름 |
| *type* |리소스 정의 |
| *subscriptionId* |Azure 사용자의 구독 식별자입니다. |
| *usageStartTime* |UTC의 시작 시간이 사용 현황 집계가 속하는 사용 현황 버킷의 |
| *usageEndTime* |이 사용 현황 집계가 속하는 사용 현황 버킷의 UTC 종료 시간 |
| *instanceData* |인스턴스 세부 정보 (에 새 형식)의 키-값 쌍:<br>  *resourceUri*: 정규화 된 리소스 ID, 리소스 그룹 및 인스턴스 이름을 포함 하 여 <br>  *위치*:이 서비스를 실행 하는 영역 <br>  *태그*: 사용자 지정 하는 리소스 태그 <br>  *additionalInfo*: OS 버전 또는 이미지 유형을 예를 들어, 사용 된 리소스에 대 한 자세한 내용 |
| *quantity* |이 시간 내에 발생 한 리소스 소비를 |
| *meterId* |사용 된 리소스에 대 한 고유 ID (호출 또한 *ResourceID*) |


## <a name="next-steps"></a>다음 단계
[공급자 리소스 사용 API](azure-stack-provider-resource-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)

