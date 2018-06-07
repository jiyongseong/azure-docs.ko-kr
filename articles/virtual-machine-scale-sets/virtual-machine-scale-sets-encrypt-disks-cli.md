---
title: Azure CLI를 사용하여 Azure 확장 집합의 디스크 암호화 | Microsoft Docs
description: Azure CLI 2.0을 사용하여 Linux 가상 머신 확장 집합에서 VM 인스턴스 및 연결된 디스크를 암호화하는 방법 알아보기
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776417"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)을 사용하여 가상 머신 확장 집합에서 OS 및 연결된 데이터 디스크 암호화

업계 표준 암호화 기술을 사용하여 미사용 데이터를 안전하게 보호하기 위해 가상 머신 확장 집합은 ADE(Azure Disk Encryption)를 지원합니다. 암호화는 Windows 및 Linux 가상 머신 확장 집합에 대해 활성화될 수 있습니다. 자세한 내용은 [Azure Disk Encryption for Linux and Windows](../security/azure-security-disk-encryption.md)(Linux 및 Windows용 Azure Disk Encryption)를 참조하세요.

> [!NOTE]
>  가상 머신 확장 집합용 Azure Disk Encryption은 현재 공개 미리 보기 단계에 있으며, 모든 Azure 공개 지역에서 이용할 수 있습니다.

Azure Disk Encryption이 지원되는 경우는 다음과 같습니다.
- 관리 디스크를 이용하여 생성된 확장 집합 지원(원시(또는 비관리) 디스크 확장 집합은 지원하지 않음).
- Windows 확장 집합의 OS 및 데이터 볼륨. Windows 확장 집합의 OS 및 데이터 볼륨에서는 암호화 비활성화도 지원됩니다.
- Linux 확장 집합의 데이터 볼륨. 현재 미리 보기에서는 Linux 확장 집합의 OS 디스크 암호화가 지원되지 않습니다.

현재 미리 보기에서는 확장 집합 VM의 이미지로 다시 설치 작업과 업그레이드 작업이 지원되지 않습니다. 가상 머신 확장 집합용 Azure Disk Encryption은 테스트 환경에서만 사용하는 것이 권장됩니다. 미리 보기에서는 암호화된 확장 집합에서 OS 이미지를 업그레이드해야 할 필요가 있는 프로덕션 환경에서 디스크 암호화를 사용하도록 설정하지 마세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.31 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="register-for-disk-encryption-preview"></a>디스크 암호화 미리 보기를 위해 등록

가상 머신 확장 집합용 Azure Disk Encryption 미리 보기를 사용하려면 [az feature register](/cli/azure/feature#az_feature_register)를 사용하여 사용자가 구독을 직접 등록해야 합니다. 디스크 암호화 미리 보기 기능을 처음 사용할 때는 다음 단계를 수행하기만 하면 됩니다.

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

등록 요청을 전파하는 데 최대 10분까지 걸릴 수 있습니다. [az feature show](/cli/azure/feature#az_feature_show)를 사용하여 등록 상태를 확인할 수 있습니다. `State`가 *Registered*로 보고되면 [az provider register](/cli/azure/provider#az_provider_register)를 사용하여 *Mirosoft.Compute* 공급자를 다시 등록합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>확장 집합 만들기

확장 집합을 만들려면 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az vmss create](/cli/azure/vmss#az_vmss_create)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 변경 내용이 적용될 때 자동으로 업데이트하도록 설정된 *myScaleSet*이라는 확장 집합을 만들고, *~/.ssh/id_rsa*에 없는 경우 SSH 키를 생성합니다. 32Gb 데이터 디스크가 각 VM 인스턴스에 연결되며, Azure [사용자 지정 스크립트 확장](../virtual-machines/linux/extensions-customscript.md)을 사용하여 [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)로 데이터 디스크를 준비합니다.

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>디스크 암호화가 활성화된 Azure 키 자격 증명 모음 만들기

Azure Key Vault는 응용 프로그램 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다.

사용자 고유의 *keyvault_name*을 정의합니다. 그런 다음, [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create)를 사용하여 확장 집합과 동일한 구독 및 영역에 KeyVault를 만들고 *--enabled-for-disk-encryption*액세스 정책을 설정합니다.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>기존 Key Vault 사용

이 단계는 디스크 암호화로 사용하려는 기존 Key Vault가 있는 경우에만 필요합니다. 이전 섹션에서 Key Vault를 만든 경우 이 단계를 건너뜁니다.

사용자 고유의 *keyvault_name*을 정의합니다. 그런 다음, [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update)를 사용하여 KeyVault를 업데이트하고 *--enabled-for-disk-encryption* 액세스 정책을 설정합니다.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>암호화 사용

확장 집합에서 VM 인스턴스를 암호화하려면 먼저 [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show)를 사용하여 Key Vault 리소스 ID에 대한 정보를 얻습니다. 이러한 변수는 [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable)로 암호화 프로세스를 시작하는 데 사용됩니다.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

암호화 프로세스를 시작하는 데 1~2분 정도 걸릴 수 있습니다.

확장 집합이 이전 단계에 만든 확장 집합에 대한 업그레이드 정책이며 “자동”으로 설정되어 있으므로 VM 인스턴스가 암호화 프로세스를 자동으로 시작합니다. 업그레이드 정책이 수동으로 설정된 확장 집합의 경우에는 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances)를 사용하여 VM 인스턴스에 대해 암호화 정책을 시작합니다.

## <a name="check-encryption-progress"></a>암호화 진행 확인

디스크 암호화의 상태를 확인하려면 [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)를 사용합니다.

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

VM 인스턴스가 암호화되어 있으면 다음 예제 출력에 나온 것처럼 “EncryptionState/encrypted”가 보고됩니다.

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>암호화 사용 안 함

암호화된 VM 인스턴스 디스크를 더 이상 사용하지 않으려는 경우 다음과 같이 [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable)을 사용하여 암호화를 비활성화할 수 있습니다.

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure CLI 2.0을 사용하여 가상 머신 확장 집합을 암호화했습니다. [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 또는 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)에 대해 [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) 또는 템플릿을 사용할 수도 있습니다.

Linux 확장 집합 데이터 디스크 암호화에 대한 종단 간 배치 파일 예제는 [여기](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)서 찾을 수 있습니다. 이 예제에서는 리소스 그룹, Linux 확장 집합을 만들고, 5GB 데이터 디스크를 탑재하고, 가상 머신 확장 집합을 암호화합니다.