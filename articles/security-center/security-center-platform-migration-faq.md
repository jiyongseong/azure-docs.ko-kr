---
title: Security Center 플랫폼 마이그레이션 FAQ | Microsoft Docs
description: 이 FAQ는 Azure Security Center 플랫폼 마이그레이션에 대한 질문에 답변합니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: terrylan
ms.openlocfilehash: 6a88fbadd8fbf05a4942e42b535770f6f068af28
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="security-center-platform-migration-faq"></a>Security Center 마이그레이션 FAQ
2017년 6월 초에 Azure Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 FAQ는 플랫폼 마이그레이션에 대한 질문에 답변합니다.

## <a name="data-collection-agents-and-workspaces"></a>데이터 수집, 에이전트 및 작업 영역

### <a name="how-is-data-collected"></a>데이터를 어떻게 수집해야 하나요?
Security Center는 Microsoft Monitoring Agent를 사용하여 VM에서 보안 데이터를 수집했습니다. 보안 데이터에는 다음에 대한 정보가 포함됩니다.

- 보안 구성 - 취약성을 파악하는 데 사용됨
- 보안 이벤트 - 위협을 감지하는 데 사용

에이전트에 의해 수집된 데이터는 VM에 연결된 기존 Log Analytics 작업 영역 또는 Security Center에서 만든 새 작업 영역 중 하나에 저장됩니다. Security Center가 새 작업 영역을 만들 때 VM의 지리적 위치가 고려됩니다.

> [!NOTE]
> Microsoft Monitoring Agent는 Log Analytics 서비스 및 SCOM(System Center Operations Manager)에서 사용되는 것과 동일한 에이전트입니다.
>
>

자동 프로비전(이전에 로그 컬렉션이라고 함)이 사용 가능하거나 구독이 마이그레이션되어 있는 경우 Security Center는 Microsoft Monitoring Agent가 각 VM에 Azure 확장으로 이미 설치되어 있는지 확인합니다. Microsoft Monitoring Agent가 설치되어 있지 않은 경우 Security Center는 기본적으로:

- VM에 Microsoft Monitoring Agent 확장을 설치합니다.

   - Security Center에서 만든 작업 영역이 VM과 동일한 지리적 위치에 이미 있는 경우 에이전트는 이 작업 영역에 연결됩니다.
   - 작업 영역이 없는 경우 Security Center는 해당 지리적 위치에 새 리소스 그룹 및 기본 작업 영역을 만들고 에이전트를 해당 작업 영역에 연결합니다. 작업 영역 및 리소스 그룹에 대한 명명 규칙은 다음과 같습니다.

       작업 영역: DefaultWorkspace-[subscription-ID]-[geo]

       리소스 그룹: DefaultResouceGroup-[geo]

- Security Center의 VM과 관련된 가격 책정 계층에 따라 작업 영역에서 Security Center 솔루션을 사용하도록 설정합니다. 자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.
- 마이그레이션된 구독에 대해서만 Security Center에서 이전 Azure Monitoring Agent를 제거합니다.

> [!NOTE]
> Microsoft Monitoring Agent의 자동 설치를 무시하고 자신의 작업 영역을 사용할 수 있습니다.  [자동 에이전트 설치 및 작업 영역 만들기를 중지하는 방법](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) 및 [기존 작업 영역을 사용하는 방법](#how-can-i-use-my-existing-log-analytics-workspace)을 참조하세요.
>
>

작업 영역의 위치는 VM의 위치를 기반으로 합니다. 자세한 내용은 [데이터 보안](security-center-data-security.md)을 참조하세요. 구독에 여러 지리적 위치의 VM이 포함된 경우에는 Security Center에서 여러 작업 영역을 만듭니다. 여러 작업 영역은 데이터 프라이버시 규칙을 유지하기 위해 만들어집니다.

> [!NOTE]
> 플랫폼 마이그레이션 이전에 Security Center는 Azure Monitoring Agent를 사용하여 VM에서 보안 데이터를 수집하고 저장소 계정에 데이터를 저장했습니다. 플랫폼 마이그레이션 후에 Security Center는 Microsoft Monitoring Agent 및 작업 영역을 사용하여 동일한 데이터를 수집하고 저장합니다. 저장소 계정은 마이그레이션 후에 제거할 수 있습니다.  또한 Security Center는 플랫폼 마이그레이션 후 이전에 설치된 Azure Monitoring Agent를 제거합니다.
>
>

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Security Center에서 만든 작업 영역에서 Log Analytics에 대한 요금을 청구하나요?
번호 Security Center에서 만든 작업 영역은 노드 요금 청구당 Log Analytics에 구성된 동안 Log Analytics 요금이 청구되지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다.

- **무료 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 솔루션을 사용하도록 설정합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center는 기본 작업 영역에서 'Security' 솔루션을 사용하도록 설정합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요. 가격 책정 페이지 주소는 2017년 6월부터 보안 데이터 저장소 및 비례 배분 청구로 변경됩니다.

> [!NOTE]
> Security Center에서 만든 작업 영역의 Log Analytics 가격 책정 계층은 Security Center 청구에 영향을 주지 않습니다.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Microsoft Monitoring Agent 설치의 자동 프로비전을 위한 VM의 요건은 무엇입니까?
다음과 같은 경우 Windows 또는 Linux IaaS VM이 요건 충족됩니다.

- Microsoft Monitoring Agent 확장이 VM에 현재 설치되어 있지 않습니다.
- VM이 실행 중인 상태입니다.
- Windows 또는 Linux VM 에이전트가 설치되어 있습니다.
- VM이 웹 응용 프로그램 방화벽이나 차세대 방화벽과 같은 어플라이언스로 사용되지 않습니다.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?
**기본 작업 영역을 삭제하지 않는 것이 좋습니다.** Security Center는 기본 작업 영역을 사용하여 VM의 보안 데이터를 저장합니다.  작업 영역을 삭제하는 경우 Security Center는 이 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다.

복구하려면 삭제된 작업 영역에 연결된 VM에서 Microsoft Monitoring Agent를 제거합니다. Security Center는 에이전트를 다시 설치하고 새 기본 작업 영역을 만듭니다.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?

기존 Log Analytics 작업 영역을 선택하여 Security Center에서 수집한 데이터를 저장할 수 있습니다. 기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.

- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

   ![다른 작업 영역 사용][5]

2. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

   > [!NOTE]
   > 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.
   >
   >

3. **저장**을 선택합니다.
4. **저장**을 선택하고 나면 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

   - 새 작업 영역 설정을 **새 VM에만 적용**하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 Agent 설치(Microsoft Monitoring Agent가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
   - 새 작업 영역 설정을 **모든 VM에 적용**하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > 예를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 작성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.
   >
   >

   - 작업을 취소하려면 **취소**를 선택합니다.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Microsoft Monitoring Agent가 VM에 확장으로 이미 설치되어 있으면 어떻게 되나요?
Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. Security Center는 이미 연결된 작업 영역에서 VM의 보안 데이터를 저장합니다. Security Center는 Security Center 사용을 지원하기 위해 VM의 Azure 리소스 ID를 포함하도록 확장 버전을 업데이트합니다.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>컴퓨터에 확장판이 아닌 Microsoft Monitoring Agent를 설치하면 어떻게 되나요?
Microsoft Monitoring Agent를 Azure 확장으로 설치하는 것이 아니라 VM에 직접 설치하는 경우 Security Center는 Microsoft Monitoring Agent를 설치하지 않으며, 보안 모니터링 기능이 제한됩니다.

자세한 내용은 다음 섹션 [SCOM 또는 OMS 직접 에이전트가 VM에 이미 설치되어 있으면 어떻게 되나요?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)를 참조하세요.

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>SCOM 또는 OMS 직접 에이전트가 VM에 이미 설치되어 있으면 어떻게 되나요?
Security Center는 에이전트가 설치되어 있는지 미리 확인할 수 없습니다.  Security Center는 Microsoft Monitoring Agent 확장을 설치하려고 시도하고 기존에 설치된 에이전트로 인해 실패합니다.  이 실패로 인해 에이전트의 연결 설정을 해당 작업 영역에 우선 적용할 수 없고 multi-homing을 만들지 않습니다.

> [!NOTE]
> 에이전트 버전이 최신 OMS 에이전트 버전으로 업데이트됩니다.  이것은 SCOM 사용자에게도 적용됩니다.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>이러한 확장을 제거할 경우 어떤 영향이 있나요?
Microsoft Monitoring 확장을 제거하는 경우 Security Center는 VM의 보안 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다. 24시간 이내에 Security Center는 VM이 확장을 누락하고 확장을 다시 설치했는지 확인합니다.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?
보안 정책에서 구독에 대해 자동 프로비저닝을 해제할 수는 있지만, 해제하지 않는 것이 좋습니다. 자동 프로비저닝을 해제하면 Security Center 권장 사항 및 경고 기능이 제한됩니다. 표준 가격 책정 계층의 구독에는 자동 프로비저닝을 반드시 사용해야 합니다. 자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 표준 계층에 대한 구독이 구성된 경우 해당 구독에 대한 보안 정책을 열고 **체험** 계층을 선택합니다.

   ![가격 책정 계층 ][1]

2. 다음으로 **보안 정책 - 데이터 수집** 블레이드에서 **끄기**를 선택하여 자동 프로비저닝을 해제합니다.
   ![데이터 수집][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 옵트아웃해야 하나요?

> [!NOTE]
> 자동 프로비전을 옵트아웃하도록 선택하는 경우 [옵트아웃의 의미는 무엇인가요?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) 및 [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.
>
>

다음에 해당하는 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.

- Security Center에 의한 자동 에이전트 설치가 전체 구독에 적용됩니다.  VM의 하위 집합에 자동 설치를 적용할 수 없습니다. Microsoft Monitoring Agent와 함께 설치할 수 없는 중요한 VM이 있는 경우 자동 프로비전을 옵트아웃해야 합니다.
- Microsoft Monitoring Agent 확장을 설치하면 에이전트 버전이 업데이트됩니다. 이것은 직접 에이전트 및 SCOM 에이전트에 적용됩니다. 설치된 SCOM 에이전트가 2012 버전이고 업그레이드된 경우 SCOM 서버도 2012 버전이면 관리 기능이 손실될 수 있습니다. 설치된 SCOM 에이전트가 2012 버전인 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.
- 구독 외부의 사용자 지정 작업 영역(중앙 집중식 작업 영역)이 있는 경우 자동 프로비전을 옵트아웃해야 합니다. Microsoft Monitoring Agent 확장을 수동으로 설치하고 Security Center가 연결을 재정의하지 않고 작업 영역에 연결할 수 있습니다.
- 구독 내에 자신의 사용자 지정 작업 영역이 있고 구독당 작업 영역을 여러 개 만들지 않으려는 경우 두 가지 옵션이 있습니다.

   1. 자동 프로비전을 옵트아웃할 수 있습니다. 마이그레이션 후 기본 작업 영역 설정을 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)의 설명에 따라 설정합니다.
   2. 또는 마이그레이션이 완료되고, Microsoft Monitoring Agent가 VM에 설치되고, VM이 생성된 작업 영역에 연결되도록 허용합니다. 그런 다음 이미 설치된 에이전트를 재구성하도록 선택하여 기본 작업 영역 설정을 설정하고 자신의 사용자 지정 작업 영역을 선택합니다. 자세한 내용은 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)를 참조하세요.

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 의미는 무엇인가요?
마이그레이션이 완료되면 Security Center는 VM에서 보안 데이터를 수집할 수 없으며 일부 보안 권장 사항과 경고를 사용할 수 없습니다. 옵트아웃하는 경우 Microsoft Monitoring Agent를 수동으로 설치해야 합니다. [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 경우 권장되는 단계는 무엇인가요?
Security Center가 VM의 보안 데이터를 수집하고 권장 사항 및 경고를 제공할 수 있도록 Microsoft Monitoring Agent를 수동으로 설치해야 합니다. 설치에 대한 지침은 [Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agent.md)을 참조하세요.

에이전트를 기존 사용자 지정 작업 영역이나 Security Center가 만든 작업 영역에 연결할 수 있습니다. 사용자 지정 작업 영역에 'Security' 또는 'SecurityCenterFree' 솔루션이 활성화되어 있지 않으면 솔루션을 적용해야 합니다. 적용하려면 사용자 지정 작업 영역 또는 구독을 선택하고 **보안 정책 - 가격 책정 계층** 블레이드를 통해 가격 책정 계층을 적용합니다.

   ![가격 책정 계층 ][1]

Security Center는 선택한 가격 책정 계층을 기반으로 작업 영역에 올바른 솔루션을 사용하도록 설정합니다.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center에서 설치한 OMS 확장을 제거하려면 어떻게 할까요?
Microsoft Monitoring Agent를 수동으로 제거할 수 있습니다. Security Center 권장 사항 및 경고를 제한하기 때문에 권장되지는 않습니다.

> [!NOTE]
> 데이터 수집을 사용하는 경우 Security Center는 에이전트를 제거한 후에 다시 설치합니다.  수동으로 에이전트를 제거하기 전에 데이터 수집을 사용하지 않도록 설정해야 합니다. 데이터 수집을 사용하지 않도록 설정하는 지침은 [자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?)를 참조하세요.
>
>

수동으로 에이전트를 제거하려면:

1.  포털에서 **Log Analytics**을 엽니다.
2.  Log Analytics 블레이드에서 작업 영역을 선택합니다.
3.  모니터링하지 않을 각 VM을 선택하고 **연결 끊기**를 선택합니다.

   ![에이전트 제거][3]

> [!NOTE]
> Linux VM에 이미 비확장 OMS 에이전트가 설치된 경우 확장명을 제거하면 에이전트도 제거되고 고객이 다시 설치해야 합니다.
>
>

## <a name="existing-log-analytics-customers"></a>기존 Log Analytics 고객

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center에서 VM과 작업 영역 간의 모든 기존 연결을 재정의하나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 작업 영역 연결을 재정의하지 않습니다. 대신 Security Center에서 기존 작업 영역을 사용합니다.

Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 관련 VM에만 적용됩니다. 솔루션을 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 배포됩니다. [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 사용하면 솔루션에 범위를 적용할 수 있습니다.

Microsoft Monitoring Agent를 Azure 확장으로 설치하는 것이 아니라 VM에 직접 설치하는 경우 Security Center는 Microsoft Monitoring Agent를 설치하지 않으며, 보안 모니터링 기능이 제한됩니다.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>데이터 플랫폼 마이그레이션이 내 VM과 내 작업 영역 간에 연결을 끊는 것으로 의심되는 경우 어떻게 해야 할까요?
이런 상황은 일어나지 않습니다. 발생하는 경우 [Azure 지원을 요청](../azure-supportability/how-to-create-azure-support-request.md)하고 다음 세부 정보를 포함합니다.

- 영향을 받는 VM의 Azure 리소스 ID
- 연결이 끊기기 전에 확장에 구성된 작업 영역의 Azure 리소스 ID
- 이전에 설치된 에이전트 및 버전

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center에서 기존 Log Analytics 작업 영역에 솔루션을 설치하나요? 요금 청구에 영향을 주는 요인은 무엇인가요?
Security Center에서 VM이 만든 작업 영역에 이미 연결되어 있는지를 식별하는 경우 Security Center를 통해 가격 책정 계층에 따라 이 작업 영역에서 솔루션을 사용할 수 있습니다. 솔루션이 [솔루션 대상 지정](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)을 통해 관련 Azure VM에만 적용되므로 청구는 동일하게 유지됩니다.

- **체험 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center가 작업 영역에 'Security' 솔루션을 설치합니다.

   ![기본 작업 영역의 솔루션][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>내 환경에 이미 작업 영역이 있는 경우 보안 데이터를 수집하는 데 사용할 수 있나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 연결된 작업 영역 연결을 사용합니다. Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 [솔루션 대상 지정](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)을 통해 관련 VM에만 적용됩니다.

Security Center에서 VM에 Microsoft Monitoring Agent를 설치하면 Security Center에서 만든 기본 작업 영역을 사용합니다. 곧 고객은 사용되는 작업 영역을 구성할 수 있게 됩니다.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>내 작업 영역에 보안 솔루션이 이미 있습니다. 요금 청구에 영향을 주는 요인은 무엇인가요?
보안 및 감사 솔루션은 Azure VM에 대한 Security Center 표준 계층 기능을 활성화하는 데 사용됩니다. 보안 및 감사 솔루션이 작업 영역에 이미 설치되어 있는 경우 Security Center에서는 기존 솔루션을 사용합니다. 요금 청구는 변하지 않습니다.

## <a name="next-steps"></a>다음 단계
Security Center 플랫폼 마이그레이션을 자세히 알아보려면 다음을 참조하세요.

- [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)
- [Azure Security Center 문제 해결 가이드](security-center-troubleshooting-guide.md).

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
