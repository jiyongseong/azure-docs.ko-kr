---
title: Log Analytics로 Azure 진단 로그 스트리밍 | Microsoft Docs
description: Azure 진단 로그를 Log Analytics 작업 영역으로 스트리밍하는 방법을 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 82011126375a3c5016e110aac9ce6bc1b2d59cdf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Log Analytics로 Azure 진단 로그 스트리밍

**[Azure 진단 로그](monitoring-overview-of-diagnostic-logs.md)**는 포털, PowerShell cmdlet 또는 Azure CLI 2.0을 사용하여 거의 실시간으로 Azure Log Analytics로 스트리밍할 수 있습니다.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Log Analytics에서 진단 로그로 수행할 수 있는 작업

Azure Log Analytics는 Azure 리소스에서 생성된 원시 로그 데이터를 깊이 있게 이해할 수 있도록 도와주는 유연한 로그 검색 및 분석 도구입니다. 일부 기능은 다음과 같습니다.

* **로그 검색** - 로그 데이터에 대해 고급 쿼리를 작성하고, 다양한 소스의 로그 간 상관 관계를 파악하고, Azure 대시보드에 고정될 수 있는 차트를 생성합니다.
* **경고** - 하나 이상의 이벤트가 특정 쿼리와 일치할 경우를 감지하고 전자 메일 또는 웹후크 호출을 통해 알림을 받습니다.
* **솔루션** - 미리 작성된 뷰와 대시보드를 사용하여 로그 데이터를 바로 이해할 수 있습니다.
* **고급 분석** - Machine Learning 및 패턴 일치 알고리즘을 적용하여 로그를 통해 확인된 가능한 문제점을 식별합니다.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Log Analytics로 진단 로그 스트리밍

프로그래밍 방식으로 포털을 통하거나 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings)를 사용하여 진단 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어느 경우든, Log Analytics 작업 영역을 지정하는 진단 설정과 해당 작업 영역으로 전송하려는 로그 범주 및 메트릭을 만듭니다. 진단 **로그 범주**는 리소스가 제공할 수 있는 로그 유형입니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 Log Analytics 작업 영역은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 ‘들어오는 메시지’ 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>포털을 사용하여 진단 로그 스트림
1. 포털에서 Azure Monitor로 이동하고 **진단 설정**을 클릭합니다.

    ![Azure Monitor의 모니터링 섹션](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. 필요에 따라 리소스 그룹 또는 리소스 종류를 기준으로 목록을 필터링합니다. 그런 다음 진단 설정을 지정하려는 리소스를 클릭합니다.

3. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. “진단 켜기”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   리소스에 기존 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. “진단 설정 추가”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. 설정에 이름을 지정하고 **Log Analytics에 보내기** 확인란을 선택한 후 Log Analytics 작업 영역을 선택합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. **저장**을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역에 스트리밍됩니다. 이벤트를 내보낸 시간과 Log Analytics에 이벤트가 나타난 시간 사이에는 최대 15분의 차이가 있을 수 있습니다.

### <a name="via-powershell-cmdlets"></a>PowerShell Cmdlet을 통해
[Azure PowerShell Cmdlet](insights-powershell-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 다음 매개 변수와 함께 `Set-AzureRmDiagnosticSetting` cmdlet을 사용하면 됩니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

workspaceID 속성은 Log Analytics에 표시되는 작업 영역 ID/키가 아니라 작업 영역의 전체 Azure 리소스 ID를 사용합니다.

### <a name="via-azure-cli-20"></a>Azure CLI 2.0을 통해

[Azure CLI 2.0](insights-cli-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 명령을 사용합니다.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` 매개 변수로 전달된 JSON 배열에 사전을 추가하여 진단 로그에 추가적인 범주를 추가할 수 있습니다.

`--resource-group` 인수는 `--workspace`가 개체 ID가 아닌 경우에만 필요합니다.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Log Analytics에서 데이터를 쿼리하려면 어떻게 하나요?

Portal의 로그 검색 블레이드 또는 Log Analytics에 속하는 고급 분석에서 AzureDiagnostics 테이블 아래에 제공되는 로그 관리 솔루션을 통해 진단 로그를 쿼리할 수 있습니다. 또한 Log Analytics로 전송하는 로그 데이터를 즉시 이해하는 데 도움이 되는 [Azure 리소스의 몇 가지 솔루션](../log-analytics/log-analytics-add-solutions.md)을 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
