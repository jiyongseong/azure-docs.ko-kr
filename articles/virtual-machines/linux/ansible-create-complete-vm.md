---
title: Azure에서 Ansible을 사용하여 전체 Linux VM 만들기 | Microsoft 문서
description: Azure에서 Ansible을 사용하여 전체 Linux 가상 컴퓨터 환경을 만들고 관리하는 방법을 알아봅니다
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
ms.openlocfilehash: 22b580e74ec412763b9c34a7fa2fea97c8a277d0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896183"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Azure에서 Ansible을 사용하여 전체 Linux 가상 컴퓨터 환경 만들기
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Azure에서 Ansible을 사용하여 다른 리소스와 동일한 방식으로 VM(가상 머신)을 관리할 수 있습니다. 이 문서에서는 Ansible을 사용하여 전체 Linux 환경 및 지원 리소스를 만드는 방법을 보여 줍니다. 또한 [Ansible을 사용하여 기본 VM을 만드는](ansible-create-vm.md) 방법을 배울 수 있습니다.


## <a name="prerequisites"></a>필수 조건
Ansible을 사용하여 Azure 리소스를 관리하려면 다음이 필요합니다.

- 호스트 시스템에 설치된 Ansible 및 Azure Python SDK 모듈
    - [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts) 및 [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)에 Ansible 설치
- Azure 자격 증명 및 이를 사용하도록 구성된 Ansible
    - [Azure 자격 증명 만들기 및 Ansible 구성](ansible-install-configure.md#create-azure-credentials)
- Azure CLI 버전 2.0.4 이상 `az --version`을 실행하여 버전을 찾습니다. 
    - 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](/azure/cloud-shell/quickstart)을 사용할 수도 있습니다.


## <a name="create-virtual-network"></a>가상 네트워크 만들기
Ansible Playbook의 다음 섹션에서는 *10.0.0.0/16* 주소 공간에 *myVnet*이라는 가상 네트워크를 만듭니다.

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

서브넷을 추가하기 위해 다음 섹션에서는 *myVnet* 가상 네트워크에 *mySubnet*이라는 서브넷을 만듭니다.

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>공용 IP 주소 만들기
인터넷을 통해 리소스에 액세스하려면 공용 IP 주소를 만들어 VM에 할당합니다. Ansible Playbook의 다음 섹션에서는 *myPublicIP*라는 공용 IP 주소를 만듭니다.

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기
네트워크 보안 그룹은 VM 내/외부 네트워크 트래픽의 흐름을 제어합니다. Ansible Playbook의 다음 섹션에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만들고 TCP 포트 22에서 SSH 트래픽을 허용하는 규칙을 정의합니다.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>가상 네트워크 인터페이스 카드 만들기
가상 NIC(네트워크 인터페이스 카드)는 지정된 가상 네트워크, 공용 IP 주소 및 네트워크 보안 그룹에 VM을 연결합니다. Ansible Playbook의 다음 섹션에서는 사용자가 만든 가상 네트워킹 리소스에 연결된 *myNIC*라는 가상 NIC를 만듭니다.

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기
마지막 단계에서는 VM을 만들고 생성한 모든 리소스를 사용합니다. Ansible Playbook의 다음 섹션에서는 *myVM*이라는 VM을 만들고 *myNIC*라는 가상 NIC를 연결합니다. 다음과 같이 *key_data* 쌍으로 사용자 고유의 전체 공개 키 데이터를 입력합니다.

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>전체 Ansible Playbook
이 모든 섹션을 함께 가져오려면 *azure_create_complete_vm.yml*이라는 Ansible Playbook을 만들고 다음 내용을 붙여넣습니다. *key_data* 쌍으로 사용자 고유의 전체 공개 키 데이터를 입력합니다.

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible에는 모든 리소스를 배포할 리소스 그룹이 필요합니다. [az group create](/cli/azure/vm#az_vm_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

Ansible을 사용하여 전체 VM 환경을 만들려면 다음과 같이 Playbook을 실행합니다.

```bash
ansible-playbook azure_create_complete_vm.yml
```

출력은 VM이 성공적으로 만들어졌음을 보여 주는 다음 예제와 유사합니다.

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>다음 단계
이 예제에서는 필요한 가상 네트워킹 리소스를 포함하여 전체 VM 환경을 만듭니다. 기본 옵션을 사용하여 기존 네트워크 리소스에 VM을 만드는 보다 직접적인 예제는 [VM 만들기](ansible-create-vm.md)를 참조하세요.
