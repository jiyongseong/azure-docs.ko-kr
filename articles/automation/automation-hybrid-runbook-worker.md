---
title: Azure Automation Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation의 기능인 Hybrid Runbook Worker를 설치하고 사용하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194624"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화

Azure Automation의 Runbook은 Azure 클라우드에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용하면 컴퓨터에 대해 직접 그리고 외부에서 환경의 리소스에 대해 Runbook을 실행하여 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 지정된 컴퓨터에 전달됩니다.

이 기능은 다음 이미지에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker 그룹

각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다.

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 특정 작업자를 지정할 수 없습니다.

## <a name="installing-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치하는 프로세스는 OS에 따라 다릅니다. 다음 표에는 Hybrid Runbook Worker를 설치하는 데 사용할 수 있는 여러 가지 방법에 대한 링크가 나와 있습니다. Windows Hybrid Runbook Worker를 설치 및 구성하려면 2가지 방법을 사용할 수 있습니다. 권장되는 방법은 Automation Runbook을 사용하여 Windows 컴퓨터를 구성하는 데 필요한 프로세스를 완전히 자동화하는 것입니다. 두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치하고 구성하는 것입니다. Linux 컴퓨터의 경우 Python 스크립트를 실행하여 컴퓨터에 에이전트를 설치합니다.

|OS  |배포 유형  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> DSC(필요한 상태 구성)로 Hybrid Runbook Worker 역할을 지원하는 서버의 구성을 관리하려면 이들을 DSC 노드로 추가해야 합니다. DSC를 통한 관리를 위한 온보드에 대한 정보는 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.
>
>[업데이트 관리 솔루션](automation-update-management.md)을 사용하도록 설정하면 Log Analytics 작업 영역에 연결된 모든 컴퓨터가 이 솔루션에 포함된 Runbook을 지원하는 Hybrid Runbook Worker로 자동으로 구성됩니다. 그러나 Automation 계정에서 이미 정의한 모든 Hybrid Worker 그룹에 등록되지 않았습니다. 솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

Hybrid Runbook Worker 배포를 시작하기 전에 [네트워크 계획 정보](#network-planning)를 검토합니다. Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker 제거

그룹에서 하나 이상의 Hybrid Runbook Worker를 제거하거나 요구 사항에 따라 그룹을 제거할 수 있습니다. 온-프레미스 컴퓨터에서 Hybrid Runbook Worker를 제거하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.
2. **설정** 블레이드에서 **키**를 선택하고 **URL** 및 **기본 액세스 키** 필드의 값을 확인합니다. 이 정보는 다음 단계에서 필요합니다.

### <a name="windows"></a>Windows

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행합니다. 제거 과정에 대한 자세한 로그를 보려면 **-Verbose** 스위치를 사용합니다.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Hybrid Worker 그룹에서 부실한 컴퓨터를 제거하려면 선택적 `machineName` 매개 변수를 사용합니다.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 컴퓨터의 Microsoft Monitoring Agent가 아닌 Hybrid Runbook Worker 역할의 기능 및 구성만을 제거합니다.

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker 그룹 제거

그룹을 제거하려면 먼저 앞서 보았던 절차를 사용하여 그룹의 구성원인 모든 컴퓨터에서 Hybrid Runbook Worker를 제거한 후 다음 단계를 수행하여 그룹을 제거해야 합니다.

1. Azure Portal에서 Automation 계정을 엽니다.
1. **프로세스 자동화**에서 **Hybrid Worker 그룹**을 선택합니다. 삭제하려는 그룹을 선택합니다. 특정 그룹이 선택되면 **Hybrid Worker 그룹** 속성 페이지가 표시됩니다.

   ![Hybrid Runbook Worker 그룹 페이지](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 선택한 그룹에 대한 속성 페이지에서 **삭제**를 클릭합니다. 이 작업을 확인하라는 메시지가 표시되고 계속하려면 **예**를 선택합니다.

   ![그룹 삭제 확인 대화 상자](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   이 프로세스를 완료하려면 몇 초 정도가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

## <a name="network-planning"></a>네트워크 구성

### <a name="hybrid-worker-role"></a>Hybrid Worker 역할

Hybrid Runbook Worker에서 Log Analytics에 연결하고 등록하려면 이 섹션에서 설명하는 포트 번호 및 URL에 대한 액세스 권한이 있어야 합니다. 이는 [Microsoft Monitoring Agent에서 Log Analytics에 연결하는 데 필요한 포트 및 URL](../log-analytics/log-analytics-agent-windows.md)에 추가됩니다.

에이전트와 Log Analytics 서비스 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. 방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다.

Hybrid Runbook Worker 역할에서 Automation 역할과 통신하려면 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 미국 정부 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 계산, SQL 및 저장소 범위가 포함되어 있습니다.
>
>업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.
>
> 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하는 데 이 파일을 사용하고 있음에 유의해야 합니다.

### <a name="update-management"></a>업데이트 관리

Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도, 업데이트 관리를 위해 특별히 다음 주소가 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 수행됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>문제 해결

Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 에이전트를 사용합니다. 이 에이전트는 Windows의 경우 Microsoft Monitoring Agent이며, Linux의 경우 Linux용 OMS 에이전트입니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.

*.azure-automation.net에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>하이브리드 작업자가 실행되는 컴퓨터에서 최소 하드웨어 요구 사항이 충족되지 않습니다.

Hybrid Runbook Worker가 실행되는 컴퓨터는 이 기능을 호스트하도록 지정하기 전에, 최소 하드웨어 요구 사항을 충족해야 합니다. 그렇지 않으면, 실행 중 Runbook에 의해 유발되는 다른 백그라운드 프로세스 및 경합의 리소스 사용률에 따라, 컴퓨터가 과도하게 사용되거나 Runbook 작업이 지연되거나 시간이 초과되는 원인이 될 수 있습니다.

Hybrid Runbook Worker 기능을 실행하도록 지정된 컴퓨터가 최소 하드웨어 요구 사항을 충족하는지 확인합니다. 그렇다면, CPU 및 메모리 사용률을 모니터링하여 Hybrid Runbook Worker 프로세스와 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 메모리 또는 CPU가 과도하게 사용된다면, 리소스 디스크 병목 현상에 대처하고 오류를 해결하기 위해 메모리를 증가시키거나 프로세서를 업그레이드 또는 추가해야 한다는 것을 나타냅니다. 아니면, 최소 요구 사항을 지원할 수 있는 다른 계산 리소스를 선택하고 필요한 워크로드에 증가가 필요하다는 것이 나타나면 규모를 확장합니다.

특정 OS의 문제 해결에 대한 추가적인 내용은 [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#troubleshooting) 또는 [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#troubleshooting)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.
