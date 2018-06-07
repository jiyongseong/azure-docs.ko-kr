---
title: Windows Server용 Azure Hybrid Benefit | Microsoft Docs
description: Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071956"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit
Software Assurance 고객은 Windows Server용 Azure Hybrid Benefit을 통해 온-프레미스 Windows Server 라이선스를 사용하고 Azure에서 Windows 가상 머신을 실행하여 비용을 절감할 수 있습니다. Windows OS를 사용하여 새 가상 머신을 배포하려면 Windows Server용 Azure Hybrid Benefit을 사용할 수 있습니다. 이 문서에서는 Windows Server용 Azure 하이브리드 혜택을 통해 새 VM을 배포하는 방법과 기존 실행 VM을 업데이트하는 방법에 대한 단계를 살펴봅니다. Windows Server용 Azure Hybrid Benefit 라이선스 및 비용 절감에 대한 자세한 내용은 [Windows Server용 Azure Hybrid Benefit 라이선스 페이지](https://azure.microsoft.com/pricing/hybrid-use-benefit/)를 참조하세요.

> [!Important]
> 각 2개 프로세서 라이선스 또는 각 16코어 라이선스 집합이 있으면 최대 8코어 인스턴스 두 개 또는 최대 16코어 인스턴스 한 개를 받을 수 있습니다. Standard Edition 라이선스에 대한 Azure Hybrid Benefit은 온-프레미스 또는 Azure에서 한 번만 사용할 수 있습니다. Datacenter Edition 혜택은 온-프레미스와 Azure 모두에서 동시에 사용할 수 있습니다.
>

> [!Important]
> Windows Server OS를 실행하는 모든 VM을 통해 Windows Server용 Azure Hybrid Benefit의 사용은 SQL Server 또는 타사 마켓플레이스 소프트웨어와 같은 추가 소프트웨어가 포함된 VM을 포함하여 모든 지역에서 지원됩니다. 
>

> [!NOTE]
> 클래식 VM의 경우 온-프레미스 사용자 지정 이미지로부터의 새 VM 배포만 지원됩니다. 이 문서에서 지원되는 기능을 활용하려면 먼저 클래식 VM을 리소스 관리자 모델로 마이그레이션해야 합니다.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit 사용 방법
몇 가지 방법으로 Windows Server용 Azure Hybrid Benefit을 사용할 수 있습니다.

1. 제공된 [Azure Marketplace의 Windows Server 이미지](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) 중 하나를 통해 VM을 배포할 수 있습니다.
2. 사용자 지정 VM을 업로드하고 Resource Manager 템플릿 또는 Azure PowerShell을 사용하여 배포할 수 있음
3. Windows Server에 대해 Azure 하이브리드 혜택 또는 종량제를 통해 실행하는 방법 간에 기존 VM을 토글 및 변환할 수 있습니다.
4. 가상 머신 확장 집합에서도 Windows Server용 Azure Hybrid Benefit을 적용할 수 있음


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit으로 VM 만들기
Windows Server용 Azure Hybrid Benefit에 대해 모든 Windows Server OS 기반 이미지를 제공합니다. Azure 플랫폼 지원 이미지를 사용하거나 사용자 지정 Windows Server 이미지를 업로드할 수 있습니다. 

### <a name="portal"></a>포털
Windows Server용 Azure Hybrid Benefit을 사용하여 VM을 만들려면 "비용 절감" 섹션에서 토글을 사용하세요.

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Template
Resource Manager 템플릿 내에서 `licenseType` 추가 매개 변수를 지정해야 합니다. [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있음
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Windows Server 용 Azure 하이브리드 혜택을 사용하여 기존 VM 변환
Windows Server용 Azure 하이브리드 혜택을 활용하기 위해 변환할 기존 VM이 있는 경우 다음과 같이 VM의 라이선스 유형을 업데이트할 수 있습니다.

### <a name="portal"></a>포털
Portal VM 블레이드에서 “구성” 옵션을 선택하여 Azure 하이브리드 혜택을 사용하고 “Azure 하이브리드 혜택”을 토글하도록 VM을 업데이트할 수 있습니다.

### <a name="powershell"></a>PowerShell
- 기존 Windows Server VM을 Windows Server용 Azure Hybrid Benefit으로 변환

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- 혜택이 포함된 Windows Server VM을 다시 종량제로 변환

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- 기존 Windows Server VM을 Windows Server용 Azure Hybrid Benefit으로 변환

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM이 라이선싱 혜택을 사용하고 있는지 확인하는 방법
PowerShell 또는 Resource Manager 템플릿 또는 포털을 통해 VM을 배포한 후 다음 메서드에서 설정을 확인할 수 있습니다.

### <a name="portal"></a>포털
포털 VM 블레이드에서 "구성" 탭을 선택하여 Windows Server용 Azure Hybrid Benefit에 대한 토글을 볼 수 있습니다.

### <a name="powershell"></a>PowerShell
다음 예제에서는 단일 VM용 라이선스 종류를 표시
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

출력
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

이 출력은 Windows Server용 Azure Hybrid Benefit 라이선스 없이 배포된 다음 VM과 대비됩니다.
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>구독에서 Windows Server용 Azure Hybrid Benefit으로 모든 VM 나열
Windows Server용 Azure Hybrid Benefit으로 배포된 모든 가상 머신을 확인 및 산출하려면 구독에서 다음 명령을 실행할 수 있습니다.

### <a name="portal"></a>포털
Virtual Machine 또는 가상 머신 확장 집합 리소스 블레이드에서 "Azure Hybrid Benefit"을 포함하기 위한 테이블 열을 구성하여 모든 VM(s) 및 라이선스 유형의 목록을 볼 수 있습니다. VM 설정은 "사용됨", "사용 안 함" 또는 "지원되지 않음" 상태 중 하나에 있을 수 있습니다.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit을 통해 새 Virtual Machine 확장 집합을 배포
가상 머신 확장 집합 Resource Manager 템플릿 내에서 추가 매개 변수 `licenseType`을 VirtualMachineProfile 속성 내에서 지정해야 합니다. ARM 템플릿, Powershell, Azure CLI 또는 REST를 통해 확장 집합에 대한 만들기나 업데이트 동안 이 작업을 수행할 수 있습니다.

다음 예제에서는 Windows Server 2016 Datacenter 이미지를 통해 ARM 템플릿을 사용합니다.
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
확장 집합을 업데이트하는 자세한 방법은 [가상 머신 확장 집합 수정](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) 방법에 대해 자세히 알아보면 됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure 하이브리드 혜택을 사용하여 비용을 절감하는 방법](https://azure.microsoft.com/pricing/hybrid-use-benefit/)에 대해 자세히 알아봅니다.
- [Azure Hybrid Benefit에 대한 질문과 대답](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)을 자세히 알아봄
- [Windows Server용 Azure 하이브리드 혜택 라이선스 세부 지침](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)에 대해 자세히 알아봅니다.
- [Windows Server용 Azure 하이브리드 혜택 및 Azure Site Recovery를 사용하여 응용 프로그램을 Azure로 훨씬 간편하게 마이그레이션하는 방법](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)에 대해 자세히 알아봅니다.
- [다중 테넌트 호스팅 권한으로 Azure에서 Windows 10](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)에 대해 자세히 알아봅니다.
- [Resource Manager 템플릿 사용](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.
