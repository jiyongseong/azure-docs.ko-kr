---
title: 이벤트 허브로 Azure 진단 로그 스트림 | Microsoft Docs
description: 이벤트 허브로 Azure 진단 로그를 스트리밍하는 방법을 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 1f5a97f5af47a3c5731d5c5d4d5e8cf17097ae60
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>이벤트 허브로 Azure 진단 로그 스트림
포털에서 기본 제공되는 “Event Hubs로 내보내기” 옵션을 사용하거나 Azure PowerShell cmdlet 또는 Azure CLI 2.0을 통해 진단 설정에서 이벤트 허브 권한 부여 규칙 ID를 사용하도록 설정하여 **[Azure 진단 로그](monitoring-overview-of-diagnostic-logs.md)**를 거의 실시간으로 응용 프로그램에 스트리밍할 수 있습니다.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>진단 로그 및 Event Hubs에서 수행할 수 있는 작업
진단 로그의 스트리밍 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

* **타사 로깅 및 원격 분석 시스템으로 로그 스트림** – 모든 진단 로그를 단일 이벤트 허브로 스트리밍하여 로그 데이터를 타사 SIEM 또는 로그 분석 도구로 파이프할 수 있습니다.
* **“실행 부하 과다 경로” 데이터를 PowerBI로 스트리밍하여 서비스 상태 보기** – Event Hubs, Stream Analytics 및 PowerBI를 사용하여 Azure 서비스에서 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. [이 설명서 문서는 Event Hubs를 설정하고 Stream Analytics로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 훌륭한 개요를 제공합니다](../stream-analytics/stream-analytics-power-bi-dashboard.md). 다음은 진단 로그로 설정하는 방법에 대한 몇 가지 팁입니다.

  * 진단 로그의 범주에 대한 이벤트 허브는 포털에서 해당 옵션을 선택하거나 PowerShell을 통해 사용하도록 설정하면 자동으로 생성되므로 **insights-**로 시작하는 이름의 네임스페이스에서 이벤트 허브를 선택할 수 있습니다.
  * 다음 SQL 코드는 모든 로그 데이터를 PowerBI 테이블로 간단히 구문 분석하는 데 사용할 수 있는 샘플 Stream Analytics 쿼리입니다.

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드** – 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 Event Hubs 사용에 대해서는 여기 Dan Rosanova의 가이드를 참조하세요.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-diagnostic-logs"></a>진단 로그의 스트리밍 사용

프로그래밍 방식으로 포털을 통하거나 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings)를 사용하여 진단 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어느 쪽이든 Event Hubs 네임스페이스를 지정하는 로그 설정과 네임스페이스로 전송하려는 로그 범주 및 메트릭을 만듭니다. 이벤트 허브는 활성화한 각 로그 범주에 대한 네임스페이스에서 생성됩니다. 진단 **로그 범주**는 리소스가 수집할 수 있는 로그 형식입니다.

> [!WARNING]
> Compute 리소스(예: VM 또는 서비스 패브릭)에서 진단 로그를 사용 및 스트리밍하려면 [여러 단계 집합을 거쳐야 합니다](../event-hubs/event-hubs-streaming-azure-diags-data.md).

설정을 구성하는 사용자에게 두 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있는 한, Event Hubs 네임스페이스가 로그를 내보내는 리소스와 동일한 구독이 아니어도 됩니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 ‘들어오는 메시지’ 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>포털을 사용하여 진단 로그 스트림

1. 포털에서 Azure Monitor로 이동하고 **진단 설정**을 클릭합니다.

    ![Azure Monitor의 모니터링 섹션](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. 필요에 따라 리소스 그룹 또는 리소스 종류를 기준으로 목록을 필터링합니다. 그런 다음 진단 설정을 지정하려는 리소스를 클릭합니다.

3. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. “진단 켜기”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   리소스에 기존 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. “진단 설정 추가”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. 이름을 지정하고 **이벤트 허브로의 스트림**의 확인란을 선택한 다음, Event Hubs 네임스페이스를 선택합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   선택한 네임스페이스는 이벤트 허브가 생성(진단 로그를 처음으로 스트리밍하는 경우)되거나 스트림(해당 로그 범주를 이 네임스페이스로 스트리밍하는 리소스가 이미 있는 경우)되는 위치이며 정책은 스트리밍 메커니즘이 포함하는 사용 권한을 정의합니다. 현재, 이벤트 허브로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. 포털에서 네임스페이스에 대한 구성 탭 아래에서 Event Hubs 네임스페이스 공유 액세스 정책을 만들거나 수정할 수 있습니다. 이러한 진단 설정 중 하나를 업데이트하려면 클라이언트에 Event Hubs 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다. 원하는 경우 이벤트 허브 이름을 지정할 수도 있습니다. 이벤트 허브 이름을 지정하면 로그가 로그 범주에 따라 새로 작성되는 이벤트 허브로 라우팅되지 않고 해당 이벤트 허브로 라우팅됩니다.

4. **저장**을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 이벤트 허브로 스트리밍됩니다.

### <a name="via-powershell-cmdlets"></a>PowerShell Cmdlet을 통해

[Azure PowerShell Cmdlet](insights-powershell-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 다음 매개 변수와 함께 `Set-AzureRmDiagnosticSetting` cmdlet을 사용하면 됩니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

이벤트 허브 권한 부여 규칙 ID는 `{Event Hub namespace resource ID}/authorizationrules/{key name}` 형식의 문자열입니다. 예를 들면 `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`입니다. 현재는 PowerShell을 사용하여 특정 이벤트 허브 이름을 선택할 수 없습니다.

### <a name="via-azure-cli-20"></a>Azure CLI 2.0을 통해

[Azure CLI 2.0](insights-cli-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 명령을 사용합니다.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` 매개 변수로 전달된 JSON 배열에 사전을 추가하여 진단 로그에 추가적인 범주를 추가할 수 있습니다.

`--event-hub-rule` 인수는 PowerShell cmdlet에 대해 설명한 것처럼 이벤트 허브 권한 부여 규칙 ID와 동일한 형식을 사용합니다.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Event Hubs에서 로그 데이터를 사용하려면 어떻게 하나요?

다음은 Event Hubs의 샘플 출력 데이터입니다.

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 요소 이름 | 설명 |
| --- | --- |
| 레코드 |이 페이로드에 있는 모든 로그 이벤트의 배열입니다. |
| 실시간 |이벤트가 발생한 시간입니다. |
| 카테고리 |이 이벤트에 대한 로그 범주입니다. |
| ResourceId |이 이벤트를 생성한 리소스의 리소스 ID입니다. |
| operationName |작업의 이름입니다. |
| level |선택 사항입니다. 로그 이벤트 수준을 나타냅니다. |
| properties |이벤트의 속성입니다. |

Event Hubs로의 스트리밍을 지원하는 모든 리소스 공급자의 목록을 [여기](monitoring-overview-of-diagnostic-logs.md)에서 볼 수 있습니다.

## <a name="stream-data-from-compute-resources"></a>Compute 리소스의 스트림 데이터

Windows Azure 진단 에이전트를 사용하여 Compute 리소스에서 진단 로그를 스트림할 수도 있습니다. 설정하는 방법은 [이 문서를 참조](../event-hubs/event-hubs-streaming-azure-diags-data.md)하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
* [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
