---
title: 'ExpressRoute 회로 만들기 및 수정: PowerShell: Azure Resource Manager | Microsoft Docs'
description: 이 문서에서는 ExpressRoute 회로를 만들고, 프로비전하고, 확인하고, 업데이트하고, 삭제하고, 프로비전을 해제하는 방법을 설명합니다.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 0d45e97cc42826375a99df16a73c9a7b0c359224
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>PowerShell을 사용하여 ExpressRoute 회로 만들기 및 수정
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [비디오 - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-circuit-classic.md)
>

이 문서에서는 PowerShell cmdlet 및 Azure Resource Manager 배포 모델을 사용하여 Azure ExpressRoute 회로를 만드는 방법을 설명합니다. 이 문서에는 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 나와 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. 자세한 내용은 [Azure PowerShell 개요](/powershell/azure/overview)를 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.


## <a name="create"></a>ExpressRoute 회로 만들기 및 프로비전
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Azure 계정에 로그인하고 구독을 선택합니다.
구성을 시작하려면, Azure 계정에 로그인합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```powershell
Connect-AzureRmAccount
```

계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

ExpressRoute 회로를 만들려는 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. 지원되는 공급자, 위치 및 대역폭 목록을 가져옵니다.
ExpressRoute 회로를 만들기 전에 지원되는 연결 공급자, 위치 및 대역폭 옵션 목록이 필요합니다.

PowerShell cmdlet **Get-AzureRmExpressRouteServiceProvider**는 이후 단계에서 사용할 이 정보를 반환합니다.

```powershell
Get-AzureRmExpressRouteServiceProvider
```

연결 공급자가 여기에 나열되었는지 확인합니다. 나중에 회로를 만들 때 필요한 다음 정보를 적어 둡니다.

* Name
* PeeringLocations
* BandwidthsOffered

이제 ExpressRoute 회로를 만들 준비가 되었습니다.   

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute 회로 만들기
아직 리소스 그룹이 없는 경우 ExpressRoute 회로를 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps ExpressRoute 회로를 만드는 방법을 보여 줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다. 다음 예제를 사용하여 새 서비스 키를 요청합니다.

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

올바른 SKU 계층과 SKU 제품군을 지정하는지 확인합니다.

* SKU 계층은 ExpressRoute 표준 또는 ExpressRoute Premium 추가 기능이 사용되는지 여부를 결정합니다. *표준*을 지정하여 표준 SKU를 가져오거나 프리미엄 추가 기능을 위해 *프리미엄*을 지정할 수 있습니다.
* SKU 제품군은 청구서 유형을 결정합니다. 데이터 요금제의 경우 *Metereddata*를 선택하고 무제한 데이터 요금제의 경우 *Unlimiteddata*를 선택할 수 있습니다. 청구서 유형을 *Metereddata*에서 *Unlimiteddata*로 변경할 수 있지만, *Unlimiteddata*에서 *Metereddata*로는 변경할 수 없습니다.

> [!IMPORTANT]
> ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. 연결 공급자가 회로를 프로비전할 준비가 된 후에 이 작업을 수행하도록 하십시오.
> 
> 

응답에 서비스 키가 포함됩니다. 다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. 모든 ExpressRoute 회로 나열
만들어 놓은 모든 ExpressRoute 회로 목록을 가져오려면 **Get-AzureRmExpressRouteCircuit** 명령을 실행합니다.

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

응답은 다음 예제와 유사합니다.

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

`Get-AzureRmExpressRouteCircuit` cmdlet을 사용하여 이 정보를 언제든지 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 *ServiceKey* 필드에 나열됩니다.

```powershell
Get-AzureRmExpressRouteCircuit
```


응답은 다음 예제와 유사합니다.

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. 프로비전을 위해 연결 공급자에 서비스 키 보내기
*ServiceProviderProvisioningState* 는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공합니다. 상태는 Microsoft 측의 상태를 제공합니다. 회로 프로비전 상태에 대한 자세한 내용은 [워크플로](expressroute-workflows.md#expressroute-circuit-provisioning-states)를 참조하세요.

새 ExpressRoute 회로를 만들면 회로는 다음 상태가 됩니다.

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



연결 공급자가 사용자에 대해 활성화를 처리 중이면 회로가 다음 상태로 변경됩니다.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

ExpressRoute 회로를 사용하려면 다음 상태여야 합니다.

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. 회로 키의 상태를 주기적으로 확인
회로 키의 상태를 확인하면 공급자가 회로를 사용하도록 설정한 시점을 알 수 있습니다. 회로가 구성된 후에는 *ServiceProviderProvisioningState*가 아래 예에서처럼 *Provisioned*로 표시됩니다.

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


응답은 다음 예제와 유사합니다.

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. 라우팅 구성 만들기
회로 피어링을 만들고 수정하는 단계별 지침은 [ExpressRoute 회로 라우팅 구성](expressroute-howto-routing-arm.md) 문서를 참조하세요.

> [!IMPORTANT]
> 이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IP VPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. 가상 네트워크를 ExpressRoute 회로에 연결합니다.
그 다음 가상 네트워크를 ExpressRoute 회로에 연결합니다. Resource Manager 배포 모델을 작업하는 경우에는 [ExpressRoute 회로에 가상 네트워크 연결](expressroute-howto-linkvnet-arm.md) 문서를 사용할 수 있습니다.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute 회로의 상태 가져오기
**Get-AzureRmExpressRouteCircuit** cmdlet을 사용하여 언제든지 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다.

```powershell
Get-AzureRmExpressRouteCircuit
```


응답은 다음 예제와 유사합니다.

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


리소스 그룹 이름 및 회로 이름을 매개 변수 형태로 호출에 전달하면 특정 ExpressRoute 회로에 대한 정보를 가져올 수 있습니다.

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


응답은 다음 예제와 유사합니다.

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>ExpressRoute 회로 수정
연결에 미치는 영향 없이 ExpressRoute 회로의 특정 속성을 수정할 수 있습니다.

가동 중지 시간 없이 다음 작업을 수행할 수 있습니다.

* ExpressRoute 회로에 대해 ExpressRoute 프리미엄 추가 기능을 사용하거나 사용하지 않을 수 있습니다.
* 포트에 사용 가능한 용량이 있는 경우 ExpressRoute 회로의 대역폭을 증가시킵니다. 회로 대역폭 다운그레이드는 지원되지 않습니다. 
* 요금제를 데이터 요금에서 무제한 데이터 요금으로 변경합니다. 요금제를 무제한 데이터 요금에서 데이터 요금으로 변경하는 것은 지원되지 않습니다.
* *Allow Classic Operations*을 활성화하거나 비활성화할 수 있습니다.

제한 및 제한 사항에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium 추가 기능을 활성화하려면
다음 PowerShell 코드 조각을 사용하여 기존 회로에 대해 ExpressRoute 프리미엄 추가 기능을 활성화할 수 있습니다.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

이제 ExpressRoute Premium 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 Premium 추가 기능에 대한 대금 청구가 시작됩니다.

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium 추가 기능을 비활성화하려면
> [!IMPORTANT]
> 표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 이 작업이 실패할 수 있습니다.
> 
> 

다음 정보에 유의하세요.

* 프리미엄을 표준으로 다운그레이드하기 전에 회로에 연결된 가상 네트워크 수가 10개 미만인지 확인해야 합니다. 그렇게 하지 않으면 업데이트 요청이 실패하고, 프리미엄 요금이 청구됩니다.
* 다른 지리적 위치의 모든 가상 네트워크를 연결 해제해야 합니다. 그렇게 하지 않으면 업데이트 요청이 실패하고, 프리미엄 요금이 청구됩니다.
* 사설 피어링을 위해서는 경로 테이블의 경로가 4000개 미만이어야 합니다. 경로 테이블 크기가 4000개 경로 이상이면 BGP 세션이 폐기되고 게시된 프리픽스 수가 4000개 미만이 될 때까지 다시 활성화되지 않습니다.

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 ExpressRoute 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭을 업데이트하려면
공급자에 대해 지원되는 대역폭 옵션은 [ExpressRoute FAQ](expressroute-faqs.md)를 확인하세요. 기존 회로의 크기보다 큰 모든 크기를 선택할 수 있습니다.

> [!IMPORTANT]
> 기존 포트에 적절한 용량이 없는 경우 ExpressRoute 회로를 다시 만들어야 할 수 있습니다. 해당 위치에서 사용 가능한 추가 용량이 없는 경우 해당 회로를 업그레이드할 수 없습니다.
>
> 그러나 중단 없이 ExpressRoute 회로의 대역폭을 줄일 수는 없습니다. 대역폭을 다운그레이드하려면 ExpressRoute 회로의 프로비전을 해제하고 새 ExpressRoute 회로를 다시 프로비전해야 합니다.
> 

필요한 크기를 선택하면, 다음 명령을 사용하여 회로 크기를 조정합니다.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


회로의 크기는 Microsoft 쪽에서 조정됩니다. 그런 다음 변경 사항에 맞게 구성을 업데이트하려면 해당 공급자에게 연락해야 합니다. 이 알림을 통보하고 나면, 업데이트된 대역폭 옵션에 대한 요금이 청구되기 시작합니다.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU를 요금제에서 무제한으로 이동하려면
다음 PowerShell 코드 조각을 사용하여 ExpressRoute 회로의 SKU를 변경할 수 있습니다.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>클래식 및 리소스 관리자 환경에 대한 액세스를 제어하려면
[클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](expressroute-howto-move-arm.md)의 지침을 검토합니다.  

## <a name="delete"></a>ExpressRoute 회로 프로비전 해제 및 삭제
다음 정보에 유의하세요.

* 모든 가상 네트워크를 ExpressRoute 회로에서 연결 해제해야 합니다. 이 작업에 실패한 경우 회로에 연결된 가상 네트워크가 있는지 확인하세요.
* ExpressRoute 회로 서비스 공급자 프로비전 상태가 **프로비전 중** 또는 **프로비전됨**인 경우에는 서비스 공급자에게 회로 프로비전 해제를 요청해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로 프로비전을 해제하여 서비스 공급자 프로비전 상태가 **프로비전되지 않음**이 되면 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

다음 명령을 실행하여 ExpressRoute 회로를 삭제할 수 있습니다.

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>다음 단계

회로를 만든 후에 다음 단계를 수행해야 합니다.

* [ExpressRoute 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-arm.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
