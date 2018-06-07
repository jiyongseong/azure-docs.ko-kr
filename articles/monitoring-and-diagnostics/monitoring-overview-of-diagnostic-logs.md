---
title: Azure 진단 로그 | Microsoft Docs
description: Azure 진단 로그란 무엇이고 Azure 리소스 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem; magoedte
ms.openlocfilehash: 00b990181e5bb96f122ff1ae530dd027a772b210
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Azure 리소스에서 로그 데이터 수집 및 소비

## <a name="what-are-azure-resource-diagnostic-logs"></a>Azure 리소스 진단 로그란?

**Azure 리소스 수준 진단 로그**는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보낸 로그입니다. 이러한 로그의 내용은 리소스 유형에 따라 달라집니다. 예를 들어 네트워크 보안 그룹 규칙 카운터와 Key Vault 감사는 리소스 로그의 두 범주입니다.

리소스 수준 진단 로그는 [활동 로그](monitoring-overview-activity-logs.md)와 다릅니다. 활동 로그는 가상 컴퓨터 만들기나 논리 앱 삭제 등, Resource Manager를 사용하여 구독에서 리소스에 대해 수행된 작업에 대한 정보를 제공합니다. 활동 로그는 구독 수준 로그입니다. 리소스 수준 진단 로그는 Key Vault에서 암호 가져오기 등과 같이 리소스 자체에서 수행된 작업에 대한 정보를 제공합니다.

리소스 수준 진단 로그도 게스트 OS 수준 진단 로그와 다릅니다. 게스트 OS 진단 로그는 가상 머신이나 다른 지원되는 리소스 유형 안에서 실행되는 에이전트가 수집합니다. 리소스 수준 진단 로그는 에이전트가 필요하지 않으며 Azure 플랫폼 자체에서 리소스 특정 데이터를 수집하고, 게스트 OS 수준 진단 로그는 가상 머신에서 실행되는 운영 체제 및 응용 프로그램에서 데이터를 수집합니다.

일부 리소스만 여기서 설명하는 새로운 형식의 리소스 진단 로그를 지원합니다. 이 문서는 리소스 유형에서 새로운 리소스 수준 진단 로그에 지원하는 나열된 섹션을 포함합니다.

![리소스 진단 로그와 다른 로그 유형 비교 ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>리소스 수준 진단 로그로 수행할 수 있는 작업
리소스 수준 진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![리소스 진단 로그의 논리적 배치](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* 감사 또는 수동 검사를 위해 [**Storage 계정**](monitoring-archive-diagnostic-logs.md)에 저장합니다. **리소스 진단 설정**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* [타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 **Event Hubs**로 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)합니다.
* [Log Analytics](../log-analytics/log-analytics-azure-storage.md)를 사용하여 분석

로그를 내보내는 것과 동일한 구독에 위치하지 않는 저장소 계정 또는 Event Hubs 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

## <a name="resource-diagnostic-settings"></a>리소스 진단 설정

리소스 진단 설정을 사용하여 비-Compute 리소스에 대한 리소스 진단 로그를 구성합니다. 리소스 제어를 위한 **리소스 진단 설정**은 다음과 같습니다.

* 리소스 진단 로그 및 메트릭을 보내는 위치(Storage 계정, Event Hubs 및/또는 Log Analytics).
* 전송되는 로그 범주 및 메트릭 데이터의 전송 여부.
* 각 로그 항목을 저장소 계정에 유지해야 하는 기간.
    - 보존이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
    - 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예: Event Hubs 또는 Log Analytics 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

이러한 설정은 Azure Portal에서 리소스에 대한 진단 설정을 통해, Azure PowerShell 및 CLI 명령을 통해 또는 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 통해 쉽게 구성됩니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

> [!WARNING]
> Compute 리소스(예: VM 또는 Service Fabric)의 게스트 OS 레이어의 진단 로그 및 메트릭에서는 [출력의 구성 및 선택을 위한 별도의 메커니즘](../azure-diagnostics.md)을 사용합니다.

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>리소스 진단 로그의 컬렉션을 사용하도록 설정하는 방법 

[리소스 관리자 템플릿에서 리소스 만들기의 일부로](./monitoring-enable-diagnostic-logs-using-template.md) 또는 포털에서 해당 리소스의 페이지에서 리소스를 만든 후 리소스 진단 로그의 컬렉션을 사용하도록 설정할 수 있습니다. 또한 Azure PowerShell 또는 CLI 명령을 사용하거나 Azure Monitor REST API를 사용하여 언제든지 컬렉션을 사용하도록 설정할 수도 있습니다.

> [!TIP]
> 이러한 지침은 모든 리소스에 직접 적용되지 않을 수 있습니다. 특정 리소스 형식에 적용할 수 있는 특수한 단계를 알아보려면 이 페이지 맨 아래에 있는 스키마 링크를 참조하세요.

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>포털에서 리소스 진단 로그 컬렉션 활성화

특정 리소스로 이동하거나 Azure Monitor로 이동하여 리소스를 만든 후 Azure Portal에서 리소스 진단 로그의 컬렉션을 사용하도록 설정할 수 있습니다. Azure Monitor를 통해 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](http://portal.azure.com)에서 Azure Monitor로 이동하고 **진단 설정**을 클릭합니다.

    ![Azure Monitor의 모니터링 섹션](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. 필요에 따라 리소스 그룹 또는 리소스 종류를 기준으로 목록을 필터링합니다. 그런 다음 진단 설정을 지정하려는 리소스를 클릭합니다.

3. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. “진단 켜기”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   리소스에 기존 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. “진단 설정 추가”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. 설정에 이름을 지정하고, 데이터를 보낼 각 대상에 대한 확인란을 선택하고, 각 대상에 사용되는 리소스를 구성합니다. 필요에 따라 **보존(일)** 슬라이더를 사용하여 이러한 로그를 유지할 일 수를 설정합니다(저장소 계정 대상에만 적용됨). 0일의 보존은 로그를 무기한 저장합니다.

   ![진단 설정 추가 - 기존 설정](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. **저장**을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 진단 로그가 지정된 대상에 전송됩니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>PowerShell을 통한 리소스 진단 로그 컬렉션 활성화

Azure PowerShell에서 리소스 진단 로그 컬렉션을 활성화하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 활성화하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

서비스 버스 규칙 ID는 `{Service Bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

다음 명령을 사용하여 Log Analytics 작업 공간의 리소스 ID를 가져올 수 있습니다.

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>Azure CLI 2.0을 통해 리소스 진단 로그의 컬렉션 활성화

Azure CLI 2.0을 통해 리소스 진단 로그의 컬렉션을 사용하도록 설정하려면 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려는 경우:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

`--resource-group` 인수는 `--storage-account`가 개체 ID가 아닌 경우에만 필요합니다.

이벤트 허브로의 진단 로그 스트리밍을 활성화하려는 경우:

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

규칙 ID는 `{Service Bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있도록 활성화하려는 경우:

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

`--resource-group` 인수는 `--workspace`가 개체 ID가 아닌 경우에만 필요합니다.

명령을 사용하면 `--logs` 매개 변수로 전달된 JSON 배열에 사전을 추가하여 진단 로그에 추가적인 범주를 추가할 수 있습니다. `--storage-account`, `--event-hub` 및 `--workspace` 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>REST API를 통해 리소스 진단 로그 컬렉션 활성화

Azure Monitor REST API를 사용하여 진단 설정을 변경하려면 [이 문서](https://msdn.microsoft.com/library/azure/dn931931.aspx)를 참조하세요.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>포털에서 리소스 진단 설정 관리

리소스를 모두 진단 설정으로 설정했는지 확인합니다. 포털에서 **모니터**로 이동하여 **진단 설정**을 엽니다.

![포털의 진단 로그 블레이드](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

모니터 섹션을 찾기 위해 “모든 서비스”를 클릭해야 할 수도 있습니다.

여기에서 진단 설정을 지원하는 모든 리소스를 보고 필터링하여 진단을 사용하도록 설정했는지 확인할 수 있습니다. 드릴다운하여 여러 설정이 리소스에 설정되어 있는지 확인하고, 데이터가 이동하는 저장소 계정, Event Hubs 네임스페이스 및/또는 Log Analytics 작업 영역을 확인할 수 있습니다.

![포털의 진단 로그 결과](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

진단 설정을 추가하면 진단 설정 뷰가 표시되고, 여기에서 선택한 리소스에 대한 진단 설정을 활성화, 비활성화 또는 수정할 수 있습니다.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>리소스 진단 로그에 대해 지원되는 서비스, 범주 및 스키마

지원되는 서비스 및 해당 서비스에서 사용되는 로그 범주 및 스키마에 대한 전체 목록은 [이 문서를 참조](monitoring-diagnostic-logs-schema.md)하세요.

## <a name="next-steps"></a>다음 단계

* [**Event Hubs**로 리소스 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)
