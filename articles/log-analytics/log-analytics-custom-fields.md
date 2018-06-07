---
title: "Azure Log Analytics의 사용자 지정 필드 | Microsoft Docs"
description: "Log Analytics의 사용자 지정 필드를 사용하면 수집된 레코드의 속성에 추가하는 Log Analytics 레코드에서 자체 검색 가능한 필드를 만들 수 있습니다.  이 문서는 사용자 지정 필드를 만드는 프로세스를 설명하고 샘플 이벤트에 대한 자세한 연습을 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: f0f3311f35f954f81560cad21e7f0e3bc850a094
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="custom-fields-in-log-analytics"></a>Log Analytics의 사용자 지정 필드
Log Analytics의 **사용자 지정 필드** 기능을 사용하면 자체적으로 검색 가능한 필드를 추가하여 Log Analytics의 기존 레코드를 확장할 수 있습니다.  사용자 지정 필드는 동일한 레코드의 다른 속성에서 추출한 데이터로 자동으로 채워집니다.

![사용자 지정 필드 개요](media/log-analytics-custom-fields/overview.png)

예를 들어, 아래 샘플 레코드에는 이벤트 설명에 파묻혀 있는 유용한 데이터가 있습니다.  이러한 데이터를 별도의 속성으로 추출하면 정렬 및 필터링 같은 작업에 사용할 수 있습니다.

![로그 검색 단추](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> 미리 보기에서는 작업 영역 내 사용자 지정 필드가 100개로 제한됩니다.  이러한 제한은 이 기능이 일반 공급이 될 때 확장됩니다.
> 
> 

## <a name="creating-a-custom-field"></a>사용자 지정 필드 만들기
사용자 지정 필드를 만드는 경우, Log Analytics는 값을 채우는 데 사용할 데이터를 이해해야 합니다.  Microsoft Research의 FlashExtract라는 기술을 사용하여 이러한 데이터를 신속하게 식별합니다.  Log Analytics는 명시적인 지침을 제공하도록 요구하지 않고, 사용자가 제공하는 예제에서 추출할 데이터에 대해 알아냅니다.

다음 섹션은 사용자 지정 필드를 만드는 절차를 제공합니다.  이 문서의 맨 아래에 샘플 추출 연습이 있습니다.

> [!NOTE]
> 사용자 지정 필드는 특정 조건에 일치하는 레코드가 Log Analytics에 추가되면 채워지기 때문에, 사용자 지정 필드가 생성된 후에 수집된 레코드에만 나타납니다.  사용자 지정 필드는 생성 당시 데이터 저장소에 이미 있었던 레코드에는 추가되지 않습니다.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1단계 – 사용자 지정 필드를 갖게 될 레코드를 식별합니다.
첫 번째 단계는 사용자 지정 필드를 갖게 될 레코드를 식별하는 것입니다.  [표준 로그 검색](log-analytics-log-searches.md) 부터 시작한 다음 Log Analytics가 학습하는 모델 역할을 할 레코드를 선택합니다.  사용자 지정 필드에 데이터를 추출할 것이라고 지정하면, **Field Extraction Wizard** (필드 추출 마법사)가 열리고 여기서 조건의 유효성을 검사하고 구체화합니다.

1. **로그 검색** 으로 이동하고 사용자 지정 필드를 갖게 될 [레코드를 검색할 쿼리](log-analytics-log-searches.md) 를 사용합니다.
2. Log Analytics가 사용자 지정 필드를 채울 데이터 추출을 위한 모델 역할을 하기 위해서 사용할 레코드를 선택합니다.  사용자는 이 레코드로부터 추출할 데이터를 식별하고, Log Analytics는 이 정보를 사용하여 유사한 모든 레코드의 사용자 지정 필드를 채울 논리를 결정합니다.
3. 레코드 텍스트 속성 왼쪽의 단추를 클릭하고 **Extract fields from**(다음에서 필드 추출)을 선택합니다.
4. **Field Extraction Wizard(필드 추출 마법사)가 열리고**, 선택한 레코드가 **Main Example**(기본 예제) 열에 표시됩니다.  사용자 지정 필드가, 선택한 속성에 동일한 값을 포함하는 레코드에 대해 정의됩니다.  
5. 선택 내용이 정확히 원하는 내용이 아니면, 추가적인 필드를 선택하여 조건을 좁혀 나갑니다.  조건에 대한 필드 값을 변경하기 위해서, 취소하고 원하는 조건에 맞는 다른 레코드를 선택해야 합니다.

### <a name="step-2---perform-initial-extract"></a>2단계 - 초기 추출을 수행합니다.
사용자 지정 필드를 갖게 될 레코드를 식별하고 나면, 추출할 데이터를 식별합니다.  Log Analytics는 이 정보를 사용하여 비슷한 레코드에서 유사한 패턴을 식별합니다.  이 단계가 지나면 결과의 유효성을 검사하고 분석에 사용할 Log Analytics에 대한 자세한 정보를 제공할 수 있게 됩니다.

1. 사용자 지정 필드를 채울 샘플 레코드에서 텍스트를 강조 표시합니다.  그러면 필드의 이름을 제공하고 초기 추출을 수행할 대화 상자가 표시됩니다.  **\_CF** 문자가 자동으로 추가됩니다.
2. **추출** 을 클릭하여 수집된 레코드에 대한 분석을 수행합니다.  
3. **요약** 및 **검색 결과** 섹션에 정확성을 검사할 수 있도록 추출 결과가 표시됩니다.  **요약** 에 식별된 각각의 데이터 값에 대한 개수 및 레코드를 식별하는 데 사용된 조건이 표시됩니다.  **검색 결과** 에 조건에 맞는 레코드의 자세한 목록이 제공됩니다.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3단계 – 사용자 지정 필드 만들기 및 추출의 정확성을 검사합니다.
초기 추출을 수행하면, Log Analytics에 이미 수집된 데이터에 기반한 결과가 표시됩니다.  결과가 정확해 보이면 더 이상 작업을 수행하지 않고 사용자 지정 필드를 만들 수 있습니다.  그렇지 않으면, Log Analytics의 논리를 개선할 수 있도록 결과를 구체화합니다.

1. 초기 추출에 정확하지 않은 값이 있으면, 정확하지 않은 레코드 옆의 **편집** 아이콘을 클릭하고 선택을 수정하기 위해서 **Modify this highlight**(이 강조 표시 수정)를 선택합니다.
2. 해당 항목이 **Main Example**(기본 예제) 아래 **Additional examples**(추가 예제) 섹션으로 복사됩니다.  여기에서 Log Analytics가 만들어야 하는 섹션을 이해할 수 있도록 강조 표시를 조정합니다.
3. **추출** 을 클릭하여 새로운 정보를 모든 기존 레코드를 평가하는 데 사용합니다.  결과는 새로운 인텔리전스를 기반으로 사용자가 수정한 레코드가 아닌 다른 레코드에 대해 수정될 수 있습니다.
4. 추출 내 모든 레코드가 새 사용자 지정 필드를 채울 데이터를 제대로 식별할 때까지 수정을 계속 추가합니다.
5. 결과에 만족하면 **Save Extract** (추출 저장)을 클릭합니다.  사용자 지정 필드가 정의되었지만 아직 레코드에는 추가되지 않았습니다.
6. 지정된 수집 조건에 일치하는 새 레코드를 기다린 다음 로그 검색을 다시 실행합니다. 새 레코드에 사용자 지정 필드가 생깁니다.
7. 사용자 지정 필드를 다른 레코드 속성처럼 사용합니다.  이것을 사용하여 데이터를 집계하고 그룹화하며 새로운 통찰을 생성하는 데도 사용할 수 있습니다.

## <a name="viewing-custom-fields"></a>사용자 지정 필드 보기
Azure Portal에서 Log Analytics 작업 영역의 **고급 설정** 메뉴에서 관리 그룹의 모든 사용자 지정 필드 목록을 볼 수 있습니다  **데이터**를 선택한 다음 **사용자 지정 필드**를 선택하여 작업 영역 내 모든 사용자 지정 필드의 목록을 표시합니다.  

![사용자 지정 필드](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>사용자 지정 필드 제거
사용자 지정 필드를 제거하는 방법은 두 가지입니다.  첫 번째는 위의 설명대로 전체 목록을 볼 때 각 필드의 **제거** 옵션입니다.  다른 방법은 레코드를 검색하고 필드 왼쪽의 단추를 클릭하는 것입니다.  메뉴에 사용자 지정 필드를 제거하는 옵션이 표시됩니다.

## <a name="sample-walkthrough"></a>샘플 연습
다음 섹션은 사용자 지정 필드를 만드는 전체 예제를 안내합니다.  이 예제는 서비스 변경 상태를 나타내는 Windows 이벤트의 서비스 이름을 추출합니다.  이것은 Windows 컴퓨터의 시스템 로그에서 서비스 제어 관리자에 의해 생성되는 이벤트에 의존합니다.  이 예제를 계속하려면, [시스템 로그에 대한 정보 이벤트를 수집](log-analytics-data-sources-windows-events.md)해야 합니다.

서비스 시작 또는 중지를 나타내는 이벤트인, 이벤트 ID가 7036인 서비스 제어 관리자의 모든 이벤트를 반환하기 위해서 다음 쿼리를 입력합니다.

![쿼리](media/log-analytics-custom-fields/query.png)

그 다음 이벤트 ID가 7036인 레코드를 아무 레코드나 선택합니다.

![소스 레코드](media/log-analytics-custom-fields/source-record.png)

**RenderedDescription** 속성에 표시되는 서비스 이름이 필요하며, 이 속성 옆의 단추를 선택합니다.

![추출 필드](media/log-analytics-custom-fields/extract-fields.png)

**Field Extraction Wizard**(필드 추출 마법사)가 열리고 **EventLog** 및 **EventID** 필드가 **Main Example**(기본 예제) 열에서 선택됩니다.  이것은 사용자 지정 필드가 이벤트 ID가 7036인 시스템 로그의 이벤트에 대해 정의된다는 것을 나타냅니다.  이것으로 충분하므로 다른 필드를 선택할 필요가 없습니다.

![Main Example](media/log-analytics-custom-fields/main-example.png)

**RenderedDescription** 속성에서 서비스의 이름을 강조 표시하고 **Service**를 사용하여 서비스 이름을 식별합니다.  사용자 지정 필드가 **Service_CF**라고 지정됩니다.

![필드 제목](media/log-analytics-custom-fields/field-title.png)

일부 레코드에 대해서는 서비스 이름이 적절하게 식별되었지만 나머지에 대해서는 그렇지 못합니다.   **검색 결과**에 **WMI Performance Adapter**의 이름 일부가 선택되지 않은 것이 표시됩니다.  **요약**에 **DPRMA** 서비스를 포함하는 네 개의 레코드에 추가적인 단어가 잘 못 포함되었고, 두 개의 레코드가 **Windows 모듈 설치 관리자**가 아닌 **모듈 설치 관리자**로 식별된 것이 표시됩니다.  

![검색 결과](media/log-analytics-custom-fields/search-results-01.png)

**WMI Performance Adapter** 레코드부터 시작합니다.  편집 아이콘을 클릭한 다음 **Modify this highlight**(이 강조 표시 수정)를 클릭합니다.  

![강조 표시 수정](media/log-analytics-custom-fields/modify-highlight.png)

**WMI**라는 단어를 포함하도록 강조 표시를 키운 다음 추출을 다시 실행합니다.  

![추가 예제](media/log-analytics-custom-fields/additional-example-01.png)

**WMI Performance Adapter**에 대한 항목이 수정되었고, Log Analytics 역시 해당 정보를 사용하여 **Windows모듈 설치 관리자**에 대한 레코드를 수정한 것을 볼 수 있습니다.  **요약** 섹션에서 **DPMRA**가 여전히 제대로 식별되지 않고 있는 것을 볼 수 있습니다.

![검색 결과](media/log-analytics-custom-fields/search-results-02.png)

DPMRA 서비스를 포함하는 레코드로 스크롤하고 동일한 프로세스를 사용하여 해당 레코드를 수정합니다.

![추가 예제](media/log-analytics-custom-fields/additional-example-02.png)

 추출을 실행하면 이제 모든 결과가 정확한 것을 볼 수 있습니다.

![검색 결과](media/log-analytics-custom-fields/search-results-03.png)

**Service_CF**가 생성되었지만 아직 어느 레코드에도 추가되지 않은 것을 볼 수 있습니다.

![초기 개수](media/log-analytics-custom-fields/initial-count.png)

새 이벤트가 수집될 만큼 시간이 지나면, **Service_CF** 필드가 조건에 일치하는 레코드에 추가되는 것을 볼 수 있습니다.

![최종 결과](media/log-analytics-custom-fields/final-results.png)

이제 사용자 지정 필드를 다른 레코드 속성처럼 사용할 수 있습니다.  이것을 설명하기 위해, 가장 활동적인 서비스가 무엇인지를 검사하는 새로운 **Service_CF** 필드로 그룹화하는 쿼리를 만듭니다.

![쿼리로 그룹화](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>다음 단계
* 조건에 대한 사용자 지정 필드를 사용하여 쿼리를 빌드하기 위해 [검색 로그](log-analytics-log-searches.md) 에 대해 알아봅니다.
* 사용자 지정 필드를 사용하여 구문 분석하는 [사용자 지정 로그 파일](log-analytics-data-sources-custom-logs.md)을 모니터링합니다.

