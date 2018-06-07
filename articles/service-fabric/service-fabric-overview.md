---
title: Azure의 Service Fabric 개요 | Microsoft Docs
description: 응용 프로그램이 여러 마이크로 서비스로 구성되어 확장성과 복원력을 제공하는 서비스 패브릭의 개요입니다. Service Fabric은 확장 가능하고 안정적이며 관리하기 쉬운 클라우드 응용 프로그램을 빌드하는 데 사용되는 분산 시스템 플랫폼입니다.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: fdb710868a75f64c3fe9f3742482c70ff86f4365
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207838"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric의 개요
Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 관리하도록 배포된 시스템 플랫폼입니다. 또한 Service Fabric은 클라우드 네이티브 응용 프로그램 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다. Service Fabric은 컨테이너에서 실행되는 엔터프라이즈급 계층 1 클라우드 규모의 응용 프로그램을 빌드 및 관리하기 위한 차세대 플랫폼을 나타냅니다.

이 짧은 비디오에서는 Service Fabric 및 마이크로 서비스를 소개합니다.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="applications-composed-of-microservices"></a>마이크로 서비스로 구성된 응용 프로그램 
Service Fabric을 사용하면 컴퓨터의 공유 풀(클러스터라고 함)에서 높은 밀도로 실행되는 마이크로 서비스로 구성된 확장성 있고 신뢰할 수 있는 응용 프로그램을 빌드하고 관리할 수 있습니다. 컨테이너에서 실행되는 분산되고 확장 가능한 상태 비저장 및 상태 저장 마이크로 서비스를 빌드하는 정교하고 간단한 런타임을 제공합니다. 또한 컨테이너화된 서비스를 비롯하여 배포된 응용 프로그램을 프로비전, 배포, 모니터링, 업그레이드/패치 및 삭제하는 포괄적인 응용 프로그램 관리 기능을 제공합니다.

Service Fabric은 Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, 비즈니스용 Skype 및 여러 주요 Azure 서비스를 비롯한 오늘날의 여러 Microsoft 서비스를 지원합니다.

Service Fabric은 필요에 따라 작은 규모로 시작하여 수백 또는 수천 대의 컴퓨터가 있는 대규모 환경으로 확장할 수 있는 클라우드 네이티브 서비스를 만들 수 있도록 조정되어 있습니다.

오늘날 인터넷 범위의 서비스는 마이크로 서비스를 토대로 빌드됩니다. 마이크로 서비스의 예로는 프로토콜 게이트웨이, 사용자 프로필, 쇼핑 카트, 인벤토리 처리, 큐, 캐시 등을 들 수 있습니다. Service Fabric은 모든 마이크로 서비스(또는 컨테이너)에 상태 비저장 또는 상태 저장 중 하나일 수 있는 고유한 이름을 제공하는 마이크로 서비스 플랫폼입니다.

Service Fabric은 이러한 마이크로 서비스로 구성된 응용 프로그램에 포괄적인 런타임 및 수명 주기 관리 기능을 제공합니다. 서비스 패브릭 클러스터에 걸쳐 배포 및 활성화된 컨테이너 내에서 마이크로 서비스를 호스트합니다. 가상 머신에서 컨테이너로 이동하면 밀도가 큰 순서대로 정렬됩니다. 마찬가지로, 컨테이너에서 다음과 같은 컨테이너의 마이크로 서비스로 이동하면 또 다른 밀도 순서대로 정렬됩니다. 예를 들어, 단일 Azure SQL Database 클러스터는 수십만 개의 데이터베이스를 호스트하는 수만 개의 컨테이너를 실행하는 수백 대의 컴퓨터로 구성됩니다. 각 데이터베이스는 서비스 패브릭 상태 저장 마이크로 서비스입니다. 

마이크로 서비스 접근 방식에 대한 자세한 내용은 [응용 프로그램 구축에 마이크로 서비스 접근 방식이 필요한 이유](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>컨테이너 배포 및 오케스트레이션
Service Fabric은 컴퓨터 클러스터에 걸쳐 마이크로 서비스를 배포하는 Microsoft의 [컨테이너 조정자](service-fabric-cluster-resource-manager-introduction.md)입니다. 마이크로 서비스는 [Service Fabric 프로그래밍 모델](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 사용부터 [선택한 코드](service-fabric-guest-executables-introduction.md) 배포에 이르는 다양한 방식으로 개발될 수 있습니다. 중요한 점은 프로세스의 서비스와 동일한 응용 프로그램의 컨테이너의 서비스를 혼합할 수 있습니다. [이미지를 배포 및 관리](service-fabric-containers-overview.md)하려는 경우 Service Fabric은 컨테이너 조정자로 선택하기에 가장 적합합니다.

## <a name="any-os-any-cloud"></a>모든 OS, 모든 클라우드
Service Fabric은 어디에서나 실행됩니다. Azure 또는 온-프레미스, Windows Server 또는 Linux 등 수많은 환경에서 Service Fabric용 클러스터를 만들 수 있습니다. 다른 공용 클라우드에서 클러스터를 만들 수도 있습니다. 또한 SDK의 개발 환경은 에뮬레이터가 포함되지 않은 프로덕션 환경과 **동일**합니다. 즉, 로컬 개발 클러스터에서 실행되는 항목은 다른 환경의 클러스터에 배포됩니다.

![서비스 패브릭 플랫폼][Image1]

Windows 개발을 위해 Service Fabric .NET SDK는 Visual Studio 및 Powershell과 통합됩니다. [Windows에서 개발 환경 준비](service-fabric-get-started.md)를 참조하세요. Linux 개발을 위해 Service Fabric Java SDK는 Eclipse와 통합되고, Yeoman은 Java, .NET Core 및 컨테이너 응용 프로그램에 대한 템플릿을 생성하는 데 사용됩니다. [Linux에서 개발 환경 준비](service-fabric-get-started.md)를 참조하세요.

클러스터를 만드는 방법에 대한 자세한 내용은 [Windows Server 또는 Linux에서 클러스터 만들기](service-fabric-deploy-anywhere.md) 또는 Azure의 경우 [Azure Portal을 통해](service-fabric-cluster-creation-via-portal.md) 클러스터 만들기를 참조하세요.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric용 상태 비저장 및 상태 저장 마이크로 서비스
Service Fabric을 사용하면 마이크로 서비스 또는 컨테이너로 구성된 응용 프로그램을 빌드할 수 있습니다. 상태 비저장 마이크로 서비스(프로토콜 게이트웨이, 웹 프록시 등)는 서비스의 요청 및 응답 이외에 변경 가능한 상태를 관리하지 않습니다. Azure Cloud Services 작업자 역할이 상태 비저장 서비스의 예입니다. 상태 저장 마이크로 서비스(사용자 계정, 데이터베이스, 장치, 쇼핑 카트, 큐 등)는 요청 및 응답 이외에 변경 가능하고 신뢰할 수 있는 상태를 관리합니다. 오늘날 인터넷 범위의 서비스는 상태 비저장 및 상태 저장 마이크로 서비스의 조합으로 구성됩니다. 

Service Fabric의 주요 차이점은 [기본 제공 프로그래밍 모델](service-fabric-choose-framework.md) 또는 컨테이너화된 상태 저장 서비스 중 하나를 사용하여 상태 저장 서비스를 빌드하는 데 집중한다는 것입니다. [응용 프로그램 시나리오](service-fabric-application-scenarios.md)는 상태 저장 서비스를 사용하는 시나리오를 설명합니다.


## <a name="application-lifecycle-management"></a>응용 프로그램 수명 주기 관리
Service Fabric은 컨테이너를 비롯하여 클라우드 응용 프로그램의 전체 응용 프로그램 수명 주기 관리 및 CI/CD를 지원합니다. 이 수명 주기는 개발부터 배포, 일상적인 관리, 유지 관리, 최종 서비스 해제까지 포함합니다.

Service Fabric 응용 프로그램 수명 주기 관리 기능을 사용하면 응용 프로그램 관리자와 IT 운영자가 간단하고 개입할 필요성이 적은 워크플로를 사용하여 응용 프로그램을 프로비전, 배포, 패치 및 모니터링할 수 있습니다. 이러한 기본 제공 워크플로는 응용 프로그램을 지속적으로 사용 가능하게 유지해야 하는 IT 운영자의 부담을 크게 줄여줍니다.

대부분의 응용 프로그램은 함께 배포되는 상태 비저장 및 상태 저장 마이크로 서비스, 컨테이너 및 다른 실행 파일의 조합으로 구성됩니다. 응용 프로그램에 대한 강력한 형식을 가지고 있으므로, Service Fabric을 사용하면 여러 응용 프로그램 인스턴스를 배포할 수 있습니다. 각 인스턴스는 독립적으로 관리 및 업그레이드됩니다. 무엇보다도 Service Fabric은 컨테이너 또는 모든 실행 파일을 배포하고 안정적으로 만들 수 있습니다. 예를 들어 Service Fabric은 .NET, ASP.NET Core, node.js, Windows 컨테이너, Linux 컨테이너, Java 가상 머신, 스크립트, Angular 또는 응용 프로그램을 구성하는 다른 모든 항목을 배포할 수 있습니다.

Service Fabric은 [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) 및 [Octopus 배포](https://octopus.com/)와 같은 CI/CD 도구와 통합되고 다른 인기 있는 CI/CD 도구와 함께 사용할 수 있습니다.

응용 프로그램 수명 주기 관리에 대한 자세한 내용은 [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)를 참조하세요. 모든 코드를 배포하는 방법에 대한 자세한 내용은 [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)를 참조하세요.

## <a name="key-capabilities"></a>주요 기능
서비스 패브릭을 사용하면 다음을 수행할 수 있습니다.

* Azure에 또는 코드 변경 없이 Windows Server 또는 Linux를 실행하는 온-프레미스 데이터 센터에 배포. 한 번 작성한 후 모든 Service Fabric 클러스터에 배포.
* Service Fabric 프로그래밍 모델, 컨테이너 또는 코드를 사용하여 마이크로 서비스로 구성된 확장성 있는 응용 프로그램을 개발합니다.
* 매우 안정적인 상태 비저장 및 상태 저장 마이크로 서비스를 개발합니다. 상태 저장 마이크로 서비스를 사용하여 응용 프로그램의 설계를 간소화합니다. 
* 새로운 Reliable Actors 프로그래밍 모델을 사용하여 자체 포함된 코드 및 상태를 사용하여 클라우드 개체를 만듭니다.
* Windows 컨테이너 및 Linux 컨테이너를 포함하는 컨테이너를 배포하고 조정합니다. Service Fabric은 데이터를 인식하는 상태 저장 컨테이너 조정자입니다.
* 수백 또는 수천 개의 응용 프로그램 또는 컴퓨터당 컨테이너를 사용하여 몇 초 내로 밀도가 높게 응용 프로그램을 배포합니다.
* 동일한 응용 프로그램의 서로 다른 버전을 병렬로 배포하고 각 응용 프로그램을 독립적으로 업그레이드.
* 중단 및 무중단 업그레이드를 포함하여, 중단 시간 없이 응용 프로그램의 수명 주기를 관리합니다.
* 클러스터의 노드 수를 확장하거나 감축합니다. 노드의 크기를 조정하면 응용 프로그램이 자동으로 조정됩니다.
* 응용 프로그램의 상태를 모니터링 및 진단하고 자동 복구를 수행하는 정책 설정.
* 리소스 분산 장치를 감시하여 클러스터 간 응용 프로그램을 재배포하도록 조정합니다. 서비스 패브릭은 오류에서 복구하고 사용 가능한 리소스에 따라 부하의 분산을 최적화합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* 자세한 내용은 다음을 참조하세요.
  * [응용 프로그램 구축에 마이크로 서비스 접근 방식이 필요한 이유](service-fabric-overview-microservices.md)
  * [용어 개요](service-fabric-technical-overview.md)
* [Windows 개발 환경](service-fabric-get-started.md) 설정  
* [Linux 개발 환경](service-fabric-get-started-linux.md) 설정
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
