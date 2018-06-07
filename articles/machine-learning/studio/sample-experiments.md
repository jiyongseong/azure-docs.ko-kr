---
title: 기계 학습 예제에서 실험 만들기 - Azure | Microsoft Docs
description: 기계 학습 실험 예제를 사용하여 Azure AI Gallery 및 Microsoft Azure Machine Learning을 통해 새 실험을 만드는 방법에 대해 알아봅니다.
keywords: 기계 학습 예제, 샘플 실험, 기계 학습 샘플, AI 예제
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2018
ms.openlocfilehash: 7cb5c869e4523333ce0651559632070e5a19163c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Azure AI Gallery의 작업 예제에서 기계 학습 실험 만들기

처음부터 기계 학습 실험을 만드는 대신 [Azure AI Gallery](https://gallery.cortanaintelligence.com/)의 예제 실험으로 시작하는 방법에 대해 알아봅니다. 예제를 사용하여 고유의 기계 학습 솔루션을 직접 빌드할 수 있습니다.

갤러리에는 Machine Learning 커뮤니티에서 공유하는 예제뿐 아니라 Microsoft Azure Machine Learning 팀의 예제 실험도 있습니다. 실험에 대한 질문을 하거나 의견을 게시할 수도 있습니다.

갤러리를 사용하는 방법에 대해 알아보려면 [초보자를 위한 데이터 과학](data-science-for-beginners-the-5-questions-data-science-answers.md) 시리즈의 3분짜리 비디오 [데이터 과학을 수행하기 위해 다른 사람의 작품 복사](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)를 참조하세요.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Azure AI Gallery에서 복사할 실험 찾기
사용할 수 있는 실험을 보려면 [갤러리](https://gallery.cortanaintelligence.com/)로 이동하여 페이지의 위쪽에 있는 **실험**을 클릭합니다.

### <a name="find-the-newest-or-most-popular-experiments"></a>최신 또는 가장 인기 있는 실험 찾기
이 페이지에서 **최근에 추가된** 실험을 확인하고 아래로 스크롤하여 **인기 항목** 또는 최신 **인기 있는 Microsoft 실험**을 볼 수 있습니다.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>특정 요구 사항을 충족하는 실험 찾기
모든 실험을 찾아보려면:

1. 페이지 위쪽에 있는 **모두 찾아보기**를 클릭합니다.
2. 왼쪽에 있는 **범주** 섹션의 **구체화 기준**에서 **실험**을 선택하여 갤러리에 있는 모든 실험을 볼 수 있습니다.
3. 두 가지 방법으로 요구 사항을 충족하는 실험을 찾을 수 있습니다.
   * **왼쪽에 필터를 선택합니다.** 예를 들어 PCA 기반 변칙 검색 알고리즘을 사용하는 실험을 찾아보려면 **범주** 아래에서 **실험**을 클릭합니다. 그런 다음 **Algorithms Used**(사용 알고리즘) 아래에서 **모두 표시**를 클릭하고 대화 상자에서 **PCA 기반 변칙 검색**을 선택합니다. 이 필터를 찾기 위해 스크롤해야 할 수도 있습니다.<br></br>
     ![필터 선택](./media/sample-experiments/choose-an-algorithm.png)
   * **검색 상자를 사용합니다.** 예를 들어 제공하는 두 개의 클래스 지원 벡터 컴퓨터 알고리즘을 사용하는 숫자 인식과 관련된 Microsoft의 실험을 찾으려면 검색 상자에 "숫자 인식"을 입력합니다. 그런 다음 **실험**, **Microsoft 콘텐츠만** 및 **2클래스 Support Vector Machine**을 선택합니다.<br></br>
     ![검색 상자를 사용합니다](./media/sample-experiments/search-for-experiments.png).
4. 실험을 클릭하여 자세히 알아봅니다.
5. 실험을 실행 및/또는 수정하려면 실험 페이지에서 **Studio에서 열기**를 클릭합니다. <br></br>

    ![예제 실험](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > 처음으로 Machine Learning Studio에서 실험을 여는 경우 Azure 구독을 무료로 사용해보거나 구입할 수 있습니다. [Machine Learning Studio 무료 평가판과 유료 서비스 알아보기](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>예제를 템플릿으로 사용하여 새 실험 만들기
갤러리 예제를 템플릿으로 사용하여 Machine Learning Studio에서 새 실험을 만들 수도 있습니다.

1. Microsoft 계정 자격 증명을 사용하여 [Studio](https://studio.azureml.net)에 로그인한 다음 **새로 만들기**를 클릭하여 새 실험을 만듭니다.
2. 예제 콘텐츠를 찾아서 하나를 클릭합니다.

예제 실험을 템플릿으로 사용하여 Machine Learning Studio 작업 영역에 새로운 실험이 생성됩니다.

## <a name="next-steps"></a>다음 단계
* [다양한 소스에서 데이터 가져오기](import-data.md)
* [Machine Learning의 R 언어용 빠른 시작 자습서](r-quickstart.md)
* [Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)
