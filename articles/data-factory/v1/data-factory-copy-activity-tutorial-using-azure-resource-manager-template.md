---
title: '자습서: Resource Manager 템플릿을 사용하여 파이프라인 생성 | Microsoft Docs'
description: 이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Data Factory 파이프라인을 만듭니다. 이 파이프라인은 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c8f96ffa44cb584cef3c3a5f785c20469aac38bd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>자습서: Azure Resource Manager 템플릿을 사용하여 데이터를 복사하는 Data Factory 파이프라인 만들기 
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기에 있는 Data Factory 서비스 버전 2를 사용하는 경우 [버전 2 설명서의 복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Data Factory를 만드는 방법을 보여 줍니다. 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. 출력 데이터를 생성하기 위해 입력 데이터를 변환하지 않습니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하도록 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

이 자습서에는 한 가지 작업 즉, 복사 작업이 포함된 파이프라인을 만듭니다. 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요. 

> [!NOTE] 
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하도록 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건
* [자습서 개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
* [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell의 최신 버전을 설치합니다. 이 자습서에서는 PowerShell을 사용하여 데이터 팩터리 엔터티를 배포합니다. 
* (선택 사항) Azure Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

## <a name="in-this-tutorial"></a>자습서 내용
이 자습서에서는 다음 데이터 팩터리 엔터티로 데이터 팩터리를 만듭니다.

| 엔터티 | 설명 |
| --- | --- |
| Azure Storage 연결된 서비스 |Azure Storage 계정을 데이터 팩터리에 연결합니다. Azure Storage는 원본 데이터 저장소이고 Azure SQL Database는 자습서의 복사 작업에 대한 싱크 데이터 저장소입니다. 복사 작업을 위한 입력 데이터가 포함된 저장소 계정을 지정합니다. |
| Azure SQL Database 연결된 서비스 |Azure SQL Database를 데이터 팩터리에 연결합니다. 복사 작업에 대한 출력 데이터를 보유하는 Azure SQL Database를 지정합니다. |
| Azure Blob 입력 데이터 집합 |Azure Storage 연결된 서비스를 참조하세요. 연결된 서비스는 Azure Storage 계정을 말하며 Azure Blob 데이터 집합은 입력 데이터를 가진 저장소의 컨테이너, 폴더, 파일 이름을 지정합니다. |
| Azure SQL 출력 데이터 집합 |Azure SQL 연결된 서비스를 참조하세요. Azure SQL 연결된 서비스는 Azure SQL Server를 말하며 Azure SQL 데이터 집합은 출력 데이터를 가진 테이블의 이름을 지정합니다. |
| 데이터 파이프라인 |파이프라인에는 입력으로 Azure Blob 데이터 집합을 사용하고 출력으로 Azure SQL 데이터 집합을 사용하는 복사 유형의 작업이 하나 포함됩니다. 복사 작업은 Azure Blob의 데이터를 Azure SQL Database의 테이블에 복사합니다. |

데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 이러한 두 가지 유형의 활동은 [데이터 이동 활동](data-factory-data-movement-activities.md) 및 [데이터 변환 활동](data-factory-data-transformation-activities.md)입니다. 이 자습서에는 한 가지 활동(복사 활동)이 있는 파이프라인을 만듭니다.

![Azure Blob을 Azure SQL Database로 복사](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

다음 섹션에서는 신속하게 자습서를 살펴보고 템플릿을 테스트할 수 있도록 데이터 팩터리 엔터티를 정의하기 위한 완전한 Resource Manager 템플릿을 제공합니다. 각 데이터 팩터리 엔터티를 정의하는 방법을 알아보려면 [템플릿의 데이터 팩터리 엔터티](#data-factory-entities-in-the-template) 섹션을 참조하세요.

## <a name="data-factory-json-template"></a>데이터 팩터리 JSON 템플릿
데이터 팩터리 정의를 위한 최상위 Resource Manager 템플릿은 다음과 같습니다. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
**C:\ADFGetStarted** 폴더에 다음과 같은 내용으로 **ADFCopyTutorialARM.json**이라는 JSON 파일을 만듭니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureStorage",
              "description": "Azure Storage linked service",
              "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
              }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureStorageLinkedServiceName')]",
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>매개 변수 JSON
Azure Resource Manager 템플릿에 대한 매개 변수를 포함하는 **ADFCopyTutorialARM-Parameters.json**이라는 JSON 파일을 만듭니다. 

> [!IMPORTANT]
> storageAccountName 및 storageAccountKey 매개 변수에 대한 Azure Storage 계정의 이름과 키를 지정합니다.  
> 
> sqlServerName, databaseName, sqlServerUserName 및 sqlServerPassword 매개 변수에 대한 Azure SQL 서버, 데이터베이스, 사용자 및 암호를 지정합니다.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
    }
}
```

> [!IMPORTANT]
> 개발, 테스트 및 프로덕션 환경에 별도의 매개 변수 JSON 파일을 두고 동일한 데이터 팩터리 JSON 템플릿을 사용할 수 있습니다. Power Shell 스크립트를 사용하여 이러한 환경에서 데이터 팩터리 엔터티 배포를 자동화할 수 있습니다.  
> 
> 

## <a name="create-data-factory"></a>데이터 팩터리 만들기
1. **Azure PowerShell** 을 시작하고 다음 명령을 실행합니다.
   * 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
   
    ```PowerShell
    Connect-AzureRmAccount      
    ```  
   * 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.
   
    ```PowerShell
    Get-AzureRmSubscription
    ```   
   * 다음 명령을 실행하여 사용하려는 구독을 선택합니다.
    
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```    
2. 1단계에서 만든 Resource Manager 템플릿을 사용하여 데이터 팩터리 엔터티를 배포하려면 다음 명령을 실행합니다.

    ```PowerShell   
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>파이프라인 모니터링

1. Azure 계정을 사용하여 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **데이터 팩터리**를 클릭하거나 **모든 서비스**를 클릭하고 **INTELLIGENCE + ANALYTICS** 범주 아래에서 **데이터 팩터리**를 클릭합니다.
   
    ![데이터 팩터리 메뉴](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. **데이터 팩터리** 페이지에서 데이터 팩터리(AzureBlobToAzureSQLDatabaseDF)를 검색하고 찾습니다. 
   
    ![데이터 팩터리 검색](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Azure Data Factory를 클릭합니다. 데이터 팩터리의 홈 페이지가 표시됩니다.
   
    ![데이터 팩터리의 홈 페이지](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
6. 이 자습서에서 만든 파이프라인과 데이터 집합을 모니터링하려면 [데이터 집합 및 파이프라인 모니터링](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline)의 지침을 참조하세요. Visual Studio는 현재 Data Factory 파이프라인 모니터링을 지원하지 않습니다.
7. 조각이 **준비** 상태일 때 데이터가 Azure SQL Database의 **emp** 테이블에 복사되는지 확인합니다.


이 자습서에서 만든 파이프라인과 데이터집합을 Azure Portal 블레이드를 사용하여 모니터링하는 방법은 [데이터 집합 및 파이프라인 모니터링](data-factory-monitor-manage-pipelines.md)을 참조하세요.

모니터링 및 관리 응용 프로그램을 사용하여 데이터 파이프라인을 모니터링하는 방법에 대한 자세한 내용은 [모니터링 앱을 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

## <a name="data-factory-entities-in-the-template"></a>템플릿의 데이터 팩터리 엔터티
### <a name="define-data-factory"></a>데이터 팩터리 정의
다음 예제에서처럼 Resource Manager 템플릿에서 데이터 팩터리를 정의합니다.  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```

dataFactoryName은 다음과 같이 정의됩니다. 

```json
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

리소스 그룹 ID를 기반으로 하는 고유 문자열입니다.  

### <a name="defining-data-factory-entities"></a>데이터 팩터리 엔터티 정의
다음 데이터 팩터리 엔터티는 JSON 템플릿에 정의됩니다. 

1. [Azure Storage 연결된 서비스](#azure-storage-linked-service)
2. [Azure SQL 연결된 서비스](#azure-sql-database-linked-service)
3. [Azure Blob 데이터 집합](#azure-blob-dataset)
4. [Azure SQL 데이터 집합](#azure-sql-dataset)
5. [복사 작업을 포함하는 데이터 파이프라인](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
AzureStorageLinkedService는 Azure 저장소 계정을 데이터 팩터리에 연결합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 컨테이너를 만들고 이 저장소 계정에 데이터를 업로드했습니다. 이 섹션의 Azure 저장소 계정 이름 및 키를 지정합니다. Azure Storage 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 참조하세요. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```

connectionString은 storageAccountName 및 storageAccountKey 매개 변수를 사용합니다. 이러한 매개 변수의 값은 구성 파일을 사용하여 전달됩니다. 정의 또한 템플릿에 정의된 azureStroageLinkedService 및 dataFactoryName 변수를 사용합니다. 

#### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스
AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. Blob 저장소에서 복사된 데이터는 이 데이터베이스에 저장됩니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다. 이 섹션에서 Azure SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 사용자 암호를 지정합니다. Azure SQL 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure SQL 연결된 서비스](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString은 sqlServerName, databaseName, sqlServerUserName, sqlServerPassword 매개 변수를 사용하며 해당 값은 구성 파일을 사용하여 전달됩니다. 정의 또한 템플릿에서 azureSqlLinkedServiceName, dataFactoryName 변수를 사용합니다.

#### <a name="azure-blob-dataset"></a>Azure Blob 데이터 집합
Azure 저장소 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure 저장소 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. Azure Blob 데이터 집합 정의에서 입력 데이터를 포함하는 Blob 컨테이너, 폴더 및 파일의 이름을 지정합니다. Azure Blob 데이터 집합을 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 집합 속성](data-factory-azure-blob-connector.md#dataset-properties)을 참조하세요. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]",
      "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL 데이터 집합
Azure Blob 저장소에서 복사된 데이터를 보유하는 Azure SQL Database의 테이블 이름을 지정합니다. Azure SQL 데이터 집합을 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 집합 속성](data-factory-azure-sql-connector.md#dataset-properties)을 참조하세요. 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>데이터 파이프라인
Azure Blob 데이터 집합에서Azure SQL 데이터 집합으로 데이터를 복사하는 파이프라인을 정의합니다. 이 예에서 파이프라인을 정의하는 데 사용된 JSON 요소에 대한 자세한 설명은 [파이프라인 JSON](data-factory-create-pipelines.md#pipeline-json)을 참조하세요. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>템플릿 재사용
이 자습서에서는 데이터 팩터리 엔터티를 정의하는 템플릿과 매개 변수 값을 전달하는 템플릿을 만들었습니다. 이 파이프라인은 Azure Storage 계정에서 매개 변수를 통해 지정된 Azure SQL Database로 데이터를 복사합니다. 같은 템플릿을 사용하여 데이터 팩터리 엔터티를 다른 환경에 배포하는 데 사용하려면 각 환경에 대한 매개 변수 파일을 만들고 해당 환경에 배포할 때 사용합니다.     

예:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

첫 번째 명령은 개발 환경에 대한 매개 변수 파일, 두 번째 명령은 테스트 환경에 대한 매개 변수 파일, 세 번째 명령은 프로덕션 환경에 대한 매개 변수 파일을 사용합니다.  

또한 이 템플릿을 재사용하여 반복 작업을 수행할 수 있습니다. 예를 들어 동일한 논리를 구현하는 하나 이상의 파이프라인으로 여러 데이터 팩터리를 만들어야 하는데 각 데이터 팩터리가 서로 다른 Storage 및 SQL Database 계정을 사용한다고 가정해 보겠습니다. 이 경우 매개 변수가 서로 다른 동일한 환경(개발, 테스트 또는 프로덕션)에서 동일한 템플릿을 사용합니다.   

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 저장소를 원본 데이터 저장소로 사용하고 Azure SQL 데이터베이스를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요.
