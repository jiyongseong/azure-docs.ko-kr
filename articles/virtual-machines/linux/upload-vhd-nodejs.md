---
title: Azure CLI 1.0으로 사용자 지정 Linux 이미지 업로드 | Microsoft Docs
description: Resource Manager 배포 모델 및 Azure CLI 1.0을 사용하여 사용자 지정 Linux 이미지로 Azure에 VHD(가상 하드 디스크)를 만들고 업로드합니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 사용자 지정 디스크 이미지에서 Linux VM 업로드 및 만들기
이 문서에서는 Resource Manager 배포 모델을 사용하여 VHD(가상 하드 디스크)를 Azure에 업로드하고 이 사용자 지정 이미지에서 Linux VM을 만드는 방법을 설명합니다. 이 기능을 사용하면 Linux 배포판을 요구에 맞게 설치 및 구성하고 해당 VHD를 사용하여 Azure 가상 머신 (Vm)를 신속하게 만들 수 있습니다.


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 Azure에 VM을 업로드하는 기본 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#requirements) 문서 끝까지 참조하세요.

[Azure CLI 1.0](../../cli-install-nodejs.md)에 로그인하여 Resource Manager 모드를 사용하는지 확인합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myimages`가 포함됩니다.

먼저 리소스 그룹을 만듭니다. 다음 예제에서는 `WestUs` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
azure group create myResourceGroup --location "WestUS"
```

가상 디스크를 보유할 저장소 계정을 만듭니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정을 만듭니다.

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

저장소 계정의 선택키를 나열합니다. `key1`을 기록해 둡니다.

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

획득한 저장소 키를 사용하여 저장소 계정 내에 컨테이너를 만듭니다. 다음 예제에서는 `key1`의 저장소 키 값을 사용하여 `myimages`라는 컨테이너를 만듭니다.

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

마지막으로, 만든 컨테이너에 VHD를 업로드합니다. `/path/to/disk/mydisk.vhd` 아래에 VHD의 로컬 경로를 지정합니다.

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

이제 [Resource Manager 템플릿을 사용하여](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)업로드된 가상 디스크에서 VM을 만들 수 있습니다. 또한 다음과 같이 디스크에 대한 URI(`--image-urn`)를 지정하여 CLI를 사용할 수도 있습니다. 다음 예제에서는 이전에 업로드한 가상 디스크를 사용하여 `myVM`이라는 VM을 만듭니다.

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

대상 저장소 계정은 가상 디스크를 업로드한 곳과 같아야 합니다. 또한 가상 네트워크, 공용 IP 주소, 사용자 이름 및 SSH 키와 같은 `azure vm create` 명령이 필요로 하는 모든 추가 매개 변수를 지정하거나 프롬프트에 응답할 수 있습니다. [사용 가능한 CLI Resource Manager 매개 변수](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)에 대해 자세히 알아볼 수 있습니다.

## <a name="requirements"></a>요구 사항
다음 단계를 완료하려면 다음이 필요합니다.

* **.vhd 파일에 설치된 Linux 운영 체제** - 가상 디스크에 VHD 형식으로 [Azure 보증 Linux 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(또는 [보증되지 않는 배포에 대한 정보](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조)을 설치합니다. VM과 VHD를 만드는 도구는 여러 가지가 있습니다.
  * [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 또는 [KVM](http://www.linux-kvm.org/page/RunningKVM)을 설치 및 구성하고 VHD를 이미지 형식으로 사용하도록 주의합니다. 필요한 경우 `qemu-img convert`를 사용하여 [이미지를 변환](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)할 수 있습니다.
  * 또한 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 또는 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)에서 Hyper-V를 사용할 수 있습니다.

> [!NOTE]
> 새 VHDX 형식은 Azure에서 지원되지 않습니다. VM을 만들 때 VHD를 형식으로 지정합니다. 필요하다면 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 또는 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet을 사용하여 VHDX 디스크를 VHD로 변환할 수 있습니다. 그뿐 아니라 Azure는 동적 VHD 업로드를 지원하지 않으므로 업로드하기 전에 이러한 디스크를 정적 VHD로 변환해야 합니다. Azure로 업로딩하는 과정 중에 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go) 와 같은 도구를 사용하여 동적 디스크를 변환할 수 있습니다.
> 
> 

* 사용자 지정 이미지에서 생성된 VM은 이미지 자체와 동일한 저장소 계정에 있어야 합니다.
  * 사용자 지정 이미지와 생성된 VM을 모두 저장할 저장소 계정 및 컨테이너 만들기
  * 모든 VM을 만든 후 이미지를 안전하게 삭제할 수 있습니다.

[Azure CLI 1.0](../../cli-install-nodejs.md)에 로그인하여 Resource Manager 모드를 사용하는지 확인합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myimages`가 포함됩니다.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>업로드할 이미지 준비
Azure에서는 다양한 Linux 배포를 지원합니다( [보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다.

* **[CentOS 기반 배포](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 및 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[기타 - 보증되지 않는 배포](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 일반적인 추가 팁은 **[Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)**을 참조하세요.

> [!NOTE]
> [Azure 인증 배포의 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 '지원되는 버전'에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux를 실행하는 VM에 [Azure 플랫폼 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)가 적용됩니다.


## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 가상 네트워킹 및 저장소와 같은 가상 머신을 지원하기 위해 논리적으로 모든 Azure 리소스를 모읍니다. [여기서 Azure 리소스 그룹](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아 봅니다. 사용자 지정 디스크 이미지를 업로드하고 VM을 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 

다음 예제에서는 `WestUS` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
VM은 저장소 계정 내에서 페이지 blob으로 저장됩니다. [여기서 Azure Blob 저장소](../../storage/common/storage-introduction.md#blob-storage)에 대해 자세히 알아 봅니다. 사용자 지정 디스크 이미지 및 VM에 대한 저장소 계정을 만듭니다. 사용자 지정 디스크 이미지에서 만든 모든 VM는 그 이미지와 동일한 저장소 계정에 있어야 합니다.

다음 예제에서는 이전에 만든 리소스 그룹에 `mystorageaccount`라는 저장소 계정을 만듭니다.

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>저장소 계정 키 나열
Azure는 각 저장소 계정에 대해 두 개의 512 비트 선택키를 생성합니다. 이러한 선택키는 쓰기 작업을 수행할 때와 같이 저장소 계정에 인증할 때에 사용됩니다. [여기서 저장소에 대한 액세스 관리](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)에 대해 자세히 알아 봅니다. `azure storage account keys list` 명령을 사용하여 선택키를 볼 수 있습니다.

만든 저장소 계정에 대한 선택키를 봅니다.

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

다음과 유사하게 출력됩니다.

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
다음 단계에서 저장소 계정과 상호 작용하는 데 사용할 수 있도록 `key1` 을 기록해 둡니다.

## <a name="create-a-storage-container"></a>저장소 컨테이너 만들기
로컬 파일 시스템을 논리적으로 구성하기 위해 서로 다른 디렉터리를 만드는 것과 같은 방식으로 가상 디스크 및 이미지를 구성하기 위해 저장소 계정 내에 컨테이너를 만듭니다. 저장소 계정에 포함할 수 있는 컨테이너의 수에는 제한이 없습니다. 

다음 예제에서는 이전 단계에서 얻은 선택키(`key1`)를 지정하여 `myimages`라는 컨테이너를 만듭니다.

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>VHD를 업로드 합니다.
이제 사용자 지정 디스크 이미지를 실제로 업로드할 수 있습니다. VM이 사용하는 모든 가상 디스크를 통해 사용자 지정 디스크 이미지를 페이지 Blob으로 업로드하고 저장합니다.

선택키 및 이전 단계에서 만든 컨테이너를 지정한 후, 로컬 컴퓨터에 있는 사용자 지정 디스크 이미지의 경로를 지정합니다.

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>사용자 지정 이미지에서 VM 만들기
사용자 지정 디스크 이미지에서 VM을 만들 때 디스크 이미지에 대한 URI를 지정합니다. 대상 저장소 계정이 사용자 지정 디스크 이미지가 저장된 저장소 계정과 일치하는지 확인합니다. Azure CLI 또는 Resource Manager JSON 템플릿을 사용하여 VM을 만들 수 있습니다.

### <a name="create-a-vm-using-the-azure-cli"></a>Azure CLI를 사용하여 VM 만들기
`azure vm create` 명령을 사용하여 `--image-urn` 매개 변수가 사용자 지정 디스크 이미지를 가리키도록 지정합니다. `--storage-account-name` 사용자 지정 디스크 이미지가 저장된 저장소 계정와 일치하는지 확인합니다. 사용자 지정 디스크 이미지와 같은 컨테이너를 사용하여 VM을 저장할 필요는 없습니다. 사용자 지정 디스크 이미지를 업로드하기 전에 이전 단계와 동일한 방식으로 모든 추가 컨테이너를 만들어야 합니다.

다음 예제에서는 사용자 지정 디스크 이미지에서 `myVM`이라는 VM을 만듭니다.

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

또한 가상 네트워크, 공용 IP 주소, 사용자 이름 및 SSH 키와 같은 `azure vm create` 명령이 필요로 하는 모든 추가 매개 변수를 지정하거나 프롬프트에 응답할 수 있습니다. [사용 가능한 CLI Resource Manager 매개 변수](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)에 대해 자세히 알아봅니다.

### <a name="create-a-vm-using-a-json-template"></a>JSON 템플릿을 사용하여 VM 만들기
Azure Resource Manager 템플릿은 구축하려는 환경을 정의하는 JSON (JavaScript Notation) 파일입니다. 템플릿은 계산 또는 네트워크과 같은 다양한 리소스 공급자로 세분화됩니다. 기존 템플릿을 사용하거나 직접 템플릿을 작성할 수 있습니다. [Resource Manager 및 템플릿 사용](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.

템플릿의 `Microsoft.Compute/virtualMachines` 공급자 내에 VM에 대한 구성 세부 정보를 포함하는 `storageProfile` 노드가 있게 될 것입니다. 편집할 두 가지 주요 매개 변수는 사용자 지정 디스크 이미지와 새 VM의 가상 디스크를 가리키는 `image`과 `vhd` URI입니다. 다음은 사용자 지정 디스크 이미지 사용에 대한 JSON의 예입니다.

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[사용자 지정 이미지에서 VM을 만드는 데 이 기존 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)을 사용할 수 있거나 [고유한 Azure Resource Manager 템플릿 만들기](../../azure-resource-manager/resource-group-authoring-templates.md)에 관해 알아봅니다. 

템플릿을 구성했다면 `azure group deployment create` 명령을 사용하여 VM을 만듭니다. `--template-uri` 매개 변수를 사용하여 JSON 템플릿의 URI를 지정합니다.

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

컴퓨터에 로컬로 저장된 JSON 파일이 있다면 `--template-file` 매개 변수를 대신 사용할 수 있습니다.

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>다음 단계
사용자 지정 가상 디스크를 준비하고 업로드한 후 [Resource Manager 및 템플릿 사용하기](../../azure-resource-manager/resource-group-overview.md)에 관해 자세히 알아볼 수 있습니다. 또한 새 Vm에 [데이터 디스크 추가](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 를 고려할 수도 있습니다. 응용 프로그램이 액세스해야 할 Vm에서 실행되고 있다면 반드시 [포트 및 끝점 열기](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 해야 합니다.

