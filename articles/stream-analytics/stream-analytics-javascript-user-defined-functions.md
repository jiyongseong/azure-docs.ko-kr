---
title: '자습서: Azure Stream Analytics JavaScript 사용자 정의 함수 | Microsoft Docs '
description: 이 자습서에서는 JavaScript 사용자 정의 함수로 고급 쿼리 메커니즘을 수행합니다.
keywords: javascript, 사용자 정의 함수, udf
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: jasonh
ms.custom: mvc
ms.date: 04/01/2018
ms.workload: data-services
ms.author: sngun
ms.openlocfilehash: f3a94017b95eb614669fa42594fe3a3499c74be7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>자습서: Azure Stream Analytics JavaScript 사용자 정의 함수

Azure Stream Analytics에서는 JavaScript로 작성된 사용자 정의 함수를 지원합니다. JavaScript에서 제공하는 풍부한 메서드 집합(**String**, **RegExp**, **Math**, **Array**, **Date**)을 통해 Stream Analytics 작업에서 복잡한 데이터 변환을 쉽게 만들 수 있게 되었습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * JavaScript 사용자 정의 함수 정의
> * 포털에 함수 추가
> * 함수를 실행하는 쿼리 정의

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="javascript-user-defined-functions"></a>JavaScript 사용자 정의 함수
JavaScript 사용자 정의 함수는 외부 연결이 필요 없는 상태 비저장, 계산 전용 스칼라 함수를 지원합니다. 함수의 반환 값은 스칼라(단일) 값만 될 수 있습니다. JavaScript 사용자 정의 함수를 작업에 추가한 후 기본 제공 스칼라 함수처럼 쿼리의 아무 곳에서나 함수를 사용할 수 있습니다.

다음은 JavaScript 사용자 정의 함수가 유용할 수 있는 몇 가지 시나리오입니다.
* 정규식 함수를 가진 문자열을 구문 분석 및 조작(예: **Regexp_Replace()** 및 **Regexp_Extract()**)
* 데이터 디코딩 및 인코딩(예: 2진을 16진으로 변환)
* JavaScript **Math** 함수로 수학 계산 수행
* 정렬, 조인, 찾기 및 채우기 등의 배열 작업 수행

다음은 Stream Analytics에서 JavaScript 사용자 정의 함수로 수행할 수 없는 작업입니다.
* 외부 REST 끝점 호출(예: 역방향 IP 조회 수행 또는 외부 원본에서 참조 데이터 끌어오기)
* 입력/출력에서 사용자 지정 이벤트 형식 직렬화 또는 역직렬화 수행
* 사용자 지정 집계 만들기

함수 정의에서 차단되지는 않지만 **Date.GetDate()** 또는 **Math.random()** 과 같은 함수는 사용하지 않아야 합니다. 이러한 함수는 호출할 때마다 **다른** 결과를 반환하며 Azure Stream Analytics 서비스에서 함수 호출 및 반환된 결과 저널을 유지하지 않습니다. 동일한 이벤트에 대해 함수가 다른 결과를 반환하면 사용자 또는 Stream Analytics 서비스에서 작업을 다시 시작할 때 반복성이 보장되지 않습니다.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Azure Portal에서 JavaScript 사용자 정의 함수 추가
기존 Stream Analytics 작업에서 간단한 JavaScript 사용자 정의 함수를 작성하려면 다음 단계를 수행하세요.

1.  Azure Portal에서 Stream Analytics 작업을 찾습니다.
2.  **작업 토폴로지** 아래에서 함수를 선택합니다. 함수의 빈 목록이 표시됩니다.
3.  새 사용자 정의 함수를 만들려면 **추가**를 선택합니다.
4.  **새 함수** 블레이드에서 **함수 유형**에 대해 **JavaScript**를 선택합니다. 기본 함수 템플릿이 편집기에 나타납니다.
5.  **UDF 별칭**의 경우 **hex2Int**를 입력하고 함수 구현을 다음과 같이 변경합니다.

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  **저장**을 선택합니다. 함수가 함수의 목록에 나타납니다.
7.  새 **hex2Int** 함수를 선택하고 함수 정의를 확인합니다. 모든 함수는 함수 별칭에 **UDF** 접두사가 추가됩니다. Stream Analytics 쿼리에서 함수를 호출할 때 *접두사를 포함*해야 합니다. 이 경우 **UDF.hex2Int**를 호출합니다.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>쿼리에서 JavaScript 사용자 정의 함수 호출

1. 쿼리 편집기에서 **작업 토폴로지** 아래에 있는 **쿼리**를 선택합니다.
2.  쿼리에 편집하고 다음과 같은 사용자 정의 함수를 호출합니다.

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  샘플 데이터 파일 업로드하려면 작업 입력을 마우스 오른쪽 단추로 클릭합니다.
4.  쿼리를 테스트하려면 **테스트**를 선택합니다.


## <a name="supported-javascript-objects"></a>지원되는 JavaScript 개체
Azure Stream Analytics JavaScript 사용자 정의 함수는 표준인 기본 제공 JavaScript 개체를 지원합니다. 이러한 개체의 목록은 [전역 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)를 참조하세요.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics 및 JavaScript 형식 변환

Stream Analytics 쿼리 언어와 JavaScript가 지원하는 형식 간에는 차이가 있습니다. 이 테이블에는 둘 간의 변환 매핑 목록이 나열되어 있습니다.

Stream Analytics | JavaScript
--- | ---
bigint | Number(JavaScript에서는 정확히 최대 2^53의 정수만 표현할 수 있음)
Datetime | Date(JavaScript에서는 밀리초만 지원)
double | Number
nvarchar(MAX) | 문자열
레코드 | Object
배열 | 배열
NULL | Null


다음은 JavaScript-Stream Analytics 변환입니다.


JavaScript | Stream Analytics
--- | ---
Number | Bigint(숫자를 반올림하여 long.MinValue와 long.MaxValue 사이에 있는 경우, 그렇지 않으면 double임)
Date | Datetime
문자열 | nvarchar(MAX)
Object | 레코드
배열 | 배열
Null, Undefined | NULL
기타 다른 형식(예: 함수 또는 오류) | 지원되지 않음(런타임 오류 발생)

## <a name="troubleshooting"></a>문제 해결
JavaScript 런타임 오류는 치명적인 것으로 간주되고 활동 로그를 통해 표시됩니다. Azure Portal에서 로그를 검색하려면 작업으로 이동하고 **활동 로그**를 선택합니다.


## <a name="other-javascript-user-defined-function-patterns"></a>기타 JavaScript 사용자 정의 함수 패턴

### <a name="write-nested-json-to-output"></a>중첩된 JSON을 출력에 작성
Stream Analytics 작업 출력을 입력으로 사용하는 후속 처리 단계가 있고 이것이 JSON 형식을 요구하는 경우 JSON 문자열을 출력에 작성할 수 있습니다. 다음 예에서는 **JSON.stringify()** 함수를 호출하여 입력의 모든 이름/값 쌍을 묶고 출력에 단일 문자열 값으로 작성합니다.

**JavaScript 사용자 정의 함수 정의:**

```
function main(x) {
return JSON.stringify(x);
}
```

**샘플 쿼리:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 필요할 때 나중에 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음 단계를 사용하여 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다.  
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="get-help"></a>도움말 보기
추가 도움이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 JavaScript 사용자 정의 함수를 실행하는 Stream Analytics 작업을 만들었습니다. Stream Analytics에 대해 자세히 알아보려면 실시간 시나리오 문서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Stream Analytics에서 실시간 Twitter 감정 분석](stream-analytics-twitter-sentiment-analysis-trends.md)
