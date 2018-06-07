---
title: 비관리 디스크에서 관리 디스크로 Windows 가상 컴퓨터 변환 - Azure Managed Disks | Microsoft Docs
description: Resource Manager 배포 모델에서 PowerShell을 사용하여 비관리 디스크에서 관리 디스크로 Windows VM을 변환하는 방법
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
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 92168ba5605e119d42ba40ee694cebb3ad116041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>비관리 디스크에서 관리 디스크로 Windows 가상 컴퓨터 변환

비관리 디스크를 사용하는 기존 Windows VM(가상 머신)이 있는 경우 [Azure Managed Disks](managed-disks-overview.md) 서비스를 통해 관리 디스크를 사용하도록 VM을 변환할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

이 문서에서는 Azure PowerShell을 사용하여 VM을 변환하는 방법을 보여 줍니다. 설치 또는 업그레이드가 필요한 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에


* [Managed Disks로 마이그레이션 계획 수립](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토합니다.

* [Managed Disks로의 마이그레이션에 대한 FAQ](faq-for-disks.md#migrate-to-managed-disks)를 검토합니다.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>단일 인스턴스 VM 변환
이 섹션에서는 단일 인스턴스 Azure VM을 비관리 디스크에서 Managed Disks로 변환하는 방법을 설명합니다. VM이 가용성 집합에 있는 경우 다음 섹션을 참조하세요. 

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet을 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다. 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet을 사용하여 VM을 관리 디스크로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하여 이전 VM을 변환하고 Virtual Machine을 시작합니다.

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>가용성 집합의 VM 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

1. [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet을 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 업데이트합니다.

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  가용성 집합이 있는 지역에 관리되는 장애 도메인은 2개뿐이고 관리되지 않는 장애 도메인은 3개인 경우 이 명령은 "지정된 장애 도메인 수 3은 1~2 범위에 있어야 합니다."와 유사한 오류를 표시합니다. 오류를 해결하려면 다음과 같이 장애 도메인을 2로 업데이트하고 `Sku`를 `Aligned`로 업데이트합니다.

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 가용성 집합의 VM을 할당 취소하고 변환합니다. 다음 스크립트는 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet을 사용하여 각 VM의 할당을 취소하고, [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)를 사용하여 변환하고, 변환 프로세스의 일부로 각 VM을 자동으로 다시 시작합니다.

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>문제 해결

변환하는 동안 오류가 발생한 경우 또는 이전 변환에서의 문제로 인해 VM 상태가 실패인 경우 `ConvertTo-AzureRmVMManagedDisk` cmdlet을 다시 실행합니다. 다시 시도만으로 상황이 해결되는 경우가 많습니다.
변환하기 전에 모든 VM 확장이 '프로비전 성공' 상태에 있는지 확인합니다. 그렇지 않으면 변환이 오류 코드 409로 실패합니다.


## <a name="next-steps"></a>다음 단계

[표준 관리 디스크를 프리미엄으로 변환](convert-disk-storage.md)

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.

