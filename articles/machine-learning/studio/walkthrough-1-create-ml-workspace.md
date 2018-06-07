---
title: '1단계: Machine Learning 작업 영역 만들기 | Microsoft Docs'
description: '예측 솔루션 개발 연습 1단계: 새 Azure Machine Learning Studio 작업 영역을 설정하는 방법을 알아봅니다.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 88f928599f62cbb39d65cd29a978e3a9d25d863f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>연습 1단계: Machine Learning 작업 영역 만들기
[Azure Machine Learning에서 예측 분석 솔루션 개발](walkthrough-develop-predictive-solution.md)연습의 첫 번째 단계입니다.

1. **Machine Learning 작업 영역 만들기**
2. [기존 데이터 업로드](walkthrough-2-upload-data.md)
3. [새 실험 만들기](walkthrough-3-create-new-experiment.md)
4. [모델 학습 및 평가](walkthrough-4-train-and-evaluate-models.md)
5. [웹 서비스 배포](walkthrough-5-publish-web-service.md)
6. [웹 서비스 액세스](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

기계 학습 스튜디오를 사용하려면 Microsoft Azure Machine Learning 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.  

Azure 구독에 대한 관리자는 작업 영역을 만들고 소유자 또는 참가자로 추가해야 합니다. 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 공유](create-workspace.md)를 참조하세요.

작업 영역을 만든 후 Machine Learning Studio([https://studio.azureml.net/Home](https://studio.azureml.net/Home))를 엽니다. 둘 이상의 작업 영역이 있는 경우 창의 오른쪽 위 모서리에 있는 도구 모음에서 작업 영역을 선택할 수 있습니다.

![Studio에서 작업 영역 선택][2]

> [!TIP]
> 작업 영역의 소유자인 경우 나중에 작업 영역에 다른 사용자를 초대하여 작업 중인 실험을 공유할 수 있습니다. Machine Learning Studio의 **SETTINGS** 페이지에서 이 작업을 할 수 있습니다. 각 사용자에 대한 Microsoft 계정 또는 조직 계정만 있으면 됩니다.
> 
> **설정** 페이지에서 **사용자**를 클릭한 다음 창의 아래쪽에 있는 **더 많은 사용자 초대**를 클릭합니다.
> 
> 

- - -
**다음: [기존 데이터 업로드](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
