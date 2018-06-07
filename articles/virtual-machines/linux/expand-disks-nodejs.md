---
title: Azure CLI 1.0을 사용하여 Linux VM에서 OS 디스크 확장 | Microsoft Docs
description: Azure CLI 1.0 및 Resource Manager 배포 모델을 사용하여 Linux VM에서 OS(운영 체제) 가상 디스크를 확장하는 방법에 대해 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Azure CLI 1.0에서 Azure CLI를 사용하여 Linux VM에서 OS 디스크 확장
Azure에서 Linux VM(가상 머신)에서 OS(운영 체제)에 대한 기본 가상 하드 디스크 크기는 일반적으로 30GB입니다. [데이터 디스크를 추가](add-disk.md)하여 추가 저장소 공간을 제공하고 OS 디스크를 확장할 수 있습니다. 이 문서에서는 Azure CLI 1.0에서 관리되지 않는 디스크를 사용하여 Linux VM에 대한 OS 디스크를 확장하는 방법을 설명합니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#prerequisites) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](expand-disks.md) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="prerequisites"></a>필수 조건
[최신 Azure CLI 1.0](../../cli-install-nodejs.md)이 설치되어 있고 다음과 같이 Resource Manager 모드를 사용하여 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)에 로그인되어 있어야 합니다.

```azurecli
azure config mode arm
```

다음 샘플에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup* 및 *myVM*이 포함됩니다.

## <a name="expand-os-disk"></a>OS 디스크 확장

1. VM 실행 중에 가상 하드 디스크에 대한 작업을 수행할 수 없습니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM* VM을 중지하고 할당을 취소합니다.

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop`은 계산 리소스를 릴리스하지 않습니다. 계산 리소스를 릴리스하려면 `azure vm deallocate`을 사용합니다. VM 할당을 취소하여 가상 하드 디스크를 확장해야 합니다.

2. `azure vm set` 명령을 사용하여 OS 관리되지 않는 디스크 크기를 업데이트합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM* VM을 *50*GB가 되도록 업데이트합니다.

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. 다음과 같이 VM을 시작합니다.

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. 적절한 자격 증명을 사용하여 VM에 SSH합니다. OS 디스크 크기를 조정했는지 확인하려면 `df -h`을 사용합니다. 다음 예제 출력에서는 주 파티션(*/dev/sda1*)이 50GB임을 보여 줍니다.

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>다음 단계
또한 추가 저장소가 필요한 경우 [Linux VM에 데이터 디스크를 추가](add-disk.md)합니다. 디스크 암호화에 대한 자세한 내용은 [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md)를 참조하세요.
