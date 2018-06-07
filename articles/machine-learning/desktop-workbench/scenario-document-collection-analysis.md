---
title: 문서 컬렉션 분석 ‑ Azure | Microsoft Docs
description: Azure ML Workbench를 사용한 문구 학습, 토픽 모델링, 토픽 모델 분석과 같은 기법을 포함하는, 여러 문서 컬렉션을 요약하고 분석하는 방법.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: cbb077796d9f14a7f9d1d00268c7b6889ade8375
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="document-collection-analysis"></a>문서 컬렉션 분석

이 시나리오에서는 Azure ML Workbench를 사용한 문구 학습, 토픽 모델링, 토픽 모델 분석과 같은 기법을 포함하는, 여러 문서 컬렉션을 요약하고 분석하는 방법을 보여줍니다. Azure Machine Learning Workbench는 방대한 문서 컬렉션에 대한 손쉬운 크기 조정을 제공하며, 로컬 컴퓨터에서 데이터 과학 Virtual Machines, Spark 클러스터에 이르는 다양한 컴퓨팅 컨텍스트에서 모델을 교육하고 조정할 메커니즘을 제공합니다. Azure Machine Learning Workbench의 Jupyter 노트북을 통해 쉽게 개발할 수 있습니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

이 실제 시나리오에 대한 공용 GitHub 리포지토리에는 이 예제에 필요한 코드 샘플을 포함한 모든 자료가 들어 있습니다.

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>개요

매일 수집되는 많은 양의 데이터(특히 구조화되지 않은 텍스트 데이터)을 두고 이러한 방대한 양의 텍스트를 정리하고, 검색하고, 이해하는 일은 상당히 어려운 일입니다. 이 문서 컬렉션 분석 시나리오는 큰 문서 컬렉션을 분석하고 다운스트림 NLP 작업을 할 수 있도록 하는 효율적이고 자동화된 종단 간 워크플로를 보여 줍니다.

이 시나리오에서 제공하는 주요 요소는:

1. 문서에서 가장 중요한 다중 단어 구 학습.

1. 문서 컬렉션에 제시된 기본 토픽 검색.

1. 문서를 주제별 분포로 나타냄.

1. 토픽 내용에 따라 문서를 정리, 검색, 요약하기 위한 메서드 제공.

이 시나리오에서 제공된 메서드를 통해 토픽 추세 비정상 발견, 문서 컬렉션 요약 및 유사한 문서 검색 등과 같이 주요한 산업 업무를 처리할 수 있습니다. 정부 법률, 뉴스 기사, 제품 사용 후기, 고객 피드백 및 과학적 연구 문서와 같은 다양한 유형의 문서 분석에 적용할 수 있습니다.

이 시나리오에서 사용된 기계 학습 기법/알고리즘은 다음과 같습니다.

1. 텍스트 처리 및 정리

1. 구 학습

1. 항목 모델링

1. 코퍼스 요약

1. 토픽 추세 및 비정상 감지

## <a name="prerequisites"></a>필수 조건

이 예제를 실행하기 위한 필수 조건은 다음과 같습니다.

* [빠른 시작 설치 및 만들기](../service/quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)를 제대로 설치했는지 확인합니다.

* 이 예를 모든 계산 컨텍스트에서 실행할 수 있습니다. 그렇지만 적어도 16GB 메모리와 5GB 디스크 공간이 있는 다중 코어 컴퓨터에서 실행하는 것이 좋습니다 .

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 검색 템플릿** 검색 상자에서 "문서 컬렉션 분석"을 입력하고 템플릿을 선택합니다.
5.  **만들기**

## <a name="data-description"></a>데이터 설명

이 시나리오에 사용된 데이터 집합은 미국 의회가 취한 각 입법 조치에 대한 텍스트 요약 및 관련된 메타 데이터를 포함합니다. 데이터는 미국 의회의 활동을 추적하고 미국인이 국가 입법 과정에 참여하도록 돕는 [GovTrack.us](https://www.govtrack.us/)에서 수집됩니다. 대량 데이터는 이 시나리오에 포함되지 않은 수동 스크립트를 사용하여 [이 링크](https://www.govtrack.us/data/congress/)를 통해 다운로드할 수 있습니다. 데이터를 다운로드하는 방법에 대한 세부 정보는 [GovTrack API 설명서](https://www.govtrack.us/developers/api)에서 찾을 수 있습니다.

### <a name="data-source"></a>데이터 원본

이 시나리오에서 수집된 원시 데이터는 1973년부터 2017년 6월(93기부터115기까지의 국회)까지 미국 의회가 도입한 일련의 입법 조치(제안된 법안 및 결의안)입니다. 수집된 데이터는 JSON 형식이며 입법 조치에 대한 다양한 정보를 포함하고 있습니다. 데이터 필드에 대한 자세한 설명은 [이 GitHub 링크](https://github.com/unitedstates/congress/wiki/bills)를 참조합니다. 이 시나리오에서의 시연 목적으로 데이터 필드의 하위 집합만이 JSON 파일에서 추출되었습니다. 이러한 입법 조치 기록이 들어 있는 미리 컴파일된 TSV 파일 `CongressionalDataAll_Jun_2017.tsv`이 이 시나리오에서 제공됩니다. TSV 파일은 노트북 폴더 아래에 있는 노트북 `1_Preprocess_Text.ipynb`이나 Python 패키지에 있는 노트북을 `preprocessText.py`를 사용해 자동으로 다운로드할 수 있습니다.

### <a name="data-structure"></a>데이터 구조

데이터 파일에 9개의 데이터 필드가 있습니다. 데이터 필드 이름 및 설명은 다음과 같이 나열됩니다.

| 필드 이름 | type | 설명 | 누락된 값 포함 |
|------------|------|-------------|---------------|
| `ID` | 문자열 | 법안/결의안 ID. 이 필드의 형식은 [bill_type][number]-[congress]입니다. 예를 들어 "hconres1 93"은 법안 유형이 "hconres"(상하 양원 동일 결의를 의미함, [이 문서](https://github.com/unitedstates/congress/wiki/bills#basic-information) 참조), 법안 번호는 1, 의회 번호는 93임을 나타냅니다. | 아니오 |
| `Text` | 문자열 | 법안/결의안 내용. | 아니오 |
| `Date` | 문자열 | 처음 제안된 청구서/해결 날짜. 'yyyy-mm-dd' 형식. | 아니오 |
| `SponsorName` | 문자열 | 법안/결의안을 제안한 주요 발기인 이름. | 예 |
| `Type` | 문자열 | 주요 발기인의 직함, 'rep'(하원 의원) 또는 'sen'(상원 의원) 중 하나입니다. | 예 |
| `State` | 문자열 | 주요 발기인의 주. | 예 |
| `District` | 정수  | 발기인의 직함이 하원 의원일 경우 주요 발기인의 지구 번호. | 예 |
| `Party` | 문자열 | 주요 발기인의 당. | 예 |
| `Subjects` | 문자열 | 의회 도서관에서 법안에 점증적으로 추가한 주제어. 주제어는 쉼표로 연결됩니다. 이러한 용어는 의회 도서관 직원에 의해 작성되며 법안의 정보가 처음 게시될 때 일반적으로 제공되지 않습니다. 이러한 용어는 언제든지 추가될 수 있습니다. 따라서 법안의 수명이 다하면 일부 주체는 더 이상 관련이 없을 수도 있습니다. | 예 |

## <a name="scenario-structure"></a>시나리오 구조

문서 컬렉션 분석 예제는 두 가지 유형의 결과물으로 구성됩니다. 첫 번째 형식은 전체 워크플로에 대한 단계별 설명을 보여 주는 일련의 iPython 노트북입니다. 두 번째 형식은 Python 패키지는 물론 해당 패키지를 사용하는 방법을 담은 몇 가지 코드 예제입니다. Python 패키지는 많은 사용 사례를 충족할 만큼 충분히 일반적입니다.

이 예제에 있는 파일은 다음과 같이 구성됩니다.

| 파일 이름 | type | 설명 |
|-----------|------|-------------|
| `aml_config` | 폴더 | Azure Machine Learning Workbench 구성 폴더는 자세한 실험 실행 구성에 대해 [이 설명서](./experimentation-service-configuration-reference.md)를 참조합니다. |
| `Code` | 폴더 | Python 스크립트 및 Python 패키지를 저장하는 데 사용하는 코드 폴더 |
| `Data` | 폴더 | 중간 파일을 저장하는 데 사용되는 데이터 폴더 |
| `notebooks` | 폴더 | Jupyter 노트북 폴더 |
| `Code/documentAnalysis/__init__.py` | Python 파일 | Python 패키지 init 파일 |
| `Code/documentAnalysis/configs.py` | Python 파일 | 미리 정의된 상수를 포함한, 문서 분석 Python 패키지에서 사용하는 구성 파일 |
| `Code/documentAnalysis/preprocessText.py` | Python 파일 | 다운스트림 작업을 위해 데이터를 전처리하는 데 사용하는 Python 파일 |
| `Code/documentAnalysis/phraseLearning.py` | Python 파일 | 데이터에서 구를 학습하고 원시 데이터를 변환하는 데 사용하는 Python 파일 |
| `Code/documentAnalysis/topicModeling.py` | Python 파일 | Latent Dirichlet Allocation (LDA) 토픽 모델 교육에 사용되는 Python 파일 |
| `Code/step1.py` | Python 파일 | 문서 컬렉션 분석의 1 단계: 텍스트 전처리 |
| `Code/step2.py` | Python 파일 | 문서 컬렉션 분석의 2 단계: 구 학습 |
| `Code/step3.py` | Python 파일 | 문서 컬렉션 분석의 3 단계: LDA 토픽 모델을 교육하고 평가합니다. |
| `Code/runme.py` | Python 파일 | 모든 단계를 한 파일에서 실행하는 예입니다. |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | 텍스트를 전처리하고 원시 데이터를 변환합니다. |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | 텍스트 데이터에서 (데이터 변환 후) 구를 학습합니다. |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | LDA 토픽 모델 교육 |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | 교육된 LDA 토픽 모델을 기반으로 문서 컬렉션의 내용을 요약합니다. |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | 문서 컬렉션의 토픽 내용을 분석하고 토픽 정보를 문서 컬렉션과 연결된 다른 메타 데이터와 서로 관련시킵니다. |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | 학습된 토픽 모델의 대화형 시각화 |
| `notebooks/winprocess.py` | Python 파일 | 노트북에서 사용하는 다중 처리에 대한 python 스크립트 |
| `README.md` | Markdown 파일 | 추가 정보 markdown 파일 |

### <a name="data-ingestion-and-transformation"></a>데이터 수집 및 변환

컴파일된 데이터 집합 `CongressionalDataAll_Jun_2017.tsv`은 Blob Storage에 저장되고 노트북 및 Python 스크립트 내에서 모두 액세스 가능합니다. 데이터 수집 및 변환을 위한 두 단계는 텍스트 데이터 전처리와 구 학습입니다.

#### <a name="preprocess-text-data"></a>텍스트 데이터 전처리

전처리 단계는 자연어 처리 기법을 적용하여 원시 텍스트 데이터를 정리하고 준비합니다. 감독되지 않는 구 학습 및 잠재 토픽 모델링에 대한 전신으로 역할합니다. 자세한 단계별 설명은 노트북 `1_Preprocess_Text.ipynb`에서 찾을 수 있습니다. 또한 이 노트북에 해당하는 Python 스크립트 `step1.py`이 있습니다.

이 단계에서는 TSV 데이터 파일을 Azure Blob Storage에서 다운로드하여 Pandas DataFrame으로 가져옵니다. DataFrame의 각 행 요소는 통합된 긴 단일 텍스트 문자열 또는 '문서'입니다. 그런 다음 각 문서는 문장또는 구 등이 될 가능성이 있는 텍스트 덩어리로 분할됩니다. 분할은 구 학습 프로세스가 문구를 학습할 때 문장 교차 또는 구 단어 교차 문자열을 사용하는 것을 금지하도록 디자인되었습니다.

노트북과 Python 패키지 모두에 전처리 단계에 대해 정의된 여러 함수가 있습니다. 이러한 두 줄의 코드를 호출하여 작업의 대부분을 구현할 수 있습니다.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>구 학습

구 학습 단계는 방대한 문서 컬렉션에서 핵심 구를 학습하도록 기본 프레임워크를 구현합니다. 이 방법은 2012 IEEE Workshop on Spoken Language Technology에서 처음 발표된 "[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)"이란 논문에 설명되어 있습니다. 구 학습 단계의 자세한 구현은 iPython Notebook `2_Phrase_Learning.ipynb` 및 Python 스크립트 `step2.py`에 나옵니다.

이 단계는 정리된 텍스트를 입력으로 사용하여 방대한 문서 컬렉션에 있는 가장 눈에 띄는 구를 학습합니다. 구 학습은 n-grams 계산, 구성 단어의 Weighted Pointwise Mutual Information(가중 점별 상호 정보)에 의한 순위 잠재 구, 구를 텍스트로 다시 작성이란 세 가지 작업으로 나눌 수 있는 반복 프로세스입니다. 이러한 세 가지 작업은 지정된 구가 학습될 때까지 여러 번 반복을 거쳐 순차적으로 실행됩니다.

문서 분석 Python 패키지에서 Python 클래스 `PhraseLearner`은 `phraseLearning.py` 파일에 정의됩니다. 다음은 구를 학습하는 데 사용되는 코드 조각입니다.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> 구 학습 단계는 다중 처리와 함께 구현됩니다. 하지만 CPU 코어가 더 많이 쓴다고 해서 실행 시간이 더 빨라지는 것을 의미하지는 **않습니다**. 자사의 테스트에 따르면 다중 처리의 오버 헤드로 인해 코어가 8개 이상일 때 성능은 더 이상 향상되지 않았습니다. 8코어(3.6 GHz) 컴퓨터에서 25,000 구를 학습하는 데 약 2시간 반이 걸렸습니다.
>

### <a name="topic-modeling"></a>항목 모델링

LDA를 사용하는 잠재 토픽 모델 학습은 이 시나리오에서 세 번째 단계입니다. [gensim](https://radimrehurek.com/gensim/) Python 패키지는 이 단계에서 [LDA 토픽 모델](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation)을 학습하는 데 필요합니다. 이 단계에 대한 해당 노트북은 `3_Topic_Model_Training.ipynb`입니다. 또한 문서 분석 패키지를 사용하는 방법에 관해 `step3.py`을 참조할 수 있습니다.

이 단계에서의 기본 작업은 LDA 토픽 모델을 배우고 하이퍼 매개 변수를 조정하는 것입니다. LDA 모델을 교육할 때 조정해야 할 매개 변수가 많지만, 가장 중요한 매개 변수는 토픽의 개수입니다. 토픽을 너무 적게 선택한 경우 문서 컬렉션에 대한 통찰이 없을 수도 있는 반면에 너무 많이 선택할 경우 코퍼스의 "과도 클러스터링"이 일어나 작고 아주 비슷한 토픽이 많이 발생할 수 있습니다. 이 시나리오의 목적은 토픽 수를 조정하는 방법을 보여 주는 것입니다. Azure Machine Learning Workbench는 자유롭게 다른 매개 변수 구성을 사용하여 다른 컴퓨팅 컨텍스트에서 실험을 실행할 수 있습니다.

문서 분석 Python 패키지에는 사용자가 최적의 토픽 수를 파악할 수 있도록 하는 몇 가지 함수가 정의되어 있습니다. 첫 번째 방법은 토픽 모델의 일관성을 평가하는 것입니다. 지원되는 평가 매트릭스는 다음 4개입니다: `u_mass`, `c_v`, `c_uci`, 및 `c_npmi`. 이러한 4 메트릭에 관한 세부 정보는 [이 논문](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf)에서 논의됩니다. 두 번째 방법은 유보 코퍼스에 대한 혼잡도를 평가하기 위한 것입니다.

혼잡도 평가에 있어 'U' 셰이프 곡선은 최적의 토픽 수를 찾아냅니다. 가장 좋은 선택은 엘보우 위치입니다. 다른 난수 초기값으로 여러 번 평가한 후 평균을 구하는 것이 좋습니다. 일관성 평가는 'n' 셰이프이어야 하며, 이는 토픽 수가 늘어남에 따라 일관성이 증가하다가 감소하는 것을 의미합니다. 혼잡도 및 수`c_v` 일관성의 예제 그리기는 다음과 같이 표시됩니다.

![혼잡도](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v 일관성](./media/scenario-document-collection-analysis/c_v_Coherence.png)

이 시나리오에서 200 토픽 후에 혼잡도가 상당히 증가한 반면 200 토픽 후에 일관성의 값도 상당히 감소합니다. 보다 일반적인 토픽과 클러스터된 토픽에 대한 요구와 이러한 그래프를 기반으로 200 토픽 선택이 적합한 옵션이어야 합니다.

하나의 실험 실행에서 하나의 LDA 토픽 모델을 교육할 수 있거나, 단일 실험 실행에서 다른 토픽 수 구성을 사용해 여러 LDA 모델을 교육하고 평가할 수 있습니다. 한 구성에 대해 여러 번 실행한 후 평균 일관성 및/또는 혼잡도 평가를 얻는 것이 좋습니다. 문서 분석 패키지를 사용하는 방법에 대한 세부 정보는 `step3.py` 파일에서 찾을 수 있습니다. 예제 코드 조각은 다음과 같습니다.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> LDA 토픽 모델을 교육하는 실행 시간은 코퍼스의 크기, 하이퍼 매개 변수 구성 및 컴퓨터의 코어 수와 같은 여러 요인에 따라 달라집니다. 여러 CPU 코어를 사용하면 모델이 빠르게 교육합니다. 하지만 동일한 하이퍼 매개 변수 설정을 사용할 경우 더 많은 코어는 교육 중 업데이트 수가 줄어듬을 의미입니다. **수렴된 LDA 모델을 교육시키기 위해 적어도 100번 이상의 업데이트**를 하는 것이 좋습니다. 업데이트 횟수 및 하이퍼 매개 변수 간의 관계에 대해서는 [이 게시물](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4)과 [이 게시물](http://miningthedetails.com/blog/python/lda/GensimLDA/)에서 다뤄집니다. 자사의 테스트에 따르면 16 코어 컴퓨터(2.0 Ghz)에서 `workers=15`, `passes=10`, `chunksize=1000`의 구성을 사용하여 LDA 모델이 200가지 토픽을 교육하는 데 약 3시간이 걸렸습니다.
>

### <a name="topic-summarization-and-analysis"></a>토픽 요약 및 분석

토픽 요약 및 분석은 두 노트북으로 구성되는 한편 문서 분석 패키지에는 해당 함수가 없습니다.

`4_Topic_Model_Summarization.ipynb`에서는 교육된 LDA 토픽 모델을 기반으로 문서의 내용을 요약하는 방법을 보여 줍니다. 요약은 3 단계에서 배운 LDA 토픽 모델에 적용됩니다. 토픽을 순도 측정값을 기록하는 데 사용하여 토픽의 중요성 또는 품질을 측정하는 방법을 보여줍니다. 이 순도 측정은 문서를 지배하는 잠재 토픽이 많은 문서에 약하게 분산되어 있는 잠재 토픽보다 의미 체계적으로 더욱 중요한 듯이 보인다고 가정합니다. 이 개념은 "[Latent Topic Modeling for Audio Corpus Summarization(오디오 코퍼스 요약에 대한 잠재 토픽 모델링)](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)"라는 기사에 소개되었습니다.

Notebook `5_Topic_Model_Analysis.ipynb`은 문서 컬렉션의 토픽 내용을 분석하고 토픽 정보를 문서 컬렉션과 연결된 다른 메타 데이터와 서로 관련시키는 방법을 보여 줍니다. 사용자가 학습된 토픽 및 문서 컬렉션을 더 잘 이해할 수 있도록 돕기 위해 이 노트북에 몇 가지 그리기가 소개됩니다.

Notebook `6_Interactive_Visualization.ipynb`는 학습된 토픽 모델을 대화형으로 시각화하는 방법을 보여 줍니다. 여기에는 4개의 대화형 시각화 작업이 포함됩니다.

## <a name="conclusion"></a>결론

이 실제 시나리오는 잘 알려진 텍스트 분석 기술(이 경우, 구 학습 및 LDA 토픽 모델링)을 사용하여 강력한 모델을 생성하는 방법과 Azure Machine Learning Workbench가 더 높은 규모에서 모델 성능을 추적하고 학습자를 원활하게 실행할 수 있는 방법을 돋보이게 합니다. 자세한 내용:

* 구 학습 및 토픽 모델링을 사용하여 문서의 컬렉션을 처리하고 강력한 모델을 빌드합니다. 문서 컬렉션이 거대할 경우 Azure Machine Learning Workbench는 쉽게 크기를 조정할 수 있습니다. 또한 사용자는 마음껏 Azure Machine Learning Workbench 안에서 쉽게 다른 컴퓨팅 컨텍스트에 대한 실험을 실행할 수 있습니다.

* Azure Machine Learning Workbench는 노트북을 단계별로 실행할 옵션과 전체 실험을 실행할 Python 스크립트를 모두 제공합니다.

* 배우는 데 필요한 최적의 토픽 수를 찾기 위해 Azure Machine Learning Workbench를 사용하여 하이퍼 매개 변수 조정. Azure Machine Learning Workbench를 사용해 더 높은 규모에서 모델 성능을 추적하고 학습자를 원활하게 실행할 수 있습니다.

* Azure Machine Learning Workbench를 통해 실행 기록과 학습된 모델을 관리할 수 있습니다. 또한 데이터 과학자는 빠르게 성능이 가장 좋은 모델을 찾고 그런 모델을 생성하는 데 사용한 스크립트 및 데이터를 찾아낼 수 있습니다.

## <a name="references"></a>참조

* **Timothy J. Hazen, Fred Richardson**, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

* **Timothy J. Hazen**, [_Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 제12회 국제 음성 의사 소통 협회 연례회의. 2011.

* **Michael Roder, Andreas Both, Alexander Hinneburg**, [_주제 일관성 측정의 공간 탐험_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). 웹 검색 및 데이터 마이닝에 관한 제8차 ACM 국제 회의의 진행. ACM, 2015.
