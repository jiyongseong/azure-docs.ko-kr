---
title: Azure Data Factory를 사용하여 Machine Learning 모델 업데이트 | Microsoft Docs
description: Azure Data Factory 및 기계 학습을 사용하여 예측 파이프라인을 만드는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: f84bb3546260622eec921684be8e2492adcdcd52
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>리소스 업데이트 작업을 사용하여 Azure Machine Learning 모델 업데이트
이 문서는 주요 Azure Data Factory - Azure Machine Learning 통합 문서인 [Azure Machine Learning 및 Azure Data Factory를 사용하여 예측 파이프라인 만들기](transform-data-using-machine-learning.md)를 보완합니다. 수행하지 않았다면 이 문서를 읽기 전에 기본 문서를 검토하세요. 

## <a name="overview"></a>개요
Azure Machine Learning 모델을 조작하는 프로세스의 일부로 모델을 학습하고 저장합니다. 그런 다음 이를 서술적 웹 서비스를 만드는 데 사용합니다. 그러면 웹 사이트, 대시보드 및 모바일 앱에서 웹 서비스를 사용할 수 있습니다.

Machine Learning을 사용하여 만드는 모델은 일반적으로 정적이지 않습니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다. Azure Machine Learning에서 모델을 다시 학습하는 방법에 대한 자세한 내용은 [Machine Learning 모델 재학습](../machine-learning/machine-learning-retrain-machine-learning-model.md)을 참조하세요. 

재학습은 자주 발생할 수 있습니다. 일괄 처리 실행 작업 및 리소스 업데이트 작업을 사용하면 Azure Machine Learning 모델을 조작하여 Data Factory를 통해 예측 웹 서비스를 다시 학습하고 업데이트할 수 있습니다. 

다음 그림에서는 학습 및 예측 웹 서비스 간의 관계를 보여 줍니다. 

![웹 서비스](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning 리소스 업데이트 작업 

다음 JSON 코드 조각에서는 Azure Machine Learning 일괄 처리 실행 작업을 정의합니다.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| 자산                      | 설명                              | 필수 |
| :---------------------------- | :--------------------------------------- | :------- |
| 이름                          | 파이프라인의 작업 이름입니다.     | 예      |
| description                   | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 아니오       |
| 형식                          | Azure Machine Learning 리소스 업데이트 작업의 경우 작업 유형은 **AzureMLUpdateResource**입니다. | 예      |
| linkedServiceName             | updateResourceEndpoint 속성을 포함하는 Azure Machine Learning 연결된 서비스입니다. | 예      |
| trainedModelName              | 업데이트할 웹 서비스 실험의 학습된 모델 모듈의 이름입니다. | 예      |
| trainedModelLinkedServiceName | 업데이트 작업으로 업로드되는 ilearner 파일을 보유한 Azure Storage 연결된 서비스의 이름입니다. | 예      |
| trainedModelFilePath          | 업데이트 작업으로 업로드되는 ilearner 파일을 나타내는 trainedModelLinkedService의 상대 파일 경로입니다. | 예      |


## <a name="end-to-end-workflow"></a>종단 간 워크플로

모델을 다시 학습하고 예측 웹 서비스를 업데이트하는 전체 프로세스에는 다음 단계가 포함됩니다. 

- **일괄 처리 실행 작업**을 사용하여 **학습 웹 서비스**를 호출합니다. 학습 웹 서비스 호출은 [Azure Machine Learning 및 Azure Data Factory 일괄 처리 실행 작업을 사용하여 예측 파이프라인 만들기](transform-data-using-machine-learning.md)에서 설명하는 예측 웹 서비스 호출과 동일합니다. 학습 웹 서비스의 출력은 예측 웹 서비스를 업데이트하는 데 사용할 수 있는 iLearner 파일입니다. 
- **리소스 업데이트 작업**을 통해 **예측 웹 서비스**의 **리소스 업데이트 끝점**을 호출하여 새로운 학습된 모델을 통해 웹 서비스를 업데이트합니다. 

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning 연결된 서비스

위에서 언급한 종단 간 워크플로가 작동하려면 다음 두 개의 Azure Machine Learning 연결된 서비스를 만들어야 합니다. 

1. 학습 웹 서비스에 대한 Azure Machine Learning 연결된 서비스 - 일괄 처리 실행 작업에서 [Azure Machine Learning 및 Azure Data Factory 일괄 처리 실행 작업을 사용하여 예측 파이프라인 만들기](transform-data-using-machine-learning.md)에서 설명한 것과 동일한 방식으로 사용됩니다. 차이점은 학습 웹 서비스의 출력이 리소스 업데이트 작업에서 예측 웹 서비스를 업데이트하는 데 사용하는 iLearner 파일이라는 것입니다. 
2. 예측 웹 서비스의 리소스 업데이트 끝점에 대한 Azure Machine Learning 연결된 서비스 - 리소스 업데이트 작업에서 위의 단계에서 반환된 iLearner 파일을 사용하여 예측 웹 서비스를 업데이트하는 데 사용됩니다. 

두 번째 Azure Machine Learning 연결된 서비스의 경우, Azure Machine Learning 웹 서비스가 기본 웹 서비스 또는 새 웹 서비스일 때 구성이 다릅니다. 차이점은 다음 섹션에서 별도로 설명합니다. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>새 Azure Resource Manager 웹 서비스 

웹 서비스가 Azure Resource Manager 끝점을 노출하는 새로운 유형의 웹 서비스인 경우 두 번째 **기본이 아닌** 끝점을 추가할 필요가 없습니다. 연결된 서비스의 **updateResourceEndpoint**는 다음 형식을 갖습니다. 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning 웹 서비스 포털](https://services.azureml.net/)에서 웹 서비스를 쿼리할 때 URL에 대한 자리 표시자 값을 가져올 수 있습니다. 

새 유형의 리소스 업데이트 끝점에는 서비스 주체 인증이 필요합니다. 서비스 주체 인증을 사용하려면 Azure AD(Azure Active Directory)에 응용 프로그램 엔터티를 등록하고, 웹 서비스가 속한 구독 또는 리소스 그룹의 **참가자** 또는 **소유자** 역할을 이 엔터티에 부여합니다. [서비스 주체를 만들고 Azure 리소스를 관리하기 위한 권한을 할당하는 방법](../azure-resource-manager/resource-group-create-service-principal-portal.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

- 응용 프로그램 UI
- 응용 프로그램 키 
- 테넌트 ID

샘플 연결된 서비스 정의는 다음과 같습니다. 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

다음 시나리오는 보다 자세한 내용을 제공합니다. Azure Data Factory 파이프라인에서 Azure ML 모델을 재학습 및 업데이트하는 예제가 포함되어 있습니다.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>샘플: Azure Machine Learning 모델 재학습 및 업데이트

이 섹션에서는 **Azure ML Batch 실행 작업** 을 사용하여 모델을 재학습하는 샘플 파이프라인을 제공합니다. 파이프라인은 또한 **Azure ML 업데이트 리소스 작업** 을 사용하여 점수 매기기 웹 서비스에서 모델을 업데이트합니다. 섹션에서는 또한 모든 연결된 서비스, 데이터 집합 및 파이프라인에 대한 JSON 코드 조각 예제도 제공합니다.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob 저장소 연결된 서비스:
Azure Storage는 다음 데이터를 보관합니다.

* 학습 데이터. Azure ML 학습 웹 서비스에 대한 입력 데이터입니다.  
* iLearner 파일. Azure ML 학습 웹 서비스에서의 출력입니다. 이 파일은 업데이트 리소스 작업에 대한 입력이기도 합니다.  

연결된 서비스의 샘플 JSON 정의는 다음과 같습니다.

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML 학습 끝점에 대한 연결된 서비스
다음 JSON 코드 조각은 학습 웹 서비스의 기본 끝점을 가리키는 Azure Machine Learning에 연결된 서비스를 정의합니다.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

**Azure ML Studio**에서 다음을 수행하여 **mlEndpoint** 및 **apiKey**에 대한 값을 가져옵니다.

1. 왼쪽 메뉴에서 **웹 서비스** 를 클릭합니다.
2. 웹 서비스 목록에서 **학습 웹 서비스** 를 클릭합니다.
3. **API 키** 텍스트 상자 옆의 복사를 클릭합니다. 클립보드의 키를 Data Factory JSON 편집기에 붙여넣습니다.
4. **Azure ML studio**에서 **배치 실행** 링크를 클릭합니다.
5. **요청** 섹션에서 **요청 URI**를 복사하여 Data Factory JSON 편집기에 붙여넣습니다.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML 업데이트 가능한 점수 매기기 끝점에 대한 연결된 서비스:
다음 JSON 코드 조각에서는 점수 매기기 웹 서비스의 업데이트 가능한 끝점을 가리키는 Azure Machine Learning 연결된 서비스를 정의합니다.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>파이프라인
파이프라인에는 **AzureMLBatchExecution** 및 **AzureMLUpdateResource**라는 두 활동이 있습니다. 일괄 처리 실행 작업에서는 학습 데이터를 입력으로 가져오고, iLearner 파일을 출력으로 생성합니다. 그런 다음 리소스 업데이트 작업에서 이 iLearner 파일을 가져오고, 이를 사용하여 예측 웹 서비스를 업데이트합니다. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
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
