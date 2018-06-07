---
title: Azure Application Insights 원격 분석 데이터 모델 - 추적 원격 분석 | Microsoft Docs
description: 추적 원격 분석을 위한 Azure Application Insights 데이터 모델
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: d93ed9f292b6c05d0a3fb3202567f4024f62e35e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="trace-telemetry-application-insights-data-model"></a>추적 원격 분석: Application Insights 데이터 모델

[Application Insights](app-insights-overview.md)에서 추적 원격 분석은 텍스트를 검색하는 `printf` 스타일 추적 문을 나타냅니다. `Log4Net`, `NLog` 및 기타 텍스트 기반 로그 파일 항목이 이 형식의 인스턴스로 변환됩니다. 추적에는 확장 기능으로 사용할 측정이 없습니다.

## <a name="message"></a>Message

추적 메시지입니다.

최대 길이: 32768자

## <a name="severity-level"></a>심각도 수준

추적 심각도 수준입니다. 값은 `Verbose`, `Information`, `Warning`, `Error`, `Critical`일 수 있습니다.

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>다음 단계

- [Application Insights에서 .NET 추적 로그 탐색](app-insights-asp-net-trace-logs.md).
- [Application Insights에서 Java 추적 로그 탐색](app-insights-java-trace-logs.md).
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.
- [사용자 지정 추적 원격 분석을 작성합니다](app-insights-api-custom-events-metrics.md#tracktrace).
- Application Insights에서 지원되는 [플랫폼](app-insights-platforms.md)을 확인합니다.
