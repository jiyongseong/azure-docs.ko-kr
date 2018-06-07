---
title: Azure Service Fabric에 대해 자세히 알아보기 | Microsoft Docs
description: Azure Service Fabric의 주요 영역과 핵심 개념에 대해 알아봅니다. Service Fabric 및 마이크로 서비스를 만드는 방법의 확장된 개요를 제공합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: 1c3ea5b041cf2a961ef57bc168ae86b83412e044
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212826"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Service Fabric에 대해 궁금하신가요?
Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다.  그러나 Service Fabric은 노출 영역이 대규모이므로 학습할 내용이 많습니다.  이 문서에서는 Service Fabric의 개요를 제공하고 핵심 개념, 프로그래밍 모델, 응용 프로그램 수명 주기, 테스트, 클러스터 및 상태 모니터링에 대해 설명합니다. 내용 소개 및 Service Fabric을 사용하여 마이크로 서비스를 만드는 방법은 [개요](service-fabric-overview.md) 및 [마이크로 서비스란?](service-fabric-overview-microservices.md)을 읽어보세요. 이 문서에는 포괄적인 콘텐츠 목록이 포함되어 있지 않지만 Service Fabric의 모든 영역에 대한 개요 및 시작 문서에 대한 링크가 있습니다. 

## <a name="core-concepts"></a>핵심 개념
[Service Fabric 용어](service-fabric-technical-overview.md), [응용 프로그램 모델](service-fabric-application-model.md) 및 [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)은 기초 수준에서 자세한 개념과 설명을 제공합니다.

<table><tr><th>핵심 개념</th><th>설계 시간</th><th>실행 시간</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>디자인 타임: 응용 프로그램 형식, 서비스 형식, 응용 프로그램 패키지 및 매니페스트, 서비스 패키지 및 매니페스트
응용 프로그램 형식은 서비스 형식 컬렉션에 할당된 이름/버전입니다. 응용 프로그램 패키지 디렉터리에 포함된 *ApplicationManifest.xml* 파일에서 정의됩니다. 그런 다음 응용 프로그램 패키지는 Service Fabric 클러스터의 이미지 저장소에 복사됩니다. 그런 다음 클러스터 내에서 실행되는 이 응용 프로그램 형식으로부터 명명된 응용 프로그램을 만들 수 있습니다. 

서비스 형식은 서비스의 코드 패키지, 데이터 패키지 및 구성 패키지에 할당된 이름/버전입니다. 서비스 패키지 디렉터리에 포함된 ServiceManifest.xml 파일에서 정의됩니다. 그런 다음 서비스 패키지 디렉터리는 응용 프로그램 패키지의 *ApplicationManifest.xml* 파일에서 참조됩니다. 클러스터 내에서 명명된 응용 프로그램을 만든 후에 응용 프로그램 형식의 서비스 형식 중 하나에서 명명된 서비스를 만들 수 있습니다. 서비스 형식은 *ServiceManifest.xml* 파일로 설명되며, 런타임에 로드되는 실행 가능한 코드 서비스 구성 설정과 서비스가 소비하는 정적 데이터로 구성됩니다.

![서비스 패브릭 응용 프로그램 유형 및 서비스 유형][cluster-imagestore-apptypes]

응용 프로그램 패키지는 응용 프로그램 형식의 *ApplicationManifest.xml* 파일이 들어 있는 디스크 디렉터리로, 응용 프로그램 형식을 구성하는 각 서비스 형식에 대해 서비스 패키지를 참조합니다. 예를 들어 전자 메일 응용 프로그램 형식에 대한 응용 프로그램 패키지는 큐 서비스 패키지, 프런트 엔드 서비스 패키지 및 데이터베이스 서비스 패키지에 대한 참조를 포함할 수 있습니다. 응용 프로그램 패키지 디렉터리에 있는 파일은 Service Fabric 클러스터의 이미지 저장소에 복사됩니다. 

서비스 패키지는 서비스 형식의 *ServiceManifest.xml* 파일이 들어 있는 디스크 디렉터리로, 서비스 형식에 대한 코드, 정적 데이터 및 구성 패키지를 참조합니다. 서비스 패키지 디렉터리의 파일은 응용 프로그램 형식의 *ApplicationManifest.xml* 파일에서 참조합니다. 예를 들어, 서비스 패키지는 데이터베이스 서비스를 구성하는 코드, 정적 데이터 및 구성 패키지를 참조할 수 있습니다.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>런타임: 클러스터 및 노드, 명명된 응용 프로그램, 명명된 서비스, 파티션 및 복제본
[Service Fabric 클러스터](service-fabric-deploy-anywhere.md): 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 규모를 컴퓨터 수천 대로 확장할 수 있습니다.

클러스터의 일부인 컴퓨터나 VM을 노드라고 합니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 배치 속성과 같은 특징이 있습니다. 각 컴퓨터 또는 VM이 Windows 서비스인 `FabricHost.exe`를 자동으로 시작하여 부팅을 실행하기 시작한 다음 `Fabric.exe` 및 `FabricGateway.exe`이라는 두 개의 실행 파일을 시작합니다. 이러한 두 실행 파일이 노드를 구성합니다. 개발 또는 테스트 시나리오에서는 `Fabric.exe` 및 `FabricGateway.exe`와 같은 여러 인스턴스를 실행하여 단일 컴퓨터 또는 VM에 여러 노드를 호스트할 수 있습니다.

명명된 응용 프로그램은 하나 이상의 특정 기능을 수행하는 명명된 서비스 컬렉션입니다. 서비스는 완전한 독립 실행형 기능을 수행하며(서비스는 다른 서비스와 독립적으로 시작 및 실행 가능) 코드, 구성 및 데이터로 이루어집니다. 응용 프로그램 패키지가 이미지 저장소에 복사된 후에 응용 프로그램 패키지의 응용 프로그램 형식을 지정하여 클러스터 내에서 응용 프로그램의 인스턴스를 만듭니다(이름/버전 사용). 각 응용 프로그램 형식 인스턴스는 *fabric:/MyNamedApp*과 같은 URI 이름이 할당됩니다. 클러스터 내에서 단일 응용 프로그램 형식에서 여러 명명된 응용 프로그램을 만들 수 있습니다. 또한 다른 형식의 응용 프로그램에서 명명된 응용 프로그램을 만들 수 있습니다. 명명된 응용 프로그램은 각각 독립적으로 관리되고 버전이 지정됩니다.

명명된 응용 프로그램을 만든 후에 서비스 형식을 지정하여(이름/버전 사용) 클러스터 내에서 해당 서비스 형식(명명된 서비스)의 인스턴스를 만들 수 있습니다. 각 서비스 형식 인스턴스는 명명된 응용 프로그램의 URI로 범위가 지정된 URI 이름이 할당됩니다. 예를 들어 "MyNamedApp"이라는 응용 프로그램 내에서 "MyDatabase"라는 서비스를 만드는 경우 URI는 *fabric:/MyNamedApp/MyDatabase*와 같습니다. 명명된 응용 프로그램 내에서 하나 이상의 명명된 서비스를 만들 수 있습니다. 명명된 각 서비스는 고유한 파티션 구성표 및 복제본/인스턴스 수를 가질 수 있습니다. 

여기에는 상태 비저장과 상태 저장 등의 두 가지 서비스 형식이 있습니다. 상태 비저장 서비스는 Azure Storage, Azure SQL Database 또는 Azure Cosmos DB와 같은 외부 저장소 서비스에 영구 상태를 저장할 수 있습니다. 서비스에 영구 저장소가 없는 경우 상태 비저장 서비스를 사용합니다. Reliable Collection 또는 Reliable Actors 프로그래밍 모델을 통해 서비스의 상태를 관리하기 위해 상태 저장 서비스가 Service Fabric을 사용합니다. 

명명된 서비스를 만들 때 파티션 구성표를 지정합니다. 많은 양의 상태가 있는 서비스는 파티션에 데이터를 분할합니다. 각 파티션은 서비스의 전체 상태 중 클러스터의 노드에 분산되어 있는 부분을 담당합니다.  

다음 다이어그램에서는 응용 프로그램 및 서비스 인스턴스, 파티션, 복제본 간의 관계를 보여 줍니다.

![서비스 내의 파티션 및 복제본][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>분할, 크기 조정 및 가용성
[분할](service-fabric-concepts-partitioning.md)은 Service Fabric에만 있는 것이 아닙니다. 분할의 잘 알려진 양식은 데이터 분할 또는 분할이라고도 합니다. 많은 양의 상태가 있는 상태 저장 서비스는 파티션에 데이터를 분할합니다. 각 파티션은 서비스의 전체 상태 일부를 담당합니다. 

각 파티션의 복제본은 클러스터의 노드에 분산되어 있으므로 명명된 서비스 상태의 [크기를 조정](service-fabric-concepts-scalability.md)할 수 있습니다. 데이터 요구 사항이 늘어나고 파티션이 늘어나면 하드웨어 리소스를 효율적으로 사용할 수 있도록 Service Fabric이 노드 간에 파티션의 균형을 다시 조정합니다. 새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 파티션 복제본의 균형을 조정합니다. 전반적인 응용 프로그램 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본의 균형을 다시 조정합니다.

상태 저장 서비스에 복제본이 있는 반면 파티션 내에서 명명된 상태 비저장 서비스에는 인스턴스가 있습니다. 일반적으로 명명된 상태 비저장 서비스는 내부 상태가 없기 때문에 하나의 파티션만을 가질 수 있습니다. 파티션 인스턴스는 [가용성](service-fabric-availability-services.md)을 제공합니다. 한 인스턴스가 실패하고 다른 인스턴스가 계속 정상적으로 작동하는 경우 Service Fabric은 새 인스턴스를 만듭니다. 명명된 상태 저장 서비스는 복제본 내에서 해당 상태를 유지하고 각 파티션에는 고유한 복제본 세트가 있습니다. 읽기 및 쓰기 작업은 하나의 복제본(주라고 함)에서 수행됩니다. 쓰기 작업에 의한 상태 변경은 다른 여러 복제본(활성 보조라고 함)으로 복제됩니다. 복제가 실패하면 서비스 패브릭은 기존 복제본에서 새 복제본을 작성해야 합니다.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric용 상태 비저장 및 상태 저장 마이크로 서비스
Service Fabric을 사용하면 마이크로 서비스 또는 컨테이너로 구성된 응용 프로그램을 빌드할 수 있습니다. 상태 비저장 마이크로 서비스(프로토콜 게이트웨이, 웹 프록시 등)는 서비스의 요청 및 응답 이외에 변경 가능한 상태를 관리하지 않습니다. Azure Cloud Services 작업자 역할이 상태 비저장 서비스의 예입니다. 상태 저장 마이크로 서비스(사용자 계정, 데이터베이스, 장치, 쇼핑 카트, 큐 등)는 요청 및 응답 이외에 변경 가능하고 신뢰할 수 있는 상태를 관리합니다. 오늘날 인터넷 범위의 서비스는 상태 비저장 및 상태 저장 마이크로 서비스의 조합으로 구성됩니다. 

Service Fabric의 주요 차이점은 [기본 제공 프로그래밍 모델 ](service-fabric-choose-framework.md) 또는 컨테이너화된 상태 저장 서비스 중 하나를 사용하여 상태 저장 서비스를 빌드하는 데 집중한다는 것입니다. [응용 프로그램 시나리오](service-fabric-application-scenarios.md)는 상태 저장 서비스를 사용하는 시나리오를 설명합니다.

상태 저장 마이크로 서비스가 상태 비저장 마이크로 서비스와 함께 있는 이유는 무엇인가요? 두 가지 주요 이유가 있습니다.

* 동일한 컴퓨터에 코드와 데이터를 거의 유지하여 높은 처리량, 낮은 대기 시간, 내결함성 온라인 트랜잭션 처리(OLTP) 서비스를 빌드할 수 있습니다. 대화형 인터넷 상점, 검색, IoT(사물 인터넷) 시스템, 거래 시스템, 신용 카드 처리 및 사기 감지 시스템,개인 레코드 관리를 몇 가지 예로 들 수 있습니다.
* 응용 프로그램 디자인을 간소화할 수 있습니다. 상태 저장 마이크로 서비스를 사용하면 순수한 상태 비저장 응용 프로그램의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요로 했던 추가 큐 및 캐시가 필요하지 않습니다. 상태 저장 서비스는 기본적으로 가용성이 높고 대기 시간이 낮아 전체적으로 응용 프로그램에서 관리하는 이동 부분 수가 줄어듭니다.

## <a name="supported-programming-models"></a>지원되는 프로그래밍 모델
서비스 패브릭은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다. 서비스는 플랫폼의 기능과 응용 프로그램 프레임워크를 최대한 활용하기 위해 Service Fabric API를 사용할 수 있습니다. 서비스는 모든 언어로 작성되고 Service Fabric 클러스터에서 호스트되는 컴파일된 실행 프로그램일 수도 있습니다. 자세한 내용은 [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)을 참조하세요.

### <a name="containers"></a>컨테이너
기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. Service Fabric도 [컨테이너](service-fabric-containers-overview.md)에 서비스를 배포할 수 있습니다. 중요한 점은 프로세스의 서비스와 동일한 응용 프로그램의 컨테이너의 서비스를 혼합할 수 있습니다. Service Fabric은 Windows Server 2016에서 Linux 컨테이너 및 Windows 컨테이너의 배포를 지원합니다. 컨테이너에서 기존 응용 프로그램, 상태 비저장 서비스 또는 상태 저장 서비스를 배포할 수 있습니다. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md)는 Service Fabric 플랫폼과 통합하여 전체 플랫폼 기능을 활용하는 서비스 작성을 위한 간단한 프레임워크입니다. Reliable Services는 웹 서버나 Azure Cloud Services의 작업자 역할 등, 대부분의 서비스 플랫폼과 유사하게 상태 비저장이 될 수 있습니다. 여기서는 상태가 Azure DB나 Azure Table Storage 같은 외부 솔루션에서 유지됩니다. Reliable Services는 Reliable Collections를 사용하여 서비스 자체에 직접 유지되게 상태를 저장할 수도 있습니다. 상태는 복제를 통해 [고가용성](service-fabric-availability-services.md)이 유지되고 [분할](service-fabric-concepts-partitioning.md)을 통해 배포되며, 모두 Service Fabric에서 자동으로 관리합니다.

### <a name="reliable-actors"></a>Reliable Actors
Reliable Services의 최상위에 구축되는 [Reliable Actor](service-fabric-reliable-actors-introduction.md) 프레임워크는 행위자 설계 패턴을 기준으로 가상 행위자 패턴을 구현하는 응용 프로그램 프레임워크입니다. Reliable Actor 프레임워크는 행위자라고 하는 단일 스레드 실행을 통해 독립적인 계산 단위 및 상태를 사용합니다. Reliable Actor 프레임워크는 행위자와 사전 설정 상태 지속성 및 확장 구성에 대해 기본 포함된 통신을 제공합니다.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric은 웹 및 API 응용 프로그램 빌드를 위한 첫 번째 클래스 프로그래밍 모델로 [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)와 통합됩니다.  ASP.NET Core는 Service Fabric에서 다음 두 가지 방식으로 사용할 수 있습니다.

- 게스트 실행 파일로 호스팅됨 주로 코드 변경 없이 Service Fabric에서 기존 ASP.NET Core 응용 프로그램을 실행하는 데 사용됩니다.
- Reliable Service에서 실행 향상된 Service Fabric 런타임 통합과 상태 저장 ASP.NET Core 서비스를 허용합니다.

### <a name="guest-executables"></a>게스트 실행 파일
[게스트 실행 파일](service-fabric-guest-executables-introduction.md)은 Service Fabric 클러스터에서 다른 서비스와 함께 호스트된 임의의 기존 실행 파일입니다. 게스트 실행 파일은 Service Fabric API와 직접 통합되지 않습니다. 그러나 사용자 지정 상태 및 로드 보고, REST API 호출에 의한 서비스 검색 가능성과 같이 플랫폼에서 제공하는 기능을 계속 활용합니다. 또한 전체 응용 프로그램 수명 주기 지원도 포함합니다. 

## <a name="application-lifecycle"></a>응용 프로그램 수명 주기
다른 플랫폼과 마찬가지로, Service Fabric 기반의 응용 프로그램은 일반적으로 디자인, 개발, 테스트, 배포, 업그레이드, 유지 관리 및 제거 단계를 거칩니다. 서비스 패브릭은 개발부터 배포, 일상적인 관리, 유지 관리 및 최종적인 서비스 해제에 이르기까지 클라우드 응용 프로그램의 전체 응용 프로그램 수명 주기 관리에 대해 최고 수준의 지원을 제공합니다. 여러 역할이 응용 프로그램 수명 주기에 독립적으로 참가할 수 있는 서비스 모델이 제공됩니다. [Service Fabric 응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에서는 API에 대한 개요 및 API가 Service Fabric 응용 프로그램 수명 주기의 전체 단계에서 여러 역할에 의해 사용되는 방법을 제공합니다. 

전체 앱 수명 주기는 [PowerShell cmdlet](/powershell/module/ServiceFabric/), [CLI 명령](service-fabric-sfctl.md), [C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API](/java/api/system.fabric._application_management_client) 및 [REST API](/rest/api/servicefabric/)를 사용하여 관리할 수 있습니다. [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) 또는 [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)와 같은 도구를 사용하여 지속적인 통합/지속적인 배포 파이프라인을 설정할 수도 있습니다.

다음 Microsoft Virtual Academy 비디오에서는 응용 프로그램 수명 주기를 관리하는 방법을 설명합니다. <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>응용 프로그램 및 서비스 테스트
진정한 클라우드 규모 서비스를 만들려면 응용 프로그램과 서비스가 실제 오류를 견딜 수 있도록 하는 것이 중요합니다. 오류 분석 서비스는 Service Fabric에서 작성된 서비스 테스트를 위해 설계되었습니다. [오류 분석 서비스](service-fabric-testability-overview.md)를 통해 의미 있는 결함을 유도하고 응용 프로그램에 대해 전체 테스트 시나리오를 실행할 수 있습니다. 이러한 오류와 시나리오는 다양한 상태를 실행하고 유효성을 검사하며 서비스가 수명 전반에서 일관되게 제어되고 안전한 방식으로 경험할 수 있도록 전환합니다.

[작업](service-fabric-testability-actions.md)은 개별 오류를 사용하여 테스트할 서비스를 대상으로 합니다. 서비스 개발자는 이러한 기본 구성 요소를 사용하여 복잡한 시나리오를 작성할 수 있습니다. 시뮬레이트된 오류의 예는 다음과 같습니다.

* 노드를 다시 시작하여 컴퓨터 또는 VM이 재부팅되는 다양한 상황을 시뮬레이션합니다.
* 상태 저장 서비스의 복제본을 이동하여 부하 분산, 장애 조치(failover) 또는 응용 프로그램 업그레이드를 시뮬레이션합니다.
* 상태 저장 서비스에 대해 쿼럼 손실을 호출하여 새 데이터를 허용하기에 충분한 "백업" 또는 "보조" 복제본이 없어서 쓰기 작업을 계속할 수 없는 상황을 만듭니다.
* 상태 저장 서비스에 대해 데이터 손실을 호출하여 모든 메모리 내 상태가 지워진 상황을 만듭니다.

[시나리오](service-fabric-testability-scenarios.md)는 하나 이상의 작업으로 구성되는 복잡한 작업입니다. 오류 분석 서비스는 두 가지 기본 제공 전체 시나리오를 제공합니다.

* [비정상 상황 시나리오](service-fabric-controlled-chaos.md)는 장기간에 걸쳐 클러스터 전체에서 정상적 및 비정상적 인터리브 오류를 지속적으로 시뮬레이트합니다.
* [장애 조치(failover) 시나리오](service-fabric-testability-scenarios.md#failover-test) - 다른 서비스의 영향을 받지 않고 특정 서비스 파티션을 대상으로 하는 비정상 상황 테스트 시나리오의 버전입니다.

## <a name="clusters"></a>클러스터
[Service Fabric 클러스터](service-fabric-deploy-anywhere.md): 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 규모를 컴퓨터 수천 대로 확장할 수 있습니다. 클러스터의 일부인 컴퓨터나 VM을 클러스터 노드라고 합니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 배치 속성과 같은 특징이 있습니다. 각 컴퓨터 또는 VM에는 자동 시작 서비스인 `FabricHost.exe`가 있습니다. 이 서비스는 부팅 시 실행된 다음 Fabric.exe, FabricGateway.exe 등의 두 실행 파일을 시작합니다. 이러한 두 실행 파일이 노드를 구성합니다. 테스트 시나리오에서는 `Fabric.exe` 및 `FabricGateway.exe`와 같은 여러 인스턴스를 실행하여 단일 컴퓨터 또는 VM에 여러 노드를 호스트할 수 있습니다.

Windows Server 또는 Linux를 실행하는 가상 또는 물리적 컴퓨터에 Service Fabric 클러스터를 만들 수 있습니다. 온-프레미스, Microsoft Azure 또는 다른 모든 클라우드 공급자에서 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 Service Fabric 응용 프로그램을 배포하고 실행할 수 있습니다.

다음 Microsoft Virtual Academy 비디오에서는 Service Fabric 클러스터를 설명합니다. <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Azure의 클러스터
Azure에서 Service Fabric 클러스터를 실행하면 클러스터의 작업 및 관리를 더 용이하고 안정적으로 해주는 다음과 같은 다른 Azure 기능 및 서비스와 통합할 수 있습니다. 클러스터는 Azure Resource Manager 리소스이므로 Azure에서 다른 리소스처럼 클러스터를 모델링할 수 있습니다. 또한 Resource Manager는 클러스터가 단일 단위로 사용하는 모든 리소스를 손쉽게 관리할 수 있습니다. Azure에서 클러스터는 Azure 진단 및 Log Analytics와 통합됩니다. 클러스터 노드 형식은 [가상 머신 확장 집합](/azure/virtual-machine-scale-sets/index)이므로 자동 크기 조정 기능을 기본 제공합니다.

[Azure Portal](service-fabric-cluster-creation-via-portal.md), [템플릿](service-fabric-cluster-creation-via-arm.md) 또는 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md)를 통해 Azure에 클러스터를 만들 수 있습니다.

Linux의 Service Fabric을 사용하면 Windows와 마찬가지로 Linux에서도 Service Fabric을 통해 해당 환경에서 가용성 및 확장성이 뛰어난 응용 프로그램을 빌드, 배포 및 관리할 수 있습니다. Service Fabric 프레임워크(Reliable Services 및 Reliable Actors)는 C#(.NET Core)뿐만 아니라 Linux의 Java에서 사용할 수 있습니다. 어떤 언어 또는 프레임워크에서도 [게스트 실행 서비스](service-fabric-guest-executables-introduction.md) 를 빌드할 수 있습니다. Docker 컨테이너 오케스트레이션도 지원됩니다. Docker 컨테이너는 게스트 실행 파일 또는 Service Fabric 프레임워크를 사용하는 네이티브 Service Fabric 서비스를 실행할 수 있습니다. 자세한 내용은 [Linux의 Service Fabric](service-fabric-deploy-anywhere.md)을 참조하세요.

Windows에서는 지원되지만 Linux에서 지원되지 않는 일부 기능이 있습니다. 자세한 내용은 [Linux와 Windows의 Service Fabric 간의 차이점](service-fabric-linux-windows-differences.md)을 참조하세요.

### <a name="standalone-clusters"></a>독립 실행형 클러스터
Service Fabric은 온-프레미스 또는 클라우드 공급자에서 독립 실행형 Service Fabric 클러스터를 만들 수 있는 설치 패키지를 제공합니다. 독립 실행형 클러스터를 사용하면 원하는 위치에서 클러스터를 호스트할 수 있습니다. 데이터가 규정 준수 또는 규제 제약을 받는 경우 또는 데이터를 로컬로 유지하려는 경우 고유한 클러스터 및 응용 프로그램을 호스트할 수 있습니다. Service Fabric 응용 프로그램은 변경 없이 여러 호스팅 환경에서 실행할 수 있으므로 하나의 호스팅 환경에서 습득한 응용 프로그램 빌드에 대한 정보를 다른 환경에서도 사용할 수 있습니다. 

[첫 번째 Service Fabric 독립 실행형 클러스터 만들기](service-fabric-get-started-standalone-cluster.md)

Linux 독립 실행형 클러스터는 아직 지원되지 않습니다.

### <a name="cluster-security"></a>클러스터 보안
실행 중인 프로덕션 작업이 있을 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지해야 합니다. 보안이 되지 않은 클러스터를 만들 수 있지만, 이렇게 하면 공용 인터넷에 관리 끝점이 노출될 경우 익명 사용자가 클러스터에 연결할 수 있게 됩니다. 보안이 설정되지 않은 클러스터에서 나중에 보안을 설정할 수는 없습니다. 클러스터 보안은 클러스터 작성 시 설정할 수 있습니다.

클러스터 보안 시나리오:
* 노드 간 보안
* 클라이언트-노드 보안
* RBAC(역할 기반 액세스 제어)

자세한 내용은 [클러스터에 보안 적용](service-fabric-cluster-security.md)을 참조하세요.

### <a name="scaling"></a>확장
새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 파티션 복제본 및 인스턴스의 균형을 조정합니다. 전반적인 응용 프로그램 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본 및 인스턴스의 균형을 다시 조정합니다. Azure에서 [수동으로](service-fabric-cluster-scale-up-down.md) 또는 [프로그래밍 방식으로](service-fabric-cluster-programmatic-scaling.md) 클러스터의 크기를 조정할 수 있습니다. 독립 실행형 클러스터는 [수동으로](service-fabric-cluster-windows-server-add-remove-nodes.md) 크기를 조정할 수 있습니다.

### <a name="cluster-upgrades"></a>클러스터 업그레이드
주기적으로 새로운 버전의 Service Fabric 런타임이 릴리스됩니다. 항상 [지원되는 버전](service-fabric-support.md)을 실행하도록 클러스터의 런타임, 패브릭 또는 업그레이드를 수행합니다. 패브릭 업그레이드 외에도 인증서 또는 응용 프로그램 포트와 같은 클러스터 구성을 업데이트할 수 있습니다.

Service Fabric 클러스터는 개인이 소유하지만 Microsoft에서 부분적으로 관리하는 리소스입니다. Microsoft는 기본 OS를 패치하고 클러스터에서 패브릭 업그레이드를 수행합니다. Microsoft에서 새로운 버전을 릴리스하거나 원하는 패브릭 버전을 선택하도록 한 경우 클러스터가 자동 패브릭 업그레이드를 수신하도록 설정할 수 있습니다. 패브릭 및 구성 업그레이드는 Azure Portal이나 Resource Manager를 통해 설정할 수 있습니다. 자세한 내용은 [Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)를 참조하세요. 

독립 실행형 클러스터는 사용자가 전적으로 소유하는 리소스입니다. 기본 OS에 패치를 적용하고 패브릭 업그레이드를 시작하는 작업은 사용자가 수행해야 합니다. 클러스터에서 [https://www.microsoft.com/download](https://www.microsoft.com/download)에 연결할 수 있는 경우 새 Service Fabric 런타임 패키지를 자동으로 다운로드하고 프로비저닝하도록 클러스터를 설정할 수 있습니다. 그런 다음 업그레이드를 시작합니다. 클러스터가 [https://www.microsoft.com/download](https://www.microsoft.com/download)에 액세스할 수 없는 경우 인터넷에 연결된 컴퓨터에서 수동으로 새 런타임 패키지를 다운로드한 다음, 업그레이드를 시작할 수 있습니다. 자세한 내용은 [독립 실행형 Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade-windows-server.md)를 참조하세요.

## <a name="health-monitoring"></a>상태 모니터링
Service Fabric은 특정 엔터티(예: 클러스터 노드 및 서비스 복제본)의 비정상 클러스터 및 응용 프로그램 상태에 플래그를 적용하도록 설계된 [상태 모델](service-fabric-health-introduction.md)을 도입했습니다. 상태 모델은 Health 보고서(시스템 구성 요소 및 Watchdog)를 사용합니다. 쉽고 빠른 진단을 목표로 합니다. 서비스 작성자는 상태 및 [상태 보고 설계](service-fabric-report-health.md#design-health-reporting) 방법을 미리 고려해야 합니다. 상태에 영향을 줄 수 있는 모든 조건이 보고되어야 하며, 특히 근본 원인에 가까운 문제를 플래깅하는 데 도움이 되는 경우에는 반드시 보고가 이루어져야 합니다. 서비스가 프로덕션에서 대규모로 실행되면 상태 정보를 통해 디버깅과 조사에 들어가는 시간과 노력을 줄일 수 있습니다.

서비스 패브릭 보고자는 식별된 관심 조건을 모니터링합니다. 보고자는 각자 로컬 보기를 기반으로 이러한 조건에 대한 정보를 보고합니다. [Health 스토어](service-fabric-health-introduction.md#health-store) 는 엔터티가 전체적으로 정상인지를 판단하기 위하여 모든 보고자가 보낸 상태 데이터를 수집합니다. 모델은 다양하고 유연하며 사용이 쉽도록 계획됩니다. 상태 보고서의 품질은 클러스터 상태 보기의 정확성을 결정합니다. 비정상 이슈를 잘못 표시하는 거짓 긍정은 상태 데이터를 사용하는 업그레이드 또는 기타 서비스에 부정적인 영향을 미칠 수 있습니다. 이러한 서비스의 예로는 복구 서비스 및 경고 메커니즘이 있습니다. 따라서 최선의 방식으로 관심 조건을 포착하는 보고서를 제공하기 위해서는 몇 가지를 고려해야 합니다.

다음 위치에서 보고를 수행할 수 있습니다.
* 모니터링되는 Service Fabric 서비스 복제본 및 인스턴스
* Service Fabric 서비스로 배포되는 내부 Watchdog(예: 조건 및 문제 보고서를 모니터링하는 Service Fabric 상태 비저장 서비스). Watchdog는 모든 노드에 배포되거나 모니터링되는 서비스로 규합될 수 있습니다.
* Service Fabric 노드에서 실행되지만 Service Fabric 서비스로 구현되지 않는 내부 watchdog
* Service Fabric 클러스터 외부의 리소스를 조사하는 외부 Watchdog(예: Gomez와 같은 모니터링 서비스)

기본적으로 Service Fabric 구성 요소가 클러스터 내의 모든 엔터티에 대해 바로 보고합니다. [시스템 상태 보고서](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)는 상태를 통해 클러스터 및 응용 프로그램의 기능 및 플래그 문제에 대한 가시성을 제공합니다. 응용 프로그램 및 서비스의 경우, 시스템 상태 보고서는 Service Fabric 런타임 관점에서 엔터티가 올바르게 구현되고 동작하는지 확인합니다. 보고서는 서비스의 비즈니스 논리의 상태 모니터링을 제공하거나 응답이 없는 프로세스를 감지하지 않습니다. 서비스 논리에 맞는 상태 정보를 추가하려면 서비스에서 [사용자 지정 상태 보고를 구현](service-fabric-report-health.md)합니다.

Service Fabric은 여러 가지 다음 방법으로 상태 저장소에 집계된 [상태 보고서를 볼](service-fabric-view-entities-aggregated-health.md) 수 있습니다.
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 또는 기타 시각화 도구
* 상태 쿼리([PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), [C# FabricClient API](/dotnet/api/system.fabric.fabricclient.healthclient) 및 [Java FabricClient API](/java/api/system.fabric._health_client) 또는 [REST API](/rest/api/servicefabric)를 통해)
* PowerShell, CLI, API 또는 REST를 통해 속성 중 하나로 상태를 가지고 있는 엔터티 목록을 반환하는 일반 쿼리

다음 Microsoft Virtual Academy 비디오에는 Service Fabric 상태 모델 및 사용 방법을 설명합니다. <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>모니터링 및 진단
[모니터링 및 진단](service-fabric-diagnostics-overview.md)은 모든 환경에서 응용 프로그램 및 서비스를 개발, 테스트 및 배포하는 데 중요합니다. Service Fabric 솔루션은 로컬 개발 환경 또는 프로덕션 환경에서 응용 프로그램과 서비스가 예상대로 작동하는지 확인하는 데 도움이 되는 모니터링 및 진단을 계획하고 구현할 때 가장 효과적입니다.

모니터링 및 진단의 주요 목표는 다음과 같습니다.

- 하드웨어 및 인프라 문제 감지 및 진단
- 소프트웨어 및 앱 문제 감지, 서비스 가동 중지 시간 감소
- 리소스 사용량 이해 및 적절한 작업 의사 결정 유도
- 응용 프로그램, 서비스 및 인프라 성능 최적화
- 비즈니스 통찰력 생성 및 향상 가능 영역 식별
 
모니터링 및 진단의 전체 워크플로는 다음 세 단계로 구성됩니다.

1. 이벤트 생성: 인프라, 클러스터 및 응용 프로그램/서비스 수준의 이벤트(로그, 추적, 사용자 지정 이벤트)가 포함됩니다.
2. 이벤트 집계: 생성된 이벤트를 표시하려면 먼저 수집하고 집계해야 합니다.
3. 분석: 필요에 따라 분석하고 표시할 수 있도록 이벤트를 특정 형식으로 시각화하고 액세스할 수 있어야 합니다.

이 세 영역을 포괄하는 여러 제품을 사용할 수 있으며 각각에 대해 서로 다른 기술을 자유롭게 선택할 수 있습니다. 자세한 내용은 [Azure Service Fabric 모니터링 및 진단](service-fabric-diagnostics-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure에서 클러스터](service-fabric-cluster-creation-via-portal.md)를 만들거나 [Windows에서 독립 실행형 클러스터](service-fabric-cluster-creation-for-windows-server.md)를 만드는 방법을 알아봅니다.
* [Reliable Services](service-fabric-reliable-services-quick-start.md) 또는 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 서비스를 만들어 봅니다.
* [Cloud Services에서 마이그레이션](service-fabric-cloud-services-migration-differences.md)하는 방법을 알아봅니다.
* [서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)에 대해 알아봅니다. 
* [앱 및 서비스 테스트](service-fabric-testability-overview.md)에 대해 알아봅니다.
* [클러스터 리소스 관리 및 오케스트레이션](service-fabric-cluster-resource-manager-introduction.md)을 참조하세요.
* [Service Fabric 샘플](http://aka.ms/servicefabricsamples)을 살펴봅니다.
* [Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.
* 문서 및 공지 사항에 대한 [팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)를 읽습니다.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
