---
title: "ExpressRoute로 회로를 구성하기 위한 워크플로 | Microsoft 문서"
description: "이 페이지는 Express 경로 회로 및 피어링을 구성하기 위한 워크플로를 안내합니다."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>회로에 대한 Express 경로 워크플로 프로비전 및 회로 상태
이 페이지에서는 높은 수준에서 구성 워크플로 프로비전 및 라우팅 서비스를 안내합니다.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

다음 그림 및 해당 단계는 종단 간 프로비전된 Express 경로 회로를 보유하기 위해 수행해야 하는 작업을 표시합니다. 

1. PowerShell을 사용하여 Express 경로 회로를 구성합니다. 자세한 세부 사항은 [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md) 문서의 지침을 수행합니다.
2. 서비스 공급자에서 연결을 정렬합니다. 이 프로세스는 다양합니다. 연결을 정렬하는 방법에 대한 자세한 내용은 연결 공급자에게 문의합니다.
3. PowerShell 통해 상태를 프로비전하는 Express 경로 회로를 확인하여 회로를 성공적으로 프로비전했는지 확인합니다. 
4. 라우팅 도메인을 구성합니다. 연결 공급자가 사용자를 위해 3계층을 관리하는 경우 회로에 라우팅을 구성합니다. 연결 공급자가 2계층 서비스만을 제공하는 경우 [라우팅 요구 사항](expressroute-routing.md) 및 [라우팅 구성](expressroute-howto-routing-classic.md) 페이지에 설명된 지침에 따라 라우팅을 구성해야 합니다.
   
   * Azure 개인 피어링 사용 - 이 피어링을 사용하여 가상 네트워크 내에 배포된 VM/클라우드 서비스에 연결해야 합니다.
   * Azure 공용 피어링 사용 - 공용 IP 주소에 호스팅된 Azure 서비스에 연결하려는 경우 Azure 공용 피어링을 사용해야 합니다. Azure 개인 피어링에 대한 기본 라우팅을 사용하도록 선택한 경우 Azure 리소스에 액세스하는 것이 요구됩니다.
   * Microsoft 피어링 사용 - Office 365 및 Dynamics 365에 액세스하려면 이 기능을 사용해야 합니다. 
     
     > [!IMPORTANT]
     > 인터넷에 사용하는 것 이외에 별도 프록시/Edge를 사용하여 Microsoft에 연결해야 합니다. Express 경로 및 인터넷 모두에 동일한 Edge를 사용하면 비대칭 라우팅이 발생하고 네트워크에 대한 연결 중단이 발생합니다.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. 가상 네트워크를 Express 경로 회로에 연결 - 가상 네트워크를 Express 경로 회로에 연결할 수 있습니다. 지침을 수행하여 회로에 [VNet을 연결](expressroute-howto-linkvnet-arm.md) 합니다. 이러한 VNet은 Express 경로 회로로써 동일한 Azure 구독에 있거나 다른 구독에 있을 수 있습니다.

## <a name="expressroute-circuit-provisioning-states"></a>Express 경로 회로 상태 프로비전
각 Express 경로 회로에는 두 가지 상태가 있습니다.

* 서비스 공급자 프로비전 상태
* 가동 상태

상태는 Microsoft의 프로비전 상태를 나타냅니다. Express 경로 회로를 만들 때는 이 속성이 Enabled로 설정됩니다.

연결 공급자 프로비전 상태는 연결 공급자 측의 상태를 나타냅니다. *프로비전되지 않음*, *프로비전 중* 또는 *프로비전됨*일 수 있습니다. Express 경로 회로는 사용할 수 있도록 프로비전됨 상태에 있어야 합니다.

### <a name="possible-states-of-an-expressroute-circuit"></a>Express 경로 회로의 가능한 상태
이 섹션은 Express 경로 회로에 대해 가능한 상태를 나열합니다.

**만드는 경우**

PowerShell cmdlet을 실행하여 Express 경로 회로를 만드는 즉시 다음 상태의 Express 경로 회로가 표시됩니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**연결 공급자에서 회로를 프로비전하고 있는 경우**

연결 공급자에 서비스 키를 전달하고 프로비전 프로세스를 시작하는 즉시 다음 상태의 Express 경로 회로가 표시됩니다.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**연결 공급자에서 프로비전 프로세스를 완료한 경우**

연결 공급자가 프로비전 프로세스를 완료하는 즉시 다음 상태의 Express 경로 회로가 표시됩니다.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

프로비전됨 및 사용은 회로를 사용할 수 있는 유일한 상태입니다. 2계층 공급자를 사용하는 경우 이 상태인 경우 회로에 라우팅을 구성할 수 있습니다.

**연결 공급자에서 회로 프로비전을 해제하는 경우**

서비스 공급자에게 Express 경로 회로 프로비전 해제를 요청한 경우 서비스 공급자가 프로비전 해제 프로세스를 완료하고 나면 회로가 다음 상태로 설정됩니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


필요에 따라 PowerShell cmdlet을 다시 사용하거나 실행하여 회로를 삭제할 수 있습니다.  

> [!IMPORTANT]
> ServiceProviderProvisioningState가 Provisioning 또는 Provisioned일 때 PowerShell cmdlet을 실행하여 회로를 삭제하면 작업이 실패합니다. 이러한 경우에는 연결 공급자에게 요청하여 먼저 Express 경로 회로 프로비전을 해제한 후에 회로를 삭제하세요. Microsoft는 PowerShell cmdlet을 실행하여 회로를 삭제할 때까지 회로에 요금을 계속 청구합니다.
> 
> 

## <a name="routing-session-configuration-state"></a>라우팅 세션 구성 상태
BGP 프로비전 상태를 사용하면 Microsoft Edge에서 BGP 세션을 사용하는지를 알 수 있습니다. 상태는 피어링을 사용할 수 있도록 활성화되어야 합니다.

특히 Microsoft 피어링의 경우 BGP 세션 상태를 확인하는 것이 중요합니다. BGP 프로비전 상태 외에도 *보급된 공용 접두사 상태*를 호출하는 다른 상태가 있습니다. BGP 세션이 가동되고 라우팅이 종단 간에 작동하기 위해 보급된 공용 접두사 상태는 *구성된* 상태에 있어야 합니다. 

보급된 공용 접두사 상태가 *유효성 검사가 필요한* 상태로 설정된 경우 보급된 접두사가 라우팅 레지스트리의의 AS 번호와 일치하지 않기 때문에 BGP 세션을 사용하지 않습니다. 

> [!IMPORTANT]
> 보급된 공용 접두사 상태가 *수동 유효성 검사* 상태인 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 열고 사용자가 연관된 자치 시스템 번호와 함께 보급된 IP 주소를 소유한 증거를 제공해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* Express 경로 연결을 구성합니다.
  
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)

