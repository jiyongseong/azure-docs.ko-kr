---
title: SMB를 사용하여 Linux VM에 Azure File Storage 탑재 | Microsoft Docs
description: Azure CLI 2.0에서 SMB를 사용하여 Linux VM에 Azure File Storage를 탑재하는 방법
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: iainfou
ms.openlocfilehash: 01e18103f9e94615357ff3b9c4be7f2473763a57
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>SMB를 사용하여 Linux VM에 Azure File Storage 탑재

이 문서에서는 Azure CLI 2.0에서 SMB 탑재를 사용하여 Linux VM에서 Azure File Storage 서비스를 활용하는 방법을 보여 줍니다. Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. [Azure CLI 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다. 요구 사항은 다음과 같습니다.

- [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 공용 및 개인 키 파일](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>빠른 명령

* 리소스 그룹
* Azure Virtual Network
* SSH 인바운드를 사용하는 네트워크 보안 그룹
* 서브넷
* Azure Storage 계정
* Azure Storage 계정 키
* Azure File Storage 공유
* Linux VM

모든 예제를 고유한 설정으로 바꿉니다.

### <a name="create-a-directory-for-the-local-mount"></a>로컬 탑재를 위한 디렉터리 만들기

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>File Storage SMB 공유를 탑재 지점에 탑재

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>다시 부팅한 후 탑재 유지
이렇게 하려면 다음 줄을 `/etc/fstab`에 추가합니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>자세한 연습

File Storage는 표준 SMB 프로토콜을 사용하는 클라우드에서 파일 공유를 제공합니다. File Storage의 최신 릴리스를 사용하면 SMB 3.0을 지원하는 OS에서 파일 공유를 탑재할 수도 있습니다. Linux에서 SMB 탑재를 사용하면 SLA에서 지원되는 강력한 영구 보관 저장소 위치에 쉽게 백업할 수 있습니다.

VM에서 File Storage에서 호스팅되는 SMB 탑재로 파일을 이동하는 것은 로그를 디버깅하는 유용한 방법입니다. 동일한 SMB 공유를 Mac, Linux 또는 Windows 워크스테이션에 로컬로 탑재하기 때문입니다. SMB 프로토콜이 이러한 과도한 로깅 작업을 다루도록 빌드되지 않았기 때문에 SMB는 Linux 또는 응용 프로그램 로그를 실시간으로 스트리밍하기 위한 최선의 솔루션은 아닐 수 있습니다. Linux 및 응용 프로그램 로그 출력을 수집하려는 경우 Fluentd와 같은 전용 통합 로깅 계층 도구가 SMB보다 더 적합할 것입니다.

이 자세한 연습 과정에서는 먼저 File Storage 공유를 만드는 데 필요한 필수 구성 요소 만든 다음 Linux VM에서 SMB를 통해 탑재합니다.

1. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만들어서 파일 공유를 저장합니다.

    "미국 서부" 지역에 `myResourceGroup`이라는 리소스 그룹을 만들려면 다음 예제를 사용합니다.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. 실제 파일을 저장하기 위해 [az storage account create](/cli/azure/storage/account#az_storage_account_create)를 사용하여 Azure Storage 계정을 만듭니다.

    Standard_LRS 저장소 SKU를 사용하여 mystorageaccount라는 저장소 계정을 만들려면 다음 예제를 사용합니다.

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. 저장소 계정 키를 표시합니다.

    저장소 계정을 만드는 경우 서비스 중단 없이 키를 순환할 수 있도록 저장소 계정 키는 쌍으로 만들어집니다. 쌍에서 두 번째 키로 전환하는 경우 새 키 쌍이 만들어집니다. 새 저장소 계정 키는 항상 쌍으로 만들어지므로 항상 사용하지 않은 저장소 계정 키를 전환할 준비가 되어 있습니다.

    [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list)를 사용하여 저장소 계정 키를 봅니다. `mystorageaccount`라는 저장소 계정 키를 다음 예제에서 나열합니다.

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    단일 키를 추출하려면 `--query` 플래그를 사용합니다. 다음 예제에서는 첫 번째 키(`[0]`)를 추출합니다.

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. File Storage 공유를 만듭니다.

    File Storage 공유는 [az storage share create](/cli/azure/storage/share#az_storage_share_create)를 사용하여 SMB 공유를 포함합니다. 할당량은 항상 GB(기가바이트) 단위로 표현됩니다. 앞의 `az storage account keys list` 명령에서 키 중 하나를 전달합니다. 다음 예제를 사용하여 10GB 할당량을 가진 mystorageshare라는 공유를 만듭니다.

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. 탑재 지점 디렉터리를 만듭니다.

    Linux 파일 시스템에서 로컬 디렉터리를 만들어서 SMB 공유를 탑재합니다. 로컬 탑재 디렉터리에서 쓰거나 읽은 모든 내용은 File Storage에 호스팅된 SMB 공유로 전달됩니다. /mnt/mymountdirectory에서 로컬 디렉터리를 만들려면 다음 예제를 사용합니다.

    ```bash
    sudo mkdir -p /mnt/mymountpoint
    ```

6. 로컬 디렉터리에 SMB 공유를 탑재합니다.

    다음과 같이 고유한 저장소 계정 사용자 이름 및 탑재 자격 증명에 대한 저장소 계정 키를 제공합니다.

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountpoint -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. 재부팅을 통해 SMB 탑재를 유지합니다.

    Linux VM를 다시 부팅하면 탑재된 SMB 공유가 종료하는 동안 분리됩니다. 부팅 시 SMB 공유를 다시 탑재하려면 Linux /etc/fstab에 줄을 추가합니다. Linux는 부팅 프로세스 중에 탑재해야 하는 파일 시스템을 나열하기 위해 /etc/fstab 파일을 사용합니다. SMB 공유를 추가하면 File Storage 공유가 Linux VM용으로 영구히 탑재된 파일 시스템이 됩니다. cloud-init를 사용하는 경우 File Storage SMB 공유를 새 VM에 추가할 수 있습니다.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>다음 단계

- [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](using-cloud-init.md)
- [Linux VM에 디스크 추가](add-disk.md)
- [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md)
