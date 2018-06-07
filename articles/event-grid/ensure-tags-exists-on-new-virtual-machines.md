---
title: "Azure Automation과 Event Grid 통합 | Microsoft Docs"
description: "새 VM을 만들 때 자동으로 태그를 추가하고 Microsoft Teams에 알림을 보내는 방법을 알아봅니다."
keywords: "자동화, runbook, 팀, 이벤트 그리드, 가상 머신, VM"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 9a4d6ecf19fc96a9c7b92cf246effbf3948fb478
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Azure Automation과 Event Grid 통합

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Event Grid 샘플 Runbook을 가져옵니다.
> * 선택적 Microsoft Teams 웹후크를 만듭니다.
> * Runbook에 대한 웹후크를 만듭니다.
> * Event Grid 구독을 만듭니다.
> * Runbook을 트리거하는 VM을 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure Event Grid 구독에서 트리거된 Runbook을 담을 [Azure Automation 계정](../automation/automation-offering-get-started.md)이 필요합니다.

## <a name="import-an-event-grid-sample-runbook"></a>Event Grid 샘플 Runbook 가져오기
1. Automation 계정을 열고 **Runbook** 페이지를 선택합니다.

   ![Runbook 선택](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. **갤러리 찾아보기** 단추를 선택합니다.

3. **Event Grid**를 검색하고 **Azure Automation과 Event grid 통합**을 선택합니다. 

    ![갤러리 Runbook 가져오기](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. **가져오기**를 선택하고 이름을 **Watch-VMWrite**로 지정합니다.

5. 가져온 후 **편집**을 선택하여 Runbook 소스를 확인합니다. **게시** 단추를 선택합니다.

## <a name="create-an-optional-microsoft-teams-webhook"></a>선택적 Microsoft Teams 웹후크 만들기
1. Microsoft Teams에서 채널 이름 옆의 **기타 옵션**을 선택하고 **커넥터**를 선택합니다.

    ![Microsoft Teams 연결](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. 커넥터 목록에서 **수신 웹후크**로 스크롤하고 **추가**를 선택합니다.

3. 이름에 **AzureAutomationIntegration**을 입력하고 **만들기**를 선택합니다.

4. 웹후크를 클립보드에 복사하고 저장합니다. 웹후크 URL은 Microsoft Teams에 정보를 보내는 데 사용됩니다.

5. **마침**을 클릭하여 웹후크를 저장합니다.

## <a name="create-a-webhook-for-the-runbook"></a>Runbook에 대한 웹후크 만들기
1. Watch-VMWrite Runbook을 엽니다.

2. **웹후크**를 선택하고 **웹후크 추가** 단추를 선택합니다.

3. 이름에 **WatchVMEventGrid**를 입력합니다. URL을 클립보드에 복사하고 저장합니다.

    ![웹후크 이름 구성](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. **매개 변수 구성 및 실행 설정 구성**을 선택하고 **CHANNELURL**에 Microsoft Teams 웹후크 URL을 입력합니다. **WEBHOOKDATA**는 빈 칸으로 둡니다.

    ![웹후크 매개 변수 구성](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. **확인**을 선택하여 Automation Runbook 웹후크를 만듭니다.


## <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기
1. **Automation 계정** 개요 페이지에서 **Event grid**를 선택합니다.

    ![Event Grid 선택](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. **+ 이벤트 구독** 단추를 선택합니다.

3. 다음 정보로 구독을 구성합니다.

    *   이름에 **AzureAutomation**을 입력합니다.
    *   **토픽 유형**에서 **Azure Subscriptions**를 선택합니다.
    *   **모든 이벤트 유형 구독** 확인란을 선택 취소합니다.
    *   **이벤트 유형**에서 **리소스 작성 성공**을 선택합니다.
    *   **구독자 엔드포인트**에서 Watch-VMWrite Runbook에 대해 웹후크 URL을 입력합니다. 
    *   **접두사 필터**에서 만든 새 VM을 검색하려는 리소스 그룹과 구독을 입력합니다. `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`처럼 표시되어야 합니다.

4. **만들기**를 선택하여 Event Grid 구독을 저장합니다.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Runbook을 트리거하는 VM 만들기
1. Event Grid 구독 접두사 필터에서 지정한 리소스 그룹에서 새 VM을 만듭니다.

2. Watch-VMWrite Runbook이 호출되고 새 태그가 VM에 추가되어야 합니다.

    ![VM 태그](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Microsoft Teams 채널에 새 메시지가 보내집니다.

    ![Microsoft Teams 알림](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Event Grid와 Automation 간의 통합을 설정했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Event Grid 샘플 Runbook을 가져옵니다.
> * 선택적 Microsoft Teams 웹후크를 만듭니다.
> * Runbook에 대한 웹후크를 만듭니다.
> * Event Grid 구독을 만듭니다.
> * Runbook을 트리거하는 VM을 만듭니다.

> [!div class="nextstepaction"]
> [Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](../event-grid/custom-event-quickstart.md)
