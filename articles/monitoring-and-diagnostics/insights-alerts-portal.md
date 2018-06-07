---
title: Azure 서비스에 대한 경고 만들기 - Azure portal | Microsoft Docs
description: 지정한 조건이 충족될 경우 전자 메일, 알림, 웹 사이트 URL 호출(webhook) 또는 자동화를 트리거합니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: b0d938112aaea4d86dd539b53a1749cc800607a7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 - Azure Portal
> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>개요

> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [최신 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 
>
>

이 문서에서는 Azure Portal을 사용하여 클래식 Azure 메트릭 경고를 설정하는 방법을 보여줍니다. 

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.    
* **활동 로그 이벤트** - *모든* 이벤트에 대해 또는 특정 이벤트가 발생했을 때만 경고를 트리거할 수 있습니다. [활동 로그 경고](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.

트리거되면 다음을 수행하도록 클래식 메트릭 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 이메일 알림을 보냅니다.
* 사용자가 지정한 추가 이메일 주소로 이메일을 보냅니다.
* webhook 호출
* Azure runbook 실행 시작(현재는 Azure 포털에서만 가능)

다음을 통해 클래식 메트릭 경고 규칙에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [명령줄 인터페이스(CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기
1. [포털](https://portal.azure.com/)에서 모니터링하려는 리소스를 찾아 선택합니다.

2. 모니터링 섹션에서 **경고(클래식)**를 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다. **경고(클래식)**를 찾을 수 없는 경우 **경고** 또는 **경고 규칙** 아래에서 찾을 수 있습니다.

    ![모니터링](./media/insights-alerts-portal/AlertRulesButton.png)

3. **Add metric alert(클래식)** 명령을 선택하고 필드에 입력합니다.

    ![Add alert](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. 경고 규칙의 **이름**을 지정하고 **설명**을 선택합니다. 알림 이메일에도 표시되는 항목입니다.

5. 모니터링할 **메트릭**을 선택하고 해당 메트릭에 대한 **조건** 및 **임계값**을 선택합니다. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**도 선택합니다. 예를 들어 "지난 5분" 기간을 사용하고 경고가 80% 이상인 CPU를 찾는다면 이 경고는 CPU가 5분 동안 계속 80%를 넘으면 트리거됩니다. 첫 번째 트리거가 발생한 후 CPU가 5분 동안 80% 미만을 유지하면 다시 트리거됩니다. CPU 메트릭 측정은 1분마다 발생합니다.

6. 경고가 발생했을 때 관리자 및 공동 관리자에게 이메일을 보내려면 **소유자에게 이메일 보내기...** 를 선택합니다.

7. 경고가 발생했을 때 다른 이메일 주소에서 알림을 받으려면 해당 이메일을 **추가 관리자 이메일** 필드에 추가합니다. 여러 전자 메일은 세미콜론(*email@contoso.com;email2@contoso.com*)으로 구분됩니다.

8. 경고가 발생했을 때 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.

9. Azure Automation을 사용하는 경우 경고가 발생할 때 실행할 Runbook을 선택할 수 있습니다.

10. 경고 만들기가 완료되면 **확인** 을 선택합니다.   

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="managing-your-alerts"></a>경고 관리
경고를 만든 후 해당 경고를 선택하여 다음을 수행할 수 있습니다.

* 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인
* 편집 또는 삭제
* 해당 경고에 대한 알림 수신을 일시 중지 또는 재개하려면 **사용 중지** 또는 **사용**하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
* [Azure 모니터링 개요](monitoring-overview.md) 를 확인합니다.
* [최신 메트릭 경고](monitoring-near-real-time-metric-alerts.md)에 대해 자세히 알아보기
* [경고에서의 webhook 구성](insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 상세 고빈도 메트릭을 수집하기 위한 [진단 로그](monitoring-overview-of-diagnostic-logs.md) 의 개요를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
