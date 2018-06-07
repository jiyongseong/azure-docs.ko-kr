---
title: Azure Service Fabric 클러스터 인증서 롤오버 | Microsoft Docs
description: 인증서 일반 이름으로 식별된 Service Fabric 클러스터 인증서를 롤오버하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: df919e23fd608cdf41e93844f13342ca00657adb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서를 수동으로 롤오버
Service Fabric 클러스터 인증서가 만료될 시간이 다가오면 인증서를 업데이트해야 합니다.  클러스터가 지문 대신 [일반 이름을 기준으로 인증서를 사용하도록 설정](service-fabric-cluster-change-cert-thumbprint-to-cn.md)된 경우 인증서를 롤오버하는 방법은 간단합니다.  인증 기관에서 새 만료 날짜와 함께 새 인증서를 얻습니다.  Azure Portal에서 Service Fabric 클러스터를 배포할 때 생성된 인증서를 포함하여 자체 서명된 인증서는 지원되지 않습니다.  새 인증서의 일반 이름이 이전 인증서와 동일해야 합니다. 

다음 스크립트는 새 인증서를 키 자격 증명 모음에 업로드하고 가상 머신 확장 집합에 인증서를 설치합니다.  Service Fabric 클러스터는 만료 날짜가 최신인 인증서를 자동으로 사용합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

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

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

자세한 내용은 다음 항목을 읽어보세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* [클러스터 인증서 업데이트 및 관리](service-fabric-cluster-security-update-certs-azure.md)

