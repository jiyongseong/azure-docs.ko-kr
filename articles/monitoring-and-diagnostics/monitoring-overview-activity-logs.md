---
title: Azure 활동 로그 개요 | Microsoft Docs
description: Azure 활동 로그란 무엇이고 Azure 구독 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 9768fd96b8023ac97d8c5711e0c02f2c147e28f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Azure 활동 로그로 구독 활동 모니터링

**Azure 활동 로그**는 Azure에서 발생한 구독 수준 이벤트에 대한 정보를 제공하는 구독 로그입니다. 여기에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. 관리 범주에서 구독의 제어 평면 이벤트를 보고하므로, 이전에는 활동 로그의 명칭이 "감사 로그" 또는 "작업 로그"였습니다. 활동 로그를 통해 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 '무엇을, 누가, 언제'를 판단할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기(GET) 작업 또는 Classic/"RDFE" 모델을 사용하는 리소스에 대한 작업이 포함되지 않습니다.

![활동 로그 및 다른 종류의 로그 ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

그림 1: 활동 로그 및 다른 종류의 로그

활동 로그는 [진단 로그](monitoring-overview-of-diagnostic-logs.md)와 다릅니다. 활동 로그는 외부("제어 평면")의 리소스에 대한 작업 관련 데이터를 제공합니다. 진단 로그는 리소스에 의해 내보내지며, 해당 리소스의 작업("데이터 평면")에 대한 정보를 제공합니다.

> [!WARNING]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.
>
>

Azure Portal, CLI, PowerShell cmdlet 및 Azure Monitor REST API를 사용하여 활동 로그에서 이벤트를 검색할 수 있습니다.

> [!NOTE]
>  [최신 경고](monitoring-overview-unified-alerts.md)는 활동 로그 경고 규칙을 만들고 관리할 때 향상된 환경을 제공합니다.  [자세히 알아보기](monitoring-activity-log-alerts-new-experience.md).

활동 로그를 소개하는 다음 동영상을 봅니다.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]


## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그에는 여러 데이터 범주가 포함됩니다. 이러한 범주의 스키마에 대한 전체 정보는 [이 문서를 참조하세요](monitoring-activity-log-schema.md). 내용은 다음과 같습니다.
* **관리** - 이 범주에는 Resource Manager를 통해 수행한 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드가 포함되어 있습니다. 이 범주에 표시되는 이벤트의 유형의 예로는 "가상 머신 만들기", "네트워크 보안 그룹 삭제" 등이 있습니다. 사용자나 응용 프로그램이 Resource Manager를 사용하여 취하는 모든 동작은 특정 리소스 종류에 대한 작업으로 모델링됩니다. 작업 유형이 쓰기, 삭제 또는 동작이면 해당 작업의 시작 및 성공이나 실패 레코드가 모두 관리 범주에 기록됩니다. 관리 범주에는 구독의 역할 기반 액세스 제어 변경 내용도 포함됩니다.
* **서비스 상태** - 이 범주에는 Azure에서 발생한 모든 서비스 상태 관련 인시던트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "미국 동부의 SQL Azure가 가동 중지 상태입니다." 등이 있습니다. 서비스 상태 이벤트는 작업 필요, 복구 지원, 인시던트, 유지 관리, 정보 또는 보안의 5가지 중 하나이며 구독에 이벤트의 영향을 받는 리소스가 있는 경우에만 표시됩니다.
* **경고** - 이 범주에는 모든 Azure 경고 활성화의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "지난 5분 동안 myVM의 CPU 사용률이 80%를 초과했습니다." 등이 있습니다. 다수의 Azure 시스템에서 경고 개념이 사용됩니다. 일종의 규칙을 정의하여 조건이 해당 규칙과 일치하면 알림을 수신할 수 있습니다. 지원되는 Azure 경고 유형이 '활성화'되거나 알림 생성을 위한 조건이 충족될 때마다 활성화 레코드도 이 활동 로그 범주로 푸시됩니다.
* **자동 크기 조정** - 이 범주에는 구독에서 정의한 자동 크기 조정 설정을 기준으로 하는 자동 크기 조정 엔진 작업 관련 이벤트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "자동 크기 조정 강화 동작이 실패했습니다." 등이 있습니다 자동 크기 조정을 사용하면 자동 크기 조정 설정을 사용하여 시간 및/또는 로드(메트릭) 데이터를 기준으로 지원되는 리소스 종류의 인스턴스 수를 자동으로 규모 확장하거나 규모 감축할 수 있습니다. 강화 또는 규모 축소 조건이 충족되면 시작 이벤트와 성공 또는 실패 이벤트가 이 범주에 기록됩니다.
* **권장 사항** - 이 범주에는 웹 사이트 및 SQL Server와 같은 특정 리소스 종류의 권장 이벤트가 포함됩니다. 이러한 이벤트는 리소스를 보다 효율적으로 사용하는 권장 방법을 제공합니다. 권장 사항을 내보내는 리소스가 있는 경우에만 이러한 형식의 이벤트가 수신됩니다.
* **보안** - 이 범주에는 Azure Security Center에서 경고가 생성한 모든 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "의심스러운 이중 확장 파일이 실행되었습니다." 등이 있습니다.
* **정책 및 리소스 상태** - 이러한 범주는 이벤트를 포함하지 않으며 나중에 사용하도록 예약됩니다.

## <a name="event-schema-per-category"></a>범주별 이벤트 스키마
[범주별 활동 로그 이벤트 스키마를 파악하려면 이 문서를 참조하세요.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>활동 로그에서 수행할 수 있는 작업
활동 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![Azure 활동 로그](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* **Azure Portal**에서 쿼리하고 봅니다.
* [활동 로그 이벤트에서 경고 만들기](monitoring-activity-log-alerts.md)
* [타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 **Event Hub**](monitoring-stream-activity-logs-event-hubs.md)로 스트림합니다.
* [**PowerBI 콘텐츠 팩**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 PowerBI에서 분석합니다.
* [보관 또는 수동 검사를 위해 **Storage 계정**에 저장합니다](monitoring-archive-activity-log.md). **로그 프로필**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* PowerShell Cmdlet, CLI 또는 REST API를 통해 쿼리합니다.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Azure Portal에서 활동 로그 쿼리
Azure Portal 내에서는 다음과 같은 여러 위치에서 활동 로그를 볼 수 있습니다.
* **활동 로그**는 왼쪽 탐색 창의 **모든 서비스** 아래에서 에서 활동 로그를 검색하여 액세스할 수 있습니다.
* **모니터**는 왼쪽 탐색 창에 기본으로 표시됩니다. 활동 로그는 Azure Monitor의 한 섹션입니다.
* 모든 리소스의 **리소스**, 예를 들어 Virtual Machine에 대한 구성 블레이드에서 볼 수 있습니다. 활동 로그는 대부분의 이러한 리소스 블레이드에서 섹션 중 하나이며, 클릭하면 해당 특정 리소스와 관련된 이벤트를 자동으로 필터링합니다.

Azure Portal에서 이러한 필드에 의해 활동 로그를 필터링할 수 있습니다.
* Timespan - 이벤트에 대한 시작 및 종료 시간입니다.
* 범주 - 위에서 설명한 것과 같이 이벤트 범주입니다.
* 구독 - 하나 이상의 Azure 구독 이름입니다.
* 리소스 그룹 - 해당 구독 내에서 하나 이상의 리소스 그룹입니다.
* 리소스(이름) - 특정 리소스의 이름입니다.
* 리소스 종류 - 리소스의 종류입니다(예: Microsoft.Compute/virtualmachines).
* 작업 이름 - Azure Resource Manager 작업의 이름입니다(예: Microsoft.SQL/servers/Write).
* 심각도 - 이벤트의 심각도 수준입니다(정보 제공, 경고, 오류, 중요).
* 이벤트를 시작한 사람 - ‘호출자’ 또는 작업을 수행한 사용자입니다.
* 검색 열기 - 모든 이벤트의 모든 필드에서 해당 문자열을 검색하는 열려 있는 텍스트 검색 상자입니다.

필터 집합을 정의하면, 나중에 적용된 해당 필터를 사용하여 동일한 쿼리를 다시 수행해야 하는 경우 세션 간에 유지되는 쿼리로 저장할 수 있습니다. 또한 쿼리를 항상 특정 이벤트를 주시하도록 Azure 대시보드에 고정할 수 있습니다.

“적용”을 클릭하면 쿼리가 실행되고 일치하는 모든 이벤트를 표시합니다. 목록에서 이벤트를 클릭하면 해당 이벤트에 대한 요약 및 해당 이벤트의 전체 원시 JSON이 표시됩니다.

[Log Analytics 활동 로그 분석 솔루션](../log-analytics/log-analytics-activity.md)의 활동 로그 데이터를 표시하는 **로그 검색** 아이콘을 클릭하면 더 많은 기능에 대해 확인할 수 있습니다. 활동 로그 블레이드는 로그에 대한 기본 필터/찾아보기 경험을 제공하지만, Log Analytics를 사용하면 더 효과적으로 데이터를 피벗, 쿼리 및 시각화할 수 있습니다.

## <a name="export-the-activity-log-with-a-log-profile"></a>로그 프로필을 사용하여 활동 로그 내보내기
**로그 프로필**은 활동 로그를 내보내는 방식을 제어합니다. 로그 프로필을 사용하여 다음을 구성할 수 있습니다.

* 활동 로그를 보낼 위치(Storage 계정 또는 Event Hubs)
* 보낼 이벤트 범주(쓰기, 삭제, 작업) *로그 프로필 및 활동 로그 이벤트에서 "범주"의 의미는 다릅니다. 로그 프로필에서 "범주"는 작업 유형(쓰기, 삭제 작업)을 나타냅니다. 활동 로그 이벤트에서 "범주" 속성은 원본 또는 이벤트의 유형(예: 관리, ServiceHealth, 경고 등)을 나타냅니다.*
* 내보낼 하위 지역(위치). 활동 로그의 많은 이벤트가 전역 이벤트이므로 “전역”이 포함되었는지 확인합니다.
* 활동 로그를 Storage 계정에 유지해야 하는 기간
    - 보존이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
    - 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예: Event Hubs 또는 Log Analytics 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

로그를 내보내는 것과 동일한 구독에 위치하지 않는 저장소 계정 또는 Event Hub 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

이러한 설정은 포털의 활동 로그 블레이드에서 "내보내기" 옵션을 통해 구성할 수 있습니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다. 하나의 구독에는 하나의 로그 프로필만 포함할 수 있습니다.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Azure Portal을 사용하여 로그 프로필 구성
활동 로그를 Event Hub로 스트림하거나 Azure Portal의 "내보내기" 옵션을 사용하여 Storage 계정에 저장할 수 있습니다.

1. 포털 왼쪽에 있는 메뉴를 사용하여 **활동 로그**로 이동합니다.

    ![포털에서 활동 로그로 이동](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 블레이드 맨 위에서 **내보내기** 단추를 클릭합니다.

    ![포털에서 내보내기 단추](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 표시되는 블레이드에서 다음을 선택할 수 있습니다.  
  * 이벤트를 내보내려는 지역
  * 이벤트를 저장하려는 Storage 계정
  * 저장소에서 이러한 이벤트를 유지하려는 기간(일). 0일로 설정하면 로그를 계속 유지합니다.
  * 이러한 이벤트를 스트리밍하기 위해 Event Hub를 만들 Service Bus 네임스페이스입니다.

     ![활동 로그 내보내기 블레이드](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **저장**을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet을 사용하여 로그 프로필 구성

#### <a name="get-existing-log-profile"></a>기존 로그 프로필 가져오기

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>로그 프로필 추가

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| Name |예 |로그 프로필의 이름입니다. |
| StorageAccountId |아니오 |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
| serviceBusRuleId |아니오 |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다. |
| 위치 |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
| RetentionInDays |예 |이벤트를 유지해야 하는 일 수는 1에서 2147483647 사이입니다. 0 값은 로그를 무기한(영원히) 저장합니다. |
| 범주 |아니요 |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |

#### <a name="remove-a-log-profile"></a>로그 프로필 제거
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 로그 프로필 구성

#### <a name="get-existing-log-profile"></a>기존 로그 프로필 가져오기

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

`name` 속성은 로그 프로필의 이름이어야 합니다.

#### <a name="add-a-log-profile"></a>로그 프로필 추가

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

CLI를 사용하여 모니터 프로필을 만드는 방법에 대한 전체 설명서는 [CLI 명령 참조](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)를 참조하세요.

#### <a name="remove-a-log-profile"></a>로그 프로필 제거

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>다음 단계
* [활동 로그(이전의 감사 로그)에 대해 자세히 알아보기](../azure-resource-manager/resource-group-audit.md)
* [Azure 활동 로그를 Event Hubs로 스트림](monitoring-stream-activity-logs-event-hubs.md)
