---
title: Azure 가상 네트워크 피어링 만들기 - 서로 다른 배포 모델 - 같은 구독 | Microsoft Docs
description: 같은 Azure 구독에 존재하는 서로 다른 Azure 배포 모델을 통해 만든 가상 네트워크 간의 가상 네트워크 피어링을 만드는 방법을 학습합니다.
services: virtual-network
documentationcenter: ''
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: f4fe93078246bc25eb41a7f8e685d75f8dcb6b7c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012627"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>가상 네트워크 피어링 만들기 - 서로 다른 배포 모델 및 같은 구독

이 자습서에서는 서로 다른 배포 모델을 통해 만들어진 가상 네트워크 간의 가상 네트워크 피어링을 만드는 방법을 알아봅니다. 두 가상 네트워크는 동일한 구독에 있어야 합니다. 두 가상 네트워크를 피어링하면 서로 다른 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. [가상 네트워크 피어링](virtual-network-peering-overview.md)에 대해 자세히 알아보세요.

가상 네트워크 피어링을 만드는 단계는 가상 네트워크가 동일한 구독에 있는지 아니면 다른 구독에 있는지에 따라, 그리고 가상 네트워크가 어느 [Azure 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 생성되었는지에 따라 다릅니다. 다음 표에 나온 시나리오를 클릭하여 다른 시나리오에서 가상 네트워크 피어링을 만드는 방법을 알아보세요.

|Azure 배포 모델  | Azure 구독  |
|--------- |---------|
|[둘 다 리소스 관리자](tutorial-connect-virtual-networks-portal.md) |동일|
|[둘 다 리소스 관리자](create-peering-different-subscriptions.md) |다름|
|[하나는 리소스 관리자, 다른 하나는 클래식](create-peering-different-deployment-models-subscriptions.md) |다름|

클래식 배포 모델을 통해 배포된 두 가상 네트워크 간에는 가상 네트워크 피어링을 만들 수 없습니다. 둘 다 클래식 배포 모델을 통해 생성된 가상 네트워크를 연결해야 할 경우 Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하여 가상 네트워크를 연결할 수 있습니다.

이 자습서는 동일한 지역에 가상 네트워크를 피어링합니다. 다른 [지원되는 지역](virtual-network-manage-peering.md#cross-region)에 있는 가상 네트워크를 피어링할 수도 있습니다. 가상 네트워크를 피어링하기 전에 [피어링 요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 이해하는 것이 좋습니다.

[Azure Portal](#portal), Azure CLI([명령줄 인터페이스](#cli)), Azure [PowerShell](#powershell) 또는 [Azure Resource Manager 템플릿](#template)을 사용하여 가상 네트워크 피어링을 만들 수 있습니다. 앞의 도구 링크 중 원하는 도구 링크를 클릭하여 원하는 도구를 사용하여 가상 네트워크 피어링을 만드는 단계로 바로 이동하세요.

## <a name="create-peering---azure-portal"></a>피어링 만들기 - Azure Portal

1. [Azure 포털](https://portal.azure.com) 에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요.
2. **+ 새로 만들기**, **네트워킹**, **가상 네트워크**를 차례로 클릭합니다.
3. **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: *myVnet1*
    - **주소 공간**: *10.0.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.0.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.
    - **위치**: *미국 동부*
4. **+새로 만들기**를 클릭합니다. **Marketplace 검색** 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다. 
5. **가상 네트워크** 블레이드의 **배포 모델 선택** 상자에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.
6. **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: *myVnet2*
    - **주소 공간**: *10.1.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.1.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **기존 항목 사용**을 선택하고 *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*
7. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
8. **myVNet1**을 클릭합니다.
9. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **피어링**을 클릭합니다.
10. 나타난 **myVnet1 - 피어링** 블레이드에서 **+ 추가**를 클릭합니다.
11. 나타나는 **피어링 추가** 블레이드에서 다음 옵션을 입력하거나 선택한 다음 **확인**을 클릭합니다.
     - **이름**: *myVnet1ToMyVnet2*
     - **가상 네트워크 배포 모델**: **클래식**을 선택합니다. 
     - **구독**: 사용자의 구독을 선택합니다.
     - **가상 네트워크**: **가상 네트워크 선택**을 클릭한 다음 **myVnet2**를 클릭합니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-a-peering)를 읽어보세요.
12. 이전 단계에서 **확인**을 클릭한 후 **피어링 추가** 블레이드가 닫히고 **myVnet1 - 피어링** 블레이드가 다시 표시됩니다. 몇 초 후 만든 피어링이 블레이드에 나타납니다. 만든 **myVnet1ToMyVnet2** 피어링의 **PEERING STATUS** 열에**Connected**가 나열됩니다.

    이제 피어링이 설정되었습니다. 어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.
13. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
14. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

## <a name="cli"></a>피어링 만들기 - Azure CLI

1. 가상 네트워크(클래식)를 만들려면 Azure CLI 1.0을 [설치](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다.
2. 명령 세션을 열고 `azure login` 명령을 사용하여 Azure에 로그인합니다.
3. `azure config mode asm` 명령을 입력하여 CLI를 서비스 관리 모드에서 실행합니다. 
4. 다음 명령을 입력하여 가상 네트워크(클래식)를 만듭니다.
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. 리소스 그룹 및 가상 네트워크(리소스 관리자)를 만듭니다. CLI 1.0 또는 2.0을 사용할 수 있습니다([설치](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). 피어링을 만들 때는 CLI 2.0을 사용해야 하기 때문에 이 자습서에서는 2.0을 사용하여 가상 네트워크(리소스 관리자)를 만듭니다. CLI 2.0.4 이상을 설치한 로컬 컴퓨터에서 다음 Bash CLI 스크립트를 실행합니다. Windows 클라이언트에서의 Bash CLI 스크립트 실행과 관련한 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. Azure Cloud Shell을 사용하여 스크립트를 실행할 수도 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 스크립트에서 **사용해보기** 단추를 클릭하면 Azure 계정 로그인 가능성을 기록하는 Cloud Shell이 호출됩니다. 스크립트를 실행하려면 **복사** 단추를 클릭하여 내용을 Cloud Shell에 붙여 넣고 `Enter`를 누릅니다.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. 서로 다른 배포 모델을 통해 만들어진 두 가상 네트워크 사이에 가상 네트워크 피어링을 만듭니다. PC의 텍스트 편집기에 다음 스크립트를 복사합니다. `<subscription id>`는 구독 ID로 바꿉니다. 구독 ID를 모르는 경우 `az account show` 명령을 입력합니다. 출력에 표시되는 **id** 값이 구독 ID입니다. CLI 세션에 수정된 스크립트를 붙여 넣고 `Enter`를 누릅니다.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. 스크립트를 실행한 후 가상 네트워크에 대한 피어링을 검토합니다(리소스 관리자). 다음 명령을 복사하여 CLI 세션에 붙여 넣고 `Enter`를 누릅니다.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    출력에서 **PeeringState** 열에 **Connected**가 표시됩니다. 

    어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.
8. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
9. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-cli)에서 설명하는 단계를 완료합니다.

## <a name="powershell"></a>피어링 만들기 - PowerShell

1. 최신 버전의 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 및 [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. PowerShell 세션을 시작합니다.
3. PowerShell에서 `Add-AzureAccount` 명령을 입력하여 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요.
4. PowerShell에 가상 네트워크(클래식)를 만들려면 기존 네트워크 구성 파일을 새로 만들거나 수정해야 합니다. [네트워크 구성 파일 내보내기, 업데이트 및 가져오기](virtual-networks-using-network-configuration-file.md) 방법을 확인합니다. 파일에는 이 자습서에서 사용되는 가상 네트워크에 대한 **VirtualNetworkSite** 요소가 있어야 합니다.

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 변경된 네트워크 구성 파일을 가져오면 구독의 기존 가상 네트워크가 변경될 수 있습니다. 이전 가상 네트워크만 추가하고, 구독에서 기존 가상 네트워크를 변경하거나 제거하지 않도록 합니다. 
5. `Connect-AzureRmAccount` 명령을 입력하여 Azure에 로그인하고 가상 네트워크(리소스 관리자)를 만듭니다.  로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요.
6. 리소스 그룹 및 가상 네트워크(리소스 관리자)를 만듭니다. 스크립트 복사하여 PowerShell에 붙여 넣고 `Enter`를 누릅니다.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. 서로 다른 배포 모델을 통해 만들어진 두 가상 네트워크 사이에 가상 네트워크 피어링을 만듭니다. PC의 텍스트 편집기에 다음 스크립트를 복사합니다. `<subscription id>`는 구독 ID로 바꿉니다. 구독 ID를 모르는 경우 `Get-AzureRmSubscription` 명령을 입력하여 확인합니다. 반환된 출력의 **ID** 값이 구독 ID입니다. 스크립트를 실행하려면 텍스트 편집기에서 수정된 스크립트를 복사한 다음 PowerShell 세션을 마우스 오른쪽 단추로 클릭하고 `Enter`를 누릅니다.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. 스크립트를 실행한 후 가상 네트워크에 대한 피어링을 검토합니다(리소스 관리자). 다음 명령을 복사하여 PowerShell 세션에 붙여 넣고 `Enter`를 누릅니다.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    출력에서 **PeeringState** 열에 **Connected**가 표시됩니다.

    어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.

9. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
10. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-powershell)에서 설명하는 단계를 완료합니다.
 
## <a name="delete"></a>리소스 삭제
이 자습서를 마친 경우 사용 요금이 발생하지 않도록 자습서에서 만든 리소스를 삭제하려고 할 것입니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다.

### <a name="delete-portal"></a>Azure Portal

1. 포털 검색 상자에 **myResourceGroup**을 입력합니다. 검색 결과에서 **myResourceGroup**을 클릭합니다.
2. **myResourceGroup** 블레이드에서 **삭제** 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력** 상자에 **myResourceGroup**을 입력한 다음 **삭제**를 클릭합니다.

### <a name="delete-cli"></a>Azure CLI

1. Azure CLI 2.0을 사용하여 다음 명령을 통해 가상 네트워크(리소스 관리자)를 삭제합니다.

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Azure CLI 1.0을 사용하여 다음 명령을 통해 가상 네트워크(클래식)를 삭제합니다.

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. 다음 명령을 입력하여 가상 네트워크(리소스 관리자)를 삭제합니다.

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. PowerShell을 통해 가상 네트워크(클래식)를 삭제하려면 기존 네트워크 구성 파일을 수정해야 합니다. [네트워크 구성 파일 내보내기, 업데이트 및 가져오기](virtual-networks-using-network-configuration-file.md) 방법을 확인합니다. 이 자습서에서 사용되는 가상 네트워크에 대한 다음 VirtualNetworkSite 요소를 제거합니다.

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 변경된 네트워크 구성 파일을 가져오면 구독의 기존 가상 네트워크(클래식)에 변경을 초래할 수 있습니다. 이전 가상 네트워크만 제거하고, 구독에서 다른 기존 가상 네트워크를 변경하거나 제거하지 않도록 합니다. 

## <a name="next-steps"></a>다음 단계

- 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 먼저 중요한 [가상 네트워크 피어링 제약 조건 및 동작](virtual-network-manage-peering.md#requirements-and-constraints)에 철저하게 익숙해집니다.
- 모든 [가상 네트워크 피어링 설정](virtual-network-manage-peering.md#create-a-peering)에 대해 알아봅니다.
- 가상 네트워크 피어링을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 방법을 알아봅니다.
