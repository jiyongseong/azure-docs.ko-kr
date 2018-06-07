---
title: Azure Monitor에서의 역할, 권한 및 보안 시작 | Microsoft Docs
description: Azure Monitor 기본 제공 역할 및 권한을 사용하여 모니터링 리소스에 대한 액세스를 제한하는 방법을 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: 248d45a59fa2769c4cfcc4b169bd9e61059f11b0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Azure Monitor에서의 역할, 권한 및 보안 시작
많은 팀에서는 모니터링 데이터 및 설정에 대한 액세스를 엄격히 규제할 필요가 있습니다. 예를 들어 모니터링에 대해 단독으로 작업하는 팀원(지원 엔지니어, devops 엔지니어)이 있거나, 관리되는 서비스 공급자를 사용할 경우 이들에게 리소스 생성, 수정 또는 삭제 기능은 제한하면서 모니터링 데이터에 대해서만 액세스를 부여하고자 할 수 있씁니다. 이 문서에서는 Azure의 사용자에게 기본 제공 모니터링 RBAC 역할을 신속하게 적용하거나 제한된 모니터링 권한이 필요한 사용자에 대해 자체 사용자 지정 역할을 구성하는 방법을 보여 줍니다. 그런 다음 Azure Monitor 관련 리소스에 대한 보안 고려 사항과, 포함된 데이터에 대한 액세스를 제한하는 방법에 대해 논의합니다.

## <a name="built-in-monitoring-roles"></a>기본 제공 모니터링 역할
Azure Monitor의 기본 제공 역할은 구독에서 리소스에 대한 액세스를 제한하면서, 인프라 모니터링을 담당하는 사용자는 필요한 데이터를 확보 및 구성할 수 있게 지원하도록 설계되었습니다. Azure Monitor는 Monitoring Reader와Monitoring Contributor 등, 바로 사용할 수 있는 2가지 역할을 제공합니다.

### <a name="monitoring-reader"></a>Monitoring Reader
Monitoring Reader 역할이 할당된 사용자는 구독에서 모든 모니터링 데이터를 볼 수 있지만 리소스를 수정하거나 모니터링 리소스와 관련한 설정은 편집할 수 없습니다. 이 역할은 다음이 필요한 지원과 같은 조직의 사용자나 운영 엔지니어에게 적합합니다.

* 포털의 모니터링 대시보드를 확인하고 자체 개별 모니터링 대시보드를 만듭니다.
* [Azure 경고](monitoring-overview-unified-alerts.md)에 정의된 경고 규칙 보기
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell cmdlets](insights-powershell-samples.md) 또는 [플랫폼 간 CLI](insights-cli-samples.md)를 사용하여 메트릭을 쿼리합니다.
* 포털, Azure Monitor REST API, PowerShell cmdlet 또는 플랫폼 간 CLI를 사용하여 작업 로그를 쿼리합니다.
* 리소스에 대한 [진단 설정](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) 을 확인합니다.
* 구독에 대한 [로그 프로필](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) 을 봅니다.
* 자동 크기 조정 설정을 봅니다.
* 경고 활동 및 설정을 봅니다.
* Application Insights 데이터에 액세스하고 AI Analytics에서 데이터를 봅니다.
* 작업 영역에 대한 사용 현황 데이터를 포함하여 Log Analytics 작업 영역 데이터를 검색합니다.
* Log Analytics 관리 그룹을 봅니다.
* Log Analytics 검색 스키마를 검색합니다.
* Log Analytics 인텔리전스 팩을 나열합니다.
* Log Analytics 저장 검색을 검색 및 실행합니다.
* Log Analytics 저장소 구성을 검색합니다.

> [!NOTE]
> 이 역할은 이벤트 허브에 스트리밍되었거나 저장소 계정에 저장된 로그 데이터에 대한 읽기 액세스를 부여하지 않습니다. [아래를 참조하세요](#security-considerations-for-monitoring-data) .
> 
> 

### <a name="monitoring-contributor"></a>Monitoring Contributor
Monitoring Reader 역할이 할당된 사용자는 구독의 모든 모니터링 데이터를 볼 수 있으며, 모니터링 설정을 만들거나 수정할 수 있지만 다른 리소스는 수정할 수 없습니다. 이 역할은 Monitoring Reader 역할의 상위 집합이며, 조직의 모니터링 팀 구성원이거나 위의 권한 외에도 다음이 필요한 관리되는 서비스 제공자인 사용자에게 적합합니다.

* 공유 대시보드로 모니터링 대시보드를 게시합니다.
* 리소스에 대한 [진단 설정](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) 을 구성합니다.*
* 구독에 대한 [로그 프로필](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) 을 설정합니다.*
* [Azure 경고](monitoring-overview-unified-alerts.md)를 통해 경고 규칙 활동 및 설정을 지정합니다.
* Application Insights 웹 테스트 및 구성 요소를 만듭니다.
* Log Analytics 작업 영역 공유 키를 나열합니다.
* Log Analytics 인텔리전스 팩을 사용하거나 사용하지 않도록 설정합니다.
* Log Analytics 저장 검색을 만들고 삭제합니다.
* Log Analytics 저장소 구성을 만들고 삭제합니다.

*사용자가 로그 프로필이나 진단 설정을 구성하려면 대상 리소스(저장소 계정 또는 이벤트 허브 네임스페이스)에 대한 ListKeys 권한도 별도로 받아야 합니다.

> [!NOTE]
> 이 역할은 이벤트 허브에 스트리밍되었거나 저장소 계정에 저장된 로그 데이터에 대한 읽기 액세스를 부여하지 않습니다. [아래를 참조하세요](#security-considerations-for-monitoring-data) .
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>권한 및 사용자 지정 RBAC 역할 모니터링
위의 기본 제공 역할이 팀의 정확한 요구에 부합하지 못할 경우 더 세밀하게 지정한 권한을 갖는 [사용자 지정 RBAC 역할](../role-based-access-control/custom-roles.md) 을 만들 수 있습니다. 다음은 공통 Azure 모니터 RBAC 작업과 그에 대한 설명입니다.

| 작업 | 설명 |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |작업 그룹을 읽고 쓰고 삭제합니다. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |활동 로그 알림을 읽고 쓰고 삭제합니다. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |경고 규칙을 읽고 쓰고 삭제합니다(경고 클래식에서). |
| Microsoft.Insights/AlertRules/Incidents/Read |경고 규칙에 대한 사건 나열(트리거된 경고 규칙 내역). 포털에만 적용됩니다. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |자동 크기 조정 설정 읽기/쓰기/삭제 |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |진단 설정 읽기/쓰기/삭제 |
| Microsoft.Insights/EventCategories/Read |활동 로그의 가능한 모든 범주를 나열합니다. Azure Portal에서 사용됩니다. |
| Microsoft.Insights/eventtypes/digestevents/Read |이 권한은 사용자 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. |
| Microsoft.Insights/eventtypes/values/Read |구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | 네트워크 흐름 로그의 진단 설정을 읽고 쓰고 삭제합니다. |
| Microsoft.Insights/LogDefinitions/Read |이 권한은 사용자 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |로그 프로필을 읽고 쓰고 삭제합니다(이벤트 허브 또는 저장소 계정으로 활동 로그 스트리밍). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |근 실시간 메트릭 경고를 읽고 쓰고 삭제합니다. |
| Microsoft.Insights/MetricDefinitions/Read |메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
| Microsoft.Insights/Metrics/Read |리소스에 대한 메트릭을 읽습니다. |
| Microsoft.Insights/Register/Action |Azure Monitor 리소스 공급자를 등록합니다. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Application Insights에 대한 로그 경고를 읽고 쓰고 삭제합니다. |



> [!NOTE]
> 리소스에 대한 알림, 진단 설정 및 메트릭에 액세스하려면 해당 사용자에게 리소스 형식과 리소스 범위에 대한 읽기 액세스 권한이 있어야 합니다. 저장소 계정에 보관하거나 이벤트 허브에 스트리밍하는 진단 설정 또는 로그 프로필을 만들려면 해당 사용자에게 대상 리소스에 대한 ListKeys 권한도 있어야 합니다.
> 
> 

예를 들어, 다음과 같이 위의 표를 사용하여 "활동 로그 판독자"에 대한 사용자 지정 RBAC 역할을 만들 수 있습니다.

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>모니터링 데이터에 대한 보안 고려 사항
모니터링 데이터, 특히 로그 파일에는 IP 주소나 사용자 이름 같은 중요 정보가 포함될 수 있습니다. Azure의 모니터링 데이터는 다음 3가지 기본 형태로 제공됩니다.

1. 활동 로그. Azure 구독에서 모든 제어 관련 작업을 설명합니다.
2. 진단 로그. 리소스가 내보낸 로그입니다.
3. 메트릭. 리소스가 내보낸 항목입니다.

이 세 데이터 형식은 저장소 계정에 저장되거나 이벤트 허브에 스트리밍되며, 모두 범용 Azure 리소스입니다. 범용 리소스이기 때문에 이 항목의 만들기, 삭제 및 액세스는 관리자에게 예약된 권한이 필요한 작업입니다. 오용을 방지하기 위해 모니터링 관련 리소스에는 다음 방법을 적용하는 것이 좋습니다.

* 모니터링 데이터에는 단일 전용 저장소 계정을 사용합니다. 모니터링 데이터를 여러 저장소 계정으로 분리해야 할 경우, 모니터링 데이터와 비 모니터링 데이터 간에 저장소 계정을 공유하여 사용하지 않습니다. 모니터링 데이터에 대한 액세스 권한만 필요한 대상(예: 타사 SIEM)에게 부주의하게 비 모니터링 데이터에 대한 액세스 권한을 줄 가능성이 있기 때문입니다.
* 같은 이유로 모든 진단 설정에서 단일 전용 Service Bus 또는 Event Hub 네임스페이스를 사용합니다.
* 별도의 리소스 그룹을 유지하여 모니터링 관련 저장소 계정이나 이벤트 허브에 대한 액세스를 제한하고, 모니터링 역할에 [범위를 사용하여](../role-based-access-control/overview.md#basics-of-access-management-in-azure) 액세스를 해당 리소스 그룹으로만 한정합니다.
* 사용자가 모니터링 데이터 액세스만 필요할 경우 구독에서 이벤트 허브나 저장소 계정에 ListKeys 권한을 부여해서는 안 됩니다. 그 대신 리소스나 리소스 그룹(전용 모니터링 리소스 그룹이 있는 경우) 범위에서 사용자에게 해당 건한을 부여합니다.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>모니터링 관련 저장소 계정에 대한 액세스 제한
사용자나 응용 프로그램이 저장소 계정의 모니터링 데이터에 대한 액세스를 필요로 할 경우, Blob 저장소에 대한 서비스 수준 읽기 전용 액세스 권한을 통해 모니터링 데이터를 포함하는 저장소 계정에서 [계정 SAS를 생성](https://msdn.microsoft.com/library/azure/mt584140.aspx) 해야 합니다. PowerShell에서는 다음과 같습니다.

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

그런 다음 해당 저장소 계정에서의 읽기가 필요한 개체에게 토큰을 부여하면 해당 저장소 계정의 모든 Blob을 나열하고 읽을 수 있습니다.

또는 RBAC로 이 권한을 제어해야 할 경우 해당 특정 저장소 계정에서 개체에 Microsoft.Storage/storageAccounts/listkeys/action 권한을 부여할 수 있습니다. 저장소 계정에 보관하기 위해 로그 프로필이나 진단 설정을 구성할 수 있는 사용자에게 필요한 항목입니다. 예를 들어, 한 저장소 계정에서 읽기만 필요한 사용자 또는 응용 프로그램에 대해 다음 사용자 지정 RBAC 역할을 만들 수 있습니다.

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys 권한이 있는 사용자는 기본 및 보조 저장소 계정 키를 나열할 수 있습니다. 이러한 키는 사용자에게 해당 저장소 계정의 모든 서명된 서비스(Blob, 큐, 테이블, 파일) 전체에서 모든 서명된 권한(읽기, 쓰기, Blob 만들기, Blob 삭제 등)을 부여합니다. 가능한 경우 위에서 설명한 계정 SAS를 사용하는 것이 좋습니다.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>모니터링 관련 이벤트 허브에 대한 액세스 제한
이벤트 허브에서도 비슷한 패턴을 따를 수 있지만 먼저 전용 수신 권한 규칙을 만들어야 합니다. 관련 모니터링 이벤트 허브를 수신 대기 하도록 하는 응용 프로그램에 대한 액세스 권한을 부여 하려면 다음을 수행합니다.

1. 수신 클레임만으로 모니터링 데이터를 스트리밍하기 위해 생성된 이벤트 허브에서 공유 액세스 정책을 만듭니다. 이 작업은 포털에서 수행할 수 있습니다. 예를 들어, 이 정책을 “monitoringReadOnly”라고 할 수 있습니다. 가능한 경우 소비자에게 직접 이 키를 제공하고 다음 단계를 건너뛰고자 할 수 있습니다.
2. 소비자가 임시로 키를 가져올 수 있어야 할 경우 해당 이벤트 허브에 대해 사용자에게 ListKeys 작업을 부여합니다. 이벤트 허브에 스트리밍하기 위해 로그 프로필이나 진단 설정을 구성할 수 있어야 하는 사용자에게 필요한 항목입니다. 예를 들어, RBAC 규칙을 만들 수 있습니다.
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>다음 단계
* [Resource Manager의 RBAC 및 권한에 대해 읽기](../role-based-access-control/overview.md)
* [Azure의 모니터링 개요 읽기](monitoring-overview.md)

