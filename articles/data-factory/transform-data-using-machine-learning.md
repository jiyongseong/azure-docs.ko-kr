---
title: Azure Data Factory를 사용하여 예측 데이터 파이프라인 만들기 | Microsoft Docs
description: Azure Data Factory에서 Azure Machine Learning - Batch 실행 작업을 사용하여 예측 파이프라인을 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 1bb4ec7cbe9e5b4ab51b4929fc63401c543d5244
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Azure Machine Learning 및 Azure Data Factory를 사용하여 예측 파이프라인 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [버전 2 - 미리 보기](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)을 사용하여 예측 분석 솔루션을 빌드, 테스트 및 배포할 수 있습니다. 대략적인 관점에서 이 작업은 다음 세 단계로 수행됩니다.

1. **학습 실험 만들기**. Azure ML Studio를 사용하여 이 단계를 수행합니다. ML Studio는 학습 데이터를 사용하여 예측 분석 모델을 학습하고 테스트하는 데 사용하는 시각적 공동 개발 환경입니다.
2. **예측 실험으로 변환**. 기존 데이터로 모델을 학습시키고 새 데이터의 점수를 매기는 데 사용할 준비가 되면, 점수 매기기를 위해 실험을 준비하고 간소화합니다.
3. **웹 서비스로 배포**. 점수 매기기 실험을 Azure 웹 서비스로 게시할 수 있습니다. 이 웹 서비스 끝점을 통해 데이터를 모델로 전송하고 모델로부터 결과 예측을 받을 수 있습니다.  

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Machine Learning Batch 실행 작업](v1/data-factory-azure-ml-batch-execution-activity.md)을 참조하세요.


### <a name="data-factory-and-machine-learning-together"></a>Data Factory 및 Machine Learning
Azure Data Factory를 사용하면 예측 분석을 위해 게시된 [Azure Machine Learning][azure-machine-learning] 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. Azure 데이터 팩터리 파이프라인에서 **Batch 실행 작업** 을 사용하여 Azure ML 웹 서비스를 호출하고 배치에 있는 데이터에 대한 예측을 할 수 있습니다. 

시간이 지남에 따라 Azure ML 점수 매기기 실험의 예측 모델은 새 입력 데이터 집합을 사용하여 다시 학습되어야 합니다. 다음 단계를 수행하여 데이터 팩터리 파이프라인에서 Azure ML 모델을 다시 학습할 수 있습니다.

1. 웹 서비스로 학습 실험(예측 실험 아님)을 게시합니다. 이전 시나리오에서 웹 서비스로 예측 실험을 노출했으므로 Azure ML Studio에서 이 단계를 수행합니다.
2. Azure ML Batch 실행 작업을 사용하여 학습 실험을 위한 웹 서비스를 호출합니다. 기본적으로, Azure ML Batch 실행 작업을 사용하여 학습 웹 서비스와 점수 매기기 웹 서비스를 모두 호출할 수 있습니다.

재학습으로 완료한 후에는 **Azure ML 업데이트 리소스 작업**을 사용하여 새로 학습한 모델로 점수 매기기 웹 서비스(웹 서비스로 노출된 예측 실험)를 업데이트합니다. 자세한 내용은 [업데이트 리소스 작업을 사용하여 모델 업데이트](update-machine-learning-models.md) 문서를 참조하세요.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning 연결된 서비스

**Azure Machine Learning** 연결된 서비스를 만들어 Azure Data Factory에 Azure Machine Learning 웹 서비스를 연결합니다. Azure Machine Learning Batch 실행 작업 및 [업데이트 리소스 작업](update-machine-learning-models.md)에서 연결된 서비스를 사용합니다. 


```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "apiVersion" : "2017-09-01-preview",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

JSON 정의에서 속성에 대한 설명은 [연결된 Compute Services](compute-linked-services.md) 문서를 참조하세요. 

Azure Machine Learning은 예측 실험에 클래식 웹 서비스 및 새 웹 서비스를 모두 지원합니다. Data Factory에서 사용할 적합한 기능을 선택할 수 있습니다. Azure Machine Learning 연결된 서비스를 만드는 데 필요한 정보를 가져오려면 모든 새 웹 서비스 및 클래식 웹 서비스가 나열된 https://services.azureml.net으로 이동합니다. 액세스하려는 웹 서비스를 클릭하고 **사용** 페이지를 클릭합니다. **apiKey** 속성의 **기본 키** 및 **mlEndpoint** 속성의 **Batch 요청**을 복사합니다. 

![Azure Machine Learning 웹 서비스](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning Batch 실행 작업

다음 JSON 코드 조각은 Azure Machine Learning Batch 실행 동작을 정의합니다. 작업 정의에는 앞에서 만든 Azure Machine Learning 연결된 서비스에 대한 참조가 있습니다. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| 자산          | 설명                              | 필수 |
| :---------------- | :--------------------------------------- | :------- |
| 이름              | 파이프라인의 작업 이름입니다.     | 예      |
| description       | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 아니오       |
| 형식              | Data Lake Analytics U-SQL 작업의 경우 작업 형식은 **AzureMLBatchExecution**입니다. | 예      |
| linkedServiceName | Azure Machine Learning 연결된 서비스에 연결된 서비스입니다. 이 연결된 서비스에 대한 자세한 내용은 [연결된 Compute Services](compute-linked-services.md) 문서를 참조하세요. | 예      |
| webServiceInputs  | Azure Machine Learning 웹 서비스 입력의 이름을 매핑하는 키, 값 쌍입니다. 키는 게시된 Azure Machine Learning 웹 서비스에 정의된 입력 매개 변수와 일치해야 합니다. 값은 Azure Storage 연결된 서비스 및 입력 Blob 위치를 지정하는 FilePath 속성 쌍입니다. | 아니오       |
| webServiceOutputs | Azure Machine Learning 웹 서비스 출력의 이름을 매핑하는 키, 값 쌍입니다. 키는 게시된 Azure Machine Learning 웹 서비스에 정의된 출력 매개 변수와 일치해야 합니다. 값은 Azure Storage 연결된 서비스 및 출력 Blob 위치를 지정하는 FilePath 속성 쌍입니다. | 아니오       |
| globalParameters  | Azure ML Batch 실행 서비스 끝점에 전달되는 키, 값 쌍입니다. 키는 게시된 Azure ML 웹 서비스에 정의된 웹 서비스 매개 변수의 이름과 일치해야 합니다. 값은 Azure ML Batch 실행 요청의 GlobalParameters 속성에서 전달됩니다. | 아니오       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>시나리오1: Azure Blob Storage의 데이터를 참조하는 웹 서비스 입력/출력을 사용하여 실험

이 시나리오에서는 Azure Machine Learning 웹 서비스는 Azure Blob 저장소의 파일에서 데이터를 사용하여 예측을 만들고 Blob 저장소에 예측 결과를 저장합니다. 다음 JSON은 AzureMLBatchExecution 작업이 포함된 Data Factory 파이프라인을 정의합니다. Azure 블로그 저장소의 입력 및 출력 데이터는 LinkedName 및 FilePath 쌍을 사용하여 참조됩니다. 입력 및 출력의 연결된 서비스가 다른 샘플에서 올바른 파일을 선택하고 Azure ML 웹 서비스에 보낼 수 있도록 각 Data Factory의 입력/출력에 다른 연결된 서비스를 사용할 수 있습니다. 

> [!IMPORTANT]
> Azure ML 실험에서 웹 서비스 입력 및 출력 포트와 전역 매개 변수에는 사용자 지정할 수 있는 기본 이름("input1", "input2")이 붙어있습니다. WebServiceInputs, webServiceOutputs 및 globalParameters 설정에 대해 사용하는 이름은 실험에서의 이름과 정확히 일치해야 합니다. 예상된 매핑을 확인하기 위해 일괄 처리 실행 도움말 페이지에서 Azure ML 끝점에 대한 샘플 요청 페이로드를 볼 수 있습니다.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>시나리오2: 다양한 저장소의 데이터를 참조하는 판독기/기록기 모듈을 사용하여 실험
Azure ML 실험을 만들 때 다른 일반적인 시나리오는 데이터 가져오기 및 출력 데이터 모듈을 사용하는 것입니다. 데이터 가져오기 모듈은 실험으로 데이터를 로드할 때 사용되고 출력 데이터 모듈은 실험에서 데이터를 저장할 때 사용됩니다. 데이터 가져오기 및 출력 데이터 모듈에 대한 세부 정보는 MSDN Library에서 [데이터 가져오기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [출력 데이터](https://msdn.microsoft.com/library/azure/dn905984.aspx) 항목을 참조하세요.     

데이터 가져오기 및 출력 데이터 모듈을 사용하는 경우 이러한 모듈의 각 속성에 웹 서비스 매개 변수를 사용하는 것이 좋습니다. 이러한 웹 매개 변수를 통해 런타임 중 값을 구성할 수 있습니다. 예를 들어 Azure SQL Database: XXX.database.windows.net을 사용하는 데이터 가져오기 모듈을 사용하여 실험을 만들 수 있습니다. 웹 서비스를 배포한 후 웹 서비스의 소비자가 `YYY.database.windows.net`이라는 다른 Azure SQL Server를 지정할 수 있도록 하려고 합니다. 웹 서비스 매개 변수를 사용하여 이 값을 구성할 수 있습니다.

> [!NOTE]
> 웹 서비스 입력 및 출력은 웹 서비스 매개 변수와 다릅니다. 첫 번째 시나리오에서 Azure ML 웹 서비스에 대해 입력 및 출력을 지정할 수 있는 방법을 살펴보았습니다. 이 시나리오에서는 데이터 가져오기/출력 데이터 모듈의 속성에 해당하는 웹 서비스에 매개 변수를 전달합니다.
>
> 

웹 서비스 매개 변수를 사용하는 시나리오를 살펴보겠습니다. Azure Machine Learning에서 지원하는 데이터 원본(예: Azure SQL Database) 중 하나에서 데이터를 읽는 판독기 모듈을 사용하는 Azure Machine Learning 웹 서비스를 배포했습니다. 배치 실행이 수행된 후 기록기 모듈(Azure SQL Database)을 사용하여 결과가 기록됩니다.  웹 서비스 입력 및 출력이 실험에서 정의되지 않습니다. 이 경우 판독기 및 기록기 모듈에 대한 관련 웹 서비스 매개 변수를 구성하는 것이 좋습니다. 이 구성을 통해 AzureMLBatchExecution 작업을 사용하는 경우 판독기/기록기 모듈을 구성할 수 있습니다. 다음과 같이 작업 JSON의 **globalParameters** 섹션에서 웹 서비스 매개 변수를 지정합니다.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> 웹 서비스 매개 변수는 대/소문자를 구분하므로 작업 JSON에서 지정한 이름이 웹 서비스에 의해 노출된 이름과 일치해야 합니다.
>

재학습으로 완료한 후에는 **Azure ML 업데이트 리소스 작업**을 사용하여 새로 학습한 모델로 점수 매기기 웹 서비스(웹 서비스로 노출된 예측 실험)를 업데이트합니다. 자세한 내용은 [업데이트 리소스 작업을 사용하여 모델 업데이트](update-machine-learning-models.md) 문서를 참조하세요.



## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
