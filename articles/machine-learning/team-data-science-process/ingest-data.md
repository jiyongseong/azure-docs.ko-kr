---
title: 분석용 Azure 저장소 환경에 데이터 로드 | Microsoft Docs
description: Azure Blob Storage의 데이터 이동
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 369b06ee22b30977affb3642f0d321e5d92e6c41
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="load-data-into-storage-environments-for-analytics"></a>분석용 저장소 환경에 데이터 로드
팀 데이터 과학 프로세스는 프로세스의 각 단계에서 가장 적절한 방법으로 처리되거나 분석되도록 데이터가 다양한 저장소 환경에 수집되거나 로드되어야 합니다. 처리하기 위해 일반적으로 사용하는 데이터 대상에는 Azure Blob Storage, SQL Azure 데이터베이스, Azure VM의 SQL Server, HDInsight(Hadoop) 및 Azure Machine Learning이 포함됩니다. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

이 **메뉴** 는 데이터가 저장되고 처리되는 이러한 대상 환경으로 데이터를 수집하는 방법을 설명하는 토픽에 연결됩니다.

데이터의 처음 위치, 형식 및 크기는 물론 기술 및 비즈니스 요구에 따라 분석의 목표를 달성하기 위해 데이터를 수집해야 하는 대상 환경이 결정됩니다. 예측 모델을 구성하는 데 필요한 다양한 작업을 구현하기 위해 데이터가 여러 환경 사이를 이동해야 하는 시나리오는 일반적이지 않습니다. 이 작업 시퀀스에는 데이터 탐색, 사전 처리, 정리, 다운 샘플링, 모델 학습 등이 포함될 수 있습니다.

