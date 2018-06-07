---
title: 자습서 - Azure PowerShell을 사용하여 Azure 디스크 관리 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell은 사용하여 가상 머신을 위한 Azure 디스크를 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ea9d89b7dd94c38b326b83ff1fbf51595d67599a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>자습서- Azure PowerShell을 사용하여 Azure 디스크 관리

Azure 가상 머신은 디스크를 사용하여 VM 운영 체제, 응용 프로그램 및 데이터를 저장합니다. VM을 만들 때 예상되는 워크로드에 적합한 디스크 크기와 구성을 선택하는 것이 중요합니다. 이 자습서에서는 VM 디스크의 배포 및 관리에 대해 다룹니다. 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="default-azure-disks"></a>기본 Azure 디스크

Azure Virtual Machine을 만들면 두 개의 디스크가 자동으로 가상 머신에 연결됩니다. 

**운영 체제 디스크** - 운영 체제 디스크는 최대 4TB까지 가능하며 VM 운영 체제를 호스트합니다.  OS 디스크는 기본적으로 *c:* 드라이브 문자가 할당됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. OS 디스크는 응용 프로그램 또는 데이터를 호스트해서는 **안 됩니다**. 응용 프로그램 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 문서의 뒷부분에서 자세히 설명합니다.

**임시 디스크** - 임시 디스크는 VM과 같은 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 임시 디스크는 성능이 높고 임시 데이터 처리 등의 작업에 사용할 수 있습니다. 그러나 VM이 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터는 제거됩니다. 임시 디스크의 크기는 VM 크기에 따라 결정됩니다. 임시 디스크는 기본적으로 *d:* 드라이브 문자가 할당됩니다.

### <a name="temporary-disk-sizes"></a>임시 디스크 크기

| type | 일반적인 크기 | 최대 임시 디스크 크기(GiB) |
|----|----|----|
| [범용](sizes-general.md) | A, B 및 D 시리즈 | 1600 |
| [Compute에 최적화](sizes-compute.md) | F 시리즈 | 576 |
| [메모리에 최적화](sizes-memory.md) | D, E, G 및 M 시리즈 | 6144 |
| [Storage에 최적화](sizes-storage.md) | L 시리즈 | 5630 |
| [GPU](sizes-gpu.md) | N 시리즈 | 1,440 |
| [고성능](sizes-hpc.md) | A 및 H 시리즈 | 2000 |

## <a name="azure-data-disks"></a>Azure 데이터 디스크

응용 프로그램을 설치하고 데이터를 저장하기 위해 데이터 디스크를 더 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 저장소가 필요한 상황에 사용해야 합니다. 각 데이터 디스크의 최대 용량은 4TB입니다. 가상 컴퓨터의 크기에 따라 VM에 연결할 수 있는 데이터 디스크 수가 결정됩니다. 각 VM vCPU에 대해 두 개의 데이터 디스크를 연결할 수 있습니다. 

### <a name="max-data-disks-per-vm"></a>VM당 최대 데이터 디스크 수

| type | 일반적인 크기 | VM당 최대 데이터 디스크 수 |
|----|----|----|
| [범용](sizes-general.md) | A, B 및 D 시리즈 | 64 |
| [Compute에 최적화](sizes-compute.md) | F 시리즈 | 64 |
| [메모리에 최적화](sizes-memory.md) | D, E, G 및 M 시리즈 | 64 |
| [Storage에 최적화](sizes-storage.md) | L 시리즈 | 64 |
| [GPU](sizes-gpu.md) | N 시리즈 | 64 |
| [고성능](sizes-hpc.md) | A 및 H 시리즈 | 64 |

## <a name="vm-disk-types"></a>VM 디스크 유형

Azure는 두 가지 유형의 디스크를 제공합니다.

### <a name="standard-disk"></a>표준 디스크

Standard Storage는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 저장소를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

### <a name="premium-disk"></a>프리미엄 디스크

프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM을 지원합니다. 프리미엄 디스크는 다섯 가지 유형(P10, P20, P30, P40, P50)으로 제공되며 디스크 크기에 따라 디스크 유형이 결정됩니다. 디스크 유형 선택 시 디스크 크기 값은 다음 유형으로 반올림됩니다. 예를 들어 디스크 크기가 128GB 미만인 경우 디스크 유형은 P10이고, 129-512GB인 경우 P20입니다.

### <a name="premium-disk-performance"></a>프리미엄 디스크 성능

|Premium Storage 디스크 유형 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 디스크 크기(반올림) | 32GB | 64GB | 128GB | 512 GB | 1,024GB(1TB) | 2,048GB(2TB) | 4,095GB(4TB) |
| 디스크당 최대 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
디스크당 처리량 | 25MB/초 | 50MB/초 | 100MB/초 | 150MB/초 | 200MB/s | 250MB/초 | 250MB/초 |

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 64 데이터 디스크는 Standard_GS5 VM에 연결할 수 있습니다. 이러한 각 디스크는 P30에 해당하는 크기이며 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [VM 유형 및 크기](./sizes.md)를 참조하세요.

## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 다음 명령을 사용하여 가상 머신을 만듭니다.

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 가상 머신의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

`-AsJob` 매개 변수는 VM을 백그라운드 작업으로 만들므로 PowerShell 프롬프트가 반환됩니다. `Job` cmdlet을 사용하여 백그라운드 작업의 세부 정보를 볼 수 있습니다.

[New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig)를 사용하여 초기 구성을 만듭니다. 다음 예제에서는 크기가 128GB인 디스크를 구성합니다.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

[New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) 명령을 사용하여 데이터 디스크를 만듭니다.

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 명령을 사용하여 데이터 디스크를 추가할 가상 컴퓨터를 가져옵니다.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

[Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) 명령을 사용하여 데이터 디스크를 가상 컴퓨터 구성에 추가합니다.

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

[Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) 명령을 사용하여 가상 머신을 업데이트합니다.

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>데이터 디스크 준비

디스크가 가상 머신에 연결된 후 디스크를 사용하도록 운영 체제를 구성해야 합니다. 다음 예제에서는 VM에 추가된 첫 번째 디스크를 수동으로 구성하는 방법을 보여 줍니다. [사용자 지정 스크립트 확장](./tutorial-automate-vm-deployment.md)을 사용하여 이 프로세스를 자동화할 수도 있습니다.

### <a name="manual-configuration"></a>수동 구성

가상 컴퓨터와 RDP 연결 만들기 PowerShell을 열고 이 스크립트를 실행합니다.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 VM 디스크 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

VM 구성 자동화에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM 구성 자동화](./tutorial-automate-vm-deployment.md)
