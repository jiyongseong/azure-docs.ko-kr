---
title: '6단계: Machine Learning 웹 서비스 액세스 | Microsoft Docs'
description: '예측 솔루션 연습 개발의 6단계: 활성 Azure Machine Learning 웹 서비스 액세스'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 82e068ca3656b28e5e8dad19a31d6e5ff9c6f8b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>연습 6단계: Azure Machine Learning 웹 서비스 액세스

[Azure Machine Learning에서 예측 분석 솔루션 개발](walkthrough-develop-predictive-solution.md)

1. [Machine Learning 작업 영역 만들기](walkthrough-1-create-ml-workspace.md)
2. [기존 데이터 업로드](walkthrough-2-upload-data.md)
3. [새 실험 만들기](walkthrough-3-create-new-experiment.md)
4. [모델 학습 및 평가](walkthrough-4-train-and-evaluate-models.md)
5. [웹 서비스 배포](walkthrough-5-publish-web-service.md)
6. **웹 서비스 액세스**

- - -
이 연습의 이전 단계에서 신용 위험 예측 모델을 사용하는 웹 서비스를 배포했습니다. 이제 사용자는 서비스에 데이터를 보내고 결과를 받을 수 있습니다. 

웹 서비스는 다음 두 방법의 하나로 REST API를 사용하여 데이터를 받고 반환할 수 있는 Azure 웹 서비스입니다.  

* **요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 하나 이상의 신용 데이터 행을 서비스에 보내고 서비스에서는 하나 이상의 결과 집합으로 응답합니다.
* **Batch 실행** - 사용자가 Azure Blob 신용 데이터 행 하나 이상을 저장한 다음 Blob 위치를 서비스에 보냅니다. 서비스에서는 입력 Blob의 모든 데이터 행에 대한 점수를 매기고 결과를 다른 Blob에 저장한 다음 해당 컨테이너의 URL을 반환합니다.  

클래식 웹 서비스에 액세스하는 가장 빠르고 쉬운 방법은 [Azure ML 요청-응답 서비스 웹앱](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) 또는 [Azure ML Batch 실행 서비스 웹앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)을 통하는 것입니다.

이러한 웹앱 템플릿은 웹 서비스의 입력 데이터 및 예상 결과를 알고 있는 사용자 지정 웹앱을 구축할 수 있습니다. 따라서 웹 서비스 및 데이터에 액세스하도록 하기만 하면 나머지 작업은 템플릿이 수행합니다.

웹앱 템플릿 사용에 대한 자세한 내용은 [웹앱 템플릿을 사용한 Azure Machine Learning 웹 서비스 사용](consume-web-service-with-web-app-template.md)을 참조하세요.

또한 R, C# 및 Python 프로그래밍 언어에서 제공하는 시작 코드를 사용하여 웹 서비스에 액세스하는 사용자 지정 응용 프로그램을 개발할 수도 있습니다.

자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

