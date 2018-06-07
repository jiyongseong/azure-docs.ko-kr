---
title: Azure Log Analytics를 사용하여 환경에서 데이터 수집 | Microsoft Docs
description: 이 항목에서는 Log Analytics를 사용하여 데이터를 수집하고 온-프레미스 또는 다른 클라우드 환경에 호스트된 컴퓨터를 모니터링하는 방법을 이해할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: magoedte
ms.openlocfilehash: 2597b434bc6db0d5639709a9ce869462c3e47f56
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Log Analytics를 사용하여 환경의 컴퓨터에서 데이터 수집

Azure Log Analytics는 다음 환경에 있는 Windows 또는 Linux 컴퓨터에서 데이터를 수집하고 그에 따른 조치를 취할 수 있습니다.

* Log Analytics VM 확장을 사용한 [Azure Virtual Machines](log-analytics-quick-collect-azurevm.md) 
* 실제 서버 또는 가상 머신으로 사용되는 데이터 센터
* AWS(Amazon Web Services) 같은 클라우드에 호스트된 서비스의 가상 머신

사용자 환경에 호스트된 컴퓨터는 Log Analytics에 직접 연결할 수도 있고, System Center Operations Manager 2012 R2, 2016 또는 버전 1801에서 해당 컴퓨터를 이미 모니터링하고 있는 경우에는 Operations Management 관리 그룹을 Log Analytics와 통합하고 IT 서비스 작업 프로세스를 계속 유지 관리할 수도 있습니다.  

## <a name="overview"></a>개요

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

수집된 데이터를 분석하고 그에 대한 조치를 취하기 전에 먼저 Log Analytics 서비스에 데이터를 보내려는 모든 컴퓨터에 대한 에이전트를 설치 및 연결해야 합니다. Azure Automation에서 설치 프로그램, 명령줄 또는 DSC(필요한 상태 구성)를 사용하여 온-프레미스 컴퓨터에 에이전트를 설치할 수 있습니다. 

Linux 및 Windows용 에이전트는 TCP 포트 443을 통해 Log Analytics 서비스와 아웃바운드 통신을 수행하고, 컴퓨터가 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우 [필수 조건 섹션](#prerequisites)을 검토하여 필요한 네트워크 구성을 이해하세요.  IT 보안 정책에는 네트워크의 컴퓨터가 인터넷에 연결할 수 있도록 허용하지 않을 경우 [OMS 게이트웨이](log-analytics-oms-gateway.md)를 설정한 후 에이전트가 게이트웨이를 통해 Log Analytics에 연결하도록 구성할 수 있습니다. 그러면 에이전트는 구성 정보를 수신하고, 사용하도록 설정한 데이터 수집 규칙 및 솔루션에 따라 수집된 데이터를 보낼 수 있습니다. 

System Center 2016 - Operations Manager 또는 Operations Manager 2012 R2를 사용하여 컴퓨터를 모니터링하는 경우 Log Analytics 서비스와 멀티홈으로 구성되어 데이터를 수집하고 서비스로 전달할 수 있고 [Operations Manager](log-analytics-om-agents.md)에서 계속 모니터링될 수도 있습니다. Log Analytics와 통합된 Operations Manager 관리 그룹에서 모니터링되는 Linux 컴퓨터는 데이터 원본에 대한 구성을 수신하거나 관리 그룹을 통해 수집된 데이터를 전달하지 않습니다. Windows 에이전트는 최대 4개의 작업 영역을 보고할 수 있는 반면 Linux 에이전트는 단일 작업 영역에 대한 보고만 지원합니다.  

Linux 및 Windows용 에이전트는 Log Analytics에 연결할 뿐만 아니라, 변경 내용 추적 및 업데이트 관리와 같은 Hybrid Runbook Worker 역할 및 관리 솔루션을 호스트하도록 Azure Automation도 지원합니다.  Hybrid Runbook Worker 역할에 대한 자세한 내용은 [Azure Automation Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 세부 정보를 검토하여 최소 시스템 요구 사항을 충족하는지 확인합니다.

### <a name="windows-operating-system"></a>Windows 운영 체제
Windows 에이전트에 대해 다음 버전의 Windows 운영 체제가 공식적으로 지원됩니다.

* Windows Server 2008 SP1(서비스 팩 1) 이상
* Windows 7 SP1 이상.

> [!NOTE]
> Windows용 에이전트는 TLS(전송 계층 보안) 1.0 및 1.1만 지원합니다.  

#### <a name="network-configuration"></a>네트워크 구성
아래 정보는 Windows 에이전트가 Log Analytics와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다. 트래픽은 네트워크에서 Log Analytics 서비스로 아웃바운드됩니다. 

| 에이전트 리소스 | 포트 | HTTPS 검사 무시|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | 예 |
|*.oms.opinsights.azure.com | 443 | 예 | 
|\*.blob.core.windows.net | 443 | 예 | 
|*.azure-automation.net | 443 | 예 | 

### <a name="linux-operating-systems"></a>Linux 운영 체제
다음 Linux 배포판이 공식적으로 지원됩니다.  하지만 Linux 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다.  다른 설명이 없는 한, 나열된 각 주 버전의 모든 부 버전이 지원됩니다.  

* Amazon Linux 2012.09 ~ 2015.09(x86/x64)
* CentOS Linux 5, 6 및 7(x86/x64)  
* Oracle Linux 5, 6 및 7(x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 및 7(x86/x64)
* Debian GNU/Linux 6, 7, 8(x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS(x86/x64)
* SUSE Linux Enterprise Server 11 및 12(x86/x64)

#### <a name="network-configuration"></a>네트워크 구성
아래 정보는 Linux 에이전트가 Log Analytics와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다.  

|에이전트 리소스| 포트 | 방향 |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | 포트 443 | 인바운드 및 아웃바운드|  
|*.oms.opinsights.azure.com | 포트 443 | 인바운드 및 아웃바운드|  
|\*.blob.core.windows.net | 포트 443 | 인바운드 및 아웃바운드|  
|* .azure-automation.net | 포트 443 | 인바운드 및 아웃바운드|  

Linux 에이전트는 HTTPS 프로토콜을 사용하여 프록시 서버 또는 OMS 게이트웨이를 통해 Log Analytics 서비스와 통신하도록 지원합니다.  익명 및 기본 인증(사용자 이름/암호)이 둘 다 지원됩니다.  설치 중에 또는 설치 후에 proxy.conf 구성 파일을 수정하여 프록시 서버를 지정할 수 있습니다.  

프록시 구성 값은 다음 구문을 갖습니다.

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 프록시 서버에 인증할 필요가 없는 경우에도 Linux 에이전트는 의사 사용자/암호를 제공해야 합니다. 이는 사용자 이름 또는 암호일 수 있습니다.

|자산| 설명 |
|--------|-------------|
|프로토콜 | https |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|암호 | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버/OMS 게이트웨이의 주소 또는 FQDN |
|포트 | 프록시 서버/OMS 게이트웨이 대한 선택적 포트 번호 |

예: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 암호에 “@”과 같은 특수 문자를 사용하는 경우 값이 잘못 구문 분석되므로 프록시 연결 오류가 발생합니다.  이 문제를 해결하려면 [URLDecode](https://www.urldecoder.org/)와 같은 도구를 사용하여 URL에서 암호를 인코드합니다.  

## <a name="install-and-configure-agent"></a>에이전트 설치 및 구성 
요구 사항에 따라 다른 방법을 사용하여 Log Analytics와 온-프레미스 컴퓨터를 직접 연결할 수 있습니다. 다음 표는 조직에서 어떤 방법이 가장 적합한지 결정하기 위해 각 방법을 설명합니다.

|원본 | 방법 | 설명|
|-------|-------------|-------------|
| Windows 컴퓨터|- [수동 설치](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Azure Stack을 사용한 Resource Manager 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) 또는 Azure Resource Manager 템플릿(데이터 센터에 Microsoft Azure Stack을 배포한 경우)과 같은 자동화된 방법을 사용하거나 명령줄에서 Microsoft Monitoring Agent를 설치합니다.| 
|Linux 컴퓨터| [수동 설치](log-analytics-quick-collect-linux-computer.md)|GitHub에 호스트된 래퍼 스크립트를 호출하는 Linux용 에이전트를 설치합니다. | 
| System Center Operations Manager|[Operations Manager를 Log Analytics와 통합](log-analytics-om-agents.md) | Linux 및 Windows 컴퓨터 보고에서 수집된 데이터를 관리 그룹에 전달하도록 Operations Manager와 Log Analytics 간에 통합을 구성합니다.|  

## <a name="next-steps"></a>다음 단계

* [데이터 원본](log-analytics-data-sources.md)을 검토하여 Windows 또는 Linux 시스템에서 데이터를 수집할 수 있는 데이터 원본을 이해하세요. 

* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md)에 대해 알아봅니다. 

* Log Analytics에 기능을 추가하고 OMS 리포지토리로 데이터를 수집하는 [솔루션](log-analytics-add-solutions.md) 에 대해 알아봅니다.