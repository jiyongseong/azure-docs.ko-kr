---
title: OMS의 에이전트 상태 솔루션 | Microsoft Docs
description: 이 문서는 OMS 또는 System Center Operations Manager에 직접 보고하는 에이전트의 상태를 모니터링하기 위해 이 솔루션을 사용하는 방법을 쉽게 이해할 수 있도록 해줍니다.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: d7eb1550a21e66d4ae4cc4932b30a90956c60d1e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
#  <a name="agent-health-solution-in-oms"></a>OMS의 에이전트 상태 솔루션
OMS의 에이전트 상태 솔루션은 응답이 없고 운영 데이터를 제출하는 OMS 작업 영역에 직접 보고하는 모든 에이전트 또는 OMS에 연결된 System Center Operations Manager 관리 그룹을 이해하는 데 도움이 됩니다.  또한 얼마나 많은 에이전트가 배포되었는지, 이들 에이전트가 지리적으로 어디에 분산되어 있는지 추적할 수 있으며, Azure, 기타 클라우드 환경 또는 온-프레미스에 배포된 에이전트의 분산 상태를 파악하기 위해 다른 쿼리를 수행할 수 있습니다.    

## <a name="prerequisites"></a>필수 조건
이 솔루션을 배포하기 전에 [Windows 에이전트](../log-analytics/log-analytics-windows-agent.md)가 OMS 작업 영역 또는 OMS 작업 영역에 통합된 [Operations Manager 관리 그룹](../log-analytics/log-analytics-om-agents.md)에 보고를 지원하는지 확인합니다.    

## <a name="solution-components"></a>솔루션 구성 요소
이 솔루션은 작업 영역 및 직접 연결된 에이전트 또는 Operations Manager와 연결된 관리 그룹에 추가되는 다음 리소스로 구성됩니다.

### <a name="management-packs"></a>관리 팩
System Center Operations Manager 관리 그룹이 OMS 작업 영역에 연결된 경우 다음 관리 팩이 Operations Manager에 설치됩니다.  이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩과 관련하여 아무 것도 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 상태 평가 직접 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor 상태 평가 서버 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../log-analytics/log-analytics-om-agents.md)을 참조하세요.

## <a name="configuration"></a>구성
[솔루션 추가](../log-analytics/log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 에이전트 상태 솔루션을 OMS 작업 영역에 추가합니다. 추가 구성은 필요 없습니다.


## <a name="data-collection"></a>데이터 수집
### <a name="supported-agents"></a>지원되는 에이전트
다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 | 예 | 하트비트 이벤트는 Windows 에이전트에서 직접 수집됩니다.|
| System Center Operations Manager 관리 그룹 | 예 | 하트비트 이벤트는 60초마다 관리 그룹에 보고하는 에이전트로부터 수집된 다음 로그 분석에 전달됩니다. Operations Manager 에이전트에서 Log Analytics로 직접 연결은 필요하지 않습니다. 하트비트 이벤트 데이터는 관리 그룹에서 Log Analytics 리포지토리로 전달됩니다.|

## <a name="using-the-solution"></a>솔루션 사용
솔루션을 OMS 작업 영역에 추가할 때 OMS 대시보드에 **에이전트 상태** 타일이 추가됩니다. 이 타일은 총 에이전트 수와 지난 24시간 동안 응답하지 않는 에이전트의 수를 표시합니다.<br><br> ![대시보드의 에이전트 상태 솔루션 타일](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

**에이전트 상태**타일을 클릭하여 **에이전트 상태** 대시보드를 엽니다.  대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 이벤트를 나열합니다. 각 열의 오른쪽 아래쪽에 있는 **모두 보기**를 선택하거나 열 제목을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|--------|-------------|
| 시간에 따른 에이전트 수 | Linux 및 Windows 에이전트에 대해 7일 동안의 에이전트 수의 추세입니다.|
| 응답하지 않는 에이전트 개수 | 지난 24시간 동안 하트비트를 보내지 않은 에이전트의 목록입니다.|
| OS 형식별 배포 | 사용자 환경에 있는 Windows 및 Linux 에이전트 개수에 따른 분할입니다.|
| 에이전트 버전별 배포 | 사용자 환경에 설치 된 다른 에이전트 버전의 분할과 그 개수입니다.|
| 에이전트 범주별 배포 | 직접 에이전트, OpsMgr 에이전트 또는 OpsMgr 관리 서버 등 하트비트 이벤트를 전송하는 에이전트의 다양한 범주의 분할입니다.|
| 관리 그룹별 배포 | 사용자 환경에서 서로 다른 SCOM 관리 그룹의 분할입니다.|
| 에이전트의 지리적 위치 | 에이전트가 있는 국가와 각 국가에 설치된 에이전트의 총수에 따른 분할입니다.|
| 설치된 게이트웨이 개수 | OMS 게이트웨이가 설치되어 있는 서버 수와 이들 서버의 목록입니다.|

![에이전트 상태 솔루션 대시보드 타일](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics 레코드
솔루션은 OMS 리포지토리에서 한 레코드 형식을 만듭니다.  

### <a name="heartbeat-records"></a>하트비트 레코드
**하트비트** 형식이 포함된 레코드가 만들어집니다.  이러한 레코드는 다음 표의 속성을 가집니다.  

| 자산 | 설명 |
| --- | --- |
| 유형 | *하트비트*|
| Category | 값은 *직접 에이전트*, *SCOM 에이전트* 또는 *SCOM 관리 서버*합니다.|
| Computer | 컴퓨터 이름입니다.|
| OSType | Windows 또는 Linux 운영 체제입니다.|
| OSMajorVersion | 운영 체제의 주 버전입니다.|
| OSMinorVersion | 운영 체제의 부 버전입니다.|
| 버전 | OMS 에이전트 또는 Operations Manager 에이전트 버전입니다.|
| SCAgentChannel | 값은 *직접* 및/또는 *SCManagementServer*합니다.|
| IsGatewayInstalled | OMS 게이트웨이가 설치된 경우 값은 *true*, 그렇지 않으면 *false*입니다.|
| ComputerIP | 컴퓨터의 IP 주소입니다.|
| RemoteIPCountry | 컴퓨터가 배포된 지리적 위치입니다.|
| ManagementGroupName | Operations Manager 관리 그룹의 이름입니다.|
| SourceComputerId | 컴퓨터의 고유 ID입니다.|
| RemoteIPLongitude | 컴퓨터 지리적 위치의 경도입니다.|
| RemoteIPLatitude | 컴퓨터 지리적 위치의 위도입니다.|

Operations Manager 관리 서버에 보고하는 각 에이전트는 두 개의 하트비트를 보내고, OMS 구독에서 어떤 Log Analytics 데이터 원본 및 솔루션에 활성화했는지에 따라 SCAgentChannel 속성 값에는 **직접** 및 **SCManagementServer**가 모두 포함됩니다. 호출하면 솔루션의 데이터는 Operations Manager 관리 서버에서 OMS 웹 서비스로 직접 전송되거나 에이전트에서 수집된 데이터의 양으로 인해 에이전트에서 OMS 웹 서비스로 직접 전송됩니다. **SCManagementServer** 값이 있는 하트비트 이벤트의 경우, ComputerIP 값은 관리 서버가 데이터를 실제로 업로드하기 때문에 관리 서버의 IP 주소가 됩니다.  SCAgentChannel가 **직접**에 설정된 하트비트의 경우 이 값은 에이전트의 공용 IP 주소입니다.  

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 테이블은 이 솔루션에 의해 수집된 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
|:---|:---|
| Heartbeat &#124; distinct Computer |에이전트의 총수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |지난 24시간 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |지난 15분 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |컴퓨터 온라인(지난 24시간) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |지난 30분 동안 총 에이전트 오프라인(지난 24시간 동안) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |시간에 따른 OSType별 에이전트 수의 추세 가져오기|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS 형식별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |에이전트 버전별 배포 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |에이전트 범주별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 관리 그룹별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |에이전트의 지리적 위치 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |설치된 OMS 게이트웨이 수 |




## <a name="next-steps"></a>다음 단계

* Log Analytics에서 경고 생성에 대한 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md) 에 관하여 알아보세요.
