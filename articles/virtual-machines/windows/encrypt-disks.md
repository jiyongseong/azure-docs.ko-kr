---
title: Azure에서 Windows VM의 디스크 암호화 | Microsoft Docs
description: Azure PowerShell을 사용하여 보안 강화를 위해 Windows VM에서 가상 디스크를 암호화하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Windows VM에서 가상 디스크를 암호화하는 방법
VM(가상 머신)의 보안과 규정 준수 상태를 향상시키기 위해 Azure에서 가상 디스크를 암호화할 수 있습니다. 디스크는 Azure Key Vault에 안전하게 보관되는 암호화 키를 사용하여 암호화됩니다. 이러한 암호화 키를 제어하고 용도를 감사할 수 있습니다. 이 문서에서는 Azure PowerShell을 사용하여 Windows VM에서 가상 디스크를 암호화하는 방법을 자세히 설명합니다. [Azure CLI 2.0을 사용하여 Linux VM을 암호화](../linux/encrypt-disks.md)할 수도 있습니다.

## <a name="overview-of-disk-encryption"></a>디스크 암호화 개요
Windows VM의 가상 디스크는 미사용 시 Bitlocker를 사용하여 암호화됩니다. Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. Azure Active Directory 서비스 사용자는 VM이 켜지고 꺼지는 경우 이러한 암호화 키 발급을 위한 보안 메커니즘을 제공합니다.

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

* Azure Marketplace 이미지 또는 사용자 지정 VHD 이미지에서 새 Windows VM에 대해 암호화 사용
* Azure에서 기존 Windows VM에 대해 암호화 사용
* 저장소 공간을 사용하여 구성된 Windows VM에서 암호화 사용
* Windows VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* 모든 리소스(예: Key Vault, Storage 계정, VM)는 동일한 Azure 지역 및 구독 내에 있어야 합니다.
* 표준 계층 VM(예: A, D, DS, G 및 GS 시리즈 VM)

다음 시나리오의 경우 디스크 암호화가 현재 지원되지 않습니다.

* 기본 계층 VM.
* 클래식 배포 모델을 사용하여 만든 VM.
* 이미 암호화된 VM에서 암호화 키 업데이트
* 온-프레미스 키 관리 서비스와의 통합

## <a name="create-azure-key-vault-and-keys"></a>Azure Key Vault 및 키 만들기
시작하기 전에 최신 버전의 Azure PowerShell 모듈을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 명령 예제 전체에서 모든 예제 매개 변수를 사용자 고유의 이름, 위치, 키 값으로 바꿉니다. 다음 예제에서는 *myResourceGroup*, *myKeyVault*, *myVM* 등의 규칙을 사용합니다.

첫 번째 단계는 암호화 키를 저장할 Azure Key Vault를 만드는 것입니다. Azure Key Vault는 응용 프로그램 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 가상 디스크 암호화의 경우 Key Vault를 만들어 가상 디스크 암호화 또는 암호 해독에 사용되는 암호화 키를 저장합니다. 

[Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider)를 사용하여 Azure 구독 내에서 Azure Key Vault 공급자를 사용하도록 설정하고 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

암호화 키를 포함하는 Azure Key Vault와 저장소 및 VM과 같은 연결된 계산 리소스는 동일한 지역에 상주해야 합니다. [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault)를 사용하여 Azure Key Vault을 만들고 디스크 암호화에 사용할 Key Vault를 사용하도록 설정합니다. *keyVaultName*에 대한 고유한 Key Vault 이름을 다음과 같이 지정합니다.

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

소프트웨어 또는 HSM(하드웨어 보안 모델) 보호를 사용하여 암호화 키를 저장할 수 있습니다. HSM을 사용하려면 프리미엄 Key Vault가 필요합니다. 소프트웨어 보호 키를 저장하는 표준 Key Vault가 아닌 프리미엄 Key Vault를 만들려면 추가 비용이 소요됩니다. 프리미엄 Key Vault를 만들려면 앞의 단계에서 *-Sku "Premium"* 매개 변수를 추가합니다. 표준 Key Vault를 만들었기 때문에 다음 예제는 소프트웨어 보호 키를 사용합니다. 

두 가지 보호 모델 모두, 가상 디스크의 암호를 해독하기 위해 VM이 부팅될 때 암호화 키를 요청하려면 Azure 플랫폼에 액세스 권한이 허용되어야 합니다. [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey)를 사용하여 Key Vault에 암호화 키를 만듭니다. 다음 예제는 *myKey*라는 키를 만듭니다.

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Azure Active Directory 서비스 사용자 만들기
가상 디스크가 암호화되거나 암호가 해독될 때 계정을 지정하여 Key Vault의 암호화 키 교환 및 인증을 처리합니다. 이 계정 즉, Azure Active Directory 서비스 사용자는 Azure 플랫폼이 VM을 대신하여 적절한 암호화 키를 요청하도록 허용합니다. 기본 Azure Active Directory 인스턴스를 구독 내에서 사용할 수 있지만 많은 조직이 전용 Azure Active Directory 디렉터리를 두고 있습니다.

[New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal)을 사용하여 Azure Active Directory에 서비스 주체를 만듭니다. 보안 암호를 지정하려면 [Azure Active Directory의 암호 정책 및 제한 사항](../../active-directory/authentication/concept-sspr-policy.md)을 따릅니다.

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

가상 디스크를 암호화하거나 암호를 해독하려면, Key Vault에 저장되어 있는 암호화 키에 대한 권한이 Azure Active Directory 서비스 사용자가 키를 읽는 것을 허용하도록 설정되어야 합니다. [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy)를 사용하여 Key Vault에 대한 사용 권한을 설정합니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기
암호화 프로세스를 테스트하기 위해 [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다. 다음 예제에서는 *Windows Server 2016 Datacenter* 이미지를 사용하여 *myVM*이라는 VM을 만듭니다. 자격 증명을 묻는 메시지가 표시되면 VM에 사용할 사용자 이름과 암호를 입력합니다.

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>가상 머신 암호화
가상 디스크를 암호화하기 위해서 이전의 구성 요소를 모두 가져옵니다.

1. Azure Active Directory 서비스 사용자 및 암호를 지정합니다.
2. 암호화된 디스크에 대한 메타데이터를 저장할 Key Vault를 지정합니다.
3. 실제 암호화 및 암호 해독에 사용될 암호화 키를 지정합니다.
4. OS 디스크, 데이터 디스크 또는 모든 디스크를 암호화할지 여부를 지정합니다.

Azure Key Vault 키 및 Azure Active Directory 서비스 주체 자격 증명을 사용하여 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension)을 통해 VM을 암호화합니다. 다음 예제에서는 모든 주요 정보를 검색한 후 *myVM*라는 VM을 암호화합니다.

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

확인 메시지를 수락하여 VM 암호화를 계속합니다. 이 프로세스 동안 VM이 다시 시작됩니다. 암호화 프로세스가 완료되고 VM이 재부팅되면 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus)를 사용하여 암호화 상태를 검토합니다.

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

다음 예제와 유사하게 출력됩니다.

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>다음 단계
* Azure Key Vault 관리 방법에 대한 자세한 내용은 [가상 머신에 대한 Key Vault 설정](key-vault-setup.md)을 참조하세요.
* Azure에 업로드할 암호화된 사용자 지정 VM 준비와 같은 디스크 암호화에 대한 자세한 내용은 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.
