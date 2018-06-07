---
title: 실제 시나리오에 대한 예측 유지 관리 | Microsoft Docs
description: PySpark를 사용한 실제 시나리오에 대한 예측 유지 관리
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 791c34785fa817fd68d0bec8111bf23e606c9b64
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>실제 시나리오에 대한 예측 유지 관리

예정되지 않은 장비 가동 중지 시간은 비즈니스에 큰 영향을 줄 수 있습니다. 현장 장비를 실행 상태로 유지하여 사용량 및 성능을 최대화하고 비용 및 예기치 않은 가동 중지 시간을 최소화하는 것이 중요합니다. 문제를 사전에 파악하므로 보다 비용 효율적인 방법으로 제한된 유지 관리 리소스를 할당하고 품질 및 공급망 프로세스를 개선할 수 있도록 합니다. 

이 시나리오에서는 상대적으로 [많은 양의 시뮬레이트된 데이터 집합](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)을 탐색하여 데이터 수집, 기능 엔지니어링, 모델 구축, 모델 운영화 및 배포 등의 예측 유지 관리 데이터 과학 프로젝트를 안내합니다. 전체 프로세스의 코드는 Azure Machine Learning Workbench 내의 PySpark를 사용하여 Jupyter 노트에 작성되었습니다. 최종 모델은 Azure Machine Learning 모델 관리를 사용하여 배포되어 장비 오류를 실시간으로 예측합니다.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence Gallery GitHub 리포지토리

PM 자습서를 위한 Cortana Intelligence Gallery는 문제를 보고하고 기여할 수 있는 공용 GitHub 리포지토리([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance))입니다.


## <a name="use-case-overview"></a>사용 사례 개요

많은 자산이 필요한 분야의 기업들이 직면한 주요 문제는 기계적 문제에 대한 지연과 관련된 상당한 비용입니다. 대부분의 기업은 이러한 문제가 발생하기 전에 예방하기 위해 이러한 문제가 언제 발생하는지 예측하는 데 관심이 있습니다. 목표는 가동 중지 시간을 줄여서 비용을 줄이고 잠재적으로 안전성을 높이는 것입니다. 

이 시나리오에서는 [예측 유지 관리 플레이 북](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)의 아이디어를 활용해 시뮬레이트된 데이터 집합에 대한 예측 모델을 빌드하는 방법을 보여 줍니다. 예제 데이터는 여러 예측 유지 관리 사용 사례에서 관측된 일반 요소에서 얻었습니다.

이 시뮬레이트된 데이터의 비즈니스 문제는 구성 요소 오류로 인한 문제를 예측하는 것입니다. 비즈니스 질문은 "*구성 요소의 실패로 인해 컴퓨터가 작동 중지될 확률은 얼마인가요*?"입니다. 이 문제는 다중 클래스 분류 문제(컴퓨터당 여러 구성 요소)로 형식이 지정됩니다. 기계 학습 알고리즘은 예측 모델을 만드는 사용됩니다. 모델은 기계에서 수집된 기록 데이터에 대해 학습됩니다. 이 시나리오에서 사용자는 Machine Learning Workbench 환경에서 모델을 구현하는 다양한 단계를 수행합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
* 설치된 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) 복사본. [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 프로그램을 설치하고 작업 영역을 만듭니다.
* Azure Machine Learning 운용화를 사용하려면 로컬 배포 환경과 [Azure Machine Learning 모델 관리 계정](model-management-overview.md)이 필요합니다.

이 예제는 Machine Learning Workbench 계산 컨텍스트에서 실행됩니다. 그러나 이 예제는 16GB 이상의 메모리를 사용하여 실행하는 것이 좋습니다. 이 시나리오는 원격 DS4_V2 표준 [Linux(Ubuntu)용 DSVM(데이터 과학 가상 머신)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)을 실행하는 Windows 10 컴퓨터에서 빌드되고 테스트되었습니다.

모델 운용화는 이 Azure Machine Learning CLI 버전 0.1.0a22를 사용하여 구현되었습니다.

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Machine Learning Workbench를 엽니다.
2.  **프로젝트** 페이지에서 **+**, **새 프로젝트**를 차례로 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 "예측 유지 관리"를 입력하고 **예측 유지 관리** 템플릿을 선택합니다.
5.  **만들기**를 선택합니다.

## <a name="prepare-the-notebook-server-computation-target"></a>노트북 서버 계산 대상 준비

로컬 컴퓨터에서 실행하려면 Machine Learning Workbench **파일** 메뉴에서 **명령 프롬프트 열기** 또는 **PowerShell CLI 열기**를 선택합니다. CLI 인터페이스에서는 `az` 명령을 사용하여 Azure 서비스에 액세스할 수 있습니다. 먼저 다음 명령을 사용하여 Azure 계정에 로그인합니다.

```
az login
``` 

이 명령은 https:\\aka.ms\devicelogin URL에서 사용할 인증 키를 제공합니다. CLI는 장치 로그인 작업이 반환되고 일부 연결 정보를 제공할 때까지 기다립니다. 그런 다음 로컬 [Docker](https://www.docker.com/get-docker)가 설치되어 있는 경우 다음 명령을 사용하여 로컬 계산 환경을 준비합니다.

```
az ml experiment prepare --target docker --run-configuration docker
```

메모리 및 디스크 요구 사항을 충족하기 위해 [Linux(Ubuntu)용 DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)에서 실행하는 것이 좋습니다. DSVM이 구성되면 다음 두 가지 명령으로 원격 Docker 환경을 준비합니다.

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

원격 Docker 컨테이너에 연결되면 다음 명령을 사용하여 DSVM docker 계산 환경을 준비합니다. 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Docker 계산 환경이 준비되면 Azure Machine Learning Workbench **노트** 탭에서 Jupyter 노트 서버를 열거나 다음 명령을 실행하여 브라우저 기반 서버를 시작합니다. 

```
az ml notebook start
```

예제 노트는 Code 디렉터리에 저장됩니다. 노트는 첫 번째(Code\1_data_ingestion.ipynb) 노트부터 순차적으로 실행되도록 설정됩니다. 각 노트를 열면 계산 커널을 선택하라는 메시지가 표시됩니다. 이전에 구성된 DSVM에서 실행할 [Project_Name]_Template [Connection_Name] 커널을 선택합니다.

## <a name="data-description"></a>데이터 설명

[시뮬레이트된 데이터](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)는 5개의 쉼표로 구분된 값(.csv) 파일로 구성됩니다. 다음 링크를 사용하여 데이터 집합에 대한 자세한 설명은 확인합니다.

* [컴퓨터](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 사용 기간 및 모델과 같이 각 컴퓨터를 구분하는 기능입니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): 오류 로그에는 기계가 여전히 작동 중일 때 발생하는 줄 바꿈하지 않는 오류가 포함됩니다. 이러한 오류는 향후 실패 이벤트를 예측할 수는 있지만 실패로 간주되지 않습니다. 오류의 날짜-시간 값은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [유지 관리](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): 유지 관리 로그에는 예정된 유지 관리 레코드와 예정되지 않은 유지 관리 레코드가 모두 들어 있습니다. 예약된 유지 관리는 구성 요소의 정기적 검사와 일치합니다. 예약되지 않은 유지 관리는 기계적 장애 또는 기타 성능 저하로 인해 발생할 수 있습니다. 유지 관리의 날짜-시간 값은 원격 분석 데이터가 시간당 속도로 수집된 이후 가장 가까운 시간으로 반올림됩니다.
* [원격 분석](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): 원격 분석 데이터는 각 컴퓨터 내의 여러 센서에서 측정되는 시계열 측정값으로 구성됩니다. 이 데이터는 1시간 간격으로 평균 센서 값으로 기록됩니다.
* [오류](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 오류는 유지 관리 로그 내의 구성 요소 교체에 해당합니다. 각 레코드에는 컴퓨터 ID, 구성 요소 유형 및 대체 날짜와 시간이 포함됩니다. 이러한 레코드는 모델이 예측하려고 하는 기계 학습 레이블을 작성하는 데 사용됩니다.

GitHub 리포지토리에서 원시 데이터 집합을 다운로드하고 이 분석을 위해 PySpark 데이터 집합을 만들려면 코드 섹션의 [데이터 수집](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 노트 시나리오를 참조하세요.

## <a name="scenario-structure"></a>시나리오 구조
이 시나리오의 콘텐츠는 [GitHub 리포지토리](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)에서 확인할 수 있습니다. 

[추가 정보](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) 파일은 데이터 준비, 모델 빌드, 프로덕션용으로 솔루션 배포 등에 이르는 워크플로를 간략하게 설명합니다. 워크플로의 각 단계는 리포지토리 내의 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 폴더에 있는 Jupyter 노트에 캡슐화됩니다.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): 이 노트는 5개의 입력 .csv 파일을 다운로드하고 예비 데이터 정리 및 시각화를 수행합니다. 노트는 각 데이터 집합을 PySpark 형식으로 변환하고, 기능 엔지니어링 노트에 사용하도록 결과를 Azure Blob 컨테이너에 저장합니다.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): 모델 기능은 원격 분석, 오류 및 유지 관리 데이터에 대한 표준 시계열 접근 방식을 사용하여 Azure Blob 저장소의 원시 데이터 집합에서 구성됩니다. 오류 관련 구성 요소 대체는 어떤 구성 요소에 오류가 있는지 설명하는 모델 레이블을 구성하는 데 사용됩니다. 레이블이 지정된 기능 데이터는 모델 빌드 노트를 위해 Azure Blob에 저장됩니다.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): 모델 빌드 노트는 레이블이 지정된 기능 데이터 집합을 사용하고, 데이터를 날짜-시간 스탬프에 따라 학습 및 개발 데이터 집합으로 분할합니다. 이 노트는 pyspark.ml.classification 모델을 사용하는 실험으로 설정됩니다. 학습 데이터는 벡터화됩니다. 사용자는 **DecisionTreeClassifier** 또는 **RandomForestClassifier**로 실험하면서 잘 작동하는 모델을 찾도록 하이퍼 매개 변수를 조작할 수 있습니다. 성능은 개발 데이터 집합에 대한 측정값 통계를 평가하여 결정됩니다. 이러한 통계는 추적을 위해 Machine Learning Workbench 실행 시간 화면에 다시 기록됩니다. 노트는 각 실행의 결과 모델을 Jupyter 노트 커널을 실행하는 로컬 디스크에 저장합니다. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): 이 노트는 로컬(Jupyter 노트 커널) 디스크에 저장된 마지막 모델을 사용하여 모델을 Azure 웹 서비스로 배포하기 위한 구성 요소를 빌드합니다. 전체 운용 자산은 다른 Azure BLOB 컨테이너에 저장된 o16n.zip 파일로 압축됩니다. 압축 파일에는 다음이 포함됩니다.

* **service_schema.json**: 배포할 스키마 정의 파일입니다. 
* **pdmscore.py**: Azure 웹 서비스에 필요한 **init()** 및 **run()** 함수입니다.
* **pdmrfull.model**: 모델 정의 디렉터리입니다.
    
노트북은 배포를 위해 운용 자산을 패키징하기 전에 모델 정의를 사용하여 함수를 테스트합니다. 배포 지침은 노트북의 끝에 있습니다.

## <a name="conclusion"></a>결론

이 시나리오는 Machine Learning Workbench의 Jupyter 노트 환경에서 PySpark를 사용하여 종단 간 예측 유지 관리 솔루션을 빌드하는 방법에 대한 개요를 제공합니다. 이 예제 시나리오에서는 Machine Learning 모델 관리 환경을 통한 모델 배포로 장비 오류를 실시간으로 예측하는 방법도 자세히 설명합니다.

## <a name="references"></a>참조

다음 참조에서는 다양한 플랫폼에 대한 기타 예측 유지 관리 사용 사례 예제를 제공합니다.

* [예측 유지 관리 솔루션 템플릿](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R Services를 사용한 예측 유지 관리 모델링 가이드](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [예측 유지 관리 모델링 가이드 Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark를 사용한 예측 유지 관리](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [예측 유지 관리에 대한 딥 러닝](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>다음 단계

Machine Learning Workbench에는 제품의 추가 기능을 보여주는 많은 다른 예제 시나리오가 있습니다. 
