---
title: Azure CLI 1.0에서 SMB를 사용하여 Linux VM에 Azure File Storage 탑재 | Microsoft Docs
description: SMB를 사용하여 Linux VM에 Azure File Storage를 탑재하는 방법
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Azure CLI 1.0에서 SMB를 사용하여 Linux VM에 Azure File Storage 탑재

이 문서에서는 SMB(서버 메시지 블록) 프로토콜을 사용하여 Linux VM에서 Azure File Storage를 탑재하는 방법을 보여 줍니다. File Storage는 표준 SMB 프로토콜을 통해 클라우드에서 파일 공유를 제공합니다. 요구 사항은 다음과 같습니다.

* [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* [SSH(Secure Shell) 공용 및 개인 키 파일](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>사용할 CLI 버전
다음 CLI(명령줄 인터페이스) 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="quick-commands"></a>빠른 명령
태스크를 신속하게 수행하려면 이 섹션의 단계를 수행합니다. 자세한 정보 및 컨텍스트는 ["자세한 연습"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) 섹션을 시작하세요.

### <a name="prerequisites"></a>필수 조건
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
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>다시 부팅한 후 탑재 유지
다음 줄을 `/etc/fstab`에 추가합니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>자세한 연습

File Storage는 표준 SMB 프로토콜을 사용하는 클라우드에서 파일 공유를 제공합니다. File Storage의 최신 릴리스를 사용하면 SMB 3.0을 지원하는 OS에서 파일 공유를 탑재할 수도 있습니다. Linux에서 SMB 탑재를 사용하면 SLA에서 지원되는 강력한 영구 보관 저장소 위치에 쉽게 백업할 수 있습니다.

VM에서 File Storage에서 호스팅되는 SMB 탑재로 파일을 이동하는 것은 로그를 디버깅하는 유용한 방법입니다. 동일한 SMB 공유를 Mac, Linux 또는 Windows 워크스테이션에 로컬로 탑재하기 때문입니다. SMB 프로토콜이 이러한 과도한 로깅 작업을 다루도록 빌드되지 않았기 때문에 SMB는 Linux 또는 응용 프로그램 로그를 실시간으로 스트리밍하기 위한 최선의 솔루션은 아닐 수 있습니다. Linux 및 응용 프로그램 로그 출력을 수집하려는 경우 Fluentd와 같은 전용 통합 로깅 계층 도구가 SMB보다 더 적합할 것입니다.

이 자세한 연습 과정에서는 먼저 File Storage 공유를 만드는 데 필요한 필수 구성 요소 만든 다음 Linux VM에서 SMB를 통해 탑재합니다.

1. 다음 코드를 사용하여 Azure Storage 계정을 만듭니다.

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. 저장소 계정 키를 표시합니다.

    저장소 계정을 만드는 경우 서비스 중단 없이 키를 순환할 수 있도록 저장소 계정 키는 쌍으로 만들어집니다. 쌍에서 두 번째 키로 전환하는 경우 새 키 쌍이 만들어집니다. 새 저장소 계정 키는 항상 쌍으로 만들어지므로 항상 사용하지 않은 저장소 키를 전환할 준비가 되어 있습니다. 저장소 계정 키를 표시하려면 다음 코드를 사용합니다.

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. File Storage 공유를 만듭니다.

    File Storage 공유는 SMB 공유를 포함합니다. 할당량은 항상 GB(기가바이트) 단위로 표현됩니다. File Storage 공유를 만들려면 다음 코드를 사용합니다.

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. 탑재 지점 디렉터리를 만듭니다.

    Linux 파일 시스템에서 로컬 디렉터리를 만들어서 SMB 공유를 탑재해야 합니다. 로컬 탑재 디렉터리에서 쓰거나 읽은 모든 내용은 File Storage에 호스팅된 SMB 공유로 전달됩니다. 디렉터리를 만들려면 다음 코드를 사용합니다.

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. 다음 코드를 사용하여 SMB 공유를 탑재합니다.

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. 재부팅을 통해 SMB 탑재를 유지합니다.

    Linux VM를 다시 부팅하면 탑재된 SMB 공유가 종료하는 동안 분리됩니다. 부팅 시 SMB 공유를 다시 탑재하려면 Linux /etc/fstab에 줄을 추가해야 합니다. Linux는 부팅 프로세스 중에 탑재해야 하는 파일 시스템을 나열하기 위해 /etc/fstab 파일을 사용합니다. SMB 공유를 추가하면 File Storage 공유가 Linux VM용으로 영구히 탑재된 파일 시스템이 됩니다. cloud-init를 사용하는 경우 File Storage SMB 공유를 새 VM에 추가할 수 있습니다.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>다음 단계

- [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM에 디스크 추가](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
