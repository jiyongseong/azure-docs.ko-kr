---
title: Azure 서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시 | Microsoft Docs
description: 조직의 구성원을 위한 Azure 관리되는 응용 프로그램을 만드는 방법이 나와 있습니다.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: b7f8bbcad39000e7e71149824535a6a82b26c758
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305313"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>내부 사용을 위한 관리되는 응용 프로그램 게시

조직의 구성원을 위한 Azure [관리되는 응용 프로그램](overview.md)을 만들고 게시할 수 있습니다. 예를 들어 조직 표준을 준수하도록 하는 IT 부서에서 관리되는 응용 프로그램을 게시할 수 있습니다. 이러한 관리되는 응용 프로그램은 Azure Marketplace가 아닌 서비스 카탈로그를 통해 사용할 수 있습니다.

서비스 카탈로그에 대한 관리되는 응용 프로그램을 게시하려면 다음을 수행해야 합니다.

* 관리되는 응용 프로그램과 함께 배포할 리소스를 정의하는 템플릿을 만듭니다.
* 관리 되는 응용 프로그램을 배포할 때 포털에 대한 사용자 인터페이스 요소를 정의합니다.
* 필요한 템플릿 파일이 포함된 .zip 패키지를 만듭니다.
* 사용자의 구독에 속한 리소스 그룹에 액세스해야 하는 사용자, 그룹 또는 응용 프로그램을 결정합니다.
* .zip 패키지를 나타내고 ID에 대한 액세스를 요청하는 관리되는 응용 프로그램 정의를 만듭니다.

이 문서에서는 관리되는 응용 프로그램에는 하나의 저장소 계정만 포함됩니다. 관리되는 응용 프로그램을 게시하는 단계를 설명하기 위한 것입니다. 전체 예제는 [Azure 관리되는 응용 프로그램의 샘플 프로젝트](sample-projects.md)를 참조하세요.

## <a name="create-the-resource-template"></a>리소스 템플릿 만들기

모든 관리되는 응용 프로그램 정의는 이름이 **mainTemplate.json**인 파일을 포함합니다. 이 안에서 프로비전할 Azure 리소스를 정의합니다. 템플릿은 일반 Resource Manager 템플릿과 차이가 없습니다.

이름이 **mainTemplate.json**인 파일을 만듭니다. 이름은 대/소문자를 구분합니다.

파일에 다음 JSON을 추가합니다. 저장소 계정을 만들기 위한 매개 변수를 정의하고 저장소 계정의 속성을 지정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

mainTemplate.json 파일을 저장합니다.

## <a name="create-the-user-interface-definition"></a>사용자 인터페이스 정의 만들기

Azure Portal은 **createUiDefinition.json** 파일을 사용하여 관리되는 응용 프로그램을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다. 사용자가 각 매개 변수에 대한 입력을 제공하는 방법을 정의합니다. 드롭다운 목록, 텍스트 상자, 암호 상자 및 기타 입력 도구와 같은 옵션을 사용할 수 있습니다. 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.

이름이 **createUiDefinition.json**인 파일을 만듭니다. 이름은 대/소문자를 구분합니다.

파일에 다음 JSON을 추가합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

createUiDefinition.json 파일을 저장합니다.

## <a name="package-the-files"></a>파일을 패키지로 만들기

이름이 app.zip인 .zip 파일에 두 파일을 추가합니다. 두 파일이 .zip 파일의 루트 수준에 있어야 합니다. 폴더에 배치하는 경우 관리되는 응용 프로그램 정의를 만들 때 필요한 파일이 없음을 나타내는 오류가 나타납니다. 

패키지를 사용할 수 있는 액세스 가능한 위치에 패키지를 업로드합니다. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>관리되는 응용 프로그램 정의 만들기

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory 사용자 그룹 또는 응용 프로그램 만들기

다음 단계는 고객을 대신하여 리소스를 관리하기 위한 사용자 그룹 또는 응용 프로그램을 선택하는 것입니다. 이 사용자 그룹 또는 응용 프로그램에는 할당된 역할에 따라 관리되는 리소스 그룹에 대한 권한이 있습니다. 역할은 소유자 또는 참가자와 같은 기본 제공 RBAC(역할 기반 Access Control) 역할일 수 있습니다. 개별 사용자에게도 리소스를 관리할 수 있는 권한을 부여할 수 있지만, 일반적으로 사용자 그룹에 이 권한을 할당합니다. 새 Active Directory 사용자 그룹을 만들려면 [그룹을 만들고 Azure Active Directory에 구성원 추가](../active-directory/active-directory-groups-create-azure-portal.md)를 참조하세요.

리소스 관리에 사용할 사용자 그룹의 개체 ID가 필요합니다. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>역할 정의 ID 가져오기

다음으로 사용자, 사용자 그룹 또는 응용 프로그램에 대한 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 일반적으로 소유자 또는 참가자 또는 읽기 권한자 역할을 사용합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>관리되는 응용 프로그램 정의 만들기

관리되는 응용 프로그램 정의를 저장하기 위한 리소스 그룹이 아직 없는 경우 지금 새로 만듭니다.

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

이제 관리되는 응용 프로그램 정의 리소스를 만듭니다.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application"></a>관리되는 응용 프로그램 만들기

포털, PowerShell 또는 Azure CLI를 통해 관리되는 응용 프로그램을 배포할 수 있습니다.

### <a name="powershell"></a>PowerShell

먼저 PowerShell을 사용하여 관리되는 응용 프로그램을 배포합니다.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

이제 관리되는 응용 프로그램 및 관리되는 인프라가 구독에 존재합니다.

### <a name="portal"></a>포털

이제 포털을 사용하여 관리되는 응용 프로그램을 배포합니다. 패키지에서 만든 사용자 인터페이스를 확인합니다.

1. Azure Portal로 이동합니다. **+ 리소스 만들기**를 선택하고 **서비스 카탈로그**를 검색합니다.

   ![Search 서비스 카탈로그](./media/publish-service-catalog-app/create-new.png)

1. **서비스 카탈로그 관리되는 응용 프로그램**을 선택합니다.

   ![서비스 카탈로그 선택](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. **만들기**를 선택합니다.

   ![만들기 선택](./media/publish-service-catalog-app/select-create.png)

1. 사용 가능한 솔루션의 목록에서 만들려는 관리되는 응용 프로그램을 찾아 선택합니다. **만들기**를 선택합니다.

   ![관리되는 응용 프로그램 찾기](./media/publish-service-catalog-app/find-application.png)

1. 관리되는 응용 프로그램에 필요한 기본 정보를 제공합니다. 관리되는 응용 프로그램을 포함할 구독 및 새 리소스 그룹을 지정합니다. 위치에 **미국 중서부**를 선택합니다. 완료되면 **확인**을 선택합니다.

   ![관리되는 응용 프로그램 매개 변수 제공](./media/publish-service-catalog-app/add-basics.png)

1. 관리되는 응용 프로그램의 리소스에 관련된 값을 제공합니다. 완료되면 **확인**을 선택합니다.

   ![리소스 매개 변수 제공](./media/publish-service-catalog-app/add-storage-settings.png)

1. 템플릿에서 사용자가 제공한 값의 유효성을 검사합니다. 유효성 검사에 성공하면 **확인**을 선택하여 배포를 시작합니다.

   ![관리되는 응용 프로그램 유효성 검사](./media/publish-service-catalog-app/view-summary.png)

배포가 완료되면 관리되는 응용 프로그램은 applicationGroup이라는 리소스 그룹에 포함됩니다. 저장소 계정은 applicationGroup이라는 리소스 그룹과 해시된 문자열 값에 들어 있습니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [관리되는 응용 프로그램 개요](overview.md)를 참조하세요.
* 예제 프로젝트는 [Azure 관리되는 응용 프로그램의 샘플 프로젝트](sample-projects.md)를 참조하세요.
* 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
