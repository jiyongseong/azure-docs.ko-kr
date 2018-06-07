---
title: ".NET에서 Azure Relay WCF 릴레이 시작 | Microsoft Docs"
description: "Azure Relay WCF 릴레이를 사용하여 서로 다른 위치에서 호스팅되는 두 응용 프로그램을 연결하는 방법에 대해 알아봅니다."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>.NET에서 Azure Relay WCF 릴레이를 사용하는 방법
이 문서에서는 Azure Relay 서비스를 사용하는 방법을 설명합니다. 이 샘플은 C#으로 작성되었으며 Service Bus 어셈블리에 포함된 확장과 함께 WCF(Windows Communication Foundation) API를 사용합니다. Azure Relay에 대한 자세한 내용은 [Azure Relay 개요](relay-what-is-it.md)를 참조하세요.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>WCF 릴레이란?

Azure [*WCF 릴레이*](relay-what-is-it.md) 서비스를 사용하면 Azure 데이터 센터와 고유한 온-프레미스 엔터프라이즈 환경 둘 다에서 실행되는 하이브리드 응용 프로그램을 빌드할 수 있습니다. 릴레이 서비스는 방화벽 연결을 열거나 회사 네트워크 인프라를 주입식으로 변경하지 않고도 회사 엔터프라이즈 네트워크 내에 있는 WCF(Windows Communication Foundation) 서비스를 공용 클라우드에 안전하게 노출할 수 있게 함으로써 이 작업을 도와줍니다.

![WCF 릴레이 개념](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Azure Relay를 사용하면 기존 엔터프라이즈 환경 내에서 WCF 서비스를 호스트할 수 있습니다. 그런 다음 이러한 WCF 서비스로 들어오는 세션 및 요청의 수신 대기를 Azure 내에서 실행되는 릴레이 서비스로 위임할 수 있습니다. 이렇게 하면 Azure에서 실행되는 응용 프로그램 코드나 모바일 작업자 또는 엑스트라넷 파트너 환경에 이러한 서비스를 노출할 수 있습니다. 릴레이를 사용하면 이러한 서비스에 액세스할 수 있는 사람을 세부적으로 안전하게 제어할 수 있습니다. 서비스 버스는 기존 엔터프라이즈 솔루션의 응용 프로그램 기능과 데이터를 노출하고 클라우드에서 이용하는 강력하고 안전한 방법을 제공합니다.

이 문서에서는 Azure Relay를 사용하여 TCP 채널 바인딩을 통해 노출되고 두 당사자 간의 보안 대화를 구현하는 WCF 웹 서비스를 만드는 방법을 보여 줍니다.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 다운로드
[Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 는 Service Bus API를 가져오고 모든 Service Bus 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다. 프로젝트에서 NuGet 패키지를 설치하려면 다음을 수행합니다.

1. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
2. "Service Bus"를 검색하고 **Microsoft Azure Service Bus** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 다음의 대화 상자를 닫습니다.
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>SOAP 웹 서비스를 노출하고 TCP와 함께 이용
기존 WCF SOAP 웹 서비스를 외부에서 이용할 수 있도록 노출하려면 서비스 바인딩과 주소를 변경해야 합니다. 이 경우 WCF 서비스를 설정 및 구성한 방법에 따라 구성 파일이나 코드를 변경해야 할 수도 있습니다. WCF를 사용하면 동일한 서비스에 대해 여러 개의 네트워크 끝점을 가질 수 있으므로 외부 액세스를 위한 릴레이 끝점을 추가하는 동시에 기존 내부 끝점을 유지할 수 있습니다.

이 작업에서는 간단한 WCF 서비스를 빌드하고 릴레이 수신기를 해당 서비스에 추가합니다. 이 예제에서는 Visual Studio에 대한 기본적인 지식이 있다고 가정하므로 프로젝트를 만드는 방법을 자세히 설명하지는 않으며, 대신 코드에 중점을 둡니다.

아래 단계를 시작하기 전에 다음 절차를 완료하여 환경을 설정합니다.

1. Visual Studio 내에서 솔루션에 "Client" 및 "Service"의 두 프로젝트가 포함된 콘솔 응용 프로그램을 만듭니다.
2. 두 프로젝트에 Service Bus NuGet 패키지를 추가합니다. 이 패키지는 필요한 모든 어셈블리 참조를 프로젝트에 추가됩니다.

### <a name="how-to-create-the-service"></a>서비스를 만드는 방법
먼저 서비스 자체를 만듭니다. 모든 WCF 서비스는 적어도 다음 세 가지 요소로 구성되어 있습니다.

* 교환되는 메시지 및 호출해야 하는 작업을 설명하는 계약 정의
* 해당 계약의 구현
* WCF 서비스를 호스트하고 여러 끝점을 노출하는 호스트

이 섹션의 코드 예제에서는 이러한 각 구성 요소를 다룹니다.

계약은 두 개의 숫자를 더하고 결과를 반환하는 단일 작업 `AddNumbers`를 정의합니다. `IProblemSolverChannel` 인터페이스를 사용하면 클라이언트에서 프록시 수명을 더 쉽게 관리할 수 있습니다. 이러한 인터페이스 생성은 모범 사례로 간주됩니다. 이 계약 정의를 별도의 파일에 저장하여 "Client" 및 "Service" 프로젝트 둘 다에서 해당 파일을 참조할 수 있게 하는 것이 좋지만 두 프로젝트에 코드를 복사할 수도 있습니다.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

계약이 준비되면 다음과 같이 구현합니다.

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>프로그래밍 방식으로 서비스 호스트를 구성
계약과 구현이 준비되었으면 이제 서비스를 호스트할 수 있습니다. 호스팅은 서비스 인스턴스를 관리하고 메시지를 수신 대기하는 끝점을 호스트하는 [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx) 개체 내부에서 수행됩니다. 다음 코드는 일반적인 로컬 끝점과 릴레이 끝점 둘 다로 서비스를 구성하여 내부 및 외부 끝점의 모양을 나란히 보여 줍니다. *네임스페이스* 문자열을 해당 네임스페이스 이름으로 바꾸고 *yourKey*를 이전 설정 단계에서 얻은 SAS 키로 바꿉니다.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

예제에서는 동일한 계약 구현에 있는 두 개의 끝점을 만듭니다. 하나는 로컬 끝점이며 다른 하나는 Azure Relay를 통해 프로젝션됩니다. 두 끝점 사이의 중요한 차이점은 바인딩입니다. 즉, 로컬 끝점에는 [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx)이 사용되고 릴레이 끝점 및 주소에는 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding)이 사용됩니다. 로컬 끝점에는 특정 포트가 포함된 로컬 네트워크 주소가 있습니다. 릴레이 끝점에는 문자열 `sb`, 해당 네임스페이스 이름 및 경로 "solver"로 구성된 끝점 주소가 있습니다. 이렇게 하면 URI `sb://[serviceNamespace].servicebus.windows.net/solver`가 생성되며, 정규화된 외부 DNS 이름을 사용하여 서비스 끝점을 Service Bus(릴레이) TCP 끝점으로 식별합니다. 자리 표시자를 바꾸는 코드를 **서비스** 응용 프로그램의 `Main` 함수에 배치하면 서비스가 정상적으로 작동합니다. 서비스가 릴레이에서만 수신 대기하도록 하려는 경우 로컬 끝점 선언을 제거합니다.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>App.config 파일에서 서비스 호스트를 구성
App.config 파일을 사용하여 호스트를 구성할 수도 있습니다. 이 예제에서 코드를 호스팅하는 서비스를 다음 예제에 표시합니다.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

끝점 정의가 App.config 파일로 이동합니다. NuGet 패키지의 App.config 파일에는 다양한 정의가 이미 추가되었으며 이는 Azure Relay의 구성 확장에 필요합니다. 이전 코드와 똑같은 다음 예제가 **system.serviceModel** 요소 바로 아래에 표시되어야 합니다. 이 코드 예제에서는 프로젝트 C# 네임스페이스의 이름이 **Service**라고 가정합니다.
자리 표시자를 해당 릴레이 네임스페이스 이름과 SAS 키로 바꿉니다.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

이렇게 변경하면 서비스가 이전처럼 시작되지만 두 개의 라이브 끝점이 있습니다. 하나는 로컬이고 다른 하나는 클라우드에서 수신 대기합니다.

### <a name="create-the-client"></a>클라이언트 만들기
#### <a name="configure-a-client-programmatically"></a>프로그래밍 방식으로 클라이언트를 구성
서비스를 이용하기 위해 [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) 개체를 사용하여 WCF 클라이언트를 구성할 수 있습니다. Service Bus는 SAS를 사용하여 구현된 토큰 기반 보안 모델을 사용합니다. [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 클래스는 잘 알려진 일부 토큰 공급자를 반환하는 기본 제공 팩터리 메서드를 사용하여 보안 토큰 공급자를 나타냅니다. 다음 예제에서는 [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) 메서드를 사용하여 적절한 SAS 토큰의 수집을 처리합니다. 이전 섹션에서 설명한 대로 이름과 키는 포털에서 얻은 것입니다.

먼저 서비스의 `IProblemSolver` 계약 코드를 클라이언트 프로젝트에 참조하거나 복사합니다.

그런 다음 자리 표시자 텍스트를 해당 릴레이 네임스페이스와 SAS 키로 바꾸어 클라이언트 `Main` 메서드의 코드를 바꿉니다.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

이제 클라이언트와 서비스를 빌드한 후에 실행할 수 있으며(서비스 먼저 실행) 클라이언트가 서비스를 호출하고 **9**를 인쇄합니다. 클라이언트와 서버를 서로 다른 컴퓨터 또는 네트워크에서도 실행할 수 있으며, 통신이 제대로 작동합니다. 클라이언트 코드가 클라우드나 로컬에서 실행될 수도 있습니다.

#### <a name="configure-a-client-in-the-appconfig-file"></a>App.config 파일에서 클라이언트를 구성
다음 코드는 App.config 파일을 사용하여 클라이언트를 구성하는 방법을 보여줍니다.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

끝점 정의가 App.config 파일로 이동합니다. 이전에 나열된 코드와 같은 다음 예제는 `<system.serviceModel>` 요소 바로 아래에 표시되어야 합니다. 이전처럼 여기서 자리 표시자를 해당 릴레이 네임스페이스와 SAS 키로 바꾸어야 합니다.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>다음 단계
이제 Azure Relay의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보세요.

* [Azure 릴레이란?](relay-what-is-it.md)
* [Azure Service Bus 아키텍처 개요](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Azure 샘플][Azure samples]에서 Service Bus 샘플을 다운로드하거나 [Service Bus 샘플 개요][overview of Service Bus samples]를 참조하세요.

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
