---
title: Azure Monitor에서 새 경고 환경을 사용하여 활동 로그 만들기 및 관리 | Microsoft Docs
description: 이 문서에서는 Azure Monitor 아래의 경고 탭에서 활동 로그 경고를 만드는 방법에 대한 정보를 제공합니다.
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: 070acc6fb6f6f744474edeef0e914dda9d24c558
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202823"
---
# <a name="create-activity-log-alerts-using-the-new-alerts-experience"></a>새 경고 환경을 사용하여 활동 로그 경고 만들기

활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생할 때 활성화되는 경고입니다.

이 경고는 Azure 리소스에 대한 것이며, Azure Resource Manager 템플릿을 사용하여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 이 문서에서는 활동 로그 경고에 대한 개념을 소개합니다. 그런 다음, Azure Portal에서 [Azure Alerts](monitoring-overview-unified-alerts.md)의 새로운 환경을 사용하여 활동 로그 이벤트에 대한 경고를 설정하는 방법을 보여줍니다.

일반적으로 Azure 구독의 리소스에서 특정 변경이 발생할 때 알림을 받기 위해 활동 로그 경고를 만들며, 특정 리소스 그룹 또는 리소스로 범위를 지정하는 경우가 많습니다. 예를 들어 (샘플 리소스 그룹) **myProductionResourceGroup**의 가상 머신이 삭제될 때 알림을 받거나, 구독의 사용자에게 새 역할이 할당될 경우 알림을 받고 싶을 수 있습니다.

활동 로그 이벤트에 대한 JSON 개체의 모든 최상위 속성에 따라 활동 로그 경고를 구성할 수 있습니다. 그러나 포털에는 다음 섹션에서 자세히 설명하는 가장 일반적인 옵션이 표시됩니다.

>[!NOTE]

> 범주가 “관리”인 경우, 앞에 나온 조건 중 하나 이상을 경고에 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.
>

활동 로그 경고가 활성화되면 작업 그룹을 사용하여 작업 또는 알림을 생성합니다. 작업 그룹은 이메일 주소, 웹후크 URL 또는 SMS 전화 번호와 같은 알림 수신자의 재사용 가능한 집합입니다. 수신자를 여러 경고에서 참조하여 알림 채널을 집중화하고 그룹화할 수 있습니다. 활동 로그 경고를 정의하는 경우 두 가지 옵션이 있습니다. 다음을 수행할 수 있습니다.

* 활동 로그 경고에서 기존 작업 그룹을 사용합니다.
* 새 작업 그룹을 만듭니다.

작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](monitoring-action-groups.md)를 참조하세요.

서비스 상태 알림에 대해 자세히 알아보려면 [서비스 상태 알림에서 활동 로그 경고 수신](monitoring-activity-log-alerts-on-service-notifications.md)을 참조하세요.


## <a name="whats-new-in-alerts-for-activity-logs"></a>활동 로그에 대한 경고의 새로운 기능은 무엇인가요?

이제 [Azure Alerts](monitoring-overview-unified-alerts.md)에서 활동 로그 경고에 대한 향상된 사용자 환경을 제공합니다. [경고에 대한 향상된 사용자 환경](monitoring-overview-unified-alerts.md)을 통해 이제 다음을 수행할 수 있습니다.

- **모니터** > **경고** 블레이드에서 활동 로그 경고 규칙을 [생성](#create-an-alert-rule-for-an-activity-log) 및 [관리](#view-and-manage-activity-log-alert-rules)합니다. [활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보세요.

- **경고 대상에 대한 새 옵션**: 새 활동 로그 경고 규칙을 만드는 동안 이제 대상 리소스 또는 리소스 그룹이나 구독을 선택할 수 있습니다.


## <a name="create-an-alert-rule-for-an-activity-log"></a>활동 로그에 대한 경고 규칙 만들기

> [!NOTE]

>  경고 규칙을 만드는 동안 다음을 확인합니다.

> - 범위의 구독이 경고가 생성된 구독과 다르지 않습니다.
- 조건은 경고가 구성된 수준/상태/호출자/리소스 그룹/리소스 ID/리소스 유형/이벤트 범주여야 합니다.
- 경고 구성 JSON에 “anyOf” 조건 또는 중첩된 조건이 없습니다(기본적으로 추가 allOf/anyOf 없이 하나의 allOf만 허용됨).


이렇게 하려면 다음 절차를 수행합니다.

1. Azure Portal에서 **모니터** > **경고**를 선택합니다.
2. **경고** 창의 맨 위에서 **새 경고 규칙**을 클릭합니다.

     ![새 경고 규칙](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     **규칙 만들기** 창이 나타납니다.

      ![새 경고 규칙 옵션](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **경고 조건 정의**에서 다음 정보를 제공하고 **완료**를 클릭합니다.

    - **경고 대상:** 새 경고의 대상을 보고 선택하려면 **구독별로 필터링** / **리소스 종류별로 필터링**을 사용하고 표시되는 목록에서 리소스 또는 리소스 그룹을 선택합니다.

    > [!NOTE]

    > 리소스, 리소스 그룹 또는 전체 구독을 선택할 수 있습니다.

    **경고 대상 샘플 보기**

     ![대상 선택](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - **대상 조건** 아래에서 **조건 추가**를 클릭하고, 신호 유형을 **활동 로그**로 선택합니다.

    - 표시되는 목록에서 신호를 선택합니다.

    이 대상 신호에 대한 로그 기록 타임라인 및 해당 경고 논리를 선택할 수 있습니다.

    **조건 추가 화면**

    ![조건 추가](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **기록 시간**: 지난 6/12/24시간, 지난 1주.

    **경고 논리**:

     - **이벤트 수준**: 이벤트의 심각도 수준(**자세한 정보, 정보, 경고, 오류** 또는 **중요**)입니다.
     - **상태**: 이벤트의 상태로, **시작됨, 실패** 또는 **성공**입니다.
     - **이벤트에 의해 시작**: 호출자로도 알려져 있습니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

        **경고 논리가 적용되는 샘플 신호 그래프** :

        ![ 선택한 기준](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. **경고 규칙 세부 정보 정의**에서 다음 세부 정보를 제공합니다.

    - **경고 규칙 이름** - 새 경고 규칙의 이름
    - **설명** - 새 경고 규칙에 대한 설명
    - **리소스 그룹에 경고 저장** - 이 새 규칙을 저장할 리소스 그룹을 선택합니다.

5. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들어](monitoring-action-groups.md) 새 규칙에 할당합니다. 새 그룹을 만들려면 **+ 새 그룹**을 클릭합니다.

6. 규칙을 만든 후 사용하도록 설정하려면 **규칙을 만들면 바로 사용**에 대해 **예**를 클릭합니다.
7. **경고 규칙 만들기**를 클릭합니다.

    활동 로그에 대한 새 경고 규칙이 생성되고 창의 오른쪽 위에 확인 메시지가 나타납니다.

    ![ 경고 규칙 생성됨](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. 활동 로그 규칙 관리에 대해 [자세히 알아보세요](#view-and-manage-activity-log-alert-rules).

## <a name="view-and-manage-activity-log-alert-rules"></a>활동 로그 경고 규칙 보기 및 관리

1. Azure Portal에서 **모니터** > **경고**를 클릭하고 창의 왼쪽 위에서 **규칙 관리**를 클릭합니다.

    ![ 경고 규칙 관리](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    사용 가능한 규칙 목록이 나타납니다.

2. 수정할 활동 로그 규칙을 검색합니다.

    ![ 활동 로그 경고 규칙 검색](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    사용 가능한 필터(**구독**, **리소스 그룹** 또는 **리소스**)를 사용하여 편집할 활동 규칙을 찾을 수 있습니다.

    > [!NOTE]

    > **설명** , **대상 조건** 및 **작업 그룹**만 편집할 수 있습니다.

3.  규칙을 선택하고 두 번 클릭하여 규칙 옵션을 편집합니다. 필요에 따라 변경하고 **저장**을 클릭합니다.

    ![ 경고 규칙 관리](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  규칙을 사용/사용하지 않도록 설정하거나 삭제할 수 있습니다. 2단계에서 설명한 대로 규칙을 선택한 후 창의 맨 위에서 적절한 옵션을 선택합니다.


## <a name="next-steps"></a>다음 단계

- [활동 로그 경고 보관](monitoring-archive-activity-log.md)
- [활동 로그를 Event Hubs로 스트리밍](monitoring-stream-activity-logs-event-hubs.md)
- [활동 로그로 마이그레이션](monitoring-migrate-management-alerts.md)
