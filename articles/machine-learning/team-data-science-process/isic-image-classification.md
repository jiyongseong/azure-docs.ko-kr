---
title: AMLPCV(Azure Machine Learning Package for Computer Vision) 및 TDSP(Team Data Science Process)를 사용한 이미지 분류 | Microsoft Docs
description: TDSP(Team Data Science Process) 및 AMLPCV를 사용한 이미지 분류에 대해 설명합니다
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 18/05/2018
ms.author: xibingao
ms.openlocfilehash: cfb49f08a245fc08ba3fe78333e801ea2d358c4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367945"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>AMLPCV(Azure Machine Learning Package for Computer Vision) 및 TDSP(Team Data Science Process)를 사용한 피부암 이미지 분류

## <a name="introduction"></a>소개

이 문서에서는 [AMLPCV(Azure Machine Learning Package for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest))를 사용하여 **이미지 분류** 모델을 학습, 테스트 및 배포하는 방법을 보여 줍니다. 샘플은 [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)의 TDSP 구조와 템플릿을 사용합니다. 전체 샘플은 이 연습에서 제공됩니다. [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/)를 딥 러닝 프레임워크로 사용하므로 [데이터 과학 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU 컴퓨터에서 학습이 수행됩니다. 배포에서는 Azure ML Operationalization CLI를 사용합니다.

Computer Vision 도메인의 많은 응용 프로그램은 이미지 분류 문제로 구분될 수 있습니다. 여기에는 "이미지에 개체가 있습니까?"(개체는 *개*, *자동차* 또는 *배*일 수 있음)와 같은 질문 및 "이 환자의 망막 스캔을 통해 안과 질환에 대해 어떤 종류의 심각도가 표시됩니까?"와 같은 보다 복잡한 질문에 대답하는 모델 작성이 포함됩니다. AMLPCV는 이미지 분류 데이터 처리 및 모델링 파이프라인을 간소화합니다. 

## <a name="link-to-github-repository"></a>GitHub 리포지토리에 연결
샘플에 대한 요약 설명이 여기에서 제공됩니다. [GitHub 사이트](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)에서 더 광범위한 설명서를 찾을 수 있습니다.

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>AMLPCV를 사용한 TDSP(Team Data Science Process) 연습

이 연습에서는 [TDSP(Team Data Science Process)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 수명 주기를 사용합니다.

이 연습에서 다루는 수명 주기 단계는 다음과 같습니다.

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. 데이터 취득](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC 데이터 집합은 이미지 분류 작업에 사용됩니다. ISIC(The International Skin Imaging Collaboration)는 디지털 피부 영상 응용 프로그램의 사용을 용이하게 하여 흑색종 사망을 연구하고 이를 줄이는 데 도움을 주기 위해 설립된 산학 협력 단체입니다. [ISIC Archive](https://isic-archive.com/#images)에는 양성 또는 악성 레이블이 지정된 13,000여 개의 피부 병변 이미지가 포함되어 있습니다. 이미지 샘플은 ISIC Archive에서 다운로드합니다.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. 모델링](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
모델링 단계에서는 다음 하위 단계를 수행합니다. 

<b>2.1 데이터 집합 만들기</b><br>

AMLPCV에서 데이터 집합 개체를 생성하기 위해 로컬 디스크에 이미지의 루트 디렉터리를 제공합니다. 

<b>2.2 이미지 시각화 및 주석</b><br>

데이터 집합 개체에서 이미지를 시각화하고 필요한 경우 레이블 중 일부를 수정합니다.

<b>2.3 이미지 증강</b><br>

[imgaug](https://github.com/aleju/imgaug) 라이브러리에 설명된 변환을 사용하여 데이터 집합 개체를 증강합니다.

<b>2.4 DNN 모델 정의</b><br>

학습 단계에서 사용되는 모델 아키텍처를 정의합니다. AMLPCV에서는 학습별 6가지 심층 신경망 모델이 지원됩니다(AlexNet, Resnet-18, Resnet-34, and Resnet-50, Resnet-101 및 Resnet-152).

<b>2.5 분류자 학습</b><br>

기본 또는 사용자 지정 매개 변수를 사용하여 신경망을 학습합니다.

<b>2.6 평가 및 시각화</b><br>

이 하위 단계에서는 독립적인 테스트 데이터 집합에서 학습된 모델의 성능을 평가할 수 있는 기능을 제공합니다. 평가 메트릭에는 정확도, 정밀도, 재현율 및 ROC 곡선이 포함됩니다.

이러한 하위 단계는 해당 Jupyter Notebook에 자세히 설명되어 있습니다. 또한 학습 속도, 미니 배치 크기 및 드롭아웃 속도와 같은 매개 변수를 설정하여 모델 성능을 향상시키는 방법에 대한 지침을 제공합니다.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. 배포](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

이 단계에서는 모델링 단계에서 생성된 모델을 운용합니다. 연산화 필수 구성 요소 및 설정을 소개하고, 웹 서비스 사용에 대해서도 설명합니다. 이 자습서를 통해 AMLPCV로 딥 러닝 모델을 작성하고 Azure에서 모델을 운용하는 방법을 배울 수 있습니다.

## <a name="next-steps"></a>다음 단계
시작하려면 [AMLPCV(Azure Machine Learning Package for Computer Vision)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) 및 [TDSP(Team Data Science Process)](https://aka.ms/tdsp)에 대한 설명서를 참조하세요.


## <a name="references"></a>참조

* [AMLPCV(Azure Machine Learning Package for Computer Vision)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [데이터 과학 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

