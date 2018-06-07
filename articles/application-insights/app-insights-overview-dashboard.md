---
title: Azure Application Insights 개요 대시보드 | Microsoft Docs
description: Azure Application Insights 및 개요 대시보드 기능을 사용하여 응용 프로그램을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356089"
---
# <a name="application-insights-overview-dashboard-preview"></a>Application Insights 개요 대시보드(미리 보기)

Application Insights에는 응용 프로그램의 상태 및 성능을 한 눈에 빠르게 파악할 수 있도록 요약 개요 창이 항상 제공됩니다. 새 미리 보기 개요 대시보드에는 더 빠르고 유연한 환경이 제공됩니다.

## <a name="how-do-i-test-out-the-new-experience"></a>새 환경을 테스트하려면 어떻게 할까요?

 Application Insights의 _개요_에서 _기본 환경이 되기 전에 새 개요를 사용해 보세요_(Please try new Overview before it becomes the default experience)를 선택합니다.

![개요 미리 보기](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

새로운 기본 개요 대시보드가 시작됩니다.

![개요 미리 보기 창](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>향상된 성능

시간 범위 선택이 간단한 한 번의 클릭 인터페이스로 간소화되었습니다.

![시간 범위](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

성능이 전반적으로 크게 향상되었습니다. 동적으로 업데이트되는 KPI 타일이 각각 해당 Application Insights 기능에 연결됩니다. 예를 들어 실패한 요청을 선택하면 _실패_ 창이 시작됩니다.

![오류](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>응용 프로그램 대시보드

응용 프로그램 대시보드는 Azure의 기존 대시보드 기술을 활용하여 응용 프로그램의 상태와 성능을 완벽하게 사용자 지정할 수 있는 단일 창 보기를 제공합니다.

기본 대시보드에 액세스하려면 왼쪽 위 모서리에 있는 _응용 프로그램 대시보드_를 선택합니다.

![대시보드 보기](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

대시보드에 처음 액세스하는 경우 기본 보기가 시작됩니다.

![대시보드 보기](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

원하는 경우 기본 보기를 유지할 수 있지만, 팀의 요구에 가장 적합하도록 대시보드에서 추가하고 삭제할 수도 있습니다.

> [!NOTE]
> Application Insights 리소스에 액세스할 수 있는 모든 사용자는 동일한 응용 프로그램 대시보드 환경을 공유합니다. 한 명의 사용자가 대시보드를 변경하면 모든 사용자의 보기가 수정됩니다.

개요 환경으로 돌아가려면 다음을 선택합니다.

![개요 단추](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>다음 단계

- [깔때기](usage-funnels.md)
- [보존](app-insights-usage-retention.md)
- [사용자 흐름](app-insights-usage-flows.md)
- [대시보드](app-insights-dashboards.md)
