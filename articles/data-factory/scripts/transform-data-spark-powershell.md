---
title: PowerShell 스크립트 - Data Factory를 사용하여 클라우드의 데이터 변환 | Microsoft Docs
description: 이 PowerShell 스크립트는 Azure HDInsight Spark 클러스터에서 Spark 프로그램을 실행하여 클라우드의 데이터를 변환합니다.
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: 27458a48c04a6d4ec612252dc298d454a48cf009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell 스크립트 - Azure Data Factory를 사용하여 클라우드의 데이터 변환

이 샘플 PowerShell 스크립트는 Azure HDInsight Spark 클러스터에서 Spark 프로그램을 실행하여 클라우드의 데이터를 변환하는 파이프라인을 만듭니다. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>필수 조건
* **Azure Storage 계정**. Python 스크립트와 입력 파일을 만들고 Azure Storage에 업로드합니다. Spark 프로그램의 출력은 이 저장소 계정에 저장됩니다. 주문형 Spark 클러스터는 기본 저장소와 동일한 저장소 계정을 사용합니다.  

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
2. **&lt;storageAccountName&gt;**을 Azure Storage 계정 이름으로 바꿉니다. 그런 다음 파일을 저장합니다. 
3. Azure Blob Storage에 아직 없는 경우 **adftutorial**이라는 컨테이너를 만듭니다. 
4. **spark**라는 폴더를 만듭니다.
5. **spark** 폴더 아래에 **script**라는 하위 폴더를 만듭니다. 
6. **script** 하위 폴더에 **WordCount_Spark.py** 파일을 업로드합니다. 


### <a name="upload-the-input-file"></a>입력 파일 업로드
1. 일부 텍스트가 포함된 **minecraftstory.txt**라는 파일을 만듭니다. Spark 프로그램은 이 텍스트의 단어 수를 계산합니다. 
2. Blob 컨테이너의 `spark` 폴더에 `inputfiles`이라는 하위 폴더를 만듭니다. 
3. `inputfiles` 하위 폴더에 `minecraftstory.txt`를 업로드합니다. 

## <a name="sample-script"></a>샘플 스크립트
> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
리소스 그룹에서 Data Factory를 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 작업이 있습니다. 이 파이프라인에서 Spark 작업은 Azure HDInsight Spark 클러스터에서 프로그램을 실행하여 데이터를 변환합니다. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Data Factory PowerShell 스크립트 샘플은 [Azure Data Factory PowerShell 샘플](../samples-powershell.md)에 있습니다.