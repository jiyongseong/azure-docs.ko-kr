---
title: Azure PowerShell을 사용하여 Azure 확장 집합의 디스크 암호화 | Microsoft Docs
description: Azure PowerShell을 사용하여 Windows 가상 머신 확장 집합에서 VM 인스턴스 및 연결된 디스크를 암호화하는 방법 알아보기
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
ms.openlocfilehash: 91138ffad0fd906061e0b0ce5cdb251f402d3341
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "32776111"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Azure PowerShell(미리 보기)을 사용하여 가상 머신 확장 집합에서 OS 및 연결된 데이터 디스크 암호화

업계 표준 암호화 기술을 사용하여 미사용 데이터를 안전하게 보호하기 위해 가상 머신 확장 집합은 ADE(Azure Disk Encryption)를 지원합니다. 암호화는 Windows 및 Linux 가상 머신 확장 집합에 대해 활성화될 수 있습니다. 자세한 내용은 [Windows 및 Linux용 Azure Disk Encryption](../security/azure-security-disk-encryption.md)을 참조하세요.

> [!NOTE]
>  가상 머신 확장 집합용 Azure Disk Encryption은 현재 공개 미리 보기 단계에 있으며, 모든 Azure 공개 지역에서 이용할 수 있습니다.

Azure Disk Encryption이 지원되는 경우는 다음과 같습니다.
- 관리 디스크를 이용하여 생성된 확장 집합 지원(원시(또는 비관리) 디스크 확장 집합은 지원하지 않음).
- Windows 확장 집합의 OS 및 데이터 볼륨. Windows 확장 집합의 OS 및 데이터 볼륨에서는 암호화 비활성화도 지원됩니다.
- Linux 확장 집합의 데이터 볼륨. 현재 미리 보기에서는 Linux 확장 집합의 OS 디스크 암호화가 지원되지 않습니다.

현재 미리 보기에서는 확장 집합 VM의 이미지로 다시 설치 작업과 업그레이드 작업이 지원되지 않습니다. 가상 머신 확장 집합용 Azure Disk Encryption은 테스트 환경에서만 사용하는 것이 권장됩니다. 미리 보기에서는 암호화된 확장 집합에서 OS 이미지를 업그레이드해야 할 필요가 있는 프로덕션 환경에서 디스크 암호화를 사용하도록 설정하지 마세요.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="register-for-disk-encryption-preview"></a>디스크 암호화 미리 보기를 위해 등록

가상 머신 확장 집합용 Azure Disk Encryption 미리 보기를 사용하려면 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)를 사용하여 사용자가 구독을 직접 등록해야 합니다. 디스크 암호화 미리 보기 기능을 처음 사용할 때는 다음 단계를 수행하기만 하면 됩니다.

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

등록 요청을 전파하는 데 최대 10분까지 걸릴 수 있습니다. [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 명령을 사용하여 등록 상태를 확인할 수 있습니다. `RegistrationState`가 *Registered*로 보고되면 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider)를 사용하여 *Mirosoft.Compute* 공급자를 다시 등록합니다.

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>디스크 암호화가 활성화된 Azure Key Vault 만들기

Azure Key Vault는 응용 프로그램 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다.

[New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault)를 사용하여 Key Vault를 만듭니다. Key Vault를 디스크 암호화에 사용할 수 있도록 허용하려면 *EnabledForDiskEncryption* 매개 변수를 설정합니다. 또한 다음 예제에서는 리소스 그룹 이름, Key Vault 이름 및 위치에 대한 변수를 정의합니다. 사용자 고유의 Key Vault 이름을 제공합니다.

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>기존 Key Vault 사용

이 단계는 디스크 암호화로 사용하려는 기존 Key Vault가 있는 경우에만 필요합니다. 이전 섹션에서 Key Vault를 만든 경우 이 단계를 건너뜁니다.

[Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy)를 사용하여 확장 집합과 동일한 구독 및 지역에 있는 기존의 Key Vault에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 다음과 같이 *$vaultName* 변수에서 기존 Key Vault의 이름을 정의합니다.

```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>확장 집합 만들기

먼저 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM 인스턴스에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치는 TCP 포트 80에서 트래픽을 분산할 뿐만 아니라 TCP 포트 3389의 원격 데스크톱 트래픽 및 TCP 포트 5985의 PowerShell 원격을 허용하는 규칙을 포함합니다.

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>암호화 사용

확장 집합에서 VM 인스턴스를 암호화하려면 먼저 [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault)를 사용하여 Key Vault URI 및 리소스 ID에 대한 정보를 얻습니다. 이러한 변수는 [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension)으로 암호화 프로세스를 시작하는 데 사용됩니다.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

메시지가 나타나면 *y*를 입력하여 확장 집합 VM 인스턴스에서 디스크 암호화 프로세스를 계속합니다.

## <a name="check-encryption-progress"></a>암호화 진행 확인

디스크 암호화의 상태를 확인하려면 [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption)을 사용합니다.

```azurepowershell-interactive
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

VM 인스턴스가 암호화될 때 *EncryptionSummary* 코드는 다음 예제 출력에 표시된 대로 *ProvisioningState/succeeded*를 보고합니다.

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>암호화 사용 안 함

암호화된 VM 인스턴스 디스크를 더 이상 사용하지 않으려는 경우 다음과 같이 [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption)을 사용하여 암호화를 비활성화할 수 있습니다.

```azurepowershell-interactive
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 머신 확장 집합을 암호화하는 데 Azure PowerShell을 사용했습니다. [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 또는 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)에 대해 [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) 또는 템플릿을 사용할 수도 있습니다.
