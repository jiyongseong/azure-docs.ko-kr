---
title: Azure Time Series Insights에서 문제 진단 및 해결 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경에서 발생할 수 있는 일반적인 문제를 진단하고 해결하는 방법을 설명합니다.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: f0c1b8aa99e9ac9c73f57af17490dd3a465a9cac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Time Series Insights 환경에서 문제 진단 및 해결

## <a name="problem-1-no-data-is-shown"></a>문제 1: 데이터가 표시되지 않음
[Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 데이터가 표시되지 않을 때는 다음과 같은 몇 가지 일반적인 원인이 있습니다.

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>가능한 원인 A: 이벤트 원본 데이터가 JSON 형식이 아닙니다.
Azure Time Series Insights는 JSON 데이터만 지원합니다. JSON 샘플의 경우 [지원되는 JSON 셰이프](time-series-insights-send-events.md#supported-json-shapes)를 참조하세요.

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>가능한 원인 B: 이벤트 원본 키에 필요한 사용 권한이 없습니다.
* IoT Hub의 경우 **서비스 연결** 사용 권한이 있는 키를 제공해야 합니다.

   ![Iot Hub 서비스 연결 권한](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   위의 이미지에 표시된 것처럼 **iothubowner** 및 **서비스** 정책에는 모두 **서비스 연결** 사용 권한이 있으므로 둘 중 하나가 작동합니다.
   
* 이벤트 허브의 경우 **수신** 사용 권한이 있는 키를 제공해야 합니다.

   ![이벤트 허브 수신 사용 권한](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   위의 이미지에 표시된 것처럼 **읽기** 및 **관리** 정책에는 모두 **수신** 사용 권한이 있으므로 둘 중 하나가 작동합니다.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>가능한 원인 C: 제공된 소비자 그룹이 Time Series Insights에 단독적이지 않습니다.
IoT Hub 또는 이벤트 허브의 등록 중에 데이터를 읽을 때 사용해야 하는 소비자 그룹을 지정합니다. 이 소비자 그룹은 공유하지 **않아야** 합니다. 소비자 그룹이 공유되면 기본 이벤트 허브는 자동으로 한 읽기 권한자와 임의로 연결을 끊습니다. Time Series Insights에서 읽을 고유한 소비자 그룹을 제공합니다.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>문제 2: 데이터 중 일부만 표시됨
데이터를 부분적으로 볼 수 있지만 데이터가 지연되는 경우 다음과 같은 몇 가지 가능성을 고려해야 합니다.

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>가능한 원인 A: 사용자 환경이 제한적입니다.
이것은 데이터로 이벤트 원본을 만든 후 환경이 프로비전될 때 나타나는 일반적인 문제입니다.  Azure IoT Hub 및 Event Hubs는 최대 7일 동안의 데이터를 저장합니다.  TSI는 항상 이벤트 원본 내에서 가장 오래된 이벤트에서 시작됩니다(FIFO).  따라서 단일 단위 TSI 환경인 S1에 연결할 때 하나의 이벤트 원본에 5백만 개의 이벤트가 있는 경우 TSI는 하루에 약 백만 개의 이벤트를 읽습니다.  대충 보면 TSI에서 5일 간의 대기 시간이 발생하는 것처럼 나타날 수 있습니다.  실제로는 작업 환경이 제한되고 있는 것입니다.  이벤트 원본에 이전 이벤트가 있는 경우 다음 두 가지 방법 중 하나로 접근할 수 있습니다.

- 이벤트 원본의 보존 제한을 변경하여 TSI에 표시하지 않으려는 이전 이벤트 제거
- 더 큰 크기의 환경(단위 수 측면에서)을 프로비전하여 이전 이벤트의 처리량 늘리기.  위 예제를 사용하는 경우 동일한 S1 환경을 하루에 5개 단위로 늘리면 해당 환경은 이제 하루 만에 같은 처리량을 따라잡아야 합니다.  안정 상태의 이벤트 생성이 하루에 백만 개 미만의 이벤트인 경우 처리량을 따라잡은 후에는 이벤트 용량을 다시 1개 단위로 줄일 수 있습니다.  

제한적 한도는 환경의 SKU 유형 및 용량을 기준으로 적용됩니다. 환경의 모든 이벤트 원본은 이 용량을 공유합니다. IoT Hub 또는 이벤트 허브에 대한 이벤트 원본이 적용된 한도를 초과하여 데이터를 푸시하는 경우 제한 및 지연될 수 있습니다.

다음 다이어그램에는 SKU S1 및 용량 3을 사용하는 Time Series Insights 환경이 나와 있습니다. 하루에 3백만 개의 이벤트를 수신할 수 있습니다.

![환경 SKU 현재 용량](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

예를 들어 이 환경이 이벤트 허브에서 메시지를 수집하고 있다고 가정합니다. 다음 다이어그램에 표시되는 수신 속도를 관찰합니다.

![이벤트 허브에 대한 예제 수신 속도](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

다이어그램에 나온 것처럼 일일 수신 속도는 67,000 메시지입니다. 이 속도는 분당 약 46 메시지로 변환합니다. 각 이벤트 허브 메시지가 단일 Time Series Insights 이벤트로 결합되는 경우 이 환경에서 제한은 나타나지 않습니다. 각 이벤트 허브 메시지가 100 Time Series Insights 이벤트로 결합되는 경우에는 1분마다 4,600 이벤트가 수집되어야 합니다. 용량이 3인 S1 SKU 환경은 1분마다 2,100 이벤트만 수신할 수 있습니다(일별 1백만 이벤트 = 3 단위에서 분당 700 이벤트 = 분당 2,100 이벤트). 따라서 제한으로 인해 지연이 나타납니다. 

평면화 논리 작동 방식을 깊이 이해하기 위해서는 [지원되는 JSON 셰이프](time-series-insights-send-events.md#supported-json-shapes)를 참조하세요.

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>과도한 제한에 대한 권장 해결 단계
지연을 해결하려면 사용자 환경의 SKU 용량을 늘립니다. 자세한 내용은 [Time Series Insights 환경의 크기를 조정하는 방법](time-series-insights-how-to-scale-your-environment.md)을 참조하세요.

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>가능한 원인 B: 기록 데이터의 초기 수집으로 인해 수신이 느려짐
기존 이벤트 원본에 연결되어 있는 경우 이미 IoT Hub 또는 이벤트 허브에 데이터가 있을 수 있습니다. 환경은 이벤트 원본 메시지 보존 기간의 시작 부분에서 데이터 풀링을 시작합니다.

이 동작은 기본 동작이며 재정의할 수 없습니다. 제한을 적용할 수 있으며 기록 데이터 수집을 캐치업하는 데 시간이 걸릴 수 있습니다.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>대용량 초기 수집에 대한 권장 해결 단계
지연을 해결하려면 다음 단계를 따르세요.
1. SKU 용량을 최대 허용 값(이 경우 10)으로 늘립니다. 용량을 증가시키면 수신 프로세스가 훨씬 더 빠르게 캐치업하기 시작합니다. [Time Series Insights 탐색기](https://insights.timeseries.azure.com)의 가용성 차트를 통해 얼마나 빨리 캐치업하는지 확인할 수 있습니다. 증가한 용량에 대해 요금이 부과됩니다.
2. 지연이 캐치업되면 다시 SKU 용량을 정상 수신 속도로 줄입니다.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>문제 3: 내 이벤트 원본의 *타임스탬프 속성 이름* 설정이 작동하지 않음
이름 및 값이 다음 규칙을 준수하는지 확인합니다.
* 타임스탬프 속성 이름은 _대/소문자를 구분_합니다.
* JSON 문자열처럼 이벤트 원본에서 가져오는 타임스탬프 속성 값은 _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ 형식이어야 합니다. 이러한 문자열의 예는 “2008-04-12T12:53Z”입니다.

*타임스탬프 속성 이름*이 캡처되고 제대작동로 하는지 확인하는 가장 쉬운 방법은 TSI 탐색기를 사용하는 것입니다.  TSI 탐색기 내에서 차트를 사용하고, *타임스탬프 속성 이름*을 제공하기 전에 경과될 기간을 선택합니다.  선택 영역을 마우스 오른쪽 단추로 클릭하고 *이벤트 탐색* 옵션을 선택합니다.  첫 번째 열 머리글은 *타임스탬프 속성 이름*이고, *타임스탬프* 단어 옆에 *($ts)* 가 나와야 합니다. 다음 표시는 사용하지 않아야 합니다.
- *(abc)*: TSI가 데이터 값을 문자열로 읽음을 나타냅니다.
- *달력 아이콘*: TSI가 데이터 값을 *날짜/시간*으로 읽음을 나타냅니다.
- *#*: TSI가 데이터 값을 정수로 읽음을 나타냅니다.


## <a name="next-steps"></a>다음 단계
- 추가 지원을 받으려면 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights)에서 대화를 시작합니다. 
- 지원 옵션에 대해서는 [Azure 지원](https://azure.microsoft.com/support/options/)을 사용할 수도 있습니다.
