---
title: 'Azure ExpressRoute Microsoft 피어링에 대한 경로 필터 구성: PowerShell | Microsoft Docs'
description: 이 문서에서는 PowerShell을 사용하여 Microsoft 피어링에 대한 경로 필터를 구성하는 방법을 설명합니다.
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: 6e767166ecf248aa0e7fc16dc21361394e03107d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Microsoft 피어링에 대한 경로 필터 구성: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

경로 필터는 Microsoft 피어링을 통해 지원되는 서비스의 하위 집합을 사용하는 방법입니다. 이 문서의 단계는 ExpressRoute 회로에 대한 경로 필터를 구성하고 관리하는 데 도움이 됩니다.

Dynamics 365 서비스, Office 365 서비스(예: Exchange Online, SharePoint Online, 비즈니스용 Skype) 및 Azure 공용 서비스(예: 저장소 및 SQL DB)는 Microsoft 피어링을 통해 액세스할 수 있습니다. Azure 공용 서비스는 지역 단위로 선택할 수 있으며, 공용 서비스별로 정의되지 않습니다. 

Microsoft 피어링이 ExpressRoute 회로에 구성되고 경로 필터가 연결되면, 이러한 서비스에 대해 선택된 모든 접두사는 설정된 BGP 세션을 통해 보급됩니다. BGP 커뮤니티 값은 접두사를 통해 제공되는 서비스를 식별하는 모든 접두사에 연결됩니다. BGP 커뮤니티 값과 매핑되는 서비스의 목록은 [BGP 커뮤니티](expressroute-routing.md#bgp)를 참조하세요.

모든 서비스에 연결해야 하는 경우 많은 수의 접두사가 BGP를 통해 보급됩니다. 그러면 네트워크 내의 라우터에서 유지 관리되는 경로 테이블의 크기가 상당히 증가합니다. Microsoft 피어링을 통해 제공되는 서비스의 하위 집합만 사용하려는 경우 두 가지 방법으로 경로 테이블의 크기를 줄일 수 있습니다. 다음을 수행할 수 있습니다.

- BGP 커뮤니티에 라우팅 필터를 적용하여 필요 없는 접두사를 필터링합니다. 표준 네트워킹 방법은 많은 네트워크 내에서 일반적으로 사용됩니다.

- 경로 필터를 정의하고 ExpressRoute 회로에 적용합니다. 경로 필터는 Microsoft 피어링을 통해 사용하려는 서비스 목록을 선택할 수 있는 새 리소스입니다. ExpressRoute 라우터는 경로 필터에서 식별된 서비스에 속하는 접두사 목록만을 보냅니다.

### <a name="about"></a>경로 필터 정보

Microsoft 피어링이 ExpressRoute 회로에 구성되면 Microsoft 에지 라우터는 에지 라우터(사용자 또는 연결 공급자 소유)를 사용하여 한 쌍의 BGP 세션을 설정합니다. 경로는 네트워크에 보급되지 않습니다. 네트워크에 경로 보급을 사용하려면 경로 필터를 연결해야 합니다.

경로 필터를 사용하면 ExpressRoute 회로의 Microsoft 피어링을 통해 사용하려는 서비스를 식별할 수 있습니다. 특히 모든 BGP 커뮤니티 값의 허용 목록입니다 경로 필터 리소스가 정의되고 ExpressRoute 회로에 연결되면 BGP 커뮤니티 값에 매핑되는 모든 접두사는 네트워크에 보급됩니다.

경로 필터를 Office 365 서비스에 연결할 수 있으려면 ExpressRoute를 통해 Office 365 서비스를 사용할 수 있는 권한이 부여되어야 합니다. ExpressRoute를 통해 Office 365 서비스를 사용할 수 있는 권한이 없는 경우 경로 필터를 연결하는 작업에 실패합니다. 권한 부여 프로세스에 대한 자세한 내용은 [Office 365용 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)를 참조하세요. Dynamics 365 서비스에 대한 연결에는 사전 권한 부여가 필요하지 않습니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다.
> 
> 

### <a name="workflow"></a>워크플로

Microsoft 피어링을 통해 서비스에 성공적으로 연결할 수 있으려면 다음 구성 단계를 완료해야 합니다.

- Microsoft 피어링을 프로비전하는 활성 ExpressRoute 회로가 있어야 합니다. 다음 지침을 사용하여 이러한 작업을 수행할 수 있습니다.
  - [ExpressRoute 회로를 만들고](expressroute-howto-circuit-arm.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 프로비전되고 활성화된 상태여야 합니다.
  - 직접 BGP 세션을 관리하는 경우 [Microsoft 피어링을 만듭니다](expressroute-circuit-peerings.md). 또는 연결 공급자가 회로에 Microsoft 피어링을 프로비전하도록 합니다.

-  경로 필터를 만들고 구성해야 합니다.
    - Microsoft 피어링을 통해 사용하려는 서비스를 식별합니다.
    - 서비스와 연결된 BGP 커뮤니티 값의 목록을 식별합니다
    - BGP 커뮤니티 값과 일치하는 접두사 목록을 허용하는 규칙을 만듭니다.

-  경로 필터를 ExpressRoute 회로에 연결해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 조건을 충족하는지 확인합니다.

 - 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps)을 참조하세요.

  > [!NOTE]
  > 설치 관리자를 사용하는 대신 PowerShell 갤러리에서 최신 버전을 다운로드합니다. 현재 설치 관리자는 필요한 cmdlet을 지원하지 않습니다.
  > 

 - 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

 - 활성화된 ExpressRoute 회로가 있어야 합니다. 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-arm.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 프로비전되고 활성화된 상태여야 합니다.

 - 활성 Microsoft 피어링이 있어야 합니다. [피어링 구성 수정 및 만들기](expressroute-circuit-peerings.md)의 지침에 따릅니다.

### <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인합니다.

이 구성을 시작하기 전에 Azure 계정에 로그인해야 합니다. Cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```powershell
Connect-AzureRmAccount
```

Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

사용할 구독을 지정합니다.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>1단계: 접두사 및 BGP 커뮤니티 값의 목록 가져오기

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP 커뮤니티 값의 목록 가져오기

다음 cmdlet을 사용하여 Microsoft 피어링을 통해 액세스할 수 있는 서비스와 관련된 BGP 커뮤니티 값의 목록 및 관련된 접두사 목록을 가져옵니다.

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. 사용하려는 값의 목록 확인

경로 필터에 사용하려는 BGP 커뮤니티 값 목록을 만듭니다. 예를 들어, Dynamics 365 서비스의 BGP 커뮤니티 값은 12076:5040입니다.

## <a name="filter"></a>2단계: 경로 필터 및 필터 규칙 만들기

경로 필터에는 하나의 규칙만이 있을 수 있고 규칙은 '허용' 형식이어야 합니다. 이 규칙에는 관련된 BGP 커뮤니티 값의 목록이 있을 수 있습니다

### <a name="1-create-a-route-filter"></a>1. 경로 필터 만들기

먼저, 경로 필터를 만듭니다. 'New-AzureRmRouteFilter' 명령은 경로 필터 리소스만을 만듭니다. 리소스를 만든 후에 규칙을 만들고 경로 필터 개체에 연결해야 합니다. 다음 명령을 실행하여 경로 필터 리소스를 만듭니다.

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. 필터 규칙 만들기

이 예제에 나와 있는 대로 BGP 커뮤니티의 집합을 쉼표로 구분된 목록으로 지정할 수 있습니다. 다음 명령을 실행하여 새 규칙을 만듭니다.
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. 경로 필터에 규칙 추가

다음 명령을 실행하여 필터 규칙을 경로 필터에 추가합니다.
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>3단계: 경로 필터를 ExpressRoute 회로에 연결

Microsoft 피어링만 있다고 가정하고 다음 명령을 실행하여 경로 필터를 ExpressRoute 회로에 연결합니다.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>일반 작업

### <a name="getproperties"></a>경로 필터의 속성을 가져오려면

경로 필터의 속성을 가져오려면 다음 단계를 사용합니다.

1. 다음 명령을 실행하여 경로 필터 리소스를 가져옵니다.

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. 다음 명령을 실행하여 경로 필터 리소스에 대한 경로 필터 규칙을 가져옵니다.

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>경로 필터의 속성을 업데이트하려면

경로 필터가 이미 회로에 연결된 경우 BGP 커뮤니티 목록에 대한 업데이트로 인해 설정된 BGP 세션을 통해 적절한 접두사 보급 변경 내용을 자동으로 전파합니다. 다음 명령을 사용하여 경로 필터의 BGP 커뮤니티 목록을 업데이트할 수 있습니다.

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>ExpressRoute 회로에서 경로 필터를 분리하려면

경로 필터를 ExpressRoute 회로에서 분리하면 접두사가 BGP 세션을 통해 보급되지 않습니다. 다음 명령을 사용하여 ExpressRoute 회로에서 경로 필터를 분리할 수 있습니다.
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>경로 필터를 삭제하려면

회로에 연결되지 않은 경우에만 필터를 삭제할 수 있습니다. 경로 필터를 삭제하기 전에 회로에 연결되지 않았는지 확인합니다. 다음 명령을 사용하여 경로 필터를 삭제할 수 있습니다.

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
