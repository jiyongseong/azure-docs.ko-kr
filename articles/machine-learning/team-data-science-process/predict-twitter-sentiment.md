---
title: Azure에서 Team Data Science Process를 사용하여 단어 포함으로 Twitter 감정 예측 | Microsoft Docs
description: 데이터 과학 프로젝트를 실행하는 데 필요한 단계입니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: deguhath
ms.openlocfilehash: fd14096813c0178761df707503ce96b1d56f2e0e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Team Data Science Process를 사용하여 단어 포함으로 Twitter 감정 예측

이 문서에서는 [Azure Machine Learning](../service/index.yml)을 통해 Twitter 감정을 예측하기 위해 _Word2Vec_ 단어 포함 알고리즘과 _SSWE(감성 관련 단어 포함)_ 알고리즘을 사용하여 효과적으로 공동 작업하는 방법을 보여 줍니다. Twitter 감정 극성을 예측하는 방법에 대한 자세한 내용은 GitHub의 [MachineLearningSamples-TwitterSentimentPrediction 리포지토리](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction)를 참조하세요. 데이터 과학 프로젝트에서 효과적인 팀 공동 작업을 촉진하는 핵심은 확정된 데이터 과학 수명 주기를 통해 프로젝트의 구조 및 문서화를 표준화하는 것입니다. [TDSP(Team Data Science Process)](overview.md)는 이러한 유형의 구조화된 [수명 주기](lifecycle.md)를 제공합니다. 

_TDSP 템플릿_을 사용하여 데이터 과학 프로젝트를 만들면 Azure Machine Learning 프로젝트에 대한 표준화된 프레임워크가 제공됩니다. 이전에 TDSP 팀은 [TDSP 프로젝트 구조 및 템플릿에 대한 GitHub 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate)를 릴리스했습니다. 이제 [Azure Machine Learning용 TDSP 템플릿](https://github.com/amlsamples/tdsp)으로 인스턴스화된 Machine Learning 프로젝트를 사용하도록 설정됩니다. 지침은 Azure Machine Learning에서 [TDSP 템플릿으로 TDSP 구조 프로젝트](../desktop-workbench/how-to-use-tdsp-in-azure-ml.md)를 사용하는 방법을 참조하세요. 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter 감정 극성 샘플

이 문서에서는 샘플을 사용하여 Machine Learning 프로젝트를 인스턴스화하고 실행하는 방법을 보여 줍니다. 샘플은 Azure Machine Learning Workbench에서 TDSP 구조와 템플릿을 사용합니다. 전체 샘플은 [이 연습](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md)에서 제공됩니다. 모델링 작업은 트윗의 텍스트를 사용하여 감정 극성(양수 또는 음수)을 예측합니다. 이 문서에서는 연습에서 설명하는 데이터 모델링 작업에 대해 간략히 설명합니다. 이 연습에서 다루는 작업은 다음과 같습니다.

- 사용 사례 개요에서 설명한 예측 문제를 해결하는 기계 학습 모델의 데이터 탐색, 학습 및 배포. [Twitter 감정 데이터](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)가 이러한 작업에 사용됩니다.
- 이 프로젝트에 Azure Machine Learning의 TDSP 템플릿을 사용하여 프로젝트 실행. TDSP 수명 주기가 프로젝트 실행 및 보고에 사용됩니다.
- Azure Container Service의 Azure Machine Learning에서 솔루션 직접 운영

프로젝트에서 강조 표시하는 Azure Machine Learning 기능은 다음과 같습니다.

- TDSP 구조의 인스턴스화 및 사용
- Azure Machine Learning Workbench에서 코드 실행
- Docker 및 Kubernetes를 사용하여 Container Service에서 쉽게 운영화

## <a name="team-data-science-process"></a>Team Data Science Process
이 샘플을 실행하려면 Azure Machine Learning Workbench에서 TDSP 프로젝트 구조와 문서화 템플릿을 사용합니다. 이 샘플에서는 다음 그림과 같이 [TDSP 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)를 구현합니다.

![TDSP 수명 주기](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

TDSP 프로젝트는 다음 그림과 같이 Azure Machine Learning Workbench에서 [이러한 지침](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)에 따라 만들어집니다.

![Azure Machine Learning Workbench에서 TDSP 만들기](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[데이터 취득 및 준비](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
이 샘플의 첫 번째 단계는 sentiment140 데이터 집합을 다운로드하고, 데이터를 학습 및 테스트 데이터 집합으로 분할하는 것입니다. sentiment140 데이터 집합에는 트윗의 실제 콘텐츠(이모티콘 제거됨)가 포함되어 있습니다. 또한 데이터 집합에는 중성 트윗이 제거된 각 트윗의 극성(negative=0, positive=4)도 포함되어 있습니다. 데이터가 분할되면 학습 데이터에는 130만 개 행이 있고, 테스트 데이터에는 32만 개 행이 있습니다.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[모델 개발](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

샘플의 다음 단계는 데이터 모델을 개발하는 것입니다. 모델링 작업은 다음 세 부분으로 구분됩니다.

- 기능 엔지니어링: 여러 단어 포함 알고리즘을 사용하여 모델에 대한 기능을 생성합니다. 
- 모델 만들기: 입력 텍스트의 감정을 예측하기 위해 여러 모델을 학습합니다. 이 모델의 예로 _로지스틱 회귀_와 _그라데이션 부스팅_이 있습니다.
- 모델 평가: 테스트 데이터를 통해 학습된 모델을 평가합니다.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[기능 엔지니어링](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec 및 SSWE 알고리즘을 사용하여 단어 포함을 생성합니다. 


#### <a name="word2vec-algorithm"></a>Word2Vec 알고리즘

Word2Vec 알고리즘은 Skip-Gram 모델에서 사용됩니다. Tomas Mikolov 등이 발표한 논문에서 이 모델에 대해 설명하고 있습니다. "[단어와 구문의 분산된 표현 및 합성성 - 신경 정보 처리 시스템의 발전](https://arxiv.org/abs/1310.4546)" 2013.

Skip-Gram 모델은 단순 신경망입니다. 입력은 원 핫 벡터(one-hot vector)로 인코딩된 대상 단어로, 인접한 단어를 예측하는 데 사용됩니다. **V**가 어휘의 크기이면 출력 계층의 크기는 __C*V__이며, 여기서 C는 컨텍스트 창의 크기입니다. 다음 그림에서는 Skip-Gram 모델에 기반한 아키텍처를 보여 줍니다.

![Skip-Gram 모델](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Word2Vec 알고리즘과 Skip-Gram 모델에 대한 자세한 메커니즘은 이 샘플에서 다루지 않습니다. 자세한 내용은 다음 참조 문서를 참조하세요.

- [참조된 TensorFlow 예제가 있는 02_A_Word2Vec.py 코드](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
- [word2vec은 정확히 어떻게 작동할까요?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [노이즈 대비 추정 및 음수 샘플링에 대한 참고 사항](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>감정 관련 단어 포함 알고리즘
SSWE(감정 관련 단어 포함) 알고리즘은 유사한 문맥과 반대 극성이 있는 단어가 유사한 단어 벡터를 가질 수 있는 Word2Vec 알고리즘의 취약점을 극복하려고 시도합니다. 유사성으로 인해 감정 분석과 같은 작업에 대해 Word2Vec 알고리즘이 정확하게 수행되지 않을 수 있습니다. SSWE 알고리즘은 문장의 극성과 단어의 문맥을 모두 손실 함수에 통합하여 이 취약점을 처리하려고 시도합니다.

이 샘플에서는 다음과 같이 SSWE 알고리즘의 변형을 사용합니다.

- 원래의 _ngram_ 및 손상된 _ngram_이 모두 입력으로 사용됩니다.
- 순위 스타일 힌지 손실 함수는 구문 손실과 의미 체계 손실 모두에 사용됩니다.
- 궁극적인 손실 함수는 구문 손실과 의미 체계 손실 모두의 가중치 조합입니다.
- 간단히 설명하기 위해 의미 체계 크로스 엔트로피(cross entropy)만 손실 함수로 사용됩니다.

샘플에서는 더 간단한 손실 함수를 사용하더라도 SSWE 포함 성능이 Word2Vec 포함보다 더 효율적임을 보여 줍니다. 다음 그림에서는 감정 관련 단어 포함을 생성하는 데 사용되는 합성곱 모델을 보여 줍니다.

![SSWE에서 영감을 얻은 신경망 모델](./media/predict-twitter-sentiment/embedding-model-2.PNG)

학습 프로세스가 완료되면 모델링 단계에서 두 개의 포함 파일이 탭으로 구분된 값(TSV) 형식으로 생성됩니다.

SSWE 알고리즘에 대한 자세한 내용은 Duyu Tang 등이 발표한 논문을 참조하세요. "[Twitter 감정 분류에 대한 감정 관련 단어 포함 학습](http://www.aclweb.org/anthology/P14-1146)" 전산 언어학 협회 (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[모델 만들기](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
단어 벡터가 SSWE 또는 Word2Vec 알고리즘을 사용하여 생성되면 분류 모델이 실제 감정 극성을 예측하도록 학습됩니다. 두 가지 유형의 기능(Word2Vec 및 SSWE)이 그라데이션 부스팅 모델과 로지스틱 회귀 모델의 두 모델에 적용됩니다. 결과적으로 네 가지 모델이 학습되었습니다.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[모델 평가](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
모델이 학습되면 모델을 사용하여 Twitter 텍스트 데이터를 테스트하고 각 모델의 성능을 평가합니다. 이 샘플에서는 다음 네 가지 모델을 평가합니다.

- SSWE 포함을 통한 그라데이션 부스팅
- SSWE 포함을 통한 로지스틱 회귀
- Word2Vec 포함을 통한 그라데이션 부스팅
- Word2Vec 포함을 통한 로지스틱 회귀

네 가지 모델에 대한 성능 비교는 다음 그림과 같습니다.

![ROC(수신기 작동 특성) 모델 비교](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

SSWE 기능이 있는 그라데이션 부스팅 모델은 AUC(곡선 아래 면적) 메트릭을 사용하여 모델을 비교할 때 최상의 성능을 제공합니다.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[배포](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

마지막 단계는 Azure Container Service의 클러스터에 있는 웹 서비스에 학습된 감정 예측 모델을 배포하는 것입니다. 이 샘플에서는 SSWE 포함 알고리즘이 있는 그라데이션 부스팅 모델을 학습된 모델로 사용합니다. 운영화 환경에서는 다음 그림과 같이 클러스터의 Docker 및 Kubernetes를 프로비전하여 웹 서비스 배포를 관리합니다. 

![Kubernetes 대시보드](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

운영화 프로세스에 대한 자세한 내용은 [웹 서비스로 Azure Machine Learning 모델 배포](../desktop-workbench/model-management-service-deploy.md)를 참조하세요.

## <a name="conclusion"></a>결론

이 문서에서는 Word2Vec 및 감정 관련 단어 포함 알고리즘을 사용하여 단어 포함 모델을 학습하는 방법을 알아보았습니다. 추출된 포함이 Twitter 텍스트 데이터에 대한 감정 점수를 예측하기 위해 여러 모델을 학습하는 기능으로 사용되었습니다. 그라데이션 부스팅 모델과 함께 사용된 SSWE 기능은 최상의 성능을 제공했습니다. 그런 다음 Azure Machine Learning Workbench를 사용하여 Container Service에서 이 모델을 실시간 웹 서비스로 배포했습니다.


## <a name="references"></a>참조

* [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Azure Machine Learning에서 TDSP(Team Data Science Process)를 사용하는 방법](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning용 TDSP 프로젝트 템플릿](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../service/index.yml)
* [UCI ML 리포지토리의 미국 소득 데이터 집합](https://archive.ics.uci.edu/ml/datasets/adult)
* [TDSP 템플릿을 사용하여 생명 공학 엔터티 인식](../desktop-workbench/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomas 외 공저, "단어와 구문의 분산된 표현 및 합성성 - 신경 정보 처리 시스템의 발전" 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu 외 공저, "Twitter 감정 분류에 대한 감정 관련 단어 포함 학습" ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
