---
title: Azure 공용 IP 주소 만들기, 변경 또는 삭제 | Microsoft 문서
description: 공용 IP 주소를 만들거나 변경하거나 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c28d409bbdb7a4100f2bb9f00ff6f58a13855ea4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305058"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>공용 IP 주소 만들기, 변경 또는 삭제

공용 IP 주소 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소는 자체적으로 구성 가능한 설정을 사용하는 리소스입니다. 공용 IP 주소를 공용 IP 주소를 지원하는 Azure 리소스에 할당하면 다음을 수행할 수 있습니다.
- Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancer, Azure VPN Gateways 등 인터넷에서 리소스로 인바운드 통신. VM에 공용 IP 주소가 할당되어 있지 않은 경우라도 VM이 부하 분산 장치 백엔드 풀의 일부이고 부하 분산 장치에 공용 IP 주소가 할당되어 있는 한, 인터넷에서 VM 등의 일부 리소스와 계속 통신할 수 있습니다. 특정 Azure 서비스의 리소스에 공용 IP 주소를 할당할 수 있는지 또는 다른 Azure 리소스의 공용 IP 주소를 통해 통신할 수 있는지 여부를 확인하려면 서비스 설명서를 참조하세요. 
- 예측 가능한 IP 주소를 사용하는 인터넷으로의 아웃바운드 연결. 예를 들어 공용 IP 주소가 할당되지 않은 가상 머신도 인터넷으로의 아웃바운드 통신을 수행할 수는 있지만, 이 가상 머신의 주소는 기본적으로 Azure에서 예측할 수 없는 공용 주소로 변환한 네트워크 주소입니다. 공용 IP 주소를 리소스에 할당하면 아웃바운드 연결에 사용되는 IP 주소를 확인할 수 있습니다. 이 주소는 예측은 가능하지만 선택한 할당 방법에 따라 변경될 수 있습니다. 자세한 내용은 [공용 IP 주소 만들기](#create-a-public-ip-address)를 참조하세요. Azure 리소스의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

공용 IP 주소에는 명목 요금이 부과됩니다. 가격을 보려면 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

1. 포털의 왼쪽 위 모서리에서 **+ 리소스 만들기**를 선택합니다.
2. *마켓플레이스 검색* 상자에 *공용 IP 주소*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소**를 선택합니다.
3. **공용 IP 주소** 아래에서 **만들기**를 선택합니다.
4. **공용 IP 주소 만들기** 아래에서 다음 설정의 값을 입력하거나 선택한 다음, 만들기**를 선택합니다**:

    |설정|Required?|세부 정보|
    |---|---|---|
    |Name|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
    |SKU|예|SKU 도입 전에 생성된 모든 공용 IP 주소는 **기본** SKU 공용 IP 주소입니다.  공용 IP 주소를 만든 후에는 SKU를 변경할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 내의 가상 머신 또는 가상 머신 확장 집합은 기본 또는 표준 SKU를 사용할 수 있습니다.  가용성 집합 또는 확장 집합 내의 가상 머신 간에 SKU를 혼합해서 사용할 수 없습니다. **기본** SKU: 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *없음*으로 설정됩니다. 가용성 영역을 선택하여 공용 IP 주소에 대한 특정 영역을 보장할 수 있습니다. **표준** SKU: 표준 SKU 공용 IP를 가상 머신 또는 부하 분산 장치 프런트 엔드에 연결할 수 있습니다. 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *영역 중복*으로 설정됩니다. 가용성 영역에 대한 자세한 내용은 **가용성 영역** 설정을 참조하세요. 주소를 표준 부하 분산 장치에 연결하는 경우에 표준 SKU가 필요합니다. 표준 부하 분산 장치에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 가상 머신의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.|
    |IP 버전|예| IPv4 또는 IPv6을 선택합니다. 공용 IPv4 주소는 여러 Azure 리소스에 할당할 수 있는 반면 IPv6 공용 IP 주소는 인터넷 연결 부하 분산 장치에만 할당할 수 있습니다. 부하 분산 장치는 Azure 가상 머신으로 IPv6 트래픽을 부하 분산할 수 있습니다. 자세한 내용은 [가상 머신으로 IPv6 트래픽 부하 분산](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. **표준 SKU**를 선택한 경우 *IPv6*를 선택할 수 있는 옵션이 없습니다. **표준 SKU**를 사용하는 경우 IPv4 주소만 만들 수 있습니다.|
    |IP 주소 할당|예|**동적:** 동적 주소는 공용 IP 주소가 가상 머신에 연결된 네트워크 인터페이스에 연결되고 가상 머신을 처음 시작한 후에만 할당됩니다. 네트워크 인터페이스가 연결된 가상 머신을 중지(할당 취소)하면 동적 주소가 변경될 수 있습니다. 가상 컴퓨터를 다시 부팅하거나 중지하는 경우(하지만 할당 취소하지 않은 경우) 주소가 동일하게 유지됩니다. **고정:** 고정 IP 주소를 만들면 고정 주소가 할당됩니다. 가상 머신이 중지(할당 취소)된 상태에 있더라도 고정 주소는 변경되지 않습니다. 이 주소는 네트워크 인터페이스를 삭제할 때만 해제됩니다. 네트워크 인터페이스를 만든 후에 할당 방법을 변경할 수 있습니다. **IP 버전**으로 *IPv6*를 선택하는 경우 할당 방법은 *동적*입니다. **SKU**로 *표준*을 선택하는 경우 할당 방법은 *정적*입니다.|
    |유휴 제한 시간(분)|아니오|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. **IP 버전**으로 IPv6을 선택하는 경우에는 이 값을 변경할 수 없습니다. |
    |DNS 이름 레이블|아니오|이름을 만드는 Azure 위치 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 *location.cloudapp.azure.com*(여기서 location은 선택한 위치임)과 같은 기본 서브넷을 추가합니다. 두 주소 버전을 모두 만드는 경우 IPv4 및 IPv6 주소 둘 다에 같은 DNS 이름이 할당됩니다. Azure의 기본 DNS는 IPv4 A 및 IPv6 AAAA 이름 레코드를 모두 포함하며, DNS 이름을 조회할 때 응답으로 두 레코드를 모두 전송합니다. 클라이언트는 어떤 주소(IPv4 또는 IPv6)와 통신할지 선택합니다. 기본 접미사로 DNS 이름 레이블을 사용하는 것 대신 또는 그 외에 Azure DNS 서비스를 사용하여 공용 IP 주소로 확인하는 사용자 지정 접미사로 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure 공용 IP 주소로 Azure DNS 사용](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|
    |IPv6 주소 만들기 또는 IPv4 주소 만들기|아니오| **IP 버전**으로 선택한 항목에 따라 IPv6 또는 IPv4가 표시됩니다. 예를 들어 **IP 버전**으로 **IPv4**를 선택하면 여기에 **IPv6**이 표시됩니다. **SKU**로 *표준*을 선택하는 경우 IPv6 주소를 만들 수 있는 옵션이 없습니다.
    |이름(**IPv6 주소 만들기 또는 IPv4 주소 만들기** 확인란을 선택한 경우에만 표시됨)|예 - **IPv6 주소 만들기** 또는 IPv4 주소 만들기 확인란을 선택하는 경우|이름은 이 목록의 첫 번째 **이름**으로 입력하는 이름과 달라야 합니다. IPv4 주소와 IPv6 주소를 모두 만들도록 선택하면 Portal에서는 각 IP 주소 버전이 할당된 개별 공용 IP 주소 리소스 두 개를 만듭니다.|
    |IP 주소 할당(**IPv6 주소 만들기 또는 IPv4 주소 만들기** 확인란을 선택한 경우에만 표시됨)|예 - **IPv6 주소 만들기** 또는 IPv4 주소 만들기 확인란을 선택하는 경우|**IPv4 주소 만들기** 확인란이 표시되는 경우 할당 방법을 선택할 수 있습니다. **IPv6 주소 만들기** 확인란이 표시되는 경우에는 **동적** 할당 방법만 사용해야 하므로 할당 방법을 선택할 수 없습니다.|
    |구독|예|공용 IP 주소를 연결하려는 리소스와 동일한 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)에 있어야 합니다.|
    |리소스 그룹|예|공용 IP 주소를 연결하려는 리소스와 동일하거나 다른 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에 있을 수 있습니다.|
    |위치|예|공용 IP 주소를 연결하려는 리소스와 동일한 [위치](https://azure.microsoft.com/regions)(하위 지역이라고도 함)에 있어야 합니다.|
    |가용성 영역| 아니오 | 이 설정은 지원되는 위치를 선택하는 경우에만 나타납니다. 지원되는 위치 목록은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. **기본** SKU를 선택한 경우 *없음*이 자동으로 선택됩니다. 특정 영역을 보장하려는 경우 특정 영역을 선택할 수 있습니다. 두 선택 중 하나는 영역 중복이 아닙니다. **표준** SKU를 선택한 경우: 영역 중복이 자동으로 선택되며, 영역 오류 시 데이터 경로가 복원 가능합니다. 영역 오류 시 복원 불가능한 특정 영역을 보장하려는 경우 특정 영역을 선택할 수 있습니다.

**명령**

Portal에서는 IPv4와 IPv6용으로 하나씩 두 개의 공용 IP 주소 리소스를 만드는 옵션을 제공하지만, 다음 CLI 및 PowerShell 명령은 두 IP 버전 중 하나의 주소가 포함된 리소스 하나를 만듭니다. 각 IP 버전용으로 하나씩 두 개의 공용 IP 주소 리소스를 사용하려는 경우에는 공용 IP 주소 리소스에 대해 각기 다른 이름과 버전을 지정하여 명령을 두 번 실행해야 합니다.

|도구|명령|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>공용 IP 주소 보기, 설정 변경 또는 삭제

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소**를 선택합니다.
2. 보거나, 해당 설정을 변경하거나, 목록에서 삭제하려는 공용 IP 주소의 이름을 선택합니다.
3. 공용 IP 주소에 대해 수행하려는 작업(보기/삭제/변경)에 따라 다음 옵션 중 하나를 수행합니다.
    - **보기**: **개요** 섹션에는 공용 IP 주소가 연결된 네트워크 인터페이스(주소가 네트워크 인터페이스에 연결된 경우) 등 공용 IP 주소에 대한 주요 설정이 표시됩니다. Portal에는 주소(IPv4 또는 IPv6)의 버전이 표시되지 않습니다. 버전 정보를 보려면 PowerShell 또는 CLI를 사용하여 공용 IP 주소를 확인합니다. IP 주소 버전이 IPv6이면 할당된 주소가 Portal, PowerShell 또는 CLI 사용 시 표시되지 않습니다.
    - **삭제**: 공용 IP 주소를 삭제하려면 **개요** 섹션에서 **삭제**를 선택합니다. 주소가 현재 IP 구성과 연결되어 있으면 삭제할 수 없습니다. 주소가 현재 구성과 연결되어 있으면 **분리**를 선택하여 IP 구성에서 주소를 분리합니다.
    - **변경**: **구성**을 선택합니다. [공용 IP 주소 만들기](#create-a-public-ip-address)의 4단계 정보를 사용하여 설정을 변경합니다. IPv4 주소 할당 방식을 정적에서 동적으로 변경하려면 먼저 공용 IPv4 주소가 연결된 IP 구성에서 주소를 분리해야 합니다. 그런 다음, 할당 방법을 동적으로 변경하고 **연결**을 선택하여 동일한 IP 구성이나 다른 구성으로 해당 IP 주소를 연결하거나 분리할 수 있습니다. 공용 IP 주소를 분리하려면 **개요** 섹션에서 **분리**를 선택합니다.

    >[!WARNING]
    >할당 방법을 고정에서 동적으로 변경하면 공용 IP 주소에 할당된 IP 주소가 손실됩니다. Azure 공용 DNS 서버는 고정 또는 동적 주소와 모든 DNS 이름 레이블(사용자가 정의한 경우) 간의 매핑을 유지하지만, 가상 머신을 중지(할당 취소)된 상태에서 시작할 때 동적 IP 주소가 변경될 수 있습니다. 주소가 변경되지 않도록 하려면 고정 IP 주소를 할당합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip#az_network_public_ip_list): 공용 IP 주소를 나열함, [az network public-ip-show](/cli/azure/network/public-ip#az_network_public_ip_show): 설정을 표시함, [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update): 업데이트함, [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete): 삭제함|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress): 공용 IP 주소 개체를 검색하고 해당 설정을 확인함, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress): 설정을 업데이트함, [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress): 삭제함|

## <a name="permissions"></a>권한

공용 IP 주소에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할 또는 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 계정이 할당되어야 합니다.

| 조치                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 공용 IP 주소 읽기                                          |
| Microsoft.Network/publicIPAddresses/write                          | 공용 IP 주소 만들기 또는 업데이트                           |
| Microsoft.Network/publicIPAddresses/delete                         | 공용 IP 주소 삭제                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 공용 IP 주소를 리소스에 연결                    |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 공용 IP 주소 만들기
- 공용 IP 주소에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용
- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure Virtual Machine, [Azure 인터넷 연결 Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure VPN Gateway를 사용하는 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 만들 때 공용 IP 주소 할당
