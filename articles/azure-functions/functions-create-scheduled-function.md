---
title: Azure에서 일정에 따라 실행되는 함수 만들기 | Microsoft Docs
description: Azure에서 정의한 일정에 따라 실행되는 함수를 만드는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9c961c06a01bba0b645088a19cd55e72a54396c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Azure에서 타이머에 따라 트리거되는 함수 만들기

Azure Functions를 사용하여 정의한 일정에 따라 실행되는 [서버를 사용하지 않는](https://azure.microsoft.com/overview/serverless-computing/) 함수를 만드는 방법을 알아봅니다.

![Azure Portal에서 함수 앱 만들기](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

+ Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-function-app"></a>Azure Function 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![함수 앱을 성공적으로 만들었습니다.](./media/functions-create-first-azure-function/function-app-create-success.png)

다음으로 새 함수 앱에서 함수를 만듭니다.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>타이머 트리거 함수 만들기

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 클릭합니다. 함수 앱에서 첫 번째 함수이면 **사용자 지정 함수**를 선택합니다. 그러면 함수 템플릿의 전체 집합이 표시됩니다.

    ![Azure Portal에서 함수 빨리 시작하기 페이지](./media/functions-create-scheduled-function/add-first-function.png)

2. 검색 필드에 `timer`를 입력한 다음 타이머 트리거 템플릿에서 원하는 언어를 선택합니다. 

    ![타이머 트리거 함수 템플릿을 선택합니다.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

3. 이미지 아래의 테이블에 지정된 설정을 사용하여 새 트리거를 구성합니다.

    ![Azure Portal에서 타이머 트리거 함수를 만듭니다.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | 설정 | 제안 값 | 설명 |
    |---|---|---|
    | **Name** | 기본값 | 타이머 트리거 함수의 이름을 정의합니다. |
    | **[일정](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | 1분마다 함수가 실행되도록 예약하는 6개 필드의 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)입니다. |

2. **만들기**를 클릭합니다. 함수는 1분마다 실행되는 선택한 언어로 생성됩니다.

3. 로그에 기록된 추적 정보를 확인하여 실행을 확인합니다.

    ![Azure Portal에서 함수 로그 뷰어.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

이제 함수의 일정을 변경하여 1분이 아니라 1시간에 한 번 실행되도록 합니다. 

## <a name="update-the-timer-schedule"></a>타이머 일정 업데이트

1. 함수를 확장하고 **통합**을 클릭합니다. 여기서 함수에 대한 입력 및 출력 바인딩을 정의하고 일정도 설정합니다. 

2. `0 0 */1 * * *`의 새 시간 단위 **일정** 값을 입력한 후 **저장**을 클릭합니다.  

![Azure Portal에서 함수 업데이트 타이머 일정.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

함수는 이제 한 시간마다 한 번씩 실행됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

일정에 따라 실행되는 함수를 만들었습니다.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

타이머 트리거에 대한 자세한 내용은 [Azure Functions를 사용하여 코드 실행 예약](functions-bindings-timer.md)을 참조하세요.
