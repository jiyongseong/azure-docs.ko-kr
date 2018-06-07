---
title: 가상 머신의 변경 사항 모니터링 - Azure Event Grid 및 Logic Apps | Microsoft Docs
description: Azure Event Grid와 Logic Apps를 사용하여 VM(가상 머신)의 구성 변경 사항을 확인합니다.
keywords: 논리 앱, 이벤트 표, 가상 컴퓨터, VM
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: ''
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: ea3063b5c445dab85a7ef1e5663c40efc34f961e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303116"
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Azure Event Grid와 Logic Apps를 사용하여 가상 머신의 변경 사항 모니터링

Azure 리소스 또는 타사 리소스에서 특정 이벤트가 발생하는 경우 자동화된 [논리 앱 워크플로](../logic-apps/logic-apps-overview.md)를 시작할 수 있습니다. 이러한 리소스는 해당 이벤트를 [Azure Event Grid](../event-grid/overview.md)에 게시할 수 있습니다. 이에 따라 Event Grid에서는 큐, 웹후크 또는 [이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)를 끝점으로 가지고 있는 구독자에게 해당 이벤트를 보냅니다. 구독자로서 논리 앱은 코드를 작성하지 않고 자동화된 워크플로를 실행하여 작업을 수행하기 전에 Event Grid에서 이러한 이벤트를 기다릴 수 있습니다.

예를 들어 게시자가 Azure Event Grid 서비스를 통해 구독자에게 보낼 수 있는 몇 가지 이벤트는 다음과 같습니다.

* 리소스를 만들고, 읽고, 업데이트하거나 삭제합니다. 예를 들어 Azure 구독에 요금이 부과되어 청구서에 영향을 줄 수 있는 변경 사항을 모니터링할 수 있습니다. 
* Azure 구독에서 사람을 추가하거나 제거합니다.
* 앱에서 특정 작업을 수행합니다.
* 새 메시지가 큐에 표시됩니다.

이 자습서에서는 가상 머신의 변경 내용을 모니터링하고 이러한 변경에 대한 전자 메일을 보내는 논리 앱을 만듭니다. Azure 리소스에 대한 이벤트 구독이 있는 논리 앱을 만드는 경우 이벤트가 Event Grid를 통해 해당 리소스에서 논리 앱으로 이동됩니다. 자습서에서는 이 논리 앱을 구축하는 과정을 설명합니다.

![개요 - Event Grid와 논리 앱으로 가상 머신 모니터링](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Event Grid를 통해 이벤트를 모니터링하는 논리 앱을 만듭니다.
> * 특별히 가상 머신 변경 내용을 확인하는 조건을 추가합니다.
> * 가상 컴퓨터가 변경될 때 전자 메일을 보냅니다.

## <a name="prerequisites"></a>필수 조건

* 알림 전송을 위한 [Azure Logic Apps에서 지원하는 전자 메일 공급자](../connectors/apis-list.md)(예: Office 365 Outlook, Outlook.com 또는 Gmail)의 전자 메일 계정. 이 자습서에서는 Office 365 Outlook을 사용합니다.

* [가상 머신](https://azure.microsoft.com/services/virtual-machines). 가상 머신이 아직 없는 경우 [VM 만들기 자습서](https://docs.microsoft.com/azure/virtual-machines/)를 통해 새로 만듭니다. 가상 머신에서 이벤트를 게시하기 위해 [필요한 별도의 작업은 없습니다](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Event Grid에서 이벤트를 모니터링하는 논리 앱을 만듭니다.

먼저 논리 앱을 만들고 가상 컴퓨터에 대한 리소스 그룹을 모니터링하는 Event Grid 트리거를 추가합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. Azure 주 메뉴의 왼쪽 위 모서리에서 **리소스 만들기** > **엔터프라이즈 통합** > **논리 앱**을 차례로 선택합니다.

   ![논리 앱 만들기](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. 다음 테이블에 지정된 설정을 사용하여 논리 앱을 만듭니다.

   ![논리 앱 세부 정보 제공](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *{your-logic-app-name}* | 고유한 논리 앱 이름을 제공합니다. | 
   | **구독** | *{your-Azure-subscription}* | 이 자습서의 모든 서비스에 대해 동일한 Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *{your-Azure-resource-group}* | 이 자습서의 모든 서비스에 대해 동일한 Azure 리소스 그룹을 선택합니다. | 
   | **위치**: | *{your-Azure-region}* | 이 자습서의 모든 서비스에 대해 동일한 하위 지역을 선택합니다. | 
   | | | 

4. 준비가 되면 **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

   이제 논리 앱에 대한 Azure 리소스를 만들었습니다. 
   Azure에서 논리 앱을 배포하면 Logic Apps 디자이너에서 일반적인 패턴에 대한 템플릿을 표시하므로 더 빨리 시작할 수 있습니다.

   > [!NOTE] 
   > **대시보드에 고정**을 선택하면 Logic Apps 디자이너에서 해당 논리 앱이 자동으로 열립니다. 그렇지 않으면 논리 앱을 수동으로 찾아 열 수 있습니다.

5. 이제 논리 앱 템플릿을 선택합니다. **템플릿** 아래에서 **비어 있는 논리 앱**을 선택하면 논리 앱을 처음부터 빌드할 수 있습니다.

   ![논리 앱 템플릿 선택](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   이제 Logic Apps 디자이너에 논리 앱을 시작하는 데 사용할 수 있는 [*커넥터*](../connectors/apis-list.md) 및 [*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)와 함께 작업을 수행하기 위해 트리거 후에 추가할 수 있는 작업이 표시됩니다. 트리거는 논리 앱 인스턴스를 만들고 논리 앱 워크플로를 시작하는 이벤트입니다. 
   논리 앱에는 첫 번째 항목으로 트리거가 필요합니다.

6. 검색 상자에 필터로 "event grid"를 입력합니다. **Azure Event Grid - On a resource event**(Azure Event Grid - 리소스 이벤트 시) 트리거를 선택합니다.

   !["Azure Event Grid - On a resource event"(Azure Event Grid - 리소스 이벤트 시) 트리거 선택](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. 메시지가 표시되면 Azure 자격 증명을 사용하여 Azure Event Grid에 로그인합니다.

   ![Azure 자격 증명으로 로그인](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > @outlook.com 또는 @hotmail.com과 같은 개인 Microsoft 계정으로 로그인하는 경우 Event Grid 트리거가 제대로 나타나지 않을 수 있습니다. 이 문제를 해결하기 위해 [서비스 주체와 연결](../azure-resource-manager/resource-group-create-service-principal-portal.md)을 선택하거나 Azure 구독과 연결된 Azure Active Directory의 구성원(예: *user-name*@emailoutlook.onmicrosoft.com)으로 인증합니다.

8. 이제 논리 앱을 게시자 이벤트에 구독합니다. 다음 테이블에 지정된 이벤트 구독에 대한 세부 정보를 제공합니다.

   ![이벤트 구독에 대한 세부 정보 제공](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **구독** | *{virtual-machine-Azure-subscription}* | 이벤트 게시자의 Azure 구독을 선택합니다. 이 자습서에서는 가상 머신에 대한 Azure 구독을 선택합니다. | 
   | **리소스 종류** | Microsoft.Resources.resourceGroups | 이벤트 게시자의 리소스 종류를 선택합니다. 이 자습서의 경우 논리 앱이 리소스 그룹만 모니터링하도록 지정된 값을 선택합니다. | 
   | **리소스 이름** | *{virtual-machine-resource-group-name}* | 게시자의 리소스 이름을 선택합니다. 이 자습서에서는 가상 머신에 대한 리소스 그룹의 이름을 선택합니다. | 
   | 옵션 설정의 경우 **고급 옵션 표시**를 선택합니다. | *{see descriptions}* | * **접두사 필터**: 이 자습서에서는 이 설정을 비워 둡니다. 기본 동작은 모든 값과 일치합니다. 그러나 접두사 문자열(예: 특정 리소스에 대한 경로 및 매개 변수)을 필터로 지정할 수 있습니다. <p>* **접미사 필터**: 이 자습서에서는 이 설정을 비워 둡니다. 기본 동작은 모든 값과 일치합니다. 그러나 특정 파일 형식만 원하는 경우 접미사 문자열(예: 파일 이름 확장명)을 필터로 지정할 수 있습니다.<p>* **구독 이름**: 이벤트 구독에 대한 고유한 이름을 제공합니다. |
   | | | 

   완료되면 Event Grid 트리거가 다음 예와 유사하게 나타납니다.
   
   ![Event Grid 트리거 세부 정보의 예](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다. 논리 앱에서 작업의 세부 정보를 접고 숨기려면 작업의 제목 표시줄을 선택합니다.

   ![논리 앱 저장](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Event Grid 트리거가 있는 논리 앱을 저장하면 Azure에서 사용자가 선택한 리소스에 대한 논리 앱의 이벤트 구독을 자동으로 만듭니다. 따라서 리소스가 이벤트를 Event Grid에 게시하면 이 Event Grid는 해당 이벤트를 논리 앱에 자동으로 푸시합니다. 이 이벤트는 다음 단계에서 정의하는 워크플로의 인스턴스를 만들고 실행하는 논리 앱을 트리거합니다.

논리 앱이 현재 라이브 상태이며 Event Grid의 이벤트를 수신 대기하지만, 워크플로에 작업을 추가하기 전까지는 아무 작업도 수행하지 않습니다. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>가상 머신의 변경 사항을 확인하는 조건 추가

특정 이벤트가 발생할 때만 논리 앱 워크플로를 실행하려면 가상 머신 “쓰기” 작업을 확인하는 조건을 추가합니다. 이 조건이 true인 경우 논리 앱에서 업데이트된 가상 머신에 대한 세부 정보가 포함된 전자 메일을 보냅니다.

1. 논리 앱 디자이너의 Event Grid 트리거 아래에서 **새 단계** > **조건 추가**를 선택합니다.

   ![논리 앱에 조건 추가](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Logic Apps 디자이너에서는 조건이 참인지 거짓인지를 기준으로 따라야 할 작업 경로를 포함하여 워크플로에 조건을 추가합니다.

   ![빈 조건](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. **조건** 상자에서 **고급 모드에서 편집**을 선택합니다.
다음 식을 입력합니다.

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   이제 조건은 다음 예와 같습니다.

   ![빈 조건](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   이 식은 `operationName` 속성이 `Microsoft.Compute/virtualMachines/write` 작업인 `data` 개체에 대해 `body` 이벤트를 확인합니다. 
   [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 대해 자세히 알아보세요.

3. 조건에 대한 설명을 제공하려면 조건 셰이프에서 **줄임표**(**...**) 단추를 선택한 다음 **이름 바꾸기**를 선택합니다.

   > [!NOTE] 
   > 또한 이 자습서의 뒷부분에 나오는 예제에는 논리 앱 워크플로의 단계에 대한 설명이 나와 있습니다.

4. 이제 **기본 모드에서 편집**을 선택하여 다음과 같이 식이 자동으로 해결되도록 합니다.

   ![논리 앱 조건](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. 논리 앱을 저장합니다.

## <a name="send-email-when-your-virtual-machine-changes"></a>가상 머신이 변경될 때 전자 메일 보내기

이제 지정한 조건이 참일 때 전자 메일을 받을 수 있도록 [*작업*](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다.

1. 조건의 **참인 경우** 상자에서 **작업 추가**를 선택합니다.

   ![조건이 true인 경우의 작업 추가](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. 검색 상자에서 필터로 “전자 메일”을 입력합니다. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 그런 다음 커넥터에 대한 "전자 메일 보내기" 작업을 선택합니다. 예:  

   * 예를 들어 Azure 회사 또는 학교 계정의 경우 Office 365 Outlook 커넥터를 선택합니다. 
   * Microsoft 개인 계정의 경우 Outlook.com 커넥터를 선택합니다. 
   * Gmail 계정의 경우 Gmail 커넥터를 선택합니다. 

   Office 365 Outlook 커넥터를 계속 사용합니다. 
   다른 공급자를 사용하는 경우 단계는 동일하지만 UI의 표시가 다를 수 있습니다. 

   !["전자 메일 보내기" 작업 선택](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. 전자 메일 공급자에 대한 연결이 아직 없는 경우 인증을 요청받을 때 전자 메일 계정에 로그인합니다.

4. 다음 테이블에 지정된 대로 전자 메일에 대한 세부 정보를 제공합니다.

   ![빈 전자 메일 작업](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 워크플로에 사용할 수 있는 필드를 선택하려면 **동적 콘텐츠** 목록이 열리는 편집 상자를 클릭하거나 **동적 콘텐츠 추가**를 선택합니다. 더 많은 필드는 목록에서 각 섹션에 대한 **자세히 보기**를 선택하세요. **동적 콘텐츠** 목록을 닫으려면 **동적 콘텐츠 추가**를 선택합니다.

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **To** | *{recipient-email-address}* |수신자의 이메일 주소를 입력합니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. | 
   | **제목** | 업데이트된 리소스: **제목**| 전자 메일의 제목에 콘텐츠를 입력합니다. 이 자습서의 경우 제안된 텍스트를 입력하고 이벤트의 **제목** 필드를 선택합니다. 여기에서 전자 메일 제목에는 업데이트된 리소스(가상 머신)에 대한 이름이 포함됩니다. | 
   | **본문** | 리소스 그룹: **토픽** <p>이벤트 유형: **이벤트 유형**<p>이벤트 ID: **ID**<p>시간: **이벤트 시간** | 전자 메일의 본문에 콘텐츠를 입력합니다. 이 자습서의 경우 제안된 텍스트를 입력하고 이벤트의 **토픽**, **이벤트 유형**, **ID** 및 **이벤트 시간** 필드를 선택하여 업데이트에 대한 리소스 그룹 이름, 이벤트 유형, 이벤트 타임스탬프 및 이벤트 ID가 전자 메일에 포함되도록 합니다. <p>콘텐츠에 빈 줄을 추가하려면 Shift + Enter 키를 누릅니다. | 
   | | | 

   > [!NOTE] 
   > 배열을 나타내는 필드를 선택하면 디자이너에서 배열을 참조하는 작업 주위에 **For each** 루프를 자동으로 추가합니다. 그렇게 하면 논리 앱에서 각 배열 항목에 대한 해당 작업을 수행합니다.

   이제 전자 메일 작업이 다음 예와 같이 표시됩니다.

   ![전자 메일에 포함될 출력 선택](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   완료된 논리 앱이 다음 예와 같이 표시됩니다.

   ![완료된 논리 앱](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. 논리 앱을 저장합니다. 논리 앱에서 각 작업의 세부 정보를 접고 숨기려면 작업의 제목 표시줄을 선택합니다.

   ![논리 앱 저장](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   논리 앱이 현재 라이브 상태이지만 어떤 작업을 수행하기 전에 가상 머신에 대한 변경을 기다리고 있습니다. 
   이제 논리 앱을 테스트하려면 다음 섹션으로 이동하세요.

## <a name="test-your-logic-app-workflow"></a>논리 앱 워크플로 테스트

1. 지정한 이벤트를 논리 앱에서 받고 있는지 확인하려면 가상 머신을 업데이트합니다. 

   예를 들어 Azure Portal에서 가상 머신의 크기를 조정하거나 [Azure PowerShell로 VM 크기를 조정](../virtual-machines/windows/resize-vm.md)할 수 있습니다. 

   몇 분 후에 전자 메일을 받아야 합니다. 예: 

   ![가상 머신 업데이트에 대한 전자 메일](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. 논리 앱에 대한 실행 및 트리거 기록을 검토하려면 논리 앱 메뉴에서 **개요**를 선택합니다. 실행에 대한 자세한 세부 정보를 보려면 해당 실행에 대한 행을 선택합니다.

   ![논리 앱 실행 기록](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. 각 단계에 대한 입력 및 출력을 보려면 검토하려는 단계를 확장합니다. 이 정보는 논리 앱의 문제를 진단하고 디버깅하는 데 유용합니다.
 
   ![논리 앱 실행 기록 세부 정보](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

축하합니다! Event Grid를 통해 리소스 이벤트를 모니터링하고 이러한 이벤트가 발생하면 전자 메일을 보내는 논리 앱을 만들고 실행했습니다. 또한 프로세스를 자동화하고 시스템과 클라우드 서비스를 통합하는 워크플로를 쉽게 만들 수 있는 방법에 대해 알아보았습니다.

예를 들어 Event Grid 및 논리 앱을 사용하여 다른 구성 변경 내용을 모니터링할 수 있습니다.

* 가상 머신에서 RBAC(역할 기반 액세스 제어) 권한을 가져옵니다.
* NIC(네트워크 인터페이스)의 NSG(네트워크 보안 그룹)가 변경되었습니다.
* 가상 컴퓨터용 디스크가 추가되거나 제거되었습니다.
* 공용 IP 주소가 가상 머신 NIC에 할당되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 리소스를 사용하고 Azure 구독에 요금을 부과하는 작업을 수행합니다. 따라서 이 자습서를 완료하고 테스트할 때 요금이 부과되지 않도록 리소스를 사용하지 않도록 설정하거나 삭제해야 합니다.

* 작업을 삭제하지 않고 논리 앱의 실행을 중지하려면 앱을 사용하지 않도록 설정합니다. 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **사용 안 함**을 선택합니다.

  ![논리 앱 사용 해제](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

* 논리 앱을 영구적으로 삭제하려면 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **삭제**를 선택합니다. 논리 앱을 삭제하려 한다고 확인한 후 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](../event-grid/custom-event-quickstart.md)
