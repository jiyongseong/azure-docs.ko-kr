---
title: Azure Service Fabric 용어 개요 | Microsoft Docs
description: 서비스 패브릭의 용어에 대해 간략하게 소개하고 설명서의 나머지 부분에서 사용되는 용어와 주요 용어 개념에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: e3da081f9b327031d6d1e0afd2f2fb52383bf933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-terminology-overview"></a>서비스 패브릭 용어 개요
Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 이 문서에서는 설명서에서 사용되는 용어를 이해할 수 있도록 Service Fabric에서 사용되는 용어에 대해 자세히 설명합니다.

이 섹션에 나열된 개념은 Microsoft Virtual Academy 비디오, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">핵심 개념인</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">디자인 타임 개념</a> 및 <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">런타임 개념</a>에서도 설명됩니다.

## <a name="infrastructure-concepts"></a>인프라 개념
**클러스터**: 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다.  클러스터의 규모를 컴퓨터 수천 대로 확장할 수 있습니다.

**노드**: 클러스터의 일부인 컴퓨터 또는 VM으로, *노드*라고 합니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 배치 속성과 같은 특징이 있습니다. 각 컴퓨터 또는 VM이 Windows 서비스인 `FabricHost.exe`를 자동으로 시작하여 부팅을 실행하기 시작한 다음 `Fabric.exe` 및 `FabricGateway.exe`라는 두 개의 실행 파일을 시작합니다. 이러한 두 실행 파일이 노드를 구성합니다. 테스트 시나리오에서는 `Fabric.exe` 및 `FabricGateway.exe`와 같은 여러 인스턴스를 실행하여 단일 컴퓨터 또는 VM에 여러 노드를 호스트할 수 있습니다.

## <a name="application-concepts"></a>응용 프로그램 개념
**응용 프로그램 유형**: 이름/버전이 서비스 형식의 컬렉션에 할당됩니다. `ApplicationManifest.xml` 파일에 정의되고 응용 프로그램 패키지 디렉터리에 포함합니다. 그런 다음 이 디렉터리는 Service Fabric 클러스터의 이미지 저장소에 복사됩니다. 그런 다음 클러스터 내에서 이 응용 프로그램 형식에서 명명된 응용 프로그램을 만들 수 있습니다.

자세한 내용은 [응용 프로그램 모델](service-fabric-application-model.md) 문서를 참조하세요.

**응용 프로그램 패키지**: 응용 프로그램 유형의 `ApplicationManifest.xml` 파일을 포함하는 디스크 디렉터리입니다. 응용 프로그램 형식을 구성하는 각 서비스 형식에 대한 서비스 패키지를 참조합니다. 응용 프로그램 패키지 디렉터리에 있는 파일은 서비스 패브릭 클러스터의 이미지 저장소에 복사됩니다. 예를 들어 전자 메일 응용 프로그램 유형에 대한 응용 프로그램 패키지는 큐 서비스 패키지, 프런트 엔드 서비스 패키지 및 데이터베이스 서비스 패키지에 대한 참조를 포함할 수 있습니다.

**명명된 응용 프로그램**: 응용 프로그램 패키지를 이미지 저장소에 복사한 후 클러스터 내에서 응용 프로그램의 인스턴스를 만듭니다. 해당 이름 또는 버전을 사용하여 응용 프로그램 패키지의 응용 프로그램 유형을 지정할 때 인스턴스를 만듭니다. 각 응용 프로그램 유형 인스턴스에 `"fabric:/MyNamedApp"`과 같은 URI(Uniform Resource Identifier) 이름이 할당됩니다. 클러스터 내에서 단일 응용 프로그램 형식에서 여러 명명된 응용 프로그램을 만들 수 있습니다. 또한 다른 형식의 응용 프로그램에서 명명된 응용 프로그램을 만들 수 있습니다. 명명된 응용 프로그램은 각각 독립적으로 관리되고 버전이 지정됩니다.      

**서비스 유형**: 서비스의 코드 패키지, 데이터 패키지 및 구성 패키지에 할당된 이름/버전입니다. 서비스 유형은 `ServiceManifest.xml` 파일에 정의되고 서비스 패키지 디렉터리에 포함됩니다. 그런 다음 서비스 패키지 디렉터리는 응용 프로그램 패키지의 `ApplicationManifest.xml` 파일에서 참조됩니다. 클러스터 내에서 명명된 응용 프로그램을 만든 후에 응용 프로그램 형식의 서비스 형식 중 하나에서 명명된 서비스를 만들 수 있습니다. 서비스 형식의 `ServiceManifest.xml` 파일은 서비스에서 설명합니다.

자세한 내용은 [응용 프로그램 모델](service-fabric-application-model.md) 문서를 참조하세요.

서비스의 두 가지 형식이 있습니다.

* **상태 비저장:** Azure Storage, Azure SQL Database 또는 Azure Cosmos DB와 같은 외부 Storage 서비스에 서비스의 영구 상태를 저장할 때 상태 비저장 서비스를 사용합니다. 서비스에 영구 저장소가 없는 경우 상태 비저장 서비스를 사용합니다. 예를 들어, 값을 서비스에 전달하는 계산기 서비스는 이러한 값을 사용하여 계산을 수행하고 결과를 반환합니다.
* **상태 저장:** 서비스 패브릭으로 신뢰할 수 있는 컬렉션 또는 Reliable Actors 프로그래밍 모델을 통해 서비스의 상태를 관리하려는 경우 상태 저장 서비스를 사용합니다. 명명된 서비스를 만들 때 상태를 확산하려는 파티션 수를 지정합니다(확장성을 위해). 또한 노드 간에 상태를 복제할 횟수를 지정합니다(안정성을 위해). 명명된 서비스 각각에는 하나의 기본 복제본과 여러 보조 복제본이 있습니다. 기본 복제본에 써서 명명된 서비스의 상태를 수정합니다. 그러면 Service Fabric은 이 상태를 모든 보조 복제본에 복제하여 상태를 동기화합니다. 기본 복제본이 실패하는 경우 서비스 패브릭은 자동으로 이를 감지하고 기존 보조 복제본을 기본 복제본으로 승격합니다. 그러면 서비스 패브릭은 새로운 보조 복제본을 만듭니다.  

**복제본 또는 인스턴스**는 배포되어 실행 중인 서비스의 코드(및 상태 저장 서비스의 상태)를 가리킵니다. [복제본 및 인스턴스](service-fabric-concepts-replica-lifecycle.md)를 참조하세요.

**재구성**은 서비스의 복제본 세트에 있는 변경 프로세스를 가리킵니다. [재구성](service-fabric-concepts-reconfiguration.md)을 참조하세요.

**서비스 패키지**: 서비스 유형의 `ServiceManifest.xml` 파일을 포함하는 디스크 디렉터리입니다. 이 파일은 서비스 형식에 대한 코드, 정적 데이터 및 구성 패키지를 참조합니다. 응용 프로그램 형식의 `ApplicationManifest.xml` 파일에서 서비스 패키지 디렉터리에 파일을 참조합니다. 예를 들어, 서비스 패키지는 데이터베이스 서비스를 구성하는 코드, 정적 데이터 및 구성 패키지를 참조할 수 있습니다.

**명명된 서비스**: 명명된 응용 프로그램을 만든 후에 클러스터 내에서 해당 서비스 유형의 인스턴스를 만들 수 있습니다. 해당 이름/버전을 사용하여 서비스 유형을 지정합니다. 각 서비스 형식 인스턴스는 명명된 응용 프로그램의 URI로 범위가 지정된 URI 이름이 할당됩니다. 예를 들어 "MyNamedApp"이라는 응용 프로그램 내에서 "MyDatabase"라는 서비스를 만드는 경우 URI는 `"fabric:/MyNamedApp/MyDatabase"`와 같습니다. 명명된 응용 프로그램 내에서 몇 가지 명명된 서비스를 만들 수 있습니다. 명명된 각 서비스는 고유한 파티션 구성표 및 인스턴스 또는 복제본 수를 가질 수 있습니다.

**코드 패키지**: 서비스 형식의 실행 파일(일반적으로 EXE/DLL 파일)을 포함한 디스크 디렉터리입니다. 서비스 형식의 `ServiceManifest.xml` 파일에서 코드 패키지 디렉터리에 파일을 참조합니다. 명명된 서비스를 만들면 코드 패키지는 명명된 서비스를 실행하기 위해 선택된 하나 이상의 노드로 복사됩니다. 그러면 코드가 실행되기 시작합니다. 코드 패키지 실행 파일은 다음과 같은 두 종류가 있습니다.

* **게스트 실행 파일**: 호스트 운영 체제(Windows 또는 Linux)에서 그대로 실행되는 실행 파일입니다. 이러한 실행 파일은 연결하거나 Service Fabric 런타임 파일을 참조하지 않으며 따라서 Service Fabric 프로그래밍 모델 중 하나를 사용하지 않습니다. 이러한 실행 파일은 끝점 검색을 위해 이름 지정 서비스와 같은 일부 Service Fabric 기능을 사용할 수 없습니다. 게스트 실행 파일은 각 서비스 인스턴스와 관련된 부하 메트릭을 보고할 수 없습니다.
* **서비스 호스트 실행 파일**: Service Fabric 런타임 파일에 연결하여 Service Fabric 프로그래밍 모델을 사용하는 실행 파일로, Service Fabric 기능을 사용하도록 설정합니다. 예를 들어 명명된 서비스 인스턴스는 서비스 패브릭의 이름 지정 서비스를 사용하여 끝점을 등록할 수 있고 부하 메트릭을 보고할 수도 있습니다.      

**데이터 패키지**: 서비스 형식의 정적인 읽기 전용 데이터 파일을 포함하는 디스크 디렉터리입니다(일반적으로 사진, 사운드 및 비디오 파일). 서비스 형식의 `ServiceManifest.xml` 파일에서 데이터 패키지 디렉터리에 파일을 참조합니다. 명명된 서비스를 만들면 데이터 패키지는 명명된 서비스를 실행하기 위해 선택된 하나 이상의 노드로 복사됩니다. 이 코드는 실행되기 시작하며, 이제 데이터 파일에 액세스할 수 있습니다.

**구성 패키지**: 서비스 형식의 정적인 읽기 전용 구성 파일을 포함하는 디스크 디렉터리입니다(일반적으로 텍스트 파일). 서비스 형식의 `ServiceManifest.xml` 파일에서 구성 패키지 디렉터리에 파일을 참조합니다. 명명된 서비스를 만들면 구성 패키지의 파일은 명명된 서비스를 실행하기 위해 선택된 하나 이상의 노드로 복사됩니다. 그러면 코드가 실행되기 시작하며, 이제 구성 파일에 액세스할 수 있습니다.

**컨테이너**: 기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. Service Fabric도 컨테이너 이미지에 서비스를 배포할 수 있습니다. 컨테이너는 응용 프로그램에서 기본 운영 체제를 가상화하는 가상화 기술입니다. 응용 프로그램 및 런타임, 종속성 및 시스템 라이브러리는 컨테이너 내에서 실행됩니다. 컨테이너에 운영 체제 구문의 컨테이너 자체 격리 보기에 대한 모든 전용 액세스 권한이 있습니다. Service Fabric은 Linux 및 Windows Server 컨테이너에서 Docker 컨테이너를 지원합니다. 자세한 내용은 [Service Fabric 및 컨테이너](service-fabric-containers-overview.md)를 참조하세요.

**파티션 구성표**: 명명된 서비스를 만들 때 파티션 구성표를 지정합니다. 많은 양의 상태가 있는 서비스는 클러스터의 노드에 상태를 분산하는 파티션에 데이터를 분할합니다. 파티션 간에 데이터를 분할하여 명명된 서비스의 상태를 확장할 수 있습니다. 상태 저장 서비스에 복제본이 있는 반면 파티션 내에서 명명된 상태 비저장 서비스에는 인스턴스가 있습니다. 일반적으로 명명된 상태 비저장 서비스는 내부 상태가 없기 때문에 하나의 파티션만을 가질 수 있습니다. 파티션 인스턴스는 가용성을 제공합니다. 한 인스턴스가 실패하고 다른 인스턴스가 계속 정상적으로 작동하는 경우 Service Fabric은 새 인스턴스를 만듭니다. 명명된 상태 저장 서비스는 복제본 내에서 해당 상태를 유지하고 각 파티션은 동기화 상태로 유지된 모든 상태를 가진 고유한 복제를 설정합니다. 복제가 실패하면 서비스 패브릭은 기존 복제본에서 새 복제본을 작성해야 합니다.

자세한 내용은 [서비스 패브릭 Reliable Services 분할](service-fabric-concepts-partitioning.md) 문서를 참조하세요.

## <a name="system-services"></a>시스템 서비스
서비스 패브릭의 플랫폼 기능을 제공하는 모든 클러스터에서 작성되는 시스템 서비스가 있습니다.

**Naming Service**: 각 서비스 패브릭 클러스터에는 서비스 이름을 클러스터의 위치로 확인하는 Naming Service가 있습니다. 클러스터에 대해 인터넷 DNS(Domain Name Service)와 비슷하게 서비스 이름과 속성을 관리할 수 있습니다. 클라이언트는 이름 지정 서비스를 사용하여 클러스터의 모든 노드와 안전하게 통신하여 서비스 이름 및 해당 위치를 확인할 수 있습니다. 응용 프로그램은 클러스터 내에서 이동합니다. 예를 들어 클러스터의 오류, 리소스 분산 또는 크기 조정으로 인해 이동될 수 있습니다. 현재 네트워크 위치를 확인하는 서비스 및 클라이언트를 개발할 수 있습니다. 클라이언트는 실제 컴퓨터 IP 주소 및 현재 주소가 실행 중인 포트를 가져옵니다.

Naming Service와 연동되는 클라이언트 및 서비스 통신 API에 대한 자세한 내용은 [서비스를 사용하여 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요.

**이미지 저장소 서비스**: 각 Service Fabric 클러스터에는 배포되어 버전이 지정된 응용 프로그램 패키지가 보관되는 이미지 저장소 서비스가 있습니다. 이미지 저장소에 응용 프로그램 패키지를 복사하고 해당 응용 프로그램 패키지 내에 포함된 응용 프로그램 형식을 등록합니다. 응용 프로그램 형식을 프로비전한 후에 명명된 응용 프로그램을 만듭니다. 명명된 응용 프로그램이 모두 삭제된 후에 이미지 저장소 서비스에서 응용 프로그램 형식의 등록을 취소할 수 있습니다.

이미지 저장소 서비스에 대한 자세한 내용은 [ImageStoreConnectionString 설정 이해](service-fabric-image-store-connection-string.md)를 참조하세요.

이미지 저장소 서비스에 응용 프로그램을 배포하는 방법에 대한 자세한 내용은 [응용 프로그램 배포](service-fabric-deploy-remove-applications.md) 문서를 참조하세요.

**Failover Manager Service**: 각 Service Fabric 클러스터에는 다음 작업을 담당하는 Failover Manager Service가 있습니다.
   - 서비스의 고가용성 및 일관성과 관련된 기능을 수행합니다.
   - 응용 프로그램 및 클러스터 업그레이드를 조정합니다.
   - 다른 시스템 구성 요소와 상호 작용합니다.

**복구 관리자 서비스**: 자동화 가능한 방식으로 안전하고 투명하며 클러스터에서 복구 작업을 수행할 수 있도록 하는 선택적 시스템 서비스입니다. 복구 관리자는:
   - [실버 및 골드 내구성](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric 클러스터에서 Azure 유지 관리 복구를 수행하는 데 사용됩니다.
   - [패치 오케스트레이션 응용 프로그램](service-fabric-patch-orchestration-application.md)에 대한 복구 작업을 수행하는 데 사용됩니다.

## <a name="built-in-programming-models"></a>기본 제공 프로그래밍 모델
Service Fabric 서비스를 작성하는 데 사용할 수 있는 .NET Framework 및 Java 프로그래밍 모델이 있습니다.

**Reliable Services**: 상태 비저장 및 상태 저장 서비스를 구축하는 API입니다. 상태 저장 서비스는 신뢰할 수 있는 컬렉션(예: 사전 또는 큐)에 자신의 상태를 저장합니다. 또한 웹 API 및 WCF(Windows Communication Foundation)와 같은 다양한 통신 스택에 연결할 수 있습니다.

**Reliable Actors**: 가상 행위자 프로그래밍 모델을 통해 상태 비저장 및 상태 저장 개체를 작성하는 API입니다. 이 모델은 계산 또는 상태의 독립적인 단위가 많은 경우 유용합니다. 이 모델은 턴 기반 스레딩 모델을 사용하기 때문에 모든 아웃바운드 요청이 완료될 때까지 개별 행위자가 들어오는 다른 요청을 처리할 수 없으므로 다른 행위자 또는 서비스를 호출하는 코드를 방지하는 좋은 방법입니다.

Service Fabric에서 기존 응용 프로그램을 실행할 수도 있습니다.

**컨테이너**: Service Fabric은 Linux에서 Docker 컨테이너의 배포를 지원하고 Hyper-V 격리 모드에 대한 지원과 함께 Windows Server 2016에서 Windows Server 컨테이너의 배포를 지원합니다. Service Fabric [응용 프로그램 모델](service-fabric-application-model.md)에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다. Service Fabric은 모든 컨테이너를 실행할 수 있으며 시나리오는 컨테이너 내에서 기존 응용 프로그램을 패키지하는 게스트 실행 가능한 시나리오와 비슷합니다. 또한 [컨테이너 내에서 Service Fabric 서비스](service-fabric-services-inside-containers.md)를 실행할 수도 있습니다.

**게스트 실행 파일**: Azure Service Fabric에서 Node.js, Java 또는 C++과 같은 모든 종류의 코드를 서비스로 실행할 수 있습니다. Service Fabric에서는 이러한 유형의 서비스를 상태 비저장 서비스로 처리되는 게스트 실행 파일이라고 합니다. Service Fabric 클러스터에서 게스트 실행 파일을 실행하는 이점에는 고가용성, 상태 모니터링, 응용 프로그램 수명 주기 관리, 고밀도 및 검색 기능이 있습니다.

자세한 내용은 [서비스에 대한 프로그래밍 모델 선택](service-fabric-choose-framework.md) 문서를 참조하세요.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
서비스 패브릭에 대해 자세히 알아보려면 다음을 참고하세요.

* [서비스 패브릭의 개요](service-fabric-overview.md)
* [응용 프로그램 구축에 마이크로 서비스 접근 방식이 필요한 이유](service-fabric-overview-microservices.md)
* [응용 프로그램 시나리오](service-fabric-application-scenarios.md)


