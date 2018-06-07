---
title: Azure에서 Ansible을 사용하여 기본 Linux VM 만들기 | Microsoft Docs
description: Azure에서 Ansible을 사용하여 기본 Linux 가상 머신을 만들고 관리하는 방법을 알아봅니다
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: a2bf047d5a08bfd3df6a6c76116d2b9b9ab81fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896166"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Azure에서 Ansible을 사용하여 기본 가상 머신 만들기
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Azure에서 Ansible을 사용하여 다른 리소스와 동일한 방식으로 VM(가상 머신)을 관리할 수 있습니다. 이 문서에서는 Ansible을 사용하여 기본 VM을 만드는 방법을 보여 줍니다. 또한 [Ansible을 사용하여 전체 VM 환경을 만드는](ansible-create-complete-vm.md) 방법도 배울 수 있습니다.


## <a name="prerequisites"></a>필수 조건
Ansible을 사용하여 Azure 리소스를 관리하려면 다음이 필요합니다.

- 호스트 시스템에 설치된 Ansible 및 Azure Python SDK 모듈
    - [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts) 및 [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)에 Ansible 설치
- Azure 자격 증명 및 이를 사용하도록 구성된 Ansible
    - [Azure 자격 증명 만들기 및 Ansible 구성](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 버전 2.0.4 이상 `az --version`을 실행하여 버전을 찾습니다. 
    - 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](/azure/cloud-shell/quickstart)을 사용할 수도 있습니다.


## <a name="create-supporting-azure-resources"></a>지원 Azure 리소스 만들기
이 예제에서는 기존 인프라에 VM을 배포하는 runbook을 만듭니다. 먼저 [az group create](/cli/azure/vm#az_vm_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 VM의 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크와 *mySubnet*이라는 서브넷을 만듭니다.

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Ansible Playbook 만들기 및 실행
*azure_create_vm.yml*이라는 Ansible Playbook을 만들고 다음 내용을 붙여넣습니다. 이 예제에서는 단일 VM을 만들고 SSH 자격 증명을 구성합니다. 다음과 같이 *key_data* 쌍으로 사용자 고유의 전체 공개 키 데이터를 입력합니다.

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible을 사용하여 VM을 만들려면 다음과 같이 Playbook을 실행합니다.

```bash
ansible-playbook azure_create_vm.yml
```

출력은 VM이 성공적으로 만들어졌음을 보여 주는 다음 예제와 유사합니다.

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>다음 단계
이 예제에서는 기존 리소스 그룹에 VM을 만들고 이미 배포된 가상 네트워크를 사용합니다. Ansible을 사용하여 가상 네트워크 및 네트워크 보안 그룹 규칙 등의 지원 리소스를 만드는 방법에 대한 자세한 예제는 [Ansible을 사용하여 전체 VM 환경 만들기](ansible-create-complete-vm.md)를 참조하세요.
