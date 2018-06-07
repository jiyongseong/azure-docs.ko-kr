---
title: Azure Virtual Machines에서 사용할 Ansible 설치 및 구성 | Microsoft Docs
description: Ubuntu, CentOS 및 SLES에서 Azure 리소스를 관리하기 위해 Ansible을 설치 및 구성하는 방법을 알아봅니다.
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
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: e6fad548eda35d1832cb4ecc2fd9bdabf825f361
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896132"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Azure에서 가상 머신을 관리하기 위한 Ansible 설치 및 구성

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Azure에서 Ansible을 사용하여 다른 리소스와 동일한 방식으로 VM(가상 머신)을 관리할 수 있습니다. 이 문서에는 가장 일반적인 Linux 배포판 중 일부에 대해 Ansible 및 필요한 Azure Python SDK 모듈을 설치하는 방법을 자세히 설명합니다. 설치된 패키지를 특정 플랫폼에 맞게 조정하여 다른 배포판에 Ansible을 설치할 수 있습니다. Azure 리소스를 안전하게 만들기 위해 Ansible에 대한 자격 증명을 만들고 정의하는 방법도 알아봅니다.

추가 플랫폼에 대한 설치 옵션 및 단계는 [Ansible 설치 가이드](https://docs.ansible.com/ansible/intro_installation.html)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="install-ansible"></a>Ansible 설치

Azure와 Ansible을 사용하는 가장 쉬운 방법 중 하나는 Azure 리소스를 관리 및 개발하는 브라우저 기반 셸 환경인 Azure Cloud Shell을 사용하는 것입니다. Ansible은 Cloud Shell에 사전 설치되어 있으므로 Ansible 설치 방법에 관한 지침은 건너뛰고 [Azure 자격 증명 만들기](#create-azure-credentials)로 이동할 수 있습니다. Cloud Shell에서 사용할 수 있는 추가 도구에 대한 목록은 [Azure Cloud Shell의 Bash를 위한 기능 및 도구](../../cloud-shell/features.md#tools)를 참조하세요.

다음 지침에서는 다양한 배포판을 위해 Linux VM을 만든 다음, Ansible를 설치하는 방법을 설명합니다. Linux VM을 만들 필요가 없으면 이 첫 번째 단계를 건너뛰고 Azure 리소스 그룹을 만듭니다. VM을 만들어야 하는 경우에는 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

이제 VM을 만드는 방법에 대한 단계에서 다음 배포판 중 하나를 선택한 다음, 필요에 따라 Ansible을 설치합니다.

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

필요한 경우 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 *myVMAnsible*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 만들기 작업의 출력에 기록된 `publicIpAddress`를 사용하여 VM에 SSH 연결합니다.

```bash
ssh azureuser@<publicIpAddress>
```

VM에서 Azure Python SDK 모듈 및 Ansible에 대한 필수 패키지를 다음과 같이 설치합니다.

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

이제 계속해서 [Azure 자격 증명 만들기](#create-azure-credentials) 작업을 수행합니다.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

필요한 경우 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 *myVMAnsible*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 만들기 작업의 출력에 기록된 `publicIpAddress`를 사용하여 VM에 SSH 연결합니다.

```bash
ssh azureuser@<publicIpAddress>
```

VM에서 Azure Python SDK 모듈 및 Ansible에 대한 필수 패키지를 다음과 같이 설치합니다.

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

이제 계속해서 [Azure 자격 증명 만들기](#create-azure-credentials) 작업을 수행합니다.

### <a name="sles-12-sp2"></a>SLES 12 SP2

필요한 경우 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 *myVMAnsible*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 만들기 작업의 출력에 기록된 `publicIpAddress`를 사용하여 VM에 SSH 연결합니다.

```bash
ssh azureuser@<publicIpAddress>
```

VM에서 Azure Python SDK 모듈 및 Ansible에 대한 필수 패키지를 다음과 같이 설치합니다.

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

이제 계속해서 [Azure 자격 증명 만들기](#create-azure-credentials) 작업을 수행합니다.

## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기

Ansible은 사용자 이름 및 암호 또는 서비스 주체를 사용하여 Azure와 통신합니다. Azure 서비스 주체는 앱, 서비스 및 Ansible과 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 주체가 Azure에서 수행할 수 있는 작업에 대한 사용 권한은 사용자가 제어하고 정의합니다. 사용자 이름 및 암호를 입력하는 것 이상으로 보안을 강화하기 위해 이 예제에서는 기본 서비스 주체를 만듭니다.

호스트 컴퓨터 또는 Azure Cloud Shell에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac)를 사용하여 서비스 주체를 만듭니다. Ansible에서 필요한 자격 증명이 화면에 출력됩니다.

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

이전 명령에서 출력의 예는 다음과 같습니다.

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure에서 인증하려면 [az account show](/cli/azure/account#az_account_show)를 사용하여 Azure 구독 ID를 가져와야 합니다.

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

다음 단계에서 이러한 두 명령의 출력을 사용합니다.

## <a name="create-ansible-credentials-file"></a>Ansible 자격 증명 파일 만들기

Ansible에 자격 증명을 제공하려면 환경 변수를 정의하거나 로컬 자격 증명 파일을 만듭니다. Ansible 자격 증명을 정의하는 방법에 대한 자세한 내용은 [Azure 모듈에 자격 증명 제공](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)을 참조하세요.

개발 환경의 경우 호스트 VM에서 Ansible에 대한 *자격 증명* 파일을 만듭니다. 이전 단계에서 Ansible을 설치한 VM에서 자격 증명 파일을 만듭니다.

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*자격 증명* 파일 자체는 구독 ID와 서비스 주체 만들기의 출력을 결합합니다. 이전 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 명령의 출력은 *client_id*, *secret* 및 *tenant*에 필요한 대로 동일합니다. 다음 예제 *자격 증명* 파일은 이전 출력과 일치하는 이러한 값을 보여 줍니다. 다음과 같이 사용자 고유의 값을 입력합니다.

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

파일을 저장하고 닫습니다.

## <a name="use-ansible-environment-variables"></a>Ansible 환경 변수 사용

Ansible Tower 또는 Jenkins와 같은 도구를 사용하려는 경우 환경 변수를 정의해야 합니다. 이전 단계에서 만든 Ansible 클라이언트 및 Azure 자격 증명 파일을 사용하려는 경우에는 이 단계를 건너뛸 수 있습니다. 환경 변수는 Azure 자격 증명 파일과 동일한 정보를 정의합니다.

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>다음 단계

이제 Ansible 및 필요한 Azure Python SDK 모듈을 설치하고 사용할 Ansible 자격 증명을 정의했습니다. [Ansible을 사용하여 VM을 만드는](ansible-create-vm.md) 방법을 알아봅니다. [Ansible을 사용하여 전체 Azure VM 및 지원 리소스를 만드는](ansible-create-complete-vm.md) 방법도 배울 수 있습니다.
