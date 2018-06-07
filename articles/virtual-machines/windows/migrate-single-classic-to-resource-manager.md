---
title: 클래식 VM을 ARM 관리 디스크 VM으로 마이그레이션 | Microsoft Docs
description: Resource Manager 배포 모델에서는 단일 Azure VM을 클래식 배포 모델에서 Managed Disks로 마이그레이션합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: d0307b26741a6bbbf29626e670467cdd72697646
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33943584"
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>VHD에서 클래식 VM을 새 ARM 관리 디스크 VM으로 수동으로 마이그레이션 


이 섹션을 통해 Resource Manager 배포 모델에서는 기존 Azure VM을 클래식 배포 모델에서 [Managed Disks](managed-disks-overview.md)로 마이그레이션할 수 있습니다.


## <a name="plan-for-the-migration-to-managed-disks"></a>Managed Disks로 마이그레이션 계획 수립

이 섹션을 통해 VM 및 디스크 유형에 대한 최선의 결정을 내릴 수 있습니다.


### <a name="location"></a>위치

Azure Managed Disks를 사용할 수 있는 위치를 선택합니다. 프리미엄 Managed Disks를 마이그레이션하는 경우에도 마이그레이션하려고 계획한 지역에서 Premium Storage를 사용할 수 있는지 확인합니다. 사용 가능한 위치에 대한 최신 정보는 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요.

### <a name="vm-sizes"></a>VM 크기

프리미엄 Managed Disks를 마이그레이션하는 경우 VM 크기를 VM이 위치한 지역에서 제공하는 Premium Storage 지원 가능 크기로 업데이트해야 합니다. Premium Storage를 사용할 수 있는 VM 크기를 검토합니다. Azure VM 크기 사양은 [가상 머신의 크기](sizes.md)에 나열되어 있습니다.
Premium Storage와 작동하는 가상 머신의 성능 특징을 검토하고 워크로드에 가장 적합한 VM 크기를 선택합니다. VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다.

### <a name="disk-sizes"></a>디스크 크기

**프리미엄 Managed Disks**

VM에서 사용할 수 있는 프리미엄 관리 디스크에는 7가지 형식이 있으며 각 형식에는 특정 IOP 및 처리량 한도가 있습니다. 용량, 성능, 확장성 및 최대 로드 측면에서 응용 프로그램의 필요에 따라 VM에 대한 프리미엄 디스크 유형을 선택할 때 이 제한을 고려합니다.

| 프리미엄 디스크 유형  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 디스크 크기           | 128GB| 512 GB| 128GB| 512 GB            | 1,024GB(1TB)    | 2,048GB(2TB)    | 4,095GB(4TB)    | 
| 디스크당 IOPS       | 120   | 240   | 500   | 2,300              | 5,000              | 7,500              | 7,500              | 
| 디스크당 처리량 | 초당 25MB  | 초당 50MB  | 초당 100MB | 초당 150MB | 초당 200MB | 초당 250MB | 초당 250MB | 

**표준 Managed Disks**

VM에서 사용할 수 있는 표준 관리 디스크에는 7가지 형식이 있습니다. 각각은 용량이 다르지만 동일한 IOPS 및 처리량이 제한됩니다. 응용 프로그램의 용량 요구 사항에 따라 표준 Managed Disks의 종류를 선택합니다.

| 표준 디스크 유형  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| 디스크 크기           | 30GB            | 64GB            | 128GB           | 512 GB           | 1,024GB(1TB)   | 2,048GB(2TB)    | 4,095GB(4TB)   | 
| 디스크당 IOPS       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| 디스크당 처리량 | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 60 MB per second | 


### <a name="disk-caching-policy"></a>디스크 캐싱 정책 

**프리미엄 Managed Disks**

기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 *읽기 / 쓰기* 및 모든 프리미엄 데이터 디스크에 대한 *읽기 전용*입니다. 응용 프로그램의 IO에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다.

### <a name="pricing"></a>가격

[Managed Disks에 대한 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 검토합니다. 프리미엄 Managed Disks의 가격 책정은 관리되지 않는 프리미엄 디스크와 같습니다. 하지만 표준 Managed Disks의 가격 책정은 관리되지 않는 표준 디스크와 다릅니다.


## <a name="checklist"></a>검사 목록

1.  프리미엄 Managed Disks를 마이그레이션하는 경우 마이그레이션하는 지역에서 사용할 수 있는지 확인합니다.

2.  사용할 새 VM 시리즈를 결정합니다. 프리미엄 Managed Disks로 마이그레이션하는 경우 Premium Storage를 사용할 수 있어야 합니다.

3.  마이그레이션하는 지역에서 사용할 수 있는 정확한 VM 크기를 결정합니다. VM 크기는 현재 포함하고 있는 데이터 디스크 수를 지원할 만큼 충분히 커야 합니다. 예를 들어, 4개의 데이터 디스크가 있는 경우 VM에는 두 개 이상의 코어가 있어야 합니다. 또한 처리 능력, 메모리 및 네트워크 대역폭 요구 사항을 고려합니다.

4.  디스크 및 해당 VHD Blob의 목록을 포함하여 도움이 될 현재 VM 세부 정보를 포함합니다.

응용 프로그램의 가동 중지 시간을 준비합니다. 원활한 마이그레이션 작업을 수행하려면 현재 시스템에서 모든 처리를 중지해야 합니다. 그런 다음 새 플랫폼으로 마이그레이션할 수 있는 일관된 상태로 가져올 수 있습니다. 가동 중지 시간은 디스크에서 마이그레이션할 데이터 양에 따라 달라집니다.


## <a name="migrate-the-vm"></a>VM 마이그레이션

응용 프로그램의 가동 중지 시간을 준비합니다. 원활한 마이그레이션 작업을 수행하려면 현재 시스템에서 모든 처리를 중지해야 합니다. 그런 다음 새 플랫폼으로 마이그레이션할 수 있는 일관된 상태로 가져올 수 있습니다. 가동 중지 시간은 디스크에서 마이그레이션할 데이터 양에 따라 달라집니다.

이 파트에는 Azure PowerShell 모듈 버전 6.0.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 `Connect-AzureRmAccount`을 실행하여 Azure와 연결해야 합니다.


1.  먼저, 공통 매개 변수를 설정합니다.

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  클래식 VM에서 VHD를 사용하여 관리되는 OS 디스크를 만듭니다.

    $osVhdUri 매개 변수에 OS VHD의 전체 URI를 제공했는지 확인합니다. 또한 마이그레이션하는 디스크의 종류(프리미엄 또는 표준)에 따라 **-AccountType**을 **Premium_LRS** 또는 **Standard_LRS**로 입력합니다.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType Premium_LRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  새 VM에 OS 디스크를 연결합니다.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType Premium_LRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  데이터 VHD 파일에서 관리되는 데이터 디스크를 만들고 새 VM에 추가합니다.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType Premium_LRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  공용 IP, Virtual Network 및 NIC를 설정하여 새 VM을 만듭니다.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>이 가이드에서 다루지 않은 응용 프로그램을 지원하기 위해 추가 단계가 필요할 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

- 가상 머신에 연결합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그온하는 방법](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

