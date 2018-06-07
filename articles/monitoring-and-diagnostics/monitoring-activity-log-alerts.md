---
title: 클래식 활동 로그 경고 만들기 | Microsoft Docs
description: 활동 로그에서 특정 이벤트가 발생하면 SMS, 웹후크 및 전자 메일을 통해 알림을 받습니다.
author: johnkemnetz
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2017
ms.author: johnkem
ms.openlocfilehash: e0d92fcd6d3cbd50a2bcb8166b27b11bc86aad83
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-activity-log-alerts-classic"></a>활동 로그 경고 만들기(클래식)

## <a name="overview"></a>개요
활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생한 경우에 활성화되는 경고입니다. 이는 Azure 리소스이므로 Azure 리소스 관리자 템플릿을 사용하여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 이 문서에서는 활동 로그 경고에 대한 개념을 소개합니다. 그런 다음 Azure Portal을 사용하여 활동 로그 이벤트에 대한 경고를 설정하는 방법에 대해 설명합니다.

> [!NOTE]

>  새 [경고](monitoring-overview-unified-alerts.md) 경험이 이 절차를 대체합니다. 이 문서는 이전 경험에 대한 참조로 제공됩니다. [자세히 알아보기](monitoring-activity-log-alerts-new-experience.md).

일반적으로 다음과 같은 경우에 알림을 수신하도록 활동 로그 경고를 만들 수 있습니다.

* Azure 구독의 리소스에서 특정 변경이 발생하는 경우. 특정 리소스 그룹 또는 리소스로 범위를 지정하기도 합니다. 예를 들어 myProductionResourceGroup에서 가상 머신이 삭제되는 경우 알림을 받도록 할 수 있습니다. 또는 새 규칙이 사용자 구독의 사용자에 할당되는 경우 알림을 받도록 할 수도 있습니다.
* 서비스 상태 이벤트가 발생하는 경우. 서비스 상태 이벤트가 구독에서 리소스에 적용되는 인시던트 및 유지 관리 이벤트의 알림을 포함합니다.

두 경우 모두 활동 로그 경고는 경고가 생성되는 구독의 이벤트만 모니터링합니다.

활동 로그 이벤트에 대한 JSON 개체의 모든 최상위 속성에 따라 활동 로그 경고를 구성할 수 있습니다. 그러나 포털에는 가장 일반적인 옵션만 나와 있습니다.

- **범주**: 관리, 서비스 상태, 자동 크기 조정 및 권장 사항 중 하나입니다. 자세한 내용은 [Azure 활동 로그 개요](./monitoring-overview-activity-logs.md#categories-in-the-activity-log)를 참조하세요. 서비스 상태 이벤트에 대한 자세한 내용은 [서비스 알림에서 활동 로그 경고 수신](./monitoring-activity-log-alerts-on-service-notifications.md)을 참조하세요.
- **리소스 그룹**
- **리소스**
- **리소스 종류**
- **작업 이름**: 리소스 관리자 역할 기반 Access Control 작업 이름입니다.
- **수준**: 이벤트의 심각도 수준(자세한 정보, 정보, 경고, 오류 또는 중요)입니다.
- **상태**: 이벤트의 상태로, 일반적으로 시작됨, 실패 또는 성공입니다.
- **이벤트를 시작한 사람**: “호출자”로 알려져 있습니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

> [!NOTE]
> 범주가 “관리”인 경우, 경고에서 앞에 나온 조건 중 둘 이상을 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.

활동 로그 경고가 활성화되면 작업 그룹을 사용하여 작업 또는 알림을 생성합니다. 작업 그룹은 이메일 주소, 웹후크 URL 또는 SMS 전화 번호와 같은 알림 수신자의 재사용 가능한 집합입니다. 수신자를 여러 경고에서 참조하여 알림 채널을 집중화하고 그룹화할 수 있습니다. 활동 로그 경고를 정의하는 경우 두 가지 옵션이 있습니다. 다음을 수행할 수 있습니다.

* 활동 로그 경고에서 기존 작업 그룹을 사용합니다.
* 새 작업 그룹을 만듭니다.

작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](monitoring-action-groups.md)를 참조하세요.

서비스 상태 알림에 대해 자세히 알아보려면 [서비스 상태 알림에서 활동 로그 경고 수신](monitoring-activity-log-alerts-on-service-notifications.md)을 참조하세요.

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 새 작업 그룹의 활동 로그 이벤트에 대한 경고(클래식) 만들기
1. [포털](https://portal.azure.com)에서 **모니터**를 선택합니다.

    ![“모니터링” 서비스](./media/monitoring-activity-log-alerts/home-monitor.png)
2. **활동 로그** 섹션에서 **경고(클래식)**를 선택합니다.

    ![“경고” 탭](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. **활동 로그 경고 추가**를 선택하고 필드를 입력합니다.

4. **활동 로그 경고 이름** 상자에 이름을 입력하고 **설명**을 선택합니다.

    ![“활동 로그 경고 추가” 명령](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. **구독** 상자가 현재 구독으로 자동으로 채워집니다. 이 구독은 작업 그룹이 저장되는 위치입니다. 이는 경고 리소스를 배포하고 활동 로그 이벤트를 모니터링하는 구독입니다.

    ![“활동 로그 경고 추가” 대화 상자](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. 경고 리소스가 만들어지는 **리소스 그룹**을 선택합니다. 이는 경고에 의해 모니터링되는 리소스 그룹이 아닙니다. 대신 경고 리소스가 있는 리소스 그룹입니다.

7. 필요에 따라 표시된 추가 필터를 수정하는 **이벤트 범주**를 선택합니다. 관리 이벤트의 경우 필터는 **리소스 그룹**, **리소스**, **리소스 종류**, **작업 이름**, **수준**, **상태** 및 **이벤트를 시작한 사람**을 포함합니다. 이러한 값이 이 경고가 모니터링해야 하는 이벤트를 식별합니다.

    >[!NOTE]
    >경고에 앞에 나온 조건 중 하나 이상을 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.
    >
    >

8. **작업 그룹 이름** 상자에 이름을 입력하고 **약식 이름** 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

9.  작업에 대해 다음 정보를 제공하여 작업 목록을 정의합니다.

    a. **이름**: 작업의 이름, 별칭 또는 식별자를 입력합니다.

    나. **작업 유형**: SMS, 전자 메일 또는 웹후크를 선택합니다.

    다. **세부 정보**: 작업 유형에 따라 전화 번호, 이메일 주소 또는 웹후크 URI를 입력합니다.

10. **확인**을 선택하여 경고를 만듭니다.

경고가 완벽하게 전파되고 활성화되는 데에는 몇 분이 걸립니다. 새 이벤트가 경고의 조건과 일치하는 경우 트리거됩니다.

자세한 내용은 [활동 로그 경고에 사용된 웹후크 스키마 이해](monitoring-activity-log-alerts-webhook.md)를 참조하세요.

>[!NOTE]
>이러한 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 기존 작업 그룹의 활동 로그 이벤트에 대한 경고 만들기
1. 이전 섹션의 1-7단계를 수행하여 활동 로그 경고를 만듭니다.

2. **다음을 통해 알림**에서 **기존** 작업 그룹 단추를 선택합니다. 목록에서 기존 작업 그룹을 선택합니다.

3. **확인**을 선택하여 경고를 만듭니다.

경고가 완벽하게 전파되고 활성화되는 데에는 몇 분이 걸립니다. 새 이벤트가 경고의 조건과 일치하는 경우 트리거됩니다.

## <a name="manage-your-alerts"></a>경고 관리

경고를 만들면 모니터 블레이드의 경고 섹션에 표시됩니다. 관리하려는 경고를 선택합니다.

* 편집합니다.
* 삭제합니다.
* 해당 경고에 대한 알림 수신을 일시적으로 중지하거나 다시 시작하려면 사용 안 함 또는 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
- [경고 개요](monitoring-overview-alerts.md)를 확인하세요.
- [알림 속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보세요.
- [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md)를 검토하세요.
- [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보세요.  
- [서비스 상태 알림](monitoring-service-notifications.md)에 대해 자세히 알아보세요.
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)를 만듭니다.
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)를 만듭니다.
