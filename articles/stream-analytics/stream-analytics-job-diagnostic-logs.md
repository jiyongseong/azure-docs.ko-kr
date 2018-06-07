---
title: 진단 로그를 사용하여 Azure Stream Analytics 문제 해결
description: 이 문서에서는 Azure Stream Analytics에서 진단 로그를 분석하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 9001a2962806ee3e691fa448dde162d12c6ecdd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>진단 로그를 사용하여 Azure Stream Analytics 문제 해결

경우에 따라 Azure Stream Analytics 작업이 예기치 않게 처리를 중지합니다. 이 이벤트 종류의 문제를 해결하는 것이 중요합니다. 이벤트는 예기치 않은 쿼리 결과, 장치에 대한 연결 또는 예기치 않은 서비스 중단으로 인해 발생할 수 있습니다. Stream Analytics에서 진단 로그를 사용하면 이와 같은 이벤트가 발생했을 때 문제 원인을 파악하고 복구 시간을 단축할 수 있습니다.

## <a name="log-types"></a>로그 형식

Stream Analytics에서는 다음과 같은 두 가지 형식의 로그를 제공합니다. 
* [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)(항상 켜짐). 활동 로그는 작업을 수행하는 작업에 대한 통찰력을 제공합니다.
* [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)(구성 가능). 진단 로그는 작업과 관련하여 발생하는 모든 항목에 대해 더 다양한 통찰력을 제공합니다. 진단 작업은 작업을 생성할 때 시작되고, 작업을 삭제할 때 종료됩니다. 작업이 업데이트되고 실행되는 동안 이벤트를 처리합니다.

> [!NOTE]
> Azure Storage, Azure Event Hubs 및 Azure Log Analytics와 같은 서비스를 사용하여 맞지 않는 데이터를 분석할 수 있습니다. 그러한 서비스에 대한 가격 책정 모델에 따라 요금이 청구됩니다.
>

## <a name="turn-on-diagnostics-logs"></a>진단 로그 켜기

진단 로그는 기본적으로 **해제**되어 있습니다. 진단 로그를 켜려면 다음과 같은 단계를 완료합니다.

1.  Azure Portal에 로그인하고 스트리밍 작업 블레이드로 이동합니다. **모니터링** 아래에서 **진단 로그**를 선택합니다.

    ![진단 로그에 대한 블레이드 탐색](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  **진단 켜기**를 선택합니다.

    ![진단 로그 켜기](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  **진단 설정** 페이지의 **상태**에서 **켜기**를 선택합니다.

    ![진단 로그에 대한 상태 변경](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  원하는 보관 대상(저장소 계정, 이벤트 허브, Log Analytics)을 설정합니다. 그런 다음, 수집할 로그의 범주를 선택합니다(실행, 작성). 

5.  새 진단 구성을 저장합니다.

진단 구성이 적용되는 데 약 10분이 소요됩니다. 그 후 구성된 보관 대상에 로그가 나타나기 시작합니다(**진단 로그** 페이지에서 볼 수 있음).

![진단 로그에 대한 블레이드 탐색 - 보관 대상](./media/stream-analytics-job-diagnostic-logs/image4.png)

진단을 구성하는 방법에 대한 자세한 내용은 [Azure 리소스의 진단 데이터 수집 및 사용](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)을 참조하세요.

## <a name="diagnostics-log-categories"></a>진단 로그 범주

현재 두 가지 진단 로그 범주를 캡처합니다.

* **작성**. 작업 생성, 입력 및 출력 추가 및 삭제, 쿼리 추가 및 업데이트, 작업 시작 및 중지 등 작업 작성 작업과 관련된 로그 이벤트를 캡처합니다.
* **실행**. 작업 실행 중 발생하는 이벤트를 캡처합니다.
    * 연결 오류
    * 다음을 포함하는 데이터 처리 오류
        * 쿼리 정의를 따르지 않는 이벤트(필드 형식 및 값 불일치, 필드 누락 등)
        * 식 평가 오류
    * 다른 이벤트 및 오류

## <a name="diagnostics-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON 형식으로 저장됩니다. 각 항목에는 다음과 같은 일반적인 문자열 필드가 있습니다.

Name | 설명
------- | -------
실시간 | 로그의 타임스탬프(UTC)입니다.
ResourceId | 작업이 수행되는 리소스의 ID(대문자)입니다. 여기에는 구독 ID, 리소스 그룹 및 작업 이름이 포함됩니다. 예: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
카테고리 | 로그 범주로, **실행** 또는 **작성** 중 하나입니다.
operationName | 기록된 작업의 이름 예: **이벤트 전송: mysqloutput에 대한 SQL 출력 쓰기 실패**.
status | 작업의 상태입니다. 예: **실패** 또는 **성공**.
level | 로그 수준. 예: **오류**, **경고** 또는 **정보**.
properties | 로그 항목별 세부 정보로, JSON 문자열로 직렬화됩니다. 자세한 내용은 다음 섹션을 참조하세요.

### <a name="execution-log-properties-schema"></a>실행 로그 속성 스키마

실행 로그에는 Stream Analytics 작업 실행 중에 발생한 이벤트에 대한 정보가 포함됩니다. 속성의 스키마는 이벤트의 형식에 따라 달라집니다. 현재 실행 로그의 형식은 다음과 같습니다.

### <a name="data-errors"></a>데이터 오류

작업이 데이터를 처리하는 동안 발생한 오류는 이 로그 범주에 속합니다. 이러한 로그는 데이터 읽기, serialization 및 쓰기 작업 도중에 가장 자주 생성됩니다. 이러한 로그는 연결 오류를 포함하지 않습니다. 연결 오류는 일반 이벤트로 처리됩니다.

Name | 설명
------- | -------
원본 | 오류가 발생한 작업 입력 또는 출력의 이름입니다.
Message | 오류와 연결된 메시지
유형 | 오류의 형식입니다. 예: **DataConversionError**, **CsvParserError** 또는 **ServiceBusPropertyColumnMissingError**.
Data | 오류 출처를 정확히 찾는 데 도움이 되는 데이터를 포함합니다. 크기에 따라 잘릴 수 있습니다.

**operationName** 값에 따라 데이터 오류의 스키마는 다음과 같습니다.
* **직렬화 이벤트**입니다. 직렬화 이벤트는 이벤트 읽기 작업 중에 발생합니다. 이는 데이터 입력 시 쿼리 스키마를 충족하지 않을 때 다음과 같은 이유 중 하나로 발생합니다.
    * *이벤트 (역)직렬화 도중 형식 불일치*: 오류를 발생시키는 필드를 식별합니다.
    * *이벤트를 읽을 수 없음, 잘못된 serialization*: 입력 데이터에서 오류가 발생하는 위치에 대한 정보를 나열합니다. Blob 입력에 대한 Blob 이름, 오프셋 및 데이터 샘플이 포함됩니다.
* **전송 이벤트**. 전송 이벤트는 쓰기 작업 중에 발생합니다. 오류를 발생시키는 스트리밍 이벤트를 식별합니다.

### <a name="generic-events"></a>일반 이벤트

일반 이벤트는 다른 모든 항목을 처리합니다.

Name | 설명
-------- | --------
오류 | (선택 사항) 오류 정보입니다. 일반적으로 사용 가능한 경우 예외 정보입니다.
Message| 로그 메시지
유형 | 메시지 형식입니다. 내부 오류 분류에 매핑합니다. 예: **JobValidationError** 또는 **BlobOutputAdapterInitializationFailure**.
상관관계 ID | 작업 실행을 고유하게 식별하는 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). 작업 시작 시간부터 작업이 중지될 때까지 모든 실행 로그 항목에는 동일한 **상관 관계 ID** 값이 있습니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
