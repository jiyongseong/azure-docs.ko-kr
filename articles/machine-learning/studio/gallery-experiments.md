---
title: Azure AI 갤러리 실험 | Microsoft Docs
description: Azure AI 갤러리에서 실험을 검색 및 공유합니다.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.openlocfilehash: d6de7aa6d16196f415688d68251e8c1b0a4b9aee
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="discover-experiments-in-azure-ai-gallery"></a>Azure AI 갤러리의 실험 검색
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Machine Learning Studio용 실험
갤러리에는 [Azure Machine Learning Studio](https://studio.azureml.net)에서 개발된 매우 다양한 [실험](https://gallery.cortanaintelligence.com/experiments)이 있습니다. 실험은 특정한 기계 학습 기법을 보여주는 빠른 개념 증명 실험에서 복잡한 기계 학습 문제에 대한 완전히 개발된 솔루션에 이르기까지 다양합니다.

> [!NOTE]
> ***실험***은 예측 분석 모델을 생성하는 데 사용할 수 있는 Machine Learning Studio의 캔버스입니다. 다양한 분석 모듈에 데이터를 연결하여 모델을 만듭니다. 다른 아이디어를 시도, 평가판 실행을 수행하고 마지막으로 Azure에 웹 서비스로 모델을 배포할 수 있습니다. 기본 실험을 만드는 방법의 예제는 [Machine Learning 자습서: Azure Machine Learning Studio에서 첫 번째 실험 만들기](create-experiment.md)를 참조하세요. 예측 분석 솔루션을 만드는 방법의 자세한 연습 과정은 [연습: Azure Machine Learning의 신용 위험 평가에 대한 예측 분석 솔루션 개발](walkthrough-develop-predictive-solution.md)을 참조하세요.
>
>

## <a name="discover"></a>검색
[갤러리에서](http://gallery.cortanaintelligence.com) 실험을 찾아보려면 갤러리 홈 페이지의 위쪽에서 **실험**을 클릭합니다.

**[실험](https://gallery.cortanaintelligence.com/experiments)** 페이지에는 최근에 추가되고 인기 있는 실험 목록이 표시됩니다. 모든 실험을 보려면 **모두 표시** 단추를 선택합니다. 특정 실험을 검색하려면 **모두 표시**를 선택한 다음 필터 조건을 선택합니다. 또한 갤러리 페이지 맨 위에 있는 **검색** 상자에서 검색 용어를 입력할 수 있습니다.

실험 세부 정보 페이지에서 실험에 대한 자세한 정보를 가져올 수 있습니다. 실험 세부 정보 페이지를 열려면 해당 실험을 선택합니다. 실험 세부 정보 페이지의 **의견** 섹션에서 실험에 대해 주석 또는 피드백을 남기거나 질문을 할 수 있습니다. Twitter 또는 LinkedIn에서 친구나 동료들과 실험을 공유할 수 있습니다. 페이지를 보도록 다른 사용자를 초대하기 위해 실험 세부 정보 페이지의 링크를 메일로 보낼 수도 있습니다.

![이 항목을 친구 들과 공유](./media/gallery-how-to-use-contribute-publish/share-links.png)

![사용자 고유의 설명 추가](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>다운로드
갤러리에서 Machine Learning Studio 작업 영역으로 모든 실험의 복사본을 다운로드할 수 있습니다. 그런 다음 복사본을 수정하여 사용자 고유의 솔루션을 만들 수 있습니다.

Azure AI 갤러리는 실험의 복사본을 가져오는 두 가지 방법을 제공합니다.

* **갤러리에서** 갤러리에서 원하는 실험을 찾으면 복사본을 다운로드하여 Machine Learning Studio 작업 영역에서 열 수 있습니다.
* **Machine Learning Studio 내에서** Machine Learning Studio에서 갤러리의 모든 실험을 템플릿으로 사용하여 새 실험을 만들 수 있습니다.

### <a name="from-the-gallery"></a>갤러리에서

1. 갤러리에서 실험의 세부 정보 페이지를 엽니다.
2. **Studio에서 열기**를 선택합니다.

    ![갤러리에서 실험 열기](./media/gallery-experiments/open-experiment-from-gallery.png)

**Studio에서 열기**를 선택하면 실험이 Machine Learning Studio 작업 영역에서 열립니다. (Machine Learning Studio에 로그인되어 있지 않은 경우 먼저 Microsoft 계정을 사용하여 로그인하도록 메시지가 표시됩니다.)

### <a name="from-within-machine-learning-studio"></a>Machine Learning Studio 내에서

1. Machine Learning Studio에서 **새로 만들기**를 선택합니다.
2. **실험**을 선택합니다. 갤러리 실험 목록에서 선택하거나 **검색** 상자를 사용하여 특정 실험을 찾을 수 있습니다.
3. 마우스로 실험을 가리킨 다음 **Studio에서 열기**를 선택합니다. (실험에 대한 정보를 보려면 **갤러리에서 보기**를 선택합니다. 이렇게 하면 갤러리에서 실험의 세부 정보 페이지로 이동합니다.)

    ![Machine Learning Studio 내에서 갤러리 실험 열기](./media/gallery-experiments/open-experiment-from-studio.png)

다운로드한 실험을 Machine Learning Studio에서 만든 다른 실험처럼 사용자 지정하고, 반복하고, 배포할 수 있습니다.

![Studio에 열려있는 실험](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>기고
갤러리에 로그인하면 갤러리 커뮤니티의 구성원이 됩니다. 커뮤니티의 멤버로서 고유한 실험을 기여하여 발견된 솔루션으로 다른 사용자에게 혜택을 줄 수 있습니다.

### <a name="publish-your-experiment-to-the-gallery"></a>갤러리에 실험 게시

1. Microsoft 계정을 사용하여 Machine Learning Studio에 로그인합니다.
2. 실험을 만든 다음 실행합니다.
3. 갤러리에 실험을 게시할 준비가 되면 실험 캔버스 아래의 작업 목록에서 **Publish to Gallery**(갤러리에 게시)를 선택합니다.

    ![“Publish to Gallery”(갤러리에 게시) 선택](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. **실험 설명** 페이지에서 제목 및 태그를 입력합니다. 제목 및 태그에 설명을 포함합니다. 사용한 기법이나 해결하고 있는 실제 문제를 강조 표시합니다. 설명이 포함된 실험 제목의 예: “이진 분류: Twitter 감정 분석.”

    ![게시에 대한 제목 및 태그 입력](./media/gallery-experiments/experiment-description.png)
5. **요약** 상자에 실험에 대한 요약을 입력합니다. 실험이 해결하는 문제와 해결 방법을 간략하게 설명합니다.
6. **자세한 설명** 상자에 실험의 각 부분에서 수행한 단계를 설명합니다. 여기에 포함할 몇 가지 유용한 주제는 다음과 같습니다.
   * 실험 그래프 스크린샷
   * 데이터 원본 및 설명
   * 데이터 처리
   * 기능 엔지니어링
   * 모델 설명
   * 결과 및 모델 성능 평가

   Markdown을 사용하여 설명에 서식을 지정할 수 있습니다. 실험이 게시될 때 실험 설명 페이지에서 해당 항목이 어떻게 보이는지를 보려면 **미리 보기**를 선택합니다.

   ![텍스트가 어떻게 표시되는지 보기 위해 “미리 보기” 선택](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Markdown 편집을 위해 제공된 텍스트 상자 및 미리 보기는 작습니다. Markdown 편집기에서 실험 설명서를 작성하여 복사한 다음 완성된 문서를 갤러리의 텍스트 상자에 붙여 넣는 것이 좋습니다. 실험을 게시한 후 편집 및 미리 보기에 대해 Markdown을 사용하는 표준 웹 기반 도구를 사용하여 모든 수정 사항을 만들 수 있습니다.

7. **이미지 선택** 페이지에서 실험에 대한 썸네일 이미지를 선택합니다. 썸네일 이미지는 실험 세부 정보 페이지의 맨 위 및 실험 타일에 나타납니다. 다른 사용자가 갤러리를 탐색할 때 썸네일 이미지가 표시됩니다. 컴퓨터에서 이미지를 업로드하거나 갤러리에서 스톡 이미지를 선택할 수 있습니다.
    </br>
    ![갤러리용 이미지 업로드 또는 선택](./media/gallery-experiments/select-gallery-image.png)
8. **설정** 페이지의 **표시 여부**에서 콘텐츠를 공개적으로 게시할 것인지(**공개**) 또는 페이지에 대한 링크가 있는 사용자만 액세스할 수 있게 할 것인지를(**비공개**) 선택합니다.

    ![공개적으로 게시할 지 또는 목록에서 포함하지 말지 선택](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > 실험을 공개적으로 게시하기 전에 설명서가 정확하게 보이는지 확인하려는 경우 먼저 실험을 **비공개**로 게시합니다. 나중에 실험 세부 정보 페이지에서 표시 여부 설정을 **공개**로 변경할 수 있습니다.
   >
   >
9. 실험을 갤러리에 게시하려면 **확인** 표시를 선택합니다.

    ![실험을 게시하려면 확인 표시를 선택합니다](./media/gallery-experiments/ok-checkmark.png)

고품질 갤러리 실험을 게시하는 방법에 대한 팁은 [실험을 문서화하고 게시하는 팁](#tips-for-documenting-and-publishing-your-experiment)을 참조하세요.

이제 끝입니다. 모두 완료되었습니다.

이제 갤러리에서 실험을 볼 수 있으며 다른 사용자와 링크를 공유할 수 있습니다. **공개** 표시 여부 설정을 사용하여 실험을 게시한 경우 실험이 갤러리의 찾아보기 및 검색 결과에 표시됩니다. 갤러리에 로그인하여 언제든지 실험 세부 정보 페이지에서 실험 설명서를 편집할 수 있습니다.

기고한 글의 목록을 보려면 갤러리 페이지의 오른쪽 위 모퉁이에서 이미지를 선택합니다. 그런 다음 사용자 이름을 선택하여 계정 페이지를 엽니다.

![계정 이름을 선택](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>실험 업데이트
필요에 따라 갤러리에 게시한 실험의 워크플로(모듈, 매개 변수 등)를 변경할 수 있습니다. Machine Learning Studio에서 변경하려는 내용을 변경한 후 다시 게시합니다. 게시되어 있는 실험이 변경한 내용으로 업데이트됩니다.

갤러리에서 실험에 대한 다음 정보를 직접 변경할 수 있습니다.

* 실험 이름
* 요약 또는 설명
* 태그들
* 이미지
* 표시 여부 설정(**공개** 또는 **비공개**)

갤러리에서 실험을 삭제할 수도 있습니다.

갤러리의 실험 세부 정보 페이지 또는 프로필 페이지에서 이러한 내용을 변경하거나 실험을 삭제할 수 있습니다.


#### <a name="from-the-experiment-details-page"></a>실험 세부 정보 페이지에서
실험 세부 정보 페이지에서 **편집**을 선택하여 실험에 대한 세부 정보를 변경합니다.

![편집을 선택하여 실험 편집](./media/gallery-experiments/edit-button.png)

세부 정보 페이지가 편집 모드로 실행됩니다. 변경 작업을 수행하려면 실험 이름, 요약, 또는 태그 옆에 있는 **편집**을 선택합니다. 변경을 마쳤으면 **완료**를 선택합니다.

![“편집”을 선택하여 세부 정보를 편집하고, 마쳤으면 “완료”를 선택](./media/gallery-experiments/edit-details-page.png)

실험에 대한 표시 여부 설정을 변경하거나 (**공개** 또는 **비공개**) 갤러리에서 실험을 삭제하려면 **설정** 아이콘을 선택합니다.

![“설정”을 선택하여 표시여부를 변경하거나 실험을 삭제](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>프로필 페이지에서
프로필 페이지에서 실험에 있는 아래쪽 화살표를 선택한 다음 **편집**을 선택합니다. 그러면 실험에 대한 세부 정보 페이지가 편집 모드에서 열립니다. 변경 작업을 마쳤으면 **완료**를 선택합니다.

갤러리에서 실험을 삭제하려면 **삭제**를 선택합니다.

![“편집” 또는 “삭제” 선택](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>실험을 문서화하고 게시하는 팁
* 읽기 권한자가 이전 데이터 과학 경험이 있다고 가정할 수도 있지만 간단한 언어를 사용하는 것이 도움이 될 수 있습니다. 가능하면 자세하게 설명합니다.
* Cortana Intelligence Suite는 비교적 새로운 기능입니다. 모든 읽기 권한자가 사용하는 방법을 잘 알고 있지 않습니다. 실험을 골고루 탐색할 수 있도록 충분한 정보와 단계별 설명을 제공합니다.
* 시각적 개체는 읽기 권한자가 실험 설명서를 올바르게 해석하고 사용하는 데 유용할 수 있습니다. 시각적 개체에는 실험 그래프 및 데이터의 스크린샷이 포함됩니다. 실험 설명서에 이미지를 포함하는 방법에 대한 자세한 내용은 [게시 지침 및 예제 컬렉션](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1)(영문)을 참조하세요.
* 데이터 집합이 실험에 포함되는 경우(즉, 데이터 가져오기 모듈을 통해 데이터 집합을 가져오지 않는 경우) 데이터 집합은 실험의 일부이며 갤러리에 게시됩니다. 게시하는 데이터 집합이 누구에게나 공유와 다운로드를 허용하는 사용 조건을 가지고 있는지 확인합니다. 갤러리 기여에 관한 자세한 내용은 Azure [사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/)에 나옵니다.

## <a name="frequently-asked-questions"></a>질문과 대답
**실험의 이미지 제출 또는 편집에 대한 요구 사항은 무엇인가요?**

실험과 제출하는 이미지는 기고한 실험 타일을 만드는 데 사용됩니다. 이미지는 500KB보다 작고 aspect ratio 3:2 및 960 × 640의 해상도인 것이 좋습니다.

**실험에 사용한 데이터 집합은 어떻게 되나요? 데이터 집합도 갤러리에 게시되나요?**

데이터 집합은 실험의 일부이고 데이터 가져오기 모듈을 통해 가져오지 않는 경우 데이터 집합은 실험의 일부로 갤러리에 게시됩니다. 실험과 함께 게시하는 데이터 집합이 적절한 사용 조건을 가지고 있는지 확인하십시오. 사용 조건은 누구나 데이터를 공유하고 다운로드 하도록 허용해야 합니다.

**데이터 가져오기 모듈을 사용하여 Azure HDInsight 또는 SQL Server에서 데이터를 끌어오는 실험이 있습니다. 여기에서 제 자격 증명을 사용하여 데이터를 검색합니다. 이러한 종류의 실험을 게시할 수 있습니까? 내 자격 증명이 공유되지 않는다고 어떻게 보장할 수 있습니까?**

현재 갤러리에 자격 증명을 사용하는 실험을 게시할 수 없습니다.

**여러 태그를 어떻게 입력하나요?**

태그를 입력한 후 또 다른 태그를 입력하려면 Tab 키를 누릅니다.

**[갤러리로 이동](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
