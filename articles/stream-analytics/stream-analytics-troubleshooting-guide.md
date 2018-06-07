---
title: Azure Stream Analytics 문제 해결 가이드
description: 이 문서에서는 Azure Stream Analytics 작업, 연결, 입력, 출력, 쿼리 및 데이터 문제를 해결하는 기술에 대해 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 44777946fdc829da222ffdd67dfecfa3bf240be7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Azure Stream Analytics 문제 해결 가이드

Azure Stream Analytics 문제 해결은 언뜻 보기에 복잡한 노력이 필요한 것처럼 보일 수 있습니다. 여러 사용자가 작업을 한 후에 프로세스를 간소화하고 입력, 출력, 쿼리 및 함수에 대한 추측을 제거하는 데 도움이 되도록 이 가이드를 만들었습니다.

## <a name="troubleshoot-your-stream-analytics-job"></a>Stream Analytics 작업 문제 해결

Stream Analytics 작업 문제 해결에서 최상의 결과를 얻으려면 다음 지침을 따르세요.

1.  연결을 테스트합니다.
    - 각 입력 및 출력에 대해 **테스트 연결** 단추를 사용하여 입력 및 출력에 대한 연결을 확인합니다.

2.  입력된 데이터를 검사합니다.
    - 해당 입력 데이터가 이벤트 허브로 전달되고 있는지 확인하려면 [Service Bus 탐색기](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)를 사용하여 Azure Event Hub(이벤트 허브 입력이 사용되는 경우)에 연결합니다.  
    - 각 입력에 대해 [**샘플 데이터**](stream-analytics-sample-data-input.md) 단추를 사용하고 입력된 샘플 데이터를 다운로드합니다.
    - 샘플 데이터를 검사하여 데이터의 셰이프(스키마 및 [데이터 형식](https://msdn.microsoft.com/library/azure/dn835065.aspx))를 파악합니다.

3.  쿼리를 테스트합니다.
    - **쿼리** 탭에서 **테스트** 단추를 사용하여 쿼리를 테스트하고 다운로드한 샘플 데이터를 사용하여 [쿼리를 테스트](stream-analytics-test-query.md)합니다. 모든 오류를 검사하고 수정합니다.
    - [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx)를 사용하는 경우 이벤트에 [작업 시작 시간](stream-analytics-out-of-order-and-late-events.md)보다 큰 타임스탬프가 있는지 확인합니다.

4.  쿼리를 디버그합니다.
    - 단계를 통해 간단한 select 문에서 좀 더 복잡한 집계까지 점진적으로 쿼리를 다시 만듭니다. 단계별로 쿼리 논리를 작성하려면 [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) 절을 사용합니다.
    - [SELECT INTO](stream-analytics-select-into.md)를 사용하여 쿼리 단계를 디버그합니다.

5.  다음과 같은 공통 문제를 제거합니다.
    - 쿼리의 [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) 절은 출력이 생성되지 않도록 방지하는 모든 이벤트를 필터링했습니다.
    - 창 함수를 사용하는 경우 전체 창 기간을 기다려서 쿼리의 출력을 확인합니다.
    - 작업 시작 시간 전에 이벤트에 대한 타임스탬프가 있으므로 이벤트가 손실됩니다.

6.  이벤트 순서를 사용합니다.
    - 이전 단계가 모두 정상적으로 작동하는 경우 **설정** 블레이드로 이동하여 [**이벤트 순서**](stream-analytics-out-of-order-and-late-events.md)를 선택합니다. 이 정책이 사용자 시나리오에 적합하게 구성되어 있는지 확인합니다. 정책은 **테스트** 단추를 사용하여 쿼리를 테스트하는 경우 적용되지 *않습니다*. 이것이 브라우저에서 테스트할 때와 프로덕션의 작업을 실행할 때의 차이입니다.

7.  메트릭을 사용하여 디버그합니다.
    - 예상된 기간(쿼리 기준) 후 획득된 출력이 없는 경우 다음을 시도합니다.
        - **모니터** 탭에서 [**모니터링 메트릭**](stream-analytics-monitoring.md)을 확인합니다. 값을 집계하기 때문에 메트릭이 몇 분 동안 지연됩니다.
            - 입력 이벤트가 0보다 큰 경우, 작업은 입력 데이터를 읽을 수 있습니다. 입력 이벤트가 0보다 크지 않으면 다음을 수행합니다.
                - 데이터 원본에 유효한 데이터가 있는지 확인하려면 [Service Bus 탐색기](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)를 사용하여 확인합니다. 이 확인은 작업이 입력으로 이벤트 허브를 사용하는 경우 적용됩니다.
                - 데이터 serialization 형식과 데이터 인코딩이 예상대로인지 확인합니다.
                - 작업이 이벤트 허브를 사용하는 경우 메시지의 본문이 *Null*인지 확인합니다.
            - 데이터 변환 오류가 0보다 크며 증가하는 경우 다음을 의미할 수 있습니다.
                - 작업이 이벤트를 역직렬화하지 못할 수도 있습니다.
                - 이벤트 스키마가 쿼리에서 이벤트의 정의 또는 예상 스키마와 일치하지 않을 수도 있습니다.
                - 이벤트에서 일부 필드의 데이터 형식이 예상과 일치하지 않을 수도 있습니다.
            - 런타임 오류가 0보다 큰 경우 작업이 데이터를 수신할 수 있지만 쿼리를 처리하는 동안 오류가 발생함을 의미합니다.
                - 오류를 찾으려면 [감사 로그](../azure-resource-manager/resource-group-audit.md)로 이동하여 *실패* 상태를 필터링합니다.
            - InputEvents가 0보다 크고 OutputEvents가 0인 경우 다음 중 하나를 의미합니다.
                - 쿼리 처리로 제로 출력 이벤트가 발생했습니다.
                - 이벤트 또는 그 필드가 잘못되어 쿼리 처리 후 제로 출력이 발생할 수 있습니다.
                - 작업이 연결 또는 인증 이유로 데이터를 [출력 싱크](stream-analytics-select-into.md)에 푸시할 수 없습니다.
        - 이전에 언급한 모든 오류 사례에서 작업 로그 메시지는 쿼리 논리가 모든 이벤트를 필터링하는 경우 외에 추가 정보(발생하는 상황 포함)를 설명합니다. 여러 이벤트 처리에서 오류가 발생하면 Stream Analytics는 작업 로그에 10분 이내 동일한 형식의 첫 세 개의 오류 메시지를 기록합니다. 그런 다음 “오류가 너무 빠르게 발생하고 억제되고 있습니다”라는 메시지로 동일한 추가 오류를 표시하지 않습니다.

8. 감사 및 진단 로그를 사용하여 디버그합니다.
    - [감사 로그](../azure-resource-manager/resource-group-audit.md)를 사용하고 필터링하여 오류를 식별하고 디버그합니다.
    - [작업 진단 로그](stream-analytics-job-diagnostic-logs.md)를 사용하여 오류를 식별하고 디버그합니다.

9. 출력을 검토합니다.
    - 쿼리에 지정한 기간에 따라 작업 상태가 *실행 중*이면 출력은 싱크 데이터 원본에서 볼 수 있습니다.
    - 특정 출력 형식으로 된 출력을 볼 수 없는 경우 Azure Blob과 같이 덜 복잡한 출력 형식으로 리디렉션합니다. Storage Explorer를 사용하여 출력을 볼 수 있는지 여부를 확인합니다. 또한 출력 시 제한 한도가 데이터를 수신하지 못하게 하는지 여부를 확인합니다.

10. 작업 다이어그램 메트릭을 통해 데이터 흐름 분석을 사용합니다.
    - 데이터 흐름을 분석하고 문제를 식별하려면 [메트릭을 사용한 작업 다이어그램](stream-analytics-job-diagram-with-metrics.md)을 사용합니다.

11. 지원 사례를 엽니다.
    - 마지막으로, 모든 작업이 실패하면 작업을 포함하는 SubscriptionID를 사용하여 Microsoft 지원 사례를 엽니다.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
