---
title: Azure Machine Learning 클래식 웹 서비스의 재학습 문제 해결 | Microsoft Docs
description: Azure Machine Learning 웹 서비스에 대한 모델을 재학습하는 경우에 발생하는 일반적인 문제를 파악하고 바로 잡습니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 302049003e9bebe48d70bf800baaa98b951abbeb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Azure Machine Learning 기존 웹 서비스의 재학습 문제 해결
## <a name="retraining-overview"></a>재학습 개요
예측 실험을 점수 매기기 웹 서비스로 배포하는 경우 정적 모델입니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다. 

기존 웹 서비스 재학습 프로세스의 자세한 연습은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요.

## <a name="retraining-process"></a>재학습 프로세스
웹 서비스를 재학습해야 할 경우, 몇 가지 추가 요소를 추가해야 합니다.

* 학습 실험에서 배포된 웹 서비스입니다. 실험에는 **모델 학습** 모듈의 출력에 연결된 **웹 서비스 출력** 모듈이 있어야 합니다.  
  
    ![모델 학습에 웹 서비스 출력을 연결합니다.][image1]
* 점수 매기기 웹 서비스에 추가된 새 끝점입니다.  프로그래밍 방식으로 항목 또는 Azure Machine Learning 웹 서비스 포털을 통해 Machine Learning 재학습 모델에서 참조한 샘플 코드를 사용하여 프로그래밍 방식으로 끝점을 추가할 수 있습니다.

그런 다음 학습 웹 서비스 API 도움말 페이지에서 샘플 C# 코드를 사용하여 모델을 재학습할 수 있습니다. 결과를 평가했고 만족한다면 추가한 새 끝점을 사용하여 학습된 모델 점수 매기기 웹 서비스를 업데이트합니다.

모든 준비가 된 경우 모델을 재학습하기 위해 취해야 할 주요 단계는 다음과 같습니다.

1. 학습 웹 서비스 호출: RRS(요청 응답 서비스)가 아닌 BES(Batch 실행 서비스)를 호출합니다. API 도움말 페이지에 나오는 샘플 C# 코드를 사용하여 호출할 수 있습니다. 
2. *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*에 대한 값 찾기: 학습 웹 서비스에 대한 호출에서 이러한 값을 출력에 반환합니다. 
   ![재학습 샘플의 출력 및 BaseLocation, RelativeLocation 및 SasBlobToken 값을 표시합니다.][image6]
3. 새 학습된 모델을 사용하여 점수 매기기 웹 서비스에서 추가된 끝점 업데이트하기: 프로그래밍 방식으로 Machine Learning 모델 재학습에서 제공된 샘플 코드를 사용하여 학습 웹 서비스에서 새로 학습된 모델로 점수 매기기 모델에 추가한 새 끝점을 업데이트합니다.

## <a name="common-obstacles"></a>일반적인 장애물
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>PATCH URL이 정확한지 확인합니다.
사용 중인 PATCH URL은 점수 매기기 웹 서비스에 추가한 새 점수 매기기 끝점과 연결된 것이어야 합니다. PATCH URL을 얻는 데는 여러 가지 방법이 있습니다.

**옵션 1: 프로그래밍 방식으로**

올바른 PATCH URL을 가지려면:

1. [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 샘플 코드를 실행합니다.
2. AddEndpoint의 출력에서 *HelpLocation* 값을 찾아 URL을 복사합니다.
   
   ![addEndpoint 샘플의 출력에 있는 HelpLocation.][image2]
3. 웹 서비스에 대한 도움말 링크를 제공하는 페이지로 이동하려면 브라우저에 URL을 붙여 넣습니다.
4. **리소스 업데이트** 링크를 클릭하여 패치 도움말 페이지를 엽니다.

**옵션 2: Azure Machine Learning 웹 서비스 포털 사용**:

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에 로그인합니다.
2. 맨 위에 있는 **웹 서비스** 또는 **기존 웹 서비스**를 클릭합니다.
4. 사용 중인 점수 매기기 웹 서비스를 클릭합니다(웹 서비스의 기본 이름을 수정하지 않은 경우 "[Scoring Exp.]"로 끝남).
5. **+새로 만들기**를 클릭합니다.
6. 끝점이 추가된 후 끝점 이름을 클릭합니다.
7. **패치** URL 아래의 **API 도움말**을클 릭하여 패치 도움말 페이지를 엽니다.

> [!NOTE]
> 예측 웹 서비스 대신 학습 웹 서비스에 끝점을 추가한 경우 **업데이트 리소스** 링크를 클릭하면 다음과 같은 오류가 발생합니다. “죄송합니다. 이 기능은 지원되지 않거나 이 컨텍스트에서 사용할 수 없습니다. 이 웹 서비스에 업데이트할 수 있는 리소스가 없습니다. 불편을 끼쳐 드려 죄송합니다. 이 워크플로를 개선하도록 작업 중입니다.”
> 
> 

PATCH 도움말 페이지에는 사용해야 하는 PATCH URL이 들어 있으며 호출하는 데 사용할 수 있는 샘플 코드가 제공됩니다.

![패치 URL.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>올바른 점수 매기기 끝점을 업데이트하고 있는지 확인합니다.
* 학습 웹 서비스를 패치하지 마십시오: 패치 작업은 점수 매기기 웹 서비스에서 수행해야 합니다.
* 기본 끝점을 웹 서비스에서 패치하지 마십시오. 패치 작업은 추가한 새 점수 매기기 웹 서비스 끝점에서 수행해야 합니다.

웹 서비스 포털을 방문하여 끝점이 어떤 웹 서비스에 있는지 확인할 수 있습니다. 

> [!NOTE]
> 끝점을 학습 웹 서비스가 아닌 예측 웹 서비스에 추가해야 합니다. 학습 및 예측 웹 서비스를 모두 올바르게 배포한 경우 나열된 두 개의 별도 웹 서비스가 표시됩니다. 예측 웹 서비스는 "[예측 exp.]"로 끝나야 합니다.
> 
> 

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에 로그인합니다.
2. **웹 서비스** 또는 **기존 웹 서비스**를 클릭합니다.
3. 예측 웹 서비스를 선택합니다.
4. 웹 서비스에 새 끝점이 추가되었는지 확인합니다.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>작업 영역이 웹 서비스와 동일한 지역에 있는지 확인합니다.
1. [Machine Learning Studio](https://studio.azureml.net/)에 로그인합니다.
2. 맨 위에 있는 작업 영역의 드롭다운 목록을 클릭합니다.

   ![기계 학습 영역 UI.][image4]

3. 작업 영역이 있는 지역을 확인합니다.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
