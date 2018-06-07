---
title: Azure Machine Learning Workbench를 사용하여 Q & A 일치 | Microsoft Docs
description: 다양하고 효과적인 기계 학습 방법을 사용하여 개방형 쿼리를 기존 FAQ 질문 /답변 쌍과 일치시키는 방법입니다.
services: machine-learning
documentationcenter: ''
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 081ca444aee5cd3d426ee569a98bf54c7923af79
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench를 사용하여 Q & A 일치
개방형 질문에 대한 대답은 어려우며 종종 SME(실무 전문가)의 수동 작업이 필요합니다. 내부 SME에 대한 요구를 줄이기 위해 회사에서는 종종 사용자를 지원하는 수단으로 FAQ(질문과 대답) 목록을 만듭니다. 이 예제에서는 개방형 쿼리를 기존 FAQ 질문/답변 쌍과 일치시키는 다양하고 효과적인 기계 학습 방법을 보여 줍니다. 이 예제는 Azure Machine Learning Workbench를 사용하여 이러한 솔루션을 빌드하는 간편한 개발 프로세스를 보여 줍니다. 

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>개요

이 예제는 [Stack Overflow](https://stackoverflow.com/) 등의 웹 사이트에 있는 질문 및 답변 쌍 또는 FAQ(질문과 대답) 목록에서 일반적으로 제공된 것처럼 사용자 질문을 기존 Q&A(질문 및 답변) 쌍으로 매핑하는 문제를 해결합니다. 질문과 가장 유사한 대답을 찾는 등 여러 가지 방법으로 질문을 정답과 일치시킬 수 있습니다. 그러나 이 예제에서는 FAQ의 각 대답이 의미적으로 동일한 여러 질문에 대답할 수 있다고 가정하여 개방형 질문을 이전에 질문된 내용에 연결합니다.

이 솔루션을 제공하는 데 필요한 주요 단계는 다음과 같습니다.

1. 텍스트 데이터를 정리하고 처리합니다.
2. 여러 단어 시퀀스이며, 독립적으로 처리할 때보다 순차적으로 볼 때 더 많은 정보를 제공하는 정보 문구를 배웁니다.
3. 텍스트 데이터에서 기능을 추출합니다.
4. 텍스트 분류 모델을 학습하고 모델 성능을 평가합니다.


## <a name="prerequisites"></a>필수 조건

이 예제를 실행하기 위한 필수 조건은 다음과 같습니다.

1. [Azure 계정](https://azure.microsoft.com/free/)(평가판 사용 가능)
2. [빠른 시작 설치 가이드](../service/quickstart-installation.md)에 따라 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)의 설치 복사본으로 프로그램을 설치하고 작업 공간을 만듭니다.
3. 이 예제는 모든 계산 컨텍스트에서 실행될 수 있습니다. 그러나 적어도 16GB의 메모리와 5GB의 디스크 공간이 있는 멀티 코어 컴퓨터에서 실행하는 것이 좋습니다.

## <a name="create-a-new-workbench-project"></a>새 Workbench 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 기호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **프로젝트 템플릿 검색** 검색 상자에 "Q & A 일치"를 입력하고 템플릿을 선택합니다.
5.  **만들기**

## <a name="data-description"></a>데이터 설명

이 예제에서 사용된 데이터 집합은 [archive.org](https://archive.org/details/stackexchange)에 저장된 스택 교환 데이터 덤프입니다. 이 데이터는 [스택 교환 네트워크](https://stackexchange.com/)에서 사용자가 제공한 모든 콘텐츠의 익명 처리된 덤프입니다. 네트워크의 각 사이트는 bzip2 압축을 사용하여 7-zip을 통해 압축된 XML 파일로 구성되는 별도 아카이브로 형식이 지정됩니다. 각 사이트 보관 파일에는 Posts, Users, Votes, Comments, PostHistory 및 PostLinks가 포함됩니다. 

### <a name="data-source"></a>데이터 원본

이 예제에서는 Stack Overflow 사이트의 [게시물 데이터(10GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) 및 [PostLinks 데이터(515MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z)를 사용합니다. 전체 스키마 정보는 [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt)를 참조하세요. 

게시물 데이터의 `PostTypeId` 필드는 게시물이 `Question`인지 또는 `Answer`인지를 나타냅니다. PostLinks 데이터의 `PostLinkTypeId` 필드는 두 개의 게시물이 연결되었는지 또는 중복되었는지 여부를 나타냅니다. 질문 게시물에는 일반적으로 다른 유사/중복 질문과 함께 질문을 분류하는 키워드인 일부 태그가 포함됩니다. `javascript`, `java`, `c#`, `php` 등과 같이 높은 빈도를 갖는 태그가 많으며 많은 수의 질문 게시물로 구성됩니다. 이 예제에서는 `javascript` 태그가 있는 질문 및 답변 쌍의 하위 집합이 추출됩니다.

또한 질문 게시물은 여러 개의 답변 게시물 또는 중복 질문 게시물과 관련될 수 있습니다. 이 두 데이터 집합에서 FAQ 목록을 작성하려면 일부 데이터 수집 기준을 고려해야 합니다. 이 예제에 포함되지 않은 SQL 스크립트를 사용하여 세 개의 컴파일된 데이터 집합이 선택됩니다. 결과 데이터 설명은 다음과 같습니다.

- `Original Questions (Q)`: 질문 게시물을 Stack Overflow 사이트에서 질문하고 답변합니다.
- `Duplications (D)`: 질문 게시물이 원래 질문인 기존 질문(`PostLinkTypeId = 3`)과 중복됩니다. 중복은 원래 질문에 제공된 대답이 새로운 중복 질문에 대한 대답도 된다는 의미에서 원래 질문과 의미상 동등한 것으로 간주됩니다.
- `Answers (A)`: 원래의 각 질문과 해당 중복 질문은 둘 이상의 답변 게시물에 링크될 수 있습니다. 이 예제에서는 원래 작성자가 수락하거나(`AcceptedAnswerId`로 필터링됨) 또는 원래 질문에서 가장 높은 점수(`Score`로 순위가 매겨짐)인 답변 게시물만 선택합니다. 

이 세 가지 데이터 집합의 조합은 다음 데이터 다이어그램에 나와 있는 것처럼 답변(A)이 원래 질문(Q) 하나와 여러 중복 질문(D)에 매핑되는 질문 및 답변 쌍을 만듭니다.

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>데이터 구조

세 가지 데이터 집합의 데이터 스키마 및 직접 다운로드 링크는 다음 표에서 볼 수 있습니다.

| 데이터 집합 | 필드 | type | 설명
| ----------|------------|------------|--------
| [questions](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Id | 문자열 | 고유 질문 ID(기본 키)
|  | AnswerId | 문자열 | 질문당 고유 답변 ID
|  | Text0 | 문자열 | 질문의 제목과 본문을 포함한 원시 텍스트 데이터
|  | CreationDate | 타임 스탬프 | 질문을 받은 시간의 타임스탬프
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Id | 문자열 | 고유 중복 ID(기본 키)
|  | AnswerId | 문자열 | 중복과 연관된 답변 ID
|  | Text0 | 문자열 | 중복의 제목과 본문을 포함한 원시 텍스트 데이터
|  | CreationDate | 타임 스탬프 | 중복 질문을 받은 시간의 타임스탬프
| [answers](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Id | 문자열 | 고유 답변 ID(기본 키)
|  | text0 | 문자열 | 답변의 원시 텍스트 데이터


## <a name="scenario-structure"></a>시나리오 구조

질문과 대답 일치 예제는 세 가지 유형의 파일을 통해 제공됩니다. 첫 번째 유형은 일련의 Jupyter Notebook으로, 전체 워크플로에 대한 단계별 설명을 보여 줍니다. 두 번째 유형은 구문 학습 및 기능 추출을 위한 사용자 지정 Python 모듈을 포함하는 일련의 Python 파일입니다. 이 Python 모듈은 이 예제뿐만 아니라 다른 사용 사례에서도 사용할 수 있습니다. 세 번째 유형은 Azure Machine Learning Workbench를 사용하여 하이퍼 매개 변수를 튜닝하고 모델 성능을 추적하는 Python 파일 집합입니다.

이 예제에 있는 파일은 다음과 같이 구성됩니다.

| 파일 이름 | type | 설명
| ----------|------------|--------
| `Image` | 폴더 | 추가 정보 파일에 대한 이미지를 저장하는 데 사용되는 폴더
| `notebooks` | 폴더 | Jupyter Notebook 폴더
| `modules` | 폴더 | Python 모듈 폴더
| `scripts` | 폴더 | Python 파일 폴더
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter 노트북 | 샘플 데이터에 액세스하고 텍스트를 전처리하고 교육 및 테스트 데이터 집합 준비
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter 노트북 | 정보 문구를 배우고 BOW(Bag-of-Words) 표현으로 텍스트 토큰화
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter 노트북 | 기능 추출, 텍스트 분류 모델 학습 및 모델 성능 평가
| `modules/__init__.py` | Python 파일 | Python 패키지 init 파일
| `modules/phrase_learning.py` | Python 파일 | 원시 데이터를 변환하고 정보 문구를 익히는 데 사용되는 Python 모듈
| `modules/feature_extractor.py` | Python 파일 | 모델 학습을 위한 기능을 추출하는 Python 모듈
| `scripts/naive_bayes.py` | Python 파일 | Naive Bayes 모델에서 하이퍼 매개 변수를 튜닝하는 Python
| `scripts/random_forest.py` | Python 파일 | 임의 포리스트 모델에서 하이퍼 매개 변수를 튜닝하는 Python
| `README.md` | Markdown 파일 | 추가 정보 markdown 파일

> [!NOTE]
> 일련의 Notebook은 Python 3.5에서 빌드되었습니다.
> 

### <a name="data-ingestion-and-transformation"></a>데이터 수집 및 변환

세 개의 컴파일된 데이터 집합은 Blob Storage에 저장되며 `Part_1_Data_Preparation.ipynb` Notebook에서 검색됩니다.  

텍스트 분류 모델을 학습하기 전에 질문의 텍스트를 정리하고 전처리하여 코드 조각을 제외합니다. 교육 자료를 통해 감독되지 않은 구문 학습을 적용하여 정보를 제공하는 여러 단어 시퀀스를 학습합니다. 이러한 구문은 텍스트 분류 모델에서 사용되는 다운스트림 BOW(Batch-of-Words) 기능화에서 단일 복합 단어 단위로 표현됩니다.

텍스트 전처리 및 구문 학습에 대한 자세한 단계별 설명은 각각 Notebook `Part_1_Data_Preparation.ipynb` 및 `Part_2_Phrase_Learning.ipynb`에서 찾을 수 있습니다.

### <a name="modeling"></a>모델링

이 예제는 각각의 기존 질문 및 답변 쌍이 고유한 클래스이고 각 질문 및 답변 쌍에 대한 중복 질문의 하위 집합이 교육 자료로 제공되는 텍스트 분류 모델을 학습하여 기존 질문 및 답변 쌍에 대해 새로운 질문을 채점하도록 설계되었습니다. 이 예제에서는 원본 질문과 중복 질문 중 75%가 학습을 위해 유지되며 가장 최근에 게시된 25%의 중복 질문은 평가 데이터로 보류됩니다.

분류 모델은 **Naive Bayes**, **지원 벡터 컴퓨터**, **임의 포리스트**를 포함하여 세 가지 기본 분류자를 집계하기 위해 앙상블 방법을 사용합니다. 각 기본 분류자에서 `AnswerId`는 클래스 레이블로 사용되고 BOW 표현은 기능으로 사용됩니다.

모델 학습 프로세스는 `Part_3_Model_Training_and_Evaluation.ipynb`에 설명되어 있습니다.

### <a name="evaluation"></a>평가

두 가지 다른 평가 메트릭을 사용하여 성능을 평가합니다. 
1. `Average Rank (AR)`: 검색된 질문 및 답변 쌍 목록에서 정답이 있는 평균 위치를 나타냅니다(전체 103개 답변 클래스 집합 중). 
2. `Top 3 Percentage`: 반환된 순위 목록의 상위 3개 항목에서 정답을 검색할 수 있는 테스트 질문의 비율을 나타냅니다. 

평가는 `Part_3_Model_Training_and_Evaluation.ipynb`에 나와 있습니다.


## <a name="conclusion"></a>결론

이 예제에서는 구문 학습 및 텍스트 분류와 같은 잘 알려진 텍스트 분석 기술을 사용하여 강력한 모델을 생성하는 방법을 중점적으로 설명합니다. 또한 Azure Machine Learning Workbench가 대화형 솔루션 개발 및 모델 성능 추적에 어떻게 도움이 되는지 보여 줍니다. 

이 예제의 주요 특징은 다음과 같습니다.

- 구문 학습 및 텍스트 분류 모델을 사용하여 질문과 대답 일치 문제를 효과적으로 해결할 수 있습니다.
- Azure Machine Learning Workbench 및 Jupyter Notebook을 사용하여 대화형 모델 개발을 설명합니다.
- Azure Machine Learning Workbench는 비교 목적으로 평가 메트릭을 기록하여 실행 기록과 학습된 모델을 관리합니다. 이러한 기능을 사용하면 신속하게 하이퍼 매개 변수를 튜닝할 수 있으며 최상의 성능 모델을 식별하는 데 도움이 됩니다.


## <a name="references"></a>참조

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/)(IEEE Transactions on Audio, Speech, and Language Processing, vol. 19, no. 8, pp. 2451-2460, 2011년 11월)
