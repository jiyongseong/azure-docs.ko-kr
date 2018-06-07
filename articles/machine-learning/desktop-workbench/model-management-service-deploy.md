---
title: Azure Machine Learning 모델 관리 웹 서비스 배포 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 모델 관리를 사용하여 기계 학습 모델을 배포하는 단계에 대해 설명합니다.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 5211fa29af1d8cba17049b69974189990d30f34a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>웹 서비스로 Machine Learning 웹 학습 모델 배포

Azure Machine Learning 모델 관리는 모델을 컨테이너화된 Docker 기반 웹 서비스로 배포하는 인터페이스를 제공합니다. Spark, Microsoft CNTK(Cognitive Toolkit), Keras, Tensorflow 및 Python과 같은 프레임워크를 사용하여 만든 모델을 배포할 수 있습니다. 

이 문서에서는 Azure Machine Learning 모델 관리 CLI(명령줄 인터페이스)를 사용하여 모델을 웹 서비스로 배포하는 단계에 대해 설명합니다.

## <a name="what-you-need-to-get-started"></a>시작에 필요한 항목

이 가이드를 최대한 활용하려면 모델을 배포할 수 있는 Azure 구독 또는 리소스 그룹에 참여자로 액세스할 수 있어야 합니다.
CLI는 Azure Machine Learning Workbench 및 [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview)이 미리 설치되어 있습니다.  독립 실행형 패키지로 설치할 수도 있습니다.

또한 모델 관리 계정 및 배포 환경은 미리 설정되어 있어야 합니다.  로컬 및 클러스터 배포를 위한 모델 관리 계정 및 환경의 설정에 대한 자세한 내용은 [모델 관리 구성](deployment-setup-configuration.md)을 참조하세요.

## <a name="deploying-web-services"></a>웹 서비스 배포
CLI를 사용하여 웹 서비스를 배포하여 로컬 컴퓨터 또는 클러스터에서 실행할 수 있습니다.

로컬 배포부터 시작하는 것이 좋습니다. 먼저 모델과 코드가 작동하는지에 대한 유효성을 검사한 다음 프로덕션 규모로 사용할 클러스터에 웹 서비스를 배포합니다.

배포 단계는 다음과 같습니다.
1. 학습되고 저장된 Machine Learning 모델 사용
2. 웹 서비스의 입력 및 출력 데이터에 대한 스키마 만들기
3. Docker 기반 컨테이너 이미지 만들기
4. 웹 서비스 만들기 및 배포

### <a name="1-save-your-model"></a>1. 모델 저장
학습되고 저장된 모델 파일(예: mymodel.pkl)로 시작합니다. 파일 확장명은 모델을 학습하고 저장하는 데 사용하는 플랫폼에 따라 다릅니다. 

예를 들어 Python의 Pickle 라이브러리를 사용하여 학습된 모델을 파일에 저장할 수 있습니다. 다음은 아이리스 데이터 집합을 사용하는 [예제](http://scikit-learn.org/stable/modules/model_persistence.html)입니다.

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. schema.json 파일 만들기

스키마 생성은 선택 사항이지만, 더 효율적인 처리를 위해 요청 및 입력 변수 형식을 정의하는 것이 좋습니다.

웹 서비스에 대한 입력과 출력의 유효성을 자동으로 검사하는 스키마를 만듭니다. 또한 CLI는 이 스키마를 사용하여 웹 서비스에 대한 Swagger 문서를 생성합니다.

스키마를 만들려면 다음 라이브러리를 가져옵니다.

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
그런 다음 Spark 데이터 프레임, Pandas 데이터 프레임 또는 NumPy 배열과 같은 입력 변수를 정의합니다. 다음 예제에서는 Numpy 배열을 사용합니다.

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
다음 예제에서는 Spark 데이터 프레임을 사용합니다.

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

다음 예제에서는 PANDAS 데이터 프레임을 사용합니다.

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

다음 예제에서는 일반 JSON 형식을 사용합니다.

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. score.py 파일 만들기
모델을 로드하고 모델을 사용하여 예측 결과를 반환하는 score.py 파일을 제공합니다.

이 파일에는 init와 run의 두 함수가 포함되어야 합니다.

score.py 파일의 맨 위에 다음 코드를 추가하여 모델 입력 및 예측 데이터를 수집하는 데 도움이 되는 데이터 수집 기능을 사용하도록 설정합니다.

```python
from azureml.datacollector import ModelDataCollector
```

이 기능을 사용하는 방법에 대한 자세한 내용은 [모델 데이터 수집](how-to-use-model-data-collection.md) 섹션을 확인하세요.

#### <a name="init-function"></a>init 함수
init 함수는 저장된 모델을 로드하는 데 사용됩니다.

모델을 로드하는 간단한 init 함수의 예제:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>run 함수
run 함수는 모델과 입력 데이터를 사용하여 예측을 반환합니다.

입력을 처리하고 예측 결과를 반환하는 간단한 run 함수의 예제:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. 모델 등록
다음 명령은 위의 1단계에서 만든 모델을 등록하는 데 사용됩니다.

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. 매니페스트 만들기
다음 명령은 모델의 매니페스트를 만드는 데 도움이 됩니다.

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
위에 표시된 명령에서 `--model-id` 또는 `-i` 인수를 사용하여 매니페스트에 이전에 등록한 모델을 추가할 수 있습니다. -i 인수를 추가하여 여러 모델을 지정할 수 있습니다.

### <a name="6-create-an-image"></a>6. 이미지 만들기 
이미지는 이전에 매니페스트를 만든 옵션을 사용하여 만들 수 있습니다. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>또한 단일 명령을 사용하여 모델 등록, 매니페스트 및 모델 생성을 수행할 수 있습니다. 자세한 내용을 보려면 service create 명령과 함께 -h를 사용하세요.

대신, 다음과 같이 하나의 단계로 모델을 등록하고, 매니페스트를 만들고, 이미지를 만드는 단일 명령이 있습니다(단, 웹 서비스 만들기 및 배포는 아직 해당하지 않음).

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>명령 매개 변수에 대한 자세한 내용을 보려면 명령 끝에 -h를 입력하세요(예: az ml image create -h).


### <a name="7-create-and-deploy-the-web-service"></a>7. 웹 서비스 만들기 및 배포
다음 명령을 사용하여 서비스를 배포합니다.

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>단일 명령을 사용하여 이전 4단계를 모두 수행할 수도 있습니다. 자세한 내용을 보려면 service create 명령과 함께 -h를 사용하세요.

대신, 다음과 같이 하나의 단계로 모델을 등록하고, 매니페스트를 만들고, 이미지를 만들 뿐 아니라 웹 서비스도 만들고 배포하는 단일 명령이 있습니다.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. 서비스 테스트
다음 명령을 사용하여 서비스를 호출하는 방법에 대한 정보를 가져옵니다.

```
az ml service usage realtime -i <service id>
```

명령 프롬프트에서 run 함수를 사용하여 서비스를 호출합니다.

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

다음 예제에서는 샘플 아이리스 웹 서비스를 호출합니다.

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>다음 단계
이제 웹 서비스를 로컬로 실행하여 테스트했으므로 대규모로 사용할 클러스터에 웹 서비스를 배포할 수 있습니다. 웹 서비스 배포를 위한 클러스터 설정에 대한 자세한 내용은 [모델 관리 구성](deployment-setup-configuration.md)을 참조하세요. 
