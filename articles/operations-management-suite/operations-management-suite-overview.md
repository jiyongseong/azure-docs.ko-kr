---
title: "OMS(Operations Management Suite) 개요 | Microsoft Docs"
description: "OMS(Microsoft Operations Management Suite)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.  이 문서에서는 OMS의 가치를 설명하고, OMS에 포함된 다양한 서비스와 제품을 식별하고, 자세한 내용에 대한 링크를 제공합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-operations-management-suite-oms"></a>OMS(Operations Management Suite)란?
이 문서에서는 OMS(Operations Management Suite)가 제공하는 비즈니스 가치, 포함된 서비스 및 관리 솔루션, 여러 서비스 및 솔루션이 패키지된 제품에 대한 간략한 개요를 포함하여 OMS를 소개합니다.  각 서비스 및 솔루션을 배포하고 사용하는 방법에 대한 구체적인 설명 링크가 포함되어 있습니다.

## <a name="from-on-premises-to-the-cloud"></a>온-프레미스에서 클라우드로
Microsoft는 오랫동안 기업 환경을 관리하기 위한 제품을 제공해 왔습니다.  2007년에는 여러 제품이 System Center 관리 제품군에 통합되었습니다.  여기에는 소프트웨어 배포 및 인벤토리 같은 기능을 제공하는 Configuration Manager, 시스템 및 응용 프로그램을 사전 예방적으로 모니터링하는 Operations Manager, 수동 프로세스를 자동화하는 runbook이 포함된 Orchestrator, 중요한 데이터를 백업하고 복구할 수 있는 Data Protection Manager가 포함됩니다.

점점 더 많은 컴퓨팅 리소스가 클라우드로 전환되면서, Azure에서 리소스를 관리하는 Operations Manager나 Orchestrator 같은 더 많은 클라우드 기능이 System Center 제품에 추가되었습니다.  이러한 제품은 여전히 근본적으로 온-프레미스 솔루션으로 설계되었지만, 온-프레미스 관리 환경을 배포하고 유지 관리하는 데 상당한 투자가 필요합니다.  클라우드를 완전하게 활용하고 미래의 응용 프로그램을 지원하려면 새로운 관리 방식이 필요했습니다.

## <a name="introducing-operations-management-suite"></a>Operations Management Suite 소개
OMS(Operations Management Suite)는 처음부터 클라우드에서 설계된 관리 서비스 컬렉션입니다.  OMS 구성 요소는 온-프레미스 리소스를 배포하고 관리하는 대신 Azure에서 전적으로 호스팅됩니다.  구성 작업이 최소화되어 문자 그대로 몇 분 안에 실행할 수 있습니다.  

- **배포에 드는 비용 및 복잡성 최소화.**  OMS의 모든 구성 요소와 데이터는 Azure에 저장되므로 온-프레미스 구성 요소의 복잡성과 투자 없이 짧은 시간 안에 가동하여 실행할 수 있습니다.
- **클라우드 수준으로 확장.**  클라우드를 사용하면 사용자가 실제로 사용한 작업에 대해서만 비용을 지불하고 필요한 모든 로드로 쉽게 확장할 수 있으므로, 필요하지 않은 계산 리소스에 대해 비용을 지불하거나 부족한 저장소 공간에서 실행하는 것에 대해 걱정할 필요가 없습니다.  처음에는 약간의 리소스로 시작하고 이후에 전체 환경을 강화할 수 있습니다.
- **최신 기능 활용.**  OMS 서비스의 기능은 지속적으로 추가되고 업데이트됩니다.  업데이트를 배포할 필요 없이 최신 기능에 지속적으로 액세스할 수 있습니다.
- **통합 서비스.**  각 OMS 서비스는 그 자체로도 상당한 가치를 제공하지만 함께 사용하면 복잡한 관리 시나리오까지 해결할 수 있습니다.  예를 들어 Azure Automation에서 runbook은 Azure Site Recovery를 사용하여 장애 조치(Failover) 프로세스를 실행한 다음 Log Analytics에 정보를 기록하여 경고를 생성할 수 있습니다.
- **글로벌 지식.**  OMS의 관리 솔루션에서 지속적으로 최신 정보에 액세스할 수 있습니다.  예를 들어 보안 및 감사 솔루션에서 전 세계에서 감지되는 최신 위험 요소를 사용하여 위험 분석을 수행할 수 있습니다.
- **어디서나 액세스.**  브라우저만 있다면 어디서나 관리 환경에 액세스할 수 있습니다.  스마트폰에 OMS 앱을 설치하면 모니터링 데이터에 즉시 액세스할 수 있습니다.

### <a name="is-it-just-for-the-cloud"></a>혹시 클라우드 전용일까요?
클라우드에서 OMS 서비스를 실행한다고 해서 온-프레미스 환경을 효과적으로 관리할 수 없다는 것은 아닙니다.  데이터 센터의 Windows 또는 Linux 컴퓨터에 에이전트를 배치하면 에이전트에서 Log Analytics로 데이터를 보내 클라우드 또는 온-프레미스 서비스에서 수집한 다른 모든 데이터와 함께 분석할 수 있습니다.  온-프레미스 리소스의 백업 및 고가용성을 위해 Azure Backup 및 Azure Site Recovery를 통해 클라우드를 활용할 수 있습니다.  
클라우드의 Runbook은 일반적으로 온-프레미스 리소스에 액세스할 수 없지만, 하나 이상의 컴퓨터에 에이전트를 설치하여 데이터 센터에서 Runbook을 호스팅할 수도 있습니다.  runbook을 시작할 때 클라우드에서 실행할 것인지 아니면 로컬 작업자에서 실행할 것인지 지정하기만 하면 됩니다.

## <a name="hybrid-management-with-system-center"></a>System Center를 사용하여 하이브리드 관리
System Center가 이미 설치되어있는 경우 이러한 구성 요소를 OMS 서비스와 통합하여 온-프레미스 환경과 클라우드 환경 모두에 각 제품과 관련된 전문성을 활용하는 하이브리드 솔루션을 제공할 수 있습니다.  기존 Operations Manager 관리 그룹을 Log Analytics에 연결하여 클라우드에서 관리되는 에이전트를 분석할 수 있습니다.  기존 백업 프로세스를 Data Protection Manager와 함께 사용하여 데이터를 클라우드에 백업할 수 있습니다.  


## <a name="oms-services"></a>OMS 서비스
OMS의 핵심 기능은 Azure에서 실행되는 서비스 집합을 통해 제공됩니다.  각 서비스는 고유의 관리 기능을 제공하며, 사용자는 서비스를 결합하여 다양한 관리 시나리오를 해결할 수 있습니다.

|| 부여 | 설명 |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | 실제 컴퓨터와 가상 컴퓨터를 포함하여 다양한 리소스의 가용성 및 성능을 모니터링하고 분석합니다. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automation | 수동 프로세스를 자동화하고 실제 컴퓨터와 가상 컴퓨터에 대한 구성을 적용합니다. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Backup | 중요한 데이터를 백업하고 복원합니다. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | 중요한 응용 프로그램에 고가용성을 제공합니다. |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 OMS에 대한 모니터링 서비스를 제공합니다.  이 데이터에는 이벤트, 성능 데이터 또는 API를 통해 제공되는 사용자 지정 데이터가 포함될 수 있습니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다.  이 방법을 사용하면 다양한 원본의 데이터를 통합할 수 있으므로 Azure 서비스의 데이터를 기존 온-프레미스 환경과 결합할 수 있습니다.  또한 모든 종류의 데이터에 모든 작업을 제공할 수 있도록 데이터 컬렉션을 해당 데이터에서 수행된 작업과 명확하게 구분합니다.  

![Log Analytics 개요](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>데이터 수집
Log Analytics에서 분석할 데이터를 리포지토리로 가져오는 다양한 방법이 있습니다.

- **Windows 또는 Linux 컴퓨터와 가상 컴퓨터.**  데이터를 수집하려는 [Windows](../log-analytics/log-analytics-windows-agent.md) 및 [Linux](../log-analytics/log-analytics-linux-agents.md) 컴퓨터 또는 가상 컴퓨터에 Microsoft 모니터링 에이전트를 설치합니다.  에이전트는 수집할 이벤트 및 성능 데이터를 정의하는 Log Analytics 구성에서 자동으로 다운로드됩니다.  사용자는 Azure Portal을 사용하여 Azure에서 실행 중인 가상 컴퓨터에 에이전트를 쉽게 설치할 수 있습니다.  기존 Operations Manager 환경이 있으면 관리 그룹을 Log Analytics에 연결하여 모든 기존 에이전트에서 자동으로 데이터 수집을 시작할 수 있습니다.
- **Azure 서비스.**  Log Analytics는 사용자가 Azure 리소스를 모니터링할 수 있도록 [Azure Diagnostics 및 Azure Monitoring](../log-analytics/log-analytics-azure-storage.md)에서 리포지토리로 원격 분석 데이터를 수집합니다.
- **데이터 수집기 API.**  Log Analytics에는 [모든 클라이언트의 데이터를 채우는 REST API](../log-analytics/log-analytics-data-collector-api.md)가 있습니다.  따라서 사용자가 타사 응용 프로그램의 데이터를 수집하거나 사용자 지정 관리 시나리오를 구현할 수 있습니다.  일반적인 방법은 Azure Automation의 runbook을 사용하여 데이터를 수집한 후 데이터 수집기 API를 사용하여 리포지토리에 쓰는 것입니다.

#### <a name="reporting-and-analyzing-data"></a>데이터 보고 및 분석
Log Analytics에는 리포지토리에 저장된 데이터를 추출하는 강력한 쿼리 언어가 포함되어 있습니다.  모든 소스의 데이터가 레코드로 저장되기 때문에 사용자가 여러 소스의 데이터를 단일 쿼리로 분석할 수 있습니다.
  
Log Analytics는 임시 분석 외에도 쿼리의 데이터를 보고하고 분석할 수 있는 여러 방법을 제공합니다.

- **보기 및 대시보드.**  [보기](../log-analytics/log-analytics-view-designer.md) 및 [대시보드](../log-analytics/log-analytics-dashboards.md)는 쿼리 결과를 포털에 시각화합니다.  일반적으로 관리 솔루션에는 솔루션의 데이터를 분석하는 보기가 포함되어 있습니다.  데이터를 분석하여 사용자 지정 포털에 즉시 제공하는 사용자 지정 보기를 만들 수도 있습니다.
- **내보내기.**  Log Analytics 외부에서 분석할 수 있도록 쿼리 결과를 내보내는 옵션이 제공됩니다.  뛰어난 시각화 및 분석 기능을 제공하는 [Power BI](../log-analytics/log-analytics-powerbi.md)로 주기적 내보내기를 예약할 수도 있습니다.
- **로그 검색 API.**  Log Analytics에는 [모든 클라이언트의 데이터를 수집하는 REST API](../log-analytics/log-analytics-log-search-api.md)가 있습니다.  따라서 사용자가 리포지토리에 수집된 데이터를 프로그래밍 방식으로 작업하거나 다른 모니터링 도구에서 액세스할 수 있습니다.

#### <a name="alerting"></a>경고
Log Analytics는 문제가 발견되면 사용자에게 [미리 경고](../log-analytics/log-analytics-alerts.md)하거나 수정 작업을 수행할 수 있습니다.  Log Analytics의 다른 분석과 마찬가지로 로그 검색을 통해 수행됩니다.  이 검색은 주기적으로 실행되며, 결과가 특정 조건과 일치하면 경고가 생성됩니다.

![Log Analytics rudrh](media/operations-management-suite-overview/overview-alerts.png)

Log Analytics 리포지토리에 경고 레코드를 만드는 작업 외에도 경고에서 다음 작업을 수행할 수 있습니다.

- **전자 메일.**  발견된 문제를 미리 알리는 전자 메일을 보냅니다.
- **Runbook.**  Log Analytics의 경고는 Azure Automation에서 runbook을 시작할 수 있습니다.  이 동작은 일반적으로 발견된 문제를 해결하기 위해 수행됩니다.  Azure 또는 다른 클라우드에 문제가 발생하는 경우 runbook을 시작하거나 실제 컴퓨터 또는 가상 컴퓨터의 문제를 해결하기 위해 로컬 에이전트에서 runbook을 시작할 수 있습니다.
- **웹후크.**  경고는 웹후크를 시작하고 로그 검색 결과의 데이터를 웹후크에 전달할 수 있습니다.  따라서 대체 경고 시스템 등의 외부 서비스와 통합하거나 외부 웹 사이트에 대한 수정 작업을 시도할 수 있습니다.

### <a name="azure-automation"></a>Azure Automation
[Azure Automation](http://azure.microsoft.com/documentation/services/automation)은 OMS에 프로세스 자동화 및 구성 관리를 제공합니다.  수동 프로세스를 자동화하고 실제 컴퓨터와 가상 컴퓨터에 대한 구성을 적용합니다.  

#### <a name="process-automation"></a>프로세스 Automation
Azure Automation은 PowerShell 스크립트 또는 PowerShell 워크플로 기반의 [runbook](../automation/automation-runbook-types.md)을 사용하여 수동 프로세스를 자동화합니다.  또한 여러 runbook과 자격 증명 간에 공유할 수 있는 변수나 runbook 인증에 필요할 수 있는 암호화된 정보를 저장할 수 있는 연결처럼 runbook을 지원하는 자산도 포함하고 있습니다.
Runbook은 제품군의 다른 서비스에 대한 프로세스 자동화를 제공합니다.  PowerShell 또는 REST API를 통해 각각의 다른 서비스에 액세스할 수 있으므로 사용자는 Log Analytics에서 관리 데이터를 수집하거나 Azure Backup으로 백업을 시작하는 등의 기능을 수행하는 runbook을 만들 수 있습니다.

##### <a name="accessing-resources"></a>리소스 액세스
runbook은 PowerShell을 기반으로 하기 때문에 PowerShell cmdlet을 통해 액세스할 수 있는 모든 리소스를 관리할 수 있습니다.  Automation 계정에 [모듈을 로드](../automation/automation-integration-modules.md)하면 해당 계정의 모든 runbook에서 모듈을 사용할 수 있습니다. 
 
runbook이 클라우드에서 실행되면 runbook은 클라우드에서 액세스할 수 있는 모든 리소스에 액세스할 수 있습니다.  Azure 구독, AWS(Amazon Web Services) 같은 다른 클라우드 또는 REST API를 통해 액세스할 수 있는 서비스의 리소스를 예로 들 수 있습니다.  클라우드의 Runbook은 모든 자격 증명으로 실행되지 않지만, Automation 자산(예: 자격 증명, 연결 및 인증서)을 활용하여 액세스하는 리소스를 인증할 수 있습니다.

아마도 데이터 센터의 리소스에는 클라우드에서 실행되는 runbook이 액세스할 수 없을 것입니다.  하지만 데이터 센터에 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)를 하나 이상 설치하여 로컬 리소스에 액세스해야 하는 runbook을 실행할 수 있습니다.  runbook을 시작할 때 클라우드에서 실행할 것인지 아니면 로컬 작업자에서 실행할 것인지 지정할 수 있습니다.

![Azure Automation runbook](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Runbook 시작
Runbook이 다양한 관리 시나리오에 포함되도록 [다양한 방법을 통해 Runbook을 시작](../automation/automation-starting-a-runbook.md)할 수 있습니다.  

- **Azure Portal.**  다른 Azure 서비스와 마찬가지로 Azure Automation은 Azure Portal에서 관리할 수 있습니다.  runbook을 시작하는 것은 물론이고 runbook을 가져오거나 직접 만들 수 있습니다.
- **예약.**  runbook을 주기적으로 시작하도록 예약할 수 있습니다.  따라서 자동으로 주기적 관리 프로세스를 반복하거나 Log Analytics로 데이터를 수집할 수 있습니다.
- **PowerShell 및 API.**  runbook을 시작하고 PowerShell cmdlet 또는 Azure Automation REST API에서 필요한 매개 변수 정보를 전달할 수 있습니다.  
- **웹후크.**  모든 runbook에 대해 외부 응용 프로그램 또는 웹 사이트에서 runbook을 시작할 수 있는 웹후크를 만들 수 있습니다.
- **Log Analytics 경고.**  Log Analytics의 경고는 경고에서 발견한 문제를 해결하기 위해 자동으로 시도하는 runbook을 시작할 수 있습니다.

#### <a name="configuration-management"></a>구성 관리
[PowerShell DSC(Desired State Configuration)](../automation/automation-dsc-overview.md)는 실제 컴퓨터와 가상 컴퓨터의 구성을 배포하고 적용할 수 있는 Windows PowerShell의 관리 플랫폼입니다.  Azure Automation은 DSC 구성을 관리하며, 에이전트가 필요한 구성을 검색하기 위해 액세스할 수 있는 완전한 서버를 클라우드에 제공합니다.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup 및 Azure Site Recovery
Azure Backup 및 Azure Site Recovery는 비즈니스 연속성 및 재해 복구에 기여합니다.  각 서비스는 작동 중단이 발생하더라도 응용 프로그램 가용성을 유지하고 시스템이 다시 온라인 상태가 되면 정상 운영으로 되돌리는 기능을 제공합니다.  두 서비스 모두 조직에 정의된 RPO(복구 지점 목표) 및 RTO(복구 시간 목표)에 영향을 줍니다. RPO는 작동 중단 시 데이터를 사용할 수 없도록 허용 가능한 제한을 정의하고, RTO는 작동 중단 시 서비스 또는 앱을 사용할 수 없도록 허용 가능한 시간을 제한합니다.

#### <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup)은 OMS에 데이터 백업 및 복원 서비스를 제공합니다.  자본 투자 없이 최소한의 운영 비용으로 응용 프로그램 데이터를 보호하고 수년 간 보관합니다.  SQL Server, SharePoint와 같은 응용 프로그램 워크로드 이외에 물리적 및 가상 Windows Server의 데이터를 백업할 수 있습니다.  또한 이중화 및 장기 보관을 위해 System Center DPM(Data Protection Manager)에서 Azure 백업을 사용하여 보호 데이터를 Azure로 복제할 수 있습니다.

Azure Backup의 보호 데이터는 특정 지리적 지역에 있는 백업 저장소에 저장됩니다. 이 데이터는 동일 지역 내에서 복제되며, 저장소 유형에 따라 복원력을 높이기 위해 다른 지역에 복제될 수 있습니다.

Azure Backup에는 세 가지 기본 시나리오가 있습니다.

- **Azure Backup 에이전트가 있는 Windows 컴퓨터.** 모든 Windows 서버 또는 클라이언트의 파일과 폴더를 Azure Backup 자격 증명 모음에 직접 백업할 수 있습니다.<br><br>![Azure Backup 에이전트가 있는 Windows 컴퓨터](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center DPM(Data Protection Manager) 또는 Microsoft Azure Backup Server.** DPM 또는 Microsoft Azure Backup Server를 활용하여 SQL, SharePoint 등의 응용 프로그램 워크로드뿐 아니라 파일과 폴더를 로컬 저장소에 백업한 다음 Azure 백업 자격 증명 모음으로 복제할 수 있습니다. Hyper-V 또는 VMware에서 Windows 및 Linux 가상 컴퓨터를 지원합니다.<br><br>![System Center DPM(Data Protection Manager) 또는 Microsoft Azure Backup Server.](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure 가상 컴퓨터 확장.** Azure의 Windows 또는 Linux 가상 컴퓨터를 Azure Backup 자격 증명 모음에 백업합니다.<br><br>![Azure 가상 컴퓨터 확장](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)는 온-프레미스 가상 컴퓨터 및 실제 컴퓨터를 Azure 또는 보조 사이트로 복제하는 작업을 오케스트레이션하여 비즈니스 연속성을 제공합니다. 기본 사이트를 사용할 수 없는 경우 사용자가 계속 작업할 수 있도록 보조 위치에 장애 조치(Failover)하고, 시스템이 다시 정상 상태가 되면 장애 복구합니다. 

Azure Site Recovery는 서버 및 응용 프로그램에 고가용성을 제공합니다.  온-프레미스 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터, 물리적 Windows/Linux 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터를 보조 데이터 센터로 복제하거나 데이터 센터를 Azure로 복제하여 확장할 수 있습니다. Site Recovery는 또한 워크로드에 대해 간단한 장애 조치(Failover) 및 복구를 제공합니다. SQL Server AlwaysOn과 같은 재해 복구 메커니즘과 통합되며 여러 컴퓨터에서 계층화되는 워크로드의 간단한 장애 조치(Faiover)에 대한 복구 계획을 제공합니다.

Azure Site Recovery에는 세 가지 기본 복제 시나리오가 있습니다.

- **Hyper-V 가상 컴퓨터 복제.**  Hyper-V 가상 컴퓨터를 VMM 클라우드에서 관리할 경우 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다. Azure로 복제할 경우 안전한 인터넷 연결이 사용됩니다. 보조 데이터 센터로 복제할 경우는 LAN이 사용됩니다.  VMM에서 Hyper-V 가상 컴퓨터를 관리하지 않는 경우 Azure 저장소로만 복제할 수 있습니다. Azure로 복제할 경우 안전한 인터넷 연결이 사용됩니다.<br><br>![Hyper-V 가상 컴퓨터 복제](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **VMware 가상 컴퓨터 복제.**  VMware 가상 컴퓨터를 VMware를 실행하는 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다. Azure로 복제할 경우 사이트 간 VPN 또는 Azure ExpressRoute 또는 보안 인터넷 연결을 사용할 수 있습니다. 보조 데이터 센터로 복제할 경우 InMage Scout 데이터 채널이 사용됩니다.<br><br>![VMware 가상 컴퓨터 복제](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **실제 Windows 또는 Linux 서버 복제.**  물리적 서버를 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다. Azure로 복제할 경우 사이트 간 VPN 또는 Azure ExpressRoute 또는 보안 인터넷 연결을 사용할 수 있습니다. 보조 데이터 센터로 복제할 경우 InMage Scout 데이터 채널이 사용됩니다. Azure Site Recovery에는 일부 통계를 표시하는 OMS 솔루션이 있지만 모든 작업에 Azure Portal을 사용해야 합니다.<br><br>![물리적 Windows 또는 Linux 서버 복제](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery는 특정 지역의 Azure 지역에 위치한 저장소에 메타데이터를 저장합니다. Site Recovery 서비스는 복제된 데이터를 저장하지 않습니다.

## <a name="management-solutions"></a>관리 솔루션
[관리 솔루션](operations-management-suite-solutions.md)은 하나 이상의 OMS 서비스를 활용하여 특정 관리 시나리오를 구현하는 미리 패키지된 논리 집합입니다.  사용자가 간편하게 Azure 구독에 추가하여 OMS 투자 가치를 높일 수 있도록 Microsoft와 파트너의 여러 솔루션이 제공됩니다.  파트너는 본인의 응용 프로그램 및 서비스를 지원하는 자체 솔루션을 만들어서 Azure Marketplace 또는 빠른 시작 템플릿을 통해 사용자에게 제공할 수 있습니다.

여러 서비스를 활용하여 추가 기능을 제공하는 솔루션의 좋은 예로는 [업데이트 관리 솔루션](oms-solution-update-management.md)이 있습니다.  이 솔루션은 Windows 및 Linux용 Log Analytics 에이전트를 사용하여 각 에이전트에 필요한 업데이트에 대한 정보를 수집합니다.  솔루션에서 이 데이터를 Log Analytics 리포지토리에 쓰면 사용자는 포함된 대시보드를 사용하여 데이터를 분석할 수 있습니다.  사용자가 배포를 만들면 Azure Automation의 runbook을 사용하여 필요한 업데이트가 설치됩니다.  이 전체 프로세스를 포털에서 관리할 수 있으며 기본적인 세부 정보에 대해 걱정할 필요가 없습니다.

![해결 방법](media/operations-management-suite-overview/overview-solution.png)

대부분의 솔루션은 다음 기능 중 하나 이상을 수행할 수 있습니다.

- 추가 정보를 수집합니다.  Log Analytics는 클라이언트와 서비스에서 이벤트 및 성능 데이터를 포함한 다양한 데이터를 수집합니다.  관리 솔루션은 종종 Azure Automation runbook을 사용하여 다른 데이터 소스에서 제공되지 않는 추가 정보를 수집할 수 있습니다.
- 수집된 정보를 추가로 분석합니다.  관리 솔루션에는 데이터 분석 및 시각화를 제공하는 대시보드와 보기가 포함되어 있습니다.  관리 솔루션은 세부 데이터를 살펴볼 수 있는 미리 정의된 로그 검색과 다시 연결됩니다.  또한 보안 이벤트에서 위협을 나타내는 패턴을 검색하는 것처럼 이미 리포지토리에 수집된 데이터를 분석할 수도 있습니다.
- 기능 추가.  Microsoft에서 제공하는 일부 솔루션은 코어 서비스의 기능을 기반으로 추가 기능을 제공할 수 있습니다.  예를 들어 서비스 맵은 서버 및 프로세스 종속성을 실시간으로 검색하여 매핑할 수 있는 자체 콘솔을 제공합니다.
Microsoft와 파트너가 OMS에 솔루션을 주기적으로 추가하고 있으므로 고객은 지속적으로 투자 가치를 높일 수 있습니다.  OMS 포털의 솔루션 카탈로그를 통해 또는 Azure Portal의 Azure Marketplace를 통해 Microsoft 및 파트너 솔루션을 검색하여 설치할 수 있습니다.  

![솔루션 갤러리](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>다음 단계
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)에 대해 알아보기
* [Azure Automation](../automation/automation-intro.md)에 대해 알아보기
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)에 대해 알아보기
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)에 대해 알아보기
* 여러 OMS 제품에 [제공되는 솔루션](../log-analytics/log-analytics-add-solutions.md)을 검색합니다. 

