---
title: "Azure에서 Linux VM의 디스크 암호화 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 보안 강화를 위해 Linux VM에서 가상 디스크를 암호화하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: b87d187eadff98ba84aa6478c2d233f2ec1c203c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Linux VM에서 가상 디스크를 암호화하는 방법
VM(가상 머신)의 보안과 규정 준수 상태를 향상시키기 위해 가상 디스크 및 VM 자체를 암호화할 수 있습니다. VM은 Azure Key Vault에 안전하게 보관되는 암호화 키를 사용하여 암호화됩니다. 이러한 암호화 키를 제어하고 용도를 감사할 수 있습니다. 이 문서에서는 Azure CLI 2.0을 사용하여 Linux VM에서 가상 디스크를 암호화하는 방법을 자세히 설명합니다. [Azure CLI 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.

## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 VM에서 가상 디스크를 암호화하는 기본 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#overview-of-disk-encryption) 문서 끝까지 참조하세요.

최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myKey*, *myVM*이 포함됩니다.

먼저 Azure 구독 내에서 [az provider register](/cli/azure/provider#az_provider_register)를 사용하여 Azure Key Vault 공급자를 사용하도록 설정하고 [az group create](/cli/azure/group#az_group_create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

[az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 사용하여 Azure Key Vault을 만들고 디스크 암호화에 사용할 Key Vault를 사용하도록 설정합니다. *keyvault_name*에 대한 고유한 Key Vault 이름을 다음과 같이 지정합니다.

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

[az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create)를 사용하여 Key Vault에 암호화 키를 만듭니다. 다음 예제는 *myKey*라는 키를 만듭니다.

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

[az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)에서 Azure Active Directory를 사용하여 서비스 사용자를 만듭니다. 서비스 사용자는 인증 및 Key Vault에서의 암호화 키 교환을 처리합니다. 다음 예제에서는 후속 명령에서 사용하기 위해 서비스 주체 ID 및 암호 값을 읽습니다.

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

암호는 서비스 사용자를 만들 때만 출력됩니다. 원할 경우 암호(`echo $sp_password`)를 보고 기록합니다. [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용하여 서비스 사용자를 나열하고 [az ad sp show](/cli/azure/ad/sp#az_ad_sp_show)를 사용하여 특정 서비스 사용자에 대한 추가 정보를 볼 수 있습니다.

[az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy)를 사용하여 Key Vault에 대한 사용 권한을 설정합니다. 다음 예제에서는 이전 명령의 서비스 사용자 ID가 제공됩니다.

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만들고 5GB 데이터 디스크를 연결합니다. 특정 Marketplace 이미지만 디스크 암호화를 지원합니다. 다음 예제는 *CentOS 7.2n* 이미지를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

위 명령의 출력에 표시된 *publicIpAddress*를 사용하여 VM에 대해 SSH를 수행합니다. 파티션 및 파일 시스템을 만든 후 데이터 디스크를 탑재합니다. 자세한 내용은 [Linux VM에 연결하여 새 디스크 탑재](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)를 참조하세요. SSH 세션을 닫습니다.

[az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable)을 사용하여 VM을 암호화합니다. 다음 예제에서는 이전 `ad sp create-for-rbac` 명령의 *$sp_id* 및 *$sp_password* 변수를 사용합니다.

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

디스크 암호화 프로세스를 완료하는 데 약간의 시간이 걸립니다. [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show)를 사용하여 프로세스의 상태를 모니터링합니다.

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

상태에는 **EncryptionInProgress**가 표시됩니다. OS 디스크의 상태가 **VMRestartPending**을 보고할 때까지 기다린 후 [az vm restart](/cli/azure/vm#az_vm_restart)를 사용하여 VM을 다시 시작합니다.

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

부팅 프로세스 동안 디스크 암호화 프로세스가 완료되므로, 몇 분 정도 기다렸다가 [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show)를 사용하여 암호화 상태를 다시 확인하세요.

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

이제 상태는 OS 디스크와 데이터 디스크를 둘 다 **Encrypted**로 표시합니다.


## <a name="overview-of-disk-encryption"></a>디스크 암호화 개요
Linux VM의 가상 디스크는 미사용 시 [dm-crypt](https://wikipedia.org/wiki/Dm-crypt)를 사용하여 암호화됩니다. Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. Azure Active Directory 서비스 사용자는 VM이 켜지고 꺼지는 경우 이러한 암호화 키 발급을 위한 보안 메커니즘을 제공합니다.

VM을 암호화하는 프로세스는 다음과 같습니다.

1. Azure Key Vault에서 암호화 키를 만듭니다.
2. 암호화하는 디스크에 사용될 수 있도록 암호화 키를 구성합니다.
3. Azure Key Vault에서 암호화 키를 읽어오려면 적절한 사용 권한을 통해 Azure Active Directory 서비스 사용자를 만듭니다.
4. 가상 디스크를 암호화하도록 명령을 실행하고 사용될 Azure Active Directory 서비스 사용자와 적절한 암호화 키를 지정합니다.
5. Azure Active Directory 서비스 사용자는 Azure Key Vault에 필요한 암호화 키를 요청합니다.
6. 가상 디스크는 제공된 암호화 키를 사용하여 암호화됩니다.

## <a name="encryption-process"></a>암호화 프로세스
디스크 암호화는 다음과 같은 추가 구성 요소에 의존합니다.

* **Azure Key Vault** - 디스크 암호화/암호 해독 프로세스를 위한 암호화 키와 암호를 안전하게 보호하는 데 사용됩니다.
  * 이미 존재하는 경우 기존 Azure Key Vault를 사용할 수 있습니다. Key Vault를 디스크 암호화에 전적으로 사용할 필요는 없습니다.
  * 관리 범위 및 키 표시 여부를 분리하기 위해 전용 Key Vault를 만들 수 있습니다.
* **Azure Active Directory** - 필요한 암호화 키의 안전한 교환과 요청된 작업에 대한 인증을 처리합니다.
  * 일반적으로 응용 프로그램 보유를 위해 Azure Active Directory 인스턴스를 사용할 수 있습니다.
  * 서비스 사용자는 해당 암호화 키를 요청하고 발급되도록 하는 보안 메커니즘을 제공합니다. Azure Active Directory와 통합되는 실제 응용 프로그램을 개발하지는 않습니다.

## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항
디스크 암호화에 대해 지원되는 시나리오 및 요구 사항은 다음과 같습니다.

* 다음 Linux Server SKU - Ubuntu, CentOS, SUSE 및 SLES(SUSE Linux Enterprise Server)와 Red Hat Enterprise Linux.
* 모든 리소스(예: Key Vault, Storage 계정, VM)는 동일한 Azure 지역 및 구독 내에 있어야 합니다.
* 표준 A, D, DS, G, GS 등, 시리즈 VM.
* 이미 암호화된 Linux VM에서 암호화 키 업데이트.

다음 시나리오의 경우 디스크 암호화가 현재 지원되지 않습니다.

* 기본 계층 VM.
* 클래식 배포 모델을 사용하여 만든 VM.
* Linux VM에서 OS 디스크 암호화 비활성화.
* 사용자 지정 Linux 이미지 사용.

지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [IaaS VM에 대한 Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.


## <a name="create-azure-key-vault-and-keys"></a>Azure Key Vault 및 키 만들기
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myKey*, *myVM*이 포함됩니다.

첫 번째 단계는 암호화 키를 저장할 Azure Key Vault를 만드는 것입니다. Azure Key Vault는 응용 프로그램 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 가상 디스크 암호화의 경우 Key Vault를 사용하여 가상 디스크 암호화 또는 암호 해독에 사용되는 암호화 키를 저장합니다.

Azure 구독 내에서 [az provider register](/cli/azure/provider#az_provider_register)를 사용하여 Azure Key Vault 공급자를 사용하도록 설정하고 [az group create](/cli/azure/group#az_group_create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `eastus` 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

암호화 키를 포함하는 Azure Key Vault와 저장소 및 VM과 같은 연결된 계산 리소스는 동일한 지역에 상주해야 합니다. [az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 사용하여 Azure Key Vault을 만들고 디스크 암호화에 사용할 Key Vault를 사용하도록 설정합니다. *keyvault_name*에 대한 고유한 Key Vault 이름을 다음과 같이 지정합니다.

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

소프트웨어 또는 HSM(하드웨어 보안 모델) 보호를 사용하여 암호화 키를 저장할 수 있습니다. HSM을 사용하려면 프리미엄 Key Vault가 필요합니다. 소프트웨어 보호 키를 저장하는 표준 Key Vault가 아닌 프리미엄 Key Vault를 만들려면 추가 비용이 소요됩니다. 프리미엄 Key Vault를 만들려면 앞의 단계에서 `--sku Premium`을 명령에 추가합니다. 표준 Key Vault를 만들었기 때문에 다음 예제는 소프트웨어 보호 키를 사용합니다.

두 가지 보호 모델 모두, 가상 디스크의 암호를 해독하기 위해 VM이 부팅될 때 암호화 키를 요청하려면 Azure 플랫폼에 액세스 권한이 허용되어야 합니다. [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create)를 사용하여 Key Vault에 암호화 키를 만듭니다. 다음 예제는 *myKey*라는 키를 만듭니다.

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Azure Active Directory 서비스 사용자 만들기
가상 디스크가 암호화되거나 암호가 해독될 때 계정을 지정하여 Key Vault의 암호화 키 교환 및 인증을 처리합니다. 이 계정 즉, Azure Active Directory 서비스 사용자는 Azure 플랫폼이 VM을 대신하여 적절한 암호화 키를 요청하도록 허용합니다. 기본 Azure Active Directory 인스턴스를 구독 내에서 사용할 수 있지만 많은 조직이 전용 Azure Active Directory 디렉터리를 두고 있습니다.

[az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)에서 Azure Active Directory를 사용하여 서비스 사용자를 만듭니다. 다음 예제에서는 후속 명령에서 사용하기 위해 서비스 사용자 ID 및 암호 값을 읽습니다.

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

암호는 서비스 사용자를 만들 때만 표시됩니다. 원할 경우 암호(`echo $sp_password`)를 보고 기록합니다. [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용하여 서비스 사용자를 나열하고 [az ad sp show](/cli/azure/ad/sp#az_ad_sp_show)를 사용하여 특정 서비스 사용자에 대한 추가 정보를 볼 수 있습니다.

가상 디스크를 암호화하거나 암호를 해독하려면, Key Vault에 저장되어 있는 암호화 키에 대한 권한이 Azure Active Directory 서비스 사용자가 키를 읽는 것을 허용하도록 설정되어야 합니다. [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy)를 사용하여 Key Vault에 대한 사용 권한을 설정합니다. 다음 예제에서는 이전 명령의 서비스 사용자 ID가 제공됩니다.

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기
[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 암호화할 VM을 만들고 5GB 데이터 디스크를 연결합니다. 특정 Marketplace 이미지만 디스크 암호화를 지원합니다. 다음 예제는 *CentOS 7.2n* 이미지를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

위 명령의 출력에 표시된 *publicIpAddress*를 사용하여 VM에 대해 SSH를 수행합니다. 파티션 및 파일 시스템을 만든 후 데이터 디스크를 탑재합니다. 자세한 내용은 [Linux VM에 연결하여 새 디스크 탑재](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)를 참조하세요. SSH 세션을 닫습니다.


## <a name="encrypt-virtual-machine"></a>가상 머신 암호화
가상 디스크를 암호화하기 위해서 이전의 구성 요소를 모두 가져옵니다.

1. Azure Active Directory 서비스 사용자 및 암호를 지정합니다.
2. 암호화된 디스크에 대한 메타데이터를 저장할 Key Vault를 지정합니다.
3. 실제 암호화 및 암호 해독에 사용될 암호화 키를 지정합니다.
4. OS 디스크, 데이터 디스크 또는 모든 디스크를 암호화할지 여부를 지정합니다.

[az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable)을 사용하여 VM을 암호화합니다. 다음 예제에서는 이전 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령의 *$sp_id* 및 *$sp_password* 변수를 사용합니다.

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

디스크 암호화 프로세스를 완료하는 데 약간의 시간이 걸립니다. [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show)를 사용하여 프로세스의 상태를 모니터링합니다.

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

다음의 잘린 예제와 유사하게 출력됩니다.

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

OS 디스크의 상태가 **VMRestartPending**을 보고할 때까지 기다린 후 [az vm restart](/cli/azure/vm#az_vm_restart)를 사용하여 VM을 다시 시작합니다.

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

부팅 프로세스 동안 디스크 암호화 프로세스가 완료되므로, 몇 분 정도 기다렸다가 **az vm encryption show**를 사용하여 암호화 상태를 다시 확인하세요.

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

이제 상태는 OS 디스크와 데이터 디스크를 둘 다 **Encrypted**로 표시합니다.


## <a name="add-additional-data-disks"></a>데이터 디스크 더 추가하기
데이터 디스크를 암호화하고 나면 나중에 VM에 가상 디스크를 더 추가하고 암호화할 수도 있습니다. 예를 들어 다음과 같이 VM에 두 번째 가상 디스크를 추가해보겠습니다.

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

명령을 다시 실행하여 다음과 같이 가상 디스크를 암호화합니다.

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>다음 단계
* 암호화 키 및 Key Vault 삭제를 비롯한 Azure Key Vault 관리에 대한 자세한 내용은 [CLI를 사용하여 Key Vault 관리](../../key-vault/key-vault-manage-with-cli2.md)를 참조하세요.
* Azure에 업로드할 암호화된 사용자 지정 VM 준비와 같은 디스크 암호화에 대한 자세한 내용은 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.
