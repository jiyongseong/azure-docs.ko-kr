---
title: Azure Machine Learning의 새로운 기능 | Microsoft Docs
description: Azure Machine Learning에서 사용할 수 있는 새로운 기능입니다.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: e0bf53670895c81379dcc2cd58542a8e3b3bc585
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Azure Machine Learning의 새로운 기능

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Microsoft Azure Machine Learning 업데이트 2017년 3월 릴리스에서는 다음 기능을 제공합니다.



* Azure Machine Learning BES 작업에 대한 전용 용량

    Machine Learning 배치 풀 처리는 Azure Machine Learning 배치 실행 서비스(BES)에 고객 관리 규모를 제공하는 데 [Azure Batch](../../batch/batch-technical-overview.md) 서비스를 사용합니다. Batch 풀 처리를 사용하면 배치 작업을 제출할 수 있는 Azure Batch 풀을 만들고 예측 가능한 방식으로 실행할 수 있습니다.

    자세한 내용은 [Machine Learning 작업에 대한 Azure Batch 서비스](dedicated-capacity-for-bes-jobs.md)를 참조하세요.


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Microsoft Azure Machine Learning 업데이트 2016년 8월 릴리스에서는 다음 기능을 제공합니다.
* 이제 기존 웹 서비스를 새로운 [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에서 관리할 수 있습니다. 이를 통해 웹 서비스의 모든 측면을 한 곳에서 관리할 수 있습니다.    
  * 웹 서비스 [사용 통계](manage-new-webservice.md)를 제공합니다.
  * 샘플 데이터를 사용하여 Azure Machine Learning 원격 요청 호출 테스트를 간소화합니다.
  * 샘플 데이터 및 작업 제출 내역을 포함하는 새 BES(Batch 실행 서비스) 테스트 페이지를 제공합니다.
  * 손쉬운 끝점 관리를 제공합니다.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Microsoft Azure Machine Learning 업데이트 2016년 7월 릴리스에서는 다음 기능을 제공합니다.
* 이제 웹 서비스는 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 인터페이스를 통해 관리되는 Azure 리소스처럼 관리되며 다음과 같은 향상된 기능을 제공합니다.
  * Resource Manager 기반 웹 서비스를 배포하고 관리하는 새로운 [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx)가 포함됩니다.
  * 웹 서비스의 모든 측면을 한 곳에서 관리할 수 있도록 해 주는 새로운 [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털이 포함됩니다.
* 웹 서비스에 Resource Manager 리소스 공급자를 활용하는 Resource Manager 기반 API를 사용하여 새로운 구독 기반 다중 지역 웹 서비스 배포를 통합합니다.
* 청구를 위해 새 Resource Manager RP를 사용하여 새 [가격 책정 계획](https://azure.microsoft.com/pricing/details/machine-learning/) 및 계획 관리 기능을 소개합니다.
  * 이제 각 지역에 구독을 만들 필요 없이 [여러 지역에 웹 서비스를 배포](how-to-deploy-to-multiple-regions.md) 할 수 있습니다.
* 웹 서비스 [사용 통계](manage-new-webservice.md)를 제공합니다.
* 샘플 데이터를 사용하여 Azure Machine Learning 원격 요청 호출 테스트를 간소화합니다.
* 샘플 데이터 및 작업 제출 내역을 포함하는 새 BES(Batch 실행 서비스) 테스트 페이지를 제공합니다.

또한 새 웹 서비스 모델에 배포하거나 기존 웹 서비스 모델에 계속해서 배포할 수 있도록 Machine Learning 스튜디오가 업데이트되었습니다. 

