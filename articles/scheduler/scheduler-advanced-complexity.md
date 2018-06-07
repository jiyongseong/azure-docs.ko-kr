---
title: "Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이 빌드"
description: "Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이를 빌드하는 방법을 알아봅니다."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이 빌드

Azure Scheduler 작업의 핵심은 일정입니다. 일정은 Scheduler에서 작업을 실행하는 시기와 방법을 결정합니다. 

Scheduler를 사용하여 작업에 대해 여러 개의 일회성 및 되풀이 일정을 설정할 수 있습니다. 일회성 일정은 지정된 시간에 한 번만 실행됩니다. 실질적으로 한 번만 실행되는 되풀이 일정입니다. 되풀이 일정은 미리 지정된 빈도로 실행됩니다.

이러한 유연성을 통해 다양한 비즈니스 시나리오에 Azure Scheduler를 사용할 수 있습니다.

* **정기적으로 데이터를 정리**합니다. 예를 들어 매일 3개월이 지난 모든 트윗을 삭제합니다.
* **보관**합니다. 예를 들어 매월 송장 기록을 백업 서비스로 푸시합니다.
* **외부 데이터를 요청**합니다. 예를 들어 매 15분마다 NOAA에서 새 스키 날씨 보고서를 끌어옵니다.
* **이미지를 처리**합니다. 예를 들어 매주 평일의 사용률이 낮은 시간에 클라우드 컴퓨팅을 사용하여 업로드한 해당 일의 이미지를 압축합니다.

이 문서에서는 Scheduler를 사용하여 만들 수 있는 작업 예제를 안내합니다. 각 일정을 설명하는 JSON 데이터를 제공합니다. [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx)를 사용하는 경우 이 동일한 JSON을 사용하여 [Scheduler 작업을 만들](https://msdn.microsoft.com/library/mt629145.aspx) 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
이 문서의 예제에서는 Scheduler에서 지원하는 다양한 시나리오를 보여 줍니다. 이러한 예제는 다음을 포함하여 일정을 다양한 동작 패턴으로 만드는 방법을 광범위하게 설명합니다.

* 특정 날짜/시간에 한 번만 실행합니다.
* 특정 횟수만큼 실행하고 되풀이합니다.
* 즉시 실행하고 되풀이합니다.
* *n*분, 시간, 일, 주 또는 월마다 실행하고 되풀이하며, 특정 시간에 시작합니다.
* 매주 또는 매월 빈도로 특정 요일 또는 월의 특정 일에만 실행하고 되풀이합니다.
* 한 기간 내에 여러 번 실행하고 되풀이합니다. 예를 들어 매월 마지막 금요일과 마지막 월요일 또는 매일 오전 5시 15분과 오후 5시 15분이 있습니다.

## <a name="date-and-date-time"></a>날짜 및 날짜-시간
Scheduler 작업의 날짜 참조는 [ISO 8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따르며 날짜만 포함합니다.

Scheduler 작업의 날짜-시간 참조는 [ISO 8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따르며 날짜와 시간을 모두 포함합니다. UTC 오프셋을 지정하지 않은 날짜-시간은 UTC로 간주됩니다.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>JSON 및 REST API를 사용하여 일정 만들기
[Scheduler REST API](https://msdn.microsoft.com/library/mt629143)를 사용하여 기본 일정을 만들려면 먼저 [리소스 공급자에 구독을 등록합니다](https://msdn.microsoft.com/library/azure/dn790548.aspx). Scheduler의 공급자 이름은 **Microsoft.Scheduler**입니다. 그런 다음, [작업 컬렉션을 만듭니다](https://msdn.microsoft.com/library/mt629159.aspx). 마지막으로, [작업을 만듭니다](https://msdn.microsoft.com/library/mt629145.aspx). 

작업을 만들 때 다음 발췌와 같은 JSON을 사용하여 일정과 되풀이를 지정할 수 있습니다.

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>작업 스키마 기본 사항
다음 표에서는 일정과 되풀이를 작업에 설정하는 데 사용되는 주요 요소에 대해 개략적으로 설명합니다.

| JSON 이름 | 설명 |
|:--- |:--- |
| **startTime** |날짜-시간 값입니다. 기본 일정의 경우 **startTime**은 첫 번째 되풀이 항목입니다. 복잡한 일정의 경우 작업은 **startTime** 이후에 시작됩니다. |
| **recurrence** |작업에 대한 되풀이 규칙과 작업이 실행되는 되풀이를 지정합니다. recurrence 개체는 **frequency**, **interval**, **endTime**, **count** 및 **schedule** 요소를 지원합니다. **recurrence**가 정의되면 **frequency**가 필수 요소이며, 다른 **recurrence** 요소는 선택 사항입니다. |
| **frequency** |작업이 되풀이되는 빈도 단위를 나타내는 문자열입니다. 지원되는 값은 "minute", "hour", "day", "week" 및 "month"입니다. |
| **interval** |양의 정수입니다. **interval**은 작업이 실행되는 빈도를 결정하는 **frequency** 값에 대한 간격을 나타냅니다. 예를 들어 **interval**이 3이고 **frequency**가 "week"인 경우 작업은 3주마다 되풀이됩니다.<br /><br />Scheduler에서 지원하는 최대 **interval**은 월 빈도의 경우 18, 주 빈도의 경우 78, 일 빈도의 경우 548입니다. 시간과 분 빈도의 경우 지원되는 **interval** 범위는 1 ~ 1000입니다. |
| **endTime** |작업이 실행되지 않는 날짜-시간을 지정하는 문자열입니다. 과거의 **endTime**에 대한 값을 설정할 수 있습니다. **endTime** 또는 **count**가 지정되지 않으면 작업은 무한으로 실행됩니다. 동일한 작업에서 **endTime** 및 **count**를 모두 포함할 수 없습니다. |
| **count** |작업이 완료되기 전에 실행되는 횟수를 지정하는 양의 정수(0보다 큼)입니다.<br /><br />**count**는 작업이 완료된 것으로 결정되기 전에 실행되는 횟수를 나타냅니다. 예를 들어 **count**가 5이고 시작 날짜가 월요일인 작업이 매일 실행되는 경우 이 작업은 금요일에 실행된 후 완료됩니다. 시작 날짜가 과거인 경우, 첫 실행 시간은 생성 시간에서 계산됩니다.<br /><br />**endTime** 또는 **count**가 지정되지 않으면 작업은 무한으로 실행됩니다. 동일한 작업에서 **endTime** 및 **count**를 모두 포함할 수 없습니다. |
| **schedule** |작업에 빈도가 지정되면 되풀이 일정을 기반으로 되풀이가 변경됩니다. **schedule** 값에는 분, 시간, 요일, 월 일, 주차를 기반으로 하는 조건이 포함됩니다. |

## <a name="job-schema-defaults-limits-and-examples"></a>작업 스키마 기본값, 제한 및 예제
이 문서의 뒷부분에서 다음 요소 각각에 대해 자세히 설명합니다.

| JSON 이름 | 값 형식 | Required? | 기본값 | 유효한 값 | 예 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |string |아니요 |없음 |ISO 8601 날짜-시간 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |object |아니요 |없음 |되풀이 개체 |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |string |예 |없음 |"minute", "hour", "day", "week", "month" |`"frequency" : "hour"` |
| **interval** |number |예 |없음 |1~1000 |`"interval":10` |
| **endTime** |string |아니오 |없음 |미래의 시간을 나타내는 날짜-시간 값입니다. |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |number |아니요 |없음 |1 이상 |`"count": 5` |
| **schedule** |object |아니오 |없음 |일정 개체 |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>자세히 알아보기: startTime
다음 표에서는 **startTime**으로 작업이 실행되는 방식을 제어하는 방법을 설명합니다.

| startTime 값 | 되풀이 없음 | 되풀이, 일정 없음 | 일정대로 되풀이 |
|:--- |:--- |:--- |:--- |
| **시작 시간 없음** |즉시 한 번 실행합니다. |즉시 한 번 실행합니다. 마지막 실행 시간에서 계산된 후속 실행을 실행합니다. |즉시 한 번 실행합니다.<br /><br />되풀이 일정에 따른 후속 실행을 실행합니다. |
| **과거의 시작 시간** |즉시 한 번 실행합니다. |시작 시간 이후 첫 번째 실행 시간을 계산하고 해당 시간에 실행합니다.<br /><br />마지막 실행 시간에서 계산된 후속 실행을 실행합니다. <br /><br />자세한 내용은 이 표 뒤에 나오는 예제를 참조하세요. |작업 시작은 지정된 시작 시간보다 *이전* 이어서는 안 됩니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다.<br /><br />되풀이 일정에 따른 후속 실행을 실행합니다. |
| **미래 또는 현재 시간의 시작 시간** |지정된 시작 시간에 한 번 실행됩니다. |지정된 시작 시간에 한 번 실행됩니다.<br /><br />마지막 실행 시간에서 계산된 후속 실행을 실행합니다.|작업 시작은 지정된 시작 시간보다 *이전* 이어서는 안 됩니다. 시작 시간에서 계산된 일정에 따라 첫 번째 되풀이 항목을 실행합니다.<br /><br />되풀이 일정에 따른 후속 실행을 실행합니다. |

예를 들어 **startTime**이 과거이고 recurrence가 있지만 schedule이 없는 경우 발생되는 상황을 살펴보겠습니다.  현재 시간이 2015-04-08 13:00, **startTime**은 2015-04-07 14:00, **recurrence**는 2일마다(**frequency**는 일, **interval**은 2로 정의됨)라고 가정합니다. **startTime**이 과거이고 현재 시간 이전에 발생합니다.

이러한 조건 하에서 첫 번째 실행은 2015-04-09 오후 2시에 수행됩니다. Scheduler 엔진이 시작 시간에서 되풀이 실행 시간을 계산합니다. 현재보다 이전의 모든 인스턴스는 무시됩니다. 엔진은 이후에 발생하는 다음 인스턴스를 사용합니다. 이 경우 **startTime**은 2015-04-07 오후 2시이므로 다음 인스턴스는 이 시간에서 2일 후인 2015-04-09 오후 2시입니다.

**startTime**이 2015-04-05 오후 2시 또는 2015-04-01 오후 2시인지 여부에 관계없이 첫 번째 실행은 동일합니다\. 첫 번째 실행 후 후속 실행은 일정을 사용하여 계산됩니다. 후속 실행은 2015-04-11 오후 2시, 2015-04-13 오후 2시, 2015-04-15 오후 2시 등으로 진행됩니다.

마지막으로, 작업에 일정이 있는 경우 시간과 분이 일정에 설정되어 있지 않으면 첫 번째 실행의 시간과 분이 기본값이 됩니다.

## <a name="deep-dive-schedule"></a>심층 분석: schedule
**schedule**을 사용하여 작업 실행의 횟수를 *제한*할 수 있습니다. 예를 들어 **frequency**가 "month"인 작업이 31일에만 실행되는 일정이 있는 경우, 이 작업은 31일이 있는 달에만 실행됩니다.

또한 **schedule**을 사용하여 작업 실행의 횟수를 *늘릴* 수도 있습니다. 예를 들어 **frequency**가 "month"인 작업에 월의 1일과 2일에 실행되는 일정이 있는 경우, 이 작업은 한 달에 한 번 실행되는 것이 아니라 월의 1일과 2일에 실행됩니다.

여러 일정 요소를 지정하는 경우 가장 큰 것부터 가장 작은 것 순으로, 즉 주차, 월 일, 요일, 시간, 분의 순서로 계산됩니다.

다음 테이블은 schedule 요소의 세부 정보입니다.

| JSON 이름 | 설명 | 유효한 값 |
|:--- |:--- |:--- |
| **minutes** |작업이 실행되는 시간(분)입니다. |정수 배열입니다. |
| **hours** |작업이 실행되는 일의 시간(시)입니다. |정수 배열입니다. |
| **weekDays** |작업이 실행되는 요일입니다. 주 단위 빈도로만 지정할 수 있습니다. |다음 값 중 하나의 배열입니다(최대 배열 크기: 7).<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />대/소문자를 구분하지 않습니다. |
| **monthlyOccurrences** |작업이 실행되는 월의 일을 결정합니다. 월 단위 빈도로만 지정할 수 있습니다. |**monthlyOccurrences** 개체의 배열입니다.<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day**는 작업이 실행되는 주의 요일입니다. 예를 들어 *{Sunday}*는 월의 매주 일요일입니다. 필수 사항입니다.<br /><br />**occurrence**는 월 중 일의 되풀이 항목입니다. 예를 들어 *{Sunday, -1}*은 월의 마지막 일요일입니다. 선택 사항입니다. |
| **monthDays** |작업이 실행되는 월의 일입니다. 월 단위 빈도로만 지정할 수 있습니다. |다음 값의 배열입니다.<br />- 1 이상 및 31 이하의 모든 값<br />- 1 이하 및 31 이상의 모든 값|

## <a name="examples-recurrence-schedules"></a>예제: 되풀이 일정
다음 예제에서는 다양한 되풀이 일정을 보여 줍니다. 이러한 예제는 일정 개체 및 해당 하위 요소에 중점을 둡니다.

이러한 일정은 **interval**이 1로 설정되어 있다고 가정합니다\. 또한 예제에서는 **schedule**의 값에 대해 올바른 **frequency** 값이 있다고 가정합니다. 예를 들어 "day"를 **frequency** 값으로 사용할 수 없으며, **schedule**에 **monthDays** 수정 내용이 있을 수 없습니다. 이러한 제한 사항은 이 문서의 앞 부분에서 설명하고 있습니다.

| 예 | 설명 |
|:--- |:--- |
| `{"hours":[5]}` |매일 오전 5시에 실행됩니다.<br /><br />Scheduler에서 "hours"의 각 값을 "minutes"의 각 값과 하나씩 일치시켜 작업이 실행되는 모든 시간의 목록을 만듭니다. |
| `{"minutes":[15], "hours":[5]}` |매일 오전 5시 15분에 실행됩니다. |
| `{"minutes":[15], "hours":[5,17]}` |매일 오전 5시 15분 및 오후 5시 15분에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17]}` |매일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45]}` |15분마다 실행됩니다. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |매시간 실행됩니다.<br /><br />이 작업은 매시간 실행됩니다. 분이 지정된 경우 **startTime**에 대한 값으로 제어됩니다. **startTime** 값이 지정되지 않으면 분은 만든 시간으로 제어됩니다. 예를 들어 시작 시간 또는 만든 시간 중 해당하는 시간이 오후 12:25인 경우 작업은 00:25, 01:25, 02:25, …, 23:25에 실행됩니다.<br /><br />이 일정은 **frequency**가 "hour"이고 **interval**이 1이며 **schedule** 값이 없는 작업과 동일합니다. 차이점은 이 일정을 다른 **frequency**와 **interval** 값으로 사용하여 다른 작업을 만들 수 있다는 것입니다. 예를 들어 **frequency**가 "month"인 경우 일정은 매일(**frequency**가 "day"인 경우)이 아니라 한 달에 한 번만 실행됩니다. |
| `{minutes:[0]}` |매시간 정각에 실행됩니다.<br /><br />또한 이 작업은 매 시간 정각(오전 12시, 오전 1시, 오전 2시 등)에만 실행됩니다. frequency가 "day"인 경우 **frequency**가 "hour", **startTime** 값이 0분, **schedule**이 없는 작업과 동일합니다. 그러나 **frequency**가 "week" 또는 "month"인 경우 일정은 각각 주에 하루 또는 월에 하루만 실행됩니다. |
| `{"minutes":[15]}` |매시간 15분에 실행됩니다.<br /><br />오전 0시 15분, 오전 1시 15분, 오전 2시 15분 등에서 시작하여 매 시간마다 실행됩니다. 오후 11시 15분에 끝납니다. |
| `{"hours":[17], "weekDays":["saturday"]}` |매주 토요일 오후 5시에 실행됩니다. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일, 금요일 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오전 5시 및 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |월요일부터 금요일까지 15분마다 실행됩니다. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 매 15분마다 실행됩니다. |
| `{"weekDays":["sunday"]}` |일요일 시작 시간에 실행됩니다. |
| `{"weekDays":["tuesday", "thursday"]}` |화요일 및 목요일 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |매월 28일 오전 6시에 실행됩니다(**frequency**가 "month"라고 가정). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |월의 마지막 일 오전 6시에 실행됩니다.<br /><br />월의 마지막 일에 작업을 실행하려면 28, 29, 30 또는 31 대신 -1을 사용합니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |매월 첫 번째 일 및 마지막 일 오전 6시에 실행됩니다. |
| `{monthDays":[1,-1]}` |매월 첫 번째 일 및 마지막 일 시작 시간에 실행됩니다. |
| `{monthDays":[1,14]}` |매월 1일 및 14일 시작 시간에 실행됩니다. |
| `{monthDays":[2]}` |매월 2일 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |매월 첫 번째 금요일 오전 5시에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |매월 첫 번째 금요일 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |매월 마지막 일부터 세 번째 금요일 시작 시간에 실행됩니다. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |매월 첫 번째 및 마지막 금요일 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |매월 5번째 금요일 시작 시간에 실행됩니다.<br /><br />월에 5번째 금요일이 없으면 작업이 실행되지 않습니다. 작업이 매월 마지막으로 금요일에 실행되도록 하려면 occurrence에 대해 5대신 -1을 사용하는 것이 좋습니다. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |매월 마지막 금요일에 15분마다 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |매월 세 번째 수요일에 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |

## <a name="see-also"></a>참고 항목

- [Scheduler란?](scheduler-intro.md)
- [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
- [Azure Portal에서 Scheduler 사용 시작](scheduler-get-started-portal.md)
- [Azure Scheduler의 버전 및 요금 청구](scheduler-plans-billing.md)
- [Azure Scheduler REST API 참조](https://msdn.microsoft.com/library/mt629143)
- [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)
- [Azure Scheduler 고가용성 및 안정성](scheduler-high-availability-reliability.md)
- [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
- [Azure Scheduler 아웃바운드 인증](scheduler-outbound-authentication.md)

