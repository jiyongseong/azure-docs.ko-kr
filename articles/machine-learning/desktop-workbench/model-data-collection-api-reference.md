---
title: Azure Machine Learning 모델 데이터 수집 API 참조 | Microsoft Docs
description: Azure Machine Learning 모델 데이터 수집 API 참조
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
ms.openlocfilehash: db8718b60cdd934e3301479489f21fe45f5578cd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Azure Machine Learning 모델 데이터 수집 API 참조

모델 데이터 수집을 사용하면 Machine Learning 웹 서비스의 모델 입력 및 예측을 보관할 수 있습니다. Windows 및 Linux 컴퓨터에 `azureml.datacollector`를 설치하는 방법을 이해하려면 [모델 데이터 수집 방법 가이드](how-to-use-model-data-collection.md)를 참조하세요.

이 API 참조 가이드에서는 Machine Learning 웹 서비스에서 모델 입력 및 예측을 수집하는 방법에 대한 단계별 접근 방식을 사용합니다.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Azure ML Workbench 환경에서 모델 데이터 수집 사용

 aml_config 폴더 아래의 프로젝트에서 conda\_dependencies.yml 파일을 찾은 후 아래와 같이 conda\_dependencies.yml 파일의 pip 섹션 아래에 azureml.datacollector 모듈을 포함합니다. 이 내용은 전체 conda\_dependencies.yml 파일의 일부에 불과합니다.

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>현재, docker 모드에서 실행하여 Azure ML Workbench에서 데이터 수집기 모듈을 사용할 수 있습니다. 로컬 모드는 일부 환경에서만 작동할 수 있습니다.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>scoring 파일에서 모델 데이터 수집 사용

연산화에 대해 사용되는 scoring 파일에서 데이터 수집기 모듈 및 ModelDataCollector 클래스를 가져옵니다.

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>모델 데이터 수집기 인스턴스화
ModelDataCollector의 새 인스턴스를 인스턴스화합니다.

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

클래스 및 매개 변수 정보를 참조하세요.

### <a name="class"></a>클래스
| Name | 설명 |
|--------------------|--------------------|
| ModelDataCollector | azureml.datacollector 네임스페이스의 클래스. 이 클래스의 인스턴스는 모델 데이터를 수집하는 데 사용됩니다. 단일 scoring 파일에는 여러 ModelDataCollector가 포함될 수 있습니다. 수집된 데이터의 스키마가 일관되게 유지되도록(즉, 입력 및 예측) 각 인스턴스가 scoring 파일의 별도 위치에 데이터를 수집하는 데 사용되어야 합니다.|


### <a name="parameters"></a>매개 변수

| Name | type | 설명 |
|-------------|------------|-------------------------|
| model_name | string | 데이터를 수집하는 모델의 이름 |
| identifier | string | 이 데이터를 식별하는 코드의 위치(예: 'RawInput' 또는 'Prediction') |
| feature_names | 문자열 목록 | 제공될 때 csv 헤더가 되는 기능 이름 목록 |
| model_management_account_id | string | 이 모델이 저장된 모델 관리 계정에 대한 식별자. 모델이 AML을 통해 연산화될 때 자동으로 채워집니다. |
| webservice_name | string | 현재 이 모델이 배포되는 웹 서비스의 이름. 모델이 AML을 통해 연산화될 때 자동으로 채워집니다. |
| model_id | string | 모델 관리 계정의 컨텍스트에서 이 모델의 고유 식별자. 모델이 AML을 통해 연산화될 때 자동으로 채워집니다. |
| model_version | string | 모델 관리 계정의 컨텍스트에서 이 모델의 버전 번호. 모델이 AML을 통해 연산화될 때 자동으로 채워집니다. |



 

## <a name="collecting-the-model-data"></a>모델 데이터 수집

위에서 만든 ModelDataCollector의 인스턴스를 사용하여 모델 데이터를 수집할 수 있습니다.

    dc.collect(input_data, user_correlation_id="")

메서드 및 매개 변수 정보를 참조하세요.

### <a name="method"></a>방법
| Name | 설명 |
|--------------------|--------------------|
| collect | 모델 입력 또는 예측에 대한 데이터를 수집하는 데 사용|


### <a name="parameters"></a>매개 변수

| Name | type | 설명 |
|-------------|------------|-------------------------|
| input_data | 여러 형식 | 수집할 데이터(현재 형식 목록, numpy.array, pandas.DataFrame, pyspark.sql.DataFrame이 허용됨). 데이터 프레임 형식의 경우, 기능 이름의 헤더가 존재할 경우 데이터 대상에 이 정보가 포함됩니다(ModelDataCollector 생성자에 기능 이름을 명시적으로 제공할 필요가 없음). |
| user_correlation_id | string | 이 예측을 상호 연관짓기 위해 사용자가 제공할 수 있는 선택적 상관 관계 ID |

