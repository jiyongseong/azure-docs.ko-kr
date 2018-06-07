---
title: Azure Automation의 역할 기반 Access Control
description: RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스 관리가 가능합니다. 이 문서에서는 Azure Automation에서 RBAC를 설정하는 방법을 설명합니다.
keywords: 자동화 rbac, 역할 기반 액세스 제어, azure rbac
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f758d6aec25ce0ef6bf9a0ecab34189296c81fc0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Azure Automation의 역할 기반 Access Control

RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스 관리가 가능합니다. [RBAC](../role-based-access-control/role-assignments-portal.md)를 사용하여 팀 내에서 업무를 분리하고 사용자, 그룹 및 응용 프로그램에 해당 작업을 수행하는 데에만 필요한 권한을 부여할 수 있습니다. Azure Portal, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 사용자에게 역할 기반 액세스에 대한 권한을 부여할 수 있습니다.

## <a name="roles-in-automation-accounts"></a>Automation 계정의 역할

Azure Automation의 Automation 계정 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 액세스를 허용합니다. 다음은 Automation 계정에서 지원하는 기본 제공 역할입니다.

| **역할** | **설명** |
|:--- |:--- |
| 소유자 |소유자 역할을 사용하면 Automation 계정을 관리하기 위해 다른 사용자, 그룹 및 응용 프로그램에 대한 액세스 권한 제공이 포함된 Automation 계정 내에서 모든 리소스 및 동작에 액세스할 수 있습니다. |
| 기여자 |참가자 역할을 사용하면 Automation 계정에 대한 다른 사용자의 액세스 권한 수정을 제외한 모든 사항을 관리할 수 있습니다. |
| 판독기 |읽기 역할을 사용하면 Automation 계정의 모든 리소스를 볼 수 있지만 변경할 수는 없습니다. |
| Automation 운영자 |Automation 연산자 역할을 사용하면 Runbook 이름 및 속성을 보고 Automation 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다. 이 역할은 자격 증명 자산 및 Runnbook 등의 Automation 계정 리소스를 보거나 수정하지 못하도록 보호하며 조직의 구성원이 이러한 Runbook을 여전히 실행하도록 하려는 경우 유용합니다. |
|Automation 작업 연산자|Automation 작업 연산자 역할을 사용하면 Automation 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다.|
|Automation Runbook 연산자|Automation Runbook 연산자 역할을 사용하면 Runbook의 이름 및 속성을 볼 수 있습니다.|
| Log Analytics 기여자 | Log Analytics Contributor 역할을 사용하면 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 저장소 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다.|
| Log Analytics 독자 | Log Analytics Reader 역할을 사용하면 모니터링 설정을 볼 뿐만 아니라 모든 모니터링 데이터를 보고 검색할 수 있습니다. 여기에는 모든 Azure 리소스에 대한 Azure 진단 구성 보기가 포함됩니다. |
| Monitoring Contributor | Monitoring Contributor 역할을 사용하면 모든 모니터링 데이터를 읽고 모니터링 설정을 업데이트할 수 있습니다.|
| Monitoring Reader | Montioring Reader 역할을 사용하면 모든 모니터링 데이터를 읽을 수 있습니다. |
| 사용자 액세스 관리자 |사용자 액세스 관리자 역할을 사용하면 Azure Automation 계정에 대한 사용자 액세스 권한을 관리할 수 있습니다. |

## <a name="role-permissions"></a>역할 권한

다음 표에서는 각 역할에 부여되는 특정 권한에 대해 설명합니다. 여기에는 권한을 부여하는 작업과 권한을 제한하는 NotActions이 포함될 수 있습니다.

### <a name="owner"></a>소유자

소유자는 액세스를 제외한 모든 것을 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|작업|설명|
|---|---|
|Microsoft.Automation/automationAccounts/|모든 유형의 리소스를 만들고 관리합니다.|

### <a name="contributor"></a>기여자

Contributor는 액세스를 제외한 모든 것을 관리할 수 있습니다. 다음 표에서는 역할에 부여되거나 거부된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|모든 유형의 리소스 만들기 및 관리|
|**조치 없음**||
|Microsoft.Authorization/*/Delete| 역할 및 역할 할당을 삭제합니다.       |
|Microsoft.Authorization/*/Write     |  역할 및 역할 할당을 만듭니다.       |
|Microsoft.Authorization/elevateAccess/Action    | 사용자 액세스 관리자를 만드는 기능을 거부합니다.       |

### <a name="reader"></a>판독기

Reader는 Automation 계정의 모든 리소스를 볼 수 있지만 변경할 수는 없습니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Automation 계정에서 모든 리소스를 봅니다. |

### <a name="automation-job-operator"></a>Automation 작업 연산자

Automation 작업 연산자 역할은 Automation 계정 범위에서 부여됩니다. 그러면 연산자 권한이 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Authorization/*/read|권한 부여를 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook의 작업을 나열합니다.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|일시 중지된 작업을 다시 시작합니다.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|진행 중인 작업을 취소합니다.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|작업 스트림 및 출력을 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|진행 중인 작업을 일시 중지합니다.|
|Microsoft.Automation/automationAccounts/jobs/write|작업을 만듭니다.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  역할 및 역할 할당을 읽습니다.       |
|Microsoft.Resources/deployments/*      |리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/* |지원 티켓을 만들고 관리합니다.|

### <a name="automation-runbook-operator"></a>Automation Runbook 연산자

Automation Runbook 운영자 역할은 Runbook 범위에서 부여됩니다. Automation Runbook 연산자 역할은 Runbook의 이름 및 속성을 볼 수 있습니다.  ‘Automation 작업 연산자’ 역할과 결합된 이 역할은 연산자가 Runbook에 대한 작업을 만들고 관리할 수 있도록 설정합니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

> [!NOTE]
> 운영자에게 계정의 모든 Runbook에 대한 작업을 관리할 수 있는 권한을 부여하려는 경우 외에는 ‘Automation 연산자’ 역할을 설정하지 마십시오.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Runbook을 나열합니다.        |
|Microsoft.Authorization/*/read      | 권한 부여를 읽습니다.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |역할 및 역할 할당을 읽습니다.         |
|Microsoft.Resources/deployments/*      | 리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/*      | 지원 티켓을 만들고 관리합니다.        |

### <a name="automation-operator"></a>Automation 운영자

Automation 연산자 역할은 작업을 만들고 관리할 수 있으며, Automation 계정의 모든 Runbook에 대한 Runbook 이름 및 속성을 읽을 수 있습니다.  참고: 개별 Runbook에 액세스하도록 연산자를 제어하려는 경우에는 이 역할을 설정하지 말고 대신 ‘Automation 작업 연산자’ 및 ‘Automation Runbook 연산자’ 역할을 사용합니다.  다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Authorization/*/read|권한 부여를 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook의 작업을 나열합니다.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|일시 중지된 작업을 다시 시작합니다.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|진행 중인 작업을 취소합니다.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|작업 스트림 및 출력을 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|진행 중인 작업을 일시 중지합니다.|
|Microsoft.Automation/automationAccounts/jobs/write|작업을 만듭니다.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |역할 및 역할 할당을 읽습니다.         |
|Microsoft.Resources/deployments/*      |리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/* |지원 티켓을 만들고 관리합니다.|

### <a name="log-analytics-contributor"></a>Log Analytics 기여자

Log Analytics Contributor 역할은 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 저장소 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.Automation/automationAccounts/*|Automation 계정을 관리합니다.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|가상 머신 확장을 만들고 관리합니다.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|클래식 저장소 계정 키를 나열합니다.|
|Microsoft.Compute/virtualMachines/extensions/*|클래식 가상 머신 확장을 만들고 관리합니다.|
|Microsoft.Insights/alertRules/*|경고 규칙 읽기/쓰기/삭제|
|Microsoft.Insights/diagnosticSettings/*|진단 설정 읽기/쓰기/삭제|
|Microsoft.OperationalInsights/*|Log Analytics를 관리합니다.|
|Microsoft.OperationsManagement/*|작업 영역에서 솔루션을 관리합니다.|
|Microsoft.Resources/deployments/*|리소스 그룹 배포를 만들고 관리합니다.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|리소스 그룹 배포를 만들고 관리합니다.|
|Microsoft.Storage/storageAccounts/listKeys/action|저장소 계정 키를 나열합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|

### <a name="log-analytics-reader"></a>Log Analytics 독자

Log Analytics Reader는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. 다음 표에서는 역할에 부여되고 거부된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Log Analytics에서 쿼리를 관리합니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics 데이터를 검색합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|
|**조치 없음**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|공유 액세스 키를 읽을 수 없습니다.|

### <a name="monitoring-contributor"></a>Monitoring Contributor

Monitoring Contributor는 모든 모니터링 데이터를 읽고 모니터링 설정을 업데이트할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.AlertsManagement/alerts/*|경고를 관리합니다.|
|Microsoft.AlertsManagement/alertsSummary/*|경고 대시보드를 관리합니다.|
|Microsoft.Insights/AlertRules/*|경고 규칙을 관리합니다.|
|Microsoft.Insights/components/*|Application Insights 구성 요소를 관리합니다.|
|Microsoft.Insights/DiagnosticSettings/*|진단 설정을 관리합니다.|
|Microsoft.Insights/eventtypes/*|구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다.|
|Microsoft.Insights/LogDefinitions/*|이 권한은 사용자 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다.|
|Microsoft.Insights/MetricDefinitions/*|메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다.|
|Microsoft.Insights/Metrics/*|리소스에 대한 메트릭을 읽습니다.|
|Microsoft.Insights/Register/Action|Microsoft Insights 공급자 등록|
|Microsoft.Insights/webtests/*|Application Insights 웹 테스트를 관리합니다.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Log Analytics 솔루션 팩을 관리합니다.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Log Analytics 저장 검색을 관리합니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics 작업 영역 검색|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics 작업 영역에 대한 키 나열|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Log Analytics 저장소 정보 구성을 관리합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|
|Microsoft.WorkloadMonitor/workloads/*|워크로드를 관리합니다.|

### <a name="monitoring-reader"></a>Monitoring Reader

Monitoring Reader는 모든 모니터링 데이터를 읽을 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics 작업 영역 검색|
|Microsoft.Support/*|지원 티켓 만들기 및 관리|

### <a name="user-access-administrator"></a>사용자 액세스 관리자

사용자 액세스 관리자는 Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|모든 리소스 읽기|
|Microsoft.Authorization/*|권한 부여 관리|
|Microsoft.Support/*|지원 티켓 만들기 및 관리|

## <a name="onboarding"></a>온보딩

다음 표에서는 변경 내용 추적 및 업데이트 관리 솔루션을 위해 가상 머신을 온보딩하는 데 필요한 최소 필수 사용 권한을 보여줍니다.

### <a name="onboarding-from-a-virtual-machine"></a>가상 머신에서 온보딩

|**작업**  |**사용 권한**  |**최소 범위**  |
|---------|---------|---------|
|새 배포 쓰기      | Microsoft.Resources/deployments/*          |구독          |
|새 리소스 그룹 쓰기      | Microsoft.Resources/subscriptions/resourceGroups/write        | 구독          |
|새로운 기본 작업 영역 만들기      | Microsoft.OperationalInsights/workspaces/write         | 리소스 그룹         |
|새 계정 만들기      |  Microsoft.Automation/automationAccounts/write        |리소스 그룹         |
|작업 영역 및 계정 연결      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|작업 영역</br>Automation 계정
|솔루션 만들기      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |리소스 그룹          |
|MMA 확장 만들기      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|저장된 검색 만들기      | Microsoft.OperationalInsights/workspaces/write          | 작업 영역         |
|범위 구성 만들기      | Microsoft.OperationalInsights/workspaces/write          | 작업 영역         |
|범위 구성에 솔루션 연결      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | 해결 방법         |
|온보딩 상태 확인 - 작업 영역 읽기      | Microsoft.OperationalInsights/workspaces/read         | 작업 영역         |
|온보딩 상태 확인 - 계정의 연결된 작업 영역 속성 읽기     | Microsoft.Automation/automationAccounts/read      | Automation 계정        |
|온보딩 상태 확인 - 솔루션 읽기      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 해결 방법         |
|온보딩 상태 확인 - VM 읽기      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|온보딩 상태 확인 - 계정 읽기      | Microsoft.Automation/automationAccounts/read  |  Automation 계정   |

### <a name="onboarding-from-automation-account"></a>Automation 계정에서 온보딩

|**작업**  |**사용 권한** |**최소 범위**  |
|---------|---------|---------|
|새 배포 만들기     | Microsoft.Resources/deployments/*        | 구독         |
|새 리소스 그룹 만들기     | Microsoft.Resources/subscriptions/resourceGroups/write         | 구독        |
|AutomationOnboarding 블레이드 - 새 작업 영역 만들기     |Microsoft.OperationalInsights/workspaces/write           | 리소스 그룹        |
|AutomationOnboarding 블레이드 - 연결된 작업 영역 읽기     | Microsoft.Automation/automationAccounts/read        | Automation 계정       |
|AutomationOnboarding 블레이드 - 솔루션 읽기     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 해결 방법        |
|AutomationOnboarding 블레이드 - 작업 영역 읽기     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | 작업 영역        |
|작업 영역 및 계정에 대한 링크 만들기     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
|Shoebox의 계정 쓰기      | Microsoft.Automation/automationAccounts/write        | 계좌        |
|솔루션 만들기      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | 리소스 그룹         |
|저장된 검색 만들기/편집     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
|범위 구성 만들기/편집     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
|범위 구성에 솔루션 연결      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | 해결 방법         |
|**2단계 - 여러 VM 온보딩**     |         |         |
|VMOnboarding 블레이드 - MMA 확장 만들기     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|저장된 검색 만들기/편집     | Microsoft.OperationalInsights/workspaces/write           | 작업 영역        |
|범위 구성 만들기/편집  | Microsoft.OperationalInsights/workspaces/write   | 작업 영역|

## <a name="update-management"></a>업데이트 관리

업데이트 관리는 여러 서비스에서 해당 서비스를 제공합니다. 다음 표에서는 업데이트 관리 배포를 관리하는 데 필요한 사용 권한을 보여줍니다.

|**리소스**  |**역할**  |**범위**  |
|---------|---------|---------|
|Automation 계정     | Log Analytics 기여자       | Automation 계정        |
|Automation 계정    | Virtual Machine 기여자        | 계정의 리소스 그룹        |
|Log Analytics 작업 영역     | Log Analytics 기여자| Log Analytics 작업 영역        |
|Log Analytics 작업 영역 |Log Analytics 독자| 구독|
|해결 방법     |Log Analytics 기여자         | 해결 방법|
|Virtual Machine     | Virtual Machine 기여자        | Virtual Machine        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Azure Portal을 사용하여 Automation 계정에 대한 RBAC 구성

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Automation 계정 페이지에서 Automation 계정을 엽니다.
2. 왼쪽 위 모서리에 있는 **액세스 제어(IAM)** 컨트롤을 클릭합니다. 그러면 새 사용자, 그룹, 응용 프로그램을 추가하여 Automation 계정을 관리하고 Automation 계정에 구성 가능한 기존 역할을 볼 수 있는 **액세스 제어(IAM)** 페이지가 열립니다.

   ![액세스 단추](media/automation-role-based-access-control/automation-01-access-button.png)

### <a name="add-a-new-user-and-assign-a-role"></a>새 사용자 추가 및 역할 할당

1. **액세스 제어(IAM)** 페이지에서 **+추가**를 클릭하여 사용자, 그룹 또는 응용 프로그램을 추가할 수 있는 **사용 권한 추가** 페이지를 열고 역할을 할당합니다.

2. 사용 가능한 역할 목록에서 역할을 선택합니다. Automation 계정이 지원하는 기본 제공 역할이나 사용자가 정의한 사용자 지정 역할을 선택할 수 있습니다.

3. **선택** 필드에서 권한을 부여하려는 사용자의 사용자 이름을 입력합니다. 목록에서 사용자를 선택하고 **저장**을 클릭합니다.

   ![사용자 추가](media/automation-role-based-access-control/automation-04-add-users.png)

   이제 선택된 역할이 할당되어 **사용자** 페이지에 추가된 사용자가 표시됩니다.

   ![사용자 나열](media/automation-role-based-access-control/automation-05-list-users.png)

   또한 **역할** 페이지에서 사용자에게 역할을 할당할 수도 있습니다.
4. **액세스 제어(IAM)** 페이지에서 **역할**을 클릭하여 **역할** 페이지를 엽니다. 이 페이지에서 역할의 이름, 해당 역할에 할당된 사용자 및 그룹의 수를 볼 수 있습니다.

    ![사용자 페이지에서 역할 할당](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > 역할 기반 액세스 제어는 Automation 계정 범위에서만 설정할 수 있으며 Automation 계정 아래의 리소스에는 설정할 수 없습니다.

### <a name="remove-a-user"></a>사용자 제거

Automation 계정을 관리하지 않는 사용자 또는 더 이상 조직에서 일하지 않는 사용자의 액세스 권한을 제거할 수 있습니다. 다음은 사용자를 제거하는 단계입니다.

1. **액세스 제어(IAM)** 페이지에서 사용자가 제거하려는 항목을 선택하고 **제거**를 클릭합니다.
2. 할당 세부 정보 창에서 **제거** 단추를 클릭합니다.
3. **예** 를 클릭하여 제거를 확인합니다.

   ![사용자 제거](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>역할이 할당된 사용자

역할을 할당받은 사용자가 Azure에 로그인하고 해당 Automation 계정을 선택하면 **디렉터리** 목록에 소유자 계정이 나열된 것을 볼 수 있습니다. 추가된 Automation 계정을 보려면 기본 디렉터리를 소유자의 기본 디렉터리로 전환해야 합니다.

### <a name="user-experience-for-automation-operator-role"></a>Automation 운영자 역할에 대한 사용자 환경

Automation 운영자 역할이 할당된 사용자가 할당된 Automation 계정을 볼 때 사용자는 Automation 계정에서 만든 Runbook 목록, Runbook 작업 및 일정을 볼 수 있지만 해당 정의는 볼 수 없습니다. 사용자는 Runbook 작업을 시작, 중지, 일시 중단, 다시 시작 또는 예약할 수 있습니다. 사용자는 구성, Hybrid Worker 그룹 또는 DSC 노드와 같은 다른 Automation 리소스에 액세스할 수 없습니다.

![리소스에 대한 액세스 권한 없음](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

사용자에게는 일정을 보고 만드는 액세스 권한이 있지만 다른 자산 형식에는 액세스할 수 없습니다.

이 사용자는 Runbook과 연결된 웹 후크를 볼 액세스 권한도 없습니다.

![Webhook에 대한 액세스 없음](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Azure PowerShell을 사용하여 Automation 계정에 대한 RBAC를 구성합니다.

다음 [Azure PowerShell cmdlet](../role-based-access-control/role-assignments-powershell.md)을 사용하여 Automation 계정에 역할 기반 액세스를 구성할 수도 있습니다.

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx)에는 Azure Active Directory에서 사용할 수 있는 모든 RBAC 역할이 나열됩니다. 이 명령과 **이름** 속성을 함께 사용하여 특정 역할에서 수행하는 모든 작업을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

예제 출력은 다음과 같습니다.

```azurepowershell-interactive
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx)에는 지정된 범위의 Azure AD RBAC 역할 할당이 나열됩니다. 이 명령은 매개 변수 없이 구독에서 할당한 모든 역할 할당을 반환합니다. **ExpandPrincipalGroups** 매개 변수를 사용하여 지정된 사용자에 대한 액세스 할당 뿐만 아니라 사용자가 멤버인 그룹에 대한 액세스 할당을 나열합니다.
    **예제:** 다음 명령을 사용하여 자동화 계정 안에 있는 모든 사용자와 해당 역할을 나열합니다.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

예제 출력은 다음과 같습니다.

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx)는 특정 범위에서 사용자, 그룹, 응용 프로그램에 액세스 권한을 할당합니다.
    **예제:** 다음 명령을 사용하여 Automation 계정 범위의 사용자에 "Automation 운영자" 역할을 할당합니다.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

예제 출력은 다음과 같습니다.

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

[Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx)를 사용하여 특정 범위에서 지정된 사용자, 그룹 또는 응용 프로그램의 액세스 권한을 제거합니다.
    **예제:** 다음 명령을 사용하여 Automation 계정 범위의 "Automation 운영자" 역할에서 사용자를 제거합니다.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

앞의 예제에서 **로그인 ID**, **구독 ID**, **리소스 그룹 이름** 및 **Automation 계정 이름**을 계정 세부 정보로 바꿉니다. 사용자 역할 할당을 계속 제거하기 전에 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation에 RBAC를 구성하는 다양한 방법에 대한 자세한 내용은 [Azure PowerShell로 RBAC 관리](../role-based-access-control/role-assignments-powershell.md)를 참조하세요.
* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)
* 다른 runbook 형식에 대한 자세한 내용은 [Azure Automation Runbook 형식](automation-runbook-types.md)
