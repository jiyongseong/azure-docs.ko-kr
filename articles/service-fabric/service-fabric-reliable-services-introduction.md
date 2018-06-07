---
title: Service Fabric의 신뢰할 수 있는 서비스 프로그래밍 모델 개요 | Microsoft Docs
description: 서비스 패브릭의 신뢰할 수 있는 서비스 프로그래밍 모델에 대해 알아보고 사용자 고유의 서비스 작성을 시작합니다.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 474cc78a4ceb872742ca7eb10837eeb89dcc1bdb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-overview"></a>신뢰할 수 있는 서비스 개요
Azure 서비스 패브릭은 상태 비저장 및 상태 저장 신뢰할 수 있는 서비스의 작성과 관리를 단순화합니다. 이 항목은 다음에 대해 설명합니다.

* 상태 비저장 및 상태 저장 서비스를 위한 신뢰할 수 있는 서비스 프로그래밍 모델.
* 신뢰할 수 있는 서비스를 작성할 때 선택해야 하는 옵션.
* 신뢰할 수 있는 서비스를 사용할 때의 일부 시나리오 및 예와 신뢰할 수 있는 서비스를 작성하는 방법.

신뢰할 수 있는 서비스는 서비스 패브릭에서 사용할 수 있는 프로그래밍 모델 중 하나입니다. 다른 하나는 Reliable Actor 프로그래밍 모델로서 Reliable Services 모델을 기반으로 가상 행위자 프로그래밍 모델을 제공합니다. 신뢰할 수 있는 행위자 프로그래밍 모델에 대한 자세한 내용은 [서비스 패브릭 신뢰할 수 있는 행위자 소개](service-fabric-reliable-actors-introduction.md)를 참조하세요.

서비스 패브릭은 [서비스 패브릭 응용 프로그램 관리](service-fabric-deploy-remove-applications.md)를 통해 프로비저닝 및 배포에서 업그레이드 및 삭제까지 서비스의 수명을 관리합니다.

## <a name="what-are-reliable-services"></a>신뢰할 수 있는 서비스는 무엇입니까?
Reliable Services는 단순하고 강력한 최고 수준의 프로그래밍 모델을 제공하여 응용 프로그램에 중요한 내용을 나타낼 수 있도록 합니다. 신뢰할 수 있는 서비스 프로그래밍 모델은 다음을 제공합니다.

* Service Fabric 프로그래밍 API의 나머지 부분에 액세스합니다. [게스트 실행 파일](service-fabric-guest-executables-introduction.md)로 모델링된 Service Fabric 서비스와는 달리 Reliable Services는 Service Fabric API의 나머지 부분을 직접 사용하게 됩니다. 그러면 서비스에서 다음을 수행할 수 있습니다.
  * 시스템 쿼리
  * 클러스터의 엔터티에 대한 상태 보고
  * 구성 및 코드 변경 내용에 대한 알림 수신
  * 다른 서비스 찾기 및 통신
  * (선택 사항)[신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md) 사용
  * ...및 다른 많은 기능에 액세스 권한 부여, 모두 여러 프로그래밍 언어에서 최고 수준의 프로그래밍 모델에서 비롯됨
* 기존에 사용하던 프로그래밍 모델과 비슷한 사용자 고유의 코드를 실행하기 위한 간단한 모델. 사용자의 코드에는 잘 정의된 진입점과 쉽게 관리되는 수명 주기가 있습니다.
* 플러그형 통신 모델. [Web API](service-fabric-reliable-services-communication-webapi.md), WebSockets, 사용자 지정 TCP 프로토콜 또는 다른 프로그램과 함께 HTTP와 같은 원하는 전송 프로토콜을 사용합니다. 신뢰할 수 있는 서비스는 훌륭한 기본 옵션을 제공하거나 직접 만들 수 있게 지원합니다.
* 상태 저장 서비스의 경우 Reliable Services 프로그래밍 모델을 통해 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)을 사용하여 일관되고 안정적으로 서비스 내에 상태를 바로 저장할 수 있습니다. 신뢰할 수 있는 컬렉션은 C# 컬렉션을 사용해본 적이 있는 사람에게 친숙한 고가용성인 신뢰할 수 있는 컬렉션 클래스의 간단한 집합입니다. 일반적으로 서비스는 신뢰할 수 있는 상태 관리를 위한 외부 시스템이 필요합니다. 신뢰할 수 있는 컬렉션을 사용하면 고가용성 외부 저장소에 기대하게 되는 것과 동일한 고가용성 및 안정성으로 컴퓨터 옆에 상태를 저장할 수 있습니다. 작동하는 데 필요한 계산 및 상태를 공동 배치하기 때문에 이 모델도 대기 시간을 개선합니다.

Reliable Services 개요는 다음 Microsoft Virtual Academy 비디오를 시청하세요. <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>신뢰할 수 있는 서비스가 다른 서비스와 다른 점은 무엇입니까?
서비스 패브릭의 신뢰할 수 있는 서비스는 이전에 작성하던 서비스와 다릅니다. 서비스 패브릭은 안정성, 가용성, 일관성 및 확장성을 제공합니다.

* **안정성** - 사용자 컴퓨터가 실패하거나 네트워크 문제가 발생한 안정적이지 않은 환경 또는 서비스 자체가 오류 및 작동 중단이나 실패가 발생하는 경우에도 서비스가 작동합니다. 상태 저장 서비스의 상태는 네트워크 또는 다른 오류가 있는 경우에도 유지됩니다.
* **가용성** - 연결 가능하고 응답성이 뛰어난 서비스입니다. Service Fabric은 실행 중인 복사본 수를 유지 관리합니다.
* **확장성** – 서비스가 특정 하드웨어에서 분리되고, 하드웨어 또는 다른 리소스의 추가 또는 제거를 통해 필요에 따라 확장 또는 축소될 수 있습니다. 서비스가 부분 오류의 크기를 조정하고 다룰 수 있도록 손쉽게 분할됩니다(특히 상태 저장 상태에서). 코드를 통해 동적으로 서비스를 만들고 삭제할 수 있으며 고객의 요청에 대한 응답 면에서 필요한 만큼 많은 인스턴스를 작동시킬 수 있습니다. 마지막으로 Service Fabric은 간단한 서비스를 권장합니다. Service Fabric을 사용하면 전체 OS 인스턴스 또는 프로세스를 서비스의 단일 인스턴스로 요구하거나 지정하기보다 단일 프로세스 내에 수천 개의 서비스가 프로비저닝될 수 있습니다.
* **일관성** - 이 서비스에 저장된 모든 정보의 일관성을 보장할 수 있습니다. 서비스 내에서 여러 신뢰할 수 있는 컬렉션 간에도 적용됩니다. 서비스 내의 컬렉션을 트랜잭션 원자 방식으로 변경할 수 있습니다.

## <a name="service-lifecycle"></a>서비스 수명 주기
서비스가 상태 저장 서비스이든, 상태 비저장 서비스이든, 신뢰할 수 있는 서비스는 신속하게 코드를 연결하고 시작할 수 있는 간단한 수명 주기를 제공합니다.  서비스를 시작 및 실행하기 위해 구현해야 하는 메서드가 실제로 한두 개뿐입니다.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - 이 메서드는 서비스에서 사용할 통신 스택을 정의합니다. [Web API](service-fabric-reliable-services-communication-webapi.md)와 같은 통신 스택은 수신 끝점 또는 서비스(클라이언트가 서비스에 도달하는 방법)에 대한 끝점을 정의합니다. 표시된 메시지가 서비스 코드의 나머지 부분과 상호 작용하는 방법도 정의합니다.
* **RunAsync** - 이 메서드는 서비스가 해당 비즈니스 논리를 실행하는 위치 및 서비스의 수명 동안 실행해야 하는 모든 백그라운드 작업을 시작하는 위치입니다. 제공되는 취소 토큰은 해당 작업이 중지되어야 하는 경우에 대한 신호입니다. 예를 들어 서비스가 신뢰할 수 있는 큐에서 메시지를 빼내 처리해야 하는 경우 이는 해당 작업이 이뤄지는 위치입니다.

처음으로 Reliable Services를 알아보는 경우 계속 읽어주세요. Reliable Services의 수명 주기에 대한 자세한 안내를 찾는 경우 [이 문서](service-fabric-reliable-services-lifecycle.md)로 건너뛸 수 있습니다.

## <a name="example-services"></a>예제 서비스
이 프로그래밍 모델을 알았으니 두 가지 서비스를 간단히 살펴보고 어떻게 연동되는지 알아보겠습니다.

### <a name="stateless-reliable-services"></a>상태 비저장 신뢰할 수 있는 서비스
상태 비저장 서비스는 호출의 서비스 내에서 유지 관리되는 상태가 없는 위치에 있습니다. 표시된 상태는 완전히 삭제 가능하며 동기화, 복제, 지속성 또는 고가용성이 필요하지 않습니다.

예를 들어 메모리가 없고 모든 용어 및 수행할 작업을 한 번에 수신하는 계산기를 가정해 보겠습니다.

이 경우 서비스가 수행해야 하는 백그라운드 작업 처리가 없으므로 서비스의 `RunAsync()`(C#) 또는 `runAsync()`(Java)는 비어 있을 수 있습니다. 계산기 서비스가 생성되면 일부 포트에서 수신 대기 끝점을 여는 `ICommunicationListener`(C#) 또는 `CommunicationListener`(Java)(예: [Web API](service-fabric-reliable-services-communication-webapi.md))를 반환합니다. 이 수신 대기 끝점은 계산기의 공용 API를 정의하는 다른 계산 메서드(예: "Add(n1, n2)")에 연결됩니다.

클라이언트에서 호출이 수행되면 적절한 메서드가 호출되고 계산기 서비스가 제공된 데이터에 대한 작업을 수행하고 결과를 반환합니다. 상태를 저장하지 않습니다.

모든 내부 상태를 저장하지 않으므로 이 계산기 예제는 간단해집니다. 하지만 대부분의 서비스는 상태 비저장이 아닙니다. 대신 다른 저장소에 상태를 외장화합니다. (예를 들어 세션 상태를 백업 저장소 또는 캐시에 유지하는 모든 웹앱은 상태 비저장이 아닙니다.)

서비스 패브릭에서 상태 비저장 서비스가 사용되는 방법의 일반적인 예로 웹 응용 프로그램에 대한 공용 API를 노출하는 프런트 엔드가 있습니다. 프런트 엔드 서비스는 상태 저장 서비스에 사용자의 요청을 완료하라고 말합니다. 이 경우 상태 비저장 서비스가 수신 대기 중인 알려진 포트(예: 80)에 클라이언트의 호출이 전송됩니다. 이 상태 비저장 서비스는 호출을 받고 해당 호출이 신뢰할 수 있는 대상에서 왔는지 및 어떤 서비스를 대상으로 하는지 확인합니다.  그런 다음 상태 비저장 서비스는 상태 저장 서비스의 올바른 파티션으로 호출을 전달하고 응답을 대기합니다. 상태 비저장 서비스가 응답을 받으면 원래 클라이언트에 응답합니다. 이러한 서비스의 예제는 [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService) 샘플에 있습니다. 샘플에 있는 이 패턴의 한 가지 예제이며 다른 샘플에는 다른 사용자도 있습니다.

### <a name="stateful-reliable-services"></a>상태 저장 신뢰할 수 있는 서비스
상태 저장 서비스는 상태의 일부분이 일관적으로 유지되고 순서대로 있어야 서비스가 작동할 수 있습니다. 수신하는 업데이트에 따라 일부 값의 이동 평균을 지속적으로 계산하는 서비스를 고려할 수 있습니다. 이 서비스를 수행하려면 처리해야 하는 수신 요청의 현재 집합과 현재 평균이 있어야 합니다. 정보를 검색, 처리하고 외부 저장소(예: Azure Blob 또는 현재 테이블 저장소)에 저장하는 모든 서비스는 상태 저장입니다. 외부 상태 저장소에 상태를 보관합니다.

오늘날 대부분의 서비스는 외부에 상태를 저장합니다. 외부 저장소는 해당 상태에 대한 안정성, 가용성, 확장성 및 일관성을 제공하기 때문입니다. Service Fabric에서 서비스는 해당 상태를 외부적으로 저장할 필요가 없습니다. Service Fabric은 서비스 코드와 서비스 상태 모두에 대한 이러한 요구 사항을 처리합니다.

이미지를 처리하는 서비스를 작성하려는 경우를 가정하겠습니다. 이를 위해 서비스는 이미지 및 일련의 변환을 사용하여 해당 이미지에서 수행합니다. 이 서비스는 `ConvertImage(Image i, IList<Conversion> conversions)`와 같은 API를 노출하는 통신 수신기(WebAPI라고 가정함)를 반환합니다. 요청을 받으면 서비스는 `IReliableQueue`에 저장하고 요청을 추적할 수 있도록 클라이언트에 일부 ID를 반환합니다.

이 서비스에서는 `RunAsync()`가 더 복잡해질 수 있습니다. 서비스에는 `IReliableQueue`에서 요청을 가져오는 해당 `RunAsync()` 내에 루프가 있어서 요청된 변환을 수행합니다. 결과는 `IReliableDictionary`에 저장되므로 클라이언트가 다시 돌아오면 변환된 이미지를 가져올 수 있습니다. 어떤 오류가 발생해도 이미지가 손실되지 않도록 하기 위해 이 신뢰할 수 있는 서비스는 큐에서 요청을 가져오고, 변환을 수행하고, 단일 트랜잭션에 모든 결과를 저장합니다. 이 경우 변환이 완료되면 메시지는 큐 및 결과 사전에 저장된 결과에서만 제거됩니다. 또는 서비스가 큐에서 이미지를 제거하고 즉시 원격 저장소에 저장할 수 있습니다. 그러면 서비스에서 관리해야 하는 상태의 양을 감소시키지만 서비스가 필요한 메타데이터를 유지하여 원격 저장소를 관리해야 하기 때문에 복잡성을 증가시킵니다. 두 가지 방법 모두 중간에 실패했다면 처리되기를 대기하는 큐에 요청이 남게 됩니다.

이 서비스에 대해 알아두어야 할 한 가지는 일반 .NET 서비스처럼 보인다는 것입니다. 유일한 차이점은 사용 중인 데이터 구조(`IReliableQueue` 및 `IReliableDictionary`)를 Service Fabric에서 제공하므로 안정성, 가용성 및 일관성이 매우 높다는 점입니다.

## <a name="when-to-use-reliable-services-apis"></a>신뢰할 수 있는 서비스 API를 사용해야 하는 경우
응용 프로그램 서비스 요구 사항이 다음에 해당할 경우 신뢰할 수 있는 서비스 API를 고려해야 합니다.

* 서비스의 코드 및 필요에 따라 상태를 항상 사용 가능하고 신뢰할 수 있도록 만들려고 합니다.
* 여러 상태 단위(예: 주문 및 주문 품목)에 걸쳐 트랜잭션 보장이 필요합니다.
* 응용 프로그램의 상태를 신뢰할 수 있는 사전 및 큐로 자연스럽게 모델링할 수 있습니다.
* 응용 프로그램 코드 또는 상태는 대기 시간이 낮은 읽기 및 쓰기에서 가용성이 높아야 합니다.
* 응용 프로그램이 신뢰할 수 있는 하나 이상의 컬렉션에서 동시성 또는 트랜잭션 처리 작업의 세분성을 제어해야 합니다.
* 서비스에 대한 통신을 제어하고 분할 체계를 제어하고자 합니다.
* 코드에 자유 스레드된 런타임 환경이 필요합니다.
* 응용 프로그램이 런타임 시 신뢰할 수 있는 사전이나 큐 또는 전체 서비스를 동적으로 만들거나 삭제해야 합니다.
* Service Fabric에서 서비스의 상태에 대해 제공하는 백업 및 복원 기능을 프로그래밍 방식으로 제어해야 합니다.
* 응용 프로그램에서 상태 단위에 대한 변경 기록을 유지 관리해야 합니다.
* 사용자 지정 상태 제공자를 직접 개발하거나 타사를 통해 개발하고자 합니다.

## <a name="next-steps"></a>다음 단계
* [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
* [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)
* [신뢰할 수 있는 행위자 프로그래밍 모델](service-fabric-reliable-actors-introduction.md)
