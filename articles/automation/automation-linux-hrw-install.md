---
title: Azure Automation Linux Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Linux 기반 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation Hybrid Runbook Worker를 설치하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aca68b6e8d0e6b80a1504b16b9b3462f20fdc6c4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195661"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker를 배포하는 방법

Azure Automation의 Hybrid Runbook Worker 기능을 사용하면 컴퓨터에 대해 직접 그리고 외부에서 환경의 리소스에 대해 Runbook을 실행하여 로컬 리소스를 관리할 수 있습니다. Linux Hybrid Runbook Worker는 Runbook을 승격이 필요한 명령을 실행하도록 승격할 수 있는 특수 사용자로 실행합니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 지정된 컴퓨터에 전달됩니다. 이 문서에서는 Linux 컴퓨터에 Hybrid Runbook Worker를 설치하는 방법에 대해 설명합니다.

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

다음은 지원되는 Linux 배포판의 목록입니다.

* Amazon Linux 2012.09 --> 2015.09(x86/x64)
* CentOS Linux 5, 6 및 7(x86/x64)
* Oracle Linux 5, 6 및 7(x86/x64)
* Red Hat Enterprise Linux Server 5,6,7(x86/x64)
* Debian GNU/Linux 6, 7, 8(x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS(x86/x64)
* SUSE Linux Enterprise Server 11 및 12(x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 설치

Linux 컴퓨터에서 Hybrid Runbook Worker를 설치 및 구성하려는 경우 간단한 프로세스에 따라 역할을 수동으로 설치하고 구성하면 됩니다. Log Analytics 작업 영역에서 **Automation Hybrid Worker** 솔루션을 활성화한 다음, 일련의 명령을 실행하여 컴퓨터를 작업자로 등록하고 새 또는 기존 그룹에 추가해야 합니다.

Linux Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* 둘 이상의 코어
* 4GB 이상의 RAM
* 443 포트(아웃바운드)

### <a name="package-requirements"></a>패키지 요구 사항

| **필수 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 라이브러리| 2.5-12 |
|Openssl| OpenSSL 라이브러리 | 0.9.8e 또는 1.0|
|Curl | cURL 웹 클라이언트 | 7.15.5|
|Python-ctypes | |
|PAM | 플러그형 인증 모듈|

계속 진행하기 전에 자동화 계정이 연결되어 있는 Log Analytics 작업 영역을 확인해야 하며 자동화 계정의 기본 키도 확인해야 합니다. 작업 영역과 기본 키는 모두 Portal에서 찾을 수 있습니다. 자동화 계정을 선택하고 **작업 영역**을 선택하면 작업 영역 ID를, **키**를 선택하면 기본 키를 확인할 수 있습니다. Hybrid Runbook Worker에 필요한 포트 및 주소 정보는 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

1. Azure에서 "Automation Hybrid Worker" 솔루션을 사용하도록 설정합니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

   1. [작업 영역에 Log Analytics 관리 솔루션 추가](../log-analytics/log-analytics-add-solutions.md)의 프로시저를 사용하여 구독에 **Automation Hybrid Worker** 솔루션을 추가합니다.
   1. 다음 cmdlet을 실행합니다.

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 다음 명령을 실행하여 Linux용 OMS 에이전트를 설치하고, \<WorkspaceID\> 및 \<WorkspaceKey\>를 해당 작업 영역의 적절한 값으로 바꿉니다.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. *-w*, *-k*, *-g*, 및 *-e* 매개 변수의 값을 변경하여 다음 명령을 실행합니다. *-g* 매개 변수의 경우 해당 값을 새 Linux Hybrid Runbook Worker가 조인해야 하는 Hybrid Runbook Worker 그룹의 이름으로 바꿉니다. 해당 이름이 Automation 계정에 아직 없으면 해당 이름의 Hybrid Runbook Worker 그룹이 새로 만들어집니다.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 명령이 완료되면 Azure Portal의 Hybrid Worker 그룹 페이지에 새 그룹 및 멤버 수가 표시되고, 기존 그룹이 있으면 해당 멤버 수가 증가됩니다. **Hybrid Worker 그룹** 페이지의 목록에서 그룹을 선택하고 **Hybrid Worker** 타일을 선택합니다. **Hybrid Worker** 페이지에서 나열된 그룹의 각 멤버를 확인합니다.

## <a name="turning-off-signature-validation"></a>서명 유효성 검사 끄기

기본적으로 Linux Hybrid Runbook Worker는 서명 유효성 검사를 요구합니다. 작업자에 대해 서명되지 않은 Runbook을 실행하는 경우 "서명 유효성 검사에 실패"를 나타내는 오류가 표시됩니다. 서명 유효성 검사를 끄려면 두 번째 매개 변수를 Log Analytics 작업 영역 ID로 바꾸어 다음 명령을 실행합니다.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>지원되는 Runbook 유형

Linux Hybrid Runbook Worker는 Azure Automation 내에서 발견되는 Runbook 유형의 전체 집합을 지원 하지 않습니다.

다음의 Runbook 유형은 Linux Hybrid Worker에서 작동합니다.

* Python 2
* PowerShell

다음의 Runbook 유형은 Linux Hybrid Worker에서 작동하지 않습니다.

* PowerShell 워크플로
* 그래픽
* 그래픽 PowerShell 워크플로

## <a name="troubleshooting"></a>문제 해결

Linux Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 Linux용 OMS 에이전트에 따라 달라집니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="the-oms-agent-for-linux-is-not-running"></a>Linux용 OMS 에이전트가 실행되고 있지 않습니다.

Linux용 OMS 에이전트가 실행되고 있지 않으면 Linux Hybrid Runbook Worker가 Azure Automation과 통신할 수 없습니다. `ps -ef | grep python` 명령을 입력하여 에이전트가 실행 중인지 확인하세요. 다음과 비슷한 출력, 즉 **nxautomation** 사용자 계정을 사용하는 python 프로세스가 표시됩니다. 업데이트 관리 또는 Azure Automation 솔루션을 사용하도록 설정하지 않은 경우 다음 프로세스 중 어떤 것도 실행되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 모두 `/var/opt/microsoft/omsagent/state/automationworker/` 디렉터리에 있습니다.

* **oms.conf** - 작업자 관리자 프로세스로, DSC에서 바로 시작됩니다.

* **worker.conf** - 이 프로세스는 자동 등록 하이브리드 작업자 프로세스로, 작업자 관리자가 시작합니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 컴퓨터에서 업데이트 관리 솔루션을 사용하도록 설정하지 않으면 이 프로세스가 없습니다.

* **diy/worker.conf** - 이 프로세스는 DIY 하이브리드 작업자 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 다른 구성을 사용한다는 점 외에는 자동 등록 하이브리드 작업자 프로세스와 차이가 없습니다. Azure Automation 솔루션을 사용하도록 설정하지 않고 DIY Linux Hybrid Worker가 등록되지 않으면 이 프로세스가 없습니다.

Linux용 OMS 에이전트가 실행되고 있지 않으면 `sudo /opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 서비스를 시작합니다.

### <a name="the-specified-class-does-not-exist"></a>지정된 클래스가 없음

**지정된 클래스가 없습니다** 오류가 `/var/opt/microsoft/omsconfig/omsconfig.log`에서 보이는 경우 Linux용 OMS 에이전트를 업데이트해야 합니다. OMS 에이전트를 다시 설치하려면 다음 명령을 실행합니다.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

업데이트 관리 관련 문제를 해결하는 방법에 대한 추가 단계는 [업데이트 관리 - 문제 해결](automation-update-management.md#troubleshooting)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.
* Hybrid Runbook Worker를 제거하는 방법의 지침은 [Azure Automation Hybrid Runbook Worker 제거](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)를 참조하세요.
