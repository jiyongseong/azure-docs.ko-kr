---
title: Machine Learning에서 웹 서비스 끝점 만들기 | Microsoft Docs
description: Azure Machine Learning에서 웹 서비스 끝점 만들기
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: fac284e9f0c852306d99733a879fc13c85f07768
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="creating-endpoints"></a>끝점 만들기
> [!NOTE]
>  이 항목에서는 **기존** Machine Learning 웹 서비스에 적용되는 기술을 설명합니다.
> 
> 

고객에게 판매할 웹 서비스를 만들 때는 웹 서비스를 만든 실험에 아직 연결되어 있는 각 고객에게 학습된 모델을 제공해야 합니다. 또한 사용자 지정을 덮어쓰지 않고 실험의 모든 업데이트를 끝점에 선택적으로 적용해야 합니다.

이를 위해 Azure Machine Learning을 사용하면 배포된 웹 서비스에 대한 여러 끝점을 만들 수 있습니다. 웹 서비스의 각 끝점은 독립적으로 처리, 제한 및 관리됩니다. 각 끝점에는 고객에게 배포할 수 있는 고유한 URL 및 권한 부여 키가 있습니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>웹 서비스에 끝점 추가
웹 서비스에 끝점을 추가하는 두 가지 방법이 있습니다.

* 프로그래밍 방식
* Azure Machine Learning 웹 서비스 포털을 통해

끝점이 만들어지면 동기 API, 일괄 처리 API 및 Excel 워크시트를 통해 끝점을 사용할 수 있습니다. 또한 이 UI 통해 끝점을 추가하는 것 외에도 끝점 관리 API를 사용하여 프로그래밍 방식으로 끝점을 추가할 수 있습니다.

> [!NOTE]
> 웹 서비스에 끝점을 더 추가한 경우 기본 끝점은 삭제할 수 없습니다.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>프로그래밍 방식으로 끝점 추가
[AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 샘플 코드를 사용하여 프로그래밍 방식으로 웹 서비스에 끝점을 추가할 수 있습니다.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning 웹 서비스 포털을 사용하여 끝점 추가
1. Machine Learning Studio의 왼쪽 탐색 열에서 Web Services를 클릭합니다.
2. 웹 서비스 대시보드 아래쪽에서 **끝점 관리**를 클릭합니다. Azure Machine Learning 웹 서비스 포털에 웹 서비스 끝점 페이지가 열립니다.
3. **새로 만들기**를 클릭합니다.
4. 새 끝점에 대한 이름 및 설명을 입력합니다. 끝점 이름은 길이가 24자 이하이고 알파벳 소문자 또는 숫자로 구성되어야 합니다. 로깅 수준 및 예제 데이터 사용 여부를 선택합니다. 로깅에 대한 자세한 내용은 [Machine Learning 웹 서비스에 대한 로깅 사용](web-services-logging.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md).

