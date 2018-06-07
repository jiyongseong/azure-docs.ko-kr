---
title: Azure Key Vault의 고객 관리 키를 사용하는 Azure Storage 서비스 암호화 | Microsoft Docs
description: Azure Storage 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob Storage를 암호화하고 고객 관리 키를 사용하여 데이터를 검색할 때 암호 해독합니다.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화

Microsoft Azure는 고객 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호하도록 지원하는 데 최선을 다하고 있습니다. Azure Storage가 데이터를 보호하는 한 가지 방법은 데이터를 저장소에 쓸 때 암호화하고 데이터를 검색할 때 암호를 해독하는 SSE(Storage 서비스 암호화)를 사용하는 것입니다. 암호화 및 암호 해독은 자동으로 적용되며 투명하고, 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화](https://wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다.

SSE를 사용하는 Microsoft 관리 암호화 키를 사용하거나 사용자 고유의 암호화 키를 사용할 수 있습니다. 이 문서에서는 사용자 고유의 암호화 키를 사용하는 방법을 설명합니다. Microsoft 관리 키 사용 또는 일반적인 SSE에 대한 자세한 내용은 [미사용 데이터에 대한 Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요.

Blob 및 파일 저장을 위한 SSE는 Azure Key Vault와 통합되므로 Key Vault를 사용하여 암호화 키를 관리할 수 있습니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용하여 키를 관리 및 제어하고 키 사용을 감사할 수도 있습니다.

자체 키를 만드는 이유는 무엇일까요? 사용자 지정 키를 사용하면 좀 더 유연하게 작업할 수 있으며 액세스 제어를 만들고, 순환하고, 사용할 수 없게 설정하고, 정의할 수 있습니다. 또한 데이터를 보호하는 데 사용된 암호화 키를 감사할 수 있습니다.

## <a name="get-started-with-customer-managed-keys"></a>고객 관리 키 사용 시작

SSE에서 고객 관리 키를 사용하려면 새 Key Vault 및 키를 만들 수도 있고 기존 Key Vault 및 키를 사용할 수도 있습니다. 저장소 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. 

### <a name="step-1-create-a-storage-account"></a>1 단계: 저장소 계정 만들기

저장소 계정이 아직 없는 경우 먼저 저장소 계정을 만듭니다. 자세한 내용은 [새 저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>2단계: Blob 및 파일 저장소에 SSE 사용

고객 관리 키를 사용하여 SSE를 사용하려면 2개의 키 보호 기능인 일시 삭제 및 삭제 안 함도 사용하도록 설정해야 합니다. 이러한 설정은 키를 실수로 또는 의도적으로 삭제할 수 없도록 합니다. 악의적인 행위자 또는 랜섬웨어 공격으로부터 사용자를 보호하기 위해 키의 최대 보존 기간은 90일로 설정됩니다.

SSE를 위한 고객 관리 키를 프로그래밍 방식으로 확인하려면 [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp), [.NET용 Storage Resource Provider 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 또는 [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli)를 사용할 수 있습니다.

SSE에서 고객 관리 키를 사용하려면 저장소 계정에 저장소 계정 ID를 할당해야 합니다. 다음 PowerShell 명령을 실행하여 ID를 설정할 수 있습니다.

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

다음 PowerShell 명령을 실행하여 일시 삭제 및 삭제 안 함을 사용하도록 설정할 수 있습니다.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>3단계: 고객 관리 키로 암호화 사용

기본적으로 SSE는 Microsoft 관리 키를 사용합니다. [Azure Portal](https://portal.azure.com/)을 사용하여 저장소 계정에 대해 고객 관리 키로 SSE를 사용하도록 설정할 수 있습니다. 저장소 계정에 대한 **설정** 블레이드에서 **암호화**를 클릭합니다. 다음 그림과 같이 **사용자 고유 키 사용** 옵션을 선택합니다.

![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>4단계: 키 선택

키를 URI로 지정하거나 Key Vault의 키를 선택할 수 있습니다.

#### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

URI에서 키를 지정하려면 다음 단계를 수행합니다.

1. **키 URI 입력** 옵션을 선택합니다.  
2. **키 URI** 필드에 URI를 지정합니다.

    ![키 URI 입력을 사용하는 암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정 

Key Vault에서 키를 지정하려면 다음 단계를 수행합니다.

1. **Key Vault에서 선택** 옵션을 선택합니다.  
2. 사용하려는 키가 포함된 Key Vault를 선택합니다.
3. Key Vault에서 키를 선택합니다.

    ![암호화 사용자 고유의 키 사용 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

저장소 계정에 Key Vault에 대한 액세스 권한이 없는 경우 다음 그림과 같이 Azure Powershell 명령을 실행하여 액세스 권한을 부여할 수 있습니다.

![Key Vault에 대해 거부된 액세스를 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

또한 Azure Portal에서 Azure Key Vault로 이동하여 저장소 계정에 액세스 권한을 부여하는 방법으로 Azure Portal을 통해 액세스 권한을 부여할 수 있습니다.


다음 PowerShell 명령을 사용하여 위의 키를 기존 저장소 계정에 연결할 수 있습니다.
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>5단계: 저장소 계정에 데이터 복사

암호화되도록 새 저장소 계정에 데이터를 전송하려고 합니다. 자세한 내용은 [저장소 서비스 암호화의 FAQ](storage-service-encryption.md#faq-for-storage-service-encryption)를 참조하세요.

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>6단계: 암호화된 데이터 상태 쿼리

암호화된 데이터 상태 쿼리

## <a name="faq-for-sse-with-customer-managed-keys"></a>고객 관리 키를 사용하는 SSE에 대한 FAQ

**Q: Premium Storage를 사용 중입니다. SSE에서 고객 관리 키를 사용할 수 있나요?**

A: 예. Standard storage 및 Premium Storage 모두에서 Microsoft 관리 키 및 고객 관리 키를 사용하는 SSE가 지원됩니다.

**Q: Azure PowerShell 및 Azure CLI를 사용하여 고객 관리 키를 사용하는 SSE로 새 저장소 계정을 만들 수 있나요?**

A: 예.

**Q: SSE에서 고객 관리 키를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?**

A: Azure Key Vault 사용과 연관된 비용이 있습니다. 자세한 내용은 [Key Vault 가격](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요. 모든 저장소 계정에 대해 사용되도록 설정된 SSE에 대한 추가 비용은 없습니다.

**Q: 암호화 키에 대한 액세스를 해지할 수 있나요?**

A: 예, 언제든 액세스를 취소할 수 있습니다. 키에 대한 액세스를 취소하는 방법은 여러 가지가 있습니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요. 액세스를 취소하면 Azure Storage에서 계정 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 Blob에 대한 액세스가 효과적으로 차단됩니다.

**Q: 저장소 계정 및 키를 다른 지역에 만들 수 있나요?**

A: 아니요. 저장소 계정 및 Azure Key Vault/키는 동일한 지역에 있어야 합니다.

**Q: 저장소 계정을 만들면서 SSE용 고객 관리 키를 사용하도록 설정할 수 있나요?**

A: 아니요. 저장소 계정을 처음 만들 때는 SSE에 Microsoft에서 관리하는 키만 사용할 수 있습니다. 고객 관리 키를 사용하려면 저장소 계정 속성을 업데이트해야 합니다. REST 또는 저장소 클라이언트 라이브러리 중 하나를 사용하여 프로그래밍 방식으로 저장소 계정을 업데이트하거나 계정을 만든 후 Azure Portal을 사용하여 저장소 계정 속성을 업데이트할 수 있습니다.

**Q: SSE에서 고객 관리 키를 사용하면서 암호화를 사용하지 않을 수 있나요?**

A: 아니요. 암호화를 사용하지 않도록 설정할 수 없습니다. 암호화는 기본적으로 Blob, 파일, Table 및 Queue 저장소를 비롯한 모든 서비스에 대해 사용됩니다. 경우에 따라, Microsoft 관리 키에서 고객 관리 키로 또는 그 반대로 사용 방식을 전환할 수 있습니다.

**Q: 새 저장소 계정을 만들 때 기본적으로 SSE가 사용되도록 설정되어 있나요?**

A. SSE는 기본적으로 모든 저장소 계정과 Blob, 파일, Table 및 Queue 저장소를 비롯한 모든 서비스에 대해 사용됩니다.

**Q: 저장소 계정에서 고객 관리 키를 사용하여 SSE를 사용하도록 설정할 수 없습니다.**

A: Azure Resource Manager 저장소 계정인가요? 클래식 저장소 계정에서는 고객 관리 키가 지원되지 않습니다. 고객 관리 키를 사용하는 SSE는 Resource Manager 저장소 계정에서만 사용할 수 있습니다.

**Q: 일시 삭제 및 삭제 안 함은 어떤 기능인가요? 고객 관리 키에서 SSE를 사용하려면 이 설정을 사용하도록 지정해야 하나요?**

A: 고객 관리 키와 함께 SSE를 사용하려면 일시 삭제 및 삭제 안 함 기능을 사용하도록 설정해야 합니다. 이러한 설정은 키를 실수로 또는 의도적으로 삭제할 수 없도록 합니다. 악의적인 행위자 및 랜섬웨어 공격으로부터 사용자를 보호하기 위해 키의 최대 보존 기간은 90일로 설정됩니다. 이 설정은 사용하지 않도록 지정할 수 없습니다.

**Q: 고객 관리 키를 사용하는 SSE는 특정 지역에서만 허용되나요?**

A: 모든 지역에서 Blob 및 파일 저장소에 고객 관리 키 및 SSE를 사용할 수 있습니다.

**Q: 문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**

A: Storage 서비스 암호화에 대한 문제는 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계

-   개발자가 보안 응용 프로그램을 빌드하는 데 도움이 되는 포괄적인 보안 기능 집합에 대한 자세한 내용은 [Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

-   Azure Key Vault에 대한 개요는 [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?을 참조하세요.

-   Azure Key Vault 시작 방법은 [Azure Key Vault 시작](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)을 참조하세요.
