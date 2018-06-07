---
title: 인증서 일반 이름을 사용하여 Azure Service Fabric 클러스터 만들기 | Microsoft Docs
description: 템플릿의 인증서 일반 이름을 사용하여 Service Fabric 클러스터를 만드는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>지문 대신 인증서 일반 이름을 사용하는 Service Fabric 클러스터 배포
두 인증서가 동일한 지문을 사용하면 안 됩니다. 이렇게 될 경우 클러스터 인증서가 롤오버되거나 관리에 어려움이 발생합니다. 그러나 여러 인증서가 동일한 일반 이름 또는 제목을 사용하는 것은 가능합니다.  인증서 일반 이름을 사용하는 클러스터는 인증서 관리가 훨씬 간단합니다. 이 문서에서는 인증서 지문 대신 인증서 일반 이름을 사용하는 Service Fabric 클러스터를 배포하는 방법을 설명합니다.
 
## <a name="get-a-certificate"></a>인증서 얻기
먼저 [CA(인증 기관)](https://wikipedia.org/wiki/Certificate_authority)에서 인증서를 얻습니다.  인증서 일반 이름이 클러스터의 호스트 이름이어야 합니다.  예: "myclustername.southcentralus.cloudapp.azure.com".  

테스트를 위해 무료 또는 오픈 인증 기관에서 CA 서명 인증서를 얻을 수 있습니다.

> [!NOTE]
> Azure Portal에서 Service Fabric 클러스터를 배포할 때 생성된 인증서를 포함하여 자체 서명된 인증서는 지원되지 않습니다.

## <a name="upload-the-certificate-to-a-key-vault"></a>키 자격 증명 모음에 인증서 업로드
Azure에서 Service Fabric 클러스터는 가상 머신 확장 집합에 배포됩니다.  키 자격 증명 모음에 인증서를 업로드합니다.  클러스터가 배포되면 클러스터가 실행되고 있는 가상 머신 확장 집합에 인증서를 설치합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>샘플 템플릿 다운로드 및 업데이트
이 문서에서는 [5노드 보안 클러스터](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) 예제 템플릿과 템플릿 매개 변수를 사용합니다. 컴퓨터에 *azuredeploy.json* 및 *azuredeploy.parameters.json* 파일을 다운로드합니다.

### <a name="update-parameters-file"></a>매개 변수 파일 업데이트
먼저 텍스트 편집기에서 *azuredeploy.parameters.json* 파일을 열고 다음 매개 변수 값을 추가합니다.
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

다음으로 *certificateCommonName*, *sourceVaultValue* 및 *certificateUrlValue* 매개 변수 값을 이전 스크립트에서 반환된 값으로 설정합니다.
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>템플릿 파일 업데이트
다음으로 텍스트 편집기에서 *azuredeploy.json* 파일을 열고 인증서 일반 이름을 지원하도록 세 가지를 업데이트합니다.

1. **매개 변수** 섹션에서 *certificateCommonName* 매개 변수를 추가합니다.
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    또한 *certificateThumbprint*이 더 이상 필요 없으면 제거해도 됩니다.

2. *sfrpApiVersion* 변수 값을 "2018-02-01"로 설정합니다.
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. **Microsoft.Compute/virtualMachineScaleSets** 리소스에서 지문 대신 인증서 설정의 일반 이름을 사용하도록 가상 머신 확장을 업데이트합니다.  **virtualMachineProfile**->**extenstionProfile**->**확장**->**속성** -> **설정**->**인증서**에서 `"commonNames": ["[parameters('certificateCommonName')]"],`을 추가하고 `"thumbprint": "[parameters('certificateThumbprint')]",`를 제거합니다.
    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  **Microsoft.ServiceFabric/clusters** 리소스에서 API 버전을 "2018-02-01"로 업데이트합니다.  또한 다음 예제와 같이 **commonNames** 속성을 사용하여 **certificateCommonNames** 설정을 추가하고 **인증서** 설정을 제거(지문 속성을 사용하여)합니다.
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
변경 후 업데이트된 템플릿을 다시 배포합니다.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>다음 단계
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* [클러스터 인증서 롤오버](service-fabric-cluster-rollover-cert-cn.md) 방법 알아보기
* [클러스터 인증서 업데이트 및 관리](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png