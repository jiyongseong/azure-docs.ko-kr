---
title: 에너지 수요 시계열 예측 | Microsoft Docs
description: Azure Machine Learning Workbench에서 기계 학습을 적용하여 에너지 수요 시계열을 예측하는 방법입니다.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: b40ebba3c85760d947861b4215dab6e16a86002b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="energy-demand-time-series-forecasting"></a>에너지 수요 시계열 예측


시계열 예측은 시간 순서에 따른 관찰 시퀀스에서 미래 값을 예측하는 작업입니다. 이는 공통된 문제이며 많은 산업 분야에 적용됩니다. 예를 들어 유통업체는 향후 제품 판매를 예측하여 수요를 충족시키기 위해 공급망을 효과적으로 구성할 수 있어야 합니다. 마찬가지로 패키지 배달 회사는 서비스 수요를 예측하여 인력 요구 사항 및 배달 경로를 미리 계획할 수 있어야 합니다. 이를 부정확하게 예측할 경우 상당한 재정적 위험이 따를 수 있습니다. 따라서 예측은 비즈니스에 중요한 작업입니다.

이 샘플은 기계 학습 기술을 적용하여 시계열 예측을 수행할 수 있는 방법을 보여 줍니다. 다음과 같은 모델링 프로세스의 모든 단계를 안내합니다.
- 데이터 정리 및 서식 지정을 위한 데이터 준비
- 원시 시계열 데이터에서 기계 학습 모델의 기능 생성
- 다양한 기계 학습 모델 학습
- 보유한 테스트 데이터 집합에서 성능을 비교하여 모델 평가
- 최상의 모델을 운영하여 웹 서비스를 통해 사용 가능하게 함으로써 수요 예측 생성

Azure Machine Learning Workbench는 모든 단계에서 모델링 프로세스를 지원합니다. 
- 이 샘플은 Workbench에서 직접 사용할 수 있는 Jupyter Notebook 환경에서 Python 코드를 쉽게 개발하는 방법을 보여 줍니다. markdown 주석과 차트를 사용하여 다른 사용자에게 모델 개발 프로세스를 보다 명확하게 설명할 수 있습니다. 이 Notebook은 Workbench에서 직접 보고 편집하고 실행할 수 있습니다.
- 학습된 모델을 유지하고 Blob Storage에 업로드할 수 있습니다. 이렇게 하면 데이터 과학자가 학습된 모델 개체를 추적하고 필요할 때 유지 및 검색할 수 있습니다.
- 데이터 과학자가 모델 성능 점수를 기록할 수 있도록 Python 스크립트를 실행하는 동안 메트릭을 기록할 수 있습니다.
- 워크벤치는 데이터 과학자가 모델 성능 메트릭을 쉽게 비교할 수 있도록 기록된 메트릭의 사용자 지정 테이블을 생성합니다. 차트가 자동으로 표시되므로 가장 성능이 좋은 모델을 쉽게 식별할 수 있습니다.
- 마지막으로 이 샘플은 학습된 모델을 실시간 웹 서비스에 배포하여 운영하는 방법을 보여 줍니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결
이 실제 시나리오에 대한 공용 GitHub 리포지토리에는 이 예제에 필요한 코드 샘플을 포함한 모든 자료가 들어 있습니다.

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>사용 사례 개요

이 시나리오는 에너지 그리드에서 미래의 부하를 예측하는 것이 목표인 에너지 수요 예측에 중점을 둡니다. 에너지 관련 기업에서는 비즈니스 운영자가 그리드에서 소비되는 에너지와 공급되는 에너지 사이의 균형을 잘 유지해야 하는 것이 중요합니다. 그리드에 공급되는 전력이 너무 많으면 에너지가 낭비되거나 기술적 오류가 발생할 수 있습니다. 그러나 전력이 너무 적게 공급되면 정전으로 이어져 고객에게 전력이 제공되지 않습니다. 일반적으로 그리드 운영자는 그리드에 대한 에너지 공급을 관리하고 부하를 균형 있게 유지하기 위한 단기적 결정을 내릴 수 있습니다. 따라서 에너지 수요에 대한 정확한 단기 예측은 운영자가 확신을 갖고 이러한 결정을 내리는 데 필수적입니다.

이 시나리오는 기계 학습 에너지 수요 예측 솔루션의 구성을 자세히 설명합니다. 이 솔루션은 뉴욕주의 전력 그리드를 운영하는 [NYISO(New York Independent System Operator)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument)의 공개 데이터 집합에 대해 학습합니다. 이 데이터 집합에는 5년 간 뉴욕시의 시간당 전력 수요 데이터가 포함되어 있습니다. 같은 기간 동안 뉴욕시의 시간별 날씨 조건이 포함된 추가 데이터 집합은 [darksky.net](https://darksky.net)에서 가져왔습니다.

## <a name="prerequisites"></a>필수 조건

- [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
- [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)의 설치 복사본으로 프로그램을 설치하고 작업 공간을 만듭니다.
- 이 샘플에서는 [Docker 엔진](https://www.docker.com/)이 로컬에 설치된 Windows 10에서 Azure ML Workbench를 실행 중이라고 가정합니다. macOS를 사용하는 경우 지침은 거의 같습니다.
- 이 [가이드](./model-management-configuration.md)에 설명된 대로 Azure Machine Learning 운영화가 로컬 배포 환경 설정으로 설치되고 모델 관리 계정이 생성되어야 합니다.
- 이 샘플에서는 Pandas 설치를 버전 0.20.3 이상으로 업데이트하고 matplotlib를 설치해야 합니다. Workbench의 *파일* 메뉴에서 *명령 프롬프트 열기*를 클릭하고 다음 명령을 실행하여 이러한 종속 항목을 설치합니다.

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 "에너지 수요 시계열 예측"을 입력하고 템플릿을 선택합니다.
5.  **만들기**


## <a name="data-description"></a>데이터 설명

두 개의 데이터 집합이 이 샘플과 함께 제공되고 `1-data-preparation.ipynb` 노트북: `nyc_demand.csv` 및 `nyc_weather.csv`를 사용하여 다운로드됩니다.

**nyc_demand.csv**에는 2012-2017년 동안 뉴욕시의 시간당 에너지 수요 값이 포함되어 있습니다. 데이터의 구조는 다음과 같이 간단합니다.

| timeStamp | demand |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

수요 값은 메가와트-시간(MWh)입니다. 다음은 2017년 7월 중 7일 간의 에너지 수요 차트입니다.

![에너지 수요](./media/scenario-time-series-forecasting/energy_demand.png  "에너지 수요")

**nyc_weather.csv**에는 2012-2017년 동안 뉴욕시의 시간별 날씨 값이 포함되어 있습니다.

| timeStamp | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip*는 강수량의 백분율 측정입니다. *temp*(온도) 값은 모든 값의 간격이 [0, 100]이 되도록 재조정되었습니다.

## <a name="scenario-structure"></a>시나리오 구조

Azure Machine Learning Workbench에서 이 프로젝트를 처음 열고 왼쪽의 *파일* 창으로 이동합니다. 이 샘플에는 다음과 같은 코드 파일이 제공됩니다.
- `1-data-preparation.ipynb` - 이 Jupyter Notebook은 데이터를 다운로드하고 처리하여 모델링을 준비합니다. 이것이 첫 번째로 실행할 Notebook입니다.
- `2-linear-regression.ipynb` - 이 Notebook은 학습 데이터에 대한 선형 회귀 모델을 학습합니다.
- `3-ridge.ipynb` - 볼록 회귀 모델을 학습합니다.
- `4-ridge-poly2.ipynb` - 2도의 다항식 기능에 대한 볼록 회귀 모델을 학습합니다.
- `5-mlp.ipynb` - 다중 계층 퍼셉트론 신경망을 학습합니다.
- `6-dtree.ipynb` - 의사 결정 트리 모델을 학습합니다.
- `7-gbm.ipynb` - 그라데이션 승격 컴퓨터 모델을 학습합니다.
- `8-evaluate-model.py` - 이 스크립트는 학습된 모델을 로드하고 보유한 테스트 데이터 집합에 대한 예측을 수행하는 데 사용합니다. 다른 모델의 성능을 비교할 수 있도록 모델 평가 메트릭을 생성합니다.
- `9-forecast-output-exploration.ipynb` - 이 Notebook은 기계 학습 모델에 의해 생성된 예측의 시각화를 생성합니다.
- `10-deploy-model.ipynb` - 이 Notebook은 학습된 예측 모델을 실시간 웹 서비스에서 운영할 수 있는 방법을 보여 줍니다.
- `evaluate-all-models.py` - 이 스크립트는 모든 학습된 모델을 평가합니다. 학습된 각 모델에 대해 개별적으로 `8-evaluate-model.py`을 실행하는 대안을 제공합니다.

### <a name="1-data-preparation-and-cleaning"></a>1. 데이터 준비 및 정리

샘플을 실행하려면 먼저 `1-data-preparation.ipynb`를 클릭하여 Notebook을 미리 보기 모드로 엽니다. ***Notebook 서버 시작***을 클릭하여 컴퓨터에서 Jupyter Notebook 서버 및 Python 커널을 시작합니다. Workbench 내에서 Notebook을 실행하거나 [http://localhost:8888](http://localhost:8888)로 이동하여 브라우저를 사용할 수 있습니다. 커널을 *PROJECT_NAME local*로 변경해야 합니다. Notebook에서 *커널* 메뉴의 *커널 변경*을 사용하여 이 작업을 수행할 수 있습니다. 개별 Notebook 셀에서 코드를 실행하려면 ***shift+Enter***를 누르고 *셀* 메뉴에서 *모두 실행*을 클릭하여 전체 Notebook을 실행합니다.

Notebook은 Azure에서 호스트되는 Blob 저장소 컨테이너에서 데이터를 먼저 다운로드합니다. 데이터는 컴퓨터의 `AZUREML_NATIVE_SHARE_DIRECTORY`에 저장됩니다. 이 위치는 Workbench에서 실행하는 모든 Notebook 또는 스크립트에서 액세스할 수 있으므로 데이터 및 학습된 모델을 저장할 수 있는 좋은 장소입니다.

데이터가 다운로드되면 Notebook은 시계열의 간격을 채우고 누락된 값을 보간하여 채워 데이터를 정리합니다. 이러한 방식으로 시계열 데이터를 정리하면 모델 학습에 사용할 수 있는 데이터의 양이 최대화됩니다.

정리된 원시 데이터에서 여러 모델 기능이 만들어집니다. 이러한 기능은 두 가지 그룹으로 분류할 수 있습니다.

- **시간 기반 기능**은 *timestamp* 변수(예: *month*, *dayofweek* 및 *hour*)에서 파생됩니다.
- **지연 기능**은 실제 수요 또는 온도 값의 시간 이동 값입니다. 이러한 기능은 모델에서 연속 기간 사이의 조건부 종속성을 캡처하는 것을 목표로 합니다.

결과 기능 데이터 집합은 예측 모델을 개발할 때 사용할 수 있습니다.

### <a name="2-model-development"></a>2. 모델 개발

첫 번째 모델링 접근 방식은 간단한 선형 회귀 모델일 수 있습니다. `2-linear-regression.ipynb` Notebook에 미래 에너지 수요에 대한 선형 회귀 예측 모델을 학습하는 방법이 나와 있습니다. 특히 이 모델은 현재 기간(*t*)보다 1시간(*t+1*) 앞당겨 에너지 수요를 예측하도록 학습됩니다. 그러나 테스트 시간에 재귀적으로 이 '1단계(one-step)' 모델을 적용하여 미래 기간 *t+n*에 대한 예측을 생성할 수 있습니다.

모델을 학습하기 위해 다음 세 가지 단계가 포함된 예측 파이프라인이 만들어집니다.

- **데이터 변환**: 입력 데이터에 필요한 형식은 기계 학습 알고리즘에 따라 다를 수 있습니다. 이 경우 선형 회귀 모델에서는 범주형 변수가 원 핫(one-hot) 방식으로 인코딩되어야 합니다.
- **교차 유효성 검사 루틴**: 기계 학습 모델에는 실험을 통해 튜닝해야 하는 하나 이상의 하이퍼 매개 변수가 종종 있습니다. 교차 유효성 검사를 사용하여 최적의 매개 변수 값 집합을 찾을 수 있습니다. 모델은 반복적으로 훈련되고 데이터 집합의 여러 접기에서 평가됩니다. 가장 좋은 매개 변수는 교차 유효성 검사 접기에서 평균화할 때 최상의 모델 성능을 달성하는 매개 변수입니다. 이 프로세스는 `2-linear-regression.ipynb`에 자세히 설명되어 있습니다.
- **최종 모델 학습**: 모델은 최상의 하이퍼 매개 변수 집합을 사용하여 전체 데이터 집합에 대해 학습됩니다.

Notebook에는 2-7의 번호가 매겨진 일련의 6가지 모델이 포함되어 있습니다. 각 Notebook은 다른 모델을 학습하고 `AZUREML_NATIVE_SHARE_DIRECTORY` 위치에 저장합니다. 이 시나리오를 위해 개발된 모든 모델을 학습하면 다음 섹션에서 설명하는 대로 평가하고 비교할 수 있습니다.

### <a name="3-model-evaluation-and-comparison"></a>3. 모델 평가 및 비교

학습된 모델을 사용하여 미래의 기간에 대해 예측할 수 있습니다. 보유한 테스트 데이터 집합에서 이 모델을 평가하는 것이 가장 좋습니다. 확인되지 않은 동일한 데이터 집합에서 여러 모델을 평가함으로써 성능을 공정하게 비교하고 최상의 모델을 식별할 수 있습니다. 이 시나리오에서는 학습된 모델을 재귀적으로 적용하여 미래의 여러 시간 단계를 예측합니다. 특히, 현재 시간(*t*)보다 최대 6시간 이후의 시간(*t+6*)에 대한 예측을 생성합니다. 이러한 예측은 각 기간에 대해 관찰된 실제 수요와 비교하여 평가됩니다.

모델을 평가하려면 Workbench의 *파일* 창에서 `8-evaluate-model.py` 스크립트를 엽니다. *구성 실행*이 **local**로 설정되어 있는지 확인한 다음 *인수* 필드에 모델 이름을 입력합니다. 모델 이름은 모델을 학습한 Notebook의 시작 부분에 설정된 *model_name* 변수와 정확히 일치해야 합니다. 예를 들어 "linear_regression"을 입력하면 학습된 선형 회귀 모델을 평가할 수 있습니다. 또는 일단 모든 모델이 학습되면 `evaluate-all-models.py`를 실행하여 하나의 명령으로 모두 평가할 수 있습니다. 이 스크립트를 실행하려면 Workbench에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
python evaluate-all-models.py
```
`8-evaluate-model.py` 스크립트에서는 다음 작업을 수행합니다.

- 디스크에서 학습된 모델 파이프라인 로드
- 테스트 데이터 집합에 대한 예측
- 모델 성능 메트릭 계산 및 기록
- 향후 검사 및 분석을 위해 테스트 데이터 집합 예측을 `AZUREML_NATIVE_SHARE_DIRECTORY`에 저장합니다.
- 학습된 모델 파이프라인을 *outputs* 폴더에 저장합니다.

> [!Note]
> 모델을 *outputs* 폴더에 저장하면 모델 개체가 실험 계정과 연결된 Azure Blob Storage 계정에 자동으로 복사됩니다. 즉, 컴퓨터 또는 계산 컨텍스트를 변경하더라도 저장된 모델 개체를 Blob에서 항상 검색할 수 있습니다.

*실행 기록* 창으로 이동하여 `8-evaluate-model.py`을 클릭합니다. 여러 모델 성능 메트릭을 표시하는 차트가 표시됩니다.

- **ME**: 예측의 평균 오차. 이는 예측의 평균 편차로 해석할 수 있습니다.
- **MPE**: [평균 백분율 오차](https://en.wikipedia.org/wiki/Mean_percentage_error)(실제 수요의 백분율로 표현된 ME)
- **MSE**: [제곱 평균 오차](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: 제곱 평균 제곱근 오차(MSE의 제곱근)
- **MAPE**: [절대 평균 백분율 오차](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [대칭 절대 평균 백분율 오차](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: 예측이 마지막으로 알려진 수요 값과 동일한 기준 예측의 MAPE
- **MdRAE**: 중앙값 상대 절대 오차. 예측 오차와 기준 예측 오차의 중앙값 비율입니다. 값이 1보다 작으면 예측이 기준보다 좋음을 나타냅니다.

위의 모든 메트릭은 *t+1* 예측을 참조합니다. 위의 메트릭 외에도 *_horizon* 접미사가 있는 일련의 메트릭 집합이 표시됩니다. 이는 기간 *t+1*부터 기간 *t+6*까지의 예측 범위에서 모든 기간에 대해 평균 계산된 메트릭입니다.

차트 영역에 메트릭이 표시되지 않으면 오른쪽 상단의 톱니바퀴 기호를 클릭합니다. 관심 있는 모델 성능 메트릭을 확인합니다. 메트릭은 차트 아래의 표에도 나타납니다. 이 표에서도 톱니바퀴 기호를 클릭하여 사용자 지정할 수 있습니다. 성능 메트릭별로 표를 정렬하여 최상의 모델을 식별합니다. 기간 *t+1*부터 *t+6*까지의 예측 성능 변화를 보려면 표에서 모델에 대한 항목을 클릭합니다. 예측 기간 동안의 MAPE, MPE 및 MdRAE 메트릭을 표시하는 차트가 *시각화*에 표시됩니다.

예측 모델을 평가할 때 출력 예측을 시각화하면 매우 유용할 수 있습니다. 이를 통해 데이터 과학자는 생성된 예측이 현실적인지 여부를 판단할 수 있습니다. 또한 예를 들어 특정 기간에 예측이 제대로 수행되지 않는 경우에도 예측을 통해 문제를 식별하는 데 도움이 될 수 있습니다. `9-forecast-output-exploration.ipynb` Notebook은 테스트 데이터 집합에 대해 생성된 예측의 시각화를 생성합니다.

### <a name="4-deployment"></a>4. 배포

최상의 모델은 실시간 웹 서비스로 배포하여 운영할 수 있습니다. 그런 다음 이 웹 서비스를 호출하여 새 데이터를 사용할 수 있게 되면 수요 예측을 생성할 수 있습니다. 이 시나리오에서는 이후 시간의 에너지 수요를 예측하기 위해 매시간 새로운 예측을 생성해야 합니다. 이 작업을 수행하기 위해 웹 서비스를 배포할 수 있습니다. 이 웹 서비스는 지정된 기간 동안 기능의 배열을 입력으로 사용하고 예측된 수요를 출력으로 반환합니다.

이 샘플에서는 웹 서비스가 Windows 10 컴퓨터에 배포됩니다. 운영화 CLI에 대한 이 [시작 가이드](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md)에 설명된 로컬 배포를 위한 필수 조건 단계를 완료했는지 확인합니다. 로컬 환경 및 모델 관리 계정을 설정했으면 `10-deploy-model.ipynb` Notebook을 실행하여 웹 서비스를 배포합니다.

## <a name="conclusion"></a>결론

이 샘플은 에너지 수요를 예측하기 위해 종단 간 시계열 예측 솔루션을 빌드하는 방법을 보여 줍니다. 이 샘플에서 살펴본 많은 원칙을 다른 예측 시나리오 및 산업으로 확장할 수 있습니다.

이 시나리오는 Azure Machine Learning Workbench를 사용하여 데이터 과학자가 Jupyter Notebook 환경 및 메트릭 기록 기능과 같은 유용한 기능을 갖춘 실제 솔루션을 개발하도록 지원하는 방법을 보여 줍니다. 이 샘플은 또한 Azure Machine Learning 운영화 CLI를 사용하여 모델을 운영 및 배포하는 방법에 대해 안내합니다. 웹 서비스 API를 배포하면 개발자나 데이터 엔지니어가 예측 모델을 다양한 데이터 파이프라인에 통합할 수 있습니다.
