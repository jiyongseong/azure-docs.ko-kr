---
title: Azure 진단 확장 개요 | Microsoft Docs
description: 클라우드 서비스, 가상 머신 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용
services: multiple
documentationcenter: .net
author: rboucher
manager: ''
editor: ''
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/01/2018
ms.author: robb
ms.openlocfilehash: daeaddefa461e71fcc62af4efc4fb7084b237cf9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886398"
---
# <a name="what-is-azure-diagnostics-extension"></a>Azure 진단 확장이란?
Azure 진단 확장은 배포된 응용 프로그램에서 진단 데이터를 수집할 수 있도록 하는 Azure 내의 에이전트입니다. 다양한 원본에서 진단 확장을 사용할 수 있습니다. Azure Cloud Service(클래식) 웹 및 작업자 역할, Virtual Machines, 가상 머신 확장 집합 및 Service Fabric에서 현재 지원되고 있습니다. 다른 Azure 서비스에는 여러 진단 메서드가 있습니다. [Azure의 모니터링 개요](monitoring-overview.md)를 참조하세요. 

## <a name="linux-agent"></a>Linux 에이전트
[Linux 버전의 확장](../virtual-machines/linux/diagnostic-extension.md)은 Linux를 실행하는 Virtual Machines에 사용할 수 있습니다. 수집되는 통계 및 동작은 Windows 버전에 따라 다릅니다. 

## <a name="data-you-can-collect"></a>수집할 수 있는 데이터
Azure 진단 확장은 다음과 같은 유형의 데이터를 수집할 수 있습니다.

| 데이터 원본 | 설명 |
| --- | --- |
| 성능 카운터 |운영 체제 및 사용자 지정 성능 카운터 |
| 응용 프로그램 로그 |응용 프로그램에서 작성한 메시지 추적 |
| Windows 이벤트 로그 |Windows 이벤트 로깅 시스템으로 전송된 정보 |
| .NET 이벤트 원본 |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 클래스를 사용하여 이벤트를 작성하는 코드 |
| IIS 로그 |IIS 웹 사이트에 대한 정보 |
| 매니페스트 기반 ETW |모든 프로세스에서 생성된 Windows 이벤트용 이벤트 추적 |
| 크래시 덤프 |응용 프로그램 크래시가 발생할 경우의 프로세스 상태에 대한 정보 |
| 사용자 지정 오류 로그 |응용 프로그램 또는 서비스에서 생성한 로그 |
| Azure 진단 인프라 로그 |진단 자체에 대한 정보입니다. |

## <a name="data-storage"></a>데이터 저장소
확장은 해당 데이터를 지정한 [Azure Storage 계정](azure-diagnostics-storage.md)에 저장합니다. 

이 데이터를 [Application Insights](../application-insights/app-insights-cloudservices.md)에 보낼 수도 있습니다. 또한 [이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)로 스트리밍할 수도 있습니다. 그러면 비 Azure 모니터링 서비스에 보낼 수 있습니다. 


## <a name="versioning-and-configuration-schema"></a>버전 관리 및 구성 스키마
[Azure 진단 버전 기록 및 스키마](azure-diagnostics-versioning-history.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
진단을 수집할 서비스를 선택하고 다음 문서를 사용하여 시작합니다. 특정 작업에 대한 참조로 일반 Azure 진단 링크를 사용합니다.

## <a name="cloud-services-using-azure-diagnostics"></a>Azure 진단을 사용하는 Cloud Services
* Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 Cloud Services 응용 프로그램 추적](../vs-azure-tools-debug-cloud-services-virtual-machines.md) 을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
* [Azure 진단을 사용하여 클라우드 서비스를 모니터링하는 방법](../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services 응용 프로그램에서 Azure 진단 설정](../cloud-services/cloud-services-dotnet-diagnostics.md)

고급 항목의 경우 다음을 참조하세요.

* [Cloud Services용 Application Insights에서 Azure 진단 사용](../application-insights/app-insights-cloudservices.md)
* [Azure 진단으로 Cloud Services 응용 프로그램의 흐름 추적](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell을 사용하여 Cloud Services에 진단 설정](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 Azure Virtual Machines 추적](../vs-azure-tools-debug-cloud-services-virtual-machines.md)을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
* [Azure Virtual Machine에서 Azure 진단 설정](../virtual-machines-dotnet-diagnostics.md)

고급 항목의 경우 다음을 참조하세요.

* [PowerShell을 사용하여 Azure Virtual Machines에서 진단 설정](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 기능으로 Windows 가상 머신 만들기](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
[Service Fabric 응용 프로그램 모니터링](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)에서 시작합니다. 이 문서를 사용하면 왼쪽의 탐색 트리에서 다른 여러 서비스 패브릭 진단 문서를 사용할 수 있습니다.

## <a name="general-articles"></a>일반 문서
* [Azure 진단에서 성능 카운터 사용](../cloud-services/diagnostics-performance-counters.md)에 대해 알아봅니다.
* 진단을 시작하거나 Azure Storage 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure 진단 문제 해결](azure-diagnostics-troubleshooting.md)을 참조하세요.
