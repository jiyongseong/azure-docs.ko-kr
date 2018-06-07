---
title: Service Fabric 프로그래밍 모델 개요 | Microsoft Docs
description: 서비스 패브릭은 서비스 빌드에 대해 행위자 프레임워크 및 서비스 프레임워크라는 두 가지 프레임워크를 제공합니다. 이 두 프레임 간에는 단순성과 제어 면에서 고유의 장단점이 있습니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: a03bb3c74d9c776b893b11c3dec8788fe9ac598c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-programming-model-overview"></a>서비스 패브릭 프로그래밍 모델 개요
서비스 패브릭은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다. 서비스는 플랫폼의 기능과 응용 프로그램 프레임워크를 최대한 활용하기 위해 Service Fabric API를 사용할 수 있습니다. 서비스는 Service Fabric 클러스터에서 호스트되는 컨테이너에서 실행 중인 모든 언어 또는 코드로 작성된 컴파일된 실행 프로그램일 수도 있습니다.

## <a name="guest-executables"></a>게스트 실행 파일
[게스트 실행 파일](service-fabric-guest-executables-introduction.md)은 응용 프로그램에서 서비스로 실행될 수 있는 임의의 기존 실행 파일(작성 언어는 관계없음)입니다. 게스트 실행 파일은 Service Fabric SDK API를 직접 호출하지 않습니다. 그러나 Service Fabric에서 노출하는 REST API를 호출하여 서비스 검색 가능성, 사용자 지정 상태 및 로드 보고와 같이 플랫폼에서 제공하는 기능을 계속 활용합니다. 또한 전체 응용 프로그램 수명 주기 지원도 포함합니다.

첫 번째 [게스트 실행 파일 응용 프로그램](service-fabric-deploy-existing-app.md)을 배포하여 게스트 실행 파일을 시작합니다.

## <a name="containers"></a>컨테이너
기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. Service Fabric도 [컨테이너](service-fabric-containers-overview.md)에 서비스를 배포할 수 있습니다. Service Fabric은 Windows Server 2016에서 Linux 컨테이너 및 Windows 컨테이너의 배포를 지원합니다. 컨테이너 이미지는 컨테이너 저장소에서 가져오고 컴퓨터에 배포될 수 있습니다. 기존 응용 프로그램을 컨테이너에서 게스트 실행 파일, Service Fabric 상태 비저장 또는 상태 저장 서비스, Reliable Actors로 배포할 수 있으며, 프로세스의 서비스와 컨테이너의 서비스를 동일한 응용 프로그램에서 함께 사용할 수 있습니다.

[Windows 또는 Linux에서 서비스를 컨테이너화하는 방법에 대해 자세히 알아보기](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services는 서비스 패브릭 플랫폼과 통합하여 전체 플랫폼 기능을 활용하는 서비스 작성을 위한 간단한 프레임워크입니다. Reliable Services는 서비스 패브릭 런타임이 서비스의 수명 주기를 관리하고 서비스가 런타임과 상호 작용할 수 있도록 하는 최소한의 API 집합을 제공합니다. 응용 프로그램 프레임워크는 최소한의 부담으로 완전한 설계 및 구현 제어를 제공하며, 이를 통해 ASP.NET Core 등의 다른 응용 프로그램 프레임워크를 호스팅할 수 있습니다.

Reliable Services는 웹 서버나 등, 대부분의 서비스 플랫폼과 유사하게 상태 비저장이 될 수 있습니다. 여기서는 서비스의 각 인스턴스가 동등하게 생성되고 상태가 Azure DB나 Azure Table Storage 같은 외부 솔루션에 유지됩니다.

Reliable Services는 서비스 패브릭 단독으로 상태를 저장할 수도 있습니다. 여기서는 Reliable Collections를 사용하여 서비스 자체에 상태가 직접적으로 유지됩니다. 상태는 복제를 통해 고가용성이 유지되고 분할을 통해 배포되며, 모두 서비스 패브릭에서 자동으로 관리합니다.

[Reliable Actors에 대해 자세히 알아보거나](service-fabric-reliable-services-introduction.md) [첫 번째 Reliable Services를 작성해 보세요.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core는 웹앱, IoT 앱 및 모바일 백 엔드와 같은 최신 클라우드 기반의 인터넷 연결 응용 프로그램을 빌드하기 위한 새로운 오픈 소스 겸 플랫폼 간 프레임워크입니다. Service Fabric은 ASP.NET Core와 통합되므로 신뢰할 수 있는 컬렉션 및 Service Fabric의 고급 오케스트레이션 기능을 활용하는 상태 비저장 및 상태 저장 ASP.NET Core 응용 프로그램을 작성할 수 있습니다.

[첫 번째 ASP.NET Core Service Fabric 응용 프로그램을 작성](service-fabric-reliable-services-communication-aspnetcore.md)하여 [Service Fabric의 ASP.NET Core에 대해 자세히 알아보세요](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="reliable-actors"></a>Reliable Actors
Reliable Services의 최상위에 구축되는 Reliable Actor 프레임워크는 행위자 설계 패턴을 기준으로 가상 행위자 패턴을 구현하는 응용 프로그램 프레임워크입니다. Reliable Actor 프레임워크는 행위자라고 하는 단일 스레드 실행을 통해 독립적인 계산 단위 및 상태를 사용합니다. Reliable Actor 프레임워크는 행위자와 사전 설정 상태 지속성 및 확장 구성에 대해 기본 포함된 통신을 제공합니다.

Reliable Actors 자체는 Reliable Services에 구축된 응용 프로그램 프레임워크이므로 Service Fabric 플랫폼과 완전히 통합되며 플랫폼이 제공하는 모든 기능을 완벽히 활용할 수 있습니다.

[Reliable Actors에 대해 자세히 알아보거나](service-fabric-reliable-actors-introduction.md) [첫 번째 Reliable Actor 서비스 작성](service-fabric-reliable-actors-get-started.md)


[ASP.NET Core를 사용하여 프런트 엔드 서비스 빌드](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>다음 단계
[Service Fabric 및 컨테이너 개요](service-fabric-containers-overview.md)

[Reliable Services 개요](service-fabric-reliable-services-introduction.md)

[Reliable Actors 개요](service-fabric-reliable-actors-introduction.md)

[Service Fabric 및 ASP.NET Core ](service-fabric-reliable-services-communication-aspnetcore.md)




