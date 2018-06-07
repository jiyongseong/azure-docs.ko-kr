---
title: Azure Machine Learning Studio로 파일 데이터 가져오기 | Microsoft Docs
description: 하드 드라이브에서 Azure Machine Learning Studio로 학습 데이터 파일을 업로드 하는 방법에 대해 알아봅니다. 이렇게 하면 작업 영역에 데이터 집합 모듈이 만들어집니다.
keywords: 데이터 가져오기, 데이터 형식, 데이터 유형, 데이터 원본, 학습 데이터
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: f7096ee9e887f13e5bff1f79b7e81702061b8dea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>하드 드라이브의 파일에서 Machine Learning Studio로 학습 데이터 가져오기
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Azure Machine Learning Studio에서 학습 데이터로 사용할 하드 드라이브의 데이터 파일을 업로드하는 방법에 대해 알아봅니다. 데이터 파일을 가져오면 작업 영역에서 사용할 수 있도록 데이터 집합 모듈을 준비할 수 있습니다.

## <a name="steps-to-import-data-from-a-local-file"></a>로컬 파일에서 데이터 가져오기
로컬 하드 드라이브에서 데이터를 가져오려면 다음을 수행합니다.

1. Machine Learning Studio 창 하단에 있는 **+새로 만들기** 를 클릭합니다.
2. **데이터 집합** 및 **로컬 파일에서**를 선택합니다.
3. **새 데이터 집합 업로드** 대화 상자에서 업로드할 파일을 찾아봅니다.
4. 이름을 입력하고, 데이터 형식을 식별하며, 선택적으로 설명을 입력합니다. 설명을 사용하면 나중에 데이터를 사용할 때 기억하려는 데이터에 대한 특성을 기록할 수 있으므로 좋습니다.
5. **기존 데이터 집합의 새로운 버전** 확인란을 사용하면 새 데이터로 기존 데이터 집합을 업데이트할 수 있습니다. 이 확인란을 클릭한 다음 기존 데이터 집합의 이름을 입력합니다.

![새 데이터 집합 업로드](./media/import-data-from-local-file/upload-dataset.png)

업로드 중에 파일이 업로드 중임을 나타내는 메시지가 표시됩니다. 업로드 시간은 데이터의 크기와 서비스에 대한 연결 속도에 따라 달라집니다. 파일이 오래 걸릴 것을 알고 있으면 기다리는 동안 Machine Learning Studio에서 다른 작업을 수행할 수 있습니다. 그러나 브라우저를 닫으면 데이터 업로드에 실패하게 됩니다.

## <a name="dataset-module-is-ready-for-use"></a>데이터 집합 모듈을 사용할 준비가 되었습니다.
데이터가 업로드되면 데이터 집합 모듈에 저장되고 작업 영역의 모든 실험에서 사용할 수 있습니다.

실험을 편집할 때 모듈 팔레트에 있는 **저장된 데이터 집합** 목록의 **내 데이터 집합** 목록에서 만든 데이터 집합을 찾을 수 있습니다. 추가 분석 및 기계 학습을 위해 데이터 집합을 사용하려고 할 때 데이터 집합을 실험 캔버스에 끌어서 놓을 수 있습니다.
