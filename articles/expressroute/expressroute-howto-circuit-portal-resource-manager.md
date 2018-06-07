---
title: "ExpressRoute 회로 만들기 및 수정: Azure Portal | Microsoft Docs"
description: "이 문서에서는 ExpressRoute 회로를 만들고, 프로비전하고, 확인하고, 업데이트하고, 삭제하고, 프로비전을 해제하는 방법을 설명합니다."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: be97f441d6eb2e97d85d598b0ed66718aec97b14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-modify-an-expressroute-circuit"></a>ExpressRoute 회로 만들기 및 수정
> [!div class="op_single_selector"]
> * [Azure 포털](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [비디오 - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-circuit-classic.md)
>

이 문서에서는 Azure Portal 및 Azure Resource Manager 배포 모델을 사용하여 Azure ExpressRoute 회로를 만드는 방법을 설명합니다. 아래 단계에서는 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 보여 줍니다.


## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.
* [Azure 포털](https://portal.azure.com)에 대한 액세스 권한이 있는지 확인합니다.
* 새 네트워킹 리소스를 만들 권한이 있는지 확인합니다. 적절한 권한이 없는 경우에는 계정 관리자에게 문의합니다.
* 단계를 더 잘 이해하기 위해 시작 전에 [비디오 보기](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)를 할 수 있습니다.

## <a name="create"></a>ExpressRoute 회로 만들기 및 프로비전
### <a name="1-sign-in-to-the-azure-portal"></a>1. Azure 포털에 로그인합니다.
브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

### <a name="2-create-a-new-expressroute-circuit"></a>2. 새 ExpressRoute 회로 만들기
> [!IMPORTANT]
> ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. 연결 공급자가 회로를 프로비전할 준비가 된 후에 이 작업을 수행하도록 하십시오.
> 
> 

1. 새 리소스를 만드는 옵션을 선택하여 ExpressRoute 회로를 만들 수 있습니다. 아래 이미지에 표시된 대로, **리소스 만들기** > **네트워킹** > **ExpressRoute**를 클릭합니다.

  ![ExpressRoute 회로 만들기](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. **ExpressRoute**를 클릭하면 **ExpressRoute 회로 만들기** 페이지가 표시됩니다. 이 페이지의 값에 데이터를 입력할 때 올바른 SKU 계층(Standard 또는 Premium) 및 데이터 계량 청구 모델(제한 없음 또는 요금제)을 지정해야 합니다.

  ![SKU 계층 및 데이터 요금제 구성](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **계층** 은 ExpressRoute 표준 또는 ExpressRoute Premium 추가 기능이 사용되는지 여부를 결정합니다. **표준**을 지정하여 표준 SKU를 가져오거나 프리미엄 추가 기능을 위해 **프리미엄**을 지정할 수 있습니다.
  * **데이터 요금제** 는 청구서 유형을 결정합니다. 데이터 요금제의 경우 **Metered**를 선택하고 무제한 데이터 요금제의 경우 **Unlimited**를 선택할 수 있습니다. 청구서 유형을 **Metered**에서 **Unlimited**로 변경할 수 있지만, **Unlimited**에서 **Metered**로는 변경할 수 없습니다.
  * **피어링 위치**는 Microsoft와 피어링하는 물리적 위치입니다.

    > [!IMPORTANT]
    > 피어링 위치는 Microsoft와 피어링하는 [물리적 위치](expressroute-locations.md)를 나타냅니다. 이 위치는 Azure Network Resource Provider가 있는 지리적 위치를 참조하는 "Location" 속성에 **연결되지 않습니다** . 이 속성에 연결되지 않는 대신 회로의 피어링 위치와 지리적으로 가까운 네트워크 리소스 공급자를 선택 하는 것이 좋습니다.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. 회로 및 속성 보기
**모든 회로 보기**

왼쪽 메뉴에서 **모든 리소스** 를 선택하면 만들어 놓은 모든 회로를 볼 수 있습니다.

![회로 보기](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**속성 보기**

회로를 선택하여 회로의 속성을 볼 수 있습니다. 회로의 **개요** 페이지에서 서비스 키가 서비스 키 필드에 표시됩니다. 회로의 서비스 키를 복사하고 서비스 공급자로 전달하여 프로비전 프로세스를 완료해야 합니다. 회로 서비스 키는 회로에 지정됩니다.

![보기 속성](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. 프로비전을 위해 연결 공급자에 서비스 키 보내기
이 페이지에서 **공급자 상태**는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공합니다. **Circuit status** (회로 상태)는 Microsoft 측의 상태를 제공합니다. 회로 프로비전 상태에 대한 자세한 내용은 [워크플로](expressroute-workflows.md#expressroute-circuit-provisioning-states) 문서를 참조하세요.

새 ExpressRoute 회로를 만들면 회로는 다음 상태가 됩니다.

공급자 상태: 프로비전되지 않음<BR>
회로 상태: 활성화됨

![프로비전 프로세스 시작](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

연결 공급자가 사용자에 대해 활성화를 처리 중이면 회로가 다음 상태로 변경됩니다.

공급자 상태: 프로비전 중<BR>
회로 상태: 활성화됨

ExpressRoute 회로를 사용하려면 다음 상태여야 합니다.

공급자 상태: 프로비전됨<BR>
회로 상태: 활성화됨

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. 회로 키의 상태를 주기적으로 확인
원하는 회로를 선택하여 회로의 속성을 볼 수 있습니다. **공급자 상태**를 확인하고 계속하기 전에 **프로비전됨**으로 이동했는지 확인합니다.

![회로 및 공급자 상태](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. 라우팅 구성 만들기
단계별 지침은 [ExpressRoute 회로 라우팅 구성(회로 피어링 만들기 및 수정)](expressroute-howto-routing-portal-resource-manager.md) 문서를 참조하세요.

> [!IMPORTANT]
> 이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IP VPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. 가상 네트워크를 ExpressRoute 회로에 연결합니다.
그 다음 가상 네트워크를 ExpressRoute 회로에 연결합니다. Resource Manager 배포 모델을 작업하는 경우에는 [ExpressRoute 회로에 가상 네트워크 연결](expressroute-howto-linkvnet-arm.md) 문서를 사용할 수 있습니다.

## <a name="status"></a>ExpressRoute 회로의 상태 가져오기
회로를 선택하고 개요 페이지로 이동하여 회로의 상태를 볼 수 있습니다. 

## <a name="modify"></a>ExpressRoute 회로 수정
연결에 미치는 영향 없이 ExpressRoute 회로의 특정 속성을 수정할 수 있습니다. **구성** 페이지에서 대역폭, SKU, 요금 청구 모델을 수정하고 기존 작업을 허용할 수 있습니다. 제한 및 제한 사항에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요. 

가동 중지 시간 없이 다음 작업을 수행할 수 있습니다.

* ExpressRoute 회로에 대해 ExpressRoute 프리미엄 추가 기능을 사용하거나 사용하지 않을 수 있습니다.
* 포트에 사용 가능한 용량이 있는 경우 ExpressRoute 회로의 대역폭을 증가시킵니다. 회로 대역폭 다운그레이드는 지원되지 않습니다. 
* 요금제를 *데이터 요금*에서 *무제한 데이터 요금*으로 변경합니다. 요금제를 무제한 데이터 요금에서 데이터 요금으로 변경하는 것은 지원되지 않습니다.
* *Allow Classic Operations*을 활성화하거나 비활성화할 수 있습니다.

> [!IMPORTANT]
> 기존 포트에 적절한 용량이 없는 경우 ExpressRoute 회로를 다시 만들어야 할 수 있습니다. 해당 위치에서 사용 가능한 추가 용량이 없는 경우 해당 회로를 업그레이드할 수 없습니다.
>
> 대역폭을 원활하게 업그레이드할 수 있지만 중단 없이 ExpressRoute 회로의 대역폭을 줄일 수는 없습니다. 대역폭을 다운그레이드하려면 ExpressRoute 회로의 프로비전을 해제하고 새 ExpressRoute 회로를 다시 프로비전해야 합니다.
> 
> 표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 프리미엄 추가 기능 작업을 사용하지 않도록 설정할 수 없습니다.
> 
> 

ExpressRoute 회로를 수정하려면 **구성**을 클릭합니다.

![회로 수정](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>ExpressRoute 회로 프로비전 해제 및 삭제
**삭제** 아이콘을 선택하여 ExpressRoute 회로를 삭제할 수 있습니다. 다음 정보에 유의하세요.

* 모든 가상 네트워크를 ExpressRoute 회로에서 연결 해제해야 합니다. 이 작업에 실패한 경우 회로에 연결된 가상 네트워크가 있는지 확인하세요.
* ExpressRoute 회로 서비스 공급자 프로비전 상태가 **프로비전 중** 또는 **프로비전됨**인 경우에는 서비스 공급자에게 회로 프로비전 해제를 요청해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로 프로비전을 해제하여 서비스 공급자 프로비전 상태가 **프로비전되지 않음**이 되면 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

## <a name="next-steps"></a>다음 단계
회로를 만든 후에 다음 단계를 진행합니다.

* [ExpressRoute 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
