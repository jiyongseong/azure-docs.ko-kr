---
title: Azure Machine Learning Workbench에서 CNTK를 사용하여 이미지 분류 | Microsoft Docs
description: Azure ML Workbench를 사용하여 사용자 지정 이미지 분류 모델을 학습, 평가 및 배포합니다.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 8bf5cd802198cba48a99c029d0c75c25dd5f6d84
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31606522"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench를 사용하여 이미지 분류

이미지 분류 방법은 많은 수의 컴퓨터 시각화 문제를 해결하는 데 사용할 수 있습니다.
여기에는 *이미지에 개체가 있습니까?* 와 같은 질문에 답하는 건물 모델이 포함되며, 이러한 개체로 *개*, *자동차* 또는 *배* 등이 있습니다 또는 *이 환자의 망막 스캔을 통해 안과 질환에 대해 어떤 종류의 심각도가 표시됩니까?* 와 같이 더 복잡한 질문도 있습니다.

이 자습서에서는 이러한 문제를 해결하는 방법에 대해 설명합니다. 심층적인 학습을 위해 [Microsoft CNTK(Cognitive Toolkit)](https://docs.microsoft.com/cognitive-toolkit/)를 사용하여 자신의 이미지 분류 모델을 학습, 평가 및 배포하는 방법을 보여 줍니다.
예제 이미지가 제공되지만 사용자 고유의 데이터 집합을 가져와서 자신의 사용자 지정 모델을 학습할 수도 있습니다.

컴퓨터 시각화 솔루션에서는 일반적으로 이미지에서 원하는 정보를 강조 표시하는 소위 *기능*을 수동으로 식별하고 구현하는 전문 지식이 필요했습니다.
수동적인 이 접근 방식은 2012년 [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)의 유명한 [1] Deep Learning(심층 학습) 논문을 통해 변경되었으며, 현재 DNN(Deep Nural Networks, 심층 신경망)은 이러한 기능을 자동으로 찾는 데 사용됩니다.
DNN은 이미지 분류뿐만 아니라 개체 검색 및 이미지 유사성과 같은 다른 컴퓨터 시각화 문제에 대해서도 큰 발전을 이루었습니다.


## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>개요

이 자습서는 세 부분으로 나뉩니다.

- 1부 - 미리 학습된 DNN을 featurizer(패턴 변환기)로 사용하여 이미지 분류 시스템을 학습, 평가 및 배포하는 방법을 보여 주며, 출력 시 SVM(Support Vector Machine)을 학습합니다.
- 2부 - DNN을 고정된 패턴 변환기로 사용하는 대신 구체화하는 방식과 같이 정확도를 향상시키는 방법을 보여 줍니다.
- 3부 - 제공된 예제 이미지 대신 사용자 고유의 데이터 집합을 사용하는 방법과 필요에 따라 네트워크에서 이미지를 스크랩하여 사용자 고유의 데이터 집합을 생성하는 방법에 대해 설명합니다.

기계 학습 및 CNTK에 대한 이전의 경험은 필요하지 않지만 기본 원리를 이해하는 데 도움이 됩니다. 자습서에서 보고된 정확도 숫자, 학습 시간 등은 참조용일 뿐이며, 코드 실행 시의 실제 값은 거의 틀림없이 다릅니다.


## <a name="prerequisites"></a>필수 조건

이 예제를 실행하기 위한 필수 조건은 다음과 같습니다.

1. [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
2. [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) - [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 프로그램을 설치하고 작업 영역을 만들 수 있습니다.  
3. Windows 컴퓨터 - Workbench는 Windows와 MacOS만 지원하므로 Windows OS가 필요하지만, Microsoft Cognitive Toolkit(심층 학습 라이브러리로 사용됨)는 Windows와 Linux만 지원합니다.
4. 전용 GPU - 1부에서 SVM 학습을 실행하는 데에는 필요하지 않지만, 2부에서 설명하는 DNN을 구체화하는 데에는 필요합니다. 강력한 GPU가 부족하거나, 여러 GPU에서 학습하려거나, Windows 컴퓨터가 없는 경우 Windows 운영 체제에서 Azure의 Deep Learning Virtual Machine을 사용하는 것이 좋습니다. 한 번 클릭으로 배포 가이드에 대해서는 [여기](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)를 참조하세요. 배포가 완료되면 원격 데스크톱 연결을 통해 VM에 연결하고, Workbench를 설치한 다음, VM에서 로컬로 코드를 실행합니다.
5. OpenCV와 같은 다양한 Python 라이브러리를 설치해야 합니다. Workbench의 *파일* 메뉴에서 *명령 프롬프트 열기*를 클릭하고 다음 명령을 실행하여 이러한 종속 항목을 설치합니다.  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` - http://www.lfd.uci.edu/~gohlke/pythonlibs/에서 OpenCV 휠 파일을 다운로드한 후(정확한 파일 이름과 버전은 변경될 수 있음)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>문제 해결/알려진 버그
- 2부에는 GPU가 필요합니다. 그렇지 않으면 DNN을 수정하려고 할 때 "CPU에서 정규화 학습 일괄 처리가 아직 구현되지 않았습니다"라는 오류가 발생합니다.
- 미니 일괄 처리 크기(`PARAMETERS.py`의 `cntk_mb_size` 변수)를 줄이면 DNN 학습 중 메모리 부족 오류를 방지할 수 있습니다.
- 이 코드는 CNTK 2.2를 사용하여 테스트되었으며, 변경하지 않거나 아주 약간만 변경하여 이전 버전(최대 v2.0) 및 새 버전에서도 실행되어야 합니다.
- 이 문서의 작성 시점에서 5MB보다 큰 노트북을 Azure Machine Learning Workbench에서 표시하는 데 문제가 있었습니다. 모든 셀 출력이 표시되는 노트북을 저장한 경우에 이 큰 크기의 노트북이 발생할 수 있습니다. 이 오류가 발생하면 Workbench의 [파일] 메뉴에서 명령 프롬프트를 열고, `jupyter notebook`을 실행하고, 노트북을 열고, 모든 출력을 지우고, 노트북을 저장합니다. 이러한 단계를 수행하면 Azure Machine Learning Workbench에서 해당 노트북이 다시 열립니다.
- 이 샘플에서 제공되는 모든 스크립트는 로컬로 실행되어야 하고 Docker 원격 환경 등에서 실행되면 안 됩니다. 모든 노트북은 이름이 “PROJECTNAME local”(예: “myImgClassUsingCNTK local”)인 로컬 프로젝트 커널로 설정된 커널을 사용하여 실행되어야 합니다.

    
## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

다음과 같이 이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench를 엽니다.
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 상자에서 "이미지 분류"를 입력하고 템플릿을 선택합니다.
5.  **만들기**를 클릭합니다.

이러한 단계를 수행하면 아래에 표시된 프로젝트 구조가 만들어집니다. Azure Machine Learning Workbench는 각 실행 후에 이 폴더의 복사본을 만들어 실행 기록을 사용하도록 설정하므로 프로젝트 디렉터리가 25MB 미만으로 제한됩니다. 따라서 모든 이미지 및 임시 파일은 *~/Desktop/imgClassificationUsingCntk_data* 디렉터리(이 문서에서는 *DATA_DIR*로 참조됨)에 저장됩니다.

  폴더| 설명
  ---|---
  aml_config/|                           Azure Machine Learning Workbench 구성 파일이 포함된 디렉터리
  libraries/|                              모든 Python 및 Jupyter 도우미 함수가 포함된 디렉터리
  notebooks/|                              모든 노트북이 포함된 디렉터리
  resources/|                              모든 리소스(예: 패션 이미지의 URL)가 포함된 디렉터리
  scripts/|                              모든 스크립트가 포함된 디렉터리
  PARAMETERS.py|                       모든 매개 변수를 지정한 Python 스크립트
  readme.md|                           이 추가 정보 문서


## <a name="data-description"></a>데이터 설명

이 자습서에서는 최대 428개의 이미지로 구성된 상반신 의류 질감 데이터 집합을 실행 예제로 사용합니다. 각 이미지는 서로 다른 세 가지 질감(점 무늬, 줄 무늬, 표범 무늬) 중 하나로 주석 처리됩니다. 이 자습서를 빨리 실행할 수 있도록 이미지 수를 줄였습니다. 그러나 이 코드는 충분히 테스트되었으며 수만 개 이상의 이미지로 작동합니다. Bing Image Search를 사용하여 모든 이미지를 스크랩하고 [3부](#using-a-custom-dataset)에서 설명한 대로 주석을 직접 달았습니다. 해당 속성이 있는 이미지 URL은 */resources/fashionTextureUrls.tsv* 파일에 나열됩니다.

`0_downloadData.py` 스크립트는 모든 이미지를 *DATA_DIR/images/fashionTexture/* 디렉터리에 다운로드합니다. 428개 URL 중 일부가 손상되었을 수도 있습니다. 이 경우 그다지 문제가 되지 않으며, 학습 및 테스트에 맞게 이미지 수가 약간 줄었음을 의미합니다. 이 샘플에서 제공되는 모든 스크립트는 로컬로 실행되어야 하고 Docker 원격 환경 등에서 실행되면 안 됩니다.

다음 그림에서는 점 무늬(왼쪽), 줄 무늬(가운데) 및 표범 무늬(오른쪽)에 대한 예제를 보여 줍니다. 주석은 상반신 의류 항목에 따라 처리되었습니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>1부 - 모델 학습 및 평가

이 자습서의 1부에서는 미리 학습된 심층 신경망을 사용하지만 수정하지 않은 시스템을 학습합니다. 이 미리 학습된 DNN은 패턴 변환기로 사용되고, 선형 SVM은 지정된 이미지의 특성(점 무늬, 줄 무늬 또는 표범 무늬)을 예측하도록 학습됩니다.

이제 이 방법을 단계별로 자세하게 설명하고, 실행해야 하는 스크립트를 보여 줍니다. 각 단계마다 작성된 파일과 해당 위치를 검사하는 것이 좋습니다.

지정된 모든 중요한 매개 변수와 간단한 설명이 한 곳(`PARAMETERS.py` 파일)에서 제공됩니다.




### <a name="step-1-data-preparation"></a>1단계: 데이터 준비
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

`showImages.ipynb` 노트북을 사용하여 이미지를 시각화하고, 필요에 따라 해당 주석을 수정할 수 있습니다. 노트북을 실행하려면 Azure Machine Learning Workbench에서 해당 노트북을 열고, “노트북 서버 시작”(이 옵션이 표시된 경우)을 클릭한 다음, 이름이 “PROJECTNAME local”(예: “myImgClassUsingCNTK local”)인 로컬 프로젝트 커널로 이동하고, 노트북의 모든 셀을 실행합니다. 노트북이 너무 커서 표시할 수 없다는 오류가 발생하면 이 문서의 문제 해결 섹션을 참조하세요.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

이제 `1_prepareData.py`라는 스크립트를 실행합니다. 이 스크립트는 모든 이미지를 학습 집합 또는 테스트 집합에 할당합니다. 이 할당은 함께 사용할 수 없으므로 어떠한 학습 이미지도 테스트에 사용되지 않으며 그 반대의 경우도 마찬가지입니다. 기본적으로 각 특성 클래스의 임의의 이미지 75%가 학습에 할당되고 나머지 25%는 테스트에 할당됩니다. 스크립트에서 생성된 모든 데이터는 *DATA_DIR/proc/fashionTexture/* 폴더에 저장됩니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>2단계: 심층 신경망 구체화
`Script: 2_refineDNN.py`

이 자습서의 1부에서 설명한 대로 미리 학습된 DNN은 고정된 채로 유지됩니다(즉 구체화되지 않음). 그러나 `2_refineDNN.py`라는 스크립트는 미리 학습된 [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] 모델을 로드하고, 예를 들어 더 높은 입력 이미지 해상도를 허용하도록 수정하기 때문에 1부에서 계속 실행됩니다. 이 단계는 빠르며(초 단위), GPU가 필요하지 않습니다.

이 자습서의 2부에서 PARAMETERS.py 파일을 수정하면 `2_refineDNN.py` 스크립트에서 미리 학습된 DNN도 구체화합니다. 구체화 과정에서는 기본적으로 45개의 학습 Epoch를 실행합니다.

두 경우 모두 최종 모델은 *DATA_DIR/proc/fashionTexture/cntk_fixed.model* 파일에 기록됩니다.

### <a name="step-3-evaluate-dnn-for-all-images"></a>3단계: 모든 이미지에 대한 DNN 평가
`Script: 3_runDNN.py`

마지막 단계에서 DNN(아마도 구체화되었음)을 사용하여 이미지 패턴을 변환할 수 있습니다. 이미지를 DNN의 입력으로 지정하면 출력은 모델의 끝에서 두 번째 계층의 512개 부동 소수점 수 벡터입니다. 이 벡터는 이미지 자체보다 훨씬 작은 차원입니다. 그럼에도 불구하고 이미지의 특성을 인식하는 것과 관련된 이미지(즉, 의류 항목에 점 무늬, 줄 무늬 또는 표범 무늬 질감이 있는 경우)의 모든 정보가 포함(및 강조 표시)되어야 합니다.

모든 DNN 이미지 표현은 *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle* 파일에 저장됩니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>4단계: SVM(Support Vector Machine) 지원
`Script: 4_trainSVM.py`

마지막 단계에서 계산된 512개 부동 소수점 수 표현은 이제 SVM 분류자를 학습하는 데 사용됩니다. 이미지를 입력으로 지정하면 SVM에서 존재할 각 특성에 대한 점수를 출력합니다. 이 예제 데이터 집합에서는 '줄 무늬', '점 무늬' 및 '표범 무늬'에 대한 점수를 의미합니다.

`4_trainSVM.py` 스크립트는 학습 이미지를 로드하고, C 정규화(슬랙) 매개 변수의 다른 값에 대해 SVM을 학습하며, SVM을 가장 높은 정확도로 유지합니다. 분류 정확도는 콘솔에 출력되고 Workbench에 표시됩니다. 제공된 질감 데이터의 경우 이러한 값은 대략 각각 100% 및 88%여야 합니다. 마지막으로 학습된 SVM은 *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np* 파일에 기록됩니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>5단계: 평가 및 시각화
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

학습된 이미지 분류자의 정확도는 `5_evaluate.py` 스크립트를 사용하여 측정할 수 있습니다. 이 스크립트는 학습된 SVM 분류자를 사용하여 모든 테스트 이미지에 대해 점수를 매기고, 각 이미지에 점수가 가장 높은 특성을 할당하고, 예측된 특성을 기본 실측 정보 주석과 비교합니다.

`5_evaluate.py` 스크립트의 출력은 다음과 같습니다. 전체 학습 집합의 정확도('전체 정확도')와 개별 정확도의 평균('전체 클래스 평균 정확도')뿐만 아니라 각 개별 클래스의 분류 정확도도 계산됩니다. 100%는 가능한 가장 높은 정확도에 해당하고, 0%는 최악의 정확도에 해당합니다. 임의 추측은 평균적으로 특성 수에 대해 1의 클래스 평균 정확도를 생성하며, 여기서의 정확도는 33.33%입니다. DNN 계산 시간이 더 오래 걸리지만 `rf_inputResoluton = 1000`과 같은 높은 입력 해상도를 사용하는 경우 이러한 결과가 크게 향상됩니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

정확도 외에도 왼쪽에는 ROC 곡선이 각 영역 아래에서 곡선으로 그려지며, 오른쪽에는 혼동 행렬이 표시됩니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

마지막으로 `showResults.py` 노트북이 테스트 이미지를 스크롤하고 각 분류 점수를 시각화하는 데 제공됩니다. 1단계에서 설명한 대로 이 샘플의 모든 노트북은 이름이 “PROJECTNAME local”인 로컬 프로젝트 커널을 사용해야 합니다.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>6단계: 배포
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

이제 학습된 시스템을 REST API로 게시할 수 있습니다. 배포는 `deploy.ipynb` 노트북에 설명되어 있으며 Azure Machine Learning Workbench의 기능을 기반으로 합니다(이름이 “PROJECTNAME local”인 로컬 프로젝트 커널을 커널로 설정해야 함). 배포에 대한 자세한 내용은 [아이리스 자습서](tutorial-classifying-iris-part-3.md)의 배포 섹션도 참조하세요.

일단 배포되면 `6_callWebservice.py` 스크립트를 사용하여 웹 서비스를 호출할 수 있습니다. 웹 서비스의 IP 주소(로컬 또는 클라우드)는 스크립트에서 맨 먼저 설정해야 합니다. 이 IP 주소를 찾는 방법은 `deploy.ipynb` 노트북에 설명되어 있습니다.








## <a name="part-2---accuracy-improvements"></a>2부 - 정확도 향상

1부에서는 DNN의 512개 부동 소수점 수 출력에서 선형 SVM(Support Vector Machine)을 학습하여 이미지를 분류하는 방법을 보여 주었습니다. 이 DNN은 수백만 개의 이미지에서 미리 학습되었고, 끝에서 두 번째 계층은 패턴 벡터로 반환되었습니다. 이 방법은 DNN을 있는 그대로 사용하므로 빠르지만, 그럼에도 불구하고 좋은 결과를 제공하는 경우가 많습니다.

이제 1부 모델의 정확도를 향상시키는 몇 가지 방법을 제시합니다. 가장 중요한 것은 DNN을 고정한 채로 유지하지 않고 구체화하는 것입니다.

### <a name="dnn-refinement"></a>DNN 구체화

SVM 대신 신경망에서 직접 분류를 수행할 수 있습니다. 이는 미리 학습된 DNN에 새로운 마지막 계층을 추가하여 이루어지며, 끝에서 두 번째 계층의 512개 부동 소수점 수를 입력으로 사용합니다. DNN에서 분류를 수행하는 이점은 이제 역전파를 사용하여 전체 네트워크를 다시 학습할 수 있다는 것입니다. 이 방법을 사용하면 미리 학습된 DNN을 있는 그대로 사용하는 것과 비교하여 종종 분류 정확도를 훨씬 높여주지만, GPU를 사용하더라도 훨씬 오랜 학습 시간을 소모합니다.

SVM 대신 신경망을 학습하는 경우 `PARAMETERS.py`의 `classifier` 변수를 `svm`에서 `dnn`으로 변경하여 수행됩니다. 그런 다음, 1부에서 설명한 대로 데이터 준비(1단계) 및 SVM 학습(4단계)을 제외한 모든 스크립트를 다시 실행해야 합니다. DNN 구체화에는 GPU가 필요합니다. GPU가 없거나 잠겨 있으면(예: 이전 CNTK 실행으로 인해) `2_refineDNN.py`스크립트에서 오류를 throw합니다. DNN 학습은 일부 GPU에서 메모리 부족 오류를 throw할 수 있습니다. 이 경우 미니 일괄 처리 크기(`PARAMETERS.py`의 `cntk_mb_size` 변수)를 줄이면 DNN 학습 중 메모리 부족 오류를 방지할 수 있습니다.

학습이 완료되면 구체화된 모델이 *DATA_DIR/proc/fashionTexture/cntk_refined.model*에 저장되고, 학습 중에 학습 및 테스트 분류 오류가 변하는 모습을 보여 주는 플롯이 그려집니다. 이 플롯에서는 학습 집합의 오류가 테스트 집합보다 훨씬 작습니다. 소위 이러한 과잉 맞춤 동작은 예를 들어 `rf_dropoutRate` 드롭아웃 속도에 더 높은 값을 사용하여 줄일 수 있습니다.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

아래 플롯에서 알 수 있듯이 제공된 데이터 집합에서 DNN 구체화를 사용한 정확도는 92.35%와 88.92%(1부) 사이입니다. 특히 ROC 영역 아래의 곡선이 앞서의 0.94에서 0.98로 자세히 구체화되어 '점선' 이미지는 크게 향상되었습니다. 작은 데이터 집합을 사용하고 있으므로 코드를 실행하는 실제 정확도는 다릅니다. 이러한 불일치는 이미지를 임의로 학습 및 테스트 집합으로 분할하는 것과 같은 확률적 효과 때문입니다.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>실행 기록 추적

Azure Machine Learning Workbench는 각 실행의 기록을 Azure에 저장하여 주 간격으로도 두 개 이상의 실행을 비교할 수 있습니다. 이는 [아이리스 자습서](tutorial-classifying-iris-part-2.md)에서 자세히 설명하고 있습니다. 또한 다음 스크린샷에서도 `classifier = "dnn"` DNN 구체화(실행 번호 148) 또는 `classifier = "svm"` SVM 학습(실행 번호 150) 중 하나를 사용하여 `5_evaluate.py` 스크립트의 두 번 실행을 비교하고 있습니다.

첫 번째 스크린샷에서는 DNN 구체화는 모든 클래스의 SVM 학습보다 더 나은 정확도를 제공합니다. 두 번째 스크린샷에서는 분류자를 포함하여 추적 중인 모든 메트릭이 표시됩니다. 이 추적은 Azure Machine Learning Workbench 로거를 호출하여 `5_evaluate.py` 스크립트에서 수행됩니다. 또한 이 스크립트는 ROC 곡선 및 혼동 행렬을 *outputs* 폴더에 저장합니다. 특히 이 *outputs* 폴더는 Workbench 기록 기능에 따라 해당 내용도 추적되므로 로컬 복사본이 덮어쓰였는지 여부에 관계없이 언제든지 출력 파일에 액세스할 수 있습니다.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/> </p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>매개 변수 튜닝
대부분의 기계 학습 프로젝트와 마찬가지로 새 데이터 집합에 대해 좋은 결과를 얻으려면 신중한 매개 변수 튜닝과 다양한 설계 결정에 대한 평가가 필요합니다. 이러한 작업을 지원하기 위해 지정된 모든 중요한 매개 변수와 간단한 설명이 한 곳(`PARAMETERS.py` 파일)에서 제공됩니다.

기능 향상을 위해 가장 가능성 있는 방법은 다음과 같습니다.

- 데이터 품질: 학습 및 테스트 집합의 품질이 우수해야 합니다. 즉 이미지에 정확하게 주석을 달고 모호한 이미지(예: 줄 무늬와 점 무늬 모두가 있는 의류 품목)를 제거하고, 특성은 함께 사용할 수 없으므로 각 이미지가 정확히 하나의 특성에만 속하도록 선택합니다.
- 이미지에서 관심 있는 개체가 작으면 이미지 분류 방법이 제대로 작동하지 않은 것으로 알려져 있습니다. 이 경우 이 [자습서](https://github.com/Azure/ObjectDetectionUsingCntk)에서 설명한 대로 개체 검색 방법을 사용하는 것이 좋습니다.
- DNN 구체화: 바로 가져올 수 있는 가장 중요한 매개 변수는 `rf_lrPerMb` 학습 속도입니다. 학습 집합의 정확도(2부의 첫 번째 그림)가 0-5%에 가깝지 않으면 학습 속도가 잘못되었을 가능성이 큽니다. `rf_`로 시작하는 다른 매개 변수는 덜 중요합니다. 일반적으로 학습 오류는 학습 후 기하급수적으로 감소하고 0%에 가까워야 합니다.
- 입력 해상도: 기본 이미지 해상도는 224x224 픽셀입니다. 예를 들어 448x448 또는 896x896 픽셀의 더 높은 이미지 해상도(매개 변수: `rf_inputResoluton`)를 사용하면 종종 정확도는 향상되지만 DNN 구체화 속도는 떨어집니다. **더 높은 이미지 해상도를 사용하는 경우 비용이 거의 추가되지 않으며 정확도도 거의 항상 향상됩니다**.
- DNN 과잉 맞춤: DNN 구체화 중 학습 및 테스트 정확도 간의 큰 차이(2부의 첫 번째 그림)를 방지합니다. 이 차이를 줄이려면 `rf_dropoutRate` 드롭아웃 속도를 0.5 이상으로 사용하고 `rf_l2RegWeight`regularizer(정규화기) 가중치를 늘립니다. DNN 입력 이미지 해상도가 높을 경우에 높은 드롭아웃 속도를 사용하면 특히 유용할 수 있습니다.
- `rf_pretrainedModelFilename`을 `ResNet_18.model`에서 `ResNet_34.model` 또는 `ResNet_50.model`로 변경하여 더 깊은 DNN을 사용해 봅니다. Resnet-50 모델은 더 깊을 뿐만 아니라 끝에서 두 번째 계층의 출력의 크기가 2,048개 부동 소수점 수입니다(ResNet-18 및 ResNet-34 모델의 경우 512개 부동 소수점 수). 이 증가된 차원은 SVM 분류자를 학습할 때 특히 유용할 수 있습니다.

## <a name="part-3---custom-dataset"></a>3부 - 사용자 지정 데이터 집합

1부와 2부에서 제공된 상반신 의류 질감 이미지를 사용하여 이미지 분류 모델을 학습하고 평가했습니다. 이제 사용자 지정된 사용자 제공 데이터 집합을 사용하는 방법을 보여 줍니다. 또는 이 집합을 사용할 수 없는 경우 Bing Image Search를 사용하여 이러한 데이터 집합을 생성하고 주석을 추가하는 방법을 사용할 수 있습니다.

### <a name="using-a-custom-dataset"></a>사용자 지정 데이터 집합 사용

먼저 의류 질감 데이터의 폴더 구조를 살펴보겠습니다. 서로 다른 특성에 대한 모든 이미지가 *DATA_DIR/images/fashionTexture/* 의 *dotted*, *leopard 및 *striped* 하위 폴더 각각에 있는 상태에 유의하세요. 또한 이미지 폴더 이름이 `PARAMETERS.py` 파일에서 발생하는 방법에도 유의하세요.
```python
datasetName = "fashionTexture"
```

사용자 지정 데이터 집합을 사용하는 것은 모든 이미지가 해당 특성에 따라 하위 폴더에 있는 이 폴더 구조를 재현하고, 이러한 하위 폴더를 새로운 *DATA_DIR/images/newDataSetName/* 사용자 지정 디렉터리에 복사하는 것처럼 간단합니다. 필요한 유일한 코드 변경은 `datasetName` 변수를 *newDataSetName*으로 설정하는 것입니다. 그런 다음 1-5 스크립트를 순서대로 실행하고, 모든 중간 파일을 *DATA_DIR/proc/newDataSetName/* 에 기록합니다. 다른 코드 변경은 필요하지 않습니다.

각 이미지를 정확하게 하나의 특성에 할당해야 합니다. 예를 들어 '표범 무늬' 이미지가 '동물'에 속하기 때문에 '동물' 및 '표범'에 대한 특성을 사용하는 것은 올바르지 않습니다. 또한 모호하고 이에 따라 주석을 달기 어려운 이미지는 제거하는 것이 가장 좋습니다.



### <a name="image-scraping-and-annotation"></a>이미지 스크랩 및 주석

학습 및 테스트를 위해 충분히 많은 수의 주석 처리된 이미지를 수집하는 것은 어려울 수 있습니다. 이 문제를 해결하는 한 가지 방법은 인터넷에서 이미지를 스크랩하는 것입니다. 예를 들어 *스트라이프 티셔츠* 쿼리에 대한 아래의 Bing Image Search 결과를 참조하세요. 예상한 대로 대부분의 이미지는 실제로 스트라이프 티셔츠입니다. 몇 가지 부정확하거나 모호한 이미지(예: 1열, 1행 또는 3열 2행)를 쉽게 식별하고 제거할 수 있습니다.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

크고 다양한 데이터 집합을 생성하려면 여러 쿼리를 사용해야 합니다. 예를 들어 7\*3 = 21개의 쿼리는 {블라우스, 후드, 풀오버, 스웨터, 셔츠, 티셔츠, 조끼} 의류 품목 및 {줄 무늬, 점 무늬, 표범 무늬} 특성의 모든 조합을 사용하여 자동으로 합성할 수 있습니다. 쿼리당 상위 50개의 이미지를 다운로드하는 경우 최대 21 * 50 = 1,050개 이미지가 됩니다.

Bing Image Search에서 이미지를 수동으로 다운로드하는 대신 [Cognitive Services Bing Image Search API](https://www.microsoft.com/cognitive-services/bing-image-search-api)를 사용하면, 쿼리 문자열에 지정된 일단의 이미지 URL을 반환하는 것이 훨씬 쉽습니다.

다운로드한 이미지 중 일부는 정확하거나 거의 중복된 이미지입니다(예: 이미지 해상도 또는 jpg 아티팩트에 따라 다를 수 있음). 이러한 중복은 분할되는 학습 및 테스트 집합에 동일한 이미지가 포함되지 않도록 제거해야 합니다. 해싱 기반 접근 방식을 사용하여 중복된 이미지를 제거할 수 있습니다. 이 방법은 두 단계로 작동합니다. 즉 (i) 먼저 모든 이미지에 대해 해시 문자열이 계산됩니다. (ii) 다음으로 이미지에 대한 두 번째 통과에서 아직 표시되지 않은 해시 문자열이 있는 이미지만 유지됩니다. 다른 모든 이미지는 무시됩니다. `imagehash` Python 라이브러리에서 `dhash` 방법을 찾을 수 있고 이 [블로그](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html)에서 설명하는 `hash_size` 매개 변수를 16으로 설정하여 수행합니다. 실제로 중복된 이미지 대부분을 제거할 수 있다면 중복되지 않은 이미지 일부만 제거하면 됩니다.





## <a name="conclusion"></a>결론

이 예제의 주요 특징은 다음과 같습니다.
- 이미지 분류 모델을 학습, 평가 및 배포하는 코드입니다.
- 데모 이미지가 제공되지만, 사용자 고유의 이미지 데이터 집합을 사용하도록 쉽게 적용할 수 있습니다(한 줄 변경).
- 학습 전송에 따라 한 높은 정확도 모델을 학습하도록 구현된 최첨단 전문가 기능입니다.
- Azure Machine Learning Workbench 및 Jupyter Notebook을 사용하는 대화형 모델 개발입니다.


## <a name="references"></a>참조

[1] Alex Krizhevsky, Ilya Sutskever 및 Geoffrey E. Hinton 공동 발표, [_나선형 심층 신경망을 이용한 ImageNet 분류_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren 및 Jian Sun 공동 발표, [_이미지 인식에 대한 심층 잔여 학습_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf), CVPR 2016
