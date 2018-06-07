---
title: 자습서 - Azure PowerShell을 사용하여 확장 집합에 응용 프로그램 설치 | Microsoft Docs
description: Azure PowerShell을 사용하여 사용자 지정 스크립트 확장이 있는 가상 머신 확장 집합에 응용 프로그램을 설치하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ce35db90e49e0d2861dd71e80ac61f05db607338
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365069"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합에 응용 프로그램 설치
확장 집합의 VM(가상 머신) 인스턴스에서 응용 프로그램을 실행하려면 먼저 응용 프로그램 구성 요소 및 필요한 파일을 설치해야 합니다. 이전 자습서에서는 사용자 지정 VM 이미지를 만들고 사용하여 VM 인스턴스를 배포하는 방법을 알아보았습니다. 이 사용자 지정 이미지에는 수동 응용 프로그램 설치 및 구성이 포함되어 있습니다. 또한 각 VM 인스턴스가 배포된 후에 확장 집합에 응용 프로그램 설치를 자동화하거나 이미 확장 집합에서 실행되는 응용 프로그램을 업데이트할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 확장 집합에 응용 프로그램 자동 설치
> * Azure 사용자 지정 스크립트 확장 사용
> * 확장 집합에서 실행되는 응용 프로그램 업데이트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 6.0.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 


## <a name="what-is-the-azure-custom-script-extension"></a>Azure 사용자 지정 스크립트 확장이란?
사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트는 Azure 저장소 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합되며, Azure CLI 2.0, Azure PowerShell, Azure Portal 또는 REST API에서 사용할 수도 있습니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../virtual-machines/windows/extensions-customscript.md)를 참조하세요.

작동 중인 사용자 지정 스크립트 확장을 확인하려면, IIS 웹 서버를 설치하고 확장 집합 VM 인스턴스의 호스트 이름을 출력하는 확장 집합을 만듭니다. 사용자 지정 스크립트 확장 정의는 GitHub에서 샘플 스크립트를 다운로드하고, 필요한 패키지를 설치한 다음, VM 인스턴스 호스트 이름을 기본 HTML 페이지에 작성합니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
이제 [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치에는 80 TCP 포트에서 트래픽을 분산할 뿐만 아니라 3389 TCP 포트의 원격 데스크톱 트래픽 및 5985 TCP 포트의 PowerShell 원격을 허용하는 규칙이 포함되어 있습니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="create-custom-script-extension-definition"></a>사용자 지정 스크립트 확장 정의 만들기
Azure PowerShell은 해시 테이블을 사용하여 다운로드할 파일과 실행할 명령을 저장합니다. 다음 예제에서는 GitHub의 예제 스크립트가 사용됩니다. 먼저 다음과 같이 이 구성 개체를 만듭니다.

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

이제 [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension)을 사용하여 사용자 지정 스크립트 확장을 적용합니다. 이전에 정의된 구성 개체가 확장에 전달됩니다. [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)를 사용하여 VM 인스턴스에서 확장을 업데이트하고 실행합니다.

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

확장 집합의 각 VM 인스턴스는 GitHub에서 스크립트를 다운로드하고 실행합니다. 더 복잡한 예제에서는 여러 응용 프로그램 구성 요소와 파일을 설치할 수 있습니다. 확장 집합의 크기를 강화하면 새 VM 인스턴스에서 자동으로 동일한 사용자 지정 스크립트 확장 정의를 적용하고 필요한 응용 프로그램을 설치합니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
작업 중인 웹 서버를 보려면 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 만든 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![IIS의 기본 웹 페이지](media/tutorial-install-apps-powershell/running-iis.png)

다음 단계에서 업데이트된 버전이 표시되도록 웹 브라우저를 열어 둡니다.


## <a name="update-app-deployment"></a>앱 배포 업데이트
확장 집합의 수명 주기 전체에서 응용 프로그램의 업데이트된 버전을 배포해야 할 수 있습니다. 사용자 지정 스크립트 확장을 사용하면 업데이트된 배포 스크립트를 참조한 다음, 해당 확장을 확장 집합에 다시 적용할 수 있습니다. 이전 단계에서 확장 집합을 만든 경우 `-UpgradePolicyMode`가 *Automatic*으로 설정되어 있습니다. 이 설정을 사용하면 확장 집합의 VM 인스턴스에서 자동으로 업데이트하여 응용 프로그램의 최신 버전을 적용할 수 있습니다.

*customConfigv2*라는 새 구성 정의를 만듭니다. 이 정의는 업데이트된 *v2* 버전의 응용 프로그램 설치 스크립트를 실행합니다.

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

[Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension)을 사용하여 사용자 지정 스크립트 확장 구성을 확장 집합의 VM 인스턴스에 다시 적용합니다. *customConfigv2* 정의는 업데이트된 응용 프로그램 버전을 적용하는 데 사용됩니다.

```azurepowershell-interactive
# Reapply the Custom Script Extension to install the updated website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $customConfigv2

# Update the scale set and reapply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

확장 집합의 모든 VM 인스턴스가 자동으로 샘플 웹 페이지의 최신 버전으로 업데이트됩니다. 업데이트된 버전을 확인하려면 브라우저에서 웹 사이트를 새로 고칩니다.

![IIS에서 업데이트된 웹 페이지](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 확장 집합에 응용 프로그램을 자동으로 설치하고 업데이트하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 확장 집합에 응용 프로그램 자동 설치
> * Azure 사용자 지정 스크립트 확장 사용
> * 확장 집합에서 실행되는 응용 프로그램 업데이트

확장 집합을 자동으로 크기 조정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합 자동 크기 조정](tutorial-autoscale-powershell.md)
