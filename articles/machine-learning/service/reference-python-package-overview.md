---
title: Azure Machine Learning용 Python 패키지
description: Azure Machine Learning 사용자가 사용할 수 있는 Python 패키지에 대해 알아봅니다.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7ff80a812ebf704315524ffb5ea518704e472429
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="python-packages-for-azure-machine-learning"></a>Azure Machine Learning용 Python 패키지

Azure Machine Learning용으로 Microsoft에서 제공하는 전용 Python 패키지에 대해 알아봅니다. 이러한 라이브러리와 기능은 다른 오픈 소스 또는 타사 패키지와 함께 사용할 수 있지만 전용 패키지를 사용하려면 인터프리터를 제공하는 컴퓨터나 서비스에 Python 코드를 실행해야 합니다.

Azure Machine Learning 패키지는 **Azure Machine Learning용 Python pip 설치 가능 확장** 기능입니다. 이를 통해 데이터 과학자와 AI 개발자는 고도로 정확한 기계 학습 및 딥 러닝 모델을 신속하게 구축하여 다양한 도메인에 배포할 수 있습니다.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Computer Vision을 위한 Azure ML 패키지

Computer Vision을 위한 Azure ML 패키지를 사용하면 이미지 분류, 개체 감지 및 이미지 유사성을 위한 딥 러닝 모델을 구축하고, 미세 조정하고 배포할 수 있습니다.

이 패키지에 대해 다음과 같은 단계를 수행해 보겠습니다.
1. 앱 패키지를 [다운로드](https://aka.ms/aml-packages/vision/download)합니다.
1. [설치 문서](https://aka.ms/aml-packages/vision)를 읽어봅니다.
1. Jupyter Notebook으로 [컴퓨터 비전 모델을 구축하고 배포](how-to-build-deploy-image-classification-models.md)합니다.
1. 패키지 [참조 설명서](https://aka.ms/aml-packages/vision)를 살펴봅니다.

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>예측을 위한 Azure ML 패키지

예측을 위한 Azure ML 패키지를 사용하면 재정 및 수요 예측 시나리오에 대한 시계열 예측 모델을 생성하고 배포할 수 있습니다.

이 패키지에 대해 다음과 같은 단계를 수행해 보겠습니다.
1. 앱 패키지를 [다운로드](https://aka.ms/aml-packages/forecasting/download)합니다.
1. [설치 문서](https://aka.ms/aml-packages/forecasting)를 읽어봅니다.
1. Jupyter Notebook으로 [예측 모델을 구축하고 배포](how-to-build-deploy-forecast-models.md)합니다.
1. 패키지 [참조 설명서](https://aka.ms/aml-packages/forecasting)를 살펴봅니다.

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Azure ML Package for Text Analytics

Azure ML Package for Text Analytics를 사용하면 텍스트 분류, 사용자 지정 엔터티 추출 및 단어 삽입을 위한 텍스트 딥 러닝 모델을 구축할 수 있습니다.

이 패키지에 대해 다음과 같은 단계를 수행해 보겠습니다.
1. 앱 패키지를 [다운로드](https://aka.ms/aml-packages/text/download)합니다.
1. [설치 문서](https://aka.ms/aml-packages/text)를 읽어봅니다.
1. Jupyter Notebook으로 [텍스트 분류 모델을 구축하고 배포](how-to-build-deploy-text-classification-models.md)합니다.
1. 패키지 [참조 설명서](https://aka.ms/aml-packages/text)를 살펴봅니다.

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai(FPGA 가속)

Azure Machine Learning 하드웨어 가속 패키지를 사용하면 데이터 과학자와 AI 개발자가 ResNet 50의 양자화된 버전으로 이미지를 기능화하고 이 기능을 기반으로 분류자를 학습시킨 다음, Azure의 [FPGA(Field-programmable Gate Array)](concept-accelerate-with-fpgas.md)에 배포하여 대기 시간이 엄청나게 짧은 추론을 수행할 수 있습니다.

이 패키지에 대해 다음과 같은 단계를 수행해 보겠습니다.
1. 앱 패키지를 [다운로드](https://aka.ms/aml-real-time-ai-package)합니다.
1. [설치 문서](reference-fpga-package-overview.md)를 읽어봅니다.
1. [FPGA에 모델을 배포합니다](how-to-deploy-fpga-web-service.md).

