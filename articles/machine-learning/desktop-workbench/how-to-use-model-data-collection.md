---
title: Azure Machine Learning Workbench에서 모델 데이터 컬렉션 기능 사용 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning Workbench에서 모델 데이터 컬렉션 기능을 사용하는 방법에 대해 설명합니다.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>데이터 컬렉션을 사용하여 모델 데이터 수집

Azure Machine Learning에서 모델 데이터 컬렉션 기능을 사용하여 웹 서비스의 모델 입력 및 예측을 보관할 수 있습니다.

## <a name="install-the-data-collection-package"></a>데이터 컬렉션 패키지 설치
기본적으로 Linux 및 Windows에 데이터 컬렉션 라이브러리를 설치할 수 있습니다.

### <a name="windows"></a>Windows
Windows에서는 다음 명령을 사용하여 데이터 수집기 모듈을 설치합니다.

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linux에서는 libxml++ 라이브러리를 먼저 설치합니다. 다음 명령을 실행합니다. 이 명령은 sudo에서 실행해야 합니다.

    sudo apt-get install libxml++2.6-2v5

그런 후 다음 명령을 실행합니다.

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>환경 변수 설정

모델 데이터 컬렉션은 두 개의 환경 변수에 의존합니다. AML_MODEL_DC_STORAGE_ENABLED는 **true**(모두 소문자)로 설정되어야 하며 AML_MODEL_DC_STORAGE는 데이터를 저장하려는 Azure Storage 계정에 대한 연결 문자열로 설정되어야 합니다.

웹 서비스가 Azure의 클러스터에서 실행되는 경우 이러한 환경 변수는 이미 설정되어 있습니다. 로컬로 실행하는 경우 사용자가 직접 설정해야 합니다. Docker를 사용하는 경우 docker run 명령의 -e 매개 변수를 사용하여 환경 변수를 전달합니다.

## <a name="collect-data"></a>데이터 수집

모델 데이터 컬렉션을 사용하려면 점수 매기기 파일을 다음과 같이 변경합니다.

1. 파일 맨 위에 다음 코드를 추가합니다.
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. `init()` 함수에 다음 코드 줄을 추가합니다.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. `run(input_df)` 함수에 다음 코드 줄을 추가합니다.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    `input_df` 및 `pred` 변수(`model.predict()`의 예측 값)를 초기화한 후 `collect()` 함수를 호출해야 합니다.

4. `--collect-model-data true` 스위치에 `az ml service create realtime` 명령을 사용하여 실시간 웹 서비스를 만듭니다. 이 단계를 통해 서비스가 실행되는 동안 모델 데이터가 수집됩니다.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. 데이터 컬렉션을 테스트하려면 `az ml service run realtime` 명령을 실행합니다.

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>수집된 데이터 보기
blob 저장소에 수집된 데이터를 보려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택합니다.
3. 검색 상자에서 **저장소 계정**을 입력하고 Enter 키를 선택합니다.
4. **저장소 계정** 검색 블레이드에서 **저장소 계정** 리소스를 선택합니다. 저장소 계정을 확인하려면 다음 단계를 사용합니다.

    a. Azure Machine Learning Workbench로 이동하여 작업 중인 프로젝트를 선택하고, **파일** 메뉴에서 명령 프롬프트를 엽니다.
    
    나. `az ml env show -v`를 입력하고 *storage_account* 값을 선택합니다. 이는 저장소 계정의 이름입니다.

5. 리소스 블레이드 메뉴에서 **컨테이너**를 선택한 다음 **modeldata**라는 컨테이너를 선택합니다. 데이터가 저장소 계정에 전파되는 것을 보려면 첫 번째 웹 서비스 요청 후 10분 정도 기다려야 할 수 있습니다. 데이터는 다음 컨테이너 경로를 사용하여 Blob으로 흐릅니다.

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Microsoft 소프트웨어 및 오픈 소스 도구를 통해 다양한 방법으로 Azure Blob에서 데이터를 사용할 수 있습니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.
- Azure Machine Learning Workbench: Azure Machine Learning Workbench에서 .csv 파일을 데이터 원본으로 추가하여 .csv 파일을 엽니다.
- Excel: 일별 .csv 파일을 스프레드시트로 엽니다.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): Blob의 .csv 데이터에서 가져온 데이터로 차트를 만듭니다.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): .csv 데이터의 큰 부분으로 데이터 프레임을 만듭니다.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): .csv 데이터를 Hive 테이블에 로드하고, Blob에 대해 SQL 쿼리를 직접 수행합니다.

