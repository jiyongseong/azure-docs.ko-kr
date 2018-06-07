---
title: Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기 | Microsoft Docs
description: Azure-SSIS 통합 런타임을 만들어 Azure Cloud에서 SSIS 패키지를 실행할 수 있는 방법을 배웁니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e0217080963502094800a8b62de9c781817aec61
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs
이 문서에서는 Azure Data Factory에서 Azure-SSIS 통합 런타임을 프로비전하는 단계를 제공합니다. 그런 다음 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에서 이 런타임에 SSIS(SQL Server Integration Services) 패키지를 배포할 수 있습니다.

자습서: [자습서: SSIS(SQL Server Integration Services 패키지)를 Azure에 배포](tutorial-create-azure-ssis-runtime-portal.md)는 Azure SQL Database를 SSIS 카탈로그의 저장소로 사용하여 Azure-SSIS IR(Integration Runtime)을 만드는 방법을 보여 줍니다. 이 문서는 자습서를 확장하고 다음을 수행하는 방법을 보여 줍니다. 

- SSIS 카탈로그(SSISDB 데이터베이스) 호스팅을 위해 Azure SQL 관리되는 인스턴스(미리 보기)를 사용합니다.
- Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인합니다. Azure-SSIS IR을 VNet에 조인하고 Azure Portal에서 VNet을 구성하는 개념적 정보는 [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.


## <a name="overview"></a>개요
이 문서에서는 Azure-SSIS IR을 프로비전하는 다양한 방법을 보여 줍니다.

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 템플릿](#azure-resource-manager-template)

Azure-SSIS IR을 만들 때 Data Factory는 Azure SQL Database에 연결하여 SSISDB(SSIS 카탈로그 데이터베이스)를 준비합니다. 또한 이 스크립트는 VNet에 대한 권한 및 설정을 구성하고(지정된 경우) Azure-SSIS Integration Runtime의 새 인스턴스를 VNet에 조인합니다.

Azure-SSIS IR의 인스턴스를 프로비전하는 경우 Azure Feature Pack for SSIS 및 Access 재배포 가능 패키지도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소가 지원하는 데이터 원본 외에도 Excel 및 Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 추가 구성 요소를 설치할 수도 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**. 구독이 없는 경우 [평가판](http://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.
- **Azure SQL Database 서버** 또는 **SQL Server 관리되는 인스턴스(미리 보기)**. 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 이 서버는 SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅합니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 Integration Runtime에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. Azure SQL Server의 가격 책정 계층을 적어 둡니다. Azure SQL Database에 지원되는 가격 책정 계층의 목록은 [SQL Database 리소스 제한](../sql-database/sql-database-resource-limits.md)을 참조하세요.

    Azure SQL Database 서버 또는 SQL Server 관리되는 인스턴스(미리 보기)에 SSIS 카탈로그(SSIDB 데이터베이스)가 없는지 확인합니다. Azure-SSIS IR 프로비전은 기존 SSIS 카탈로그 사용을 지원하지 않습니다.
- **클래식 또는 Azure Resource Manager VNet(가상 네트워크)(선택 사항)** 다음 조건 중 하나 이상에 해당하는 경우 Azure Virtual Network(VNet)가 있어야 합니다.
    - VNet의 일부인 SQL Server 관리되는 인스턴스(미리 보기)에서 SSIS 카탈로그 데이터베이스를 호스팅합니다.
    - Azure-SSIS 통합 런타임에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결하려고 합니다.
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요. PowerShell을 사용하여 클라우드에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 프로비전하는 스크립트를 실행합니다. 

> [!NOTE]
> - 미국 동부, 미국 동부 2, 동남 아시아 및 유럽 서부 등의 지역에서 2 버전의 데이터 팩터리를 만들 수 있습니다. 
> - 미국 동부, 미국 동부 2, 미국 중부, 미국 서부 2, 북유럽, 유럽 서부, 영국 남부 및 오스트레일리아 동부 등에서 Azure SSIS IR을 만들 수 있습니다.

## <a name="azure-portal"></a>Azure portal
이 섹션에서는 Azure Portal, 특히 Data Factory UI를 사용하여 Azure-SSIS IR을 만듭니다. 

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.    
3. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **MyAzureSsisDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)** 를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **작성 및 모니터링**을 클릭하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 시작합니다. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임 프로비전

1. 시작 페이지에서 **SSIS Integration Runtime 구성** 타일을 클릭합니다. 

   ![SSIS Integration Runtime 구성 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. **Integration Runtime 설정**의 **일반 설정** 페이지에서 다음 단계를 수행합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 통합 런타임에 대한 **이름**을 지정합니다.
    2. 통합 런타임에 대한 **위치**를 선택합니다. 지원되는 위치만 표시됩니다.
    3. SSIS 런타임으로 구성할 **노드 크기**를 선택합니다.
    4. 클러스터의 **노드 수**를 지정합니다.
    5. **다음**을 클릭합니다. 
1. **SQL 설정**에서 다음 단계를 수행합니다. 

    ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Azure SQL 서버가 있는 Azure **구독**을 지정합니다. 
    2. **카탈로그 데이터베이스 서버 엔드포인트**에 대한 Azure SQL 서버를 선택합니다.
    3. **관리자** 사용자 이름을 입력합니다.
    4. 관리자에 대한 **암호**를 입력합니다.  
    5. SSISDB 데이터베이스에 대한 **서비스 계층**을 선택합니다. 기본값은 [기본]입니다.
    6. **다음**을 클릭합니다. 
1.  **고급 설정** 페이지에서 **노드당 최대 병렬 실행 횟수**에 대한 값을 선택합니다.   

    ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 이 단계는 **선택 사항**입니다. 통합 런타임을 조인하려는 클래식 VNet(클래식 또는 Azure Resource Manager)이 있는 경우, **Azure-SSIS 통합 런타임용 VNet을 선택하고 Azure 서비스에서 VNet 권한/설정을 구성하도록 허용** 옵션을 선택하고 다음 단계를 수행합니다. 

    ![VNet이 포함된 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. **구독**에서 VNet이 있는 **구독**을 지정합니다. 
    2. 형식에는 Vnet의 **형식**(클래식 가상 네트워크 또는 Azure Resource Manager 가상 네트워크)을 지정합니다. 
    3. **VNet 이름**에는 **VNet**의 이름을 선택합니다. 
    4. **서브넷 이름**에는 VNet의 **서브넷** 이름을 선택합니다.
1. **마침**을 클릭하여 Azure-SSIS 통합 런타임을 만들기 시작합니다. 

    > [!IMPORTANT]
    > - 이 프로세스를 완료하는 데 약 20분이 걸립니다.
    > - Data Factory 서비스는 Azure SQL Database에 연결하여 SSISDB(SSIS 카탈로그 데이터베이스)를 준비합니다. 또한 이 스크립트는 VNet에 대한 권한 및 설정을 구성하고(지정된 경우) Azure-SSIS Integration Runtime의 새 인스턴스를 VNet에 조인합니다.
7. 필요한 경우 **연결** 창에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 클릭하여 상태를 새로 고칩니다. 

    ![만들기 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. **작업** 열 아래의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다. 

    ![Azure SSIS IR - 작업](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **편집** 탭으로 전환하고, **연결**을 클릭한 다음, **Integration Runtime** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 살펴봅니다. 
    ![기존 IR 보기](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. **새로 만들기**를 클릭하여 새운 Azure-SSIS IR을 만듭니다. 

    ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Azure-SSIS 통합 런타임을 만들려면 이미지에서 표시한 대로 **새로 만들기**를 클릭합니다. 
3. [Integration Runtime 설정] 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 클릭합니다.

    ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell
이 섹션에서는 Azure PowerShell을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-variables"></a>변수 만들기
이 자습서에서 스크립트에 사용할 변수를 정의합니다.

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (Preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>로그인 및 구독 선택
스크립트에 다음 코드를 추가하여 로그인하고 Azure 구독을 선택합니다. 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>데이터베이스에 대한 연결 유효성 검사
다음 스크립트를 추가하여 server.database.windows.net Azure SQL Database 서버 끝점 또는 Azure SQL 관리되는 인스턴스(미리 보기) 서버 끝점의 유효성을 검사합니다. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>가상 네트워크 구성
다음 스크립트를 추가하여 조인할 Azure-SSIS Integration Runtime에 대한 가상 네트워크 권한/설정을 자동으로 구성합니다.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integration Runtime 만들기
다음 명령을 실행하여 Azure에서 SSIS 패키지를 실행하는Azure-SSIS Integration Runtime을 만듭니다. 이때 사용 중인 데이터베이스 유형(Azure SQL Database 및 Azure SQL 관리되는 인스턴스(미리 보기))에 따라 이 섹션의 스크립트를 사용합니다. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>SSISDB 데이터베이스(SSIS 카탈로그)를 호스트하는 Azure SQL Database 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri
```

온-프레미스 데이터 액세스가 필요하지 않으면 VNetId 및 서브넷에 대한 값을 전달할 필요가 없습니다. 즉, SSIS 패키지에 온-프레미스 데이터 원본/대상이 있습니다. CatalogPricingTier 매개 변수 값을 전달해야 합니다. Azure SQL Database에 지원되는 가격 책정 계층의 목록은 [SQL Database 리소스 제한](../sql-database/sql-database-resource-limits.md)을 참조하세요.

#### <a name="azure-sql-managed-instance-preview-to-host-the-ssisdb-database"></a>SSISDB 데이터베이스를 호스트하는 Azure SQL 관리되는 인스턴스(미리 보기)

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

VNet을 조인하는 Azure SQL 관리되는 인스턴스(미리 보기)와 함께 VnetId 및 서브넷 매개 변수에 대한 값을 전달해야 합니다. Azure SQL 관리되는 인스턴스(미리 보기)에는 CatalogPricingTier 매개 변수가 적용되지 않습니다. 

### <a name="start-integration-runtime"></a>Integration Runtime 시작
다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다. 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
이 명령은 완료하는 데 **20-30분** 정도 걸립니다. 


### <a name="full-script"></a>전체 스크립트
다음은 Azure-SSIS IR을 만들어 VNet에 조인하는 전체 스크립트입니다. 이 스크립트에서는 Azure SQL 관리되는 인스턴스(미리 보기)를 사용하여 SSIS 카탈로그를 호스트한다고 가정합니다. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure-SSIS 통합 런타임을 만듭니다. 다음은 샘플 연습입니다. 

1. 다음 Resource Manager 템플릿을 사용하여 JSON 파일을 만듭니다. 꺾쇠 괄호(자리 표시자)의 값을 사용자 고유의 값으로 바꿉니다. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Resource Manager 템플릿을 배포하려면 다음 예제와 같이 New-AzureRmResourceGroupDeployment 명령을 실행합니다. 이 예제에서 ADFTutorialResourceGroup은 리소스 그룹의 이름입니다. ADFTutoriaArRM.json은 데이터 팩터리 및 Azure-SSIS IR에 대한 JSON 정의를 포함하고 있는 파일입니다. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    이 명령은 데이터 팩터리를 만들고 그 안에 Azure-SSIS IR을 만들지만 IR을 시작하지는 않습니다. 
3. Azure-SSIS IR을 시작하려면 Start-AzureRmDataFactoryV2IntegrationRuntime 명령을 실행합니다. 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포
이제 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에 SSIS 패키지를 배포합니다. SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅하는 Azure SQL Server에 연결합니다. Azure SQL Server 이름의 형식은 &lt;servername&gt;.database.windows.net(Azure SQL Database의 경우)입니다. 자세한 지침은 [패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계
이 설명서의 다른 Azure-SSIS IR 항목을 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 VNet에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서는 Azure-SSIS IR을 Azure 가상 네트워크(VNet)에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 VNet에 조인할 수 있도록 VNet을 구성하는 단계도 제공합니다. 
