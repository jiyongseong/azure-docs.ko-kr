---
title: Azure Machine Learning Workbench의 실행 기록 및 모델 메트릭을 사용하는 방법 | Microsoft Docs
description: Azure Machine Learning Workbench의 실행 기록 및 모델 메트릭 기능 사용에 대한 가이드
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dddf6af506269c7f22cea69e39b55effa2f14d63
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench의 실행 기록 및 모델 메트릭을 사용하는 방법

Azure Machine Learning Workbench는 **실행 기록** 및 **모델 메트릭** 기능을 통해 데이터 과학 실험을 지원합니다.
**실행 기록**은 Machine Learning 실험의 출력을 추적하는 방법을 제공하며, 그 후 결과를 필터링 및 비교할 수 있습니다.
**모델 메트릭**은 스크립트의 어느 지점에서나 기록이 가능하며, 데이터 과학 실험에서 가장 중요한 값을 추적할 수 있습니다.
이 문서에서는 이러한 기능을 효과적으로 사용하여 데이터 과학 실험의 속도와 품질을 높이는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음을 수행해야 합니다.
* [Azure Machine Learning 만들기 및 설치](../service/quickstart-installation.md)
- [프로젝트 만들기](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Azure ML 로깅 API 개요
[Azure ML 로깅 API](reference-logging-api.md)는 Python의 **azureml.logging** 모듈을 통해 제공됩니다(Azure ML Workbench와 함께 설치됨). 이 모듈을 가져온 후 **get_azureml_logger** 메서드를 사용하여 **로거** 개체를 인스턴스화할 수 있습니다.
그런 다음 로거의 **log** 메서드를 사용하여 Python 스크립트에서 생성한 키/값 쌍을 저장할 수 있습니다.
현재 표시된 대로 스칼라 및 목록 형식의 모델 메트릭 로깅이 지원됩니다.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Azure ML Workbench 프로젝트 내에서 간편하게 로거를 사용할 수 있으며, 이 문서에서는 그 방법을 보여줍니다.

## <a name="create-a-project-in-azure-ml-workbench"></a>Azure ML Workbench에서 프로젝트 만들기
아직 프로젝트가 없는 경우 표시된 것처럼 [프로젝트 대시보드](../service/quickstart-installation.md)의 **빠른 시작 만들기 및 설치**에서 프로젝트를 만들고, **iris_sklearn.py** 스크립트를 열 수 있습니다.

![파일 탭에서 스크립트에 액세스](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Azure ML에서 모델 메트릭 로깅의 예상되는 구현에 대한 지침으로 이 스크립트를 사용할 수 있습니다.

## <a name="parameterize-and-log-model-metrics-from-script"></a>스크립트에서 로그 모델 메트릭 매개 변수화 및 로깅
**iris_sklearn.py** 스크립트에서, Python에서 로거를 가져와서 작성하는 예상 패턴을 다음 코드 줄로 줄일 수 있습니다.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

만든 후에는 아무 이름/값 쌍을 사용하여 **log** 메서드를 호출할 수 있습니다.

개발이 완료되면 명령줄을 통해 값을 전달할 수 있도록 스크립트를 매개 변수화하면 유용할 때가 많습니다.
아래 샘플은 표준 Python 라이브러리를 사용하여 명령줄 매개 변수(있는 경우)를 수락하는 방법을 보여줍니다.
이 스크립트는 과잉 맞춤 없이 *정확도*를 높이기 위해 분류 모델 맞춤에 사용되는 단일 매개 변수를 정규화 속도(*reg*)에 사용합니다.
최적의 결과가 나온 모델을 쉽게 식별할 수 있도록 그 후 이러한 값은 *정규화 속도* 및 *정확도*로 로깅됩니다.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

스크립트에서 이러한 단계를 수행하면 **실행 기록**을 가장 적절하게 사용할 수 있습니다.

## <a name="launch-runs-from-project-dashboard"></a>프로젝트 대시보드에서 실행 시작
**프로젝트 대시보드**로 돌아가서 **iris_sklearn.py** 스크립트를 선택하고 **인수** 편집 상자에 **정규화 속도** 매개 변수를 입력하여 **추적되는 실행**을 시작할 수 있습니다.

![매개 변수를 입력하고 실행 시작](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

추적되는 실행을 시작해도 Azure ML Workbench가 차단되지 않으므로 여러 개를 병렬로 시작할 수 있습니다.
각 추적되는 실행의 상태는 그림과 같이 **작업 패널**에 표시됩니다.

![작업 패널에서 실행 추적](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

이를 통해 각 작업을 직렬로 실행할 필요 없이 최적의 리소스 활용이 가능합니다.

## <a name="view-results-in-run-history"></a>실행 기록에서 결과 보기
추적되는 실행의 진행률 및 결과는 Azure ML Workbench의 **실행 기록**에서 분석에 사용할 수 있습니다.
**실행 기록**은 다음과 같은 세 가지 고유의 보기를 제공합니다.
- 대시보드
- 세부 정보
- 비교

**대시보드** 보기에는 특정 스크립트의 모든 실행에 대한 데이터가 표시되며, 그래픽 형식과 테이블 형식으로 렌더링됩니다.
**세부 정보** 보기에는 기록된 메트릭과 출력 파일(예: 렌더링된 플롯)을 포함하여 특정 스크립트의 특정 실행에서 생성된 모든 데이터가 표시됩니다. **비교** 보기에는 2개 또는 3개 실행의 결과를 나란히 표시할 수 있으며, 기록된 메트릭과 출력 파일도 함께 표시할 수 있습니다.

8개의 추적되는 **iris_sklearn.py** 실행에서, **정규화 속도** 매개 변수 및 **정확도** 결과에 대한 값은 실행 기록 보기를 사용하는 방법을 보여주기 위해 기록되었습니다.

### <a name="run-history-dashboard"></a>실행 기록 대시보드
총 8개 실행의 결과는 **실행 기록 대시보드**에 표시됩니다.
**iris_sklearn.py**가 *정규화 속도* 및 *정확도*를 기록하므로 **실행 기록 대시보드**는 기본적으로 이러한 값에 대한 차트를 표시합니다.

![실행 기록 대시보드](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

기록된 값이 그리드에도 표시되도록 **실행 기록 대시보드**를 사용자 지정할 수 있습니다.  **사용자 지정** 아이콘을 클릭하면 그림과 같이 **목록 보기 사용자 지정** 대화 상자가 표시됩니다.

![실행 기록 대시보드 그리드 사용자 지정](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

추적되는 실행 중에 기록된 모든 값은 표시가 가능하며, **정규화 속도** 및 **정확도**를 선택하면 값이 그리드에 추가됩니다.

![기록된 값을 사용자 지정된 그리드에 표시](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

차트의 점 위로 마우스 커서를 이동하여 흥미로운 실행을 간편하게 찾을 수 있습니다.  이 예에서는 실행 7이 높은 정확도와 낮은 기간을 보였습니다.

![흥미로운 실행 찾기](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

아무 차트에서 실행 7과 연결된 점을 클릭하거나 그리드에서 실행 7 링크를 클릭하면 **실행 기록 세부 정보**가 표시됩니다.

### <a name="run-history-details"></a>실행 기록 세부 정보
이 보기에는 실행 7에 의해 생성된 아티팩트와 함께 실행 7의 전체 결과가 표시됩니다.

![실행 기록 세부 정보](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**실행 기록 세부 정보**는 작성된 모든 파일을 **./outputs** 폴더로 **다운로드**하는 기능도 제공합니다. 이러한 파일은 Azure ML Workbench의 실행 기록용 클라우드 저장소에 백업되며, 여기에 대한 내용은 다른 문서에서 다루겠습니다.

마지막으로, **실행 기록 세부 정보**는 프로젝트를 이 실행 당시의 상태로 복원하는 수단을 제공합니다.
**복원** 단추를 클릭하면 그림과 같이 확인 대화 상자가 표시됩니다.

![실행 복원 확인](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

복원을 확인하면 파일을 덮어쓰거나 파일이 제거될 수 있으므로 이 기능을 신중하게 사용해야 합니다.

### <a name="run-history-comparison"></a>실행 기록 비교
**실행 기록 대시보드**에서 2개 또는 3개의 실행을 선택하고 **비교**를 클릭하면 **실행 기록 비교** 보기로 이동됩니다.
또는 **비교**를 클릭하고 **실행 기록 세부 정보** 보기 내에서 실행을 선택해도 **실행 기록 비교** 보기로 이동됩니다.
어떤 방법을 선택하든, **실행 기록 비교** 보기에 2개 또는 3개 실행의 기록된 결과와 아티팩트가 나란히 표시됩니다.

![실행 기록 비교](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

이 보기는 플롯 비교에 특히 유용하지만, 일반적으로 실행의 모든 속성을 여기서 비교할 수 있습니다.

### <a name="command-line-interface"></a>명령줄 인터페이스
Azure Machine Learning Workbench는 **명령줄 인터페이스**를 통해서도 실행 기록에 액세스할 수 있습니다.
**명령줄 인터페이스**에 액세스하려면 그림과 같이 **명령 프롬프트 열기** 메뉴를 클릭합니다.

![명령 프롬프트 열기](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

실행 기록에 사용할 수 있는 명령은 `az ml history`를 통해 액세스되며, `-h` 플래그를 추가하여 온라인 도움말을 사용할 수 있습니다.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
이러한 명령은 동일한 기능을 제공하며 **실행 기록 보기**에 표시된 것과 동일한 데이터를 반환합니다.
예를 들어 마지막 실행의 결과를 JSON 개체로 표시할 수 있습니다.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
또한 모든 실행 목록은 테이블 형식으로 표시할 수 있습니다.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
**명령줄 인터페이스**는 Azure Machine Learning Workbench의 강력한 기능을 사용할 수 있는 대체 경로입니다.

## <a name="next-steps"></a>다음 단계
이러한 기능은 데이터 과학 실험 과정을 지원하기 위해 제공됩니다.
여러분에게 많은 도움이 되기를 바라며, 기능에 대한 의견을 보내주시기 바랍니다.
현재는 초기 구현 단계이며 앞으로 수많은 기능 향상이 계획되어 있습니다.
Azure Machine Learning Workbench에 지속적으로 기능을 제공하도록 하겠습니다. 
