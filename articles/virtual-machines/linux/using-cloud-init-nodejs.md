---
title: Azure에서 cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정 | Microsoft Docs
description: Azure CLI 1.0에서 cloud-init를 사용하여 생성 중인 Linux VM을 사용자 지정하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 2e9182a18a2827ed7f54f5fd042e5934b3b1fd5c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Azure CLI 1.0에서 cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정
이 문서에서는 cloud-init 스크립트를 사용하여 호스트 이름 설정, 설치된 패키지 업데이트, 사용자 계정 관리를 수행하는 방법을 설명합니다.  VM을 만드는 중에 Azure CLI에서 cloud-init 스크립트를 호출합니다.  이 문서의 내용을 실행하기 위해 필요한 사항:

* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* `azure login`으로 로그인된 [Azure CLI](../../cli-install-nodejs.md)
* Azure Resource Manager 모드 `azure config mode arm`으로 *있어야 하는* Azure CLI

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="quick-commands"></a>빠른 명령
호스트 이름을 설정하고, 모든 패키지를 업데이트하고, Linux에 sudo 사용자를 추가하는 cloud-init.txt 스크립트를 만듭니다.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
VM을 시작할 리소스 그룹을 만듭니다.

```azurecli
azure group create myResourceGroup westus
```

cloud-init을 사용해 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>자세한 연습
### <a name="introduction"></a>소개
새 Linux VM을 시작하면 사용자 지정된 사항이나 사용자의 요구에 맞게 준비된 기능이 없는 표준 Linux VM이 시작됩니다. [cloud-init](https://cloudinit.readthedocs.org) 은 Linux VM을 처음으로 부팅할 때 해당 VM에 스크립트 또는 구성 설정을 삽입하는 표준 방법입니다.

Azure에서는 다음의 세 가지 방법으로 배포 또는 부팅 중인 Linux VM을 변경할 수 있습니다.

* cloud-init을 사용하여 스크립트를 삽입합니다.
* Azure [VMAccess 확장](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 스크립트를 삽입합니다.
* Azure 템플릿을 사용합니다(cloud-init 사용).
* Azure 템플릿을 사용합니다( [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)사용).

부팅 후에 언제든지 스크립트를 삽입하려면 다음 방법을 사용합니다.

* SSH를 통해 명령을 직접 실행합니다.
* Azure [VMAccess 확장](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 명령적으로 또는 Azure 템플릿에서 스크립트를 삽입합니다.
* Ansible, Salt, Chef, Puppet 등의 구성 관리 도구를 사용합니다.

> [!NOTE]
> : VMAccess 확장은 SSH를 사용할 때와 같은 방식으로 스크립트를 루트로 실행합니다.  그러나 VM 확장 사용 시에는 시나리오에 따라 유용할 수 있는 다양한 Azure 제공 기능을 사용할 수 있습니다.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Azure VM 빨리 만들기 이미지 별칭에 대한 cloud-init 사용 가능 여부
| Alias | 게시자 | 제안 | SKU | 버전 | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |최신 |no |
| CoreOS |CoreOS |CoreOS |Stable |최신 |예 |
| Debian |credativ |Debian |8 |최신 |no |
| openSUSE |SUSE |openSUSE |13.2 |최신 |no |
| RHEL |Redhat |RHEL |7.2 |최신 |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |최신 |예 |

Microsoft는 파트너와 협력하여 파트너가 Azure에 제공하는 이미지에 cloud-init를 포함하고 이러한 이미지에서 cloud-init가 작동하도록 설정하고 있습니다.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Azure CLI를 사용하여 VM 생성에 cloud-init 스크립트 추가
Azure에서 VM을 만들 때 cloud-init 스크립트를 시작하려면 Azure CLI `--custom-data` 스위치를 사용하여 cloud-init 파일을 지정합니다.

VM을 시작할 리소스 그룹을 만듭니다.

```azurecli
azure group create myResourceGroup westus
```

cloud-init을 사용해 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>cloud-init 스크립트를 만들어 Linux VM의 호스트 이름 설정
Linux VM의 가장 간단하고 중요한 설정 중 하나는 호스트 이름입니다. 이 스크립트와 함께 cloud-init을 사용하여 손쉽게 설정할 수 있습니다.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>예제 cloud-init 스크립트 `cloud_config_hostname.txt`
```sh
#cloud-config
hostname: myservername
```

VM을 처음 시작하는 동안 이 cloud-init 스크립트는 호스트 이름을 `myservername`으로 설정합니다.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

로그인한 다음 새 VM의 호스트 이름을 확인합니다.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Linux를 업데이트하기 위한 cloud-init 만들기
보안상의 이유로 최초 부팅 시 Ubuntu VM을 업데이트하려고 합니다.  사용 중인 Linux 배포에 따라 다음 스크립트와 cloud-init을 사용하여 업데이트할 수 있습니다.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_apt_upgrade.txt`
```sh
#cloud-config
apt_upgrade: true
```

Linux가 부팅되고 나면 설치된 모든 패키지가 `apt-get`을 통해 업데이트됩니다.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

로그인한 다음 모든 패키지가 업데이트되었는지 확인합니다.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Linux에 사용자를 추가하기 위한 cloud-init 만들기
새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 사용자 자신을 추가하는 것이며, 그렇지 않을 경우 `root`를 사용하지 않아야 합니다. 보안 및 유용성 측면의 모범 사례인 SSH 키를 사용하는 것이 좋습니다. SSH 키는 이 cloud-init 스크립트와 함께 `~/.ssh/authorized_keys` 파일에 추가됩니다.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_add_users.txt`
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux가 부팅되고 나면 나열된 모든 사용자가 작성되어 sudo 그룹에 추가됩니다.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

로그인한 다음 새로 생성된 사용자를 확인합니다.

```bash
ssh myVM
cat /etc/group
```

출력

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>다음 단계
cloud-init은 부팅 시 Linux VM을 수정하는 표준 방식의 하나로 자리잡고 있습니다. Azure에는 부팅 시에 또는 실행 중에 Linux VM을 수정할 수 있는 VM 확장도 있습니다. 예를 들어 VM을 실행하는 동안 Azure VMAccessExtension을 사용하여 SSH 또는 사용자 정보를 다시 설정할 수 있습니다. cloud-init을 사용하는 경우 암호를 다시 설정하려면 VM을 다시 부팅해야 합니다.

[가상 머신 확장 및 기능 정보](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

