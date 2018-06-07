---
title: 소득 분류 - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: Team Data Science Process 템플릿을 사용하여 Azure Machine Learning에서 미국 소득을 분류하는 프로젝트를 만드는 방법입니다.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 48c88f541f650fac3bdec431f3164138fb0f3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>TDSP(Team Data Science Process) 프로젝트에서 소득 분류

## <a name="introduction"></a>소개

데이터 과학 팀의 공동 작업 효율성을 높이기 위한 핵심은 설정된 [데이터 과학 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)에 고정된 데이터 과학 프로젝트의 구조 및 문서를 표준화하는 것입니다. [TDSP(Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP) 템플릿을 사용하여 Azure Machine Learning 프로젝트를 만들면 이렇게 표준화를 위한 프레임워크가 제공됩니다.

이전에 [TDSP 프로젝트 구조 및 템플릿에 대한 GitHub 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate)를 릴리스했습니다. 하지만 지금까지는 데이터 과학 도구 내에서 TDSP 구조 및 템플릿을 인스턴스화할 수 없었습니다. [Azure Machine Learning에 대한 TDSP 구조 및 문서 템플릿](https://github.com/amlsamples/tdsp)에서 인스턴스화된 Azure Machine Learning 프로젝트를 생성하도록 설정되었습니다. Azure Machine Learning에서 TDSP 구조와 템플릿을 사용하는 방법에 대한 지침은 [여기](https://aka.ms/how-to-use-tdsp-in-aml)에서 제공됩니다. 여기에서는 TDSP 구조를 사용하여 실제 Machine Learning 프로젝트를 만들고, 프로젝트 지정 코드, 아티팩트 및 문서로 채우고, Azure Machine Learning 내에서 실행하는 방법의 예제를 제공합니다.

## <a name="link-to-github-repository"></a>GitHub 리포지토리에 연결
샘플에 대한 요약 설명이 [여기](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)에서 제공됩니다. GitHub 사이트에서 더 광범위한 설명서를 찾을 수 있습니다.

### <a name="purpose"></a>목적
이 샘플은 Azure Machine Learning에서 [TDSP(Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP) 구조 및 템플릿을 사용하여 Machine Learning 프로젝트를 인스턴스화하고 실행하는 방법을 보여주려고 합니다. 이를 위해 잘 알려진 [UCI Machine Learning 리포지토리의 1994 미국 인구 조사 데이터](https://archive.ics.uci.edu/ml/datasets/adult)를 사용합니다. 모델링 작업은 미국 인구 조사 정보(예: 나이, 인종, 교육 수준, 국적 등)에서 미국 연간 소득 수준을 예측하는 것입니다.

### <a name="scope"></a>범위
 * 사용 사례 개요에 설명된 예측 문제를 해결하는 Machine Learning 모델의 데이터 탐색, 학습 및 배포 
 * 이 프로젝트에 Azure Machine Learning의 TDSP(Team Data Science Process) 템플릿을 사용하여 Azure Machine Learning의 프로젝트 실행 프로젝트 실행 및 보고를 위해 TDSP 수명 주기를 사용하도록 하겠습니다.
 * Azure Container Service에서 Azure Machine Learning의 솔루션 직접 운영

 프로젝트는 TDSP 구조 인스턴스화 및 사용, Jupyter 노트북과 Python 파일의 코드 실행 및 Docker 및 Kubernetes를 사용하는 Azure Container Service에서 간편한 운영과 같은 Azure Machine Learning의 몇 가지 기능을 강조 표시합니다.


## <a name="team-data-science-process-tdsp-lifecycle"></a>TDSP(Team Data Science Process) 수명 주기
[TDSP(Team Data Science Process) 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)를 참조하세요.

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>필수 조건
### <a name="required-subscription-hardware-software"></a>필수: 구독, 하드웨어, 소프트웨어
1. [Azure](https://azure.microsoft.com) 구독 [체험 구독](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg)으로 이 샘플을 실행할 수도 있습니다.
2. [Azure DSVM(Science Virtual Machine) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm)(VM 크기: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), 4개의 가상 CPU 및 14Gb RAM포함) Azure DSVM에서 테스트되었지만 모든 Windows 10 컴퓨터에서 작동할 가능성이 높습니다.
3. Azure Machine Learning 및 관련 서비스에 대한 설명서를 검토합니다(링크는 아래 참조).
4. [빠른 시작 설치 가이드](../service/quickstart-installation.md)에서 Azure Machine Learning을 제대로 설치했는지 확인합니다.

이 샘플의 데이터 집합은 UCI ML 리포지토리[[링크]](https://archive.ics.uci.edu/ml/datasets/adult)에서 가져옵니다. 1994 미국 인구 조사 데이터베이스에서 가져왔으며 약 50,000명의 개인에 대한 인구 조사 및 소득 정보를 포함합니다. 숫자 및 범주 기능이 있는 데이터 집합 및 두 가지 소득 범주로 구성된 범주 대상입니다('>50K' 또는 '<=50K'). 

### <a name="optional-version-control-repository"></a>선택 사항: 버전 제어 리포지토리
프로젝트와 해당 콘텐츠를 저장하고 버전을 제어하려는 경우 작업을 수행할 수 있는 버전 제어 리포지토리가 있어야 합니다. Azure Machine Learning에서 TDSP 템플릿을 사용하여 새 프로젝트를 만드는 동안 Git 리포지토리 위치를 입력할 수 있습니다. 세부 정보는 [Azure Machine Learning에서 Git을 사용하는 방법](using-git-ml-project.md)을 참조하세요.

### <a name="informational-about-azure-machine-learning"></a>정보 제공: Azure Machine Learning 정보
* [FAQ - 시작 방법](frequently-asked-questions.md)
* [개요](../service/overview-what-is-azure-ml.md)
* [설치](../service/quickstart-installation.md)
* [실행](experimentation-service-configuration.md)
* [TDSP 사용](https://aka.ms/how-to-use-tdsp-in-aml)
* [파일 읽기 및 쓰기](how-to-read-write-files.md)
* [Azure Machine Learning에서 Git 사용](using-git-ml-project.md)
* [ML 모델을 웹 서비스로 배포](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **검색 프로젝트 템플릿** 검색 상자에서 "미국 소득 분류 - TDSP 프로젝트"를 입력하고 템플릿을 선택합니다.
5.  **만들기**

프로젝트를 만드는 동안 적절한 상자에 빈 Git 리포지토리 위치를 입력하면 프로젝트 생성 후 해당 리포지토리가 프로젝트 구조 및 콘텐츠로 채워집니다.

## <a name="use-case-overview"></a>사용 사례 개요
문제는 미국 인구 조사에서 수집된 사회 경제적 데이터를 통해 미국에서 개인의 연간 소득을 예측할 수 있는 방법을 이해하는 것니다. 이러한 인구 조사 기능에 따라 Machine Learning 작업은 개인의 소득이 $50,000 이상인지 아닌지를 예측하는 것입니다(이진 분류 작업).

## <a name="data-description"></a>데이터 설명
데이터에 대한 자세한 내용은 UCI 리포지토리에서 [설명](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names)을 참조하세요. 

이 데이터는 https://www.census.gov/en.html에서 찾은 인구 조사국 데이터베이스에서 추출되었습니다. 


* 필터링 이전에 불연속 및 연속이 섞인 총 48,842개의 인스턴스(학습 = 32,561개, 테스트 = 16,281개)가 있습니다.
* 레이블에 대한 확률 '>50K' :23.93%/24.78%(알 수 없음 제외)
* 레이블에 대한 확률 '>50K' :76.07%/75.22%(알 수 없음 제외)  

* **대상**: 소득 수준 '>50K', '<=50K' 이러한 항목은 데이터 준비 단계에서 각각 1과 0으로 바뀝니다.
* **기능**: 나이, 직업 수준, 교육 수준, 인종, 성별, 주당 작업 시간 등입니다.


## <a name="project-structure-execution-and-reporting"></a>프로젝트 구조, 실행 및 보고

### <a name="structure"></a>구조
이 프로젝트의 경우 TDSP 폴더 구조 및 문서 템플릿(아래)을 사용합니다. 이 템플릿은 [TDSP 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)를 따릅니다. 

프로젝트는 [여기](https://aka.ms/how-to-use-tdsp-in-aml)에 제공된 지침에 따라 생성됩니다. 구조가 프로젝트의 코드 및 아티팩트로 채워진 후에는 다음과 같습니다(아래 그림의 빨간색 상자에 있는 프로젝트 구조 참조).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>실행
이 예제에서 코드를 **로컬 계산 환경**에서 실행합니다. [실행 옵션](experimentation-service-configuration.md)에 대한 자세한 내용은 Azure Machine Learning 문서를 참조합니다.

로컬 Python 런타임에서 Python 스크립트를 쉽게 실행할 수 있습니다.

    az ml experiment submit -c local my_script.py

Azure Machine Learning UI의 왼쪽의 프로젝트 구조에서 IPython Notebook 파일을 두 번 클릭하고 Jypyter Notebook 서버에서 실행할 수 있습니다.


단계별 데이터 과학 워크플로는 다음과 같습니다.

* [**데이터 취득 및 이해**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

UCI ML 리포지토리[[링크]](https://archive.ics.uci.edu/ml/datasets/adult)에서 URL의 .csv 형식으로 데이터를 다운로드합니다. 기능, 대상 및 해당 변환은 ProjectReport.md 파일에서 자세히 설명되어 있습니다.

데이터 취득 및 이해를 위한 코드는 /code/01_data_acquisition_and_understanding에 위치합니다.

데이터 탐색은 [데이터 과학 도구의 TDSP 제품군](https://github.com/Azure/Azure-TDSP-Utilities) 중 일부로 게시된 Python 3 [IDEAR(대화형 데이터 탐색 및 보고) 유틸리티](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python)를 사용하여 수행됩니다. 이 유틸리티를 통해 숫자와 범주 기능 및 대상을 포함하는 데이터에 대한 표준화된 데이터 탐색 보고서를 생성할 수 있습니다. Python 3 IDEAR 유틸리티를 사용하는 방법에 대한 세부 정보는 아래에 제공됩니다. 

마지막 데이터 탐색 보고서의 위치는 [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs)입니다. IDEAR 보고서의 뷰는 다음과 같습니다.

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**모델링**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

3단계 교차 유효성 검사를 사용하여 탄력적 네트워크 및 임의 포리스트라는 두 개의 모델을 만듭니다. 교차 유효성 검사 및 모델 매개 변수 최적화를 위한 전략으로 임의 그리드 검색에 [59개 지점 샘플링](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf)을 사용했습니다. 테스트 데이터 집합에서 AUC(곡선 아래의 영역)를 사용하여 모델의 정확도를 측정했습니다. 

모델링을 위한 코드는 /code/02_modeling에 위치합니다.

탄력적 네트워크 및 임의 포리스트 모델의 AUC는 >0.85이었습니다. 두 모델을 pickled.pkl 파일에 저장하고 두 모델의 ROC 플롯을 출력합니다. 임의 포리스트 모델의 AUC는 0.92이며 탄력적 네트워크 모델의 AUC는 0.90입니다. 또한 모델 해석을 위해 임의 포리스트 모델의 기능 중요도는 .csv 파일로 출력되고 .pdf(상위 20개 예측 기능만 해당) 파일로 표시됩니다.

테스트 데이터에 대한 **임의 포리스트 모델**의 ROC 곡선은 아래에 표시됩니다. 배포된 모델은 다음과 같습니다.

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

임의 포리스트 모델의 기능 중요도(상위 20)는 아래에 표시됩니다. 기능 중요도가 가장 높은 자본 소득량, 교육, 결혼 여부를 보여줍니다.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**배포**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

임의 포리스트 모델을 [ACS(Azure Container Service)](https://azure.microsoft.com/services/container-service/)의 클러스터에 웹 서비스로 배포했습니다. 운영 환경에서는 클러스터의 Docker 및 Kubernetes를 프로비전하여 웹 서비스 배포를 관리합니다. 운영 프로세스에 대한 자세한 내용은 [여기](model-management-service-deploy.md)를 참조하세요. 

배포의 코드는 /code/03_deployment에 위치합니다.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[최종 프로젝트 보고서](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
위의 섹션에 대한 각각의 세부 정보는 컴파일된 최종 프로젝트 보고서 [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)에 제공됩니다. 프로젝트 보고서에는 사용 사례, 모델 성능 메트릭, 배포 및 프로젝트를 개발하고 배포한 인프라에 대한 세부 정보가 포함되어 있습니다.

전체 프로젝트 폴더의 콘텐츠와 함께 프로젝트 보고서 및 버전 제어 리포지토리는 클라이언트에 전달될 수 있습니다.


## <a name="conclusion"></a>결론

이 샘플에서는 Azure Machine Learning에서 TDSP 구조와 템플릿을 사용하는 방법을 보여주었습니다. 문서와 아티팩트 템플릿을 통해 다음을 수행할 수 있습니다.
1. 프로젝트의 용도 및 범위를 제대로 정의
2. 분산된 역할 및 책임을 가진 프로젝트 팀 만들기
3. TDSP 수명 주기 단계에 따라 프로젝트 구성 및 실행
4. TDSP 데이터 과학 유틸리티를 사용하여 표준화된 보고서 개발(예: IDEAR 데이터 탐색 및 시각화 보고서)
5. 클라이언트에 제공할 수 있는 최종 데이터 과학 프로젝트 보고서 준비

Azure Machine Learning의 이 기능을 사용하여 데이터 과학 팀에서 표준화 및 공동 작업을 용이하게 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 아래 참조를 참조하세요.

[Azure Machine Learning에서 TDSP(Team Data Science Process)를 사용하는 방법](https://aka.ms/how-to-use-tdsp-in-aml)

[TDSP(Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP)

[Azure Machine Learning의 TDSP 프로젝트 템플릿](https://aka.ms/tdspamlgithubrepo)

[UCI ML 리포지토리의 미국 소득 데이터 집합](https://archive.ics.uci.edu/ml/datasets/adult)