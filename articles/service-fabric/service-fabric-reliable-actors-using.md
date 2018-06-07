---
title: Azure Service Fabric 행위자에서 기능 구현 | Microsoft Docs
description: StatefulService를 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현하는 고유의 행위자 서비스를 작성하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>행위자 서비스에서 서비스 수준 기능 구현
[서비스 계층](service-fabric-reliable-actors-platform.md#service-layering)에 설명된 대로 행위자 서비스 자체는 신뢰할 수 있는 서비스입니다.  `ActorService`에서 파생된 고유한 서비스를 작성할 수 있고 다음과 같이 StatefulService를 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현할 수 있습니다.

- 서비스 백업 및 복원
- 모든 행위자에 대한 공유 기능(예: 회로 차단기)
- 행위자 서비스 자체 및 각 개별 행위자에서의 원격 프로시저 호출

## <a name="using-the-actor-service"></a>행위자 서비스 사용
행위자 인스턴스는 실행 중인 행위자 서비스에 대한 액세스 권한이 있습니다. 행위자 인스턴스는 행위자 서비스를 통해 프로그래밍 방식으로 서비스 컨텍스트를 얻을 수 있습니다. 서비스 컨텍스트에는 파티션 ID, 서비스 이름, 응용 프로그램 이름 및 기타 Service Fabric 플랫폼 관련 정보가 있습니다.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Reliable Services처럼 행위자 서비스는 Service Fabric 런타임에서 서비스 유형으로 등록되어야 합니다. 행위자 인스턴스를 실행하는 행위자 서비스의 경우 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다. 가장 간단한 경우 행위자 형식만을 등록할 수 있고 기본 설정이 있는 행위자 서비스는 암시적으로 다음과 같은 경우 사용됩니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

또는 행위자 서비스를 직접 생성하는 등록 메서드가 제공하는 람다를 사용할 수 있습니다. 그런 다음 생성자를 통해 행위자에 종속성을 주입할 수 있는 행위자 인스턴스를 명시적으로 생성하고 행위자 서비스를 구성할 수 있습니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>행위자 서비스 메서드
행위자 서비스는 `IActorService`(C#) 또는 `ActorService`(Java)를 구현하며 이는 `IService`(C#) 또는 `Service`(Java)를 구현합니다. 그러면 원격 서비스 메서드에서 프로시저 호출을 허용하지 않는 Reliable Services 원격 서비스에서 사용되는 인터페이스입니다. 원격 서비스를 통해 원격으로 호출할 수 있는 서비스 수준 메서드가 포함되어 있으며 행위자를 [열거](service-fabric-reliable-actors-enumerate.md) 및 [삭제](service-fabric-reliable-actors-delete-actors.md)할 수 있습니다.


## <a name="custom-actor-service"></a>사용자 지정 행위자 서비스
행위자 등록 람다를 사용하여 `ActorService`(C#) 및 `FabricActorService`(Java)에서 파생된 사용자 지정 행위자 서비스를 등록할 수 있습니다. 이 사용자 지정 행위자 서비스에서 `ActorService`(C#) 또는 `FabricActorService`(Java)를 상속하는 서비스 클래스를 작성하여 고유한 서비스 수준 기능을 구현할 수 있습니다. 사용자 지정 행위자 서비스는 `ActorService`(C#) 또는 `FabricActorService`(Java)로부터 행위자 런타임 기능을 모두 상속하고 고유한 서비스 메서드를 구현하는 데 사용될 수 있습니다.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>행위자 백업 및 복원 구현
사용자 지정 행위자 서비스가 `ActorService`에 이미 나타난 원격 수신기를 활용하여 행위자 데이터를 백업하는 메서드를 노출할 수 있습니다.  예를 들어 [행위자 Backup 및 복원](service-fabric-reliable-actors-backup-and-restore.md)을 참조하세요.

## <a name="actor-using-remoting-v2-stack"></a>원격 V2 스택을 사용하는 작업자
이제 사용자는 2.8 nuget 패키지와 함께 Remoting V2 스택을 사용할 수 있습니다. 이 스택은 성능이 우수하며 사용자 지정 직렬화와 같은 기능을 제공합니다. Remoting V2는 기존 Remoting 스택(이제부터 V1 Remoting 스택이라 함)과 호환되지 않습니다.

Remoting V2 스택을 사용하려면 다음과 같은 변경 내용이 필요합니다.
 1. 다음과 같은 어셈블리 특성을 작업자 인터페이스에 추가합니다.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. 시작하려면 V2 스택을 사용하여 ActorService 및 Actor Client 프로젝트를 빌드하고 업그레이드합니다.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>서비스 가용성에 영향을 주지 않고 Remoting V2 Stack으로 Actor Service 업그레이드.
이 변경은 2단계 업그레이드입니다. 열거된 순서에 따라 단계를 따릅니다.

1.  다음과 같은 어셈블리 특성을 작업자 인터페이스에 추가합니다. 이 특성은 ActorService인 V1(기존)과 V2 Listener에 대한 두 가지 수신기를 시작합니다. 이 변경을 통해 ActorService를 업그레이드합니다.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. 위의 업그레이드를 마친 후 ActorClients를 업그레이드합니다.
이 단계를 수행하려면 Actor Proxy가 Remoting V2 스택을 사용 중인지 확인합니다.

3. 이 단계는 옵션입니다. V1 수신기를 제거하려면 위의 특성을 변경합니다.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>다음 단계
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
