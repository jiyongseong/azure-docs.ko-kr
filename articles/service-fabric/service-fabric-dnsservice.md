---
title: Azure Service Fabric DNS 서비스 | Microsoft Docs
description: Service Fabric의 DNS 서비스를 사용하여 클러스터 내부에서 마이크로 서비스를 검색할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204997"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric의 DNS 서비스
DNS 서비스는 DNS 프로토콜을 통해 다른 서비스를 검색하기 위해 클러스터에서 사용할 수 있는 선택적 시스템 서비스입니다. 

많은 서비스, 특히 컨테이너화된 서비스는 기존 URL 이름을 사용할 수 있으며, 특히 "리프트 앤 시프트" 시나리오에서는 명명 서비스 프로토콜 대신 표준 DNS 프로토콜을 사용하여 이러한 이름을 확인할 수 있으면 효율적입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 끝점 IP 주소를 확인할 수 있습니다. 

DNS 서비스는 DNS 이름을 서비스 이름에 매핑하며, 서비스 이름은 명명 서비스를 통해 확인되어 서비스 끝점이 반환됩니다. 서비스의 DNS 이름은 생성 시 제공됩니다.

![서비스 끝점](./media/service-fabric-dnsservice/dns.png)

동적 포트는 DNS 서비스에서 지원되지 않습니다. 동적 포트에서 노출되는 서비스를 확인하려면 [역방향 프록시 서비스](./service-fabric-reverseproxy.md)를 사용합니다.

## <a name="enabling-the-dns-service"></a>DNS 서비스 사용
포털을 사용하여 클러스터를 만들 때 DNS 서비스는 **클러스터 구성** 메뉴의 **DNS 서비스 포함** 확인란에서 기본적으로 활성화됩니다.

![포털을 통해 DNS 서비스를 사용하도록 설정][2]

클러스터를 만드는 데 포털을 사용하지 않거나 기존 클러스터를 업데이트하는 경우 템플릿에서 DNS 서비스를 활성화해야 합니다.

- 새 클러스터를 배포하기 위해 [샘플 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)을 사용하거나 사용자 고유의 Resource Manager 템플릿을 만들 수 있습니다. 
- 기존 클러스터를 업데이트하기 위해 포털에서 클러스터의 리소스 그룹으로 이동하고 **Automation 스크립트**를 클릭하여 클러스터의 현재 상태를 반영하는 템플릿 및 그룹의 다른 리소스를 사용하여 작업할 수 있습니다. 자세히 알아보려면 [리소스 그룹에서 템플릿 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)를 참조하세요.

템플릿을 가져온 후 다음 단계에 따라 DNS 서비스를 활성화할 수 있습니다.

1. 다음 코드 조각과 같이 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 `2017-07-01-preview` 또는 이후로 설정되었는지 확인하고 이렇게 설정되어 있지 않으면 업데이트합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 아래 코드 조각과 같이 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 DNS 서비스를 사용하도록 설정합니다. 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 위와 같은 변경 내용으로 클러스터 템플릿을 업데이트한 후 변경 내용을 적용하고 업그레이드가 완료되도록 합니다. 업그레이드가 완료되면 DNS 시스템 서비스가 클러스터에서 실행을 시작합니다. 서비스 이름은 `fabric:/System/DnsService`이며, Service Fabric 탐색기의 **시스템** 서비스 섹션 아래에서 찾을 수 있습니다. 


## <a name="setting-the-dns-name-for-your-service"></a>서비스에 대한 DNS 이름 설정
DNS 서비스가 클러스터에서 실행되는 경우 `ApplicationManifest.xml`에서 기본 서비스에 대해 선언적으로 또는 PowerShell 명령을 통해 서비스에 대한 DNS 이름을 설정할 수 있습니다.

서비스의 DNS 이름은 클러스터 전체에서 확인할 수 있습니다. `<ServiceDnsName>.<AppInstanceName>`의 이름 지정 체계를 사용하는 것이 좋습니다(예: `service1.application1`). 이렇게 하면 클러스터 전체에서 DNS 이름의 고유성이 보장됩니다. 응용 프로그램이 Docker Compose를 사용하여 배포되는 경우 서비스는 이 이름 지정 체계를 사용하여 DNS 이름이 자동으로 할당됩니다.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml에서 기본 서비스에 대한 DNS 이름 설정
Visual Studio 또는 원하는 편집기에서 프로젝트를 연 다음 `ApplicationManifest.xml` 파일을 엽니다. 기본 서비스 섹션으로 이동한 다음 각 서비스에 대해 `ServiceDnsName` 특성을 추가합니다. 다음 예제에서는 서비스의 DNS 이름을 `service1.application1`로 설정하는 방법을 보여 줍니다.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
응용 프로그램을 배포한 후 Service Fabric Explorer의 서비스 인스턴스에는 다음 그림에 나와 있는 것처럼 이 인스턴스에 대한 DNS 이름이 표시됩니다. 

![서비스 끝점][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Powershell을 사용하여 서비스에 대한 DNS 이름 설정
`New-ServiceFabricService` Powershell을 사용하여 만들 때 서비스에 대한 DNS 이름을 설정할 수 있습니다. 다음 예제에서는 DNS 이름 `service1.application1`을 사용하여 새로운 상태 비저장 서비스를 만듭니다.

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>서비스에 DNS 사용
둘 이상의 서비스를 배포하는 경우 DNS 이름을 사용하여 통신할 다른 서비스의 끝점을 찾을 수 있습니다. DNS 서비스는 상태 비저장 서비스에만 적용됩니다. DNS 프로토콜은 상태 저장 서비스와 통신할 수 없기 때문입니다. 상태 저장 서비스의 경우 http 호출에 기본 제공 [역방향 프록시 서비스](./service-fabric-reverseproxy.md)를 사용하여 특정 서비스 파티션을 호출할 수 있습니다. 동적 포트는 DNS 서비스에서 지원되지 않습니다. 역방향 프록시를 사용하여 동적 포트를 사용하는 서비스를 확인할 수 있습니다.

다음 코드는 URL의 일부분으로 포트와 선택적 경로를 제공하는 일반 http 호출인 다른 서비스를 호출하는 방법을 보여 줍니다.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>다음 단계
[서비스와 연결 및 통신](service-fabric-connect-and-communicate-with-services.md)에서 클러스터 내의 서비스 통신에 대해 자세히 알아봅니다.

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
