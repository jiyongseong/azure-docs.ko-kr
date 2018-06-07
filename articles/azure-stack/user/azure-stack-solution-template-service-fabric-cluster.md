---
title: Azure 스택 서비스 패브릭 클러스터를 보안된 배포 | Microsoft Docs
description: Azure 스택 보안된 서비스 패브릭 클러스터를 배포 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>서비스 패브릭 클러스터를 Azure 스택 배포

사용 하 여는 **서비스 패브릭 클러스터** Azure 스택의 보안된 서비스 패브릭 클러스터를 배포 하는 Azure 마켓플레이스의 항목입니다. 

서비스 패브릭 사용에 대 한 자세한 내용은 참조 [Azure 서비스 Frabric 개요](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) 및 [서비스 패브릭 클러스터 보안 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), Azure 설명서에서.

## <a name="prerequisites"></a>필수 조건

다음은 서비스 패브릭 클러스터를 배포 하려면 필요 합니다.
1. **클러스터 인증서**  
   서비스 패브릭을 배포할 때 KeyVault에 추가한 X.509 서버 인증서입니다. 
   - **CN** 이 인증서에는 정규화 된 도메인 이름 (FQDN)를 만들면 서비스 패브릭 클러스터의 일치 해야 합니다. 
   - 공용 및 개인 키가 필요한 인증서 형식이 PFX를 해야 합니다. 
   참조 [요구 사항](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) 이 서버 쪽 인증서를 만들기 위한 합니다.

    > [!NOTE]  
    > 테스트를 위해 x.509 서버 인증서의 자체 서명 된 인증서 inplace를 사용할 수 있습니다. 자체 서명 된 인증서는 클러스터의 FQDN과 일치 필요가 없습니다.

2.  **관리 클라이언트 인증서** 자체 서명 될 수 있는 서비스 패브릭 클러스터에 인증 하는 클라이언트에서 사용할 인증서입니다. 참조 [요구 사항](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) 이 클라이언트 인증서를 만들기 위한 합니다.

3.  **다음 항목은 Azure 스택 시장에서 사용할 수 해야 합니다.**
     - **Windows Server 2016** –는 템플릿은 Windows Server 2016 이미지를 사용 하 여 클러스터를 만듭니다.  
     - **고객 스크립트 확장** -Microsoft에서 가상 컴퓨터 확장.  
     - **PowerShell 필요한 구성 단계** -Microsoft에서 가상 컴퓨터 확장.


## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가
서비스 패브릭 클러스터를 배포 하려면 올바른 KeyVault 지정 해야 *암호 식별자* 또는 서비스 패브릭 클러스터에 대 한 URL입니다. Azure 리소스 관리자 템플릿 입력으로 KeyVault 걸리며 서비스 패브릭 클러스터를 설치 하는 동안 클러스터 인증서를 검색 합니다. 

> [!IMPORTANT]  
> 서비스 패브릭와 함께 사용할 KeyVault에 암호를 추가 하려면 PowerShell을 사용 해야 합니다. 포털을 사용 하지 마십시오.  

다음 스크립트를 사용 하 여는 KeyVault 만들고 추가 하는 *클러스터 인증서* 를 합니다. (참조는 [필수 구성 요소](#prerequisites).) 스크립트를 실행 하기 전에 예제 스크립트를 검토 하 고 사용자 환경에 맞게 표시 된 매개 변수를 업데이트 합니다. 이 스크립트는 Azure 리소스 관리자 템플릿으로 제공 해야 할 값을 출력도 합니다. 

> [!TIP]  
> 스크립트를 성공적으로 수행 되기 전에 계산, 네트워크, 저장소 및 KeyVault에 대 한 서비스를 포함 하는 공용 행사 이어야 합니다. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


자세한 내용은 참조 [PowerShell과 함께 Azure 스택에 KeyVault 관리](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell)합니다.

## <a name="deploy-the-marketplace-item"></a>마켓플레이스 항목을 배포

1. 사용자 포털에서로 이동 **새로** > **계산** > **서비스 패브릭 클러스터**합니다. 

   ![서비스 패브릭 클러스터를 선택 합니다.](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. 각 페이지에 대해 같은 *기본 사항*, 배포 양식을 작성 합니다. 확실 하지 않은 값의 경우 기본값을 사용 합니다. 선택 **확인** 하 여 다음 페이지로 이동 합니다.

   ![기본 사항](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. 에 *네트워크 설정을* 페이지를 열려면 응용 프로그램에 대 한 특정 포트를 지정할 수 있습니다.

   ![네트워크 설정](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. 에 *보안* 페이지에서 가져온 값을 추가 합니다 [Azure KeyVault 만들기](#add-a-secret-to-key-vault) 및 암호를 업로드 합니다.

   에 대 한는 *관리 클라이언트 인증서 지문이*, 지문을 입력는 *관리 클라이언트 인증서*합니다. (참조는 [필수 구성 요소](#prerequisites).)
   
   - 원본 주요 자격 증명 모음: 전체 지정 *keyVault id* 스크립트 결과에서 문자열입니다. 
   - 클러스터 인증서 URL: 전체 URL을 지정 하는 *비밀 Id* 스크립트 결과에서 합니다. 
   - 인증서 지문을 클러스터: 지정 된 *클러스터 인증서 지문을* 스크립트 결과에서 합니다.
   - 관리 클라이언트는 인증서 지문: 지정는 *관리 클라이언트 인증서 지문이* 필수 구성 요소에서 만든 합니다. 

   ![스크립트 출력](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![보안](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. 마법사를 완료 한 다음 선택 **만들기** 서비스 패브릭 클러스터를 배포할 수 있습니다.



## <a name="access-the-service-fabric-cluster"></a>서비스 패브릭 클러스터에 액세스
서비스 패브릭 탐색기 또는 서비스 패브릭 PowerShell을 사용 하 여 서비스 패브릭 클러스터를 액세스할 수 있습니다.


### <a name="use-service-fabric-explorer"></a>서비스 패브릭 탐색기를 사용 하 여
1.  웹 브라우저 관리 클라이언트 인증서에 대 한 액세스 권한이 확인 하 여 서비스 패브릭 클러스터에 인증할 수 있습니다.  

    a. Internet Explorer를 열고로 이동 **인터넷 옵션** > **콘텐츠** > **인증서**합니다.
  
    나. 인증서를 선택 **가져오기** 시작 하는 *인증서 가져오기 마법사*, 클릭 하 고 **다음**합니다. 에 *가져올 파일* 페이지 **찾아보기**, 선택는 **관리 클라이언트 인증서** Azure 리소스 관리자 템플릿을 제공 합니다.
        
       > [!NOTE]  
       > 이 인증서 KeyVault에 이전에 추가 하는 클러스터 인증서가 아닙니다.  

    다. "개인 정보 교환"에서 파일 탐색기 창의 확장 드롭다운 선택 했는지 확인 합니다.  

       ![개인 정보 교환](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. 에 *인증서 저장소* 페이지에서 **개인**, 한 다음 마법사를 완료 합니다.  
       ![인증서 저장소](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. 서비스 패브릭 클러스터의 FQDN을 찾으려면:  

    a. 서비스 패브릭와 연결 된 리소스 그룹에 클러스터를 찾습니다 이동는 *공용 IP 주소* 리소스입니다. 열려는 공용 IP 주소와 연결 된 개체를 선택는 *공용 IP 주소* 블레이드입니다.  

      ![공용 IP 주소](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    나. 공용 IP 주소 블레이드를 FQDN으로 표시 *DNS 이름*합니다.  

      ![DNS 이름](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. 서비스 패브릭 탐색기 및 클라이언트 연결 끝점에 대 한 URL을 찾으려면 템플릿 배포의 결과 검토 합니다.

4. 브라우저에서 https:// 이동*FQDN*: 19080 합니다. 대체 *FQDN* 2 단계에서 서비스 패브릭 클러스터의 fqdn입니다.   
   자체 서명 된 인증서를 사용한 연결이 안전 하지 않다는 경고가 얻게 됩니다. 웹 사이트를 계속 하려면 선택 **추가 정보**, 차례로 **웹 페이지로 이동**합니다. 

5. 인증 사이트에 사용할 인증서를 선택 해야 합니다. 선택 **추가 선택 사항을**, 적절 한 인증서를 선택 하 고 클릭 **확인** 서비스 패브릭 탐색기로 연결 합니다. 

   ![인증](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>서비스 패브릭 PowerShell을 사용 하 여

1. 설치는 *Microsoft Azure Service Fabric SDK* 에서 [Windows에서 개발 환경을 준비](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) Azure Service Fabric 설명서에 있습니다.  

2. 설치가 완료 된 후 서비스 패브릭 cmdlet은 PowerShell에서 액세스할 수 있는지 확인 하려면 시스템 환경 변수를 구성 합니다.  
    
    a. 로 이동 **제어판** > **시스템 및 보안** > **시스템**를 선택한 후 **고급 시스템 설정**.  
    
      ![제어판](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    나. 에 **고급** 탭 *시스템 속성*선택, **환경 변수**합니다.  

    다. 에 대 한 *시스템 변수*, 편집 **경로** 있는지 확인 하 고 **c:\\Program Files\\Microsoft 서비스 패브릭\\bin\\패브릭 \\Fabric.Code** 환경 변수의 목록 맨 위에 있는 됩니다.  

      ![환경 변수 목록](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. 환경 변수의 순서를 변경한 후 PowerShell을 다시 시작 하 고 서비스 패브릭 클러스터에 액세스 하 고 다음 PowerShell 스크립트를 실행 하십시오.

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > 없는 *https://* 전에 스크립트에서 클러스터의 이름입니다. 19000 포트를 입력 해야 합니다.
 
