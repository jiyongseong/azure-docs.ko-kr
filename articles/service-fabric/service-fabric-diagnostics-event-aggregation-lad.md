---
title: Linux Azure 진단을 사용하여 Azure Service Fabric 이벤트 집계 | Microsoft Docs
description: Azure Service Fabric 클러스터 모니터링 및 진단을 위해 LAD를 사용하여 이벤트를 집계 및 수집하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 681a29776914263c62b9887e4d8dafb715cd14e4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Linux Azure 진단을 사용하여 이벤트 집계 및 수집
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스의 문제를 분석하고 해결하는 데 도움이 됩니다.

로그를 업로드 및 수집하는 방법 중 하나는 LAD(Linux Azure 진단) 확장을 사용하는 것입니다. 이 확장은 Azure Storage에 로그를 업로드하고 Azure Application Insights 또는 Event Hubs에 로그를 보낼 수 있는 옵션을 제공합니다. 또한 외부 프로세스를 사용하여 저장소의 이벤트를 읽고 분석 플랫폼 제품(예: [Log Analytics](../log-analytics/log-analytics-service-fabric.md) 또는 기타 로그 구문 분석 솔루션)에 배치할 수 있습니다.

## <a name="log-and-event-sources"></a>로그 및 이벤트 원본

### <a name="service-fabric-platform-events"></a>Service Fabric 플랫폼 이벤트
Service Fabric은 운영 이벤트 또는 런타임 이벤트를 포함하여 [LTTng](http://lttng.org)를 통해 몇 가지 기본 제공 로그를 생성합니다. 이러한 로그는 클러스터의 Resource Manager 템플릿에서 지정한 위치에 저장됩니다. 저장소 계정 세부 정보를 가져오거나 설정하려면 **AzureTableWinFabETWQueryable** 태그를 찾아서 **StoreConnectionString**을 검색합니다.

### <a name="application-events"></a>응용 프로그램 이벤트
 소프트웨어를 계측할 때 지정한 대로 응용 프로그램 및 서비스 코드에서 발생되는 이벤트입니다. 텍스트 기반 로그 파일을 작성하는 모든 로깅 솔루션을 사용할 수 있습니다(예: LTTng). 자세한 내용은 응용 프로그램 추적에 대한 LTTng 설명서를 참조하세요.

[로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>진단 확장 배포
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 진단 확장을 배포하는 것입니다. 진단 확장은 각 VM에서 로그를 수집하여 사용자가 지정하는 저장소 계정에 업로드합니다. 

클러스터 만들기의 일환으로 클러스터 내의 VM에 진단 확장을 배포하려면 **진단**을 **켜기**로 설정합니다. 클러스터를 만든 후에는 포털을 사용하여 이 설정을 변경할 수는 없으므로 Resource Manager 템플릿에서 올바른 변경을 수행해야 합니다.

이는 지정된 로그 파일을 모니터링하도록 LAD 에이전트를 구성합니다. 새 줄이 파일에 추가될 때마다 이 에이전트는 사용자가 지정한 저장소(테이블)로 전송되는 syslog 항목을 만듭니다.


## <a name="next-steps"></a>다음 단계

1. 문제를 해결하는 동안 살펴봐야 하는 이벤트에 대해 자세히 알아보려면 [LTTng 설명서](http://lttng.org/docs) 및 [LAD 사용](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.
2. [Log Analytics 에이전트를 설정](service-fabric-diagnostics-event-analysis-oms.md)하여 메트릭을 수집하고, 클러스터에 배포되는 컨테이너를 모니터링하고 로그를 시각화합니다. 