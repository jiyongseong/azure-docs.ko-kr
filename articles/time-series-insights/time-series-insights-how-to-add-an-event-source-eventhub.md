---
title: "Azure Time Series Insights에 이벤트 허브 이벤트 원본을 추가하는 방법 | Microsoft Docs"
description: "이 문서에서는 이벤트 허브에 연결된 이벤트 원본을 Time Series Insights 환경에 추가하는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/21/2017
ms.openlocfilehash: c07c847784eb13c62e350e9c655e027e7df696a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Time Series Insights 환경에 이벤트 허브 이벤트 원본을 추가하는 방법

이 문서에서는 Azure Portal을 사용하여 이벤트 허브에서 데이터를 읽는 이벤트 원본을 Time Series Insights 환경에 추가하는 방법을 다룹니다.

## <a name="prerequisites"></a>필수 조건
- Time Series Insights 환경을 만듭니다. 자세한 내용은 [Azure Time Series Insights 환경 만들기](time-series-insights-get-started.md)를 참조하세요. 
- 이벤트 허브를 만듭니다. Event Hubs에 대한 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요.
- 이벤트 허브에는 전송 중인 활성 메시지 이벤트가 있어야 합니다. 자세한 내용은 [.NET Framework를 사용하여 Azure Event Hubs로 이벤트 전송](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)을 참조하세요.
- Time Series Insights에서 사용할 이벤트 허브의 전용 소비자 그룹을 만듭니다. 각 Time Series Insights 이벤트 원본에는 다른 소비자와 공유되지 않은 전용 소비자 그룹 자체가 있어야 합니다. 같은 소비자 그룹에서 여러 읽기 권한자가 이벤트를 소비하는 경우 모든 읽기 권한자에게 오류가 표시될 수 있습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [Event Hubs 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)를 참조하세요.

## <a name="add-a-new-event-source"></a>새 이벤트 원본 추가
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 기존 Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 클릭합니다. Time Series Insights 환경을 선택합니다.

3. **환경 토폴로지** 제목 아래에서 **이벤트 원본**을 클릭합니다.

   ![이벤트 원본 + 추가](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. **+ 추가**를 클릭합니다.

5. 이 Time Series Insights 환경에 고유한 **이벤트 원본 이름**(예: **event-stream**)을 제공합니다.

   ![양식에 처음 세 개의 매개 변수를 입력합니다.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. **이벤트 허브**로 **원본**을 선택합니다.

7. 해당 **가져오기 옵션**을 선택합니다. 
   - 구독 중 하나에 기존 이벤트 허브가 이미 있는 경우 **사용 가능한 구독의 이벤트 허브 사용**을 선택합니다. 이것이 가장 쉬운 방법입니다.
   - 이벤트 허브가 구독 외부에 있거나 고급 옵션을 선택하려는 경우 **이벤트 허브 설정 수동으로 제공**을 선택합니다. 

8. **사용 가능한 구독의 이벤트 허브 사용** 옵션을 선택한 경우 다음 표에 나오는 각 필수 속성에 대한 설명을 참조하세요.

   ![구독 및 이벤트 허브 정보](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | 속성 | 설명 |
   | --- | --- |
   | 구독 ID | 이 이벤트 허브가 만들어진 구독을 선택합니다.
   | Service Bus 네임스페이스 | 이벤트 허브를 포함하는 Service Bus 네임스페이스를 선택합니다.
   | 이벤트 허브 이름 | 이벤트 허브의 이름을 선택합니다.
   | 이벤트 허브 정책 이름 | 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책을 선택합니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **읽기** 사용 권한이 *반드시* 있어야 합니다.
   | 이벤트 허브 정책 키 | 키 값이 미리 채워질 수 있습니다.
   | 이벤트 허브 소비자 그룹 | 이벤트 허브에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다. |
   | 이벤트 직렬화 형식 | JSON이 현재 사용 가능한 유일한 직렬화입니다. 이벤트 메시지는 이 형식이어야 합니다. 그렇지 않으면 데이터를 읽을 수 없습니다. |
   | 타임스탬프 속성 이름 | 이 값을 확인하려면 이벤트 허브로 전송되는 메시지 데이터의 메시지 형식을 이해해야 합니다. 이 값은 이벤트 타임스탬프로 사용하려는 메시지 데이터에 있는 특정 이벤트 속성의 **이름**입니다. 이 값은 대/소문자를 구분합니다. 이 값을 비워 두면 이벤트 원본 내의 **이벤트 인큐 시간**이 이벤트 타임스탬프로 사용됩니다. |


9. **이벤트 허브 설정 수동으로 제공** 옵션을 선택한 경우 다음 표에 나오는 각 필수 속성에 대한 설명을 참조하세요.

   | 속성 | 설명 |
   | --- | --- |
   | 구독 ID | 이 이벤트 허브가 만들어진 구독입니다.
   | 리소스 그룹 | 이 이벤트 허브가 만들어진 리소스 그룹입니다.
   | Service Bus 네임스페이스 | Service Bus 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 Event Hub를 만들 때 Service Bus 네임스페이스도 만들었습니다.
   | 이벤트 허브 이름 | 이벤트 허브의 이름입니다. 이벤트 허브를 만들 때 지은 특정 이름입니다.
   | 이벤트 허브 정책 이름 | 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **읽기** 사용 권한이 *반드시* 있어야 합니다.
   | 이벤트 허브 정책 키 | Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키 기본 또는 보조 키를 여기에 입력합니다.
   | 이벤트 허브 소비자 그룹 | 이벤트 허브에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다.
   | 이벤트 직렬화 형식 | JSON이 현재 사용 가능한 유일한 직렬화입니다. 이벤트 메시지는 이 형식이어야 합니다. 그렇지 않으면 데이터를 읽을 수 없습니다. |
   | 타임스탬프 속성 이름 | 이 값을 확인하려면 이벤트 허브로 전송되는 메시지 데이터의 메시지 형식을 이해해야 합니다. 이 값은 이벤트 타임스탬프로 사용하려는 메시지 데이터에 있는 특정 이벤트 속성의 **이름**입니다. 이 값은 대/소문자를 구분합니다. 이 값을 비워 두면 이벤트 원본 내의 **이벤트 인큐 시간**이 이벤트 타임스탬프로 사용됩니다. |


10. 새 이벤트 원본을 추가하려면 **만들기**를 선택합니다.
   
   ![만들기 클릭](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   이벤트 원본이 생성되면 Time Series Insights가 자동으로 데이터를 환경으로 스트리밍하기 시작합니다.


### <a name="add-a-consumer-group-to-your-event-hub"></a>이벤트 허브에 소비자 그룹 추가
소비자 그룹은 응용 프로그램에서 Azure Event Hubs의 데이터를 끌어오는 데 사용됩니다. 이벤트 허브에서 안정적으로 데이터를 읽기 위해 이 Time Series Insights 환경에서만 사용하는 전용 소비자 그룹을 제공합니다.

이벤트 허브에 새 소비자 그룹을 추가하려면 다음 단계를 수행합니다.
1. Azure Portal에서 이벤트 허브를 찾아서 엽니다.

2. **엔터티** 제목 아래에서 **소비자 그룹**을 선택합니다.

   ![이벤트 허브 - 소비자 그룹 추가](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. **+소비자 그룹**을 선택하여 새 소비자 그룹을 추가합니다. 

4. **소비자 그룹** 페이지에서 고유한 새 **이름**을 제공합니다.  Time Series Insights 환경에서 새 이벤트 원본을 만들 때 이 동일한 이름을 사용합니다.

5. **만들기**를 선택하여 새 소비자 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계
- 데이터를 보호하기 위한 [데이터 액세스 정책 정의](time-series-insights-data-access.md)
- 이벤트 원본으로 [이벤트 전송](time-series-insights-send-events.md)
- [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경에 액세스
