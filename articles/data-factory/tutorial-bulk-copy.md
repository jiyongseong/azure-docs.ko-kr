---
title: Azure Data Factory를 사용하여 데이터 대량 복사 | Microsoft Docs
description: Azure Data Factory 및 복사 작업을 사용하여 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 대량으로 복사하는 방법을 알아봅니다.
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
ms.openlocfilehash: 28128e07b4beb03b1a8ecdd8546d54c19145689f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 테이블 대량 복사
이 자습서에서는 **Azure SQL Database에서 Azure SQL Data Warehouse로 여러 테이블을 복사**하는 방법을 보여 줍니다. 다른 복사 시나리오에도 동일한 패턴을 적용할 수 있습니다. 예를 들어 SQL Server/Oracle에서 Azure SQL Database/Data Warehouse/Azure Blob으로 테이블을 복사하고, Blob에서 Azure SQL Database 테이블로 다른 경로를 복사합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

이 자습서에서 수행하는 단계는 대략적으로 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure SQL Database, Azure SQL Data Warehouse 및 Azure Storage 연결된 서비스를 만듭니다.
> * Azure SQL Database 및 Azure SQL Data Warehouse 데이터 집합을 만듭니다.
> * 복사할 테이블을 조회하는 파이프라인을 만들고, 실제 복사 작업을 수행하는 다른 파이프라인을 만듭니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

이 자습서에서는 Azure PowerShell을 사용합니다. 다른 도구/SDK를 사용하여 데이터 팩터리를 만드는 방법을 알아보려면 [빠른 시작](quickstart-create-data-factory-dot-net.md)을 참조하세요. 

## <a name="end-to-end-workflow"></a>종단 간 워크플로
이 시나리오에서는 SQL Data Warehouse에 복사할 여러 개의 Azure SQL Database 테이블이 있습니다. 다음은 파이프라인에서 발생하는 워크플로 단계의 논리적 시퀀스입니다.

![워크플로](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* 첫 번째 파이프라인은 싱크 데이터 저장소로 복사해야 하는 테이블의 목록을 찾습니다.  또는 싱크 데이터 저장소에 복사할 모든 테이블을 나열하는 메타데이터 테이블을 유지할 수 있습니다. 그런 다음 파이프라인에서 다른 파이프라인을 트리거하여 데이터베이스의 각 테이블을 반복하고 데이터 복사 작업을 수행합니다.
* 두 번째 파이프라인은 실제 복사를 수행하며, 테이블 목록을 매개 변수로 사용합니다. 최상의 성능을 위해 목록의 각 테이블에 대해 [Blob 저장소 및 PolyBase를 통해 스테이징되는 복사](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 사용하여 Azure SQL Database의 특정 테이블을 SQL Data Warehouse의 해당 테이블에 복사합니다. 이 예제에서 첫 번째 파이프라인은 테이블의 목록을 매개 변수의 값으로 전달합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.
* **Azure Storage 계정**. Azure Storage 계정은 대량 복사 작업에서 스테이징 Blob 저장소로 사용됩니다. 
* **Azure SQL Database**. 이 데이터베이스에는 원본 데이터가 포함되어 있습니다. 
* **Azure SQL Data Warehouse** 이 데이터 웨어하우스에는 SQL Database에서 복사된 데이터를 보관하고 있습니다. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Database 및 SQL Data Warehouse 준비

**원본 Azure SQL Database 준비**

[Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서에 따라 Adventure Works LT 샘플 데이터가 포함된 Azure SQL Database를 만듭니다. 이 자습서에서는 이 샘플 데이터베이스의 모든 테이블을 SQL 데이터 웨어하우스로 복사합니다.

**싱크 Azure SQL Data Warehouse 준비**

1. 아직 없는 경우 Azure SQL Data Warehouse를 만드는 단계는 [SQL Data Warehouse 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) 문서를 참조하세요.

2. SQL Data Warehouse에 해당하는 테이블 스키마를 만듭니다. [마이그레이션 유틸리티](https://www.microsoft.com/download/details.aspx?id=49100)를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse로 **스키마를 마이그레이션할 수 있습니다**. Azure Data Factory를 사용하여 이후 단계에서 데이터를 마이그레이션/복사합니다.

## <a name="azure-services-to-access-sql-server"></a>SQL 서버에 액세스하는 Azure 서비스

SQL Database와 SQL Data Warehouse 모두에서 Azure 서비스를 통해 SQL 서버에 액세스할 수 있도록 허용합니다. Azure SQL 서버에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **켜기**로 지정되어 있는지 확인합니다. 이 설정을 사용하면 Data Factory 서비스에서 Azure SQL Database의 데이터를 읽고 Azure SQL Data Warehouse에 데이터를 쓸 수 있습니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

1. 왼쪽에서 **모든 서비스**를 클릭하고 **SQL 서버**를 클릭합니다.
2. 서버를 선택하고 **설정** 아래의 **방화벽**을 클릭합니다.
3. **방화벽 설정** 페이지에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **PowerShell**을 시작합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
        
    ```powershell
    Connect-AzureRmAccount
    ```
    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. **Set-AzureRmDataFactoryV2** cmdlet을 실행하여 데이터 팩터리를 만듭니다. 이 명령을 실행하기 전에 자리 표시자를 사용자 고유의 값으로 바꿉니다. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    다음 사항에 유의하세요.

    * Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
    * 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기

이 자습서에서는 데이터 저장소에 대한 연결을 포함하여 원본, 싱크 및 스테이징 Blob 각각에 대해 하나씩, 세 개의 연결된 서비스를 만듭니다.

### <a name="create-the-source-azure-sql-database-linked-service"></a>원본 Azure SQL Database 연결된 서비스 만들기

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **AzureSqlDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADFv2TutorialBulkCopy 폴더를 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; 및 &lt;password&gt;를 Azure SQL Database의 값으로 바꿉니다.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell**에서 **ADFv2TutorialBulkCopy** 폴더로 전환합니다.

3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureSqlDatabaseLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>싱크 Azure SQL Data Warehouse 연결된 서비스 만들기

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **AzureSqlDWLinkedService.json**이라는 JSON 파일을 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; 및 &lt;password&gt;를 Azure SQL Database의 값으로 바꿉니다.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureSqlDWLinkedService** 연결된 서비스를 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>스테이징 Azure Storage 연결된 서비스 만들기

이 자습서에서는 더 나은 복사 성능을 위해 Azure Blob 저장소를 중간 스테이징 영역으로 사용하여 PolyBase를 사용할 수 있게 합니다.

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountName&gt;과 &lt;accountKey&gt;를 Azure 저장소 계정의 이름과 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>데이터 집합 만들기

이 자습서에서는 데이터가 저장되는 위치를 지정하는 원본 및 싱크 데이터 집합을 만듭니다.

### <a name="create-a-dataset-for-source-sql-database"></a>원본 SQL Database에 대한 데이터 집합 만들기

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **AzureSqlDatabaseDataset.json**이라는 JSON 파일을 만듭니다. "tableName"은 나중의 복사 작업에서 SQL 쿼리를 사용하여 데이터를 검색할 때의 더미 데이터입니다.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **AzureSqlDatabaseDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>싱크 SQL Data Warehouse에 대한 데이터 집합 만들기

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **AzureSqlDWDataset.json**이라는 JSON 파일을 만듭니다. "tableName"이 매개 변수로 설정되며, 나중에 이 데이터 집합을 참조하는 복사 작업에서 실제 값을 데이터 집합으로 전달합니다.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행하여 **AzureSqlDWDataset** 데이터 집합을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>파이프라인 만들기

이 자습서에서는 두 개의 파이프라인을 만듭니다.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>"IterateAndCopySQLTables" 파이프라인 만들기

이 파이프라인에서는 테이블 목록을 매개 변수로 사용합니다. 목록의 각 테이블에 대해 스테이징된 복사본과 PolyBase를 사용하여 Azure SQL Database의 테이블에서 Azure SQL Data Warehouse로 데이터를 복사합니다.

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **IterateAndCopySQLTables.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 **IterateAndCopySQLTables** 파이프라인을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>"GetTableListAndTriggerCopyData" 파이프라인 만들기

이 파이프라인에서는 두 단계를 수행합니다.

* Azure SQL Database 시스템 테이블을 찾아 복사할 테이블의 목록을 가져옵니다.
* "IterateAndCopySQLTables" 파이프라인을 트리거하여 실제 데이터 복사를 수행합니다.

1. **C:\ADFv2TutorialBulkCopy** 폴더에 다음 내용이 포함된 **GetTableListAndTriggerCopyData.json**이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 **GetTableListAndTriggerCopyData** 파이프라인을 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>파이프라인 실행 시작 및 모니터링

1. 주 "GetTableListAndTriggerCopyData" 파이프라인에 대한 파이프라인 실행을 시작하고 이후의 모니터링에 대한 파이프라인 실행 ID를 캡처합니다. 아래의 ExecutePipeline 작업에서 지정한 대로 "IterateAndCopySQLTables" 파이프라인에 대한 실행을 트리거합니다.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  다음 스크립트를 실행하여 **GetTableListAndTriggerCopyData** 파이프라인의 실행 상태를 계속 확인하고 최종 파이프라인 실행 및 작업 실행 결과를 출력합니다.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 다음과 같이 "**IterateAndCopySQLTables**" 파이프라인의 실행 ID를 가져오고, 자세한 작업 실행 결과를 확인할 수 있습니다.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    샘플 실행의 출력은 다음과 같습니다.

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. 싱크 Azure SQL Data Warehouse에 연결하여 Azure SQL Database의 데이터가 제대로 복사되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure SQL Database, Azure SQL Data Warehouse 및 Azure Storage 연결된 서비스를 만듭니다.
> * Azure SQL Database 및 Azure SQL Data Warehouse 데이터 집합을 만듭니다.
> * 복사할 테이블을 조회하는 파이프라인을 만들고, 실제 복사 작업을 수행하는 다른 파이프라인을 만듭니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

원본에서 대상으로 데이터를 증분 방식으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.
> [!div class="nextstepaction"]
>[증분 방식으로 데이터 복사](tutorial-incremental-copy-powershell.md)
