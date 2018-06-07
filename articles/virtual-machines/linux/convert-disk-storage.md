---
title: "Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환 | Microsoft Docs"
description: "Azure CLI를 사용하여 Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환

Managed Disks는 [프리미엄](../windows/premium-storage.md)(SSD 기반) 및 [표준](../windows/standard-storage.md)(HDD 기반)이라는 두 가지 저장소 옵션을 제공합니다. 성능 요구 사항에 따라 최소한의 가동 중지 시간으로 두 가지 옵션 사이를 쉽게 전환할 수 있습니다. 이 기능은 관리되지 않는 디스크에 사용할 수 없습니다. 하지만 두 옵션 사이를 쉽게 전환하도록 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)할 수 있습니다.

이 문서는 Azure CLI를 사용하여 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법을 설명합니다. CLI를 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli.md)를 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에

* 변환은 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약하도록 VM의 재시작이 필요합니다. 
* 관리되지 않는 디스크를 사용하는 경우 이 문서를 사용하여 두 가지 저장소 옵션 사이로 전환하려면 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)해야 합니다. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM의 모든 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

다음 예제에서는 VM의 모든 디스크를 표준에서 프리미엄 저장소로 전환하는 방법을 보여 줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 프리미엄 저장소를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합할 수도 있습니다. 더 나은 성능을 요구하는 디스크만 프리미엄 저장소로 업그레이드하여 이를 수행할 수 있습니다. 다음 예제에서는 VM의 단일 디스크를 표준에서 프리미엄 저장소로, 또 그 반대로 전환하는 방법을 보여 줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 프리미엄 저장소를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.

