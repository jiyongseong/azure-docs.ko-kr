---
title: "Azure Backup을 사용하여 암호화된 VM의 Key Vault 키 및 암호 복원 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure Backup에서 Key Vault 키 및 암호를 복원하는 방법을 알아봅니다."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Azure Backup을 사용하여 암호화된 VM의 Key Vault 키 및 암호 복원
이 문서에서는 Key Vault에 키와 암호가 존재하지 않는 경우 Azure VM Backup을 사용하여 암호화된 Azure VM의 복원을 수행하는 방법을 설명합니다. 복원된 VM에 대한 키(키 암호화 키)와 암호(BitLocker 암호화 키)의 별도의 복사본을 유지하려는 경우 이러한 단계도 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* **암호화된 VM 백업** - 암호화된 Azure VM은 Azure Backup을 사용하여 백업됩니다. 암호화된 Azure VM을 백업하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md) 문서를 참조하세요.
* **Azure Key Vault 구성** – 키 및 암호를 복원해야 하는 Key Vault가 이미 있는지 확인합니다. Key Vault 관리에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md) 문서를 참조하세요.
* **디스크 복원** - [PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)를 사용하여 암호화된 VM에 대한 디스크를 복원하는 복원 작업을 트리거했는지 확인합니다. 이 작업은 복원할 암호화된 VM의 키 및 암호가 포함된 저장소 계정에 JSON 파일을 생성하기 때문입니다.

## <a name="get-key-and-secret-from-azure-backup"></a>Azure Backup에서 키 및 비밀 가져오기

> [!NOTE]
> 암호화된 VM의 디스크가 복원되면 다음 사항을 확인하세요.
> 1. [디스크 복원 섹션의 PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)에 설명된 것처럼 $details가 디스크 복원 작업 세부 정보로 채워집니다.
> 2. **키 및 비밀이 키 자격 증명 모음으로 복원된 후**에만 복원된 디스크에서 VM이 만들어져야 합니다.
>
>

복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Azure 저장소 컨텍스트를 설정하고 암호화된 VM에 대한 키 및 비밀 세부 정보가 포함된 JSON 구성 파일을 복원합니다.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>키 복원
위에 언급된 대상 경로에 JSON 파일이 생성되면 JSON에서 키 BLOB 파일을 생성하고 restore key cmdlet에 제공하여 해당 키(KEK)를 다시 키 자격 증명 모음에 지정합니다.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>암호 복원
위에서 생성된 JSON 파일을 사용하여 비밀 이름 및 값을 가져오고 set secret cmdlet에 제공하여 해당 비밀(BEK)을 다시 키 자격 증명 모음에 지정합니다. **VM이 BEK 및 KEK를 사용하여 암호화된 경우 이러한 cmdlet을 사용합니다.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

VM이 **BEK만 사용하여 암호화된 경우** JSON에서 비밀 Blob 파일을 생성하고 공급하여 비밀 cmdlet을 복원함으로써 비밀(BEK)을 다시 Key Vault에 추가합니다.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. $secretname 값은 $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl 출력을 참조하고 secrets/ 뒤의 텍스트를 사용하여 얻을 수 있습니다. 예를 들어 출력 비밀 URL은 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163이고 비밀 이름은 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA입니다.
> 2. DiskEncryptionKeyFileName 태그 값은 비밀 이름과 동일합니다.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>복원된 디스크에서 가상 컴퓨터 만들기
Azure VM Backup을 사용하여 암호화된 VM을 백업한 경우 위에 언급된 PowerShell cmdlet은 키 및 비밀을 Key Vault로 다시 복원하는 데 도움이 됩니다. 복원한 후에는 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 문서를 참조하여 복원된 디스크, 키 및 비밀에서 암호화된 VM을 만듭니다.

## <a name="legacy-approach"></a>기존의 접근 방식
위에 설명된 접근 방식이 모든 복구 지점에 유효합니다. 그렇지만 BEK 및 KEK를 사용하여 암호화된 VM의 경우는 복구 지점에서 키 및 비밀 정보를 가져오는 이전의 접근 방식도 2017년 7월 11일 이전의 복구 지점에 계속 유효합니다. [PowerShell 단계](backup-azure-vms-automation.md#restore-an-azure-vm)를 사용하여 암호화된 VM에 대한 디스크 복원 작업을 완료하면 $rp가 유효한 값으로 채워졌는지 확인합니다.

### <a name="restore-key"></a>키 복원
다음 cmdlet을 사용하여 복구 지점에서 키(KEK) 정보를 가져오고 restore key cmdlet에 제공하여 키 자격 증명 모음에 다시 지정합니다.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>암호 복원
다음 cmdlet을 사용하여 복구 지점에서 비밀(BEK) 정보를 가져오고 set secret cmdlet에 제공하여 키 자격 증명 모음에 다시 지정합니다.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. $secretname 값은 $rp1.KeyAndSecretDetails.SecretUrl 출력을 참조하고 secrets/ 뒤의 텍스트를 사용하여 얻을 수 있습니다. 예를 들어 출력 비밀 URL은 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163이고 비밀 이름은 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA입니다.
> 2. DiskEncryptionKeyFileName 태그 값은 비밀 이름과 동일합니다.
> 3. 키를 다시 복원한 후 [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet을 사용하여 DiskEncryptionKeyEncryptionKeyURL에 대한 값을 Key Vault에서 얻을 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계
키 및 비밀을 키 자격 증명 모음으로 다시 복원한 후에는 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 문서를 참조하여 복원된 디스크, 키 및 비밀에서 암호화된 VM을 만듭니다.
