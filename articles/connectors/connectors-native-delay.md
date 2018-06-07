---
title: "논리 앱에 지연 추가 | Microsoft Docs"
description: "지연 및 다음 기간까지 지연 동작에 대한 개요와 Azure 논리 앱에서 이를 사용하는 방법을 설명합니다."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 6cde5b8ba8d770a07199816286b666e952394de1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>지연 및 다음 기간까지 지연 동작 시작
지연 및 다음 기간까지 지연 동작을 사용하여 워크플로 시나리오를 완료할 수 있습니다.

예를 들어 다음을 수행할 수 있습니다.

* 평일까지 기다렸다가 전자 메일로 상태 업데이트 전송
* 다시 시작하고 결과를 검색하기 전에 HTTP 호출이 완료될 때까지 워크플로 지연

논리 앱에서 지연 동작 사용을 시작하려면 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="use-the-delay-actions"></a>지연 작업 사용
동작은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다](connectors-overview.md).

논리 앱에서 지연 단계는 사용하는 방법의 예제 시퀀스는 다음과 같습니다.

1. 트리거를 추가한 후 **새 단계** 를 클릭하여 동작을 추가합니다.
2. **delay** 를 검색하여 지연 동작을 불러옵니다. 이 예제에서는 **지연**을 선택합니다.
   
    ![지연 작업](./media/connectors-native-delay/using-action-1.png)
3. 동작 속성을 완료하여 지연을 구성합니다.
   
    ![지연 구성](./media/connectors-native-delay/using-action-2.png)
4. **저장** 을 클릭하여 논리 앱을 게시하고 활성화합니다.

## <a name="action-details"></a>작업 세부 정보
되풀이 트리거에는 구성할 수 있는 다음과 같은 속성이 있습니다.

### <a name="delay-action"></a>지연 동작
이 동작은 특정 시간 간격 동안 실행을 지연합니다.
A*는 필수 필드 임을 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| Count* |count |지연할 시간 단위 수 |
| Unit* |단위 |시간 단위: `Second`, `Minute`, `Hour` 또는 `Day` |

<br>

### <a name="delay-until-action"></a>다음 기간까지 지연 동작
이 동작은 지정된 날짜/시간까지 실행을 지연합니다.
*는 필수 필드임을 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| Year* |timestamp |다음까지 지연 연도(GMT) |
| Month* |timestamp |다음가지 지연 월(GMT) |
| Day* |timestamp |다음까지 지연 일(GMT) |

<br>

## <a name="next-steps"></a>다음 단계
이제 플랫폼을 사용해 보고 [논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

