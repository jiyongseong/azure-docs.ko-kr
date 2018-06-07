---
title: Azure 서비스에 대한 경고 만들기 - 플랫폼 간 CLI | Microsoft Docs
description: 지정한 조건이 충족될 경우 전자 메일, 알림, 웹 사이트 URL 호출(webhook) 또는 자동화를 트리거합니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 9ea7da35acefc139625e71904c8aa1b01b87e4df
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 - 플랫폼 간 CLI
> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>개요
> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [더 새롭고 더 나은 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 이러한 경고는 여러 메트릭을 모니터링하고 차원 메트릭에 대한 경고를 허용할 수 있습니다. 더 새로운 메트릭 경고를 위한 CLI 지원이 곧 제공됩니다.
>
>

이 문서에서는 플랫폼 간 CLI(명령줄 인터페이스)를 사용하여 Azure 클래식 메트릭 경고를 설정하는 방법을 보여줍니다.

> [!NOTE]
> Azure Monitor는 2016년 9월 25일까지는 "Azure Insights"로 지칭했던 제품의 새로운 이름입니다. 하지만 네임스페이스와 아래 명령에서는 "insights"를 계속 포함합니다.
>
>

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.    
* **활동 로그 이벤트** - *모든* 이벤트에 대해 또는 특정 이벤트가 발생했을 때만 경고를 트리거할 수 있습니다 활동 로그 경고에 대해 자세히 알아보려면 [여기를 클릭](monitoring-activity-log-alerts.md)하세요.

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

명령을 입력하고 마지막에 -help를 붙이면 항상 도움말을 확인할 수 있습니다. 예: 

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>CLI를 사용하여 경고 규칙 만들기
1. 필수 구성 요소를 수행한 다음 Azure에 로그인합니다. [Azure Monitor CLI 샘플](insights-cli-samples.md)을 참조하세요. 즉 CLI를 설치하고 이 명령을 실행합니다. 로그인 후 사용자가 사용하는 구독을 표시하며 Azure Monitor 명령 실행을 준비합니다.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. 리소스 그룹에 대해 기존 규칙을 나열하려면 **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;* 형식을 사용합니다.

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. 규칙을 만들려면 먼저 몇 가지 중요한 정보가 필요합니다.
  * 경고를 설정할 리소스의 **리소스 ID**
  * 리소스에 대해 사용 가능한 **메트릭 정의**

     리소스 ID를 가져오는 한 가지 방법은 Azure 포털을 사용하는 것입니다. 리소스를 이미 만들었다고 가정하고 포털에서 선택합니다. 이 후 다음 블레이드에서 *설정* 섹션의 *속성*을 선택합니다. *리소스 ID* 는 다음 블레이드의 필드입니다. 또 다른 방법은 [Azure Resource Explorer](https://resources.azure.com/)를 사용하는 것입니다.

     웹앱에 대한 예제 리소스 ID

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     이전 리소스 예제에 대한 메트릭의 사용 가능한 메트릭 및 단위 목록을 가져오려면 다음 CLI 명령을 사용합니다.  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* 은 사용 가능한 측정의 세분성입니다(1분 간격). 다른 세분성을 사용할 때는 다른 메트릭 옵션이 있습니다.
4. 메트릭 기반 경고 규칙을 만들려면 다음 형태의 명령을 사용합니다.

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    다음 예제에서는 웹 사이트 리소스에 대한 경고를 설정합니다. 이 경고는 어느 트래픽이나 5분 동안 계속 수신되면 항상 트리거되며, 트래픽이 5분 동안 수신되지 않으면 다시 트리거됩니다.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. 클래식 메트릭 경고가 발생할 때 웹후크를 만들거나 이메일을 보내려면 먼저 이메일 및/또는 웹후크를 만듭니다. 그런 다음 바로 규칙을 만듭니다. Webhook나 이메일을 CLI를 통해 이미 생성된 규칙과 연결할 수 없습니다.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. 개별 규칙을 살펴서 경고가 제대로 만들어졌는지 확인할 수 있습니다.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. 규칙을 삭제하려면 다음 형태의 명령을 사용합니다.

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    이 명령은 이 문서에서 앞서 만든 규칙을 삭제합니다.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>다음 단계
* [Azure 모니터링 개요](monitoring-overview.md) 를 확인합니다.
* [경고에서의 webhook 구성](insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 상세 고빈도 메트릭을 수집하기 위한 [진단 로그 수집](monitoring-overview-of-diagnostic-logs.md) 의 개요를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
