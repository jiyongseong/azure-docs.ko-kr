---
title: Azure Machine Learning에서 심층 학습을 사용하여 감정 분석 | Microsoft Docs
description: Azure ML Workbench에서 심층 학습을 사용하여 감정 분석을 수행하는 방법입니다.
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
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: b4f16ba296e5481e70bd3172eae7b89af67638d3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Azure Machine Learning에서 심층 학습을 사용하여 감정 분석

감정 분석은 자연어 처리 영역에서 잘 알려진 작업입니다. 일련의 텍스트에서 해당 텍스트의 감정을 결정하는 작업입니다. 이 솔루션의 목표는 영화 리뷰에서 감정을 예측하기 위해 심층 학습을 사용하는 것입니다.

솔루션 파일은 https://github.com/Azure/MachineLearningSamples-SentimentAnalysis에 있습니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

공용 GitHub 리포지토리에 대한 다음 링크로 이동하세요.

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>사용 사례 개요

데이터 급증 및 모바일 장치의 확산에 따라 고객이 언제든지 모든 대상에 대한 감정 및 태도를 표현할 기회가 많아졌습니다. 이 의견 또는 "감정"은 주로 리뷰, 채팅, 공유, 좋아요 표시, 트윗 등의 형태로 소셜 채널을 통해 생성됩니다. 제품 및 서비스를 개선하고, 더욱 합리적인 의사 결정을 내리고, 해당 브랜드를 홍보하려는 비즈니스에서 매우 유용할 수 있습니다.

감정 분석을 활용하려면 비즈니스는 실행 가능한 정보를 위해 거대한 분량의 구조화되지 않은 소셜 데이터를 캐낼 수 있는 능력이 있어야 합니다. 이 샘플에서는 AMLWorkbench를 사용하여 영화 리뷰의 감정 분석을 수행하기 위한 심층 학습 모델을 개발합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)

* [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)의 설치 복사본으로 프로그램을 설치하고 작업 공간을 만듭니다.

* 운영화의 경우 로컬로 설치 및 실행되는 Docker 엔진이 있는 것이 가장 좋습니다. 그렇지 않으면 클러스터 옵션을 사용할 수 있습니다. 그러나 ACS(Azure Container Service)를 실행하면 비용이 많이 들 수 있습니다.

* 이 솔루션에서는 Docker 엔진이 로컬에 설치된 Windows 10에서 Azure Machine Learning Workbench를 실행 중이라고 가정합니다. macOS에서도 지침은 거의 같습니다.

## <a name="data-description"></a>데이터 설명

이 샘플에 사용되는 데이터 집합은 소규모의 수작업 데이터 집합이며 [데이터 폴더](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data)에 있습니다.

첫 번째 열에는 영화 리뷰가 포함되고 두 번째 열에는 해당 감정(0 - 부정, 1 - 긍정)이 포함됩니다. 데이터 집합은 단순히 데모 용도로 사용되지만 일반적으로 강력한 감정 점수를 얻으려면 대규모 데이터 집합이 필요합니다. 예를 들어 Keras의 [IMDB 영화 리뷰 감정 분류 문제](https://keras.io/datasets/#datasets )는 25,000개의 IMDB 영화 리뷰 데이터 집합으로 구성되며 감정별로(긍정 또는 부정) 레이블이 지정됩니다. 이 교육에서는 AML Workbench에서 심층 학습을 사용하여 감정 분석을 수행하는 방법을 보여주려고 합니다.

## <a name="scenario-structure"></a>시나리오 구조

폴더 구조는 다음과 같이 정렬됩니다.

1. AML Workbench를 사용하는 감정 분석에 관련된 모든 코드는 루트 폴더에 있습니다.
2. data: 솔루션에 사용된 데이터 집합을 포함합니다.
3. docs: 모든 실습 교육을 포함합니다.

솔루션을 수행하는 실습 교육의 순서는 다음과 같습니다.

| 순서| 파일 이름 | 관련된 파일 |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>결론

결론적으로 이 솔루션은 Azure Machine Learning Workbench에서 심층 학습을 사용하여 감정 분석을 수행하는 방법을 소개합니다. 또한 HDF5 모델을 사용하여 운영합니다.
