---
title: Log Analytics에서 Azure Automation 계정 연결 해제
description: 이 아티클에서는 Log Analytics 작업 영역에서 Azure Automation 계정 연결을 해제하는 방법을 대략적으로 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f51103045f6a0cac1b1ed4f32200eaf7bef9cf24
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Log Analytics에서 Automation 계정 연결을 해제하는 방법

Azure Automation은 Log Analytics와 통합되어 모든 Automation 계정의 runbook 작업에 대한 모니터링을 지원할 뿐만 아니라, Log Analytics에 의존하는 다음 솔루션을 가져올 때도 필요합니다.

* [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)
* [변경 내용 추적](../log-analytics/log-analytics-change-tracking.md)
* [작업이 없는 동안 VM 시작/중지](automation-solution-vm-management.md)

Automation 계정을 Log Analytics에 더 이상 통합하지 않기로 결정할 경우 Azure Portal에서 직접 계정 연결을 해제할 수 있습니다.  계속하기 전에 앞에서 언급한 솔루션을 제거해야 합니다. 그러지 않으면 이 프로세스가 계속 진행되지 않습니다. 가져온 특정 솔루션에 대한 항목을 검토하여 제거에 필요한 단계를 이해하세요.

이러한 솔루션을 제거한 후에 다음 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 솔루션에서 자동화 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

## <a name="unlink-workspace"></a>작업 영역 연결 해제

1. Azure Portal에서 Automation 계정을 열고 Automation 계정 페이지에서 왼쪽의 **관련된 리소스** 섹션 아래의 **작업 영역 연결 해제**를 선택합니다.

   ![작업 영역 연결 해제 옵션](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭합니다.

   ![작업 영역 연결 해제 페이지](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png)에서도 확인할 수 있습니다.

   계속할지 묻는 메시지가 나타납니다.

1. Azure Automation이 Log Analytics에서 계정 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상태를 추적할 수 있습니다.

업데이트 관리 솔루션을 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* 업데이트 일정.  각 일정에는 사용자가 만든 업데이트 배포와 일치하는 이름이 지정됩니다.

* 솔루션에 대해 생성된 Hybrid Worker 그룹.  각 그룹에는 machine1.contoso.com_9ceb8108-26 c 9-4051-b6b3-227600d715c8과 비슷하게 이름이 지정됩니다.

작업이 없는 동안 VM 시작/중지를 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* VM runbook 시작 및 중지 일정
* VM runbook 시작 및 중지
* variables

## <a name="next-steps"></a>다음 단계

Log Analytics와 통합되도록 Automation 계정을 다시 구성하려면 [Automation에서 Log Analytics로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.