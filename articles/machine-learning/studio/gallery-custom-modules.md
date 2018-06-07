---
title: Azure AI 갤러리 사용자 지정 모듈 | Microsoft Docs
description: Azure AI 갤러리에서 사용자 지정 기계 학습 모듈을 검색합니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: 26cd814980af0824cbf40052a4a9f47161b8cc62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Azure AI 갤러리에서 사용자 지정 기계 학습 모듈 검색
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Machine Learning Studio
Azure AI 갤러리는 Azure Machine Learning Studio의 역량을 확장하는 여러 [사용자 지정 모듈](https://gallery.cortanaintelligence.com/customModules)을 제공합니다. 실험에서 사용하도록 모듈을 가져와서 한층 더 진보된 예측 분석 솔루션을 개발할 수 있습니다.

현재 갤러리는 *시계열 분석*, *연결 규칙*, *클러스터링 알고리즘*(K-means 외) 및 *시각화* 모듈과 기타 자주 사용되는 유틸리티 모듈을 제공합니다.


## <a name="discover"></a>검색
[갤러리에서](http://gallery.cortanaintelligence.com) 사용자 지정 모듈을 찾아보려면 **추가 정보** 아래에서 **사용자 지정 모듈**을 선택합니다.

![갤러리 홈 페이지에서 사용자 지정 모듈 선택](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[사용자 지정 모듈](https://gallery.cortanaintelligence.com/customModules)** 페이지에는 최근에 추가되고 인기 있는 모듈 목록이 표시됩니다. 모든 사용자 지정 모듈을 보려면 **모두 표시** 단추를 선택합니다. 특정 사용자 지정 모듈을 검색하려면 **모두 표시**를 선택한 다음 필터 조건을 선택합니다. 또한 갤러리 페이지 맨 위에 있는 **검색** 상자에서 검색 용어를 입력할 수 있습니다.

![모든 사용자 지정 모듈을 찾아보려면 “모두 표시” 선택](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>이해

게시된 사용자 지정 모듈의 작동 원리를 이해하려면 사용자 지정 모듈을 선택하여 모듈 상세 정보 페이지를 엽니다. 세부 정보 페이지는 일관된 정보 제공용 학습 환경을 제공합니다. 예를 들어 세부 정보 페이지는 모듈의 목적을 강조 표시하고 예상되는 입력, 출력 및 매개 변수를 나열합니다. 세부 정보 페이지에는 검사 및 사용자 지정할 수 있는 기본 소스 코드에 대한 링크도 있습니다.

### <a name="comment-and-share"></a>주석 및 공유
사용자 지정 모듈 세부 정보 페이지의 **의견** 섹션에서 모듈에 대해 주석 또는 피드백을 남기거나 질문을 할 수 있습니다. Twitter 또는 LinkedIn에서 친구나 동료들과 모듈을 공유할 수 있습니다. 페이지를 보도록 다른 사용자를 초대하기 위해 모듈 세부 정보 페이지의 링크를 메일로 보낼 수도 있습니다.

![이 항목을 친구 들과 공유](./media/gallery-how-to-use-contribute-publish/share-links.png)

![사용자 고유의 설명 추가](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>가져오기
갤러리에 있는 모든 사용자 지정 모듈을 자신의 실험으로 가져올 수 있습니다.

Azure AI 갤러리는 모듈의 복사본을 가져오는 두 가지 방법을 제공합니다.

* **갤러리에서** 갤러리에서 사용자 지정 모듈을 가져오는 경우 모듈을 사용하는 방법에 대한 예를 제공하는 샘플 실험도 받게 됩니다.
* **Machine Learning Studio 내에서** Machine Learning Studio에서 작업 하는 동안에 모든 사용자 지정 모듈을 가져올 수 있습니다(이 경우 샘플 실험을 가져오지 못함).

### <a name="from-the-gallery"></a>갤러리에서

1. 갤러리에서 모듈의 세부 정보 페이지를 엽니다. 
2. **Studio에서 열기**를 선택합니다.
   
    ![갤러리에서 사용자 지정 모듈 열기](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
각 사용자 지정 모듈은 모듈 사용법을 보여주는 샘플 실험을 포함합니다. **Studio에서 열기**를 선택하면 샘플 실험이 Machine Learning Studio 작업 영역에서 열립니다. (Studio에 로그인되어 있지 않은 경우 먼저 Microsoft 계정을 사용하여 로그인하도록 메시지가 표시됩니다.)

샘플 실험 외에도 사용자 지정 모듈이 작업 영역에 복사됩니다. 또한 모든 기본 제공 또는 사용자 지정 Machine Learning Studio 모듈과 함께 모듈 팔레트에 배치됩니다. 이제 이 모듈을 작업 영역의 다른 모듈처럼 자신의 실험에서 사용할 수 있습니다.

### <a name="from-within-machine-learning-studio"></a>Machine Learning Studio 내에서

1. Machine Learning Studio에서 **새로 만들기**를 선택합니다.
2. **모듈**을 선택합니다. 갤러리 모듈 목록에서 선택하거나 **검색** 상자를 사용하여 특정 모듈을 찾을 수 있습니다.
3. 모듈을 마우스로 가리킨 다음 **모듈 가져오기**를 선택합니다. (모듈에 대한 정보를 가져오려면 **갤러리에서 보기**를 선택합니다. 이렇게 하면 갤러리에서 모듈의 세부 정보 페이지로 이동합니다.)
   
    ![Machine Learning Studio로 사용자 지정 모듈 가져오기](./media/gallery-custom-modules/add-custom-module-in-studio.png)

사용자 지정 모듈이 작업 영역으로 복사되고 기본 제공 또는 사용자 지정 Machine Learning Studio 모듈과 함께 모듈 팔레트에 배치됩니다. 이제 이 모듈을 작업 영역의 다른 모듈처럼 자신의 실험에서 사용할 수 있습니다.

## <a name="use"></a>사용

모듈을 가져올 때, 사용자 지정 모듈을 가져오기 위해 선택하는 방법에 관계없이 모듈은 Machine Learning Studio의 모듈 팔레트에 배치됩니다. 모듈 팔레트에서 사용자 지정 모듈을 다른 모듈처럼 작업 영역의 모든 실험에 사용할 수 있습니다.

가져온 모듈을 사용합니다.

1. 실험을 만들거나 기존 실험을 엽니다.
2. 모듈 팔레트의 작업 영역에서 사용자 지정 모듈의 목록을 확장하려면 **사용자 지정**을 선택합니다. 모듈 팔레트는 실험 캔버스의 왼쪽에 있습니다.
   
    ![Studio 팔레트의 사용자 지정 모듈 목록](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. 가져온 모듈을 선택하고 실험으로 끌어옵니다.


**[갤러리로 이동](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

