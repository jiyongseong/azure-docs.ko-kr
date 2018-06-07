---
title: Windows에서 Azure 마이크로 서비스 디버그 | Microsoft Docs
description: 로컬 개발 컴퓨터에서 Microsoft Azure 서비스 패브릭을 사용하여 작성된 서비스를 모니터링하고 진단하는 방법에 대해 알아보세요.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 93cf4985e94c0af480d9f4e2e38b792cffe4df6e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

모니터링, 감지, 진단 및 문제 해결을 통해 사용자 환경에 미치는 영향을 최소화하면서 서비스를 계속할 수 있습니다. 실제 배포된 프로덕션 환경에서 모니터링 및 진단이 매우 중요한데, 실제 사용 설정으로 이전했을 때 정상적으로 작동하도록 서비스 개발 과정에서 얼마나 유사한 모델을 채택하느냐에 따라 그 효율성이 좌우됩니다. 서비스 패브릭을 사용하면 서비스 개발자가 단일 컴퓨터 로컬 개발 설정과 실제 사용 프로덕션 클러스터 설정 양쪽에서 매끄럽게 작동하는 진단 기능을 간편하게 구현할 수 있습니다.

## <a name="event-tracing-for-windows"></a>Windows용 이벤트 추적
[Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW)은 서비스 패브릭의 추적 메시지용으로 바람직한 기술입니다. ETW를 사용하면 다음과 같은 이점이 있습니다.

* **ETW는 속도가 빠릅니다.** 코드 실행 시간에 미치는 영향을 최소화하도록 설계된 추적 기술입니다.
* **ETW 추적은 로컬 개발 환경 및 실제 사용 클러스터 설정에서도 매끄럽게 작동합니다.** 즉, 실제 클러스터에 코드를 배포할 준비가 되었을 때 추적 코드를 다시 쓸 필요가 없습니다.
* **서비스 패브릭은 내부 추적에도 ETW를 사용합니다.** 따라서 서비스 패브릭 시스템 추적으로 인터리브된 응용 프로그램 추적을 볼 수 있습니다. 또한 기본 시스템에서 응용 프로그램 코드와 이벤트 간의 시퀀스 및 상호 관계를 보다 쉽게 이해할 수 있습니다.
* **서비스 패브릭 Visual Studio 도구는 ETW 이벤트 보기를 내부적으로 지원합니다.** Service Fabric을 통해 Visual Studio를 올바르게 구성하고 나면 ETW 이벤트가 Visual Studio의 진단 이벤트 보기에 표시됩니다. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visual Studio에서 서비스 패브릭 시스템 이벤트 보기
서비스 패브릭은 ETW 이벤트를 내보내서 응용 프로그램 개발자가 플랫폼에서 일어나는 일을 이해할 수 있도록 도와줍니다. 아직 응용 프로그램을 만들지 않은 경우 [Visual Studio에서 응용 프로그램 처음 만들기](service-fabric-create-your-first-application-in-visual-studio.md)의 단계에 따라 지금 만드세요. 이 정보는 응용 프로그램이 실행되는 동안 진단 이벤트 뷰어에 추적 메시지를 표시하는 데 도움이 될 것입니다.

1. 진단 이벤트 창이 자동으로 표시되지 않으면, Visual Studio에서 **보기** 탭으로 이동하여 **다른 창**, **진단 이벤트 뷰어**를 차례로 선택합니다.
2. 각 이벤트는 이벤트가 기인하는 노드, 응용 프로그램 및 서비스를 알려 주는 표준 메타데이터 정보를 가지고 있습니다. 이벤트 창 상단의 **이벤트 필터링** 상자를 사용하여 이벤트 목록을 필터링할 수도 있습니다. 예를 들어 **노드 이름**이나 **서비스 이름**으로 필터링할 수 있습니다. 또한 이벤트 상세 정보를 볼 때 이벤트 창 상단의 단추를 사용하여 **일시 중지**하고 나중에 이벤트 손실 없이 재개할 수 있습니다.
   
   ![Visual Studio 진단 이벤트 뷰어](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>응용 프로그램 코드에 독자적인 사용자 지정 추적 추가
서버 패브릭 Visual Studio 프로젝트 템플릿에는 샘플 코드가 포함됩니다. 이 코드는 서비스 패브릭에서 시스템 추적과 함께 Visual Studio ETW 뷰어에 표시되는 사용자 지정 응용 프로그램 코드 ETW 추적을 추가하는 방법을 보여 줍니다. 이 방법의 장점은 메타데이터가 추적에 자동으로 추가되고 Visual Studio 진단 이벤트 뷰어가 이를 표시하도록 이미 구성되어 있다는 점입니다.

**서비스 템플릿**(상태 비저장 또는 상태 저장)에서 만들어진 프로젝트의 경우 `RunAsync` 구현을 검색하기만 하면 됩니다.

1. `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` 호출은 응용 프로그램 코드에서 사용자 지정 ETW 추적의 예를 보여 줍니다.
2. **ServiceEventSource.cs`ServiceEventSource.ServiceMessage` 파일을 보면 성능상의 이유로 빈도가 높은 이벤트에 사용해야 하는** 메서드에서 오버로드를 확인할 수 있습니다.

**행위자 템플릿** (상태 비저장 또는 상태 저장)에서 만들어진 프로젝트의 경우:

1. **ProjectName** 이 Visual Studio 프로젝트용으로 선택한 이름인 *“ProjectName”.cs* 파일을 엽니다.  
2. *DoWorkAsync* 메서드에서 `ActorEventSource.Current.ActorMessage(this, "Doing Work");` 코드를 찾습니다.  이는 응용 프로그램 코드에서 작성된 사용자 지정 ETW의 예제입니다.  
3. **ActorEventSource.cs** 파일을 보면 성능상의 이유로 빈도가 높은 이벤트에 사용해야 하는 `ActorEventSource.ActorMessage` 메서드에서 오버로드를 확인할 수 있습니다.

서비스 코드에 사용자 지정 ETW 추적을 추가한 다음에는 응용 프로그램을 다시 빌드, 배포, 실행하여 진단 이벤트 뷰어에서 이벤트를 볼 수 있습니다. **F5**키를 눌러서 응용 프로그램을 디버깅하면 진단 이벤트 뷰어가 자동으로 열립니다.

## <a name="next-steps"></a>다음 단계
위에서 로컬 진단을 위해 응용 프로그램에 추가한 것과 동일한 추적 코드는 Azure 클러스터에서 응용 프로그램을 실행할 때 이 이벤트를 보는 데 이용할 수 있는 도구와 함께 작동합니다. 도구에 대한 다양한 옵션과 도구를 설정하는 방법에 대해 설명하는 이러한 문서를 확인합니다.

* [Azure 진단을 사용하여 로그를 수집하는 방법](service-fabric-diagnostics-how-to-setup-wad.md)
* [EventFlow를 사용하여 이벤트 집계 및 수집](service-fabric-diagnostics-event-aggregation-eventflow.md)

