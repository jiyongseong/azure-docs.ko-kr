---
title: "OMS(Operations Management Suite) 아키텍처 | Microsoft Docs"
description: "OMS(Microsoft Operations Management Suite)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.  이 문서는 OMS에 포함된 다양한 서비스를 식별하고 자세한 내용에 대한 링크를 제공합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: fb4e9150c1069d48399fb217f865b294ccd317dc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="oms-architecture"></a>OMS 아키텍처
[OMS(Operations Management Suite)](https://azure.microsoft.com/documentation/services/operations-management-suite/)는 온-프레미스 및 클라우드 환경을 관리하기 위한 클라우드 기반 서비스의 모음입니다.  이 문서는 OMS의 다양한 온-프레미스 및 클라우드 구성 요소와 개략적인 클라우드 컴퓨팅 아키텍처에 대해 설명합니다.  자세한 내용은 각 서비스의 설명서를 참조하세요.

## <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)에서 수집한 모든 데이터는 Azure에서 호스팅되는 OMS 리포지토리에 저장됩니다.  연결된 원본은 OMS 리포지토리로 수집되는 데이터를 생성합니다.  현재는 세 가지 유형의 연결된 원본이 지원됩니다.

* OMS에 직접 연결된 [Windows](../log-analytics/log-analytics-windows-agent.md) 또는 [Linux](../log-analytics/log-analytics-linux-agents.md) 컴퓨터에 설치된 에이전트.
* [Log Analytics에 연결](../log-analytics/log-analytics-om-agents.md) 된 SCOM(System Center Operations Manager) 관리 그룹.  SCOM 에이전트는 Log Analytics로 이벤트와 성능 데이터를 전달하는 관리 서버와 계속 통신합니다.
* Azure의 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 데이터를 수집하는 [Azure Storage 계정](../log-analytics/log-analytics-azure-storage.md).

데이터 원본은 Log Analytics가 연결된 원본에서 이벤트 로그, 성능 카운터와 같은 데이터를 정의합니다.  솔루션은 OMS에 기능을 추가하며 [OMS 솔루션 갤러리](../log-analytics/log-analytics-add-solutions.md)에서 작업 공간에 쉽게 추가될 수 있습니다.  일부 솔루션은 SCOM 에이전트에서 Log Analytics로 직접 연결해야 하며 다른 솔루션은 추가 에이전트를 설치해야 할 수 있습니다.

Log Analytics에는 OMS 리소스를 관리하고 OMS 솔루션을 구성하며 OMS 리포지토리에서 데이터를 보고 분석하는 데 사용할 수 있는 웹 기반 포털이 있습니다.

![Log Analytics의 개략적 아키텍처](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Azure Automation Runbook](http://azure.microsoft.com/documentation/services/automation) 은 Azure 클라우드에서 실행하며 Azure 또는 기타 클라우드 서비스에 있는 리소스나 공용 인터넷에서 액세스할 수 있는 리소스에 액세스할 수 있습니다.  또한 Runbook이 로컬 리소스에 액세스할 수 있도록 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)를 사용하여 로컬 데이터 센터에 온-프레미스 컴퓨터를 지정할 수 있습니다.

Azure Automation에 저장된 [DSC 구성](../automation/automation-dsc-overview.md)은 Azure 가상 컴퓨터에 직접 적용할 수 있습니다.  기타 물리적 및 가상 컴퓨터는 Azure Automation DSC 풀 서버에서 구성을 요청할 수 있습니다.

Azure Automation에는 모든 작업에 대해 Azure Portal을 실행하는 링크와 통계를 표시하는 OMS 솔루션이 있습니다.

![Azure Automation의 개략적 아키텍처](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) 의 보호된 데이터는 특정 지리적 지역에 있는 백업 저장소에 저장됩니다.  이 데이터는 동일 지역 내에서 복제되며, 저장소 유형에 따라 추가 중복성을 위해 다른 지역으로 복제될 수 있습니다.

Azure Backup에는 세 가지 기본 시나리오가 있습니다.

* Azure Backup 에이전트가 있는 Windows 컴퓨터.  모든 Windows Server 또는 클라이언트의 파일과 폴더를 Azure 백업 자격 증명 모음으로 직접 백업할 수 있습니다.  
* System Center DPM(Data Protection Manager) 또는 Microsoft Azure Backup 서버. DPM 또는 Microsoft Azure Backup Server를 사용하여 SQL, SharePoint 등의 응용 프로그램 워크로드뿐만 아니라 파일과 폴더를 로컬 저장소로 백업한 다음 Azure 백업 자격 증명 모음으로 복제할 수 있습니다.
* Azure 가상 컴퓨터 확장.  Azure Virtual Machines을 Azure 백업 자격 증명 모음으로 백업할 수 있습니다.

Azure Backup에는 모든 작업에 대해 Azure Portal을 실행하는 링크와 통계를 표시하는 OMS 솔루션이 있습니다.

![Azure Backup의 개략적 아키텍처](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 는 가상 컴퓨터 및 물리적 서버의 복제, 장애 조치(failover), 장애 복구(failback)를 처리합니다. 복제 데이터는 Hyper-V 호스트, VMware 하이퍼바이저 또는 기본 및 보조 데이터 센터의 물리적 서버 간에 교환되거나 데이터 센터와 Azure 저장소 간에 교환됩니다.  Site Recovery는 특정 지역의 Azure 지역에 위치한 저장소에 메타데이터를 저장합니다. Site Recovery 서비스는 복제된 데이터를 저장하지 않습니다.

Azure Site Recovery에는 세 가지 기본 복제 시나리오가 있습니다.

**Hyper-V 가상 컴퓨터 복제**

* Hyper-V 가상 컴퓨터를 VMM 클라우드에서 관리할 경우 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다.  Azure로 복제할 경우 안전한 인터넷 연결이 사용됩니다.  보조 데이터 센터로 복제할 경우는 LAN이 사용됩니다.
* VMM에서 Hyper-V 가상 컴퓨터를 관리하지 않을 경우 Azure 저장소로만 복제할 수 있습니다.  Azure로 복제할 경우 안전한 인터넷 연결이 사용됩니다.

**VMware 가상 컴퓨터 복제**

* VMware 가상 컴퓨터를 VMware를 실행하는 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다.  Azure로 복제할 경우 사이트 간 VPN 또는 Azure ExpressRoute 또는 보안 인터넷 연결을 사용할 수 있습니다. 보조 데이터 센터로 복제할 경우 InMage Scout 데이터 채널이 사용됩니다.

**물리적 Windows 또는 Linux 서버 복제** 

* 물리적 서버를 보조 데이터 센터 또는 Azure 저장소로 복제할 수 있습니다. Azure로 복제할 경우 사이트 간 VPN 또는 Azure ExpressRoute 또는 보안 인터넷 연결을 사용할 수 있습니다. 보조 데이터 센터로 복제할 경우 InMage Scout 데이터 채널이 사용됩니다.  Azure Site Recovery에는 일부 통계를 표시하는 OMS 솔루션이 있지만 모든 작업에 Azure Portal을 사용해야 합니다.

![Azure Site Recovery의 개략적 아키텍처](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>다음 단계
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)에 대해 알아보기
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation)에 대해 알아보기
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)에 대해 알아보기
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)에 대해 알아보기

