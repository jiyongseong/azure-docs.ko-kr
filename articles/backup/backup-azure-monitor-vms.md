---
title: Azure Virtual Machines에 대한 백업 경고 모니터링 | Microsoft Docs
description: Azure Virtual Machine 백업 작업의 이벤트 및 경고를 모니터링합니다. 경고에 기반한 전자 메일을 보냅니다.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: fbdce5c244d733a2978d473f01c8d875cbeaa65e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Azure 가상 컴퓨터 백업에 대한 경고 모니터링
경고는 이벤트 임계값을 만족하거나 초과한 서비스에서 나오는 응답입니다. 문제가 시작되는 시기를 아는 것은 비즈니스 비용을 낮게 유지하는 데 중요할 수 있습니다. 일반적으로 경고는 일정에 따라 발생하지 않으므로 경고가 발생한 후 가능한 빨리 아는 것이 유용합니다. 예를 들어 백업 또는 복원 작업이 실패할 경우 실패의 5분 내에서 경고가 발생합니다. 자격 증명 모음 대시보드의 Backup 경고 타일에 중요 및 경고 수준 이벤트를 표시합니다. Backup 경고 설정에서 모든 이벤트를 볼 수 있습니다. 그러나 별개의 문제에 대해 작업하는 경우 경고가 발생하면 어떻게 하나요? 경고가 발생하는 시기를 모르면 조금 불편할 수도 있고 데이터를 손상시킬 수도 있습니다. 정확한 담당자가 경고를 인식하게 하려면 경고가 발생할 때 전자 메일을 통해 경고 알림을 보내도록 서비스를 구성합니다. 전자 메일 알림 설정에 대한 자세한 내용은 [알림 구성](backup-azure-monitor-vms.md#configure-notifications)을 참조하세요.

## <a name="how-do-i-find-information-about-the-alerts"></a>경고에 대한 정보를 찾으려면 어떻게 해야 합니까?
경고를 발생시킨 이벤트에 관한 정보를 보려면 Backup 경고 섹션을 열어야 합니다. 두 가지 방법으로: 즉, 자격 증명 모음 대시보드의 Backup 경고 타일에서 또는 경고 및 이벤트 섹션에서 Backup 경고 섹션을 열 수 있습니다.

Backup 경고 타일에서 Backup 경고 블레이드를 열려면:

* 자격 증명 모음 대시보드의 **Backup 경고** 타일에서 **중요** 또는 **경고**를 클릭하여 해당 심각도 수준에서 작동할 수 있는 이벤트를 봅니다.

    ![Backup 경고 타일](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

경고 및 이벤트 섹션에서 Backup 경고 블레이드를 열려면:

1. 자격 증명 모음 대시보드에서 **모든 설정**을 클릭합니다. ![모든 설정 단추](./media/backup-azure-monitor-vms/all-settings-button.png)
2. **설정** 블레이드에서 **경고 및 이벤트**를 클릭합니다. ![경고 및 이벤트 단추](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. **경고 및 이벤트** 블레이드에서 **Backup 경고**를 클릭합니다. ![Backup 경고 단추](./media/backup-azure-monitor-vms/backup-alerts.png)

    **Backup 경고** 섹션이 열리고 필터링된 경고가 표시됩니다.

    ![Backup 경고 타일](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. 특정 경고에 관한 세부 정보를 보려면 이벤트 목록에서 경고를 클릭하여 해당 **세부 정보** 섹션을 엽니다.

    ![이벤트 세부 정보](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    목록에 표시된 특성을 사용자 지정하려면 [추가 이벤트 특성 보기](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>알림 구성
 지난 한 시간 동안 발생한 경고에 대해 또는 특정 유형의 이벤트가 발생할 때 전자 메일 알림을 보내도록 서비스를 구성할 수 있습니다.

경고에 대한 전자 메일 알림을 설정하려면

1. Backup 경고 메뉴에서 **알림 구성**

    ![Backup 경고 메뉴](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    알림 구성 섹션이 열립니다.

    ![알림 구성 블레이드](./media/backup-azure-monitor-vms/configure-notifications.png)
2. 알림 구성 섹션에서 전자 메일 알림에 대해 **켜기**를 클릭합니다.

    받는 사람 및 심각도 대화 상자에는 해당 정보가 필수이기 때문에 별표가 표시됩니다. 최소 하나 이상의 전자 메일 주소를 제공하고 최소 하나 이상의 심각도를 선택합니다.
3. **받는 사람(전자 메일)** 대화 상자에 알림을 받을 전자 메일 주소를 입력합니다. 사용할 형식: username@domainname.com. 세미콜론(;)을 사용하여 여러 전자 메일 주소를 구분합니다.
4. **알림** 영역에서 **경고별**을 선택하여 지정된 경고가 발생하면 알림을 보내거나 **시간별 요약**을 선택하여 지난 한 시간 동안의 요약을 보냅니다.
5. **심각도** 대화 상자에서 전자 메일 알림을 트리거할 수준을 하나 이상 선택합니다.
6. **저장**을 클릭합니다.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Azure IaaS VM 백업에 사용할 수 있는 경고 유형은 무엇입니까?
   | 경고 수준 | 전송되는 경고 |
   | --- | --- |
   | 중요 | 백업 실패, 복구 실패 |
   | Warning | 백업 작업 성공, 경고 있음(예: 스냅숏을 만드는 중 일부 작성기 오류) |
   | 정보 제공 | 현재 Azure VM 백업에 사용할 수 있는 정보 경고 없음 |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>알림이 구성된 경우에도 전자 메일이 전송되지 않는 경우가 있습니까?
알림이 제대로 구성되었더라도 경고가 전송되지 않는 경우가 있습니다. 이처럼 전자 메일 알림이 전송되지 않는 경우는 다음과 같습니다.

* 알림이 매시간 다이제스트로 구성되고 알림이 발생하고 한 시간 이내에 확인되는 경우.
* 작업이 취소됩니다.
* 백업 작업이 트리거된 다음 실패하고 다른 백업 작업이 진행 중입니다.
* 리소스 관리자 활성화된 VM에 대한 예약된 백업 작업이 시작되지만 VM이 더 이상 존재하지 않습니다.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>활동 로그를 사용하여 성공적인 백업에 대한 알림 받기

백업이 성공한 후 알림을 받으려면 자격 증명 모음의 [활동 로그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)에서 구축되는 경고를 사용할 수 있습니다.

### <a name="login-into-azure-portal"></a>Azure Portal에 로그인
Azure Portal에 로그인하고 관련 Azure Recovery Services 자격 증명 모음으로 이동 한 후 속성에서 "활동 로그" 섹션을 클릭합니다.

### <a name="identify-appropriate-log"></a>적절한 로그 확인

다음 그림에 나와 있는 필터를 적용하여 성공적인 백업에 대한 활동 로그를 받도록 할 수 있습니다. 시간 범위를 변경하면서 레코드를 봅니다.

![활동 로그](./media/backup-azure-monitor-vms/activity-logs-identify.png)

"JSON" 세그먼트를 클릭하여 자세한 내용을 확인하고 복사한 후 텍스트 편집기에서 붙여넣고 볼 수 있습니다. 해당 자격 증명 모음 세부 정보와 활동 로그를 트리거한 항목(예: 백업 항목)이 표시됩니다.

그런 후 "활동 로그 경고 추가"를 클릭하여 이러한 모든 로그에 대한 경고를 생성합니다.

### <a name="add-activity-log-alert"></a>활동 로그 경고 추가

"활동 로그 경고 추가"를 클릭하면 아래와 같은 화면이 표시됩니다.

![활동 로그 경고](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
구독 및 리소스 그룹은 경고를 저장하는 데 사용됩니다. 조건은 미리 채워집니다. 모든 값이 사용자 요구에 적합한지 확인합니다.

성공적인 백업의 경우 '수준'이 "정보"이고 상태는 "성공"으로 표시됩니다.

위의 "리소스"를 선택하면 해당 리소스 또는 자격 증명 모음에 대해 활동 로그가 기록될 때 경고가 생성됩니다. 해당 규칙을 모든 자격 증명 모음에 적용하려면 "리소스"를 비워둡니다.

### <a name="define-action-on-alert-firing"></a>경고 발생 작업 정의

"작업 그룹"을 사용하여 경고 발생 작업을 정의합니다. "작업 유형"을 클릭하여 전자 메일/SMS/ITSM과의 통합과 같은 사용 가능한 작업에 대해 자세히 알아볼 수 있습니다.

![활동 로그 작업 그룹](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


확인을 클릭하면 활동 로그 경고가 생성되고, 성공적인 백업에 대해 기록된 후속 활동 로그로 인해 작업 그룹에 정의된 작업이 발생합니다.

### <a name="limitations-on-alerts"></a>경고에 대한 제한
이벤트 기반 경고에 다음과 같은 제한 사항이 적용됨:

1. Recovery Services 자격 증명 모음의 모든 가상 머신에서 경고를 유발합니다. Recovery Services 자격 증명 모음에서 가상 머신의 하위 집합에 대한 경고를 사용자 지정할 수 없습니다.
2. "alerts-noreply@mail.windowsazure.com"에서 경고를 보냅니다. 현재는 전자 메일 보낸 사람을 수정할 수 없습니다.

## <a name="next-steps"></a>다음 단계
복구 지점에서 가상 머신을 다시 만드는 방법에 대한 내용은 [Azure VM 복원](backup-azure-arm-restore-vms.md)을 확인하세요.

가상 머신을 보호하는 방법에 대한 정보가 필요한 경우 [먼저 보기: Recovery Services 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요. 

[Azure 가상 머신 백업 관리](backup-azure-manage-vms.md)문서에서 VM 백업에 대한 관리 작업에 관하여 알아봅니다.
