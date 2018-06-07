---
title: Azure Resource Manager 템플릿에서 Key Vault 비밀 | Microsoft Docs
description: 배포하는 동안 키 자격 증명 모음의 비밀을 매개 변수로 전달하는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/11/2018
ms.author: tomfitz
ms.openlocfilehash: 6a6c1f10b5a46633785d9c26a766df9334fe1cb0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달

배포 중에 보안 값(예: 암호)을 매개 변수로 전달해야 할 경우 [Azure Key Vault](../key-vault/key-vault-whatis.md)에서 값을 검색할 수 있습니다. 매개 변수 파일에서 Key Vault 및 비밀을 참조하여 이 값을 검색합니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다. 리소스를 배포할 때마다 수동으로 비밀 값을 입력하지 않아도 됩니다. Key Vault는 배포하는 리소스 그룹과는 다른 구독에 있을 수 있습니다. Key Vault를 참조할 때는 구독 ID를 포함합니다.

Key Vault를 만들 때는 *enabledForTemplateDeployment* 속성을 *true*로 설정합니다. 이 값을 true로 설정하면 배포 중에 Resource Manager 템플릿으로부터의 액세스가 허용됩니다.

## <a name="deploy-a-key-vault-and-secret"></a>키 자격 증명 모음 및 비밀 배포

Key Vault 및 비밀을 만들려면 Azure CLI 또는 PowerShell을 사용합니다. Key Vault가 템플릿 배포에 사용할 수 있도록 설정되었는지 확인합니다. 

Azure CLI의 경우 

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell의 경우 다음을 사용합니다.

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>비밀에 대한 액세스를 사용하도록 설정

새 Key Vault를 사용하든, 기존 Key Vault를 사용하든, 템플릿을 배포하는 사용자가 비밀에 액세스할 수 있어야 합니다. 비밀을 참조하는 템플릿을 배포하는 사용자에게는 Key Vault에 대한 `Microsoft.KeyVault/vaults/deploy/action` 권한이 있어야 합니다. [소유자](../role-based-access-control/built-in-roles.md#owner) 및 [참여자](../role-based-access-control/built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다.

## <a name="reference-a-secret-with-static-id"></a>정적 ID로 비밀 참조

Key Vault 비밀을 수신하는 템플릿은 다른 템플릿과 비슷합니다. **템플릿이 아닌 매개 변수 파일에서 Key Vault를 참조**하기 때문입니다. 다음 이미지에서는 매개 변수 파일이 암호를 참조하고 템플릿에 해당 값을 전달하는 방법을 보여줍니다.

![정적 ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

예를 들어 [다음 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)은 관리자 암호를 포함하는 SQL Database를 배포합니다. 암호 매개 변수는 보안 문자열로 설정됩니다. 하지만 이 템플릿은 해당 값이 제공되는 위치를 지정하지는 않습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

이제 위 템플릿용으로 매개 변수 파일을 만들어야 합니다. 매개 변수 파일에서 템플릿의 매개 변수 이름과 일치하는 매개 변수를 지정합니다. 매개 변수 값으로는 Key Vault의 비밀을 참조합니다. 키 자격 증명 모음의 리소스 식별자와 비밀 이름을 전달하여 암호를 참조합니다. [다음 매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)에는 키 자격 증명 모음 비밀이 이미 있어야 하고, 리소스 ID에 정적 값을 제공합니다. 이 파일을 로컬로 복사하고, 구독 ID, 자격 증명 모음 이름 및 SQL Server 이름을 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

현재 버전이 아닌 암호 버전을 사용해야 할 경우 `secretVersion` 속성을 사용합니다.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

이제 템플릿을 배포하고 매개 변수 파일을 전달합니다. GitHub에서 예제 템플릿을 사용할 수 있지만 사용자 환경에 설정한 값이 포함된 로컬 매개 변수 파일을 사용해야 합니다.

Azure CLI의 경우 

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>동적 ID로 비밀 참조

이전 섹션에서는 키 자격 증명 모음 비밀에 대해 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 하지만, 일부 시나리오에서는, 현재 배포를 기반으로 달라지는 키 자격 증명 모음 비밀을 참조해야 합니다. 이런 경우, 매개 변수 파일에 리소스 ID를 하드 코딩할 수 없습니다. 아쉽게도, 매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다.

키 자격 증명 모음 비밀에 대한 리소스 ID를 동적으로 생성하려면, 비밀이 필요한 리소스를 연결된 템플릿으로 이동해야 합니다. 부모 템플릿에서 연결된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다. 다음 이미지에서는 연결된 템플릿의 매개 변수가 암호를 참조하는 방법을 보여줍니다.

![동적 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

연결된 템플릿은 외부 URI를 통해 사용할 수 있어야 합니다. 일반적으로 저장소 계정에 템플릿을 추가하고 `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`과 같은 URI를 통해 액세스합니다.

[다음 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json)은 동적으로 키 자격 증명 모음 ID를 만들고 매개 변수로 전달합니다. GitHub에서 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)에 연결합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

이전 템플릿을 배포하고 매개 변수에 값을 제공합니다. GitHub에서 예제 템플릿을 사용할 수 있지만 사용자 환경에 매개 변수 값을 제공해야 합니다.

Azure CLI의 경우 

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>다음 단계
* 키 자격 증명 모음에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
* 키 비밀을 참조하는 전체 예제는 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.
