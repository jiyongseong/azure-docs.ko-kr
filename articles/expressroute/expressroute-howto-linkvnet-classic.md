---
title: 'ExpressRoute 회로에 가상 네트워크 연결: PowerShell: 클래식: Azure | Microsoft Docs'
description: 이 문서는 클래식 배포 모델 및 PowerShell을 사용하여 VNet(가상 네트워크)을 ExpressRoute 회로에 연결하는 방법에 대한 개요를 제공합니다.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 2f0fed77a676bc46e8daa9c41efd533dffe72d8b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>PowerShell을 사용하여 ExpressRoute 회로에 가상 네트워크 연결(클래식)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-linkvnet-classic.md)
>

이 문서를 참조하면 클래식 배포 모델 및 PowerShell을 사용하여 VNet(가상 네트워크)을 Azure ExpressRoute 회로에 연결할 수 있습니다. 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>필수 구성 요소
1. Azure PowerShell 모듈의 최신 버전이 필요합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)의 PowerShell 섹션에서 최신 PowerShell 모듈을 다운로드할 수 있습니다. Azure PowerShell 모듈을 사용하도록 컴퓨터를 구성하는 방법에 대한 단계별 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 의 지침을 따르세요.
2. 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토해야 합니다.
3. 활성화된 ExpressRoute 회로가 있어야 합니다.
   * 지침에 따라 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-classic.md) 연결 공급자가 회로를 사용하도록 설정합니다.
   * 회로에 구성된 Azure 개인 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-classic.md)을 참조하세요.
   * Azure 개인 피어링이 구성되어 있고 네트워크와 Microsoft 간의 BGP 피어링이 종단 간 연결을 사용하도록 작동 중이어야 합니다.
   * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들고 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크를 구성합니다](expressroute-howto-vnet-portal-classic.md).

최대 10개의 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 모든 가상 네트워크는 같은 지정학적 지역에 있어야 합니다. 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로에 많은 수의 가상 네트워크를 연결하거나 다른 지역에 있는 가상 네트워크를 연결할 수 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

단일 VNet을 최대 4개의 ExpressRoute 회로에 연결할 수 있습니다. 아래 프로세스를 사용하여 각 ExpressRoute 회로에 대한 새 링크를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>동일한 구독에 있는 가상 네트워크를 회로에 연결
다음 cmdlet을 사용하여 ExpressRoute 회로에 가상 네트워크를 연결할 수 있습니다. cmdlet을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>다른 구독에 있는 가상 네트워크를 회로에 연결
여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 부서는 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크로 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 ExpressRoute 회로를 소유할 수 있습니다. 조직 내의 기타 구독도 ExpressRoute 회로를 사용할 수 있습니다.

> [!NOTE]
> 전용 회로에 대한 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
> 
> 

![구독 간 연결](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>관리
*회로 소유자*는 ExpressRoute 회로를 만드는 구독의 관리자/공동 관리자입니다. 회로 소유자는 자신이 소유한 전용 회로를 *회로 사용자*라고 지칭되는 다른 구독의 관리자/공동 관리자가 사용하도록 권한을 부여할 수 있습니다. 조직의 ExpressRoute 회로 사용 권한을 부여받은 회로 사용자는 권한이 부여된 후 구독의 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크가 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**권한 부여 만들기**

회로 소유자는 다른 구독 관리자에게 지정한 회로를 사용할 수 있는 권한을 부여합니다. 아래 예제에서 회로 관리자는(Contoso IT) 다른 구독의 관리자가(Dev-Test) 회로에 대해 최대 2개의 가상 네트워크를 연결하도록 설정합니다. Contoso IT 관리자는 Dev-Test Microsoft ID를 지정하여 이것을 설정합니다. 다음 cmdlet은 지정된 Microsoft ID로 전자 메일을 보내지 않습니다. 회로 소유자는 권한 부여가 완료된 사실을 다른 구독 소유자에게 명시적으로 알려야 합니다.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**권한 부여 검토**

회로 소유자는 다음 cmdlet을 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**권한 부여 업데이트**

회로 소유자는 다음 cmdlet을 사용하여 권한 부여를 수정할 수 있습니다.

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**권한 부여 삭제**

회로 소유자는 다음 cmdlet을 실행하여 권한 부여를 취소/삭제할 수 있습니다.

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>회로 사용자 작업

**권한 부여 검토**

회로 사용자는 다음 cmdlet을 사용하여 권한 부여를 검토할 수 있습니다.

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**링크 권한 부여 사용**

회로 사용자는 다음 cmdlet을 실행하여 링크 권한 부여를 사용할 수 있습니다.

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

가상 네트워크에 대해 새로 연결된 구독에서 다음 명령을 실행합니다.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>다음 단계
ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

