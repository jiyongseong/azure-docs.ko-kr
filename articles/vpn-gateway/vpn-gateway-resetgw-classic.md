---
title: "Azure VPN Gateway를 초기화하여 IPsec 터널 다시 설정 | Microsoft Docs"
description: "이 문서에서는 Azure VPN Gateway를 초기화하여 IPsec 터널을 설정하는 과정을 안내합니다. 이 문서는 클래식 및 Resource Manager 배포 모델의 VPN Gateway에 모두 적용됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway 다시 설정

Azure VPN Gateway 재설정은 하나 이상의 사이트 간 VPN 터널에서 크로스-프레미스 VPN 연결이 손실되는 경우에 유용합니다. 이 상황에서 온-프레미스 VPN 장치는 모두 올바르게 작동하지만 Azure VPN 게이트웨이와 IPsec 터널을 설정할 수 없습니다. 이 문서는 VPN Gateway를 다시 설정하도록 도와줍니다.

### <a name="what-happens-during-a-reset"></a>다시 설정하는 동안 어떻게 되나요?

VPN Gateway는 활성-대기 구성에서 실행 중인 두 VM 인스턴스로 구성됩니다. 게이트웨이를 다시 설정한 경우 게이트웨이가 다시 부팅된 후 크로스-프레미스 구성이 다시 적용됩니다. 게이트웨이는 기존의 공용 IP 주소를 유지합니다. 즉, Azure VPN 게이트웨이에 대한 새 공용 IP 주소로 VPN 라우터 구성을 업데이트할 필요가 없습니다.

게이트웨이를 다시 설정하는 명령을 실행하면 Azure VPN Gateway의 현재 활성 인스턴스가 즉시 재부팅됩니다. 활성 인스턴스(재부팅)에서 대기 인스턴스로 장애 조치(Failover) 중에 잠깐의 간격이 있습니다. 이 간격은 1분 미만이어야 합니다.

첫 번째 재부팅 후 연결이 복원되지 않으면 같은 명령을 다시 실행하여 두 번째 VM인스턴스(새 활성 게이트웨이)를 재부팅합니다. 두 번의 재부팅이 연속으로 요청되는 경우 두 VM 인스턴스(활성 및 대기)가 재부팅되는 기간이 약간 길어집니다. 이로 인해 VM에서 재부팅을 완료하는 데 VPN 연결에 최대 2~4분까지 긴 간격이 발생합니다.

두 번의 재부팅 후 크로스-프레미스 연결 문제가 여전히 발생하는 경우 Azure Portal에서 지원 요청을 여세요.

## <a name="before"></a>시작하기 전에

게이트웨이를 재설정하기 전에 각 IPsec 사이트 간(S2S) VPN 터널에 대해 아래 나열된 키 항목을 확인합니다. 일치하지 않는 항목은 S2S VPN 터널에서 연결이 끊깁니다. 온-프레미스 및 Azure VPN Gateway에 대한 구성을 확인 및 수정하면 게이트웨이에서 작동 중인 기타 연결에 대해 불필요한 재부팅 및 중단을 피할 수 있습니다.

게이트웨이를 재설정하기 전에 다음 항목을 확인합니다.

* Azure VPN 게이트웨이 및 온-프레미스 VPN 게이트웨이 모두에 대한 인터넷 IP 주소(VIP)가 Azure 및 온-프레미스 VPN 정책에서 바르게 구성되어 있습니다.
* 미리 공유한 키가 Azure와 온-프레미스 VPN 게이트웨이에서 동일해야 합니다.
* 암호화, 해시 알고리즘 및 전달 완전 보안(PFS)과 같은 특정 IPsec/IKE 구성을 적용하는 경우 Azure와 온-프레미스 VPN 게이트웨이에서 동일한 구성을 포함하는지 확인하세요.

## <a name="portal"></a>Azure Portal

Azure Portal을 사용하여 Resource Manager VPN Gateway를 다시 설정할 수 있습니다. 클래식 게이트웨이를 다시 설정하려는 경우 [PowerShell](#resetclassic) 단계를 참조하세요.

### <a name="resource-manager-deployment-model"></a>리소스 관리자 배포 모델

1. [Azure Portal](https://portal.azure.com)을 열고 다시 설정하려는 Resource Manager 가상 네트워크 게이트웨이로 이동합니다.
2. 가상 네트워크 게이트웨이에 대한 블레이드에서 '다시 설정'을 클릭합니다.

  ![VPN Gateway 블레이드 다시 설정](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. 다시 설정 블레이드에서 **다시 설정** 단추를 클릭합니다.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>리소스 관리자 배포 모델

게이트웨이를 다시 설정하는 cmdlet은 **Reset-AzureRmVirtualNetworkGateway**입니다. 다시 설정을 수행하기 전에 최신 버전의 [Resource Manager PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)이 있는지 확인합니다. 다음 예제에서는 TestRG1 리소스 그룹에서 VNet1GW라는 가상 네트워크 게이트웨이를 다시 설정합니다.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

결과:

반환 결과를 받으면 게이트웨이 재설정에 성공한 것으로 가정할 수 있습니다. 그러나 반환 결과에는 재설정에 성공했음을 명시적으로 나타내는 항목이 없습니다. 기록을 면밀히 확인하여 게이트웨이 재설정이 발생한 시점을 정확히 알아보려면 [Azure Portal](https://portal.azure.com)에서 해당 정보를 보면 됩니다. 포털에서 **'GatewayName' -> Resource Health**로 이동합니다.

### <a name="resetclassic"></a>클래식 배포 모델

게이트웨이를 다시 설정하는 cmdlet은 **Reset-AzureVNetGateway**입니다. 다시 설정을 수행하기 전에 최신 버전의 [SM(Service Management) PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0)이 있는지 확인합니다. 다음 예제에서는 "ContosoVNet"이라는 가상 네트워크에 대한 게이트웨이를 다시 설정합니다.

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

결과:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

게이트웨이를 다시 설정하려면 [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) 명령을 사용합니다. 다음 예제에서는 TestRG5 리소스 그룹에서 VNet5GW라는 가상 네트워크 게이트웨이를 다시 설정합니다.

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

결과:

반환 결과를 받으면 게이트웨이 재설정에 성공한 것으로 가정할 수 있습니다. 그러나 반환 결과에는 재설정에 성공했음을 명시적으로 나타내는 항목이 없습니다. 기록을 면밀히 확인하여 게이트웨이 재설정이 발생한 시점을 정확히 알아보려면 [Azure Portal](https://portal.azure.com)에서 해당 정보를 보면 됩니다. 포털에서 **'GatewayName' -> Resource Health**로 이동합니다.