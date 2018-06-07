---
title: Machine Learning Studio에서 간단한 실험 | Microsoft Docs
description: 이 기계 학습 자습서에서는 쉬운 데이터 과학 실험을 안내합니다. 회귀 알고리즘을 사용하여 자동차의 가격을 예측합니다.
keywords: 실험, 선형 회귀, 기계 학습 알고리즘, 기계 학습 자습서, 예측 모델링 기술, 데이터 과학 실험
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.openlocfilehash: ec49d7e82da58c90e66f65a1a1d303ca9b3824f7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>기계 학습 자습서: Azure Machine Learning Studio에서 첫 번째 데이터 과학 실험 만들기

**Azure Machine Learning Studio**를 사용한 경험이 없는 경우 이 자습서는 도움이 됩니다.

이 자습서에서 기계 학습 실험을 하기 위해 처음 Studio를 사용하는 방법을 살펴봅니다. 이 실험은 제조업체 및 기술 사양과 같은 여러 변수에 따라 자동차 가격을 예측하는 분석 모델을 테스트합니다.

> [!NOTE]
> 이 자습서는 실험에 모듈을 끌어 놓고 서로 연결하고 실험을 실행하고 결과를 확인하는 방법의 기본 사항을 보여줍니다. 기계 학습의 일반적인 주제 또는 Studio에 포함된 100개 이상의 기본 제공 알고리즘 및 데이터 조작 모듈을 선택하고 사용하는 방법은 논의에서 제외됩니다.
>
>기계 학습이 처음이라면 비디오 시리즈 [초보자를 위한 데이터 과학](data-science-for-beginners-the-5-questions-data-science-answers.md)이 시작에 좋은 출발점이 될 수 있습니다. 이 비디오 시리즈는 일상적인 언어 및 개념을 사용하여 기계 학습을 잘 소개하고 있습니다.
>
>기계 학습에 익숙하지만 Machine Learning Studio에 대한 일반 상세 정보와 이러한 정보가 포함된 기계 학습 알고리즘을 찾고 있는 경우 다음과 같은 몇 가지 유용한 자료를 참고하세요.
>
- [Machine Learning Studio란 무엇인가요?](what-is-ml-studio.md) - Studio의 차원 높은 개요입니다.
- [알고리즘 예제가 포함된 기계 학습 기본 사항](basics-infographic-with-algorithm-examples.md) - 이 인포그래픽은 Machine Learning Studio에 포함된 기계 학습 알고리즘의 다양한 유형에 대해 자세히 알아보고자 할 경우에 유용합니다.
- [Machine Learning 가이드](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) -이 가이드에서는 위의 인포그래픽과 유사한 정보를 대화형 형식으로 다룹니다.
- [기계 학습 알고리즘 치트 시트](algorithm-cheat-sheet.md) 및 [Microsoft Azure Machine Learning용 알고리즘 선택 방법](algorithm-choice.md) - 다운로드 가능한 이 포스터 및 관련 문서는 Studio 알고리즘을 깊이 있게 설명합니다.
- [Machine Learning Studio: 알고리즘 및 모듈 도움말](https://msdn.microsoft.com/library/azure/dn905974.aspx) -기계 학습 알고리즘을 포함하여 모든 Studio 모듈에 대 한 완전한 참조입니다.

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Machine Learning Studio는 도움이 되나요?

Machine Learning Studio를 사용하면 예측 모델링 기술로 프로그램한 끌어서 놓기 모듈을 사용하여 실험을 쉽게 설정할 수 있습니다.

대화형 시각적 작업 영역을 사용하여 ***데이터 집합*** 및 분석 ***모듈***을 대화형 캔버스로 끌어서 놓습니다. Machine Learning Studio에서 실행할***실험***을 구성하려면 이들을 함께 연결합니다.
***모델을 만들고*** ***학습하고*** ***점수를 매기고 테스트***합니다.

실험을 편집하고 원하는 결과가 나올 때까지 실행하여 모델 디자인을 반복할 수 있습니다. 모델이 준비되면 ***웹 서비스***로 게시하여 다른 사람이 새 데이터를 전송하고 예측 결과를 얻을 수 있습니다.

## <a name="open-machine-learning-studio"></a>Machine Learning Studio 열기

Studio을 시작하려면 [https://studio.azureml.net](https://studio.azureml.net)으로 이동합니다. 이전에 Machine Learning Studio에 등록했다면 **로그인**을 클릭합니다. 그렇지 않은 경우 **여기서 등록**을 클릭하고 무료와 유료 옵션 중 하나를 선택합니다.

![Machine Learning Studio에 로그인][sign-in-to-studio]
<br/>
***Machine Learning Studio에 로그인***

## <a name="five-steps-to-create-an-experiment"></a>실험 만들기 5단계

이 기계 학습 자습서에서 모델을 만들고 학습하고 점수를 매기기 위해 Machine Learning Studio에서 실험을 빌드하기 위한 5가지 기본 단계를 따릅니다.

- **모델 만들기**
    - [1단계: 데이터 가져오기]
    - [2단계: 데이터 준비]
    - [3단계: 기능 정의]
- **모델 학습**
    - [4단계: 학습 알고리즘 선택 및 적용]
- **모델 점수 매기기 및 테스트**
    - [5단계: 새 자동차 가격 예측]

[1단계: 데이터 가져오기]: #step-1-get-data
[2단계: 데이터 준비]: #step-2-prepare-the-data
[3단계: 기능 정의]: #step-3-define-features
[4단계: 학습 알고리즘 선택 및 적용]: #step-4-choose-and-apply-a-learning-algorithm
[5단계: 새 자동차 가격 예측]: #step-5-predict-new-automobile-prices

> [!TIP] 
> 다음 실험의 작업 복사본은 [Azure AI Gallery](https://gallery.cortanaintelligence.com)에서 찾을 수 있습니다. Go to **[Walkthrough - Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** and click **Open in Studio** to download a copy of the experiment into your Machine Learning Studio workspace.


## <a name="step-1-get-data"></a>1단계: 데이터 가져오기

기계 학습을 수행해야 할 첫 번째가 데이터입니다.
Machine Learning Studio에는 사용할 수 있고 다양한 원본에서 데이터를 가져올 수 있는 여러 샘플 데이터 집합이 있습니다. 이 예제에서는 작업 영역에 포함되어 있는 샘플 데이터 집합, 즉 **자동차 가격 데이터(원시)**를 사용합니다.
이 데이터 집합에는 제조업체, 모델, 기술 사양 및 가격과 같은 정보를 포함하여 여러 개별 자동차에 대한 항목이 포함되어 있습니다.

실험에 데이터 집합을 가져오는 방법은 다음과 같습니다.

1. Machine Learning Studio 창의 아래쪽에서 **+새로 만들기**를 클릭하여 새 실험을 만들고 **실험**을 선택한 다음, **빈 실험**을 선택합니다.

2. 실험은 캔버스의 위쪽에서 볼 수 있는 기본 이름이 지정됩니다. 이 텍스트를 선택하고 **자동차 가격 예측**과 같이 의미 있는 것으로 이름을 바꿉니다. 이름은 고유할 필요가 없습니다.

    ![실험 이름 바꾸기][rename-experiment]

2. 실험 캔버스 왼쪽에는 데이터 집합과 모듈의 팔레트가 있습니다. 이 팔레트 맨 위에 있는 검색 상자에 **automobile**을 입력하여 레이블이 **Automobile price data (Raw)**인 데이터 집합을 찾습니다. 실험 캔버스에 데이터 집합을 끌어 놓습니다.

    ![자동차 데이터 집합을 찾아서 실험 캔버스에 끌어 놓기][type-automobile]
    <br/>
    ***자동차 데이터 집합을 찾아서 실험 캔버스에 끌어 놓기***

이 데이터의 모양을 확인하려면 자동차 데이터 집합 아래에서 출력 포트를 두 번 클릭한 다음 **Visualize**를 선택합니다.

![출력 포트를 클릭하고 “시각화” 선택][select-visualize]
<br/>
***출력 포트를 클릭하고 “시각화” 선택***

> [!TIP]
> 데이터 집합 및 모듈에는 쪽의 입력 포트, 아래쪽의 출력을 작은 원으로 나타내는 입력 및 출력 포트가 있습니다.
실험을 통해 데이터 흐름을 만들려면 한 모듈의 출력 포트를 다른 모듈의 입력 포트에 연결합니다.
언제든지 데이터 흐름에서 해당 지점에서 데이터가 어떻게 보이는지 알아보려면 데이터 집합 또는 모듈의 출력 포트를 클릭할 수 있습니다.

이 샘플 데이터 집합에서 자동차의 각 인스턴스가 행으로 나타나고 각 자동차와 연결된 변수는 열로 표시됩니다. 특정 자동차에 대한 변수를 지정하여 맨 오른쪽 열(열 26, "price")에서 가격을 예측하려고 합니다.

![데이터 시각화 창에서 자동차 데이터 보기][visualize-auto-data]
<br/>
***데이터 시각화 창에서 자동차 데이터 보기***

오른쪽 위 모서리에서 "**x**"를 클릭하여 시각화 창을 닫습니다.

## <a name="step-2-prepare-the-data"></a>2단계: 데이터 준비

데이터 집합은 일반적으로 전처리를 거쳐야 분석할 수 있습니다. 예를 들어 여러 행의 열에 누락된 값이 있다는 것을 알 수 있습니다. 모델에서 데이터를 올바르게 분석할 수 있도록 이러한 누락된 값을 정리해야 합니다. 지금은 누락된 값이 있는 행을 모두 제거하겠습니다. 또한 **normalized-losses** 열에 값이 누락된 비율이 크므로 여기서는 해당 열을 모델에서 완전히 제외하겠습니다.

> [!TIP]
> 입력 데이터에서 누락 값을 정리하는 것은 대부분의 모듈을 사용하기 위한 필수 조건입니다.

먼저 **normalized losses** 열을 완전히 제거하는 모듈을 추가한 다음 누락된 데이터가 있는 행을 제거하는 다른 모듈을 추가합니다.

1. 모듈 팔레트의 맨 위에 있는 검색 상자에 **열 선택**을 입력하여 [데이터 집합에서 열 선택][select-columns] 모듈을 찾아 실험 캔버스에 끌어 놓습니다. 이 모듈을 사용하면 모델에서 포함하거나 제외할 데이터 열을 선택할 수 있습니다.

2. **자동차 가격 데이터(원시)** 데이터 집합의 입력 포트를 [데이터 집합의 열 선택][select-columns] 모듈의 입력 포트에 연결합니다.

    ![“데이터 집합에서 열 선택” 모듈을 실험 캔버스에 추가 및 연결][type-select-columns]
    <br/>
    ***“데이터 집합에서 열 선택” 모듈을 실험 캔버스에 추가 및 연결***

3. [데이터 집합에서 열 선택][select-columns] 모듈을 클릭하고 **속성** 창에서 **열 선택기 시작**을 클릭합니다.

    - 왼쪽에서 **규칙으로**
    - **다음으로 시작**에서 **모든 열**을 클릭합니다. 그러면 모든 열을 전달하는 [데이터 집합에서 열 선택][select-columns]으로 리디렉션됩니다(제외할 열은 예외임).
    - 다음 드롭다운에서 **제외** 및 **열 이름**을 선택한 후 텍스트 상자 내부를 클릭합니다. 열 목록이 표시됩니다. **normalized-losses**를 선택하면 텍스트 상자에 추가됩니다.
    - 확인 표시(확인) 단추를 클릭하여 열 선택기(오른쪽 아래)를 닫습니다.

    ![열 선택기 시작및 "normalized-losses" 열 제외][launch-column-selector]
    <br/>
    ***열 선택기 시작 및 "normalized-losses" 열 제외***

    이제 **데이터 집합에서 열 선택**의 속성 창은 **nomalized-losses**를 제외한 데이터 집합의 모든 열을 통과한다는 것을 나타냅니다.

    ![속성 창은 “normalized-losses” 열이 제외되었음을 표시합니다][showing-excluded-column]
    <br/>
    ***속성 창은 “normalized-losses” 열이 제외되었음을 표시합니다***

    > [!TIP]
    모듈을 두 번 클릭하고 텍스트를 입력하여 모듈에 주석을 추가할 수 있습니다. 그러면 모듈이 실험에서 수행하는 내용을 한눈에 볼 수 있습니다. 이 경우에 [데이터 집합에서 열 선택][select-columns] 모듈을 두 번 클릭하고 "정규화된 손실 제외"라는 주석을 입력합니다.

    ![모듈을 두 번 클릭하여 주석 추가][add-comment]
    <br/>
    ***모듈을 두 번 클릭하여 주석 추가***

3. [누락된 데이터 정리][clean-missing-data] 모듈을 실험 캔버스로 끌어 놓고 [데이터 집합에서 열 선택][select-columns] 모듈과 연결합니다. **속성** 창에서 **정리 모드** 아래 **전체 열 제거**를 선택합니다. 그러면 [누락된 데이터 정리][clean-missing-data]로 리디렉션되어 모든 누락 값이 있는 행을 제거하여 데이터를 정리합니다. 모듈을 두 번 클릭하고 주석 "Remove missing value rows"를 입력합니다.

    ![“누락된 데이터 정리” 모듈의 경우 정리 모드를 “전체 행 제거”로 설정][set-remove-entire-row]
    <br/>
    ***“누락된 데이터 정리” 모듈의 경우 정리 모드를 “전체 행 제거”로 설정***

4. 페이지 아래쪽의 **실행**을 클릭하여 실험을 실행합니다.

    실험이 실행 완료되면 모든 모듈에 성공적으로 완료되었음을 나타내는 녹색 확인 표시가 표시됩니다. 오른쪽 위 모서리에서 **Finished running** 상태도 확인됩니다.

![실행한 후 실험은 다음과 같이 표시됨][early-experiment-run]
<br/>
***실행한 후 실험은 다음과 같이 표시됨***

> [!TIP]
> 왜 지금 실험을 실행했을까요? 실험을 실행하여 데이터에 대한 열 정의는 데이터 집합에서 [누락된 데이터 정리][select-columns] 모듈을 통해, 그리고 [누락된 데이터 정리][clean-missing-data] 모듈을 통해 전달됩니다. 즉, [누락된 데이터 정리][clean-missing-data]에 연결하는 모든 모듈은 같은 정보를 갖게 됩니다.

이 시점까지 완료된 모든 실험은 데이터를 정리합니다. 정리된 데이터 집합을 보려면 [누락된 데이터 정리][clean-missing-data] 모듈의 왼쪽 출력 포트를 클릭하고 **시각화**를 선택합니다. **normalized-losses** 열이 더 이상 포함되지 않으며, 누락된 값이 없습니다.

데이터가 정리되었으며, 이제 예측 모델에서 사용할 특성을 지정할 수 있습니다.

## <a name="step-3-define-features"></a>3단계: 기능 정의

기계 학습에서 *기능*은 관심 있는 부분에 대한 측정 가능한 개별 속성입니다. 여기서는 데이터 집합의 각 행이 하나의 자동차를 나타내고 각 열은 해당 자동차의 기능입니다.

예측 모델을 만들기에 적절한 기능 집합을 찾으려면 해결하려는 문제에 대한 실험과 지식이 있어야 합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계가 있고 제거할 수 있습니다. 예를 들어 city-mpg와 highway-mpg는 밀접한 관련이 있으므로 예측에 크게 영향을 주지 않고 하나를 유지하고 다른 하나를 제거할 수 있습니다.

데이터 집합에서 기능 하위 집합을 사용하는 모델을 빌드하겠습니다. 다시 돌아와서 다양한 특성을 선택하고 실험을 다시 실행하여 더 나은 결과가 나오는지 확인할 수 있습니다. 하지만 시작하려면 다음과 같은 기능을 시도해 보겠습니다.

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. 다른 [데이터 집합에서 열 선택][select-columns] 모듈을 실험 캔버스에 끌어 놓습니다. [누락된 데이터 정리][clean-missing-data] 모듈의 왼쪽 출력 포트를 [데이터 집합에서 열 선택][select-columns] 모듈의 입력 포트에 연결합니다.

    ![“데이터 집합에서 열 선택” 모듈을 “누락된 데이터 정리” 모듈에 연결][connect-clean-to-select]
    <br/>
    ***“데이터 집합에서 열 선택” 모듈을 “누락된 데이터 정리” 모듈에 연결***

2. 모듈을 두 번 클릭하고 "Select features for prediction"을 입력합니다.

2. **Properties** 창에서 **Launch column selector**를 클릭합니다.

3. **규칙으로**를 클릭합니다.

4. **다음으로 시작**에서 **열 없음**을 클릭합니다. 필터 행에서 **포함** 및 **열 이름**을 선택하고 텍스트 상자에 열 이름 목록을 선택합니다. 그러면 지정하는 확인란을 제외한 모든 열(기능)를 통과하지 않도록 모듈이 리디렉션됩니다.

5. 확인 표시(확인) 단추를 클릭합니다.

    ![예측에 포함시킬 열(기능) 선택][select-columns-to-include]
    <br/>
    ***예측에 포함시킬 열(기능) 선택***

그러면 다음 단계에서 사용할 학습 알고리즘에 전달하려는 기능만 포함하는 필터링된 데이터 집합을 생성합니다. 나중에 돌아와서 다른 선택 기능을 사용하여 다시 시도할 수 있습니다.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>4단계: 학습 알고리즘 선택 및 적용

데이터가 준비되었으며, 예측 모델 생성은 학습과 테스트로 구성됩니다. 데이터를 사용하여 모델을 학습한 다음 모델을 테스트하여 어느 정도 근접한 가격을 예측할 수 있는지 확인합니다.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*분류*와 *회귀*는 감독 기계 학습 알고리즘의 두 형식입니다. 분류는 색(빨강, 파랑 또는 녹색)과 같이 정의된 범주 집합에서 답변을 예측합니다. 회귀는 숫자를 예측하는 데 사용됩니다.

숫자인 가격을 예측하기 위해 회귀 모델을 사용합니다. 이 예제에서는 간단한 *선형 회귀* 모델을 사용합니다.

> [!TIP]
> 기계 학습 알고리즘의 여러 유형과 사용 시기에 대해 자세히 알아보려면 초급자를 위한 데이터 과학 시리즈의 첫 번째 비디오, [다섯 가지 질문에 대한 데이터 과학의 답변](data-science-for-beginners-the-5-questions-data-science-answers.md)을 볼 수 있습니다. 인포그래픽 [알고리즘 예제가 포함된 기계 학습 기본 사항](basics-infographic-with-algorithm-examples.md), 또는 [기계 학습 알고리즘 치트 시트](algorithm-cheat-sheet.md)를 볼 수도 있습니다.

가격을 포함하는 데이터 집합을 부여하여 모델을 학습합니다. 이 모델은 데이터를 검색하고 자동차의 기능과 해당 가격 간의 상관 관계를 찾습니다. 그런 다음 모델 테스트합니다. 익숙한 자동차 기능 집합을 제공하고 모델이 얼마나 근접하게 알려진 가격을 예측하는지 확인합니다.

데이터 집합을 별도로 학습하고 테스트하도록 데이터를 분할하여 모델을 학습하고 테스트하는 데 모두 데이터를 사용합니다.

1. [데이터 분할][split] 모듈을 선택하여 실험 캔버스로 끌어 놓고 마지막 [데이터 집합에서 열 선택][select-columns] 모듈에 연결합니다.

2. [데이터 분할][split] 모듈을 클릭하여 선택합니다. **첫 번째 출력 데이터 집합의 행 분수**(캔버스의 오른쪽에 있는 **속성** 창에서)를 찾아서 0.75로 설정합니다. 그러면 데이터의 75%를 모델 학습에 사용하고 25%는 테스트용으로 보유합니다.(나중에 다른 비율로 실험해 볼 수 있습니다.)

    ![“데이터 분할” 모듈의 분할 분수를 0.75로 설정][set-split-data-percentage]
    <br/>
    ***“데이터 분할” 모듈의 분할 분수를 0.75로 설정***

    > [!TIP]
    > **Random seed** 매개 변수를 변경하면 학습 및 테스트용으로 서로 다른 무작위 샘플을 생성할 수 있습니다. 이 매개 변수는 난수 발생기의 시드를 제어합니다.

2. 실험을 실행합니다. 실험이 실행되면 [데이터 집합에서 열 선택][select-columns] 및 [데이터 분할][split] 모듈이 다음에 추가할 모듈에 열 정의를 전달합니다.  

3. 학습 알고리즘을 선택하려면 캔버스 왼쪽의 모듈 팔레트에서 **Machine Learning** 범주를 확장한 후 **Initialize Model**을 확장합니다. 기계 학습 알고리즘을 초기화하는 데 사용할 수 있는 몇 가지 범주의 모듈이 표시됩니다. 이 예제의 경우 **회귀** 범주 아래 [선형 회귀][linear-regression] 모듈을 선택하고 실험 캔버스로 끌어 놓습니다.
(팔레트 검색 상자에 "linear regression"을 입력하여 모듈을 찾을 수도 있습니다.)

4. [모델 학습][train-model] 모듈을 찾아 실험 캔버스로 끌어 놓습니다. [선형 회귀][linear-regression] 모듈의 출력 포트를 [모델 학습][train-model] 모듈의 왼쪽 포트에 연결하고 [분할 데이터][split] 모듈의 데이터 학습 출력(왼쪽 포트)를 [모델 학습][train-model] 모듈의 오른쪽 입력 포트에 연결합니다.

    ![“모델 학습” 모듈을 “선형 회귀”와 “데이터 분할” 모듈에 모두 연결][connect-train-model]
    <br/>
    ***“모델 학습” 모듈을 “선형 회귀”와 “데이터 분할” 모듈에 모두 연결***

5. [모델 학습][train-model] 모듈을 선택하고 **속성** 창에서 **열 선택기 시작**을 클릭한 다음 **가격** 열을 선택합니다. 모델이 예측할 값입니다.

    **사용 가능한 열** 목록에서 가격 열을 **선택한 열** 목록으로 이동하여 열 선택기에서 **가격** 열을 선택합니다.

    ![“모델 학습” 모듈에 대한 가격 열 선택][select-price-column]
    <br/>
    ***“모델 학습” 모듈에 대한 가격 열 선택***

6. 실험을 실행합니다.

이제 가격을 예측하는 데 새 자동차 데이터의 점수를 매기는 데 사용할 수 있는 학습된 회귀 모델이 있습니다.

![실행한 후 실험은 이제 다음과 같이 표시됩니다.][second-experiment-run]
<br/>
***실행한 후 실험은 이제 다음과 같이 표시됩니다.***

## <a name="step-5-predict-new-automobile-prices"></a>5단계: 새 자동차 가격 예측

데이터의 75%를 사용하여 모델을 학습했으며, 이제 모델을 사용하여 나머지 25% 데이터의 점수를 매겨 모델 기능이 얼마나 좋은지 확인할 수 있습니다.

1. [모델 점수 매기기][score-model] 모듈을 찾아 실험 캔버스로 끌어 놓습니다. [모델 학습][train-model]모듈의 출력 포트를 [모델 점수 매기기][score-model]의 왼쪽 입력 포트에 연결합니다. 오른쪽 입력 포트를 [데이터 분할][split] 모듈의 테스트 데이터 출력(오른쪽 포트)을 [모델 점수 매기기][score-model]의 오른쪽 입력 포트에 연결합니다.

    ![“모델 점수 매기기” 모듈을 “모델 학습”과 “데이터 분할” 모듈에 모두 연결][connect-score-model]
    <br/>
    ***“모델 점수 매기기” 모듈을 “모델 학습”과 “데이터 분할” 모듈에 모두 연결***

2. 실험을 실행하고 [모델 점수 매기기][score-model] 모듈의 출력을 보려면 [모델 점수 매기기][score-model]의 출력 포트를 클릭하고 **시각화**를 선택합니다. 출력에 테스트 데이터에서 가져온 알려진 값과 함께 가격 예측 값이 표시됩니다.  

    ![“모델 점수 매기기” 모듈의 출력][score-model-output]
    <br/>
    ***“모델 점수 매기기” 모듈의 출력***

3. 마지막으로 결과의 품질을 테스트합니다. [모델 평가][evaluate-model] 모듈을 선택하여 실험 캔버스로 끌어 놓은 후 [모델 점수 매기기][score-model] 모듈의 출력 포트를 [모델 평가][evaluate-model]의 왼쪽 입력 포트에 연결합니다.

    > [!TIP]
    > [모델 평가][evaluate-model] 모듈은 두 개의 모델을 나란히 비교하는 데 사용될 수 있으므로 모듈에 두 개의 입력 포트가 있습니다. 나중에 다른 알고리즘을 실험에 추가하고 [모델 평가][evaluate-model]를 사용하여 어느 모델이 더 나은 결과 제공하는지 확인할 수 있습니다.

4. 실험을 실행합니다.

[모델 평가][evaluate-model] 모듈의 출력을 보려면 출력 포트를 클릭한 다음 **시각화**를 선택합니다.

![실험에 대한 평가 결과][evaluation-results]
<br/>
***실험에 대한 평가 결과***

모델에 대한 다음 통계가 표시됩니다.

- **Mean Absolute Error** ): 절대 평균 오차입니다( *error* 는 예측 값과 실제 값 사이의 차이).
- **Root Mean Squared Error** ): 테스트 데이터 집합에 대해 예측한 평균 제곱 오차의 제곱근입니다.
- **Relative Absolute Error**: 실제 값과 모든 실제 값 평균 사이의 절대값 차에 대해 상대적인 절대 오차의 평균입니다.
- **Relative Squared Error**: 실제 값과 모든 실제 값 평균 사이의 제곱 차에 대해 상대적인 평균 제곱 오차입니다.
- **Coefficient of Determination**: **R 제곱 값**이라고도 하며, 모델이 데이터에 얼마나 적합한지 나타내는 통계 메트릭입니다.

각 오차 통계는 작을수록 좋습니다. 값이 작을수록 예측이 실제 값과 더 근접하게 일치함을 나타냅니다. **결정 계수**의 경우 값이 1(1.0)에 가까울수록 더 잘 예측한 것입니다.

## <a name="final-experiment"></a>최종 실험

최종 실험은 다음과 같이 표시됩니다.

![최종 실험][complete-linear-regression-experiment]
<br/>
***최종 실험***

## <a name="next-steps"></a>다음 단계

이제 첫 번째 기계 학습 자습서를 완료하고 실험을 설정했으므로 계속 모델을 향상시킨 후 예측 웹 서비스로서 배포할 수 있습니다.

- **모델을 개선 하기 위해 반복** - 예를 들어 예측에 사용하는 기능을 변경할 수 있습니다. 또는 [선형 회귀][linear-regression] 알고리즘의 속성을 수정하거나 다른 알고리즘을 시도해 볼 수 있습니다. 한 번에 여러 Machine Learning 알고리즘을 실험에 추가하고 [모델 평가][evaluate-model] 모듈을 사용하여 둘을 비교할 수도 있습니다.
단일 실험에서 여러 모델을 비교하는 방법에 대한 예제는 [Azure AI Gallery](https://gallery.cortanaintelligence.com)의 [회귀 변수 비교](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5)를 참조합니다.

    > [!TIP]
    > 실험 반복을 복사하려면 페이지 아래쪽의 **다른 이름으로 저장** 단추를 사용합니다. 페이지 아래쪽의 **실행 기록 보기**를 클릭하여 실험의 모든 반복을 볼 수 있습니다. 자세한 내용은 [Azure Machine Learning 스튜디오에서 실험 반복 관리][runhistory]를 참조하세요.

[runhistory]: manage-experiment-iterations.md

- **예측 웹 서비스로 모델 배포** - 모델이 마음에 들면 새 데이터를 사용하여 자동차 가격 예측에 사용할 웹 서비스로 모델을 배포할 수 있습니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 배포][publish]를 참조하세요.

[publish]: publish-a-machine-learning-web-service.md

자세히 알아보시겠어요? 모델 만들기, 학습, 점수 매기기 및 배포 과정의 보다 광범위하고 자세한 연습은 [Azure Machine Learning을 사용하여 예측 솔루션 개발][walkthrough]을 참조하세요.

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
