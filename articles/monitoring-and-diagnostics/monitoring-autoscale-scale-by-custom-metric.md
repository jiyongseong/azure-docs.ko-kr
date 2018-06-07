---
title: "Azure에서 사용자 지정 메트릭을 기준으로 자동 크기 조정 시작 Microsoft Docs"
description: "Azure에서 사용자 지정 메트릭을 기준으로 리소스 크기를 조정하는 방법에 대해 알아봅니다."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: 72b6a68d0dbad4639f21aa701ec4865f36409f0a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Azure에서 사용자 지정 메트릭을 기준으로 자동 크기 조정 시작
이 문서에서는 Azure Portal에서 사용자 지정 메트릭을 기준으로 리소스 크기를 조정하는 방법에 대해 설명합니다.

Azure Monitor 자동 크기 조정은 VMSS(Virtual Machine Scale Sets), 클라우드 서비스 및 앱 서비스 계획 및 앱 서비스 환경에만 적용됩니다. 

# <a name="lets-get-started"></a>시작
이 문서에서는 Application Insights가 구성된 웹앱이 있다고 가정합니다. 아직 없으면 [ASP.NET 웹 사이트에 대한 Application Insights를 설정할 수 있습니다][1].

- [Azure Portal][2]을 엽니다.
- 왼쪽 탐색 창에서 Azure Monitor 아이콘을 클릭합니다.
  ![Azure Monitor 시작][3]
- 자동 크기 조정 설정을 클릭하여 현재 자동 크기 조정 상태와 함께 자동 크기 조정을 적용할 수 있는 모든 리소스를 확인합니다. ![Azure Monitor에서 자동 크기 조정 검색][4]
- Azure Monitor에서 '자동 크기 조정' 블레이드를 열고 크기를 조정할 리소스를 선택합니다.
> 참고: 아래 단계에서는 앱 정보가 구성된 웹앱과 연결된 앱 서비스 계획을 사용합니다.
- 리소스에 대한 크기 조정 설정 블레이드에서 현재 인스턴스 수가 하나임을 알려줍니다. '자동 크기 조정 사용'을 클릭합니다.
  ![새 웹앱에 대한 크기 조정 설정][5]
- 크기 조정 설정의 이름을 제공하고 "규칙 추가"를 클릭합니다. 오른쪽에 열리는 컨텍스트 창에서 크기 조정 규칙 옵션이 표시됩니다. 기본적으로 리소스의 CPU 사용률이 70%를 초과하면 인스턴스 수를 하나씩 늘리는 옵션이 설정되어 있습니다. 위쪽의 메트릭 원본을 'Application Insights'로 변경하고, '리소스' 드롭다운에서 앱 정보를 선택한 다음, 크기 조정하려는 기준에 따라 사용자 지정 메트릭을 선택합니다.
  ![사용자 지정 메트릭 기준 크기 조정][6]
- 위의 단계와 마찬가지로 규모 축소하고, 사용자 지정 메트릭이 임계값 아래이면 크기 조정 수를 하나씩 줄이는 크기 조정 규칙을 추가합니다.
  ![CPU 기준 크기 조정][7]
- 인스턴스 한도를 설정합니다. 예를 들어 사용자 지정 메트릭 변동에 따라 2-5개 인스턴스 간에 크기를 조정하려면 '최소'를 '2'로, '최대'를 '5'로, '기본값'을 '2'로 설정합니다.
> 참고: 리소스 메트릭을 읽는 데 문제가 있고 현재 용량이 기본 용량보다 적으면, 리소스의 가용성을 보장하기 위해 자동 크기 조정이 기본값으로 확장됩니다. 이미 현재 용량이 기본 용량보다 많은 경우에는 자동 크기 조정이 축소되지 않습니다.
- '저장'을 클릭합니다.

축하합니다. 이제 사용자 지정 메트릭을 기준으로 웹앱의 크기를 자동으로 조정하는 크기 조정 설정을 성공적으로 만들었습니다.

> 참고: VMSS 또는 클라우드 서비스 역할을 시작하는 데에도 동일한 단계를 적용할 수 있습니다.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
