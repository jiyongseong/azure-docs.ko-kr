---
title: "Azure Time Series Insights 환경 만들기 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 새로운 Time Series Insights 환경을 만드는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 20156432e17d5eca90779271bd18dc49fa988d7c
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal에서 Time Series Insights 환경 만들기
이 문서에서는 Azure Portal을 사용하여 새로운 Time Series Insights 환경을 만드는 방법을 설명합니다.

Time Series Insights를 사용하여 Azure IoT Hub 및 Event Hubs로 이동하는 데이터를 빠른 시간 안에 시각화하고 쿼리할 수 있으므로 대용량 시계열 데이터를 빠르게 쿼리할 수 있습니다.  이 기능은 IoT(사물 인터넷) 규모에 맞게 설계되었으며 테라바이트 단위의 데이터를 처리할 수 있습니다.

## <a name="steps-to-create-the-environment"></a>환경을 만드는 단계
다음 단계에 따라 환경을 만듭니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  **+새로 만들기** 단추를 선택합니다.

3.  **사물 인터넷** 범주를 선택하고 **Time Series Insights**를 선택합니다.

   ![Time Series Insights 환경 만들기](media/time-series-insights-get-started/1-new-tsi.png)

4.  **Time Series Insights** 페이지에서 **만들기**를 선택합니다.

5. 필수 매개 변수를 입력합니다. 다음 표에서는 각 매개 변수에 대해 설명합니다.
   
   ![Time Series Insights 리소스 그룹 만들기](media/time-series-insights-get-started/2-create-tsi.png)
   
   설정|제안 값|설명
   ---|---|---
   환경 이름 | 고유한 이름 | 이 이름은 [Time Series 탐색기](https://insights.timeseries.azure.com)에서 해당 환경을 나타내는 데 사용됩니다.
   구독 | 사용자의 구독 | 구독이 여러 개인 경우 이벤트 원본을 포함하는 구독을 선택합니다. Time Series Insights는 같은 구독에 있는 Azure IoT Hub 및 Event Hub 리소스를 자동으로 검색할 수 있습니다.
   리소스 그룹 | 새로 만들기 또는 기존 항목 사용 | 리소스 그룹은 함께 사용되는 Azure 리소스 컬렉션입니다. 예를 들어 이벤트 허브 또는 IoT Hub를 포함하는 기존 리소스 그룹을 선택할 수 있습니다. 또는 이 리소스가 다른 리소스와 관련이 없는 경우에는 새로 만들 수 있습니다.
   위치 | 이벤트 원본에 가장 가까운 위치 | 지역 외부로 데이터를 이동할 때 지역 간 및 영역 간 대역폭 비용이 추가되고 대기 시간이 늘어나지 않도록 하기 위해 이벤트 원본 데이터를 포함하는 동일한 데이터 센터 위치를 선택하는 것이 좋습니다.
   가격 책정 계층  | S1 | 필요한 처리량을 선택합니다. 최저 비용의 시작 용량을 사용하려면 S1을 선택합니다.
   용량 | 1 | 용량은 선택한 SKU와 관련된 입력 속도, 저장 용량 및 비용에 적용되는 승수입니다.  환경을 만든 후 환경의 용량을 변경할 수 있습니다. 최저 비용을 원할 경우 용량 1을 선택합니다. 
  
6. **대시보드에 고정**을 선택하면 나중에 자신의 Time Series 환경에 가장 쉽게 액세스할 수 있습니다.

   ![Time Series Insights를 만들고 대시보드에 고정](media/time-series-insights-get-started/3-pin-create.png)

7. **만들기**를 선택하여 프로비전 프로세스를 시작합니다. 이 작업에 몇 분 정도가 소요됩니다.

8. 배포 프로세스를 모니터링하려면 **알림** 기호(종 모양)를 선택합니다.

   ![알림 보기](media/time-series-insights-get-started/4-notifications.png)

배포가 성공하면 **리소스로 이동**을 선택하여 다른 속성을 구성하고, 데이터 액세스 정책을 사용하여 보안을 설정하고, 이벤트 원본을 추가하고, 기타 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 환경을 보호하기 위한 [데이터 액세스 정책 정의](time-series-insights-data-access.md)
* Azure Time Series Insights 환경에 [이벤트 허브 이벤트 원본 추가](time-series-insights-how-to-add-an-event-source-eventhub.md) 
* 이벤트 원본으로 [이벤트 전송](time-series-insights-send-events.md)
* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경 보기
