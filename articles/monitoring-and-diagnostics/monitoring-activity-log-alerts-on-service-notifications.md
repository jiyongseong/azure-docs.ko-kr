---
title: Azure 서비스 알림에 대한 활동 로그 경고 수신 | Microsoft Docs
description: Azure 서비스가 발생할 때 SMS, 전자 메일 또는 웹후크를 통해 알림을 받습니다.
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: johnkem
ms.openlocfilehash: b4c4fdeb825bbcab54f074c5224140282a24d196
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>서비스 알림에 대한 활동 로그 경고 만들기
## <a name="overview"></a>개요
이 문서에서는 Azure Portal을 사용하여 서비스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 보여 줍니다.  

Azure에서 Azure 구독에 서비스 상태 알림을 전송할 때 경고를 받을 수 있습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

- 서비스 상태 알림 클래스(서비스 문제, 계획된 유지 보수, 상태 자문)
- 영향을 받는 구독
- 영향을 받는 서비스
- 영향을 받는 하위 지역

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

- 기존 작업 그룹을 선택합니다.
- 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](monitoring-action-groups.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용하여 서비스 상태 알림 경고를 구성하는 방법에 대한 자세한 내용은 [ 템플릿](monitoring-create-activity-log-alerts-with-resource-manager-template.md)을 참조하세요.

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 새 작업 그룹에 대한 서비스 상태 알림의 경고 만들기
1. [포털](https://portal.azure.com)에서 **서비스 상태**를 선택합니다.

    !["서비스 상태" 서비스](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. **경고** 섹션에서 **상태 경고**를 선택합니다.

    !["상태 경고" 탭](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. **서비스 상태 경고 만들기**를 선택하고 필드를 입력합니다.

    !["서비스 상태 경고 만들기" 명령](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. **활동 로그 경고 이름** 상자에 이름을 입력하고 **설명**을 입력합니다.

    ![“활동 로그 경고 추가” 대화 상자](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. **구독** 상자가 현재 구독으로 자동으로 채워집니다. 이 구독은 활동 로그 경고를 저장하는 데 사용됩니다. 경고 리소스가 이 구독에 배포되고 이에 대한 활동 로그에서 이벤트를 모니터링합니다.

6. 경고 리소스가 만들어지는 **리소스 그룹**을 선택합니다. 이는 경고에 의해 모니터링되는 리소스 그룹이 아닙니다. 대신 경고 리소스가 있는 리소스 그룹입니다.

7. **이벤트 범주** 상자가 자동으로 **서비스 상태**로 설정됩니다. 필요에 따라 수신하려는 서비스 상태 알림의 **서비스**,  **지역** 및 **형식**을 선택합니다.

8. **다음을 통해 경고**에서 **신규** 작업 그룹 단추를 선택합니다. **작업 그룹 이름** 상자에 이름을 입력하고 **짧은 이름** 상자에 이름을 입력합니다. 약식 이름은 이 경고가 발생할 때 전송된 알림에서 참조됩니다.

9. 받는 사람에 대한 다음 항목을 제공하여 받는 사람 목록을 정의합니다.

    a. **이름**: 받는 사람의 이름, 별칭 또는 식별자를 입력합니다.

    나. **작업 유형**: SMS, 메일, 웹후크, Azure 앱 등을 선택합니다.

    다. **세부 정보**: 선택한 작업 유형에 따라 전화 번호, 메일 주소, 웹후크 URI 등을 입력합니다.

10. **확인**을 선택하여 경고를 만듭니다.

몇 분 이내에 경고가 활성화되고 만들 때 지정한 조건에 따라 트리거를 시작합니다.

[기존 문제 관리 시스템에 대한 웹후크 알림 구성](../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다. 활동 로그 경고에 대한 웹후크 스키마 정보는 [Azure 활동 로그 경고에 대한 웹후크](monitoring-activity-log-alerts-webhook.md)를 참조하세요.

>[!NOTE]
>이러한 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 기존 작업 그룹에 대한 서비스 상태 알림의 경고 만들기

1. 이전 섹션의 1-7단계를 수행하여 서비스 상태 알림을 만듭니다. 

2. **다음을 통해 경고**에서 **기존** 작업 그룹 단추를 선택합니다. 적절한 작업 그룹을 선택합니다.

3. **확인**을 선택하여 경고를 만듭니다.

몇 분 이내에 경고가 활성화되고 만들 때 지정한 조건에 따라 트리거를 시작합니다.

## <a name="manage-your-alerts"></a>경고 관리

경고를 만들면 **모니터**의 **경고** 섹션에 표시됩니다. 관리하려는 경고를 선택합니다.

* 편집합니다.
* 삭제합니다.
* 해당 경고에 대한 알림 수신을 일시적으로 중지하거나 다시 시작하려면 사용 안 함 또는 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](../service-health/service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [서비스 상태 알림](monitoring-service-notifications.md)에 대해 자세히 알아보세요.
- [알림 속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보세요.
- [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md)를 검토하세요.
- [활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 경고를 받는 방법에 대해 알아보세요. 
- [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보세요.
