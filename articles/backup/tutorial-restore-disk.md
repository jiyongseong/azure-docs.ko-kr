---
title: Azure Backup으로 VM 디스크 복원 | Microsoft Docs
description: Backup 및 Recovery Services를 사용하여 Azure에서 디스크를 복원하고 복구된 VM을 만드는 방법을 알아봅니다.
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f2b82725362517d12dd4e7df7b2bb083fa107253
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Azure에서 디스크 복원 및 복구된 VM 만들기
Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 개별 파일을 복원할 수 있습니다. 이 문서에서는 CLI를 사용하여 전체 VM을 복원하는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복구 지점 나열 및 선택
> * 복구 지점에서 디스크 복원
> * 복원된 디스크에서 VM 만들기

PowerShell을 사용하여 디스크를 복원하고 복구된 VM을 만드는 방법은 [PowerShell을 사용하여 Azure VM 백업 및 복원](backup-azure-vms-automation.md#restore-an-azure-vm)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.18 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


## <a name="prerequisites"></a>필수 조건
이 자습서에서는 Azure Backup으로 보호된 Linux VM이 필요합니다. 실수로 인한 VM 삭제 및 복구 프로세스를 시뮬레이션하려면 복구 지점의 디스크에서 VM을 만듭니다. Azure Backup으로 보호된 Linux VM이 필요한 경우 [CLI를 사용하여 Azure에서 가상 머신 백업](quick-backup-vm-cli.md)을 참조하세요.


## <a name="backup-overview"></a>Backup 개요
Azure에서 백업을 시작하면 VM에 대한 백업 확장에서 특정 시점 스냅숏을 만듭니다. 첫 번째 백업이 요청될 때 백업 확장이 VM에 설치됩니다. 또한 백업이 수행될 때 VM이 실행되고 있지 않으면 Azure Backup에서 기본 저장소의 스냅숏을 만들 수도 있습니다.

기본적으로 Azure Backup은 파일 시스템 일치 백업을 만듭니다. Azure Backup에서 스냅숏을 만들면 데이터가 Recovery Services 자격 증명 모음으로 전송됩니다. 효율성을 극대화하기 위해 Azure Backup은 이전 백업 이후에 변경된 데이터 블록만 식별하여 전송합니다.

데이터 전송이 완료되면 스냅숏이 제거되고 복구 지점이 생성됩니다.


## <a name="list-available-recovery-points"></a>사용 가능한 복구 지점 나열
디스크를 복원하려면 복구 지점을 복구 데이터에 대한 원본으로 선택합니다. 기본 정책은 매일 복구 지점을 만들고 이를 30일 동안 유지하므로 복구 시점을 특정 시점으로 선택할 수 있는 복구 지점 집합을 유지할 수 있습니다. 

사용 가능한 복구 지점 목록을 보려면 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list)를 사용합니다. 복구 지점 **이름**은 디스크를 복구하는 데 사용됩니다. 이 자습서에서는 사용 가능한 최근 복구 지점을 사용하겠습니다. `--query [0].name` 매개 변수는 다음과 같이 최근 복구 지점 이름을 선택합니다.

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>VM 디스크 복원
복구 지점에서 디스크를 복원하려면 먼저 Azure 저장소 계정을 만듭니다. 이 저장소 계정은 복원된 디스크를 저장하는 데 사용됩니다. 추가 단계에서 복원된 디스크를 사용하여 VM을 만듭니다.

1. [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create)를 사용하여 저장소 계정을 만듭니다. 저장소 계정 이름은 모두 소문자여야 하며 전역적으로 고유해야 합니다. *mystorageaccount*를 사용자 고유의 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks)를 사용하여 복구 지점에서 디스크를 복원합니다. *mystorageaccount*를 이전 명령에서 만든 저장소 계정의 이름으로 바꿉니다. *myRecoveryPointName*을 이전 [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) 명령의 출력에서 얻은 복구 지점 이름으로 바꿉니다.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>복원 작업 모니터링
복원 작업의 상태를 모니터링하려면 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list)를 사용합니다.

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

출력은 다음 예제와 비슷합니다. 여기서는 복원 작업 상태가 *진행 중*이라고 표시됩니다.

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

복원 작업의 *상태*가 *완료됨*을 보고되면 디스크가 저장소 계정으로 복원되었습니다.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>복원된 디스크를 관리 디스크로 변환
복원 작업은 관리되지 않는 디스크를 만듭니다. 디스크에서 VM을 만들려면 먼저 관리 디스크로 변환해야 합니다.

1. [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string)을 사용하여 저장소 계정에 대한 연결 정보를 얻습니다. *mystorageaccount*를 다음과 같이 저장소 계정의 이름으로 바꿉니다.
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. 관리되지 않는 디스크는 저장소 계정에서 보호됩니다. 다음 명령은 관리되지 않는 디스크에 대한 정보를 가져와서 관리 디스크를 만드는 다음 단계에서 사용되는 *uri*라는 변수를 만듭니다.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. 이제 [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create)를 사용하여 복구된 디스크에서 관리 디스크를 만들 수 있습니다. 이전 단계의 *uri* 변수가 관리 디스크의 원본으로 사용됩니다.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. 이제 복원된 디스크에서 관리 디스크를 얻었으므로 [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete)를 사용하여 관리되지 않는 디스크와 저장소 계정을 정리합니다. *mystorageaccount*를 다음과 같이 저장소 계정의 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>복원된 디스크에서 VM 만들기
마지막 단계는 관리 디스크에서 VM을 만드는 것입니다.

1. 다음과 같이 [az vm create](/cli/azure/vm?view=azure-cli-latest#az_vm_create)를 사용하여 관리 디스크에서 VM을 만듭니다.

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. VM이 복구된 디스크에서 만들어졌는지 확인하려면 다음과 같이 [az vm list](/cli/azure/vm?view=azure-cli-latest#az_vm_list)를 사용하여 리소스 그룹에 VM을 나열합니다.

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 복구 지점에서 디스크를 복원한 다음 디스크에서 VM을 만들었습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 복구 지점 나열 및 선택
> * 복구 지점에서 디스크 복원
> * 복원된 디스크에서 VM 만들기

복구 지점에서 개별 파일을 복원하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [Azure에서 가상 머신에 파일 복원](tutorial-restore-files.md)

