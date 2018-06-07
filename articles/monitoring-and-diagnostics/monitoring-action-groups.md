---
title: Azure Portal에서 작업 그룹 만들기 및 관리 | Microsoft Docs
description: Azure Portal에서 작업 그룹을 만들고 관리하는 방법에 대해 알아봅니다.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: dukek
ms.openlocfilehash: 07e3c1a95aa223121117f3deba0269fb6cc280c2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170379"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal에서 작업 그룹 만들기 및 관리
## <a name="overview"></a>개요 ##
이 문서에서는 Azure Portal에서 작업 그룹을 만들고 관리하는 방법을 보여 줍니다.

작업 그룹을 사용하여 작업 목록을 구성할 수 있습니다. 이러한 그룹은 사용자가 정의한 각 경고에서 다시 사용할 수 있습니다. 이를 통해 경고가 트리거될 때마다 동일한 작업이 수행되도록 합니다.

각 작업은 다음과 같은 속성으로 구성됩니다.

* **이름**: 작업 그룹 내의 고유 식별자입니다.  
* **동작 유형**: 음성 통화 또는 SMS 보내기, 이메일 보내기, 웹후크 호출, ITSM 도구에 데이터 보내기, 논리 앱 호출, Azure 앱에 푸시 알림 보내기 또는 Automation Runbook 실행.
* **세부 정보**: 해당 전화 번호, 이메일 주소, 웹후크 URI 또는 ITSM 연결 세부 정보입니다.

Azure 리소스 관리자 템플릿을 사용하여 작업 그룹을 구성하는 방법에 대한 자세한 내용은 [작업 그룹 리소스 관리자 템플릿](monitoring-create-action-group-with-resource-manager-template.md)을 참조하세요.

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 작업 그룹 만들기 ##
1. [포털](https://portal.azure.com)에서 **모니터**를 선택합니다. **모니터** 블레이드는 모든 모니터링 설정 및 데이터를 하나의 뷰에 통합합니다.

    ![“모니터링” 서비스](./media/monitoring-action-groups/home-monitor.png)
2. **설정** 섹션에서 **작업 그룹**을 선택합니다.

    ![“작업 그룹” 탭](./media/monitoring-action-groups/action-groups-blade.png)
3. **작업 그룹 추가**를 선택하고 필드를 입력합니다.

    ![“작업 그룹 추가” 명령](./media/monitoring-action-groups/add-action-group.png)
4. **작업 그룹 이름** 상자에 이름을 입력하고 **약식 이름** 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

      ![“작업 그룹 추가” 대화 상자](./media/monitoring-action-groups/action-group-define.png)

5. **구독** 상자가 현재 구독으로 자동으로 채워집니다. 이 구독은 작업 그룹이 저장되는 위치입니다.

6. 작업 그룹이 저장되는 **리소스 그룹**을 선택합니다.

7. 각 작업에 대해 다음 정보를 제공하여 작업 목록을 정의합니다.

    a. **이름**: 이 작업에 대한 고유 식별자를 입력합니다.

    나. **작업 유형**: 이메일/SMS/푸시/음성, 논리 앱, 웹후크, ITSM 또는 Automation Runbook을 선택합니다.

    다. **세부 정보**: 작업 유형에 따라 전화 번호, 이메일 주소, 웹후크 URI, Azure 앱, ITSM 연결 또는 Automation Runbook을 입력합니다. ITSM 작업의 경우 **작업 항목** 및 ITSM 도구에 필요한 다른 필드를 추가로 지정합니다.

8. **확인**을 선택하여 작업 그룹을 만듭니다.

## <a name="action-specific-information"></a>작업별 정보
<dl>
<dt>Azure 앱 푸시</dt>
<dd>하나의 작업 그룹에 최대 10개의 Azure 앱 작업이 포함될 수 있습니다.</dd>
<dd>현재, Azure 앱 작업은 ServiceHealth 알림만 지원합니다. 다른 경고 시간은 무시됩니다. [서비스 상태 알림이 게시될 때마다 경고 구성](monitoring-activity-log-alerts-on-service-notifications.md)을 참조하세요.</dd>

<dt>전자 메일</dt>
<dd>하나의 작업 그룹에 최대 50개의 전자 메일 작업이 포함될 수 있습니다.</dd>
<dd>[속도 제한 정보](./monitoring-alerts-rate-limiting.md) 문서를 참조하세요.</dd>

<dt>ITSM</dt>
<dd>하나의 작업 그룹에 최대 10개의 ITSM 작업이 포함될 수 있습니다.</dd>
<dd>ITSM 작업에는 ITSM 연결이 필요합니다. [ITSM 연결](../log-analytics/log-analytics-itsmc-overview.md)을 만드는 방법에 대해 알아봅니다.</dd>

<dt>논리 앱</dt>
<dd>하나의 작업 그룹에 최대 10개의 논리 앱 작업이 포함될 수 있습니다.</dd>

<dt>Runbook</dt>
<dd>하나의 작업 그룹에 최대 10개의 Runbook 작업이 포함될 수 있습니다.</dd>

<dt>SMS</dt>
<dd>하나의 작업 그룹에 최대 10개의 SMS 작업이 포함될 수 있습니다.</dd>
<dd>[속도 제한 정보](./monitoring-alerts-rate-limiting.md) 문서를 참조하세요.</dd>
<dd>[SMS 경고 동작](monitoring-sms-alert-behavior.md) 문서를 참조하세요.</dd>

<dt>음성</dt>
<dd>하나의 작업 그룹에 최대 10개의 음성 작업이 포함될 수 있습니다.</dd>
<dd>[속도 제한 정보](./monitoring-alerts-rate-limiting.md) 문서를 참조하세요.</dd>

<dt>웹후크</dt>
<dd>하나의 작업 그룹에 최대 10개의 웹후크 작업이 포함될 수 있습니다.
<dd>다시 시도 논리 - 응답에 대한 제한 시간은 10초입니다. webhook 호출은 HTTP 상태 코드 408, 429, 503, 504가 반환되거나 HTTP 엔드포인트가 응답하지 않으면 최대 2번 다시 시도됩니다. 10초 후에 첫 번째 다시 시도가 발생합니다. 100초 후에 두 번째 및 마지막 다시 시도가 발생합니다.</dd>
</dl>

## <a name="manage-your-action-groups"></a>작업 그룹 관리 ##
작업 그룹을 만들면 **모니터** 블레이드의 **작업 그룹** 섹션에 표시됩니다. 관리하려는 작업 그룹을 선택합니다.

* 작업을 추가, 편집 또는 제거합니다.
* 작업 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계 ##
* [SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보세요.  
* [활동 로그 경고 웹후크 스키마의 이해](monitoring-activity-log-alerts-webhook.md)를 확인해 보세요.  
* [ITSM 커넥터](../log-analytics/log-analytics-itsmc-overview.md)에 대해 자세히 알아보세요.
* 경고의 [속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보세요.
* [활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](monitoring-activity-log-alerts-on-service-notifications.md)하는 방법을 알아보세요.
