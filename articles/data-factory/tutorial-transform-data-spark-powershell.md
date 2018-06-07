---
title: Azure Data Factory에서 Spark를 사용하여 데이터 변환 | Microsoft Docs
description: 이 자습서에서는 Azure Data Factory에서 Spark 작업을 사용하여 데이터를 변환하는 단계별 지침을 제공합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: c8676b4e7228105216f23de7c898c43c40fc2d0a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory에서 Spark 작업을 사용하여 클라우드의 데이터 변환
이 자습서에서는 Azure PowerShell을 사용하여 Spark 작업 및 주문형 HDInsight 연결된 서비스를 통해 데이터를 변환하는 Data Factory 파이프라인을 만듭니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 연결된 서비스를 작성하고 배포합니다.
> * 파이프라인을 작성하고 배포합니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure Storage 계정**. Python 스크립트와 입력 파일을 만들고 Azure 저장소에 업로드합니다. Spark 프로그램의 출력은 이 저장소 계정에 저장됩니다. 주문형 Spark 클러스터는 기본 저장소와 동일한 저장소 계정을 사용합니다.  
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.


### <a name="upload-python-script-to-your-blob-storage-account"></a>Blob Storage 계정에 Python 스크립트 업로드
1. 다음 내용이 포함된 **WordCount_Spark.py**라는 Python 파일을 만듭니다. 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt;storageAccountName&gt;** 을 Azure Storage 계정 이름으로 바꿉니다. 그런 다음 파일을 저장합니다. 
3. Azure Blob Storage에 아직 없는 경우 **adftutorial**이라는 컨테이너를 만듭니다. 
4. **spark**라는 폴더를 만듭니다.
5. **spark** 폴더 아래에 **script**라는 하위 폴더를 만듭니다. 
6. **script** 하위 폴더에 **WordCount_Spark.py** 파일을 업로드합니다. 


### <a name="upload-the-input-file"></a>입력 파일 업로드
1. 일부 텍스트가 포함된 **minecraftstory.txt**라는 파일을 만듭니다. Spark 프로그램은 이 텍스트의 단어 수를 계산합니다. 
2. `spark` 폴더에 `inputfiles`이라는 하위 폴더를 만듭니다. 
3. `inputfiles` 하위 폴더에 `minecraftstory.txt`를 업로드합니다. 

## <a name="author-linked-services"></a>연결된 서비스 작성
이 섹션에서는 두 개의 연결된 서비스를 작성합니다. 
    
- Azure Storage 계정을 데이터 팩터리에 연결하는 Azure Storage 연결된 서비스 - 이 저장소는 주문형 HDInsight 클러스터에서 사용됩니다. 실행될 Spark 스크립트도 포함되어 있습니다. 
- 주문형 HDInsight 연결된 서비스 - Azure Data Factory는 HDInsight 클러스터를 자동으로 만들고, Spark 프로그램을 실행한 다음, 미리 구성된 시간 동안 유휴 상태가 되면 이 HDInsight 클러스터를 삭제합니다. 

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
원하는 편집기를 사용하여 JSON 파일을 만들고, 다음과 같은 Azure Storage 연결된 서비스의 JSON 정의를 복사하고, 파일을 **MyStorageLinkedService.json**으로 저장합니다.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
&lt;storageAccountName&gt; 및 &lt;storageAccountKey&gt;를 Azure Storage 계정의 이름과 키로 업데이트합니다. 


### <a name="on-demand-hdinsight-linked-service"></a>주문형 HDInsight 연결된 서비스
원하는 편집기를 사용하여 JSON 파일을 만들고, 다음과 같은 Azure HDInsight 연결된 서비스의 JSON 정의를 복사하고, 파일을 **MyOnDemandSparkLinkedService.json**으로 저장합니다.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
연결된 서비스 정의에서 다음 속성 값을 업데이트합니다. 

- **hostSubscriptionId** - &lt;subscriptionID&gt;를 Azure 구독의 ID로 바꿉니다. 이 구독에 주문형 HDInsight 클러스터가 만들어집니다. 
- **tenant** - &lt;tenantID&gt;를 Azure 테넌트의 ID로 바꿉니다. 
- **servicePrincipalId**, **servicePrincipalKey** - &lt;servicePrincipalID&gt; 및 &lt;servicePrincipalKey&gt;를 Azure Active Directory에 있는 서비스 주체의 ID와 키로 바꿉니다. 이 서비스 주체는 클러스터를 만든 구독 또는 리소스 그룹의 참가자 역할의 구성원이어야 합니다. 자세한 내용은 [Azure Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 참조하세요. 
- **clusterResourceGroup** - &lt;resourceGroupOfHDICluster&gt;를 HDInsight 클러스터를 만들어야 하는 리소스 그룹의 이름으로 바꿉니다. 

> [!NOTE]
> Azure HDInsight에는 지원하는 각 Azure 지역에서 사용할 수 있는 총 코어 수에 대한 제한이 있습니다. 주문형 HDInsight 연결된 서비스의 경우 HDInsight 클러스터는 기본 저장소로 사용되는 Azure Storage와 동일한 위치에 만들어집니다. 클러스터를 성공적으로 만드는 데 충분한 코어 할당량이 있는지 확인합니다. 자세한 내용은 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요. 


## <a name="author-a-pipeline"></a>파이프라인 작성 
이 단계에서는 Spark 작업이 있는 새 파이프라인을 만듭니다. 이 작업은 **단어 개수** 샘플을 사용합니다. 아직 다운로드하지 않았으면 이 위치에서 콘텐츠를 다운로드합니다.

원하는 편집기에서 JSON 파일을 만들고, 다음과 같은 파이프라인 정의에 대한 JSON 정의를 복사하고, **MySparkOnDemandPipeline.json**으로 저장합니다. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

다음 사항에 유의하세요. 

- rootPath는 adftutorial 컨테이너의 spark 폴더를 가리킵니다. 
- entryFilePath는 spark 폴더의 script 하위 폴더에 있는 WordCount_Spark.py 파일을 가리킵니다. 


## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다. 
JSON 파일에서 연결된 서비스 및 파이프라인 정의를 작성했습니다. 이제 데이터 팩터리를 만들고 PowerShell cmdlet을 사용하여 연결된 Service 및 파이프라인 JSON 파일을 배포해 보겠습니다. 다음 PowerShell 명령을 개별적으로 실행합니다. 

1. 개별적으로 변수를 설정합니다.

    **리소스 그룹 이름**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **데이터 팩터리 이름. 전역적으로 고유해야 합니다.** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **파이프라인 이름**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 Azure PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

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
3. ADFTutorialResourceGroup 리소스 그룹을 만듭니다. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. 데이터 팩터리를 만듭니다. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    출력을 보려면 다음 명령을 실행합니다. 

    ```powershell
    $df
    ```
5. JSON 파일을 만든 폴더로 전환하고 다음 명령을 실행하여 Azure Storage 연결된 서비스를 배포합니다. 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. 다음 명령을 실행하여 주문형 Spark 연결된 서비스를 배포합니다. 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. 다음 명령을 실행하여 파이프라인을 배포합니다. 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>파이프라인 실행 시작 및 모니터링  

1. 파이프라인 실행을 시작합니다. 또한 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. 다음 스크립트를 실행하여 완료될 때까지 파이프라인 실행 상태를 계속 확인합니다.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. 샘플 실행의 출력은 다음과 같습니다. 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Spark 프로그램의 출력을 사용하여 `outputfiles`라는 폴더가 adftutorial 컨테이너의 `spark` 폴더에 만들어졌는지 확인합니다. 


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 연결된 서비스를 작성하고 배포합니다.
> * 파이프라인을 작성하고 배포합니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다.

가상 네트워크에 있는 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
> [자습서: Azure Virtual Network에서 Hive를 사용하여 데이터 변환](tutorial-transform-data-hive-virtual-network.md).





