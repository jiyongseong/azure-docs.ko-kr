---
title: Azure에서 PowerShell을 사용하여 Traffic Manager 관리 | Microsoft Docs
description: Azure Resource Manager과 함께 Traffic Manager용 powershell 사용
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="using-powershell-to-manage-traffic-manager"></a>PowerShell을 사용하여 Traffic Manager 관리

Azure의 서비스용 관리 인터페이스로는 기본적으로 Azure Resource Manager가 사용됩니다. Azure Resource Manager 기반 API 및 도구를 사용하여 Azure Traffic Manager 프로파일을 관리할 수 있습니다.

## <a name="resource-model"></a>리소스 모델

Azure Traffic Manager는 Traffic Manager 프로필을 호출하는 설정 모음을 사용하여 구성됩니다. 이 프로필에는 DNS 설정, 트래픽 라우팅 설정, 끝점 모니터링 설정 및 트래픽이 라우팅되는 서비스 끝점 목록이 포함됩니다.

각 Traffic Manager 프로필은 'TrafficManagerProfiles' 유형의 리소스로 표시됩니다. REST API 수준에서 각 프로필의 URI는 다음과 같습니다.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell 설정

이러한 지침은 Microsoft Azure PowerShell을 사용합니다. 다음 문서는 Azure PowerShell 설치 및 구성하는 방법을 설명합니다.

* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)

이 문서의 예제에서는 기존 리소스 그룹이 있다고 가정합니다. 다음 명령을 사용하여 리소스 그룹을 만들 수 있습니다.

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager를 사용하려면 모든 리소스 그룹에 위치가 있어야 합니다. 이 위치는 해당 리소스 그룹에서 만들어진 리소스의 기본 위치로 사용됩니다. 하지만 Traffic Manager 프로필 리소스는 영역별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure Traffic Manager에 영향을 주지 않습니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

Traffic Manager 프로필을 만들려면 `New-AzureRmTrafficManagerProfile` cmdlet를 사용합니다.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

다음 표는 매개 변수를 설명합니다.

| 매개 변수 | 설명 |
| --- | --- |
| Name |Traffic Manager 프로필 리소스의 ARM 리소스 이름입니다. 동일한 리소스 그룹의 프로필 이름은 고유해야 합니다. 이 이름은 DNS 쿼리에 사용되는 DNS 이름과 구분됩니다. |
| ResourceGroupName |프로필 리소스가 포함된 리소스 그룹의 이름. |
| TrafficRoutingMethod |DNS 쿼리에 대한 응답으로 반환되는 끝점을 결정하는 데 사용되는 트래픽 라우팅 메서드를 지정합니다. 가능한 값은 '성능', '가중' 또는 '우선 순위'입니다. |
| RelativeDnsName |이 Traffic Manager 프로필을 통해 제공되는 DNS 이름의 호스트 이름 부분을 지정합니다. 이 값은 프로필의 FQDN(정규화된 도메인 이름)을 형성하여 Azure Traffic Manager가 사용하는 DNS 도메인 이름과 결합됩니다. 예를 들어 'contoso'의 값이 'contoso.trafficmanager.net.'이 되도록 설정합니다. |
| TTL |초 단위로 DNS TTL(Time-to-Live)을 지정합니다. 이 TTL은 로컬 DNS 확인자 및 DNS 클라이언트에게 이 Traffic Manager 프로필에 대한 DNS 응답을 캐시하는 데 걸린 시간을 알려줍니다. |
| MonitorProtocol |끝점 상태를 모니터링 하는 데 사용할 프로토콜을 지정합니다. 가능한 값은 ‘HTTP’ 및 ‘HTTPS’입니다. |
| MonitorPort |끝점 상태를 모니터링 하는 데 사용할 TCP 포트를 지정합니다. |
| MonitorPath |끝점 상태를 조사하는 데 사용되는 끝점 도메인 이름에 대한 상대 경로를 지정합니다. |

cmdlet는 Azure에서 Traffic Manager 프로필 만들고 해당 프로필 개체를 PowerShell에 반환합니다. 이 시점에 프로필에 끝점이 없습니다. Traffic Manager 프로필에 끝점을 추가하는 방법에 대한 자세한 내용은 [Traffic Manager 끝점 추가](#adding-traffic-manager-endpoints)를 참조하세요.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager 프로필 가져오기

기존 Traffic Manager 프로필 개체를 검색하려면 `Get-AzureRmTrafficManagerProfle` cmdlet를 사용합니다.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

이 cmdlet은 Traffic Manager 프로필 개체를 반환합니다.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager 프로필 업데이트

Traffic Manager 프로필을 수정하려면 3 단계 프로세스를 따릅니다.

1. `Get-AzureRmTrafficManagerProfile`을 사용하여 프로필을 검색하거나 `New-AzureRmTrafficManagerProfile`에서 반환된 프로필을 사용합니다.
2. 프로필을 수정합니다. 끝점을 추가 및 제거하거나 끝점 또는 프로필 매개 변수를 변경할 수 있습니다. 이러한 변경은 오프라인 작업입니다. 프로필을 나타내는 메모리에 있는 로컬 개체만 변경합니다.
3. `Set-AzureRmTrafficManagerProfile` cmdlet을 사용하여 변경 내용을 커밋합니다.

프로필의 RelativeDnsName을 제외하고 모든 프로필 속성을 변경할 수 있습니다. RelativeDnsName를 변경하려면 프로필 및 새 이름이 있는 새 프로필을 삭제해야 합니다.

다음 예제는 프로필의 TTL을 변경하는 방법을 보여 줍니다.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Traffic Manager 끝점에는 세 가지 종류가 있습니다.

1. **Azure 끝점** 은 Azure에서 호스팅되는 서비스입니다.
2. **외부 끝점**은 Azure 외부에서 호스트되는 서비스입니다.
3. **중첩 끝점**은 Traffic Manager 프로필의 중첩된 계층을 구성하는 데 사용됩니다. 중첩 끝점은 복잡한 응용 프로그램에 대한 고급 트래픽 라우팅 구성을 사용하도록 설정합니다.

세 가지 경우 모두, 끝점은 두 가지 방법으로 추가될 수 있습니다.

1. 앞에서 설명한 3 단계 프로세스를 사용합니다. 이 방법의 장점은 여러 끝점 변경이 단일 업데이트에서 이뤄진다는 점입니다.
2. New-AzureRmTrafficManagerEndpoint cmdlet 사용. 이 cmdlet은 끝점을 단일 작업의 기존 Traffic Manager 프로필에 추가합니다.

## <a name="adding-azure-endpoints"></a>Azure 끝점 추가

Azure 끝점는 Azure에서 호스팅되는 서비스를 나타냅니다. 두 가지 유형의 Azure 끝점이 지원됩니다.

1. Azure Web Apps
2. Azure PublicIpAddress 리소스(부하 분산 장치 또는 가상 머신 NIC에 연결할 수 있음). publicIpAddress에는 Traffic Manager에서 사용되도록 지정된 DNS 이름이 있어야 합니다.

각 경우에 다음이 해당됩니다.

* 서비스는 `Add-AzureRmTrafficManagerEndpointConfig` 또는 `New-AzureRmTrafficManagerEndpoint`의 'targetResourceId' 매개 변수를 사용하여 지정합니다.
* 'Target' 및 'EndpointLocation'은 TargetResourceId에 포함됩니다.
* '가중치' 지정은 선택 사항입니다. 가중치는 '가중' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 지정된 경우, 이 값은 1과 1000 사이의 숫자여야 합니다. 기본값은 '1'입니다.
* '우선 순위' 지정은 선택 사항입니다. 우선 순위는 '우선 순위' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 유효한 값은 낮은 숫자가 더 높은 우선 순위를 나타내도록 한 1부터 1000까지의 숫자입니다. 한 끝점에 대해 지정한 경우 모든 끝점에 대해 지정되어야 합니다. 생략한 경우 '1'부터 시작하는 기본값이 끝점이 나열된 순서대로 적용됩니다.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>예시 1: `Add-AzureRmTrafficManagerEndpointConfig`을 사용하여 웹앱 끝점 추가

이 예제에서는 Traffic Manager 프로필을 만들고 `Add-AzureRmTrafficManagerEndpointConfig` cmdlet을 사용하여 두 개의 웹앱 끝점을 추가합니다.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>예시 2: `New-AzureRmTrafficManagerEndpoint`을 사용하여 publicIpAddress 끝점 추가

이 예제에서는 공용 IP 주소 리소스가 Traffic Manager 프로필에 추가됩니다. 공용 IP 주소는 구성된 DNS 이름이 있어야 하고 VM의 NIC 또는 부하 분산 장치에 바인딩될 수 있습니다.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>외부 끝점 추가

Traffic Manager는 외부 끝점을 사용하여 Azure 외부에서 호스팅되는 서비스에 트래픽을 보냅니다. Azure 끝점과 마찬가지로 외부 끝점은 `Add-AzureRmTrafficManagerEndpointConfig` 뒤 이어 `Set-AzureRmTrafficManagerProfile` 또는 `New-AzureRMTrafficManagerEndpoint`을 사용하여 추가할 수 있습니다.

외부 끝점을 지정하는 경우:

* 'Target' 매개 변수를 사용하여 끝점 도메인 이름을 지정해야 합니다.
* 'Performance' 트래픽 라우팅 메서드를 사용하는 경우 'EndpointLocation'이 필수입니다. 그렇지 않은 경우 선택적입니다. 값은 [올바른 Azure 지역 이름](https://azure.microsoft.com/regions/)이어야 합니다.
* 'Weight' 및 'Priority'는 선택 사항입니다.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>예시 1: `Add-AzureRmTrafficManagerEndpointConfig`과 `Set-AzureRmTrafficManagerProfile`를 사용하여 외부 끝점 추가

이 예제에서는 Traffic Manager 프로필을 만들고 두 개의 외부 끝점을 추가하고 변경 내용을 커밋합니다.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>예시 2: `New-AzureRmTrafficManagerEndpoint`을 사용하여 외부 끝점 추가

이 예제에서는 기존 프로필에 외부 끝점을 추가합니다. 프로필은 프로필 및 리소스 그룹 이름을 사용하여 지정합니다.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>'중첩' 끝점 추가

각 Traffic Manager 프로필은 단일 트래픽 라우팅 방법을 지정합니다. 그러나 단일 Traffic Manager 프로필에서 제공하는 것보다 더 정교한 트래픽 라우팅을 요구하는 시나리오가 있습니다. 둘 이상의 트래픽 라우팅 메서드의 장점을 결합하기 위해 Traffic Manager 프로필을 중첩할 수 있습니다. 중첩된 프로필을 사용하여 더 크고 복잡한 응용 프로그램 배포를 지원하기 위해 기본 Traffic Manager 동작을 재정의할 수 있습니다. 자세한 예시는 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

중첩 끝점은 특정 끝점 유형인 'NestedEndpoints'를 사용하여 부모 프로필에서 구성됩니다. 중첩된 끝점을 지정하는 경우:

* 'targetResourceId' 매개 변수를 사용하여 끝점을 지정해야 합니다.
* 'Performance' 트래픽 라우팅 메서드를 사용하는 경우 'EndpointLocation'이 필수입니다. 그렇지 않은 경우 선택적입니다. 값은 [올바른 Azure 지역 이름](http://azure.microsoft.com/regions/)이어야 합니다.
* Azure 끝점에 대해 'Weight' 및 'Priority'는 선택 사항입니다.
* 'MinChildEndpoints' 매개 변수는 선택적입니다. 기본값은 '1'입니다. 사용할 수 있는 끝점 수가 이 임계값 아래로 떨어지는 경우 부모 프로필은 자식 프로필의 '성능이 저하되었다'고 간주하고 트래픽을 부모 프로필에 있는 다른 끝점으로 전환합니다.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>예시 1: `Add-AzureRmTrafficManagerEndpointConfig`과 `Set-AzureRmTrafficManagerProfile`를 사용하여 중첩 끝점 추가

이 예제에서는 새 Traffic Manager 자식 및 부모 프로필을 만들고 자식을 부모의 중첩 끝점으로 추가하고 변경 내용을 커밋합니다.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

이 예제에서는 간단한 설명을 위해 자식 또는 부모 프로필에 다른 끝점을 추가하지 않았습니다.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>예시 2: `New-AzureRmTrafficManagerEndpoint`을 사용하여 중첩 끝점 추가

이 예제에서는 기존 부모 프로필에 기존 자식 프로필을 중첩된 끝점으로 추가합니다. 프로필은 프로필 및 리소스 그룹 이름을 사용하여 지정합니다.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>다른 구독에서 엔드포인트 추가

Traffic Manager는 다른 구독의 엔드포인트에서 사용할 수 있습니다. Traffic Manager에 필요한 입력을 검색하려면 추가하려는 엔드포인트로 구독을 전환해야 합니다. 그런 다음, Traffic Manager 프로필을 포함하는 구독으로 전환하고 여기에 엔드포인트를 추가해야 합니다. 아래 예제에서는 공용 IP 주소로 이 작업을 수행하는 방법을 보여줍니다.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager 끝점 업데이트

기존 Traffic Manager 끝점을 업데이트하는 방법은 두 가지입니다.

1. `Get-AzureRmTrafficManagerProfile`을 사용하여 Traffic Manager 프로필을 가져오고 프로필 내의 끝점 속성을 업데이트하고 `Set-AzureRmTrafficManagerProfile`를 사용하여 변경 내용을 커밋합니다. 이 메서드는 한 번에 끝점을 둘 이상 업데이트할 수 있다는 장점이 있습니다.
2. `Get-AzureRmTrafficManagerEndpoint`을 사용하여 Traffic Manager 끝점 가져오고 끝점 속성을 업데이트하고 `Set-AzureRmTrafficManagerEndpoint`를 사용하여 변경 내용을 커밋합니다. 이 메서드는 프로필에서 끝점 배열로 인덱싱하지 않아도 되므로 훨씬 간단합니다.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>예시 1: `Get-AzureRmTrafficManagerProfile`과 `Set-AzureRmTrafficManagerProfile`를 사용하여 끝점 업데이트

이 예제에서는 기존 프로필 내에서 두 개의 끝점에 대한 우선 순위를 수정합니다.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>예시 2: `Get-AzureRmTrafficManagerEndpoint`과 `Set-AzureRmTrafficManagerEndpoint`를 사용하여 끝점 업데이트

이 예제에서는 기존 프로필에 있는 단일 끝점의 가중치를 수정합니다.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>끝점 및 프로필 활성화 및 비활성화

Traffic Manager는 전체 프로필의 활성화 및 비활성화를 허용할 뿐 아니라 개별 끝점의 활성화 및 비활성화를 허용합니다.
끝점 또는 프로필 리소스 가져오기/업데이트/설정으로 변경할 수 있습니다. 이러한 일반적인 작업을 간소화하기 위해 전용 cmdlet을 통해 지원됩니다.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>예제 1: Traffic Manager 프로필 활성화 및 비활성화

Traffic Manager 프로필을 사용하도록 설정하려면 `Enable-AzureRmTrafficManagerProfile` cmdlet를 사용합니다. 프로필은 프로필 개체를 사용하여 지정할 수 있습니다. 프로필 개체를 파이프라인을 통해 또는 '-TrafficManagerProfile' 매개 변수를 사용하여 전달할 수 있습니다. 이 예제에서는 프로필 개체 및 리소스 그룹 이름을 사용하여 프로필을 지정합니다.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager 프로필을 비활성화하려면:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzureRmTrafficManagerProfile cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>예제 2: Traffic Manager 끝점 활성화 및 비활성화

Traffic Manager 끝점을 활성화하려면 `Enable-AzureRmTrafficManagerEndpoint`을 사용합니다. 끝점을 지정하는 방법은 두 가지입니다

1. 파이프라인을 통해 전달되는 TrafficManagerEndpoint 개체를 사용하거나 '-TrafficManagerEndpoint' 매개 변수를 사용합니다.
2. 끝점 이름, 끝점 형식, 프로필 이름 및 리소스 그룹 이름 사용:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

마찬가지로 Traffic Manager 끝점을 비활성화하려면:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

`Disable-AzureRmTrafficManagerProfile`과 마찬가지로, `Disable-AzureRmTrafficManagerEndpoint` cmdlet는 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager 끝점 삭제

개별 끝점을 제거하려면 `Remove-AzureRmTrafficManagerEndpoint` cmdlet을 사용합니다.

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

이 cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager 프로필 삭제

Traffic Manager 프로필을 삭제하려면 프로필 이름 및 리소스 그룹 이름을 지정하는 `Remove-AzureRmTrafficManagerProfile` cmdlet을 사용합니다.

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

이 cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

삭제할 프로필은 프로필 개체를 사용하여 지정할 수도 있습니다.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

이 순서는 파이프될 수도 있습니다.

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>다음 단계

[Traffic Manager 모니터링](traffic-manager-monitoring.md)

[Traffic Manager 성능 고려 사항](traffic-manager-performance-considerations.md)
