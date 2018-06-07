---
title: Azure Data Factory를 사용하여 SQL Server에서 Blob Storage로 데이터 복사 | Microsoft Docs
description: Azure Data Factory에서 자체 호스팅 Integration Runtime을 사용하여 온-프레미스 데이터 저장소에서 Azure 클라우드로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 5d362d8167cdfb772c70b02cc57bb49d3c2eb01d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>자습서: 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터 복사
이 자습서에서는 Azure PowerShell을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 Data Factory 파이프라인을 만듭니다. 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는, 자체 호스팅된 통합 런타임을 생성하고 사용합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 이 문서는 Data Factory 서비스를 자세히 소개하지 않습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요. 

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
### <a name="azure-subscription"></a>Azure 구독
시작하기 전에 Azure 구독이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 *참여자* 또는 *소유자* 역할로 할당되거나 Azure 구독의 *관리자*여야 합니다. 

구독에서 사용 권한을 보려면 Azure Portal로 이동하고, 오른쪽 위 모서리에 있는 사용자 이름을 선택한 다음 **사용 권한**을 선택합니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 및 2017
이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 *원본* 데이터 저장소로 사용합니다. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 Azure Blob Storage(싱크)로 데이터를 복사합니다. 그런 다음 SQL Server 데이터베이스에 **emp**라는 테이블을 만들고 테이블에 동일한 두 개의 샘플 항목을 삽입합니다. 

1. SQL Server Management Studio를 시작합니다. 컴퓨터에 아직 설치되어 있지 않으면 [SQL Server Management Studio 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)로 이동합니다. 

2. 자격 증명을 사용하여 SQL server 인스턴스에 연결합니다. 

3. 샘플 데이터베이스 만들기 트리 뷰에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 선택합니다. 
 
4. **새 데이터베이스** 창에서 데이터베이스의 이름을 입력하고 **확인**을 선택합니다. 

5. **emp** 테이블을 만들고 일부 샘플 데이터를 이 테이블에 삽입하려면 데이터베이스에 대해 다음 쿼리 스크립트를 실행합니다.

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

6. 트리 뷰에서 생성한 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

### <a name="azure-storage-account"></a>Azure Storage 계정
이 자습서에서는 범용 Azure Storage 계정(특히 Blob Storage)을 대상/싱크 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 이 자습서에서 만든 데이터 팩터리의 파이프라인은 온-프레미스 SQL Server 데이터베이스(원본)에서 이 Azure Blob Storage(싱크)로 데이터를 복사합니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 자습서에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음을 수행하여 저장소 계정의 이름 및 키를 가져옵니다. 

1. Azure 사용자 이름 및 암호를 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 왼쪽 창에서 **추가 서비스**를 선택하고, **저장소** 키워드를 사용하여 필터링한 다음 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 저장소 계정을 선택합니다. 

4. **저장소 계정** 창에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. **저장소 계정 이름** 및 **key1** 상자에서 값을 복사한 다음 메모장 또는 나중에 자습서에서 사용할 다른 편집기에 붙여넣습니다. 

#### <a name="create-the-adftutorial-container"></a>adftutorial 컨테이너 만들기 
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 

1. **저장소 계정** 창에서 **개요**로 전환한 다음, **Blob**을 선택합니다. 

    ![Blob 옵션 선택](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. **Blob service** 창에서 **컨테이너**를 선택합니다. 

    ![컨테이너 단추 추가](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. **새 컨테이너** 창의 **이름** 상자에 **adftutorial**을 입력한 후 **확인**을 선택합니다. 

    ![컨테이너 이름 입력](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 컨테이너 목록에서 **adftutorial**을 선택합니다.  

    ![컨테이너를 선택합니다.](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. **adftutorial**의 **컨테이너** 창을 열어 둡니다. 이 자습서의 끝부분에서 출력을 확인하는 데 사용합니다. 데이터 팩터리는 이 컨테이너에서 출력 폴더를 자동으로 만듭니다. 따라서 새로 만들 필요가 없습니다.

    ![컨테이너 창](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Azure Powershell 설치
컴퓨터에 최신 버전의 Azure PowerShell이 설치되지 않은 경우 설치합니다. 

1. [Azure SDK 다운로드](https://azure.microsoft.com/downloads/)로 이동합니다. 

2. **명령줄 도구** 아래의 **PowerShell** 섹션에서 **Windows 설치**를 선택합니다. 

3. Azure PowerShell을 설치하려면 MSI 파일을 실행합니다. 

자세한 지침은 [Azure PoweShell 설치 및 구성 방법](/powershell/azure/install-azurerm-ps)을 참조하세요. 

#### <a name="log-in-to-powershell"></a>PowerShell에 로그인

1. 컴퓨터에서 PowerShell을 시작하고, 이 빠른 시작 자습서가 완료될 때까지 열어 둡니다. PowerShell을 닫고 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.

    ![PowerShell 시작](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 Azure 사용자 이름 및 암호를 입력합니다.
       
    ```powershell
    Connect-AzureRmAccount
    ```        

3. 여러 Azure 구독이 있는 경우 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령을 복사하고, [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고(큰따옴표로 묶임, 예: `"adfrg"`), 명령을 실행합니다. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.

3. 나중에 PowerShell 명령에서 사용할 수 있는 데이터 팩터리 이름에 대한 변수를 정의합니다. 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 예를 들어 ADFTutorialFactorySP1127이라는 이름을 사용합니다. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. 데이터 팩터리의 위치에 대한 변수를 정의합니다. 

    ```powershell
    $location = "East US"
    ```  

5. 데이터 팩터리를 만들려면 다음 `Set-AzureRmDataFactoryV2` cmdlet을 실행합니다. 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 *참여자* 또는 *소유자* 역할로 할당되거나 Azure 구독의 *관리자*여야 합니다.
> * 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory 버전 2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(Azure HDInsight 등)은 다른 지역에 있을 수 있습니다.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

이 섹션에서는 자체 호스팅 Integration Runtime을 만들고 이를 Microsoft SQL Server 데이터베이스와 함께 온-프레미스 컴퓨터에 연결합니다. 자체 호스팅된 Integration Runtime은 사용자 컴퓨터의 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 구성 요소입니다. 

1. Integration Runtime의 이름에 대한 변수를 만듭니다. 고유한 이름을 사용하고 이름을 적어 둡니다. 이 자습서의 뒷부분에서 사용합니다. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. 자체 호스팅 통합 런타임을 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    샘플 출력은 다음과 같습니다.

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

3. 만든 Integration Runtime의 상태를 검색하려면 다음 명령을 실행합니다.

    ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    샘플 출력은 다음과 같습니다.
    
    ```json
    Nodes                     : {}
    CreateTime                : 9/14/2017 10:01:21 AM
    InternalChannelEncryption :
    Version                   :
    Capabilities              : {}
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    AutoUpdate                :
    ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
    ResourceGroupName         : <ResourceGroup name>
    DataFactoryName           : <DataFactory name>
    Name                      : <Integration Runtime name>
    State                     : NeedRegistration
    ```

4. 자체 호스팅 Integration Runtime을 클라우드의 Data Factory 서비스에 등록하기 위해 *인증 키*를 검색하려면 다음 명령을 실행합니다. 다음 단계에서 컴퓨터에 설치할 자체 호스팅된 Integration Runtime을 등록하기 위한 키(인용 부호 제외) 중 하나를 복사합니다. 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    샘플 출력은 다음과 같습니다.
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Integration Runtime 설치
1. 로컬 Windows 컴퓨터에서 [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)을 다운로드하고 설치를 실행합니다. 

2. **Microsoft Integration Runtime 설치 시작** 마법사에서 **다음**을 선택합니다.  

3. **최종 사용자 사용권 계약** 창에서 사용권 계약에 동의하고 **다음**을 선택합니다. 

4. **대상 폴더** 창에서 **다음**을 선택합니다. 

5. **Microsoft Integration Runtime을 설치할 준비가 됨** 창에서 **설치**를 선택합니다. 

6. 컴퓨터를 사용하지 않는 동안 절전 또는 최대 절전 모드로 전환되도록 구성하는 방법에 대한 경고 메시지가 표시되면 **확인**을 선택합니다. 

7. **전원 옵션** 창이 표시되면 닫은 후 설치 창으로 전환합니다. 

8. **Microsoft Integration Runtime 설치 완료** 마법사에서 **마침**을 선택합니다.

9. **Integration Runtime(자체 호스팅) 등록** 창에 이전 섹션에서 저장한 키를 붙여넣고 **등록**을 선택합니다. 

    ![통합 런타임 등록](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    자체 호스팅 Integration Runtime이 성공적으로 등록되면 다음 메시지가 표시됩니다. 

    ![성공적으로 등록되었습니다.](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. **새로운 Integration Runtime(자체 호스팅) 노드** 창에서 **다음**을 선택합니다. 

    ![새 Integration Runtime 노드 창](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. **인트라넷 통신 채널** 창에서 **건너뛰기**를 선택합니다.  
    다중 노드 Integration Runtime 환경에서 노드 내 통신을 보호하기 위한 TLS/SSL 인증을 선택할 수 있습니다.

    ![인트라넷 통신 채널 창](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. **Integration Runtime(자체 호스팅) 등록** 창에서 **구성 관리자 시작**을 선택합니다. 

13. 노드가 클라우드 서비스에 연결되면 다음 메시지가 표시됩니다.

    ![노드가 연결됨](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. 다음을 수행하여 SQL Server 데이터베이스에 대한 연결을 테스트합니다.

    ![진단 탭](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. **구성 관리자** 창에서 **진단** 탭으로 전환합니다.

    나. **데이터 원본 형식** 상자에서 **SqlServer**를 선택합니다.

    다. 서버 이름을 입력합니다.

    d. 데이터베이스 이름을 입력합니다. 

    e. 인증 모드를 선택합니다. 

    f. 사용자 이름을 입력합니다. 

    g. 사용자 이름과 연결된 암호를 입력합니다.

    h. Integration Runtime을 Microsoft SQL Server에 연결할 수 있는지 확인하려면 **테스트**를 선택합니다.  
    연결이 성공적인 경우 녹색 확인 표시 아이콘이 표시됩니다. 그렇지 않다면 실패와 관련된 오류 메시지가 나타납니다. 모든 문제를 해결하고 Integration Runtime을 SQL Server 인스턴스에 연결할 수 있는지 확인합니다.

    이전 값은 모두 나중에 이 자습서에서 사용합니다.
    
## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결하려면 데이터 팩터리에서 연결된 서비스를 만듭니다. 이 자습서에서는 Azure Storage 계정 및 온-프레미스 SQL Server 인스턴스를 데이터 저장소에 연결합니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage 연결된 서비스(대상/싱크) 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. 다음 코드를 사용하여 *C:\ADFv2Tutorial* 폴더에 *AzureStorageLinkedService.json*이라는 JSON 파일을 만듭니다. *ADFv2Tutorial* 폴더가 아직 없는 경우 만듭니다.  

    > [!IMPORTANT]
    > 파일을 저장하기 전에 \<accountName>과 \<accountKey>를 Azure Storage 계정의 이름과 키로 바꿉니다. [필수 구성 요소](#get-storage-account-name-and-account-key) 섹션에 적어둡니다.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. PowerShell에서 *C:\ADFv2Tutorial* 폴더로 전환합니다.

3. AzureStorageLinkedService라는 연결된 서비스를 만들려면 다음 `Set-AzureRmDataFactoryV2LinkedService` cmdlet을 실행합니다. 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    "파일을 찾을 수 없음" 오류가 발생하는 경우 `dir` 명령을 실행하여 파일이 있는지 확인합니다. 파일 이름에 *.txt* 확장명이 있으면(예: AzureStorageLinkedService.json.txt) 확장명을 제거하고 PowerShell 명령을 다시 실행합니다. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server 연결된 서비스(원본) 만들기 및 암호화
이 단계에서 온-프레미스 SQL Server 인스턴스를 데이터 팩터리에 연결합니다.

1. 다음 코드를 사용하여 *C:\ADFv2Tutorial* 폴더에 *SqlServerLinkedService.json*이라는 JSON 파일을 만듭니다.

    > [!IMPORTANT]
    > Microsoft SQL Server에 연결하는 데 사용하는 인증에 기반한 섹션을 선택합니다.

    **SQL 인증(sa) 사용:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    

    **Windows 인증 사용:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```

    > [!IMPORTANT]
    > - SQL Server 인스턴스에 연결하는 데 사용한 인증에 기반한 섹션을 선택합니다.
    > - **\<integration runtime 이름>**을 사용자의 Integration Runtime 이름으로 바꿉니다.
    > - 파일을 저장하기 전에 **\<servername>**, **\<databasename>**, **\<username>** 및 **\<password>**를 Microsoft SQL Server 인스턴스의 값으로 바꿉니다.
    > - 백슬래시(\\)를 사용자 계정 또는 서버 이름에 사용해야 하는 경우 앞에 이스케이프 문자(\\)를 사용합니다. 예를 들어 *mydomain\\\\myuser*를 사용합니다. 

2. 중요한 데이터(사용자 이름, 암호 등)를 암호화하려면 `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential` cmdlet을 실행합니다.  
    이 암호화를 사용하면 DPAPI(데이터 보호 응용 프로그램 프로그래밍 인터페이스)를 사용하여 자격 증명을 암호화합니다. 암호화된 자격 증명은 자체 호스팅 통합 런타임 노드(로컬 컴퓨터)에 로컬로 저장됩니다. 출력 페이로드는 암호화된 자격 증명을 포함하는 다른 JSON 파일(이 경우 *encryptedLinkedService.json*)로 리디렉션될 수 있습니다.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. EncryptedSqlServerLinkedService를 생성하는 다음 명령을 실행합니다.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 입력 및 출력 데이터 집합을 만듭니다. 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 복사 작업에 입력 및 출력 데이터를 나타냅니다.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>원본 SQL Server 데이터베이스에 대한 데이터 집합 만들기
이 단계에서는 SQL Server 데이터베이스 인스턴스에 있는 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 SqlServerTable 형식입니다. 이 데이터 집합은 이전 단계에서 만든 SQL Server 연결된 서비스를 참조합니다. 연결된 서비스에는 Data Factory 서비스가 런타임 시 SQL Server 인스턴스에 연결하는 데 사용하는 연결 정보가 있습니다. 이 데이터 집합은 데이터가 포함된 데이터베이스에 SQL 테이블을 지정합니다. 이 자습서에서 **emp** 테이블에는 원본 데이터가 포함됩니다. 

1. 다음 코드를 사용하여 *C:\ADFv2Tutorial* 폴더에 *SqlServerDataset.json*이라는 JSON 파일을 만듭니다.  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. SqlServerDataset 데이터 집합을 만들려면 `Set-AzureRmDataFactoryV2Dataset` cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Azure Blob Storage(싱크)에 대한 데이터 집합 만들기
이 단계에서는 Azure Blob Storage에 복사될 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 AzureBlob 형식입니다. 이 데이터 집합은 이 자습서의 앞부분에서 만든 Azure Storage 연결된 서비스를 참조합니다. 

연결된 서비스에는 Data Factory가 런타임 시 Azure Storage Account에 연결하는 데 사용하는 연결 정보가 있습니다. 이 데이터 집합은 SQL Server 데이터베이스에서 데이터를 복사할 Azure Storage의 폴더를 지정합니다. 이 자습서에서 폴더는 `adftutorial`가 Blob 컨테이너이며, `fromonprem`이 폴더인 *adftutorial/fromonprem*입니다. 

1. 다음 코드를 사용하여 *C:\ADFv2Tutorial* 폴더에 *AzureBlobDataset.json*이라는 JSON 파일을 만듭니다.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. AzureBlobDataset 데이터 집합을 만들려면 `Set-AzureRmDataFactoryV2Dataset` cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 자습서에서는 복사 활동을 사용하여 파이프라인을 만듭니다. 복사 작업은 SqlServerDataset을 입력 데이터 집합으로 사용하고 AzureBlobDataset을 출력 데이터 집합으로 사용합니다. 원본 형식은 *SqlSource*로 설정되고 싱크 형식은 *BlobSink*로 설정됩니다.

1. 다음 코드를 사용하여 *C:\ADFv2Tutorial* 폴더에 *SqlServerToBlobPipeline.json*이라는 JSON 파일을 만듭니다.

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. SQLServerToBlobPipeline 파이프라인을 만들려면 `Set-AzureRmDataFactoryV2Pipeline` cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기
SQLServerToBlobPipeline 파이프라인에서 파이프라인을 실행하기 시작하고, 이후 모니터링할 파이프라인 실행 ID를 캡처합니다.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. SQLServerToBlobPipeline 파이프라인의 실행 상태를 계속 확인하려면 PowerShell에서 다음 스크립트를 실행하고 최종 결과를 출력합니다.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

2. 다음과 같이 SQLServerToBlobPipeline 파이프라인의 실행 ID를 가져오고, 다음 명령을 실행하여 자세한 작업 실행 결과를 확인할 수 있습니다. 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>출력 확인
파이프라인은 자동으로 `adftutorial` Blob 컨테이너에서 *fromonprem*이라는 출력 폴더를 만듭니다. 출력 폴더에서 *dbo.emp.txt* 파일이 표시되는지 확인합니다. 

1. Azure Portal의 **adftutorial** 컨테이너 창에서 출력 폴더를 보려면 **새로 고침**을 선택합니다.

    ![만든 출력 폴더](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. 폴더 목록에서 `fromonprem`을 선택합니다. 
3. `dbo.emp.txt`이라는 파일이 표시됨을 확인합니다.

    ![출력 파일](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob Storage의 위치 간에 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * SQL Server 및 Azure Storage 연결된 서비스를 만듭니다. 
> * SQL Server 및 Azure Blob 데이터 집합을 만듭니다.
> * 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

Data Factory에서 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.

원본에서 대상으로 데이터를 대량으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[대량 데이터 복사](tutorial-bulk-copy.md)
