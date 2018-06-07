---
title: Azure Search에서 인식 검색에 대한 문제 해결 팁 | Microsoft Docs
description: Azure Search에서 인식 설정 파이프라인을 설정하기 위한 문제 해결 및 팁입니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 15fc879958bfd886210a90239e0247c60fe231f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786832"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>인식 검색을 위한 문제 해결 팁

이 문서에는 Azure Search에서 인식 검색 기능을 시작할 때 감동시키는 팁과 요령 목록이 포함돼 있습니다. 

아직 그렇게 하지 않은 경우 BLOB 데이터 원본에 인식 검색 보강을 적용하려면 [자습서: 인식 검색 API를 호출하는 방법 알아보기](cognitive-search-quickstart-blob.md)를 단계별로 실행합니다.

## <a name="tip-1-start-with-a-small-dataset"></a>팁 1: 작은 데이터 집합으로 시작
문제를 신속하게 찾는 최상의 방법은 문제를 해결할 수 있는 속도를 높이는 것입니다. 인덱싱 시간을 줄이는 최상의 방법은 인덱싱할 문서 수를 줄이는 것입니다. 

약간의 문서/레코드를 사용하여 데이터 원본을 만들어 시작합니다. 문서 샘플에는 인덱싱할 다양한 문서가 잘 표현되어 있어야 합니다. 

통합형 파이프라인을 통해 문서 샘플을 실행하고 결과가 사용자의 요구를 충족하는지 확인합니다. 결과에 만족하면 데이터 원본에 더 많은 파일을 추가할 수 있습니다.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>팁 2: 데이터 원본 자격 증명이 올바른지 확인
데이터 원본 연결을 사용하는 인덱서를 정의하기까지 연결의 유효성은 검사되지 않습니다. 인덱서가 데이터에 액세스할 수 없다는 오류가 표시되면 다음을 확인합니다.
- 연결 문자열이 올바릅니다. 특히 SAS 토큰을 만드는 경우 Azure Search에서 예상한 형식을 사용하는지 확인합니다. 지원되는 다른 형식에 대해 알려면 [자격 증명을 지정하는 방법 섹션](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)을 참조합니다.
- 인덱서의 사용자 컨테이너 이름은 올바릅니다.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>팁 3: 일부 오류가 있는 경우라도 작동 조건 참조
경우에 따라 작은 오류가 해당 트랙에서 인덱서를 중지합니다. 차례로 문제를 해결하려는 경우 괜찮습니다. 그러나 특정 유형의 오류를 무시하고 어떤 흐름이 실제로 작동하는지 확인할 수 있도록 인덱서를 계속 허용하려 할 수 있습니다.

이 경우 인덱서에게 오류를 무시하라고 명령하고 싶을 수 있습니다. *maxFailedItems* 및 *maxFailedItemsPerBatch*를 -1로 인덱서 정의의 일부로 설정하여 그렇게 합니다.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>팁 4: 내부적으로 보강된 문서 살펴보기 
보강된 문서는 보강 중에 만들어진 임시 구조체로, 프로세싱이 완료되면 삭제됩니다.

인덱싱 중에 만들어진 보강된 문서의 스냅숏을 캡처하려면 ```enriched```라는 필드를 인덱스에 추가합니다. 인덱서는 해당 문서의 모든 보강 문자열 표현에 대한 필드에 자동으로 덤프됩니다.

```enriched``` 필드는 JSON으로 보강된 메모리 내 문서의 논리적 표현인 문자열을 포함하게 됩니다.  그러나 필드 값은 유효한 JSON 문서입니다. 따옴표가 이스케이프되므로 문서를 JSON 형식으로 보려면 `\"`를 `"`로 바꿔야 합니다. 

보강된 필드는 디버깅용이며, 식을 평가하는 콘텐츠의 논리적 형식을 쉽게 이해하도록 도와주는 역할만 합니다. 인덱싱 목적으로 이 필드에 의존해서는 안 됩니다.

디버깅 목적에서 인덱스 정의의 일부로 ```enriched``` 필드를 추가합니다.

#### <a name="request-body-syntax"></a>요청 본문 구문
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>팁 5: 예상 콘텐츠가 표시되지 않음

누락된 콘텐츠는 인덱싱 동안 삭제되는 문서의 결과물일 수 있습니다. 무료 및 기본 계층은 문서 크기에 대한 제한이 낮습니다. 제한을 초과하는 모든 파일은 인덱싱 동안 삭제됩니다. Azure Portal에서 삭제된 문서를 확인할 수 있습니다. 검색 서비스 대시보드에서 인덱서 타일을 두 번 클릭합니다. 성공적으로 인덱싱된 문서의 비율을 검토합니다. 100%가 아닌 경우 자세한 내용을 보려면 비율을 클릭하면 됩니다. 

문제가 파일 크기와 관련된 경우 다음과 같은 오류가 표시될 수 있습니다. "BLOB <file-name>은 현재 서비스 계층에 대해 문서 추출을 위한 최대 크기를 초과하는 <file-size> 바이트 크기입니다." 인덱서 제한에 대한 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

콘텐츠를 표시하는 데 실패한 두 번째 이유는 관련된 입/출력 매핑 오류일 수 있습니다. 예를 들어 출력 대상 이름은 "People"이지만 인덱스 필드 이름은 소문자 "people"입니다. 실제로 필드가 비어있을 때 인덱싱이 성공한 것으로 생각하도록 시스템이 전체 파이프라인에 대한 201 성공 메시지를 반환할 수 있습니다. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>팁 6: 최대 실행 시간(24시간 범위)을 초월하여 처리 확장

이미지 분석은 단순한 사례라도 계산 집약적이므로 이미지가 특히 크거나 복잡한 경우 처리 시간이 최대 허용 시간을 초과할 수 있습니다. 

최대 실행 시간은 무료 계층에는 몇 분 인덱싱, 청구 가능 계층에는 24시간 인덱싱 등 계층에 따라 다릅니다. 요청 시 처리를 위해 처리를 24시간 내에 완료하지 못하면 인덱서가 중단된 경우 처리를 선택할 수 있도록 일정을 전환합니다. 

예약된 인덱서의 경우 인덱싱은 마지막으로 알려진 좋은 문서에서 일정에 따라 다시 시작합니다. 처리되지 않은 이미지를 모두 처리할 때까지, 인덱서는 반복 일정을 사용하여 일련의 시간 또는 일이 지나는 동안 이미지 백로그를 통해 자기 방식대로 작동할 수 있습니다. 일정 구문에 대한 자세한 내용은 [3단계: 인덱서 만들기](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer)를 참조합니다.

포털 기반 인덱싱의 경우(빠른 시작에서 설명한 대로) "한 번 실행" 인덱서 옵션을 선택하면 처리 시간을 1시간(`"maxRunTime": "PT1H"`)으로 제한합니다. 처리 창을 조금 더 길게 확장하고 싶을 수 있습니다.

## <a name="tip-7-increase-indexing-throughput"></a>팁 7: 인덱싱 처리량 증가

[병렬 인덱싱](search-howto-reindex.md#parallel-indexing)의 경우 여러 컨테이너에 데이터를 또는 동일한 컨테이너 내부에 여러 가상 폴더를 저장합니다. 그런 다음, 여러 데이터 원본 및 인덱서 쌍을 만듭니다. 모든 인덱서는 동일한 기술 집합을 사용하며 동일한 대상 검색 인덱스에 쓸 수 있으므로 검색 앱이 이 분할을 알 필요가 없습니다.
자세한 내용은 [큰 데이터 집합 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)을 참조합니다.

## <a name="see-also"></a>참고 항목
+ [빠른 시작: 포털에서 인식 검색 파이프라인 만들기](cognitive-search-quickstart-blob.md)
+ [자습서: 인식 검색 REST API 알아보기](cognitive-search-tutorial-blob.md)
+ [데이터 원본 자격 증명 지정](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [큰 데이터 집합 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱스에 보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)