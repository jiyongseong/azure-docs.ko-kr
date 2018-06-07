---
title: Azure API Management에서 Azure 관리 서비스 ID 사용 | Microsoft Docs
description: API Management에서 Azure 관리 서비스 ID 사용 방법 알아보기
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 98aa70935a3efbbe2edb07aade85fa3ea17ce786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150433"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure API Management에서 Azure 관리 서비스 ID 사용

이 문서에서는 API Management 서비스 인스턴스에 대한 관리 서비스 ID를 만드는 방법과 다른 리소스에 액세스하는 방법을 보여 줍니다. Azure AD(Azure Active Directory)에서 생성된 관리 서비스 ID를 사용하면 API Management 인스턴스에서 Azure Key Vault처럼 Azure AD로 보호되는 다른 리소스에 쉽고 안전하게 액세스할 수 있습니다. 이 관리 서비스 ID는 Azure에서 관리하며 암호를 프로비전하거나 순환할 필요가 없습니다. Azure 관리 서비스 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 서비스 ID](../active-directory/msi-overview.md)를 참조하세요.

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>API Management 인스턴스에 대한 관리되는 서비스 ID 만들기

### <a name="using-the-azure-portal"></a>Azure Portal 사용

포털에서 관리되는 서비스 id를 설정하려면 먼저 평소와 같은 방법으로 API Management 인스턴스를 만든 다음, 기능을 설정합니다.

1. 평소처럼 포털에서 API Management 인스턴스를 만듭니다. 포털에서 해당 앱으로 이동합니다.
2. **관리되는 서비스 ID**를 선택합니다.
3. Azure Active Directory로 등록을 켜기로 전환합니다. 저장을 클릭합니다.

![MSI 사용](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

API Management 인스턴스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다. 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

이렇게 하면 API Management 인스턴스에 대한 ID를 만들어서 관리하도록 Azure에 지시됩니다. 

예를 들어 전체 Azure Resource Manager 템플릿은 다음과 같이 보일 수 있습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>관리되는 서비스 ID를 사용하여 다른 리소스에 액세스

> [!NOTE]
> 현재 API Management 사용자 지정 도메인 이름에 대해 Azure Key Vault에서 인증서를 가져오는 데 관리되는 서비스 ID를 사용할 수 있습니다. 더 많은 시나리오는 곧 지원될 예정입니다.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Azure Key Vault에서 인증서 가져오기

#### <a name="prerequisites"></a>필수 조건
1. pfx 인증서가 포함된 Key Vault는 API Management 서비스와 동일한 Azure 구독 및 리소스 그룹에 있어야 합니다. 이것은 Azure Resource Manager 템플릿의 요구 사항입니다. 
2. 비밀의 콘텐츠 형식은 *application/x-pkcs12*이어야 합니다. 다음 스크립트를 사용하여 인증서를 업로드합니다.

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> 인증서의 개체 버전이 제공되지 않은 경우 API Management는 인증서의 최신 버전이 Key Vault로 업로드된 후에 자동으로 해당 인증서를 가져옵니다. 

다음 예제에서는 다음 단계를 포함하는 Azure Resource Manager 템플릿을 보여줍니다.

1. 관리되는 서비스 ID를 사용하여 API Management 인스턴스를 만듭니다.
2. Azure Key Vault 인스턴스의 액세스 정책을 업데이트하고 API Management 인스턴스가 비밀을 가져올 수 있도록 허용합니다.
3. Key Vault 인스턴스의 인증서를 통해 사용자 지정 도메인 이름을 설정하여 API Management 인스턴스를 업데이트합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>다음 단계

Azure 관리 서비스 ID에 대해 자세히 알아봅니다.

* [Azure 리소스용 관리 서비스 ID](../active-directory/msi-overview.md)
* [Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates)

