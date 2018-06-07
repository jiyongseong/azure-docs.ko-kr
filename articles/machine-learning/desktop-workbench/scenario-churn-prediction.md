---
title: Azure Machine Learning을 사용한 고객 변동 분석 | Microsoft Docs
description: Azure ML Workbench를 사용한 변동 분석을 수행하는 방법입니다.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 501735e1cf32359a34c01ad1c7990609a2bf4058
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Azure Machine Learning을 사용한 고객 변동 분석

평균적으로 기존 고객 유지는 새로운 고객을 유치하는 데 드는 비용보다 5배 더 저렴합니다. 결과적으로, 마케팅 임원들은 종종 고객 변동의 가능성을 예측하기 위해 애쓰고 변동률을 최소화하기 위해 필요한 작업을 찾습니다.

이 솔루션의 목적은 Azure Machine Learning Workbench를 사용하여 예측 변동 분석을 보여 주기 위한 것입니다. 이 솔루션은 소매 업체를 위해 변동 예측 데이터 파이프라인을 개발하기에 사용하기 쉬운 템플릿을 제공합니다. 템플릿은 변동에 대한 서로 다른 데이터 집합 및 다른 정의에 사용될 수 있습니다. 실습 예제의 목표는 다음과 같습니다.

1. Azure Machine Learning Workbench의 데이터 준비 도구를 이해하여 변동 분석을 위한 고객 관계 데이터를 정리 및 수집합니다.

2. 변환 기능을 수행하여 노이즈가 많은 다른 유형의 데이터를 처리합니다.

3. 타사 라이브러리(예: `scikit-learn` 및 `azureml`)를 통합하여 변동을 예측하기 위한 Bayesian 및 트리 기반 분류자를 개발합니다.

4. 배포합니다.

## <a name="link-of-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리 링크
다음은 모든 코드가 호스팅되는 공용 GitHub 리포지토리에 대한 링크입니다.

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>사용 사례 개요
회사에는 고객 변동을 관리하기 위한 효과적인 전략이 필요합니다. 고객 변동에는 고객의 서비스 사용 중지, 경쟁 업체 서비스로 전환, 서비스의 하위 계층 환경으로 전환 및 서비스 계약 감소가 포함됩니다.

이 사용 사례에서는 서비스를 개선하고 고객 유지에 도움이 되는 사용자 지정 홍보 캠페인을 만들기 위해 프랑스 통신 회사인 Orange의 데이터를 살펴보고 단기적으로 변동 가능성이 있는 고객을 식별합니다.

통신 회사는 경쟁 시장에 직면해 있습니다. 많은 사업자들은 변동으로 인해 우편료 선불 고객으로부터의 수익을 잃습니다. 따라서 고객 변동을 정확하게 식별하는 기능은 큰 경쟁 우위가 될 수 있습니다.

통신 고객 변동에 기여하는 요소 중 일부는 다음과 같습니다.

* 인지된 잦은 서비스 중단
* 온라인/소매점의 형편 없는 고객 서비스 환경
* 다른 경쟁 사업자의 제품(더 나은 가족 요금제, 데이터 요금제 등)

이 솔루션에서는 통신 회사를 위한 예측 고객 변동 모델의 구체적인 빌드 예제를 사용합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)

* 프로그램을 설치하고 작업 영역을 만들기 위한, [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 설치된 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)의 복사본

* 운영화의 경우 로컬로 설치 및 실행되는 Docker 엔진이 있는 것이 가장 좋습니다. 그렇지 않은 경우 클러스터 옵션을 사용할 수는 있지만, ACS(Azure Container Service)를 실행하는 데 비용이 많이 들 수 있습니다.

* 이 솔루션에서는 Docker 엔진이 로컬에 설치된 Windows 10에서 Azure Machine Learning Workbench를 실행 중이라고 가정합니다. macOS를 사용하는 경우 지침은 거의 같습니다.

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 “고객 변동 예측”을 입력하고 템플릿을 선택합니다.
5.  **만들기**

## <a name="data-description"></a>데이터 설명

솔루션에 사용된 데이터 집합은 SIDKDD 2009 경쟁에서 가져온 것입니다. `CATelcoCustomerChurnTrainingSample.csv`라고 하며, [`data`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) 폴더에 위치해 있습니다. 데이터 집합은 프랑스 통신 회사인 Orange의 노이즈가 많은 다른 유형의 데이터(숫자/범주 변수)로 구성되어 있으며 익명으로 처리됩니다.

변수는 고객 인구 통계 정보, 호출 통계(예: 평균 호출 기간, 호출 오류 비율 등), 계약 정보, 불만 통계를 캡처합니다. 변동 변수는 이진값(0 - 변동되지 않음, 1 - 변동됨)입니다.

## <a name="scenario-structure"></a>시나리오 구조

폴더 구조는 다음과 같이 정렬됩니다.

__data__: 솔루션에 사용된 데이터 집합을 포함  

__docs__: 모든 실습 교육을 포함

솔루션을 수행하는 실습 교육의 순서는 다음과 같습니다.
1. 데이터 준비: 데이터 폴더의 데이터 준비와 관련된 주요 파일은 `CATelcoCustomerChurnTrainingSample.csv`입니다.
2. 모델링 및 평가: 루트 폴더의 모델링 및 평가와 관련된 주요 파일은 `CATelcoCustomerChurnModeling.py`입니다.
3. .dprep 없이 모델링 및 평가: 루트 폴더의 이 작업에 대한 주요 파일은 `CATelcoCustomerChurnModelingWithoutDprep.py`입니다.
4. 운영화: 배포에 대한 주요 파일은 모델(`model.pkl`) 및 `churn_schema_gen.py`입니다.

| 순서| 파일 이름 | 관련 파일 |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

위에 설명한 순차적 방식으로 교육을 수행합니다.

## <a name="conclusion"></a>결론
이 실습 시나리오는 Azure Machine Learning Workbench를 사용하여 변동 예측을 수행하는 방법을 설명했습니다. 먼저 데이터 정리를 수행하여 노이즈가 많은 다른 유형의 데이터를 처리하고, 데이터 준비 도구를 사용한 기능 엔지니어링이 이어졌습니다. 그 후, 오픈 소스 Machine Learning 도구를 사용하여 분류 모델을 만들고 평가한 다음, 로컬 Docker 컨테이너를 사용하여 프로덕션용으로 준비된 모델을 배포했습니다.
