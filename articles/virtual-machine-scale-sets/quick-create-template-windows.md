---
title: 빠른 시작 - Azure 템플릿을 사용하여 Windows 가상 머신 확장 집합 만들기 | Microsoft Docs
description: 샘플 앱을 배포하고 자동 크기 조정 규칙을 구성하는 Azure Resource Manager 템플릿을 사용하여 Windows 가상 머신 확장 집합을 빠르게 만드는 방법을 알아봅니다.
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
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: iainfou
ms.openlocfilehash: d1e9dbb6cf4a7db0e60e04bf266bb988d21f2ed8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>빠른 시작: Azure 템플릿을 사용하여 Windows 가상 머신 확장 집합 만들기
가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 리소스 사용량(예: CPU, 메모리 요구량 또는 네트워크 트래픽)에 따라 자동으로 크기를 조정하는 규칙을 정의할 수 있습니다. 그러면 Azure 부하 분산 장치에서 확장 집합의 VM 인스턴스에 트래픽을 분산합니다. 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합을 만들고, 샘플 응용 프로그램을 배포합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.5.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.


## <a name="define-a-scale-set-in-a-template"></a>템플릿에서 확장 집합 정의
Azure Resource Manager 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 템플릿은 JSON(JavaScript Object Notation)으로 작성되며, 응용 프로그램에 대한 전체 Azure 인프라 환경을 정의합니다. 단일 템플릿에서 가상 머신 확장 집합을 만들고, 응용 프로그램을 설치하고, 자동 크기 조정 규칙을 구성할 수 있습니다. 변수와 매개 변수를 사용하면 이 템플릿을 다시 사용하여 기존의 확장 집합을 업데이트하거나 추가 확장 집합을 만들 수 있습니다. Azure Portal, Azure CLI 2.0 또는 Azure PowerShell을 통하거나 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 배포할 수 있습니다.

템플릿에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)를 참조하세요.

템플릿은 각 리소스 종류에 대한 구성을 정의합니다. 가상 머신 확장 집합 리소스 종류는 개별 VM과 비슷합니다. 가상 머신 확장 집합 리소스 종류의 주요 부분은 다음과 같습니다.

| 자산                     | 속성 설명                                  | 예제 템플릿 값                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| 형식                         | 만들 Azure 리소스 종류                            | Microsoft.Compute/virtualMachineScaleSets |
| 이름                         | 확장 집합 이름                                       | myScaleSet                                |
| location                     | 확장 집합을 만들 위치                     | 미국 동부                                   |
| sku.name                     | 각 확장 집합 인스턴스에 대한 VM 크기                  | Standard_A1                               |
| sku.capacity                 | 처음에 만들 VM 인스턴스의 수           | 2                                         |
| upgradePolicy.mode           | 변경 발생 시의 VM 인스턴스 업그레이드 모드              | 자동                                 |
| imageReference               | VM 인스턴스에 사용할 플랫폼 또는 사용자 지정 이미지 | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | 각 VM 인스턴스에 대한 이름 접두사                     | myvmss                                    |
| osProfile.adminUsername      | 각 VM 인스턴스에 대한 사용자 이름                        | azureuser                                 |
| osProfile.adminPassword      | 각 VM 인스턴스에 대한 암호                        | P@ssw0rd!                                 |

 다음 예제에서는 코어 확장 집합이 리소스 정의를 설정하는 것을 보여 줍니다. 확장 집합 템플릿을 사용자 지정하려면 VM 크기 또는 초기 용량을 변경하거나 다른 플랫폼 또는 사용자 지정 이미지를 사용할 수 있습니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 샘플을 짧게 유지하기 위해 가상 NIC(네트워크 인터페이스 카드) 구성이 표시되지 않습니다. 부하 분산 장치와 같은 추가 구성도 표시되지 않습니다. 완전한 확장 집합 템플릿은 [이 문서의 끝 부분](#deploy-the-template)에 표시되어 있습니다.


## <a name="add-a-sample-application"></a>샘플 응용 프로그램 추가
확장 집합을 테스트하려면 기본 웹 응용 프로그램을 설치합니다. 확장 집합을 배포하는 경우 VM 확장에서 배포 후 구성 및 자동화 작업(예: 응용 프로그램 설치)을 제공할 수 있습니다. 스크립트는 Azure 저장소 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 확장 집합에 확장을 적용하려면 앞의 리소스 예제에 *extensionProfile* 섹션을 추가합니다. 확장 프로필은 일반적으로 다음 속성을 정의합니다.

- 확장 형식
- 확장 게시자
- 확장 버전
- 구성 또는 설치 스크립트의 위치
- VM 인스턴스에서 실행할 명령

[Windows의 ASP.NET 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 샘플 템플릿은 PowerShell DSC 확장을 사용하여 IIS에서 실행되는 ASP.NET MVC 앱을 설치합니다. 

설치 스크립트는 *url*에서 정의한 대로 GitHub에서 다운로드됩니다. 그런 다음 확장이 *function* 및 *script*에서 정의한 대로 *IISInstall.ps1* 스크립트에서 *InstallIIS*를 실행합니다. ASP.NET 앱 자체는 *WebDeployPackagePath*에서 정의한 대로 GitHub에서 다운로드되는 웹 배포 패키지로 제공됩니다.

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>템플릿 배포
다음 **Azure에 배포** 단추를 사용하여 [Windows의 ASP.NET MVC 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 템플릿을 배포할 수 있습니다. 이 단추는 Azure Portal을 열고, 전체 템플릿을 로드하고, 몇 가지 매개 변수(예: 확장 집합 이름, 인스턴스 수 및 관리자 자격 증명)를 요구하는 메시지를 표시합니다.

[![Azure에 템플릿 배포](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

또한 Azure PowerShell을 사용하여 다음과 같이 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)를 통해 Windows에서 ASP.NET 응용 프로그램을 설치할 수도 있습니다.

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

프롬프트에 응답하여 VM 인스턴스에 대한 확장 집합 이름 및 관리자 자격 증명을 제공합니다. 확장 집합을 만들고 앱을 구성하기 위해 확장을 적용하는 데 10-15분 정도 걸릴 수 있습니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
작동 중인 확장 집합을 확인하려면 웹 브라우저에서 샘플 웹 응용 프로그램에 액세스합니다. 다음과 같이 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 *http://publicIpAddress/MyApp* 형식으로 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![실행 중인 IIS 사이트](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹과 확장 집합을 제거할 수 있습니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure 템플릿을 사용하여 Windows 확장 집합을 만들고, PowerShell DSC 확장을 사용하여 VM 인스턴스에 기본 ASP.NET 앱을 설치했습니다. 더 자세히 알아보려면 Azure 가상 머신 확장 집합을 만들고 관리하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure 가상 머신 확장 집합 만들기 및 관리](tutorial-create-and-manage-powershell.md)
