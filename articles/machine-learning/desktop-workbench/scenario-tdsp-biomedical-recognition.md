---
title: 생명 공학 엔터티 인식 - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: Azure Machine Learning Workbench에서 생명 공학 엔터티 인식을 위한 심화 학습을 사용하는 Team Data Science Process 프로젝트 빠른 시작 가이드입니다.
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
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 421af12da6d207a50d12dcbf64aac51aba06b196
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>TDSP(Team Data Science Process) 템플릿을 사용하는 생명 공학 엔터티 인식

엔터티 추출은 정보 추출의 하위 작업입니다([NER(명명된 엔터티 인식)](https://en.wikipedia.org/wiki/Named-entity_recognition)이라고도 하는 엔터티 청크 및 엔터티 식별). 이 실제 시나리오의 목표는 Azure Machine Learning Workbench를 사용하여 구조화되지 않은 텍스트에서 엔터티 추출과 같은 복잡한 NLP(자연어 처리) 작업을 해결하는 방법을 중점적으로 설명하는 것입니다.

1. [Spark Word2Vec 구현](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec)을 사용하여 약 1,800만 개 PubMed 요약의 텍스트 모음에 신경망 단어 삽입 모델을 학습하는 방법을 보여 줍니다.
2. Azure의 GPU DS VM(GPU 기반 Azure 데이터 과학 Virtual Machine)에서 엔터티 추출을 위해 LSTM(장단기 기억) 순환 신경망 모델을 빌드하는 방법을 보여 줍니다.
2. 도메인별 단어 포함 모델이 엔터티 인식 작업에서 일반 단어 포함 모델보다 우수한 성능을 발휘할 수 있음을 보여 줍니다. 
3. Azure Machine Learning Workbench를 사용하여 심화 학습 모델을 학습하고 운영하는 방법을 보여 줍니다.

4. Azure Machine Learning Workbench에서 다음과 같은 기능을 보여 줍니다.

    * [TDSP(Team Data Science Process) 구조와 템플릿](how-to-use-tdsp-in-azure-ml.md)의 인스턴스화
    * 다운로드 및 설치를 포함하여 프로젝트 종속성의 자동화된 관리
    * 다른 계산 환경에서 Python 스크립트 실행
    * Python 스크립트에 대한 실행 기록 추적
    * HDInsight Spark 2.1 클러스터를 사용한 원격 Spark 계산 컨텍스트에서의 작업 실행
    * Azure의 원격 GPU VM에서의 작업 실행
    * ACS(Azure Container Services)에서 웹 서비스로 심화 학습 모델 간편하게 조작

## <a name="use-case-overview"></a>사용 사례 개요
생명 공학 명명된 엔터티 인식은 다음과 같은 복잡한 생명 공학 NLP 작업을 위한 중요 단계입니다. 
* 전자 의료 또는 건강 기록에서 이러한 질병, 마약, 화학 물질 및 증상과 같은 명명된 엔터티의 언급을 추출합니다.
* 약품 검색
* 약품-약품 상호 작용, 약품-질병 관계 및 유전자-단백질 관계 등 서로 다른 엔터티 유형 간의 상호 작용 이해

사용 사례 시나리오는 Medline PubMed 요약과 같이 구조화되지 않은 많은 양의 데이터 모음을 분석하여 단어 포함 모델을 학습하는 방법에 중점을 둡니다. 그런 다음 출력 포함은 신경망 엔터티 추출기를 학습하는 자동 생성 기능으로 간주됩니다.

결과에서는 도메인 특정 단어 포함 기능에 대한 생명 공학적 엔터티 추출 모델 학습이 일반 기능 유형에 대해 학습된 모델보다 우수함을 보여 줍니다. 도메인 특정 모델은 일반 모델의 F1 점수가 0.61인 5274개 엔터티와 비교하여 F1 점수가 0.73인 7012개(9475개 중) 엔터티를 올바르게 검색할 수 있습니다.

다음 그림은 데이터를 처리하고 모델을 학습하는 데 사용된 아키텍처를 보여 줍니다.

![아키텍처](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>데이터 설명

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec 모델 학습 데이터
먼저 [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html)에서 원시 MEDLINE 요약 데이터를 다운로드했습니다. 데이터는 [FTP 서버](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline)에서 XML 파일 형태로 공개적으로 사용할 수 있습니다. 서버에는 892개의 XML 파일이 있으며 각 XML 파일에는 3만 개의 문서 정보가 있습니다. 데이터 수집 단계에 대한 자세한 내용은 프로젝트 구조 섹션에 나와 있습니다. 각 파일에 있는 필드는 다음과 같습니다. 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. LSTM 모델 학습 데이터

신경망 엔터티 추출 모델은 공개적으로 사용 가능한 데이터 집합에 대해 학습 및 평가되었습니다. 이러한 데이터 집합에 대한 자세한 설명을 보려면 다음 소스를 참조하세요.
 * [BioNLP/NLPBA 2004에서의 생명 과학 엔터티 인식 작업](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR 작업 모음](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - 작업 9.1(약품 인식)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Azure 갤러리 GitHub 리포지토리에 연결
다음은 코드와 자세한 설명이 포함된 실제 시나리오의 공개 GitHub 리포지토리에 대한 링크입니다. 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>필수 조건 

* Azure [구독](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. [설치 가이드](../service/quickstart-installation.md)를 참조하세요. 현재 Azure Machine Learning Workbench는 다음 운영 체제에만 설치할 수 있습니다. 
    * Windows 10 또는 Windows Server 2016
    * macOS Sierra(이상)


### <a name="azure-services"></a>Azure 서비스
* 규모 확장 계산을 위한 Linux(HDI 3.6)에서 [HDInsight Spark 클러스터](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) 버전 Spark 2.1 아래에 설명된 MEDLINE 요약의 전체 양을 처리하려면 다음의 최소 구성이 필요합니다. 
    * 헤드 노드: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) 크기
    * 작업자 노드: [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/) 중 4개 이상. 이 작업에서는 D12_V2 크기의 작업자 노드 11개를 사용했습니다.
* 강화 계산을 위한 [NC6 DSVM(데이터 과학 Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro)

### <a name="python-packages"></a>Python 패키지

필요한 모든 종속성은 시나리오 프로젝트 폴더 아래의 aml_config/conda_dependencies.yml 파일에 정의되어 있습니다. 이 파일에 정의된 종속성은 docker, VM 및 HDI 클러스터 대상에 대한 실행을 위해 자동으로 프로비전됩니다. Conda 환경 파일 형식에 대한 자세한 내용은 [여기](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)를 참조하세요.

* [Tensorflow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>AML(Azure Machine Learning) Workbench에 대한 기본 지침
* [개요](../service/overview-what-is-azure-ml.md)
* [설치](../service/quickstart-installation.md)
* [TDSP 사용](how-to-use-tdsp-in-azure-ml.md)
* [파일 읽기 및 쓰기 방법](how-to-read-write-files.md)
* [Jupyter Notebook을 사용하는 방법](how-to-use-jupyter-notebooks.md)
* [GPU를 사용하는 방법](how-to-use-gpu.md)

## <a name="scenario-structure"></a>시나리오 구조
시나리오의 경우 TDSP 프로젝트 구조 및 문서 템플릿(그림 1)을 사용합니다. 이 템플릿은 [TDSP 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)를 따릅니다. 프로젝트는 [여기](./how-to-use-tdsp-in-azure-ml.md)에 제공된 지침에 따라 생성됩니다.


![프로젝트 정보 입력](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

단계별 데이터 과학 워크플로는 다음과 같습니다.

### <a name="1-data-acquisition-and-understanding"></a>1. 데이터 취득 및 이해

[데이터 획득 및 이해](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md)를 참조하세요.

원시 MEDLINE 모음에는 약 1천만 개의 문서에 빈 요약 필드가 있는 약 2천 7백만 개의 요약이 있습니다. Azure HDInsight Spark는 [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)처럼 단일 시스템의 메모리에 로드할 수 없는 빅 데이터를 처리하는 데 사용됩니다. 먼저, 데이터가 Spark 클러스터에 다운로드됩니다. 그런 다음 [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)에서 다음 단계가 실행됩니다. 
* Medline XML 파서를 사용하여 XML 파일을 구문 분석합니다.
* 문장 분할, 토큰화 및 대소문자 정규화를 포함하여 요약 텍스트를 전처리합니다.
* 요약 필드가 비어 있거나 짧은 텍스트가 있는 문서는 제외합니다. 
* 학습 요약에서 단어 어휘를 만듭니다.
* 신경망 모델을 포함하는 단어를 학습합니다. 자세한 내용은 [GitHub 코드 링크](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md)를 참조하여 시작합니다.


XML 파일을 구문 분석한 후의 데이터 형식은 다음과 같습니다. 

![데이터 샘플](./media/scenario-tdsp-biomedical-recognition/datasample.png)

신경망 엔터티 추출 모델은 공개적으로 사용 가능한 데이터 집합에 대해 학습 및 평가되었습니다. 이러한 데이터 집합에 대한 자세한 설명을 보려면 다음 소스를 참조하세요.
 * [BioNLP/NLPBA 2004에서의 생명 과학 엔터티 인식 작업](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR 작업 모음](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - 작업 9.1(약품 인식)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. 모델링

[모델링](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling)을 참조하세요.

모델링은 이전 섹션에서 다운로드한 데이터를 사용하여 고유한 단어 포함 모델을 학습하고 다른 다운스트림 작업에 사용하는 방법을 보여 주는 단계입니다. PubMed 데이터를 사용하지만 포함을 생성하는 파이프라인은 일반적이므로 다른 도메인의 단어 포함을 학습할 때 재사용할 수 있습니다. 포함을 데이터의 정확한 표현으로 사용하려면 많은 양의 데이터에 대해 word2vec를 학습해야 합니다.
단어 포함이 준비되면 학습된 포함을 사용하여 포함 레이어를 초기화하는 심층 신경망 모델을 학습할 수 있습니다. 포함 레이어는 학습 불가능하다고 표시되어 있지만 반드시 그런 것은 아닙니다. 단어 포함 모델의 학습은 감독되지 않으므로 레이블이 없는 텍스트를 활용할 수 있습니다. 그러나 엔터티 인식 모델의 학습은 감독된 학습 작업이며, 그 정확성은 수동으로 주석을 첨부한 데이터의 양과 품질에 따라 달라집니다. 


#### <a name="21-feature-generation"></a>2.1. 기능 생성

[기능 생성](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering)을 참조하세요.

Word2Vec는 레이블이 없는 학습 모음에서 신경망 모델을 학습하는 단어 포함 자율 학습 알고리즘입니다. 의미 정보를 나타내는 모음의 각 단어에 대한 연속 벡터를 생성합니다. 이러한 모델은 숨겨진 단일 레이어가 있는 단순한 신경망입니다. 단어 벡터/포함은 역전파 및 확률 기울기 하강에 의해 학습됩니다. word2vec 모델에는 Skip-Gram과 연속 BOW(자세한 내용은 [여기](https://arxiv.org/pdf/1301.3781.pdf) 확인)가 있습니다. Skip-gram 모델을 지원하는 MLlib의 word2vec 구현을 사용하고 있으므로 여기서는 간단히 설명합니다([링크](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)에서 가져온 이미지). 

![Skip Gram 모델](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

이 모델은 계층적 Softmax 및 음수 샘플링을 사용하여 성능을 최적화합니다. H-SoftMax(계층적 SoftMax)는 이진 트리에서 영감을 얻은 근사값입니다. H-SoftMax는 기본적으로 일반 SoftMax 레이어를 단어가 리프인 계층적 레이어로 대체합니다. 이를 통해 한 단어의 확률 계산을 일련의 확률 계산으로 분해할 수 있으므로 모든 단어에 대해 비싼 정규화를 계산하지 않아도 됩니다. 균형 있는 이진 트리의 깊이는 log2(|V|)이므로(V는 어휘임) 단어의 최종 확률을 구하려면 최대 log2(|V|) 노드를 평가하기만 하면 됩니다. 해당 컨텍스트 c에 철자 w가 지정될 확률은 리프 노드로 이어지는 오른쪽 및 왼쪽 전환 확률의 곱입니다. 자주 사용하는 단어의 표현이 더 짧도록 데이터 집합의 단어 빈도에 따라 Huffman 트리를 만들 수 있습니다. 자세한 내용은 [이 링크](http://sebastianruder.com/word-embeddings-softmax/)를 참조하세요.
이미지는 [여기](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)에서 가져왔습니다.

##### <a name="visualization"></a>시각화

단어 포함이 완료되면 이를 시각화하고 의미상 유사한 단어 간의 관계를 확인하고자 합니다. 

![W2V 유사성](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

포함을 시각화하는 두 가지 다른 방법을 알아보았습니다. 첫 번째는 PCA를 사용하여 고차원 벡터를 2D 벡터 공간에 투영합니다. 이로 인해 정보가 크게 손실되고 시각화가 정확하지 않게 됩니다. 두 번째 방법은 [t-SNE](https://distill.pub/2016/misread-tsne/)와 함께 PCA를 사용하는 것입니다. t-SNE는 2차원 또는 3차원 공간에 고차원 데이터를 포함하는 데 적합한 비선형 차원 감소 기술입니다. 이를 산점도로 시각화할 수 있습니다.  유사한 개체는 근처 점으로 모델링되고 유사하지 않은 개체는 먼 점으로 모델링되는 방식으로 고차원 개체를 2차원 또는 3차원 점으로 모델링합니다. 이 방법은 두 부분으로 작동합니다. 첫째, 유사한 개체가 선택될 확률이 높고 유사하지 않은 점이 선택될 확률이 낮은 방식으로 상위 차원 공간에서 쌍에 대한 확률 분포를 생성합니다. 둘째, 저차원 지도상의 점들에 대해 유사한 확률 분포를 정의하고 지도상에 있는 점들의 위치와 관련하여 두 분포 사이의 KL 확산을 최소화합니다. 저차원에서 점의 위치는 기울기 하강을 사용하여 KL 확산을 최소화함으로써 얻어집니다. 그러나 t-SNE는 항상 신뢰할만한 것은 아닙니다. 구현에 대한 자세한 내용은 [여기](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering)를 참조하세요. 


다음 그림에서 볼 수 있듯이 t-SNE 시각화는 단어 벡터와 잠재적인 클러스터링 패턴을 더 자세히 분리합니다. 


* PCA로 시각화

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* t-SNE로 시각화

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* "암"에 가장 가까운 점(암의 모든 하위 유형)

![암에 가장 가까운 점](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. 신경망 엔터티 추출기 학습

[신경망 엔터티 추출기 학습](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md)을 참조하세요.

피드 정방향 신경망 아키텍처는 각각의 입력 및 출력을 다른 입력 및 출력과 독립적으로 취급한다는 문제가 있습니다. 이 아키텍처는 기계 번역 및 엔터티 추출과 같은 시퀀스 간 레이블 작업을 모델링할 수 없습니다. 순환 신경망 모델은 지금까지 계산된 정보를 다음 노드로 전달하는 방법으로 이 문제를 해결합니다. 이 속성은 다음 그림과 같이 이전에 계산된 정보를 사용할 수 있으므로 네트워크에 메모리가 있는 경우 호출됩니다.

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

바닐라 RNN은 실제로 이전에 보았던 모든 정보를 활용할 수 없기 때문에 [기울기 소실 문제](https://en.wikipedia.org/wiki/Vanishing_gradient_problem)로 어려움을 겪고 있습니다. 예측하기 위해 많은 양의 컨텍스트가 필요할 때만 문제가 분명해집니다. 그러나 LSTM과 같은 모델은 이러한 문제가 발생하지 않으며 사실 장기 종속성을 기억하도록 디자인되었습니다. 단일 신경망을 가진 바닐라 RNN과는 달리, LSTM은 각 세포에 대한 4개의 신경망 사이에서 상호 작용합니다. LSTM 작동 방식에 대한 자세한 설명은 [이 게시물](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)을 참조하세요.

![LSTM 세포](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

LSTM 기반의 고유한 순환 신경망을 만들고 PubMed 데이터에서 약품, 질병 및 증상과 같은 엔터티 유형을 추출하려고 합니다. 첫 번째 단계는 많은 양의 레이블이 있는 데이터를 얻는 것인데 쉽지 않습니다. 대부분의 의료 데이터에는 개인에 대한 중요한 정보가 많이 포함되어 있으므로 공개적으로 사용할 수 없습니다. 공개적으로 사용 가능한 두 개의 서로 다른 데이터 집합 조합을 사용합니다. 첫 번째 데이터 집합은 Semeval 2013-Task 9.1(약품 인식)이고 다른 하나는 BioCreative V CDR 작업입니다. 이 두 데이터 집합을 결합하고 자동으로 레이블을 지정하여 의학 텍스트에서 약품과 질병을 모두 검색하고 단어 포함을 평가할 수 있습니다. 구현 세부 정보는 [GitHub 코드 링크](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation)를 참조하세요.

모든 코드에서 비교를 위해 사용한 모델 아키텍처는 다음과 같습니다. 다른 데이터 집합의 경우 변경되는 매개 변수는 최대 시퀀스 길이(이 경우 613)입니다.

![LSTM 모델](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. 모델 평가
[모델 평가](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md)를 참조하세요.

공유 작업 [BioNLP/NLPBA 2004에서의 생명 과학 엔터티 인식 작업](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)의 평가 스크립트를 사용하여 모델의 정밀도, 회수율 및 F1 점수를 평가합니다. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>도메인 내 단어와 일반 단어 포함 모델 비교

다음은 두 기능 유형, 즉 (1) PubMed 요약에 대해 학습된 단어 포함과 (2) Google 뉴스에서 학습한 단어 포함 유형의 정확성을 비교한 것입니다. 도메인 내의 모델이 일반 모델을 능가한다는 것을 분명히 알 수 있습니다. 따라서 일반적인 단어를 사용하는 대신 특정 단어 포함 모델을 사용하면 훨씬 도움이 됩니다. 

* 작업 1: 약품 및 질병 검색

![모델 비교 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

유사한 방식으로 다른 데이터 집합의 단어 포함을 평가하고 도메인 내 모델이 항상 더 낫다는 것을 확인합니다.

* 작업 2: 단백질, 세포주, 세포 유형, DNA 및 RNA 검출

![모델 비교 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* 작업 3: 화학 물질 및 질병 검색

![모델 비교 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* 작업 4: 약품 검색

![모델 비교 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* 작업 5: 유전자 검색

![모델 비교 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow 및 CNTK 비교
보고된 모든 모델은 Keras와 TensorFlow를 백 엔드로 사용하여 학습합니다. CNTK 백 엔드를 사용하는 Keras는 이 작업이 완료되었을 때 "역방향"을 지원하지 않습니다. 따라서 비교를 위해 CNTK 백 엔드를 사용하는 단방향 LSTM 모델을 학습하고 이를 TensorFlow 백 엔드를 사용하는 단방향 LSTM 모델과 비교했습니다. [여기](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)에서 Keras용 CNTK 2.0을 설치합니다. 

![모델 비교 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

CNTK는 Epoch당 수행한 학습 시간(CNTK의 경우 60초, Tensorflow의 경우 75초)과 검색된 테스트 엔터티의 수 모두에서 Tensorflow만큼 우수한 성능을 보였습니다. 평가를 위해 단방향 레이어를 사용하고 있습니다.


### <a name="3-deployment"></a>3. 배포

[배포](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment)를 참조하세요.

[ACS(Azure Container Service)](https://azure.microsoft.com/services/container-service/)의 클러스터에 웹 서비스를 배포했습니다. 운영 환경에서는 클러스터의 Docker 및 Kubernetes를 프로비전하여 웹 서비스 배포를 관리합니다. 운영 프로세스에 대한 자세한 내용은 [여기](model-management-service-deploy.md )를 참조하세요.


## <a name="conclusion"></a>결론

Spark에서 Word2Vec 알고리즘을 사용하여 단어 포함 모델을 학습한 다음 추출된 포함을 기능으로 사용하여 엔터티 추출을 위한 심층 신경망을 학습하는 방법에 대해 자세히 설명했습니다. 생명 과학 도메인에 학습 파이프라인을 적용했습니다. 그러나 이 파이프라인은 다른 도메인의 사용자 지정 엔터티 형식을 검색하는 데 적용할 만큼 충분히 일반적입니다. 충분한 데이터가 필요하고 여기에 제시된 워크플로를 다른 도메인에 맞게 쉽게 적용할 수 있습니다.

## <a name="references"></a>참조

* Tomas Mikolov, Kai Chen, Greg Corrado 및 Jeffrey Dean. 2013a. Efficient estimation of word representations in vector space. In Proceedings of ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado 및 Jeff Dean. 2013b. Distributed representations of words and phrases and their compositionality. In Proceedings of NIPS, 3111–3119페이지.
* Billy Chiu, Gamal Crichton, Anna Korhonen 및 Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), In Proceedings of the 15th Workshop on Biomedical Natural Language Processing, 166–174페이지.
* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
* [Recurrent Neural Networks](https://www.tensorflow.org/tutorials/recurrent)
* [Problems encountered with Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: lessons learned](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

