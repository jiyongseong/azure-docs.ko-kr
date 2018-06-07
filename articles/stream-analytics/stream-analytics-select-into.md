---
title: SELECT INTO를 사용하여 Azure Stream Analytics 쿼리 디버그
description: 이 문서에서는 쿼리 구문에서 SELECT INTO 문을 사용하여 Azure Stream Analytics 작업에서 데이터 중간 쿼리를 샘플링하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: ccaa6203e4bfe52758e26416646f9152ac5378ea
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>SELECT INTO 문을 사용하여 쿼리 디버그

실시간 데이터를 처리하는 동안 쿼리 중간에 데이터가 어떤 상태인지를 알면 유용할 수 있습니다. Azure Stream Analytics 작업의 입력 또는 단계를 여러 번 읽을 수 있기 때문에 추가 SELECT INTO 문을 작성할 수 있습니다. 이렇게 하면 프로그램을 디버그할 때 *watch 변수*가 동작하는 것처럼 중간 데이터가 저장소로 출력되어 데이터의 정확성을 검사할 수 있습니다.

## <a name="use-select-into-to-check-the-data-stream"></a>SELECT INTO를 사용하여 데이터 스트림 확인

Azure Stream Analytics 작업의 다음 예제 쿼리에는 Azure Table Storage에 대해 하나의 스트림 입력, 두 개의 참조 데이터 입력 및 출력이 있습니다. 쿼리는 이벤트 허브의 데이터와 두 개의 참조 Blob을 조인하여 이름 및 범주 정보를 가져옵니다.

![SELECT INTO 쿼리 예제](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

작업이 실행되고 있지만 이벤트는 출력에 생성되지 않습니다. 여기에 표시된 **모니터링** 타일에서 입력이 데이터를 생성하는 것을 확인할 수는 있지만 **JOIN**의 어떤 단계에서 모든 이벤트가 삭제되었는지는 알 수 없습니다.

![모니터링 타일](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
이 경우 몇 가지 추가 SELECT INTO 문을 추가하여 중간 JOIN 결과와 입력에서 읽은 데이터를 “로그”할 수 있습니다.

이 예제에서는 두 개의 새로운 “임시 출력”을 추가했습니다. 사용자가 원하는 어떠한 싱크도 될 수 있습니다. 여기서는 Azure Storage를 예로 사용합니다.

![추가 SELECT INTO 문 추가](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

그런 다음 다음과 같이 쿼리를 다시 작성할 수 있습니다.

![SELECT INTO 쿼리 다시 작성](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

이제 작업을 다시 시작하고 몇 분 동안 실행되도록 합니다. 그런 다음 Visual Studio Cloud Explorer를 통해 temp1 및 temp2를 쿼리하여 다음과 같은 테이블을 생성합니다.

**temp1 테이블**
![SELECT INTO temp1 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 테이블**
![SELECT INTO temp2 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

보시다시피 temp1 및 temp2에 모두 데이터가 있고 이름 열이 temp2에서 올바르게 채워집니다. 그러나 여전히 출력에 데이터가 없기 때문에 문제가 있습니다.

![데이터가 없는 SELECT INTO output1 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

데이터를 샘플링하면 두 번째 JOIN에서 문제가 있음을 거의 확신할 수 있습니다. Blob의 참조 데이터를 다운로드하고 살펴 볼 수 있습니다.

![SELECT INTO 참조 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

보시다시피 이 참조 데이터에서 GUID의 형식은 temp2의 열 형식과는 다릅니다. 데이터가 output1에 예상대로 도착하지 않았기 때문입니다.

데이터 형식을 수정하고, 참조 Blob에 업로드하고 다시 시도할 수 있습니다.

![SELECT INTO 임시 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

이번에는 출력 데이터가 예상대로 포맷되고 채워져 있습니다.

![SELECT INTO 최종 테이블](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

