---
ms.assetid: 
title: "Azure Key Vault - CLI로 일시 삭제를 사용하는 방법"
description: "CLI 코드 캡처를 통한 일시 삭제의 사용 사례 예제"
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>CLI로 Key Vault 일시 삭제를 사용하는 방법

Azure Key Vault의 일시 삭제 기능을 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 특히, 일시 삭제는 다음과 같은 시나리오를 해결합니다.

- Key Vault의 복구 가능한 삭제 지원
- Key Vault 개체(예: 키, 비밀 및 인증서)의 복구 가능한 삭제를 지원

## <a name="prerequisites"></a>필수 조건

- Azure CLI 2.0 - 사용자 환경에 이 단계가 없는 경우 [CLI 2.0을 사용한 Key Vault 관리](key-vault-manage-with-cli2.md)를 참조하세요.

CLI에 대한 Key Vault 관련 특정 참조 정보는 [Azure CLI 2.0 Key Vault 참조](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한

Key Vault 작업은 RBAC(역할 기반 액세스 제어) 권한을 통해 다음과 같이 별도로 관리됩니다.

| 작업 | 설명 | 사용자 권한 |
|:--|:--|:--|
|나열|삭제된 Key Vault를 나열합니다.|Microsoft.KeyVault/deletedVaults/read|
|복구|삭제된 Key Vault를 복구합니다.|Microsoft.KeyVault/vaults/write|
|제거|삭제된 Key Vault 및 모든 콘텐츠를 영구적으로 제거합니다.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

권한 및 액세스 제어에 대한 자세한 내용은 [Key Vault 보안](key-vault-secure-your-key-vault.md)을 참조하세요.

## <a name="enabling-soft-delete"></a>일시 삭제를 사용하도록 설정

삭제된 Key Vault 또는 Key Vault에 저장된 개체를 복구할 수 있도록 하려면 먼저 해당 Key Vault에 대한 일시 삭제를 사용하도록 설정해야 합니다.

### <a name="existing-key-vault"></a>기존 Key Vault 사용

ContosoVault라는 기존 Key Vault의 경우 다음과 같이 일시 삭제를 사용하도록 설정합니다. 

>[!NOTE]
>현재 Azure Resource Manager 리소스 조작을 사용하여 *enableSoftDelete* 속성을 Key Vault 리소스에 직접 작성해야 합니다.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>새로운 Key Vault

새로운 Key Vault에 대한 일시 삭제를 사용하도록 설정하는 것은 만들면서 생성 명령에 일시 삭제 사용 플래그를 추가하면 됩니다.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>일시 삭제 사용 확인

Key Vault에 일시 삭제가 사용할 수 있도록 설정되어 있는지 확인하려면 *show* 명령을 실행하여 ‘일시 삭제 사용?’ 특성과 해당 설정(true 또는 false)을 찾습니다.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>일시 삭제로 보호되는 Key Vault 삭제

Key Vault 삭제(또는 제거)에 대한 명령이 동일해도 해당 동작은 일시 삭제를 사용하도록 설정했는지 여부에 따라 달라집니다.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>일시 삭제가 활성화되어 있지 않은 Key Vault에 대한 이전 명령을 실행하는 경우 복구에 대한 옵션 없이 영구적으로 이 Key Vault 및 해당 콘텐츠 전체를 삭제하게 됩니다.

### <a name="how-soft-delete-protects-your-key-vaults"></a>일시 삭제가 Key Vault를 보호하는 방식

활성화된 일시 삭제 사용:

- Key Vault가 삭제되면 해당 리소스 그룹에서 제거되고, 예약된 네임스페이스에 배치됩니다. 이는 Key Vault가 생성되었던 위치에만 연결되어 있습니다. 
- 키, 비밀 및 인증서 같이 삭제된 Key Vault의 개체에는 액세스할 수 없으며 포함된 Key Vault가 삭제된 상태로 남아 있습니다. 
- 삭제된 상태의 Key Vault에 대한 DNS 이름은 여전히 예약되어 있으므로, 동일한 이름으로 새 Key Vault를 만들 수 없습니다.  

다음 명령을 사용하여 사용자 구독에 연결된 삭제된 상태 Key Vault를 볼 수도 있습니다.

```azurecli
az keyvault list-deleted
```

출력의 *리소스 ID*는 이 자격 증명 모음의 원본 리소스 ID를 참조합니다. 이제 이 Key Vault가 삭제된 상태로 있으므로 해당 리소스 ID를 사용하는 리소스가 없습니다. *ID* 필드를 사용하여 복구 또는 제거할 때 리소스를 식별할 수 있습니다. *예약된 제거 날짜* 필드는 이 삭제된 자격 증명 모음에 대해 어떤 작업도 수행되지 않는 경우 자격 증명 모음이 영구적으로 삭제(제거)될 때를 나타냅니다. *예약된 제거 날짜*를 계산하는 데 사용되는 기본 보존 기간은 90일입니다.

## <a name="recovering-a-key-vault"></a>Key Vault 복구

Key Vault를 복구하려면 Key Vault 이름, 리소스 그룹 및 위치를 지정해야 합니다. 삭제된 Key Vault의 위치 및 리소스 그룹은 Key Vault 복구 프로세스에서 필요하므로 적어 둡니다.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Key Vault를 복구하면 결과는 Key Vault의 원본 리소스 ID 사용하는 새 리소스입니다. Key Vault가 존재했던 리소스 그룹을 제거한 경우에는 동일한 이름을 사용하는 새 리소스 그룹을 만들어야 Key Vault를 복구할 수 있습니다.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault 개체 및 일시 삭제

일시 삭제가 활성화되어 있는 ‘ContosoVault’라는 Key Vault의 ‘ContosoFirstKey’ 키의 경우 여기에 어떻게 해당 키를 삭제하는지가 나와 있습니다.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

일시 삭제가 활성화되어 있는 Key Vault를 사용하면 삭제된 키를 명시적으로 나열하거나 검색할 때 외에는 삭제된 키가 여전히 삭제된 것으로 표시됩니다. 삭제된 상태의 키에서의 작업은 대부분 삭제된 키의 나열, 복구 또는 제거 외에는 실패하게 됩니다. 

예를 들어 Key Vault에서 삭제된 키 나열을 요청하려면 다음 명령을 사용합니다.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>전환 상태 

일시 삭제가 활성화되어 있는 Key Vault의 키를 삭제하는 경우 전환이 완료되기까지 몇 초 정도 걸릴 수 있습니다. 이 전환 상태 동안 키가 활성 상태 또는 삭제된 상태가 아닌 것으로 나타납니다. 이 명령은 ‘ContosoVault’라는 Key Vault에 있는 모든 삭제된 키를 나열합니다.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Key Vault 개체를 통해 일시 삭제 사용

Key Vault와 마찬가지로, 삭제된 키, 비밀 또는 인증서는 복구하거나 제거하지 않는 한 최대 90일 동안 삭제된 상태로 남아 있게 됩니다. 

#### <a name="keys"></a>구성

삭제된 키를 복구하려면 다음을 수행합니다.

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

키를 영구적으로 삭제하려면 다음을 수행합니다.

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>키 제거는 키를 영구적으로 삭제하며 복구할 수 없습니다.

**복구** 및 **제거** 작업은 Key Vault 액세스 정책에 연결된 자체 권한이 있습니다. 사용자 또는 서비스 주체가 **복구** 또는 **제거** 작업을 실행하려면 Key Vault 액세스 정책에서 해당 개체(키 또는 비밀)에 대한 각각의 권한이 있어야 합니다. ‘모든’ 바로 가기를 사용하여 사용자에게 모든 권한을 부여하는 경우 기본적으로 **제거** 권한은 Key Vault의 액세스 정책에 추가되지 않습니다. **제거** 권한을 명시적으로 부여해야 합니다. 예를 들어 다음 명령은 **제거**를 포함하는 *ContosoVault*의 키에 대한 여러 작업을 수행할 수 있도록 user@contoso.com 권한을 부여합니다.

#### <a name="set-a-key-vault-access-policy"></a>Key Vault 액세스 정책 설정

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> 이제 막 일시 삭제 활성화되도록 기존 Key Vault를 설정한 경우 **복구** 및 **제거** 권한이 없을 수도 있습니다.

#### <a name="secrets"></a>비밀

키와 마찬가지로 Key Vault의 비밀도 자체 명령으로 작동됩니다. 다음은 비밀을 삭제, 나열, 복구 및 제거하기 위한 암호입니다.

- SQLPassword라는 비밀 삭제: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Key Vault의 모든 삭제된 비밀 나열: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- 삭제된 상태의 비밀 복구: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- 삭제된 상태의 비밀 제거: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>비밀 제거는 비밀을 영구적으로 삭제하며 복구할 수 없습니다.

## <a name="purging-and-key-vaults"></a>제거 및 Key Vault

### <a name="key-vault-objects"></a>Key Vault 개체

키, 비밀 또는 인증서 제거는 해당 항목을 영구적으로 삭제하며 복구할 수 없습니다. 그러나 삭제된 개체가 포함된 Key Vault는 Key Vault의 다른 모든 개체처럼 그대로 유지됩니다. 

### <a name="key-vaults-as-containers"></a>컨테이너로써의 Key Vault
Key Vault를 제거하면 키, 비밀 및 인증서를 포함한 모든 콘텐츠가 영구적으로 삭제됩니다. Key Vault를 제거하려면 `az keyvault purge` 명령을 사용합니다. `az keyvault list-deleted` 명령을 사용하여 구독의 삭제된 Key Vault의 위치를 찾을 수 있습니다.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Key Vault 제거는 영구적으로 삭제하며 복구할 수 없습니다.

### <a name="purge-permissions-required"></a>제거 권한 필요
- 자격 증명 모음 및 해당 콘텐츠가 영구적으로 제거되도록 삭제된 Key Vault를 제거하려면 사용자는 *Microsoft.KeyVault/locations/deletedVaults/purge/action* 작업을 수행하기 위한 RBAC 권한이 필요합니다. 
- 삭제된 키를 나열하려면 사용자는 *Microsoft.KeyVault/deletedVaults/read* 권한을 수행하기 위한 RBAC 권한이 필요합니다. 
- 기본적으로 구독 관리자만 이러한 권한을 갖습니다. 

### <a name="scheduled-purge"></a>예약된 제거

삭제된Key Vault 개체를 나열하면 Key Vault에 의해 제거되는 시기가 표시됩니다. *예약된 제거 날짜* 필드는 어떤 작업도 수행되지 않는 경우 Key Vault 개체가 영구적으로 삭제될 때를 나타냅니다. 기본적으로 삭제된 Key Vault 개체에 대한 보존 기간은 90일입니다.

>[!NOTE]
>해당 *예약된 제거 날짜* 필드에 의해 트리거되는 제거된 자격 증명 모음 개체가 영구적으로 삭제됩니다. 복구할 수는 없습니다.

## <a name="other-resources"></a>기타 리소스

- Key Vault의 일시 삭제 기능에 대한 자세한 내용은 [Azure Key Vault 일시 삭제 개요](key-vault-ovw-soft-delete.md)를 참조하세요.
- Azure Key Vault 사용의 일반적인 개요는 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요.

