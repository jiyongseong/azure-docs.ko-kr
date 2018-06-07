---
ms.assetid: ''
title: Azure Key Vault Storage 계정 키
description: 저장소 계정 키는 Azure Key Vault 간의 원활한 통합과 Azure Storage 계정에 대한 키 기반 액세스를 제공합니다.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault Storage 계정 키

Azure Key Vault Storage 계정 키 이전에는 개발자가 고유한 ASA(Azure Storage 계정) 키를 관리하고 수동으로 또는 외부 자동화를 통해 회전해야 했습니다. 이제 Key Vault Storage 계정 키가 Azure Storage 계정에 인증하기 위한 [Key Vault 암호](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)로 구현됩니다.

ASA(Azure Storage 계정) 키 기능이 비밀 회전을 자동으로 관리합니다. 또한 SAS(공유 액세스 서명)를 방법으로 제공하므로 ASA 키를 사용하여 직접 연결할 필요가 없습니다.

Azure Storage 계정에 대한 일반적인 내용은 [Azure storage 계정 정보](https://docs.microsoft.com/azure/storage/storage-create-storage-account)를 참조하세요.

## <a name="supporting-interfaces"></a>인터페이스 지원

[Key Vault 개발자 가이드](key-vault-developers-guide.md#coding-with-key-vault)에서 프로그래밍 및 스크립팅 인터페이스의 전체 목록 및 해당 링크를 찾을 수 있습니다.


## <a name="what-key-vault-manages"></a>Key Vault에서 관리하는 사항

관리되는 저장소 계정 키를 사용할 경우 Key Vault에서 사용자 대신 여러 가지 내부 관리 기능을 수행합니다.

- Azure Key Vault는 ASA(Azure Storage Account)의 키를 관리합니다.
    - 내부적으로 Azure Key Vault는 키와 Azure Storage 계정을 나열(동기화)할 수 있습니다.
    - Azure Key Vault는 정기적으로 키를 다시 생성(회전)합니다.
    - 키 값은 호출자에게 응답으로 반환되지 않습니다.
    - Azure Key Vault는 Storage 계정과 클래식 Storage 계정 둘 다의 키를 관리합니다.
- Azure Key Vault를 사용하면 자격 증명 모음/개체 소유자가 SAS(계정 또는 서비스 SAS) 정의를 만들 수 있습니다.
    - SAS 정의를 사용하여 만든 SAS 값은 REST URI 경로를 통해 암호로 반환됩니다. 자세한 내용은 [Azure Key Vault 저장소 계정 작업](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)을 참조하세요.

## <a name="naming-guidance"></a>명명 지침

- Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- SAS 정의 이름은 1-102자로, 0-9, a-z, A-Z만 포함해야 합니다.

## <a name="developer-experience"></a>개발자 환경

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault Storage 키 이전

개발자는 Azure storage에 액세스하기 위해 저장소 계정 키로 다음 작업을 수행해야 했습니다.
1. Azure App Service 응용 프로그램 설정 또는 다른 저장소에 연결 문자열 또는 SAS 토큰을 저장합니다.
1. 응용 프로그램 시작 시 연결 문자열 또는 SAS 토큰을 인출합니다.
1. [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)를 만들어서 저장소와 상호 작용합니다.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault Storage 키 이후

개발자가 [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient)를 만들고 활용하여 해당 저장에 SAS 토큰을 가져옵니다. 이후에 해당 토큰을 사용하여 [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)를 만듭니다.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>개발자 지침

- Key Vault에서만 ASA 키를 관리하도록 허용합니다. 직접 관리할 경우 Key Vault 프로세스를 방해할 수 있으므로 직접 관리하려고 하지 마세요.
- 둘 이상의 Key Vault 개체가 ASA 키를 관리하도록 허용하지 않습니다.
- ASA 키를 수동으로 다시 생성해야 하는 경우 Key Vault를 통해 다시 생성하는 것이 좋습니다.

## <a name="getting-started"></a>시작

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>RBAC(역할 기반 액세스 제어) 권한 설정

Azure Key Vault 응용 프로그램 ID는 저장소 계정의 키를 *나열*하고 *다시 생성*할 수 있는 권한이 필요합니다. 다음 단계를 따라 이러한 권한을 설정하세요.

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>작업 예제

다음 예제에서는 Key Vault로 관리되는 Azure Storage 계정 및 연결된 SAS(공유 액세스 서명) 정의를 만드는 방법을 보여 줍니다.

### <a name="prerequisite"></a>필수 요소

[RBAC(역할 기반 액세스 제어) 권한 설정](#setup-for-role-based-access-control-rbac-permissions)을 완료했는지 확인합니다.

### <a name="setup"></a>설정

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

그런 다음, **계정**에 대한 권한을 설정하여 Key Vault에서 모든 저장소 사용을 관리할 수 있는지 확인합니다. 아래 예제에서 Azure 계정은 _developer@contoso.com_입니다.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault 관리 저장소 계정 만들기

이제 Azure Key Vault에서 관리 저장소 계정을 만들고 저장소 계정의 액세스 키를 사용하여 SAS 토큰을 만듭니다.
- `-ActiveKeyName`은 'key2'를 사용하여 SAS 토큰을 생성합니다.
- `-AccountName`을 사용하여 관리 저장소 계정을 식별합니다. 아래에서 저장소 계정 이름을 사용하여 단순하게 유지하지만 모든 이름일 수 있습니다.
- `-DisableAutoRegenerateKey`는 저장소 계정 키를 다시 생성하지 않도록 지정합니다.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>키 다시 생성

Key Vault로 저장소 액세스 키를 주기적으로 다시 생성하려는 경우 다시 생성 기간을 설정할 수 있습니다. 아래에서 다시 생성 기간을 3일로 설정합니다. 3일 후에 Key Vault는 'key1'을 다시 생성하고 활성 키를 'key1'에서 'key2'로 교체합니다.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS 정의 설정

계정 SAS는 여러 권한을 통해 Blob service 액세스 권한을 제공합니다.
관리되는 저장소 계정에 대해 Key Vault에서 SAS 정의를 설정합니다.
- `-AccountName`은 Key Vault에서 관리 저장소 계정의 이름입니다.
- `-Name`은 저장소에서 SAS 토큰에 대한 식별자입니다.
- `-ValidityPeriod`는 생성된 SAS 토큰에 대한 만료 날짜를 설정합니다.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>SAS 토큰 가져오기

해당하는 SAS 토큰을 가져오고 저장소를 호출합니다. [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)을 실행할 때 `-SecretName`은 `AccountName` 및 `Name` 매개 변수의 입력을 사용하여 생성됩니다.

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>저장소 만들기

*$readSasToken*을 사용하는 액세스 시도는 실패하지만 *$writeSasToken*를 사용하여 액세스할 수 있습니다.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

쓰기 권한이 있는 SAS 토큰을 사용하면 저장소 BLOB 콘텐츠에 액세스할 수 있습니다.

### <a name="relevant-powershell-cmdlets"></a>관련 PowerShell cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Storage 계정 등록

예제: Key Vault 개체 소유자인 사용자는 Azure Key Vault에 저장소 계정 개체를 추가하여 저장소 계정을 등록합니다.

등록하는 동안 Key Vault는 계정을 등록하는 ID에 저장소 키를 *나열* 및 *다시 생성*할 수 있는 사용 권한이 있는지 확인해야 합니다. 이러한 사용 권한을 확인하기 위해 Key Vault는 인증 서비스에서 OBO(On Behalf Of) 토큰을 가져오고, 대상을 Azure Resource Manager로 설정하고, Azure Storage 서비스에 대한 *나열* 키 호출을 만듭니다. *list* 호출이 실패할 경우 Key Vault 개체 생성이 실패하며 HTTP 상태 코드 *사용할 수 없음*이 표시됩니다. 이러한 방식으로 나열된 키는 Key Vault 엔터티 저장소로 캐시됩니다.

Key Vault에서 ID에 *다시 생성* 권한이 있는지 확인해야 키 다시 생성에 대한 소유권을 가질 수 있습니다. OBO 토큰을 통한 ID 및 Key Vault의 첫 번째 파티 ID에 이러한 사용 권한이 있는지 확인하기 위해

- Key Vault는 저장소 계정 리소스에 대한 RBAC 권한을 나열합니다.
- Key Vault는 작업 및 비작업의 정규식 일치를 통해 응답의 유효성을 검사합니다.

[Key Vault - Managed Storage 계정 키 샘플](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167)에서 지원하는 몇 가지 예제를 찾습니다.

ID에 *다시 생성* 권한이 없거나 Key Vault의 첫 번째 파티 ID에 *나열* 또는 *다시 생성* 권한이 없는 경우 등록 요청이 실패하고 적절한 오류 코드 및 메시지가 반환됩니다.

OBO 토큰은 PowerShell 또는 CLI의 네이티브 클라이언트 응용 프로그램인 첫 번째 파티를 사용하는 경우에만 작동합니다.

## <a name="other-applications"></a>다른 응용 프로그램

- Key Vault 저장소 계정 키를 사용하여 생성된 SAS 토큰은 Azure storage 계정에 훨씬 더 제어된 액세스 권한을 부여합니다. 자세한 내용은 [공유 액세스 서명 사용](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [키, 비밀 및 인증서에 대한 정보](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 팀 블로그](https://blogs.technet.microsoft.com/kv/)
