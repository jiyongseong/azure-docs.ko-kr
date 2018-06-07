---
title: Machine Learning을 사용한 신용 위험에 대 한 예측 솔루션 | Microsoft Docs
description: Azure Machine Learning Studio의 신용 위험 평가에 대한 예측 분석 솔루션을 만드는 방법을 보여주는 자세한 연습.
keywords: 신용 위험, 예측 분석 솔루션, 위험 평가
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/23/2017
ms.openlocfilehash: d8a9776d97f10c64f7c29fa32fb1187bd76af714
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>연습: Azure Machine Learning의 신용 위험 평가에 대한 예측 분석 솔루션 개발

이 연습에서는 Machine Learning Studio에서 예측 분석 솔루션을 개발하는 과정을 자세히 살펴보겠습니다. Machine Learning Studio에서 간단한 모델을 개발한 다음 이 모델에서 새로운 데이터를 사용하여 예측할 수 있는 Azure Machine Learning 웹 서비스로 배포합니다. 

이 연습에서는 이전에 Machine Learning Studio를 사용해 본 경험이 한 번 이상 있으며 기계 학습 개념에 대해 어느 정도 이해하고 있다고 가정합니다. 그러나 어느 쪽이든 전문가는 아니라고 가정합니다.

**Azure Machine Learning Studio**를 사용한 경험이 없는 경우 [Azure Machine Learning Studio에서 첫 번째 데이터 과학 실험 만들기](create-experiment.md) 자습서를 시작하는 것이 좋습니다. 해당 자습서에서는 처음으로 Machine Learning Studio를 안내합니다. 이 자습서는 실험에 모듈을 끌어 놓고 서로 연결하고 실험을 실행하고 결과를 확인하는 방법의 기본 사항을 보여 줍니다. 시작하는 데 도움이 될 수 있는 다른 도구는 Machine Learning Studio의 기능에 대한 개요를 제공하는 다이어그램입니다. [Azure Machine Learning Studio 기능 개요 다이어그램](studio-overview-diagram.md)에서 다운로드하고 인쇄할 수 있습니다.
 
일반적으로 기계 학습 분야를 처음 접하는 경우 도움이 될 수 있는 비디오 시리즈가 있습니다. [초급자를 위한 데이터 과학](data-science-for-beginners-the-5-questions-data-science-answers.md)이라고 하며 일상적인 언어 및 개념을 사용하여 기계 학습에 대한 훌륭한 소개를 제공할 수 있습니다.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>문제

신용대출 지원 시 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.  

신용 위험 평가는 복잡한 문제이지만 이 연습에서는 다소 간소화할 수 있습니다. Microsoft Azure Machine Learning을 사용하여 예측 분석 솔루션을 만들 수 있는 방법의 예로 사용합니다. 이 작업을 수행하기 위해 Azure Machine Learning Studio 및 Machine Learning 웹 서비스를 사용합니다.  

## <a name="the-solution"></a>솔루션

이 자세한 연습에서는 공개적으로 사용 가능한 신용 위험 데이터와 함께 시작하고 개발하고 해당 데이터를 기반으로 예측 모델을 학습합니다. 그런 다음 다른 사용자가 신용 위험 평가를 위해 사용할 수 있도록 웹 서비스로 모델을 배포합니다.

이 신용 위험 평가 솔루션을 만들려면 다음 단계를 따릅니다.  

1. [Machine Learning 작업 영역 만들기](walkthrough-1-create-ml-workspace.md)
2. [기존 데이터 업로드](walkthrough-2-upload-data.md)
3. [실험 만들기](walkthrough-3-create-new-experiment.md)
4. [모델 학습 및 평가](walkthrough-4-train-and-evaluate-models.md)
5. [웹 서비스 배포](walkthrough-5-publish-web-service.md)
6. [웹 서비스 액세스](walkthrough-6-access-web-service.md)

> [!TIP] 
> 이 연습에서 개발한 실험의 작업 복사본은 [Azure AI Gallery](https://gallery.cortanaintelligence.com)에서 찾을 수 있습니다. Go to **[Walkthrough - Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** and click **Open in Studio** to download a copy of the experiment into your Machine Learning Studio workspace.
> 
> 이 연습은 [이진 분류: 신용 위험 예측](http://go.microsoft.com/fwlink/?LinkID=525270) 샘플 실험의 간소화된 버전을 기반으로 하며 [갤러리](http://gallery.cortanaintelligence.com/)에서 사용 가능합니다.
