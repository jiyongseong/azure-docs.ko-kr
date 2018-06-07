---
title: Azure Time Series Insights에서 제한을 모니터링하고 줄이는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights에서 대기 시간과 제한을 발생시키는 성능 문제를 모니터링, 진단 및 완화하는 방법에 대해 설명합니다.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: ac59359eb6af268f311534d90e1529fc5e41094f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Azure Time Series Insights에서 모니터링을 수행하고 제한을 축소하여 대기 시간 줄이기
들어오는 데이터의 양이 사용자의 환경 구성을 초과하면 Azure Time Series Insights에서 대기 시간 또는 제한이 발생할 수 있습니다.

분석하려는 데이터의 양에 맞게 환경을 적절히 구성하여 대기 시간 및 제한을 방지할 수 있습니다.

다음과 같은 경우 대기 시간 및 제한이 발생할 수 있습니다.

- 할당된 수신 속도를 초과할 수 있는 오래된 데이터가 포함된 이벤트 원본을 추가합니다. Time Series Insights를 확인해야 합니다.
- 더 많은 이벤트 원본을 환경에 추가하면 추가 이벤트가 발생할 수 있습니다(환경 용량을 초과할 수 있음).
- 많은 양의 기록 이벤트를 이벤트 원본으로 푸시하면 지연이 발생합니다. Time Series Insights를 확인해야 합니다.
- 원격 분석을 사용하여 참조 데이터를 결합하면 이벤트 크기가 더 커집니다.  제한 관점에서, 패킷 크기가 32KB인 수신 데이터 패킷은 각각 1KB 크기의 32개 이벤트로 처리됩니다. 최대 허용 이벤트 크기는 32KB입니다. 32KB보다 큰 데이터 패킷은 잘립니다.


## <a name="monitor-latency-and-throttling-with-alerts"></a>경고를 사용하여 대기 시간 및 제한 모니터링

경고는 환경으로 인한 대기 시간 문제를 진단하고 완화하는 데 도움이 됩니다. 

1. Azure Portal에서 **메트릭**을 클릭합니다. 

   ![메트릭](media/environment-mitigate-latency/add-metrics.png)

2. **메트릭 경고 추가**를 클릭합니다.  

    ![메트릭 경고 추가](media/environment-mitigate-latency/add-metric-alert.png)

여기에서 다음 메트릭을 사용하여 경고를 구성할 수 있습니다.

|메트릭  |설명  |
|---------|---------|
|**수신된 바이트**     | 이벤트 원본에서 읽은 원시 바이트 수입니다. 일반적으로 원시 바이트 수에는 속성 이름 및 값이 포함됩니다.  |  
|**수신된 잘못된 메시지**     | 모든 Azure Event Hubs 또는 Azure IoT Hub 이벤트 원본에서 읽은 잘못된 메시지 수입니다.      |
|**수신된 메시지**   | 모든 Event Hubs 또는 IoT Hub 이벤트 원본에서 읽은 메시지 수입니다.        |
|**저장된 수신 바이트**     | 저장되어 쿼리에 사용할 수 있는 총 이벤트 크기입니다. 크기는 속성 값에 대해서만 계산됩니다.        |
|**저장된 수신 이벤트**     |   저장되어 쿼리에 사용할 수 있는 일반 이벤트 수입니다.      |
|**수신된 메시지 시간 지연**    |  메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.      |
|**수신된 메시지 수 지연**    |  이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.      |


![대기 시간](media/environment-mitigate-latency/latency.png)

제한이 적용되는 경우 TSI에서 메시지가 이벤트 원본을 거치는 실제 시간이 되기까지 몇 분이 남았는지를 나타내는 *수신된 메시지 시간 지연* 값을 볼 수 있습니다(약 30-60초의 인덱싱 시간 제외).  *수신된 메시지 수 지연*에도 값이 표시되므로 메시지가 뒤에 몇 개나 더 남아 있는지 알 수 있습니다.  이러한 차이를 해소하는 가장 쉬운 방법은 작업 환경의 용량을 차이가 극복될 수 있는 크기로 늘리는 것입니다.  

예를 들어, 단일 단위 S1 환경을 사용하며 5백만 개의 지연된 메시지가 남아 있는 경우 하루 동안 이 차이를 따라잡기 위해 작업 환경의 크기를 6개 단위로 늘릴 수 있습니다.  더 빠르게 따라잡기 위해 더 크게 늘릴 수도 있습니다.  이러한 문제는 처음에 환경을 프로비전할 때 발생하는 일반적인 문제로, 이미 이벤트가 들어 있는 이벤트 원본에 연결하거나 많은 기록 데이터를 대량으로 업로드할 때 특히 두드러집니다.

또 다른 방법은 **저장된 수신 이벤트** 경고를 2시간 동안의 전체 환경 용량보다 약간 낮게 설정하는 것입니다.  이 경고는 용량이 일정한지 이해하는 데 도움이 되며 대기 시간이 길어질 가능성이 높다는 것을 나타냅니다.  

예를 들어 3개의 S1 장치가 프로비전된 경우(또는 1분당 2100개의 이벤트를 수신하는 경우) **저장된 수신 이벤트** 경고를 2시간 동안 1900개 이벤트 이하로 설정할 수 있습니다. 이 임계값을 지속적으로 초과하여 경고를 발생시키는 경우 프로비전 부족 상태가 될 가능성이 큽니다.  

또한 제한이 의심되는 경우 **수신된 메시지** 수를 이벤트 원본의 발신 메시지와 비교할 수 있습니다.  Event Hub로 수신된 메시지 수가 **수신된 메시지** 수보다 크면 Time Series Insights가 제한될 수 있습니다.

## <a name="improving-performance"></a>성능 향상 
제한 또는 대기 시간을 줄이고 싶을 때 이를 수정하는 가장 좋은 방법은 환경의 용량을 늘리는 것입니다. 

분석하려는 데이터의 양에 맞게 환경을 적절히 구성하여 대기 시간 및 제한을 방지할 수 있습니다. 환경에 용량을 추가하는 방법에 대한 자세한 내용은 [환경 크기 조정](time-series-insights-how-to-scale-your-environment.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- 추가 문제 해결 단계는 [Time Series Insights 환경에서 문제 진단 및 해결](time-series-insights-diagnose-and-solve-problems.md)을 참조하세요.
- 추가 지원을 받으려면 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights)에서 대화를 시작합니다. 지원 옵션에 대해서는 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의할 수도 있습니다.
