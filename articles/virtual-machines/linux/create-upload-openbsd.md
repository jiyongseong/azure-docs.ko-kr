---
title: OpenBSD VM 이미지 만들기 및 Azure로 업로드 | Microsoft Docs
description: OpenBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하여 Azure CLI를 통해 Azure 가상 컴퓨터를 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 6c0eae36874c6d2738385c4530cc208a0b1362c4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>OpenBSD 디스크 이미지 만들기 및 Azure로 업로드
이 문서에서는 OpenBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다. VHD를 업로드한 후에는 VHD를 사용자 고유의 이미지로 사용하여 Azure CLI를 통해 Azure에서 VM(가상 머신)을 만들 수 있습니다.


## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

* **Azure 구독** - 계정이 없는 경우 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [무료 평가판 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.  
* **Azure CLI 2.0** - 최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치했고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.
* **.vhd 파일에 설치된 OpenBSD 운영 체제** - 가상 하드 디스크에 지원되는 OpenBSD 운영 체제([6.1 버전 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/))를 설치해야 합니다. .vhd 파일을 만드는 도구는 여러 가지가 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. Hyper-V를 설치하고 사용하는 방법에 대한 자세한 내용은 [Hyper-V 설치 및 가상 머신 만들기](http://technet.microsoft.com/library/hh846766.aspx)를 참조하세요.


## <a name="prepare-openbsd-image-for-azure"></a>OpenBSD 이미지를 Azure에 사용하도록 준비
Hyper-V 지원을 추가한, OpenBSD 운영 체제 6.1을 설치한 VM에서 다음 절차를 완료합니다.

1. 설치하는 동안 DHCP가 사용하도록 설정되지 않은 경우 다음과 같이 서비스를 사용하도록 설정합니다.

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. 다음과 같이 직렬 콘솔을 설치합니다.

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. 다음과 같이 패키지 설치를 구성합니다.

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. 기본적으로 `root` 사용자는 Azure의 가상 머신에서는 사용되지 않도록 설정됩니다. 사용자는 OpenBSD VM에서 `doas` 명령을 사용하여 상승된 권한으로 명령을 실행할 수 있습니다. Doas는 기본적으로 사용하도록 설정됩니다. 자세한 내용은 [doas.conf](http://man.openbsd.org/doas.conf.5)를 참조하세요. 

5. 다음과 같이 Azure 에이전트에 대한 필수 구성 요소를 설치하고 구성합니다.

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. 언제든지 최신 버전의 Azure 에이전트를 [Github](https://github.com/Azure/WALinuxAgent/releases)에서 확인할 수 있습니다. 다음과 같이 에이전트를 설치합니다.

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Azure 에이전트를 설치한 후 다음과 같이 에이전트가 실행 중인지 확인하는 것이 좋습니다.
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. 시스템을 프로비전 해제하여 다시 프로비전하기에 적합한 상태로 정리합니다. 다음 명령은 마지막으로 프로비전된 사용자 계정 및 관련 데이터를 삭제합니다.

    ```sh
    waagent -deprovision+user -force
    ```

이제 VM을 종료할 수 있습니다.


## <a name="prepare-the-vhd"></a>VHD 준비
VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD**만 지원됩니다. Hyper-V 관리자 또는 Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet을 사용하여 디스크를 고정 VHD 형식으로 변환할 수 있습니다. 예제는 다음과 같습니다.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>저장소 리소스 만들기 및 업로드
먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

VHD를 업로드하려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create)를 사용하여 저장소 계정을 만듭니다. Storage 계정 이름은 고유해야 하므로 자신만의 이름을 제공하세요. 다음 예제에서는 *mystorageaccount*라는 저장소 계정을 만듭니다.

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

저장소 계정에 대한 액세스를 제어하려면 다음과 같이 [az storage account key list](/cli/azure/storage/account/keys#az_storage_account_keys_list)를 사용하여 저장소 키를 확보합니다.

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

업로드하는 VHD를 논리적으로 분리하려면 [az storage container create](/cli/azure/storage/container#az_storage_container_create)를 사용하여 저장소 계정 내에서 컨테이너를 만듭니다.

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

마지막으로 다음과 같이 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload)를 사용하여 VHD를 업로드합니다.

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>VHD에서 VM 만들기
[샘플 스크립트](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md)를 사용하거나 직접 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만들 수 있습니다. 업로드한 OpenBSD VHD를 지정하려면 다음과 같이 `--image` 매개 변수를 사용합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

다음과 같이 [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses)를 사용하여 OpenBSD VM의 IP 주소를 확인합니다.

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

이제 정상적으로 OpenBSD VM에 SSH할 수 있습니다.
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>다음 단계
OpenBSD6.1의 Hyper-V 지원에 대한 자세한 내용은 [OpenBSD 6.1](https://www.openbsd.org/61.html) 및 [hyperv.4](http://man.openbsd.org/hyperv.4)를 참조하세요.

관리 디스크에서 VM을 만들려면 [az disk](/cli/azure/disk)를 참조하세요. 
