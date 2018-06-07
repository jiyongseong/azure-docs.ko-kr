---
title: Azure Service Fabric의 서비스 원격 호출 | Microsoft Docs
description: 서비스 패브릭 원격 호출을 사용하면 클라이언트와 서비스가 원격 프로시저 호출을 사용하여 서비스와 통신할 수 있도록 합니다.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 074c428662abb5c3acf86835f6fedbf3f8791acf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212979"
---
# <a name="service-remoting-with-reliable-services"></a>Reliable Services로 서비스 원격 호출
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux에서 Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Reliable Services 프레임워크는 서비스에 대한 원격 프로시저 호출을 쉽고 빠르게 설정하기 위한 원격 메커니즘을 제공합니다.

## <a name="set-up-remoting-on-a-service"></a>서비스에 원격 호출 설정
서비스에 대한 원격 호출은 간단한 두 가지 단계로 수행됩니다.

1. 서비스로 구현할 인터페이스를 만듭니다. 이 인터페이스는 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의합니다. 메서드는 작업을 반환하는 비동기 메서드여야 합니다. 인터페이스는 서비스에 원격 호출 인터페이스가 있다는 것을 신호하기 위해 `microsoft.serviceFabric.services.remoting.Service` 를 구현해야 합니다.
2. 서비스에서 원격 수신기를 사용합니다. 이것은 원격 호출 기능을 제공하는 `CommunicationListener` 구현입니다. `FabricTransportServiceRemotingListener`를 사용하여 기본 원격 전송 프로토콜을 통해 원격 수신기를 만들 수 있습니다.

예를 들어 다음의 상태 비저장 서비스는 단일 메서드를 노출하여 원격 프로시저 호출에 "Hello World"를 가져옵니다.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> 서비스 인터페이스의 인수 및 반환 형식은 간단한 형식, 복합 형식 또는 사용자 지정 형식이 가능하지만 serialization이 가능해야 합니다.
>
>

## <a name="call-remote-service-methods"></a>원격 서비스 메서드 호출
원격 스택을 사용하여 서비스에서 메서드를 호출하는 작업은 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 클래스를 통해 서비스에 대한 로컬 프록시를 사용하여 수행됩니다. `ServiceProxyBase` 메서드는 서비스가 구현하는 것과 동일한 인터페이스를 사용하여 로컬 프록시를 만듭니다. 이 프록시를 통해 원격으로 인터페이스의 메서드를 간단하게 호출할 수 있습니다.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

원격 호출 프레임워크는 서비스에 throw된 예외를 클라이언트에 전파합니다. 따라서 `ServiceProxyBase` 을 사용하여 클라이언트에서 논리를 예외 처리하는 작업은 서비스가 throw하는 예외를 직접 처리할 수 있습니다.

## <a name="service-proxy-lifetime"></a>서비스 프록시 수명
ServiceProxy 만들기는 가벼운 작업이므로 사용자가 원하는 만큼 만들 수 있습니다. 서비스 프록시는 사용자가 필요할 때까지 다시 사용할 수 있습니다. 사용자는 예외 발생 시 동일한 프록시를 다시 사용할 수 있습니다. 각 서비스 프록시는 유선으로 메시지를 보내는 데 사용되는 통신 클라이언트를 포함합니다. API를 호출하는 동안 사용된 통신 클라이언트가 유효한지 내부적으로 확인합니다. 그 결과에 따라 통신 클라이언트를 다시 만듭니다. 따라서 사용자는 예외 발생 시 serviceproxy를 다시 작성할 필요가 없습니다.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 수명
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory)는 다른 원격 인터페이스를 위한 프록시를 만드는 팩터리입니다. 프록시를 만들기 위해 API `ServiceProxyBase.create`를 사용하는 경우 프레임워크에서 `FabricServiceProxyFactory`를 만듭니다.
[ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) 속성을 재정의해야 하는 경우 수동으로 만드는 것이 유용합니다.
팩터리는 비용이 많이 드는 작업입니다. `FabricServiceProxyFactory`는 통신 클라이언트의 캐시를 유지 관리합니다.
`FabricServiceProxyFactory`를 가능한 한 오랫동안 캐시하는 것이 가장 좋습니다.

## <a name="remoting-exception-handling"></a>원격 예외 처리
서비스 API에 의해 throw되는 모든 원격 예외는 RuntimeException 또는 FabricException으로 클라이언트에 다시 전송됩니다.

ServiceProxy는 만들어진 서비스 파티션에 대한 모든 장애 조치(failover) 예외를 처리합니다. 장애 조치(Failover) 예외(영구적인 예외)가 있는 경우 끝점을 다시 확인하고 올바른 끝점으로 호출을 다시 시도합니다. 장애 조치(Failover) 예외에 대한 재시도 횟수는 무한합니다.
TransientExceptions의 경우에는 호출만 다시 시도합니다.

기본 재시도 매개 변수는 [OperationRetrySettings]에서 제공됩니다. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) 사용자는 OperationRetrySettings 개체를 ServiceProxyFactory 생성자에 전달하여 이러한 값을 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Reliable Services에 대한 통신 보안 유지](service-fabric-reliable-services-secure-communication.md)
