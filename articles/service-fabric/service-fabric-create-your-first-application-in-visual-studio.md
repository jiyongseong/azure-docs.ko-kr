---
title: C#에서 Azure Service Fabric Reliable Service 만들기
description: Visual Studio를 사용하여 Azure Service Fabric을 기반으로 하는 Reliable Services 응용 프로그램을 만들고, 배포하고, 디버그합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2018
ms.author: ryanwi
ms.openlocfilehash: 7e64bc34f5c39edaf87cc732d7c4702655df0e3e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212673"
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>첫 번째 C# Service Fabric 상태 저장 Reliable Services 응용 프로그램 만들기

Windows에서 첫 번째 .NET용 Azure Service Fabric 응용 프로그램을 몇 분 만에 배포하는 방법에 대해 알아봅니다. 배포가 완료되면 Reliable Services 응용 프로그램으로 실행되는 로컬 클러스터가 제공됩니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 [개발 환경이 설정](service-fabric-get-started.md)되었는지 확인합니다. 이 프로세스에는 Service Fabric SDK 및 Visual Studio 2017 또는 2015 설치가 포함됩니다.

## <a name="create-the-application"></a>응용 프로그램 만들기

1. 관리자 권한으로 Visual Studio를 시작합니다.

2. Ctrl+Shift+N을 선택하여 프로젝트를 만듭니다.

3. **새 프로젝트** 대화 상자에서 **클라우드** > **Service Fabric 응용 프로그램**을 차례로 선택합니다.

4. 응용 프로그램의 이름을 **MyApplication**으로 지정합니다. 그런 다음 **확인**을 선택합니다.

   ![Visual Studio의 새 프로젝트 대화 상자][1]

5. 다음 대화 상자에서 모든 유형의 Service Fabric 응용 프로그램을 만들 수 있습니다. 이 빠른 시작에서는 **.Net Core 2.0** > **상태 저장 서비스**를 선택합니다.

6. 서비스의 이름을 **MyStatefulService**로 지정합니다. 그런 다음 **확인**을 선택합니다.

    ![Visual Studio의 새 서비스 대화 상자][2]

    Visual Studio에서 응용 프로그램 프로젝트 및 상태 저장 서비스 프로젝트를 만듭니다. 그러면 [솔루션 탐색기]에 표시됩니다.

    ![상태 저장 서비스와 함께 응용 프로그램을 만든 솔루션 탐색기][3]

    응용 프로그램 프로젝트(**MyApplication**)에는 코드가 없습니다. 대신 서비스 프로젝트의 집합을 참조합니다. 또한 세 가지 다른 형식의 콘텐츠도 있습니다.

    * **게시 프로필**  
    다양한 환경에 배포하기 위한 프로필

    * **스크립트**  
    응용 프로그램을 배포하거나 업그레이드하기 위한 PowerShell 스크립트입니다.

    * **응용 프로그램 정의**  
*ApplicationPackageRoot* 아래에 응용 프로그램의 구성을 설명하는 ApplicationManifest.xml 파일을 포함합니다. 연결된 응용 프로그램 매개 변수 파일은 *ApplicationParameters* 아래에 있으며 환경 관련 매개 변수를 지정하는 데 사용할 수 있습니다. Visual Studio에서 연결된 게시 프로필에 지정된 응용 프로그램 매개 변수 파일을 선택합니다.
    
서비스 프로젝트의 내용에 대한 개요는 [Reliable Services 시작](service-fabric-reliable-services-quick-start.md)을 참조하세요.

## <a name="deploy-and-debug-the-application"></a>응용 프로그램 배포 및 디버깅

이제 다음 단계에 따라 응용 프로그램을 만들고, 실행하고, 배포하고, 디버그할 수 있습니다.

1. Visual Studio에서 F5를 선택하여 디버그할 응용 프로그램을 배포합니다.

    >[!NOTE]
    >로컬에서 처음으로 응용 프로그램을 실행하고 배포할 때 Visual Studio는 디버깅을 위해 로컬 클러스터를 만듭니다. 이 작업에는 시간이 약간 걸릴 수 있습니다. Visual Studio 출력 창에 클러스터 생성 상태가 표시됩니다.

    클러스터가 준비되면 SDK에 포함된 로컬 클러스터 시스템 트레이 관리자 응용 프로그램에서 알림을 받습니다.
    
    ![로컬 클러스터 시스템 트레이 알림][4]

    응용 프로그램이 시작되면 Visual Studio에서 자동으로 진단 이벤트 뷰어를 표시하여 서비스의 추적 출력을 확인할 수 있습니다.
    
    ![진단 이벤트 뷰어][5]

    >[!NOTE]
    >이벤트는 진단 이벤트 뷰어에서 자동으로 추적을 시작해야 합니다. 진단 이벤트 뷰어를 수동으로 구성해야 하는 경우 먼저 **MyStatefulService** 프로젝트에 있는 `ServiceEventSource.cs` 파일을 엽니다. `ServiceEventSource` 클래스 위쪽에 있는 `EventSource` 특성 값을 복사합니다. 다음 예제에서 이벤트 원본은 `"MyCompany-MyApplication-MyStatefulService"`라고 하며, 상황에 따라 달라질 수 있습니다.
>
    >![서비스 이벤트 원본 이름 찾기][service-event-source-name]


2. 다음으로, **ETW 공급자** 대화 상자를 엽니다. 여기서 **진단 이벤트** 탭에 있는 기어 아이콘을 선택합니다. 복사한 이벤트 원본의 이름을 **ETW 공급자** 입력 상자에 붙여넣습니다. 그런 다음, **적용** 단추를 선택합니다. 그러면 이벤트 추적이 자동으로 시작됩니다.

    ![진단 이벤트 원본 이름 설정][setting-event-source-name]

    이제 진단 이벤트 창에 이벤트가 표시됩니다.

    상태 저장 서비스 템플릿에서 **MyStatefulService.cs**의 `RunAsync` 메서드에서 증분하는 카운터 값을 표시합니다.

3. 코드가 실행되는 노드를 포함하여 자세한 정보를 보려면 이벤트 중 하나를 확장합니다. 이 경우 컴퓨터에서 다를 수 있지만 **\_Node\_0,** 입니다.
   
    ![진단 이벤트 뷰어 세부 정보][6]

4. 로컬 클러스터에는 단일 컴퓨터에서 호스팅되는 다섯 개의 노드가 있습니다. 프로덕션 환경에서 각 노드는 고유한 물리적 컴퓨터 또는 가상 머신에서 호스팅됩니다. Visual Studio 디버거를 실행하는 동안 컴퓨터의 손실을 시뮬레이션하려면 로컬 클러스터의 노드 중 하나를 해제합니다.

5. **솔루션 탐색기** 창에서 **MyStatefulService.cs**를 엽니다. 

6. `RunAsync` 메서드를 찾은 다음, 메서드의 첫 번째 줄에 중단점을 설정합니다.

    ![상태 저장 서비스 RunAsync 메서드의 중단점][7]

7. **로컬 클러스터 관리자** 시스템 트레이 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음, **로컬 클러스터 관리**를 선택하여 Service Fabric Explorer 도구를 시작합니다.

    ![로컬 클러스터 관리자에서 Service Fabric Explorer 시작][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 클러스터에 대한 시각적 표현을 제공합니다. 여기에는 배포된 응용 프로그램 집합 및 이를 구성하는 실제 노드 집합이 포함되어 있습니다.

8. 왼쪽 창에서 **클러스터** > **노드**를 차례로 펼치고, 코드가 실행될 노드를 찾습니다. 그런 다음, 시스템 다시 시작을 시뮬레이션하기 위해 **작업** > **비활성화(다시 시작)** 를 차례로 선택합니다.

    ![서비스 패브릭 탐색기에서 노드 중지][sfx-stop-node]

    한 노드에서 원활하게 수행한 계산이 다른 노드에 장애 조치되면 일시적으로 Visual Studio에서 중단점을 확인해야 합니다.

9. 다음으로 진단 이벤트 뷰어로 돌아가서 메시지를 관찰합니다. 실제로 다른 노드에서 이벤트가 들어오더라도 카운터는 계속 증분합니다.

    ![장애 조치 후 진단 이벤트 뷰어][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>로컬 클러스터 정리(선택 사항)

이 로컬 클러스터는 실제입니다. 디버거를 중지하면 응용 프로그램 인스턴스를 제거하고 응용 프로그램 형식의 등록을 취소합니다. 하지만 클러스터는 백그라운드에서 계속 실행됩니다. 로컬 클러스터를 중지할 준비가 되면 몇 가지 옵션이 표시됩니다.

### <a name="keep-application-and-trace-data"></a>응용 프로그램 및 추적 데이터 유지

**로컬 클러스터 관리자** 시스템 트레이 응용 프로그램을 마우스 오른쪽 단추로 클릭한 다음, **로컬 클러스터 중지**를 선택하여 클러스터를 종료합니다.

### <a name="delete-the-cluster-and-all-data"></a>클러스터 및 모든 데이터 삭제

**로컬 클러스터 관리자** 시스템 트레이 응용 프로그램을 마우스 오른쪽 단추로 클릭하여 클러스터를 제거합니다. 그런 다음, **로컬 클러스터 제거**를 선택합니다. 

이 옵션을 선택하면 Visual Studio에서 다음에 응용 프로그램을 실행할 때 해당 클러스터를 다시 배포합니다. 잠시 동안 로컬 클러스터를 사용하지 않으려거나 리소스를 회수해야 하는 경우 이 옵션을 선택합니다.

## <a name="next-steps"></a>다음 단계
[Reliable Services](service-fabric-reliable-services-introduction.md)에 대해 자세히 알아봅니다.
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
