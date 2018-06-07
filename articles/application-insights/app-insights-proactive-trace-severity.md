---
title: "스마트 검색 - Azure Application Insights에서 추적 심각도 비율의 저하 | Microsoft Docs"
description: "Azure Application Insights에서 응용 프로그램 추적을 모니터링하여 추적 원격 분석의 비정상적인 패턴을 확인합니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 29ed195dadb7230df425d6d981a0a482e09ee79f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>추적 심각도 비율의 저하(미리 보기)

추적은 배후 상황에서 수행되는 작업을 설명하는 데 도움이 되기 때문에 응용 프로그램에서 널리 사용됩니다. 오류가 발생하는 경우 추적은 원치 않는 상태로 전환되는 이벤트 시퀀스에 중요한 가시성을 제공합니다. 추적이 일반적으로 구조화되지 않는 동안 여기에서 구체적으로 확인할 수 있는 한 가지는 심각도 수준입니다. 응용 프로그램이 안정 상태인 경우 "올바른" 추적(*정보* 및 *Verbose*) 및 "잘못된" 추적(*경고*, *오류* 및 *위험*) 간의 비율을 안정적으로 유지합니다. "잘못된" 추적이 여러 가지 이유로 인해 정기적으로 어느 정도까지 발생할 수 있습니다(예: 일시적인 네트워크 문제). 하지만 진짜 문제가 증가하기 시작하면 일반적으로 "잘못된" 추적 및 "올바른" 추적의 상대적 비율이 증가합니다. Application Insights 스마트 검색은 자동으로 응용 프로그램에 의해 기록된 추적을 분석하고, 추적 원격 분석의 심각도에 표시된 비정상적인 패턴에 대해 경고할 수 있습니다.

이 기능에는 앱에 대한 추적 로깅 구성 이외의 특별한 설정이 필요하지 않습니다([.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) 또는 [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)에 추적 로그 수신기를 구성하는 방법 참조 ). 앱에서 충분한 예외 원격 분석을 생성하면 이 기능은 활성화됩니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 형식의 스마트 검색 알림은 언제 받게 되나요?
지난 7일 동안 계산된 기준에 비해 "올바른" 추적(*정보* 또는 *Verbose* 수준에서 기록된 추적) 및 "잘못된" 추적 (*경고*, *오류 또는 *치명적* 수준으로 기록된 추적) 간의 비율이 특정한 날에 저하되는 경우 이러한 형식의 알림이 발생할 수 있습니다.

## <a name="does-my-app-definitely-have-a-problem"></a>내 앱에 분명히 문제가 있나요?
아니요, 알림이 제공된다고 해서 앱에 반드시 문제가 있는 것은 아닙니다. "올바른" 및 "잘못된" 추적 간 비율의 저하가 응용 프로그램 문제를 나타낼 수 있지만 이러한 비율의 변경은 무해할 수 있습니다. 예를 들어 기존 흐름보다 더 많은 "잘못된" 추적을 내보내는 응용 프로그램에서 새 흐름으로 인해 증가가 발생할 수 있습니다.

## <a name="how-do-i-fix-it"></a>이 문제를 어떻게 해결하나요?
알림에는 진단 프로세스에서 도움이 되는 진단 정보가 포함되어 있습니다.
1. **심사.** 이 알림은 영향을 받은 작업의 수를 보여줍니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 이 문제가 모든 트래픽에 영향을 주나요? 아니면 일부 작업에만 영향을 주나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 지원 정보에 연결된 관련 항목 및 보고서를 사용하면 문제를 추가로 진단할 수 있습니다.


