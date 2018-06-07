---
title: Azure CLI 1.0을 사용하여 Linux VM에서 디스크 암호화 | Microsoft Docs
description: Azure CLI 1.0 및 Resource Manager 배포 모델을 사용하여 Linux VM에서 디스크를 암호화하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 Linux VM에서 디스크 암호화
가상 머신(VM)의 보안과 규정 준수 상태를 향상시키기 위해 Azure에서 가상 디스크를 미사용 시 암호화할 수 있습니다. 디스크는 Azure Key Vault에 안전하게 보관되는 암호화 키를 사용하여 암호화됩니다. 이러한 암호화 키를 제어하고 용도를 감사할 수 있습니다. 이 문서는 Azure CLI 1.0 및 Resource Manager 배포 모델을 사용하여 Linux VM에서 가상 디스크를 암호화하는 방법을 자세히 설명합니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="quick-commands"></a>빠른 명령
작업을 빠르게 완료해야 하는 경우 다음 섹션에서 VM에서 가상 디스크를 암호화하는 기본 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#overview-of-disk-encryption) 문서 끝까지 참조하세요.

[최신 Azure CLI 1.0](../../xplat-cli-install.md)이 설치되어 있고 다음과 같이 Resource Manager 모드를 사용하여 로그인되어 있어야 합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myKeyVault` 및 `myVM`이 포함됩니다.

먼저 Azure 구독 내에서 Azure Key Vault 공급자를 사용하도록 설정하고 리소스 그룹을 만듭니다. 다음 예제는 `WestUS` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure Key Vault를 만듭니다. 다음 예제에서는`myKeyVault`라는 Key Vault를 만듭니다.

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Key Vault에 암호화 키를 만들고 디스크 암호화에 사용하도록 설정합니다. 다음 예제는 `myKey`라는 키를 만듭니다.

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Key Vault의 암호화 키를 교환하고 인증을 처리하기 위해 Azure Active Directory를 사용하여 끝점을 만듭니다. `--home-page` 및 `--identifier-uris`에는 실제 라우팅이 가능한 주소를 사용할 필요가 없습니다. 최고 수준의 보안을 위해 암호 대신 클라이언트 암호가 사용되어야 합니다. Azure CLI는 현재 클라이언트 암호를 생성할 수 없습니다. 클라이언트 암호는 Azure Portal에서만 생성될 수 있습니다. 다음 예제는 `myAADApp`이라는 Azure Active Directory 끝점을 만들고 `myPassword`라는 암호를 사용합니다. 다음과 같이 사용자 고유의 암호를 지정합니다.

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

위 명령의 출력에 표시된 `applicationId`를 기록해 둡니다. 이 응용 프로그램 ID가 다음 단계에서 사용됩니다.

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

데이터 디스크를 기존 VM에 추가합니다. 다음 예제는 데이터 디스크를 `myVM`이라는 VM에 추가합니다.

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

생성한 Key Vault 및 키의 세부 정보를 검토합니다. 마지막 단계에서 Key Vault ID, URI 및 키 URL이 필요합니다. 다음 예제는 `myKeyVault`라는 Key Vault와 `myKey`라는 키의 세부 정보를 검토합니다.

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

사용자 고유의 매개 변수 이름을 입력하여 다음과 같이 디스크를 암호화합니다.

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI는 암호화 프로세스 중에 자세한 오류를 제공하지 않습니다. 추가적인 문제 해결 정보는 `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`를 참조하세요. 이전 명령에 변수가 많기 때문에 프로세스가 실패한 이유에 대한 정보가 확실치 않을 수 있습니다. 전체 명령 예제는 다음과 같습니다.

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

마지막으로 암호화 상태를 다시 검토하여 이제 가상 디스크가 암호화되었는지 확인합니다. 다음 예제는 `myResourceGroup` 리소스 그룹의 `myVM`라는 VM의 상태를 확인합니다.

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>디스크 암호화 개요
Linux VM의 가상 디스크는 미사용 시 [dm-crypt](https://wikipedia.org/wiki/Dm-crypt)를 사용하여 암호화됩니다. Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. Azure Active Directory 끝점은 VM이 켜지고 꺼지는 경우 이러한 암호화 키 발급을 위한 보안 메커니즘을 제공합니다.

VM을 암호화하는 프로세스는 다음과 같습니다.

1. Azure Key Vault에서 암호화 키를 만듭니다.
2. 암호화하는 디스크에 사용될 수 있도록 암호화 키를 구성합니다.
3. Azure Key Vault에서 암호화 키를 읽어오려면 적절한 사용 권한을 통해 Azure Active Directory를 사용하여 끝점을 만듭니다.
4. 가상 디스크를 암호화하도록 명령을 실행하고 사용될 Azure Active Directory 끝점과 적절한 암호화 키를 지정합니다.
5. Azure Active Directory 끝점은 Azure Key Vault에 필요한 암호화 키를 요청합니다.
6. 가상 디스크는 제공된 암호화 키를 사용하여 암호화됩니다.

## <a name="supporting-services-and-encryption-process"></a>지원 서비스 및 암호화 프로세스
디스크 암호화는 다음과 같은 추가 구성 요소에 의존합니다.

* **Azure Key Vault** - 디스크 암호화/암호 해독 프로세스를 위한 암호화 키와 암호를 안전하게 보호하는 데 사용됩니다.
  * 이미 존재하는 경우 기존 Azure Key Vault를 사용할 수 있습니다. Key Vault를 디스크 암호화에 전적으로 사용할 필요는 없습니다.
  * 관리 범위 및 키 표시 여부를 분리하기 위해 전용 Key Vault를 만들 수 있습니다.
* **Azure Active Directory** - 필요한 암호화 키의 안전한 교환과 요청된 작업에 대한 인증을 처리합니다.
  * 일반적으로 응용 프로그램 보유를 위해 Azure Active Directory 인스턴스를 사용할 수 있습니다.
  * 응용 프로그램은 적절한 암호화 키를 요청하고 발급받기 위한 Virtual Machines 서비스 및 Key Vault의 끝점에 더 가깝습니다. Azure Active Directory와 통합되는 실제 응용 프로그램을 개발하지는 않습니다.

## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항
디스크 암호화에 대해 지원되는 시나리오 및 요구 사항은 다음과 같습니다.

* 다음 Linux Server SKU - Ubuntu, CentOS, SUSE 및 SLES(SUSE Linux Enterprise Server)와 Red Hat Enterprise Linux.
* 모든 리소스(예: Key Vault, Storage 계정, VM)는 동일한 Azure 지역 및 구독 내에 있어야 합니다.
* 표준 A, D, DS, G 및 GS 시리즈 VM.

다음 시나리오의 경우 디스크 암호화가 현재 지원되지 않습니다.

* 기본 계층 VM.
* 클래식 배포 모델을 사용하여 만든 VM.
* Linux VM에서 OS 디스크 암호화 비활성화.
* 이미 암호화된 Linux VM에서 암호화 키 업데이트.

## <a name="create-the-azure-key-vault-and-keys"></a>Azure Key Vault 및 키 만들기
이 가이드의 나머지 부분을 완료하려면 [최신 Azure CLI 1.0](../../xplat-cli-install.md)이 설치되어 있고 다음과 같이 Resource Manager 모드를 사용하여 로그인되어 있어야 합니다.

```azurecli
azure config mode arm
```

명령 예제 전체에서 모든 예제 매개 변수를 사용자 고유의 이름, 위치, 키 값으로 바꿉니다. 다음 예제는 `myResourceGroup`, `myKeyVault`, `myAADApp` 등의 규칙을 사용합니다.

첫 번째 단계는 암호화 키를 저장할 Azure Key Vault를 만드는 것입니다. Azure Key Vault는 응용 프로그램 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 가상 디스크 암호화의 경우 Key Vault를 사용하여 가상 디스크 암호화 또는 암호 해독에 사용되는 암호화 키를 저장합니다.

Azure 구독 내에서 Azure Key Vault 공급자를 사용하도록 설정한 후 리소스 그룹을 만듭니다. 다음 예제에서는 `WestUS` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

암호화 키를 포함하는 Azure Key Vault와 저장소 및 VM과 같은 연결된 계산 리소스는 동일한 지역에 상주해야 합니다. 다음 예제는 `myKeyVault`라는 Azure Key Vault를 만듭니다.

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

소프트웨어 또는 HSM(하드웨어 보안 모델) 보호를 사용하여 암호화 키를 저장할 수 있습니다. HSM을 사용하려면 프리미엄 Key Vault가 필요합니다. 소프트웨어 보호 키를 저장하는 표준 Key Vault가 아닌 프리미엄 Key Vault를 만들려면 추가 비용이 소요됩니다. 프리미엄 Key Vault를 만들려면 앞의 단계에서 `--sku Premium`을 명령에 추가합니다. 표준 Key Vault를 만들었기 때문에 다음 예제는 소프트웨어 보호 키를 사용합니다.

두 가지 보호 모델 모두, 가상 디스크의 암호를 해독하기 위해 VM이 부팅될 때 암호화 키를 요청하려면 Azure 플랫폼에 액세스 권한이 허용되어야 합니다. Key Vault 내에서 암호화 키를 만든 후 가상 디스크 암호화에 사용할 수 있도록 설정합니다. 다음 예제는 `myKey`라는 키를 만든 후 이것을 디스크 암호화에 사용할 수 있도록 설정합니다.

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기
가상 디스크가 암호화되거나 암호가 해독될 때 끝점을 사용하여 Key Vault의 암호화 키 교환 및 인증을 처리합니다. 이 끝점 즉, Azure Active Directory 응용 프로그램은 Azure 플랫폼이 VM을 대신하여 적절한 암호화 키를 요청하도록 허용합니다. 기본 Azure Active Directory 인스턴스를 구독 내에서 사용할 수 있지만 많은 조직이 전용 Azure Active Directory 디렉터리를 두고 있습니다.

전체 Azure Active Directory 응용 프로그램을 만드는 것이 아니므로 다음 예제의 `--home-page` 및 `--identifier-uris` 매개 변수에 실제 라우팅이 가능한 주소를 사용할 필요는 없습니다. 다음 예제는 Azure Portal 내에서 키를 생성하기 보다는 암호 기반 암호를 지정합니다. 현재는 Azure CLI에서 키 생성을 수행할 수 없습니다.

Azure Active Directory 응용 프로그램을 만듭니다. 다음 예제는 `myAADApp`이라는 응용 프로그램을 만들고 `myPassword`라는 암호를 사용합니다. 다음과 같이 사용자 고유의 암호를 지정합니다.

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

앞의 명령에서 출력으로 반환된 `applicationId`를 기록해 둡니다. 이 응용 프로그램 ID는 일부 나머지 단계에서 사용됩니다. 다음으로, 환경 내에서 응용 프로그램에 액세스할 수 있도록 SPN(서비스 사용자 이름)을 만듭니다. 가상 디스크를 암호화하거나 암호를 해독하려면, Key Vault에 저장되어 있는 암호화 키에 대한 권한이 Azure Active Directory 응용 프로그램이 키를 읽는 것을 허용하도록 설정되어야 합니다.

다음과 같이 SPN을 만들고 적절한 권한을 설정합니다.

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>가상 디스크를 추가하고 암호화 상태를 검토
일부 가상 디스크를 실제로 암호화하기 위해 기존 VM에 디스크를 추가해 보겠습니다. 다음과 같이 5GB 데이터 디스크를 기존 VM에 추가합니다.

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

가상 디스크는 현재 암호화되지 않았습니다. 다음과 같이 VM의 현재 암호화 상태를 검토합니다.

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>가상 디스크 암호화
이제 가상 디스크를 암호화하기 위해서 이전의 구성 요소를 모두 가져옵니다.

1. Azure Active Directory 응용 프로그램 및 암호를 지정합니다.
2. 암호화된 디스크에 대한 메타데이터를 저장할 Key Vault를 지정합니다.
3. 실제 암호화 및 암호 해독에 사용될 암호화 키를 지정합니다.
4. OS 디스크, 데이터 디스크 또는 모든 디스크를 암호화할지 여부를 지정합니다.

Key Vault ID, URI 및 마지막 단계에서 키 URL이 필요하므로 생성한 키 및 Azure Key Vault에 대한 세부 정보를 검토합니다.

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

다음과 같이 `azure keyvault show` 및 `azure keyvault key show` 명령의 출력을 사용하여 가상 디스크를 암호화합니다.

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

앞의 명령에 변수가 많으므로 다음 예제에서 전체 명령을 참조할 수 있습니다.

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI는 암호화 프로세스 중에 자세한 오류를 제공하지 않습니다. 추가적인 문제 해결 정보는 암호화하는 VM의 `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`를 검토하세요.

마지막으로 암호화 상태를 다시 검토하여 이제 가상 디스크가 암호화되었는지 확인합니다.

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>데이터 디스크 더 추가하기
데이터 디스크를 암호화하고 나면 나중에 VM에 가상 디스크를 더 추가하고 암호화할 수도 있습니다. `azure vm enable-disk-encryption` 명령을 실행할 때 `--sequence-version` 매개 변수를 사용하여 시퀀스 버전을 증가시킵니다. 이 시퀀스 버전 매개 변수를 사용하면 동일한 VM에서 반복 작업을 수행할 수 있습니다.

예를 들어 다음과 같이 VM에 두 번째 가상 디스크를 추가해보겠습니다.

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

명령을 다시 실행하여 가상 디스크를 암호화 하고, 이번에는 `--sequence-version` 매개 변수를 추가하여, 다음과 같이 첫 번째 실행의 값을 증가시킵니다.

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>다음 단계
* 암호화 키 및 Key Vault 삭제를 비롯한 Azure Key Vault 관리에 대한 자세한 내용은 [CLI를 사용하여 Key Vault 관리](../../key-vault/key-vault-manage-with-cli2.md)를 참조하세요.
* Azure에 업로드할 암호화된 사용자 지정 VM 준비와 같은 디스크 암호화에 대한 자세한 내용은 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.
