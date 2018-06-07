---
title: Azure 스택에 안전 하 게 저장 된 인증서와 함께 가상 컴퓨터를 배포 | Microsoft Docs
description: 가상 컴퓨터를 배포 하 고 놓아서 인증서 스택 Azure 주요 자격 증명 모음을 사용 하 여 강제 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3950c9dfc5ff5f7ea1d170da086b4f97048ed81c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>가상 컴퓨터를 만들고는 스택 Azure 키 자격 증명 모음에서 검색 한 인증서를 설치 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

주요 자격 증명 모음 인증서가 설치 되어 있는 Azure 스택 가상 컴퓨터 (VM)를 만드는 방법에 알아봅니다.

## <a name="overview"></a>개요

인증서를 Active Directory 인증 또는 웹 트래픽을 암호화와 같은 대부분의 시나리오에서 사용 됩니다. 암호를 스택 Azure 키 자격 증명 모음에서으로 인증서를 안전 하 게 저장할 수 있습니다. Azure 스택 키 자격 증명 모음을 사용 하 여의 이점은 다음과 같습니다.

* 인증서는 스크립트, 명령줄 기록 또는 서식 파일에 노출 되지 않습니다.
* 인증서 관리 프로세스가 간소화 됩니다.
* 인증서에 액세스 하는 키의 제어할을 수 있습니다.

### <a name="process-description"></a>프로세스 설명

다음 단계는 가상 컴퓨터에 인증서를 푸시하는 데 필요한 프로세스를 설명 합니다.

1. 비밀 키 자격 증명 모음을 만듭니다.
2. Azuredeploy.parameters.json 파일을 업데이트 합니다.
3. 템플릿 배포

>[!NOTE]
>VPN을 통해 연결 되어 있는 경우 Azure 스택 개발 키트 또는 외부 클라이언트에서 다음이 단계를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 키 자격 증명 모음 서비스를 포함 하는 서비스를 구독 해야 합니다.
* [Azure 스택에 대 한 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>키 자격 증명 모음 암호 만들기

다음 스크립트는.pfx 형식으로 인증서를 만듭니다 주요 자격 증명 모음을 만들고는 암호로 키 자격 증명 모음에 인증서를 저장 합니다.

>[!IMPORTANT]
>사용 해야 합니다는 `-EnabledForDeployment` 키 오류를 만들 때 매개 변수입니다. 이 매개 변수를 사용 하면 키 자격 증명 모음에서 Azure 리소스 관리자 템플릿을 참조할 수 있습니다.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

앞의 스크립트를 실행 하면 출력 비밀 URI를 포함 합니다. 이 URI를 적어 둡니다. 참조 해야는 [푸시 인증서를 Windows 리소스 관리자 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)합니다. 다운로드는 [푸시 인증서 windows vm 템플릿을](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) 개발 컴퓨터에는 폴더입니다. 이 폴더에는 `azuredeploy.json` 및 `azuredeploy.parameters.json` 파일을 다음 단계에서 필요 합니다.

수정 된 `azuredeploy.parameters.json` 환경 값에 따라 파일입니다. 특별 한 관심의 매개 변수는 자격 증명 모음 이름, 자격 증명 모음 리소스 그룹 및 암호 (앞의 스크립트에서 생성) 대로 URI입니다. 다음 파일은 매개 변수 파일의 예:

## <a name="update-the-azuredeployparametersjson-file"></a>Azuredeploy.parameters.json 파일 업데이트

Azuredeploy.parameters.json 파일은 vaultName, 비밀 URI, VmName, 및 사용자 환경에 따라 다른 값을 업데이트 합니다. 다음 JSON 파일 템플릿 매개 변수 파일의 예를 보여 줍니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>템플릿 배포

다음 PowerShell 스크립트를 사용 하 여 템플릿을 배포 합니다.

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

템플릿이 성공적으로 배포 되 면 그 결과 다음과 같은 출력:

![템플릿 배포 결과](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Azure 스택 배포 중 가상 컴퓨터에 인증서를 푸시합니다. 인증서의 위치는 VM의 운영 체제에 따라 다릅니다.

* Windows에서 인증서는 LocalMachine 인증서의 위치에 있는 사용자 제공 되는 인증서 저장소에 추가 됩니다.
* Linux에서 인증서 아래에 배치 됩니다 /var/lib/waagent 디렉터리에 파일 이름의 &lt;UppercaseThumbprint&gt;.crt X509에 대 한 인증서 파일 및 &lt;UppercaseThumbprint&gt;.prv 개인 키에 대 한 .

## <a name="retire-certificates"></a>인증서를 사용 중지

인증서의 사용을 중지 하는 것은 인증서 관리 프로세스의 일부입니다. 인증서의 이전 버전을 삭제할 수 없지만 사용 하 여 비활성화할 수 있습니다는 `Set-AzureKeyVaultSecretAttribute` cmdlet.

다음 예제는 데 사용할 인증서를 사용 하지 않도록 설정 하는 방법을 보여 줍니다. 에 대 한 사용자 지정 값을 사용 하 여는 **VaultName**, **이름**, 및 **버전** 매개 변수입니다.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>다음 단계

* [Key Vault 암호를 사용하여 VM 배포](azure-stack-kv-deploy-vm-with-secret.md)
* [응용 프로그램이 키 자격 증명 모음에 액세스 하도록 허용](azure-stack-kv-sample-app.md)
