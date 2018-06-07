---
title: Azure Virtual Network 서브넷 추가, 변경 또는 삭제 | Microsoft 문서
description: Azure에서 가상 네트워크 서브넷을 추가, 변경 또는 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 68d4c54b2648dc3b40e69dcde9828d18de318796
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894461"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>가상 네트워크 서브넷 추가, 변경 또는 삭제

가상 네트워크 서브넷을 추가, 변경 또는 삭제하는 방법을 알아봅니다. 가상 네트워크에 배포된 모든 Azure 리소스는 가상 네트워크 내의 서브넷에 배포됩니다. 가상 네트워크를 처음 사용하는 경우, [가상 네트워크 개요](virtual-networks-overview.md)를 참조하거나 [자습서](quick-create-portal.md)를 완료하여 자세히 알아볼 수 있습니다. 가상 네트워크를 만들거나 변경 또는 삭제하려면 [가상 네트워크 관리](manage-virtual-network.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="add-a-subnet"></a>서브넷 추가

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 서브넷을 추가할 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. **+서브넷**을 선택합니다.
5. 다음 매개 변수에 대한 값을 입력합니다.
    - **이름:** 가상 네트워크 내에서 고유해야 합니다.
    - **주소 범위:** 범위는 가상 네트워크의 주소 공간 내에서 고유해야 합니다. 범위는 가상 네트워크 내에서 다른 서브넷 주소 범위와 겹칠 수 없습니다. CIDR(Classless Inter-Domain Routing) 표기법을 사용하여 주소 공간을 지정해야 합니다. 예를 들어 주소 공간이 10.0.0.0/16인 가상 네트워크에서 서브넷 주소 공간을 10.0.0.0/24로 정의할 수 있습니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 주소 범위가 /29인 서브넷을 정의하면 서브넷에 사용할 수 있는 3개의 IP 주소가 만들어집니다. 가상 네트워크를 VPN Gateway에 연결하려면 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에서 대해 자세히 알아보세요. 특정 조건에서는 서브넷을 추가한 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대한 자세한 내용은 [서브넷 설정 변경](#change-subnet-settings)을 참조하세요.
    - **네트워크 보안 그룹**: 0개 또는 1개의 기존 네트워크 보안 그룹을 서브넷에 연결하여 서브넷에 대한 인바운드 및 아웃바운드 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [네트워크 보안 그룹](security-overview.md) 및 [네트워크 보안 그룹을 만드는 방법](tutorial-filter-network-traffic.md)에 대해 알아봅니다.
    - **경로 테이블**: 0개 또는 1개의 기존 경로 테이블을 서브넷에 연결하여 다른 네트워크에 대한 네트워크 트래픽 라우팅을 제어할 수 있습니다. 경로 테이블은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [Azure 라우팅](virtual-networks-udr-overview.md) 및 [경로 테이블을 만드는 방법](tutorial-create-route-table-portal.md)에 대해 알아봅니다.
    - **서비스 끝점:** 서브넷은 0개 또는 여러 개의 서비스 끝점을 서비스에 대해 설정할 수 있습니다. 서비스에 대해 서비스 끝점을 사용하려면 **서비스** 목록에서 서비스 끝점을 사용할 서비스를 선택합니다. 끝점의 위치는 자동으로 구성됩니다. 기본적으로 서비스 끝점은 가상 네트워크의 지역에 대해 구성됩니다. Azure Storage의 경우, 지역 장애 조치(failover) 시나리오를 지원하기 위해 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)에 끝점이 자동으로 구성됩니다.

    서비스 끝점을 제거하려면 서비스 끝점을 제거할 서비스를 선택 취소합니다. 서비스 끝점 및 서비스 끝점을 사용할 수 있는 서비스에 대한 자세한 내용은 [가상 네트워크 서비스 끝점 개요](virtual-network-service-endpoints-overview.md)를 참조하세요. 서비스에 대한 서비스 끝점을 사용하도록 설정하면 서비스를 사용하여 만든 리소스의 서브넷에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 예를 들어 *Microsoft.Storage*에 대해 서비스 끝점을 사용하도록 설정하면 네트워크 액세스 권한을 부여할 모든 Azure Storage 계정에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 서비스 끝점을 사용하도록 설정한 서브넷에 대해 네트워크 액세스를 사용하는 방법에 대한 자세한 내용은 서비스 끝점을 사용하도록 설정한 개별 서비스에 대한 설명서를 참조하세요.

    서브넷에 대해 서비스 끝점을 사용할 수 있는지 검증하려면 서브넷에 있는 임의 네트워크 인터페이스의 [유효 경로](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-virtual-machine)를 확인합니다. 끝점이 구성된 경우 서비스의 주소 접두사와 **VirtualNetworkServiceEndpoint**의 nextHopType으로 구성된 기본 경로가 표시됩니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요.
6. 선택한 가상 네트워크에 서브넷을 추가하려면 **확인**을 선택합니다.

**명령**

- Azure CLI: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>서브넷 설정 변경

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크의 목록에서 설정을 변경할 서브넷이 포함된 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. 서브넷의 목록에서 설정을 변경할 서브넷을 선택합니다. 다음 설정을 변경할 수 있습니다.

    - **주소 범위:** 서브넷 내에 배포된 리소스가 없는 경우 주소 범위를 변경할 수 있습니다. 서브넷에 리소스가 있으면 먼저 다른 서브넷으로 리소스를 이동하거나 서브넷에서 삭제해야 합니다. 리소스 이동 또는 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 이동 또는 삭제하는 방법에 대해 알아보려면 이동 또는 삭제하려는 각 리소스 종류의 설명서를 참조하세요. [서브넷 추가](#add-a-subnet)의 5단계에서 **주소 범위**에 대한 제약 조건을 참조하세요.
    - **사용자**: 기본 제공 역할 또는 사용자 지정 역할을 사용하여 서브넷에 대한 액세스를 제어할 수 있습니다. 역할과 사용자를 할당하여 서브넷에 액세스하는 방법에 대한 자세한 내용은 [역할 할당을 사용하여 Azure 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)를 참조하세요.
    - **네트워크 보안 그룹** 및 **경로 테이블**: [서브넷 추가](#add-a-subnet)의 5단계를 참조하세요.
    - **서비스 끝점**: [서브넷 추가](#add-a-subnet)의 5단계에서 서비스 끝점을 참조하세요. 기존 서브넷에 대해 서비스 끝점을 사용하도록 설정할 경우, 서브넷의 리소스에서 실행 중인 중요 작업이 없는지 확인합니다. 서비스 끝점은 서브넷에 있는 모든 네트워크 인터페이스의 경로를 *0.0.0.0/0* 주소 접두사를 사용하고 다음 홉 유형이 *인터넷*인 기본 경로 사용에서 서비스의 주소 접두사를 사용하고 다음 홉 유형이 *VirtualNetworkServiceEndpoint*인 새 경로 사용으로 전환합니다. 전환 중에, 열려 있는 TCP 연결이 종료될 수 있습니다. 모든 네트워크 인터페이스에서 서비스로 들어오는 트래픽 흐름이 새 경로로 업데이트될 때까지 서비스 끝점을 사용할 수 없습니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요.
5. **저장**을 선택합니다.

**명령**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>서브넷 삭제

서브넷에 리소스가 없는 경우에만 해당 서브넷을 삭제할 수 있습니다. 서브넷에 리소스가 있으면 서브넷을 삭제하기 전에 먼저 서브넷에 있는 리소스를 삭제해야 합니다. 리소스 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류의 설명서를 참조하세요.

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 삭제할 서브넷이 포함된 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. 서브넷 목록에서 삭제할 서브넷의 오른쪽에 있는 **...** 를 선택합니다.
5. **삭제**를 선택한 후 **예**를 선택합니다.

**명령**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>권한

서브넷에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할 또는 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 계정이 할당되어야 합니다.

|조치                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   가상 네트워크 서브넷 읽기              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   가상 네트워크 서브넷 만들기 또는 업데이트  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   가상 네트워크 서브넷 삭제            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   가상 네트워크 연결                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   서브넷에 대해 서비스 끝점 사용     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   서브넷의 가상 머신 가져오기       |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [리소스 관리자 템플릿](template-samples.md)을 사용하여 가상 네트워크 및 서브넷 만들기
- 가상 네트워크에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용