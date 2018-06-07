---
title: Machine Learning 모델 재학습 | Microsoft Docs
description: Azure Machine Learning에서 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: f02bf54cc8941d18c866183c9b7d12bbe7222d2b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-a-machine-learning-model"></a>Machine Learning 모델 재학습
Azure Machine Learning에서 기계 학습 모델 운영 프로세스의 일부로 모델은 학습 및 저장됩니다. 그런 다음 이를 서술적 웹 서비스를 만드는 데 사용합니다. 그러면 웹 사이트, 대시보드 및 모바일 앱에서 웹 서비스를 사용할 수 있습니다. 

Machine Learning을 사용하여 만드는 모델은 일반적으로 정적이지 않습니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다. 

재학습은 자주 발생할 수 있습니다. 프로그래밍 방식의 재학습 API 기능을 사용하면 재학습 API를 통해 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델과 함께 웹 서비스를 업데이트할 수 있습니다. 

이 문서에서는 재학습 프로세스를 설명하고 재학습 API를 사용하는 방법을 보여 줍니다.

## <a name="why-retrain-defining-the-problem"></a>다시 학습 이유: 문제 정의
기계 학습 훈련 프로세스의 일부로, 데이터 집합을 사용하여 모델을 학습합니다. Machine Learning을 사용하여 만드는 모델은 일반적으로 정적이지 않습니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다.

이러한 시나리오에서 개발자나 API 소비자는 프로그래밍 방식의 API를 통해 일회성이나 주기적으로 고유한 데이터를 사용하여 모델을 다시 학습할 수 있는 클라이언트를 만들 수 있습니다. 그런 후에 다시 학습의 결과를 평가하고 새로 학습된 모델을 사용하도록 웹 서비스 API를 업데이트할 수 있습니다.

> [!NOTE]
> 기존 학습 실험 및 새 웹 서비스가 있다면 다음 섹션에서 언급된 연습을 수행하는 대신에 기존 예측 웹 서비스 재학습을 확인하는 것이 좋습니다.
> 
> 

## <a name="end-to-end-workflow"></a>종단 간 워크플로
프로세스에 학습 실험 및 웹 서비스로 게시된 서술적 실험이 구성 요소로 포함됩니다. 학습된 모델을 다시 학습할 수 있으려면 학습 실험은 학습된 모델의 출력을 사용하여 웹 서비스로 게시되어야 합니다. 그러면 API가 다시 학습을 위해 모델에 액세스할 수 있습니다. 

다음 단계는 신규 및 기존 웹 서비스에 모두 적용됩니다.

초기 예측 웹 서비스를 만듭니다.

* 학습 실험 만들기
* 예측 웹 실험 만들기
* 예측 웹 서비스 배포

웹 서비스 재학습:

* 재학습을 감안하여 학습 실험 업데이트
* 재학습 웹 서비스 배포
* 학습 실험의 일괄 처리 실행 서비스 코드를 사용하여 모델 재학습

이전 단계에 대한 연습은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요.

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

기존 웹 서비스를 배포한 경우:

* 예측 웹 서비스에 새 끝점 만들기
* PATCH URL 및 코드 가져오기
* PATCH URL을 사용하여 다시 학습된 모델의 새 끝점을 가리킵니다. 

이전 단계에 대한 연습은 [기존 웹 서비스 재학습](retrain-a-classic-web-service.md)을 참조하세요.

기존 웹 서비스를 다시 학습하는 데 있어 난관에 봉착한 경우 [Azure Machine Learning Classic Web 서비스 재학습 문제 해결](troubleshooting-retraining-models.md)을 참조하세요.

새 웹 서비스를 배포한 경우:

* Azure Resource Manager 계정으로 로그인합니다.
* 웹 서비스 정의 가져오기
* JSON으로 웹 서비스 정의 내보내기
* JSON에서 `ilearner` blob에 대한 참조 업데이트
* JSON을 웹 서비스 정의로 가져오기
* 웹 서비스를 새 웹 서비스 정의로 업데이트합니다.

이전 단계에 대한 연습은 [Machine Learning Management PowerShell cmdlets를 사용하여 새 웹 서비스 재학습](retrain-new-web-service-using-powershell.md)을 참조하세요.

Classic Web 서비스에 대한 재학습을 설정하는 프로세스에는 다음 단계가 포함됩니다.

![재학습 프로세스 개요][1]

New Web 서비스에 대한 재학습을 설정하는 프로세스에는 다음 단계가 포함됩니다.

![재학습 프로세스 개요][7]

## <a name="other-resources"></a>기타 리소스
* [Azure Data Factory를 사용하여 Azure Machine Learning 모델 재학습 및 업데이트](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [PowerShell을 사용하여 한 실험에서 여러 Machine Learning 모델 및 웹 서비스 끝점 만들기](create-models-and-endpoints-with-powershell.md)
* [API를 사용한 AML 재학습 모델](https://www.youtube.com/watch?v=wwjglA8xllg) 비디오는 Retraining API 및 PowerShell을 사용하여 Azure Machine Learning에서 만들어진 Machine Learning 모델을 재학습하는 방법을 보여줍니다.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

