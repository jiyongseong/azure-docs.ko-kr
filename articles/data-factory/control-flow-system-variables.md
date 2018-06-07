---
title: Azure Data Factory의 시스템 변수 | Microsoft Docs
description: 이 문서에서는 Azure Data Factory에서 지원하는 시스템 변수에 대해 설명합니다. 데이터 팩터리 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 77285692846d2a301e091426801e20203816181a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory에서 지원하는 시스템 변수
이 문서에서는 Azure Data Factory에서 지원하는 시스템 변수에 대해 설명합니다. 데이터 팩터리 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1의 함수와 변수](v1/data-factory-functions-variables.md)를 참조하세요.


## <a name="pipeline-scope"></a>파이프라인 범위:

| 변수 이름 | 설명 |
| --- | --- |
| @pipeline().DataFactory |안에서 파이프라인 실행이 진행 중인 데이터 팩터리의 이름 | 
| @pipeline().Pipeline |파이프라인의 이름  |
| @pipeline().RunId | 특정 파이프라인 실행의 ID | 
| @pipeline().TriggerType | (수동, 스케줄러) 파이프라인을 호출하는 트리거 유형 | 
| @pipeline().TriggerId| 파이프라인을 호출하는 트리거 ID |
| @pipeline().TriggerName| 파이프라인을 호출하는 트리거 이름 |
| @pipeline().TriggerTime| 트리거가 파이프라인을 호출한 시간. 트리거 시간은 예약 시간이 아니라 실제 발생 시간입니다. 예를 들어 `13:20:00.00Z` 대신 `13:20:08.0149599Z`를 반환합니다. |

## <a name="trigger-scope"></a>트리거 범위:

| 변수 이름 | 설명 |
| --- | --- |
| trigger().scheduledTime |트리거가 파이프라인 실행을 호출하도록 예약된 시간입니다. 예를 들어 5분마다 발생하는 트리거의 경우 이 변수는 각각 `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z`를 반환합니다.|
| trigger().startTime |트리거가 파이프라인 실행을 **실제로** 실행한 시간입니다. 예를 들어 5분마다 발생하는 트리거의 경우 이 변수는 각각 `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z`를 반환할 수 있습니다.|

## <a name="next-steps"></a>다음 단계
이러한 변수가 식에서 사용되는 방법은 [식 언어 및 함수](control-flow-expression-language-functions.md)를 참조하세요. 
