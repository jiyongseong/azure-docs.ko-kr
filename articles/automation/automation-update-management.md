---
title: Azure의 업데이트 관리 솔루션
description: 이 문서는 이 솔루션을 사용하여 Windows 및 Linux 컴퓨터에 대한 업데이트를 관리하는 방법을 이해할 수 있도록 제공됩니다.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/23/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d93f79874ff65a1b6bb7ddd75932111c5caa6072
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195848"
---
# <a name="update-management-solution-in-azure"></a>Azure의 업데이트 관리 솔루션

Azure Automation의 업데이트 관리 솔루션을 사용하면 Azure, 온-프레미스 환경 또는 다른 클라우드 공급자에 배포된 Windows 및 Linux 컴퓨터에 대한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

Azure Automation 계정에서 직접 가상 머신에 업데이트 관리를 사용하도록 설정할 수 있습니다.
Automation 계정에서 가상 머신에 업데이트 관리를 사용하는 방법을 알아보려면 [여러 가상 머신에 대한 업데이트 관리](manage-update-multi.md)를 참조하세요. Azure Portal의 가상 머신 페이지에서 단일 가상 머신에 대한 업데이트 관리를 사용할 수도 있습니다. 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) 가상 머신 모두에 지원됩니다.

## <a name="solution-overview"></a>솔루션 개요

업데이트 관리를 통해 관리되는 컴퓨터는 다음 구성을 사용하여 평가 및 업데이트 배포를 수행합니다.

* Windows 또는 Linux용 Microsoft Monitoring Agent
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 컴퓨터용 Microsoft Update 또는 Windows Server Update Services

다음 다이어그램에서는 솔루션이 작업 영역에서 보안 업데이트를 평가하고, 연결된 모든 Windows Server 및 Linux 컴퓨터에 적용하는 방식과 관련된 동작 및 데이터 흐름을 개념적으로 보여 줍니다.

![업데이트 관리 프로세스 흐름](media/automation-update-management/update-mgmt-updateworkflow.png)

컴퓨터에서 업데이트 준수를 검색한 후 에이전트에서 Log Analytics에 정보를 대량으로 전달합니다. Windows 컴퓨터에서 준수 검사는 기본적으로 12시간마다 수행됩니다. 검사 일정 외에도, MMA(Microsoft Monitoring Agent)가 다시 시작되면 업데이트 설치 전과 업데이트 설치 후 15분 내에 업데이트 준수 검사가 시작됩니다. Linux 컴퓨터의 경우 기본적으로 3시간마다 준수 검사가 수행되며, MMA 에이전트가 다시 시작되면 15분 내에 준수 검사가 시작됩니다.

솔루션은 동기화하도록 구성된 소스를 기반으로 컴퓨터가 최신 상태를 유지하는 방식을 보고합니다. Windows 컴퓨터가 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft Update와 마지막으로 동기화된 시기에 따라 그 결과는 Microsoft Update가 표시하는 것과 다를 수 있습니다. 로컬 리포지토리에 보고하도록 구성된 Linux 컴퓨터와 공용 리포지토리에 보고하도록 구성된 Linux 컴퓨터에서도 마찬가지입니다.

> [!NOTE]
> 업데이트 관리에서는 특정 URL 및 포트가 서비스에 올바르게 보고하도록 설정되어야 합니다. 이러한 요구 사항에 대한 자세한 내용을 보려면 [Hybrid Workers에 대한 네트워크 계획](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

예약 배포를 만들어서 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. *선택 사항*으로 분류된 업데이트는 Windows 컴퓨터의 배포 범위에 포함되지 않으며, 필수 업데이트만 포함됩니다. 예약 배포는 컴퓨터를 명시적으로 지정하거나 특정 컴퓨터 집합의 로그 검색을 기반으로 하는 [컴퓨터 그룹](../log-analytics/log-analytics-computer-groups.md)을 선택하여 해당 업데이트를 받는 대상 컴퓨터를 정의합니다. 또한 업데이트 설치가 허용되는 시간을 승인하고 지정하는 일정을 지정합니다. Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 runbook을 볼 수 없고 구성이 필요하지 않습니다. 업데이트 배포가 생성되면 업데이트 배포는 포함된 컴퓨터에 지정된 시간에 마스터 업데이트 runbook을 시작하는 일정을 만듭니다. 이 마스터 runbook은 필수 업데이트를 설치하는 각 에이전트에서 하위 runbook을 시작합니다.

업데이트 배포에 지정된 날짜 및 시간에 대상 컴퓨터는 배포를 병렬로 실행합니다. 업데이트가 여전히 필수인지 확인하기 위한 검사가 수행된 수 업데이트가 설치됩니다. WSUS 클라이언트 컴퓨터의 경우 업데이트가 WSUS에서 승인되지 않으면 업데이트 배포가 실패합니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에서는 지원되는 운영 체제의 목록을 보여 줍니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | 업데이트 평가만 지원합니다.         |
|Windows Server 2008 R2 SP1 이상     |Windows PowerShell 4.0 이상이 필요합니다([WMF 4.0 다운로드](https://www.microsoft.com/download/details.aspx?id=40855)).</br> Windows PowerShell 5.1([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))은 안정성 개선을 위해 필요합니다.         |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나열되어 있습니다.

|운영 체제  |메모  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(Windows 7, Windows 10 등)는 지원되지 않습니다.        |
|Windows Server 2016 Nano Server     | 지원되지 않음       |

### <a name="client-requirements"></a>클라이언트 요구 사항

#### <a name="windows"></a>Windows

Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Microsoft 업데이트에 대한 액세스 권한을 가지고 있어야 합니다. 업데이트 관리는 System Center Configuration Manager와 함께 사용할 수 있습니다. 통합 시나리오에 대해 자세히 알아보려면 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md#configuration)을 참조하세요. [Windows 에이전트](../log-analytics/log-analytics-agent-windows.md)가 필요합니다. 이 에이전트는 Azure VM을 등록하는 경우 자동으로 설치됩니다.

#### <a name="linux"></a>Linux

Linux의 경우 컴퓨터에 개인 또는 공용일 수 있는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 여러 Log Analytics 작업 영역에 보고하도록 구성된 Linux용 OMS 에이전트는 이 솔루션에서 지원되지 않습니다.

Linux용 OMS 에이전트를 설치하고 최신 버전을 다운로드하는 방법에 대한 자세한 내용은 [Linux용 Operations Management Suite 에이전트](https://github.com/microsoft/oms-agent-for-linux)를 참조하세요. Windows용 OMS 에이전트를 설치하는 방법은 [Windows용 Operations Management Suite 에이전트](../log-analytics/log-analytics-windows-agent.md)를 검토하세요.

## <a name="permissions"></a>권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 자세한 내용은 [역할 기반 액세스 - 업데이트 관리](automation-role-based-access-control.md#update-management)를 참조하세요.

## <a name="solution-components"></a>솔루션 구성 요소

이 솔루션은 Automation 계정 및 직접 연결된 에이전트 또는 Operations Manager와 연결된 관리 그룹에 추가되는 다음 리소스로 구성됩니다.

### <a name="hybrid-worker-groups"></a>Hybrid Worker 그룹

이 솔루션을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 Log Analytics 작업 영역에 직접 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다. 솔루션에서 관리되는 각 Windows 컴퓨터의 경우 *호스트 이름 FQDN_GUID* 명명 규칙에 따라 Automation 계정에 대한 시스템 하이브리드 작업자 그룹으로 하이브리드 작업자 그룹 페이지 아래에 나열됩니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없으며, 그렇지 않은 경우 오류가 발생합니다. 이러한 그룹은 관리 솔루션을 지원하는 용도로만 사용할 수 있습니다.

하지만 솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

System Center Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결되면 다음 관리 팩이 Operations Manager에 설치됩니다. 이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩과 관련하여 아무 것도 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../log-analytics/log-analytics-om-agents.md)을 참조하세요.

### <a name="confirm-non-azure-machines-are-onboarded"></a>비Azure 컴퓨터가 등록되어 있는지 확인

몇 분 후에 다음 로그 검색을 실행하여 직접 연결된 컴퓨터에서 Log Analytics와 통신하고 있는지 확인할 수 있습니다.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Windows 컴퓨터에서 다음 사항을 검토하여 에이전트가 Log Analytics에 연결되었는지 확인할 수 있습니다.

1. 제어판에서 Microsoft Monitoring Agent를 열면 **Azure Log Analytics** 탭에서 에이전트가 **Microsoft Monitoring Agent가 Log Analytics에 성공적으로 연결되었습니다**라는 메시지를 표시합니다.
2. Windows 이벤트 로그를 열고, **응용 프로그램 및 서비스 Logs\Operations Manager**로 이동하여 원본 서비스 커넥터에서 이벤트 ID 3000 및 5002를 검색합니다. 이러한 이벤트는 컴퓨터가 Log Analytics 작업 영역에 등록되었으며 구성을 수신하고 있음을 나타냅니다.

에이전트가 Log Analytics 서비스와 통신할 수 없고 방화벽 또는 프록시 서버를 통해 인터넷과 통신하도록 구성된 경우 [Windows 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-agent-windows.md) 또는 [Linux 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-agent-linux.md)을 검토하여 방화벽 또는 프록시 서버가 올바르게 구성되었는지 확인합니다.

> [!NOTE]
> Linux 시스템에서 프록시 또는 OMS 게이트웨이와 통신하도록 구성되고 이 솔루션을 등록하는 경우, 다음 명령을 수행하여 omiuser 그룹 읽기 권한을 파일에 부여하도록 *proxy.conf* 권한을 업데이트하세요. `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

평가가 수행된 후 새로 추가된 Linux 에이전트의 상태가 **업데이트됨**으로 표시됩니다. 이 프로세스는 최대 6시간까지 걸릴 수 있습니다.

Operations Manager 관리 그룹이 Log Analytics와 통신하는지 확인하려면 [Log Analytics와 Operations Manager 통합 유효성 검사](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics)를 참조하세요.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-agents"></a>지원되는 에이전트

다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |솔루션은 Windows 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 필수 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |이 솔루션은 Linux 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필수 업데이트 설치를 시작합니다. |
| Operations Manager 관리 그룹 |예 |솔루션은 연결된 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집합니다.</br>Operations Manager 에이전트에서 Log Analytics로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

관리되는 Windows 컴퓨터 각각의 경우 검색은 하루에 두 번 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하여 상태가 변경되었는지, 상태가 변경되었다면 준수 검사가 시작되었는지 확인합니다. 관리되는 Linux 컴퓨터 각각의 경우 검색은 세 시간마다 수행됩니다.

관리되는 컴퓨터의 업데이트 데이터가 대시보드에 표시될 때까지 30분에서 6시간이 걸릴 수 있습니다.

## <a name="viewing-update-assessments"></a>업데이트 평가 보기

Automation 계정의 **업데이트 관리**를 클릭하여 컴퓨터 상태를 확인합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다.

목록에서 항목을 선택하여 컴퓨터, 업데이트 또는 배포에 대한 정보를 반환하는 로그 검색을 실행할 수 있습니다. 이렇게 하면 선택한 항목에 대한 쿼리가 있는 **로그 검색** 페이지가 열립니다.

## <a name="installing-updates"></a>업데이트 설치

작업 영역의 모든 Linux 및 Windows 컴퓨터에 대한 업데이트 평가가 완료된 후에는 *업데이트 배포*를 만들어서 필수 업데이트를 설치할 수 있습니다. 업데이트 배포는 예약된 일정에 따라 하나 이상의 컴퓨터에 필요한 업데이트를 설치합니다. 배포 범위에 포함되어야 하는 컴퓨터 또는 컴퓨터 그룹 외에도 배포 날짜 및 시간을 지정합니다. 컴퓨터 그룹에 대한 자세한 내용은 [Log Analytics의 컴퓨터 그룹](../log-analytics/log-analytics-computer-groups.md)을 참조하세요. 업데이트 배포에 컴퓨터 그룹이 포함되면 일정을 만들 때 그룹 멤버 자격이 한 번만 평가됩니다. 이후의 그룹 변경 내용은 반영되지 않습니다. 이 문제를 해결하려면 예약된 업데이트 배포를 삭제하고 다시 만들면 됩니다.

> [!NOTE]
> Azure Marketplace에서 배포된 VM은 기본적으로 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다. 이 동작은 작업 영역에 이 솔루션 또는 Windows VM을 추가한 후에도 변경되지 않습니다. 이 솔루션에서 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

Azure Marketplace에서 사용할 수 있는 주문형 RHEL(Red Hat Enterprise Linux) 이미지에서 만든 가상 머신은 Azure에 배포된 [RHUI(Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)에 액세스하도록 등록됩니다. 다른 모든 Linux 배포판은 지원되는 방법에 따라 배포판 온라인 파일 리포지토리에서 업데이트되어야 합니다.

## <a name="viewing-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 클릭하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되고, 업데이트가 필요한 컴퓨터 수, 운영 체제 및 세부 정보에 대한 링크와 관련된 정보가 표시됩니다. 각 업데이트를 선택할 수 있으며, 그러면 **로그 검색** 페이지가 표시되어 업데이트에 대한 자세한 정보를 보여 줍니다.

## <a name="viewing-update-deployments"></a>업데이트 배포 보기

**업데이트 배포** 탭을 클릭하여 기존 업데이트 배포의 목록을 볼 수 있습니다. 테이블에서 업데이트 배포 중 하나를 클릭하면 해당 업데이트 배포에 대한 **업데이트 배포 실행** 페이지가 열립니다.

![업데이트 배포 결과의 개요](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>업데이트 배포 만들기

화면 위쪽의 **업데이트 배포 예약** 단추를 클릭하여 **새 업데이트 배포** 페이지를 열어 새 업데이트 배포를 만듭니다. 다음 표에서 설명하는 속성에 대한 값을 제공해야 합니다.

| 자산 | 설명 |
| --- | --- |
| Name |업데이트 배포를 식별하는 고유 이름입니다. |
|운영 체제| Linux 또는or Windows|
| 업데이트할 컴퓨터 |저장된 검색을 선택하거나, 드롭다운에서 컴퓨터를 선택하고 개별 컴퓨터를 선택합니다. |
|업데이트 분류|필요한 모든 업데이트 분류를 선택합니다.|
|제외할 업데이트|제외할 모든 KB를 'KB' 접두사 없이 입력합니다.|
|일정 설정|시작 시간을 선택하고 되풀이에 대해 [한 번] 또는 [정기]를 선택합니다.|
| 유지 관리 기간 |업데이트에 대해 설정되는 시간(분)입니다. 값은 30분 이상 6시간 이하여야 합니다. |

## <a name="update-classifications"></a>업데이트 분류

다음 표에서는 각 분류에 대한 정의와 함께 업데이트 관리에서 업데이트 분류의 목록을 제공합니다.

### <a name="windows"></a>Windows

|분류  |설명  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 응용 프로그램에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 응용 프로그램 또는 파일에 대한 업데이트입니다.        |

### <a name="linux"></a>Linux

|분류  |설명  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 특성 또는 보안 업데이트에 중요하지 않은 기타 모든 업데이트입니다.        |

## <a name="ports"></a>포트

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 수행됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

Hybrid Runbook Worker에 필요한 포트에 대한 자세한 내용은 [Hybrid Worker 역할 포트](automation-hybrid-runbook-worker.md#hybrid-worker-role)를 참조하세요.

## <a name="search-logs"></a>로그 검색

포털에서 제공하는 세부 정보 외에도 로그에 대한 검색을 수행할 수 있습니다. **변경 내용 추적** 페이지가 열린 상태에서 **Log Analytics**를 클릭하면 **로그 검색** 페이지가 열립니다.

### <a name="sample-queries"></a>샘플 쿼리

다음 표에서는 이 솔루션에서 수집된 업데이트 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
| --- | --- |
|주 지역에서</br>&#124; where UpdateState == "필요함" and Optional == false</br>&#124; project Computer, Title, KBID, Classification, PublishedDate |업데이트가 누락된 모든 컴퓨터</br>다음 중 하나를 추가하여 OS를 제한합니다.</br>OSType = "Windows"</br>OSType == "Linux" |
| 주 지역에서</br>&#124; where UpdateState == "필요함" and Optional == false</br>&#124; where Computer == "ContosoVM1.contoso.com"</br>&#124; project Computer, Title, KBID, Product, PublishedDate |특정 컴퓨터의 누락된 업데이트(값을 사용자 고유의 컴퓨터 이름으로 대체)|
| 행사</br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "보안 업데이트 Updates" or Classification == "중요 업데이트")</br>&#124; where UpdateState == "필요함" and Optional == false </br>&#124; distinct Computer)) |중요 또는 보안 필수 업데이트가 누락된 컴퓨터의 오류 이벤트 |
| 주 지역에서</br>&#124; where UpdateState == "필요함" and Optional == false</br>&#124; distinct Title |모든 컴퓨터에 누락된 업데이트 구분 |
| UpdateRunProgress</br>&#124; where InstallationStatus == "failed" </br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |업데이트 실행에 실패한 업데이트가 있는 컴퓨터</br>다음 중 하나를 추가하여 OS를 제한합니다.</br>OSType = "Windows"</br>OSType == "Linux" |
| 주 지역에서</br>&#124; where OSType == "Linux"</br>&#124; where UpdateState != "필요하지 않음" and (Classification == "중요 업데이트" or Classification == "보안 업데이트")</br>&#124; summarize AggregatedValue = count() by Computer |중요 또는 보안 취약성을 해결하는 패키지 업데이트를 사용할 수 있는 모든 Linux 컴퓨터 목록 |
| UpdateRunProgress</br>&#124; where UpdateRunName == "DeploymentName"</br>&#124; summarize AggregatedValue = count() by Computer|이 업데이트 실행에서 업데이트된 컴퓨터(값을 사용자의 업데이트 배포 이름으로 대체) |

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager와 상호 작용

PC, 서버 및 모바일 장치를 관리하기 위해 System Center Configuration Manager에 투자하는 고객들은 SUM(소프트웨어 업데이트 관리) 주기의 일환으로 이 강력하고 완성도 있는 소프트웨어 업데이트 관리를 신뢰합니다.

System Center Configuration Manager와 관리 솔루션을 통합하는 방법을 알아보려면 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md)을 참조하세요.

## <a name="patching-linux-machines"></a>Linux 컴퓨터 패치

다음 섹션에서는 Linux 패치의 잠재적 문제에 대해 설명합니다.

### <a name="package-exclusion"></a>패키지 제외

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 컴퓨터에서 로컬로 수행하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적 인 것입니다.

업데이트 관리 실행을 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 redhat-release-server.x86_64입니다.

![제외할 Linux용 패키지](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>보안 패치가 적용되지 않음

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 그러면 지정된 조건에 맞는 업데이트에 적용되는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다. 업데이트 관리는 클라우드에서 업데이트 적용을 수행하므로, 로컬 컴퓨터에 해당 정보가 없더라도 일부 업데이트에는 업데이트 관리에서 보안에 영향을 주는 것으로 표시되는 플래그가 지정될 수 있습니다. 따라서 Linux 시스템에 중요 업데이트를 적용하면 해당 컴퓨터에 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있을 수 있으며, 이 경우 업데이트가 적용되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 컴퓨터를 호환되지 않는 것으로 보고할 수 있습니다.

사용되는 패치 모델이 서로 다르기 때문에 업데이트 분류를 통한 업데이트 배포가 openSUSE Linux에서 작동하지 않을 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 업데이트 관리 솔루션과 관련된 문제 해결에 도움이 되는 정보를 제공합니다.

솔루션 또는 가상 머신을 온보딩하는 동안 문제가 발생할 경우 이벤트 ID가 4502인 **응용 프로그램 및 서비스 로그\운영 관리자** 이벤트 로그와 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**를 포함하고 있는 이벤트 메시지를 확인합니다. 다음 표에는 특정 오류 메시지 및 각각의 해결 방법이 설명되어 있습니다.

| Message | 이유 | 해결 방법 |
|----------|----------|----------|
| 패치 관리용 컴퓨터를 등록할 수 없습니다.</br>예외와 함께 등록이 실패했습니다.</br>System.InvalidOperationException: {"메시지":"컴퓨터가 이미</br>다른 계정에 등록되었습니다. "} | 컴퓨터가 이미 업데이트 관리를 위한 다른 작업 영역에 등록되었습니다. | [하이브리드 Runbook 그룹을 삭제](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)하여 오래된 아티팩트를 정리합니다.|
| 패치 관리용 컴퓨터를 등록할 수 없습니다. 예외로 인해 등록이 실패했습니다.</br>System.Net.Http.HttpRequestException: 요청을 보내는 동안 오류가 발생했습니다. ---></br>System.Net.WebException: 기본 연결이</br>닫혔습니다. 받는 동안</br>예기치 않은 오류가 발생했습니다. ---> System.ComponentModel.Win32Exception:</br>클라이언트와 서버의 공통 알고리즘이 없기 때문에</br>서로 통신할 수 없습니다. | 프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](automation-hybrid-runbook-worker.md#network-planning)|
| 패치 관리용 컴퓨터를 등록할 수 없습니다.</br>예외와 함께 등록이 실패했습니다.</br>Newtonsoft.Json.JsonReaderException: 양의 무한대 값을 구문 분석하는 도중에 오류가 발생했습니다. | 프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](automation-hybrid-runbook-worker.md#network-planning)|
| \<wsid\>.oms.opinsights.azure.com 서비스에서 제공하는 인증서가</br>Microsoft 서비스에 사용된 인증서 기관에서</br>발급한 것이 아닙니다. 연락처</br>관리자에 연락하여</br>TLS/SSL 통신을 가로채는 프록시를 실행 중인지 확인합니다. |프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](automation-hybrid-runbook-worker.md#network-planning)|
| 패치 관리용 컴퓨터를 등록할 수 없습니다.</br>예외와 함께 등록이 실패했습니다.</br>AgentService.HybridRegistration.</br>PowerShell.Certificates.CertificateCreationException:</br>자체 서명된 인증서를 만들지 못했습니다. ---></br>System.UnauthorizedAccessException: 액세스가 거부되었습니다. | 자체 서명된 인증서 생성 오류 | 시스템 계정에</br>다음 폴더에 대한 읽기 권한이 있는지 확인합니다.</br>**C:\ProgramData\Microsoft\**</br>** Crypto\RSA**|

## <a name="next-steps"></a>다음 단계

Windows VM에 대한 업데이트를 관리하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Windows VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)

* [Log Analytics](../log-analytics/log-analytics-log-searches.md)의 로그 검색을 사용하여 자세한 업데이트 데이터 보기
* 중요 업데이트가 컴퓨터에서 누락된 것으로 검색되거나 컴퓨터가 자동 업데이트를 사용하지 않도록 설정한 경우 [경고 만들기](../log-analytics/log-analytics-alerts.md)
