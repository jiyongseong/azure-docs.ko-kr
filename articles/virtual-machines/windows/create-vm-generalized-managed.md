---
title: Azure에서 관리되는 이미지에서 VM 만들기 | Microsoft Docs
description: Resource Manager 배포 모델에서 Azure PowerShell 또는 Azure Portal을 사용하여 일반화된 관리되는 이미지로 Windows 가상 머신을 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-vm-from-a-managed-image"></a>관리되는 이미지에서 VM 만들기

Azure PowerShell 또는 Azure Portal을 사용하여 관리되는 VM 이미지로 여러 VM을 만들 수 있습니다. 관리 VM 이미지는 OS 및 데이터 디스크를 비롯하여 VM을 만드는 데 필요한 정보를 포함하고 있습니다. OS 디스크와 데이터 디스크를 포함하여 이미지를 구성하는 VHD는 관리 디스크로 저장됩니다. 

새 VM을 만들려면 [만들어 놓은 관리 VM 이미지가 있어야 합니다](capture-image-resource.md). 

## <a name="use-the-portal"></a>포털 사용

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. **형식**별로 리소스를 정렬하여 간편하게 이미지를 찾을 수 있습니다.
3. 목록에서 사용할 이미지를 선택합니다. 이미지 **개요** 페이지가 열립니다.
4. 메뉴에서 **+ VM 만들기**를 클릭합니다.
5. 가상 머신 정보를 입력합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 머신에 로그인하는 데 사용됩니다 완료되면 **확인**을 클릭합니다. 기존 리소스 그룹에서 새 VM을 만들거나 **새로 만들기**를 선택하여 VM을 저장할 새 리소스 그룹을 만들 수 있습니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 
7. **설정**에서 필요한 항목을 변경하고 **확인**을 클릭합니다. 
8. 요약 페이지에서 이미지 이름이 **개인 이미지** 아래 나열되어야 합니다. **확인**을 클릭하여 가상 머신 배포를 시작합니다.


## <a name="use-powershell"></a>PowerShell 사용

[New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet에 설정된 간소화된 매개 변수를 사용하여 이미지에서 VM을 만드는 데 PowerShell을 사용할 수 있습니다. 이미지는 VM을 만들려는 동일한 리소스 그룹에 있어야 합니다.

이 예제에는 AzureRM 모듈 버전 5.6.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

New-AzureRmVm에 설정된 간소화된 매개 변수에는 이름, 리소스 그룹 및 이미지 이름을 입력하여 이미지에서 VM을 만들어야 합니다. 하지만 자동으로 생성된 모든 리소스 이름으로 **-Name** 매개 변수 값을 사용합니다. 이 예제에서는 각 리소스에 보다 자세한 이름을 입력하지만 cmdlet을 통해 자동으로 만들 수 있습니다. 사전에 가상 네트워크와 같은 리소스를 만들고 이름을 cmdlet에 전달할 수도 있습니다. 이름별로 찾을 수 있는 경우 기존 리소스를 사용합니다.

다음 예제에서는 *myImage*라는 이미지의 *myResourceGroup* 리소스 그룹에서 *myVMFromImage*라는 VM을 만듭니다. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>다음 단계
Azure PowerShell을 사용하여 새 가상 머신을 관리하려면 [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

