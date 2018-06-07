---
title: Azure에서 HPC 팩 2016 클러스터 | Microsoft Docs
description: Azure에서 HPC 팩 2016 클러스터를 배포하는 방법에 대해 알아봅니다
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: 91f067de33d1ff4bc272773e3db49de47fac2feb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Azure에서 HPC 팩 2016 클러스터 배포

이 문서의 단계에 따라 Azure 가상 머신에서 [Microsoft HPC 팩 2016 업데이트 1](https://technet.microsoft.com/library/cc514029) 클러스터를 배포합니다. HPC Pack은 Microsoft Azure 및 Windows Server 기술로 구축된 Microsoft의 무료 HPC 솔루션으로, 광범위한 HPC 워크로드를 지원합니다.

[Azure Resource Manager 템플릿](https://github.com/MsHpcPack/HPCPack2016) 중 하나를 사용하여 HPC 팩 2016 클러스터를 배포할 수 있습니다. 클러스터 헤드 노드의 숫자 및 형식이 다른 클러스터 토폴로지를 선택하고 계산 노드를 사용할지 선택합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="pfx-certificate"></a>PFX 인증서

Microsoft HPC 팩 2016 클러스터에는 HPC 노드 간 통신을 보호하기 위해 PFX(개인 정보 교환) 인증서가 필요합니다. 인증서는 다음 요구 사항을 충족해야 합니다.

* 키 교환이 가능한 개인 키가 있어야 함
* 디지털 서명 및 키 암호화를 포함하는 키 사용
* 클라이언트 인증 및 서버 인증을 포함하는 강화된 키 사용

이러한 요구 사항을 충족하는 인정서가 아직 없는 경우 인증 기관에서 인증서를 요청할 수 있습니다. 또는 다음 명령을 사용하여 명령을 실행하는 운영 체제에 따라 자체 서명된 인증서를 생성합니다. 그런 다음, 개인 키가 있는 암호로 보호된 PFX 파일로 인증서를 내보냅니다.

* **Windows 10 또는 Windows Server 2016의 경우**, 다음과 같이 기본 제공 **New-SelfSignedCertificate** PowerShell cmdlet를 실행합니다.

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Windows 10 또는 Windows Server 2016 이전의 운영 체제의 경우** Microsoft Script Center에서 [자체 서명 인증서 생성기](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)를 다운로드합니다. 그 내용을 추출하고 PowerShell 프롬프트에서 다음 명령을 실행합니다.

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

현재 사용자 저장소에 인증서를 만든 후 인증서 스냅인을 사용하여 개인 키가 있는 암호로 보호된 PFX 파일로 인증서를 내보냅니다. 또한 [Export-Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) PowerShell cmdlet을 사용하여 인증서를 내보낼 수도 있습니다.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Azure Key Vault에 인증서 업로드

HPC 클러스터를 배포하기 전에 PFX 인증서를 [Azure Key Vault](../../key-vault/index.yml)에 암호로 업로드하고 배포하는 동안 사용할 **자격 증명 모음 이름**, **자격 증명 모음 리소스 그룹**, **인증서 URL** 및 **인증서 지문** 정보를 기록합니다.

인증서를 업로드하고, 키 자격 증명 모음을 만들고, 필요한 정보를 생성하는 샘플 PowerShell 스크립트가 뒤에 나옵니다. Azure Key Vault에 인증서를 업로드하는 방법에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/key-vault-get-started.md)을 참조하세요.

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>지원되는 토폴로지

[Azure Resource Manager 템플릿](https://github.com/MsHpcPack/HPCPack2016) 중 하나를 선택하여 HPC 팩 2016 클러스터를 배포할 수 있습니다. 다음은 세 가지 예제 클러스터 토폴로지의 상위 수준 아키텍처입니다. 고가용성 토폴로지는 여러 클러스터 헤드 노드를 포함합니다.

1. Active Directory 도메인을 사용하는 고가용성 클러스터

    ![AD 도메인의 HA 클러스터](./media/hpcpack-2016-cluster/haad.png)


2. Active Directory 도메인을 사용하지 않는 고가용성 클러스터

    ![AD 도메인이 없는 HA 클러스터](./media/hpcpack-2016-cluster/hanoad.png)

3. 단일 헤드 노드를 포함한 클러스터

   ![단일 헤드 노드를 포함한 클러스터](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>클러스터 배포

클러스터를 만들려면 템플릿을 선택하고 **Azure에 배포**를 클릭합니다. [Azure Portal](https://portal.azure.com)에서 다음 단계에 설명된 대로 템플릿에 대한 매개 변수를 지정합니다. 각 템플릿은 HPC 클러스터 인프라에 필요한 모든 Azure 리소스를 만듭니다. 리소스는 Azure 가상 네트워크, 공용 IP 주소, 부하 분산 장치(고가용성 클러스터만 해당), 네트워크 인터페이스, 가용성 집합, 저장소 계정 및 가상 머신을 포함합니다.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>1단계: 구독, 위치 및 리소스 그룹 선택

**구독** 및 **위치**는 PFX 인증서를 업로드하는 경우 지정한 것과 동일해야 합니다(필수 구성 요소 참조). 이 배포에 대해 다른 **리소스 그룹**을 만드는 것이 좋습니다.

### <a name="step-2-specify-the-parameter-settings"></a>2단계: 매개 변수 설정 지정

템플릿 매개 변수의 값을 입력하거나 수정합니다. 도움말 정보를 보려면 각 매개 변수 옆에 있는 아이콘을 클릭합니다. 또한 [사용 가능한 VM 크기](sizes.md)에 대한 지침을 참조하세요.

**자격 증명 모음 이름**, **자격 증명 모음 리소스 그룹**, **인증서 URL** 및 **인증서 지문** 매개 변수의 필수 구성 요소에 기록한 값을 지정합니다.

### <a name="step-3-review-terms-and-create"></a>3단계. 사용 약관 검토 및 만들기
템플릿과 관련된 사용 약관을 검토합니다. 동의하는 경우 **구매**를 클릭하여 배포를 시작합니다.

클러스터 토폴로지에 따라 배포를 완료하는 데 30분 이상이 걸릴 수 있습니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결
1. HPC 팩 클러스터를 배포한 후 [Azure Portal](https://portal.azure.com)로 이동합니다. **리소스 그룹**을 클릭하고 클러스터가 배포된 리소스 그룹을 찾습니다. 헤드 노드 가상 머신을 찾을 수 있습니다.

    ![포털의 클러스터 헤드 노드](./media/hpcpack-2016-cluster/clusterhns.png)

2. 하나의 헤드 노드를 클릭합니다. 고가용성 클러스터에서 헤드 노드 중 하나를 클릭합니다. **개요**에서 클러스터의 공용 IP 주소 또는 전체 DNS 이름을 찾을 수 있습니다.

    ![클러스터 연결 설정](./media/hpcpack-2016-cluster/clusterconnect.png)

3. **연결**을 클릭하여 지정된 관리자 사용자 이름으로 원격 데스크톱을 사용하여 헤드 노드 중 하나에 로그온합니다. 배포한 클러스터가 Active Directory 도메인에 있으면 사용자 이름은 \<privateDomainName>\\\<adminUsername>(예: hpc.local\hpcadmin)과 같은 형식입니다.

## <a name="next-steps"></a>다음 단계
* 클러스터에 작업을 제출합니다. [Azure에서 HPC 팩 클러스터에 작업 제출](hpcpack-cluster-submit-jobs.md) 및 [Azure에서 Azure Active Directory를 사용하여 HPC 팩 2016 클러스터 관리](hpcpack-cluster-active-directory.md)를 참조하세요.

