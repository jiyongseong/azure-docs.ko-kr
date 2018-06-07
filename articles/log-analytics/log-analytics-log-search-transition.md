---
title: Azure Log Analytics 쿼리 언어 참고 자료 | Microsoft Docs
description: 이 문서는 이미 레거시 언어에 잘 알고 있는 경우에 Log Analytics에 대한 새로운 쿼리 언어로의 전환에 관한 지원을 제공합니다.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Azure Log Analytics 새로운 쿼리 언어로 전환
Log Analytics는 최근에 새로운 쿼리 언어를 구현했습니다.  이 문서는 이미 레거시 언어에 잘 알고 있고 약간의 도움이 필요한 사용자에게 Log Analytics에 대한 이 언어로 전환하기 위한 지원을 제공합니다.

## <a name="resources"></a>리소스


## <a name="language-converter"></a>언어 변환기

레거시 Log Analytics 쿼리 언어에 익숙한 경우 새 언어로 동일한 쿼리를 만드는 가장 쉬운 방법은 작업 영역을 변환할 때 로그 검색 포털에 설치된 언어 변환기를 사용하는 것입니다.  변환기를 사용하는 작업은 상위 텍스트 상자에 레거시 쿼리로 입력한 다음 **변환**을 클릭하기만 하면 됩니다.  검색 단추를 클릭하여 쿼리를 실행하거나, 다른 위치에서 사용하도록 복사하여 붙여넣을 수 있습니다.

![언어 변환기](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>리소스
[Log Analytics 쿼리 언어에 대한 설명서 사이트](https://docs.loganalytics.io)에는 새 언어를 신속히 습득하는 데 필요한 모든 리소스가 있습니다.  여기에는 자습서, 예제 및 전체 언어 참조가 포함됩니다.


## <a name="cheat-sheet"></a>참고 자료

다음 테이블에서는 Azure Log Analytics의 신규 및 레거시 쿼리 언어 간 동등한 명령에 관해 다양한 일반 쿼리 간 비교를 제공합니다.

| 설명 | 레거시 | 신규 |
|:--|:--|:--|
| 모든 테이블 검색      | error | “오류” 검색(대소문자 구분 안 함) |
| 테이블에서 데이터 선택 | Type=Event |  행사 |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| 문자열 비교      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (not case sensitive)<br>Event &#124; where Computer contains_cs "Contoso" (case sensitive) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| 날짜 비교        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &amp;#124; where TimeGenerated between (datetime(2017-05-01) . datetime(2017-05-31)) |
| 부울 비교     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| where IsGatewayInstalled == false |
| 정렬                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Distinct               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| 열 확장         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| 집계            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| 제한있는 집계 | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| 통합                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>다음 단계
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](https://go.microsoft.com/fwlink/?linkid=856078)를 확인해 보세요.
- [쿼리 언어 참조](https://go.microsoft.com/fwlink/?linkid=856079)에서 새 쿼리 언어에 대한 모든 명령, 연산자 및 함수를 자세히 알아보세요.  
