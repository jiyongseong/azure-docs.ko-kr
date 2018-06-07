---
title: Azure 스택에 안전 하 게 저장 된 암호를 사용 하 여 VM 배포 | Microsoft Docs
description: Azure 스택 키 자격 증명 모음에 저장 된 암호를 사용 하는 VM을 배포 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Azure 스택 주요 자격 증명 모음에 저장 된 보안 암호를 사용 하 여 가상 컴퓨터 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이 문서는 Azure 스택 키 자격 증명 모음에 저장 된 암호를 사용 하 여 Windows Server 가상 컴퓨터를 배포 하는 과정 단계입니다. 주요 자격 증명 모음 암호를 사용 하는 일반 텍스트 암호를 전달 하는 보다 더 안전 합니다.

## <a name="overview"></a>개요

스택의 Azure 키 자격 증명 모음에서 암호로 암호 등의 값을 저장할 수 있습니다. 암호를 만든 후에 Azure 리소스 관리자 템플릿을에서 참조할 수 있습니다. 리소스 관리자와 암호를 사용 하 여 다음과 같은 이점을 제공 합니다.

* 리소스를 배포할 때마다 암호를 수동으로 입력할 필요가 없습니다.
* 암호에 액세스할 수 있는 사용자 또는 서비스 사용자를 지정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 키 자격 증명 모음 서비스를 포함 하는 서비스를 구독 해야 합니다.
* [Azure 스택에 대 한 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경을 구성 합니다.](azure-stack-powershell-configure-user.md)

다음 단계를 키 자격 증명 모음에 저장 된 암호를 검색 하 여 가상 컴퓨터를 만드는 데 필요한 프로세스를 설명 합니다.

1. 비밀 키 자격 증명 모음을 만듭니다.
2. Azuredeploy.parameters.json 파일을 업데이트 합니다.
3. 템플릿을 배포합니다.

>[참고] VPN을 통해 연결 되어 있는 경우 Azure 스택 개발 키트 또는 외부 클라이언트에서 다음이 단계를 사용할 수 있습니다.

## <a name="create-a-key-vault-secret"></a>키 자격 증명 모음 암호 만들기

다음 스크립트는 주요 자격 증명 모음을 만들고는 암호로 키 자격 증명 모음에 암호를 저장 합니다. 사용 하 여는 `-EnabledForDeployment` 주요 자격 증명 모음을 만들 때 매개 변수입니다. 이 매개 변수는 키 자격 증명 모음에서 Azure 리소스 관리자 템플릿을 참조할 수 있도록 합니다.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

앞의 스크립트를 실행 하면 출력 비밀 URI를 포함 합니다. 이 URI를 적어 둡니다. 참조 해야는 [주요 자격 증명 모음 서식 파일에 암호를 가진 가상 컴퓨터를 Windows 배포](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)합니다. 다운로드는 [101-vm-secure-암호](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) 개발 컴퓨터에는 폴더입니다. 이 폴더에는 `azuredeploy.json` 및 `azuredeploy.parameters.json` 파일을 다음 단계에서 필요 합니다.

수정 된 `azuredeploy.parameters.json` 환경 값에 따라 파일입니다. 특별 한 관심의 매개 변수는 자격 증명 모음 이름, 자격 증명 모음 리소스 그룹 및 암호 (앞의 스크립트에서 생성) 대로 URI입니다. 다음 파일은 매개 변수 파일의 예:

## <a name="update-the-azuredeployparametersjson-file"></a>Azuredeploy.parameters.json 파일 업데이트

KeyVault uri가 secretName, 사용자 환경에 따라 가상 컴퓨터 값의 adminUsername azuredeploy.parameters.json 파일을 업데이트 합니다. 다음 JSON 파일 템플릿 매개 변수 파일의 예를 보여 줍니다.

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>템플릿 배포

다음 PowerShell 스크립트를 사용 하 여 템플릿을 배포 이제:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

템플릿이 성공적으로 배포 되 면 그 결과 다음과 같은 출력:

![배포 출력](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>다음 단계

[키 자격 증명 모음 샘플 응용 프로그램 배포](azure-stack-kv-sample-app.md)

[주요 자격 증명 모음 인증서를 사용 하 여 VM 배포](azure-stack-kv-push-secret-into-vm.md)
