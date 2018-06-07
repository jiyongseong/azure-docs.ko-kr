---
title: Visual Studio에서 응용 프로그램 디버그 | Microsoft Docs
description: 로컬 개발 클러스터의 Visual Studio에서 개발하고 디버그하여 서비스의 안정성과 성능을 향상시킵니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4582fd16d08ae8d51460dc8cabfd282e1f2cd43e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Visual Studio를 사용하여 서비스 패브릭 응용 프로그램 디버그
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>로컬 서비스 패브릭 응용 프로그램 디버깅
로컬 컴퓨터 개발 클러스터에서 Azure 서비스 패브릭 응용 프로그램을 배포하고 디버그하여 시간과 비용을 절약할 수 있습니다. Visual Studio 2017 또는 Visual Studio 2015는 로컬 클러스터에 응용 프로그램을 배포하고 응용 프로그램의 모든 인스턴스에 디버거를 자동으로 연결할 수 있습니다.

1. [서비스 패브릭 개발 환경 설정](service-fabric-get-started.md)의 단계를 따라 로컬 개발 클러스터를 시작합니다.
2. **F5** 키를 누르거나 **디버그** > **디버깅 시작**을 클릭합니다.
   
    ![응용 프로그램 디버깅 시작][startdebugging]
3. 코드의 중단점을 설정하고 **디버그** 메뉴의 명령을 클릭하여 응용 프로그램의 단계를 진행합니다.
   
   > [!NOTE]
   > Visual Studio는 응용 프로그램의 모든 인스턴스에 연결합니다. 단계별로 코드를 실행하는 동안 중단점은 동시 세션에서 발생하는 여러 프로세스에 히트될 수 있습니다. 스레드 ID의 각 중단점을 조건부로 생성하거나 진단 이벤트를 사용하여 히트된 후에 중단점을 사용하지 않도록 설정합니다.
   > 
   > 
4. **진단 이벤트** 창이 자동으로 열려서 실시간으로 진단 이벤트를 볼 수 있습니다.
   
    ![실시간으로 진단 이벤트 보기][diagnosticevents]
5. 클라우드 탐색기의 **진단 이벤트** 창도 열 수 있습니다.  **Service Fabric**에서 아무 노드를 마우스 오른쪽 단추로 클릭하고 **스트리밍 추적 보기**를 선택합니다.
   
    ![진단 이벤트 창 열기][viewdiagnosticevents]
   
    특정 서비스 또는 응용 프로그램에 대해 추적을 필터링하려는 경우, 간단히 해당 특정 서비스 또는 응용 프로그램에서 스트리밍 추적을 사용하면 됩니다.
6. 진단 이벤트는 자동으로 생성되는 **ServiceEventSource.cs** 파일에서 볼 수 있으며 응용 프로그램 코드에서 호출됩니다.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **진단 이벤트** 창은 필터링, 일시 중지 및 실시간 이벤트 검사를 지원합니다.  필터는 해당 콘텐츠를 포함하는 이벤트 메시지의 단순 문자열 검색입니다.
   
    ![실시간으로 이벤트를 필터링, 일시 중지, 다시 시작 또는 검사합니다.][diagnosticeventsactions]
8. 서비스 디버깅은 다른 모든 응용 프로그램의 디버깅과 같습니다. 손쉬운 디버깅을 위해 일반적으로 Visual Studio를 통해 중단점을 설정할 수 있습니다. 신뢰할 수 있는 컬렉션은 여러 노드에 걸쳐 복제하더라도 여전히 IEnumerable을 구현합니다. 따라서 디버그하는 동안 Visual Studio에서 결과 뷰를 사용하여 내부에 저장한 내용을 볼 수 있습니다. 코드의 아무 곳에나 중단점을 설정하기만 하면 됩니다.
   
    ![응용 프로그램 디버깅 시작][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>원격 서비스 패브릭 응용 프로그램 디버깅
Azure의 서비스 패브릭 클러스터에서 서비스 패브릭 응용 프로그램이 실행 중인 경우, Visual Studio에서 직접 원격으로 이를 디버깅할 수 있습니다.

> [!NOTE]
> 이 기능은 [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 및 [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/)가 필요합니다.    
> 
> 

<!-- -->
> [!WARNING]
> 원격 디버깅은 실행 중인 응용 프로그램에 영향을 미치기 때문에 개발/테스트 시나리오를 위한 것이며 프로덕션 환경에서는 사용되지 않습니다.
> 
> 

1. **Cloud Explorer**에 있는 클러스터로 이동하여, 마우스 오른쪽 단추로 클릭하고 **디버깅 사용**을 선택합니다.
   
    ![원격 디버깅 사용][enableremotedebugging]
   
    이는 필수 네트워크 구성뿐만 아니라 클러스터 노드에서 원격 디버깅 확장을 사용하는 프로세스를 시작합니다.
2. **Cloud Explorer**에 있는 클러스터 노드를 마우스 오른쪽 단추로 클릭하고 **디버거 연결**을 선택합니다.
   
    ![디버거 연결][attachdebugger]
3. **프로세스에 연결** 대화 상자에서 디버깅하려는 프로세스를 선택하고 **연결**을 클릭하세요.
   
    ![프로세스 선택][chooseprocess]
   
    연결하려는 프로세스의 이름은 서비스 프로젝트 어셈블리 이름과 일치합니다.
   
    디버거는 프로세스를 실행하는 모든 노드에 연결됩니다.
   
   * 상태 비저장 서비스를 디버깅하는 경우, 모든 노드에서 서비스의 모든 인스턴스는 디버그 세션의 일부입니다.
   * 상태 저장 서비스를 디버깅하는 경우, 파티션의 주 복제본에만 활성화되고 디버거에 의해 검색됩니다. 디버그 세션 중에 주 복제본이 이동한 경우, 해당 복제본의 처리는 여전히 디버그 세션의 일부입니다.
   * 지정된 서비스의 관련 파티션 또는 인스턴스만 캐치하려면 조건부 중단점을 사용하여 특정 파티션 또는 인스턴스만 중단할 수 있습니다.
     
     ![조건부 중단점][conditionalbreakpoint]
     
     > [!NOTE]
     > 현재 동일한 서비스 실행 파일 이름의 인스턴스가 다수인 패브릭 클러스터 디버깅을 지원하지 않습니다.
     > 
     > 
4. 응용 프로그램 디버깅을 완료하면 **Cloud Explorer**에 있는 클러스터를 마우스 오른쪽 단추로 클릭하여 **디버깅 사용 안 함**을 선택하여 원격 디버깅 확장을 사용하지 않을 수 있습니다.
   
    ![원격 디버깅 사용 안 함][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>원격 클러스터 노드에서 스트리밍 추적
또한 원격 클러스터 노드에서 Visual Studio까지 추적을 직접 스트리밍할 수 있습니다. 이 기능을 통해 Service Fabric 클러스터 노드에서 생성된 ETW 추적 이벤트를 스트리밍할 수 있습니다.

> [!NOTE]
> 이 기능은 [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 및 [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/)가 필요합니다.
> 이 기능은 Azure에서 실행되는 클러스터만 지원합니다.
> 
> 

<!-- -->
> [!WARNING]
> 스트리밍 추적은 실행 중인 응용 프로그램에 영향을 미치기 때문에 개발/테스트 시나리오를 위한 것이며 프로덕션 환경에서는 사용되지 않습니다.
> 프로덕션 시나리오에서는 Azure 진단을 통해 이벤트 전달을 사용해야 합니다.
> 
> 

1. **Cloud Explorer**에 있는 클러스터로 이동하여, 마우스 오른쪽 단추로 클릭하고 **스트리밍 추적 사용**을 선택합니다.
   
    ![원격 스트리밍 추적 사용][enablestreamingtraces]
   
    이는 필수 네트워크 구성뿐만 아니라 클러스터 노드에서 스트리밍 추적 확장을 사용하는 프로세스를 시작합니다.
2. **Cloud Explorer**에 있는 **노드** 요소를 확장하고, 추적을 스트리밍하려는 노드를 마우스 오른쪽 단추로 클릭하고 **스트리밍 추적 보기**를 선택합니다.
   
    ![원격 스트리밍 추적 보기][viewremotestreamingtraces]
   
    추적을 보려는 노드 개수만큼 2단계를 반복합니다. 각 노드 스트림은 전용 창에 표시됩니다.
   
    이제 서비스 패브릭에서 내보낸 추적과 서비스를 볼 수 있습니다. 특정 응용 프로그램에만 표시하도록 이벤트를 필터링하려는 경우, 필터에서 응용 프로그램의 이름만 간단히 입력하면 됩니다.
   
    ![스트리밍 추적 보기][viewingstreamingtraces]
3. 클러스터에서 스트리밍 추적을 완료하면 **Cloud Explorer**에 있는 클러스터를 마우스 오른쪽 단추로 클릭하고 **스트리밍 추적 사용 안 함**을 선택하여 원격 스트리밍 추적을 사용하지 않을 수 있습니다.
   
    ![원격 스트리밍 추적 사용 안 함][disablestreamingtraces]

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 서비스 테스트](service-fabric-testability-overview.md)
* [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
