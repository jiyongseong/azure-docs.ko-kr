---
title: Azure Application Insights 원격 분석 데이터 모델 - 이벤트 원격 분석 | Microsoft 문서
description: 이벤트 원격 분석을 위한 Azure Application Insights 데이터 모델
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
ms.openlocfilehash: 4f6b5c35b65b4aff2dbe8dafbb2eb07d75c2382a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="event-telemetry-application-insights-data-model"></a>이벤트 원격 분석: Application Insights 데이터 모델

[Application Insights](app-insights-overview.md)에서 이벤트 원격 분석 항목을 만들어 응용 프로그램에서 발생한 이벤트를 나타낼 수 있습니다. 일반적으로 이 작업은 단추 클릭 또는 주문 체크 아웃과 같은 사용자 조작입니다. 초기화 또는 구성 업데이트 같은 응용 프로그램 수명 주기 이벤트일 수도 있습니다. 

의미상 이벤트는 요청과 상호 관련되거나 그렇지 않을 수 있습니다. 하지만 제대로 사용될 경우 이벤트 원격 분석은 요청이나 추적보다 더 중요합니다. 이벤트는 비즈니스 원격 분석을 나타내며, 별도의 덜 공격적인 [샘플링](app-insights-api-filtering-sampling.md)의 대상이어야 합니다.

## <a name="name"></a>Name

이벤트 이름입니다. 적절한 그룹화 및 유용한 메트릭을 허용하려면 적은 수의 별도 이벤트 이름을 생성하도록 응용 프로그램을 제한합니다. 예를 들어 생성된 이벤트 인스턴스마다 별도의 이름을 사용하지 않습니다.

최대 길이: 512자

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>사용자 지정 측정

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>다음 단계

- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.
- [사용자 지정 이벤트 원격 분석을 작성합니다](app-insights-api-custom-events-metrics.md#trackevent).
- Application Insights에서 지원되는 [플랫폼](app-insights-platforms.md)을 확인합니다.
