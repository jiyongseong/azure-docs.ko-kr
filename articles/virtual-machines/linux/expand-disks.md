---
title: Azure의 Linux VM에서 가상 하드 디스크 확장 | Microsoft Docs
description: Azure CLI 2.0을 사용하여 Linux VM에서 가상 하드 디스크를 확장하는 방법 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: c3dcd2f9c71b64ac48c2c7b6f51d2694d649c15e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크를 확장하는 방법
Azure에서 Linux VM(가상 머신)에서 OS(운영 체제)에 대한 기본 가상 하드 디스크 크기는 일반적으로 30GB입니다. [데이터 디스크를 추가](add-disk.md)하여 추가 저장소 공간을 제공할 수 있으나, 기존 데이터 디스크를 확장할 수도 있습니다. 이 문서에서는 Azure CLI 2.0을 사용하여 Linux VM에서 Managed Disks를 확장하는 방법을 설명합니다. [Azure CLI 1.0](expand-disks-nodejs.md)을 사용하여 관리되지 않는 OS 디스크를 확장할 수도 있습니다.

> [!WARNING]
> 항상 디스크 크기 조정 작업을 수행하기 전에 데이터를 백업해야 합니다. 자세한 내용은 [Azure에서 Linux VM 백업](tutorial-backup-vms.md)을 참조하세요.

## <a name="expand-azure-managed-disk"></a>Azure Managed Disks 확장
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치했고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

이 문서는 Azure에 데이터 디스크가 하나 이상 연결되고 준비된 기존 VM이 필요합니다. 사용할 수 있는 VM이 아직 없는 경우 [데이터 디스크가 있는 VM 만들기 및 준비](tutorial-manage-disks.md#create-and-attach-disks)를 참조하세요.

다음 샘플에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup* 및 *myVM*이 포함됩니다.

1. VM 실행 중에 가상 하드 디스크에 대한 작업을 수행할 수 없습니다. [az vm deallocate](/cli/azure/vm#az_vm_deallocate)를 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > VM 할당을 취소하여 가상 하드 디스크를 확장해야 합니다. `az vm stop`은 계산 리소스를 릴리스하지 않습니다. 계산 리소스를 릴리스하려면 `az vm deallocate`을 사용합니다.

2. 이제 [az disk list](/cli/azure/disk#az_disk_list)를 사용하여 리소스 그룹에서 Managed Disks 목록을 봅니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*의 Managed Disks 목록을 표시합니다.

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    [az disk update](/cli/azure/disk#az_disk_update)를 사용하여 필요한 디스크를 확장합니다. 다음 예제에서는 *myDataDisk*이라는 Managed Disk를 *200*GB 크기로 확장합니다.

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Managed Disk를 확장하면 업데이트된 크기는 가장 가까운 Managed Disk 크기에 매핑됩니다. 사용 가능한 Managed Disk 크기 및 계층의 테이블은 [Azure Managed Disks 개요 - 가격 책정 및 청구](../windows/managed-disks-overview.md#pricing-and-billing)를 참조하세요.

3. [az vm start](/cli/azure/vm#az_vm_start)를 사용하여 VM을 시작합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 시작합니다.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>디스크 파티션 및 파일 시스템을 확장합니다.
확장된 디스크를 사용하려면 기본 파티션 및 파일 시스템을 확장해야 합니다.

1. 적절한 자격 증명을 사용하여 VM에 SSH합니다. [az vm show](/cli/azure/vm#az_vm_show)를 사용하여 VM의 공용 IP 주소를 가져올 수 있습니다.

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. 확장된 디스크를 사용하려면 기본 파티션 및 파일 시스템을 확장해야 합니다.

    a. 이미 탑재되어 있는 경우 디스크를 분리합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

    나. `parted`를 사용하여 디스크 정보를 보고 파티션 크기를 조정합니다.

    ```bash
    sudo parted /dev/sdc
    ```

    `print`를 사용하여 기존 파티션 레이아웃에 대한 정보를 확인합니다. 출력은 기본 디스크 크기가 215Gb임을 보여 주는 다음 예제와 비슷합니다.

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    다. `resizepart`를 사용하여 파티션을 확장합니다. 파티션 수 *1*과 새 파티션에 대한 크기를 입력합니다.

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. 끝내려면 `quit`를 입력합니다.

3. 파티션 크기를 조정했으면 `e2fsck`를 사용하여 파티션 일관성을 확인합니다.

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. 이제 파일 시스템 크기를 `resize2fs`로 조정합니다.

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. `/datadrive`와 같이 파티션을 원하는 위치로 탑재합니다.

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. OS 디스크 크기를 조정했는지 확인하려면 `df -h`을 사용합니다. 다음 예제 출력에서는 데이터 드라이브(*/dev/sdc1*)가 200GB임을 보여 줍니다.

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>다음 단계
또한 추가 저장소가 필요한 경우 [Linux VM에 데이터 디스크를 추가](add-disk.md)합니다. 디스크 암호화에 대한 자세한 내용은 [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md)를 참조하세요.
