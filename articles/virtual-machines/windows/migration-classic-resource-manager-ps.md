---
title: PowerShell을 사용하여 Resource Manager로 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure PowerShell 명령을 사용하여 VM(가상 머신), VNET(가상 네트워크), 저장소 계정 등의 IaaS 리소스를 플랫폼 지원 방식으로 클래식에서 ARM(Azure Resource Manager)으로 마이그레이션하는 과정을 안내합니다.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: a57337acadafe40839e16d6a31861ff7c892c071
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션
이러한 단계에서는 Azure PowerShell 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다.

원한 경우 [Azure CLI(Azure 명령줄 인터페이스)](../linux/migration-classic-resource-manager-cli.md)를 사용하여 리소스를 마이그레이션할 수도 있습니다.

* 지원되는 마이그레이션 시나리오에 대한 배경 정보를 보려면 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)을 참조하세요.
* 자세한 지침 및 마이그레이션 연습에 대해서는 [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)를 참조하세요.
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)

<br>
다음은 마이그레이션 프로세스 중에 단계를 실행해야 하는 순서를 나타내는 순서도입니다.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>1단계: 마이그레이션 계획
클래식에서 Resource Manager로 IaaS 리소스 마이그레이션을 평가하는 몇 가지 모범 사례가 있습니다.

* [지원/지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md)을 읽어봅니다. 지원되지 않는 구성 또는 기능을 사용하는 가상 머신이 있다면, 해당 구성/기능 지원이 발표되기를 기다리는 것이 좋습니다. 필요에 따라서는 마이그레이션이 가능하도록 해당 기능을 제거하거나 구성 외부로 이동할 수도 있습니다.
* 현재 인프라 및 응용 프로그램을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

> [!IMPORTANT]
> Application Gateway는 현재 클래식에서 Resource Manager로 마이그레이션될 수 없습니다. Application Gateway를 사용하여 클래식 가상 네트워크를 마이그레이션하려면 준비 작업을 실행하여 네트워크를 이동하기 전에 게이트웨이를 제거합니다. 마이그레이션을 완료한 후 Azure Resource Manager에서 게이트웨이를 다시 연결합니다.
>
>다른 구독에서 ExpressRoute 회로에 연결하는 ExpressRoute 게이트웨이를 자동으로 마이그레이션할 수 없습니다. 이러한 경우에 ExpressRoute 게이트웨이를 제거하고 가상 네트워크를 마이그레이션한 다음 게이트웨이를 다시 만듭니다. 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션](../../expressroute/expressroute-migration-classic-resource-manager.md)을 참조하세요.
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>2단계: Azure PowerShell 최신 버전 설치
Azure PowerShell을 설치하는 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/) 또는 [WebPI(웹 플랫폼 설치 관리자)](http://aka.ms/webpi-azps)가 있습니다. WebPI는 매월 업데이트를 수신합니다. PowerShell 갤러리는 지속적으로 업데이트를 수신합니다. 이 문서는 Azure PowerShell 버전 2.1.0을 기반으로 합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>3단계: Azure Portal에서 구독에 대한 관리자인지 확인
이 마이그레이션을 수행하려면 [Azure Portal](https://portal.azure.com)에 구독에 대한 공동 관리자로 추가되어야 합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **구독**을 선택합니다. 표시되지 않으면 **모든 서비스**를 선택합니다.
3. 적절한 등록 항목을 찾은 다음 **내 역할** 필드를 확인합니다. 공동 관리자의 경우 값은 _계정 관리자_여야 합니다.

공동 관리자를 추가할 수 없으면 구독에 대한 서비스 관리자 또는 공동 관리자에게 추가해 줄 것을 요청합니다.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4단계: 구독 설정 및 마이그레이션에 등록
먼저, PowerShell 프롬프트를 시작합니다. 마이그레이션의 경우 클래식 및 Resource Manager에 대한 환경을 설정해야 합니다.

Resource Manager 모델에 대한 계정으로 로그인합니다.

```powershell
    Connect-AzureRmAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독 이름을 **내 Azure 구독**으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 등록은 일회성 단계이지만 마이그레이션 전에 한 번은 수행해야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.
>
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.*
>
>

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다.

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

등록이 완료될 때까지 5분 정도 기다려 주세요. 다음 명령을 사용하여 승인 상태를 확인할 수 있습니다.

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

계속 진행하기 전에 RegistrationState가 `Registered` 인지 확인합니다.

이제 클래식 모델에 대한 계정으로 로그인합니다.

```powershell
    Add-AzureAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독을 **내 Azure 구독**으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>5단계: 현재 배포의 Azure 지역 또는 VNET에 Azure Resource Manager Virtual Machine vCPU가 충분한지 확인
다음 PowerShell 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)를 참조하세요.

이 예제에서는 **미국 서부** 지역의 사용 가능 여부를 확인합니다. 예제 지역 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6단계: IaaS 리소스를 마이그레이션하는 명령 실행
> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼이 해당 작업을 다시 시도합니다.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>6.1단계: 옵션 1 - 클라우드 서비스(가상 네트워크가 아님)에서 가상 머신 마이그레이션
다음 명령을 사용하여 클라우드 서비스 목록을 가져와서 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크에 있거나 웹 또는 작업자 역할을 포함하는 경우 명령을 오류 메시지를 반환합니다.

```powershell
    Get-AzureService | ft Servicename
```

클라우드 서비스에 대한 배포 이름을 가져옵니다. 이 예제에서는 서비스 이름이 **내 서비스**입니다. 예제 서비스 이름을 사용자 고유의 서비스 이름으로 바꿉니다.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

마이그레이션을 위해 클라우드 서비스의 가상 머신을 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

* **옵션 1. VM을 플랫폼에서 만든 가상 네트워크에 마이그레이션**

    먼저 다음 명령을 사용하여 클라우드 서비스를 마이그레이션할 수 있는지 유효성을 검사합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 **준비** 단계로 넘어갈 수 있습니다.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **옵션 2. Resource Manager 배포 모델에서 기존 가상 네트워크로 마이그레이션**

    이 예제에서는 리소스 그룹 이름을 **myResourceGroup**, 가상 네트워크 이름을 **myVirtualNetwork**, 서브넷 이름을 **mySubNet**으로 설정합니다. 예제의 이름을 사용자 고유의 리소스 이름으로 바꿉니다.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    먼저 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에서 마이그레이션이 가능한 것으로 확인되면 다음 준비 단계를 진행할 수 있습니다.

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

위의 옵션 중 하나를 사용할 경우 준비 작업에 성공적으로 수행되면 VM의 마이그레이션 상태를 쿼리합니다. `Prepared` 상태인지 확인합니다.

이 예제에서는 VM 이름을 **myVM**으로 설정합니다. 예제 이름을 사용자 고유의 VM 이름으로 바꿉니다.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>6.1단계: 옵션 2 - 가상 네트워크에서 가상 머신 마이그레이션

가상 네트워크에서 가상 머신을 마이그레이션하려면 가상 네트워크를 마이그레이션합니다. 가상 머신은 가상 네트워크와 함께 자동으로 마이그레이션됩니다. 마이그레이션할 가상 네트워크를 선택합니다.
> [!NOTE]
> 가상 머신의 VHD(OS 및 데이터) 파일을 사용하여 Managed Disks를 사용하는 새로운 Resource Manager 가상 머신을 만들어서 [단일 클래식 가상 머신을 마이그레이션](migrate-single-classic-to-resource-manager.md)합니다.
<br>

> [!NOTE]
> 가상 네트워크 이름은 새 포털에 표시된 것과 다를 수 있습니다. 새 Azure Portal에는 이름이 `[vnet-name]`으로 표시되지만 실제 가상 네트워크 이름은 `Group [resource-group-name] [vnet-name]` 형식입니다. 마이그레이션하기 전에 `Get-AzureVnetSite | Select -Property Name` 명령을 사용하여 실제 가상 네트워크 이름을 조회하거나, 이전 Azure Portal에서 확인합니다. 

이 예제에서는 가상 네트워크 이름을 **myVnet**으로 설정합니다. 예제 가상 네트워크 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 가상 네트워크에 웹 또는 작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면 유효성 검사 오류 메시지가 표시됩니다.
>
>

먼저 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에서 마이그레이션이 가능한 것으로 확인되면 다음 준비 단계를 진행할 수 있습니다.

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell 또는 Azure 포털을 사용하여 준비된 가상 머신에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>6.2단계 저장소 계정 마이그레이션
가상 머신 마이그레이션이 완료되면 저장소 계정을 마이그레이션하는 것이 좋습니다.

저장소 계정을 마이그레이션하기 전에 이전의 필수 요소 검사를 수행하세요.

* **디스크가 저장소 계정에 저장되는 클래식 가상 머신 마이그레이션**

    이전 명령은 저장소 계정에서 모든 클래식 VM 디스크의 RoleName 및 DiskName 속성을 반환합니다. RoleName은 디스크가 연결된 가상 머신의 이름입니다. 이전 명령이 디스크를 반환하면 저장소 계정을 마이그레이션하기 전에 디스크가 연결된 가상 머신을 마이그레이션해야 합니다.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **저장소 계정에서 연결되지 않은 클래식 VM 디스크 삭제**

    다음 명령을 사용하여 저장소 계정에서 연결되지 않은 클래식 VM 디스크를 찾습니다.

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    위의 명령이 디스크를 반환하면 다음 명령을 사용하여 다음과 같은 디스크를 삭제합니다.

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **저장소 계정에 저장된 VM 이미지 삭제**

    이전 명령은 저장소 계정에 저장된 이미지 중 OS 디스크가 포함된 모든 VM 이미지를 반환합니다.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     이전 명령은 저장소 계정에 저장된 이미지 중 데이터가 포함된 모든 VM 이미지를 반환합니다.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    이전 명령을 사용하여 위의 명령을 통해 반환된 모든 VM 이미지를 삭제합니다.
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

다음 명령을 사용하여 마이그레이션을 위한 각 저장소 계정의 유효성을 검사합니다. 이 예제에서 저장소 계정 이름은 **myStorageAccount**입니다. 예제 이름을 사용자 고유의 저장소 계정 이름으로 바꿉니다.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

다음 단계에서는 마이그레이션을 위한 저장소 계정을 준비합니다.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Azure PowerShell 또는 Azure 포털을 사용하여 준비된 저장소 계정에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>다음 단계
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
