---
title: "Azure Operations Management Suite(OMS) 설명서 - 자습서 | Microsoft Docs"
description: "OMS(Microsoft Operations Management Suite)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. 이 문서는 OMS에 포함된 다양한 서비스를 식별하고 자세한 내용에 대한 링크를 제공합니다."
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>OMS(Operations Management Suite)란?
OMS(Microsoft Operations Management Suite)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.  OMS는 클라우드 기반 서비스로 구현되므로 인프라 서비스에 대한 최소한의 투자로 빠르게 실행할 수 있습니다.  새로운 기능이 자동으로 제공되므로 지속적 유지 관리 및 업그레이드 비용을 절감할 수 있습니다.

OMS는 자체적으로 중요 서비스를 제공하는 것 이외에 System Center Operations Manger와 같은 System Center 구성 요소와 통합하여 기존 관리 투자를 클라우드로 확장할 수 있습니다.  System Center 및 OMS는 함께 작동하여 완전한 하이브리드 관리 환경을 제공할 수 있습니다.

다음 섹션은 OMS의 다양한 가치 영역 및 이러한 가치를 구현하는 서비스에 대해 개략적으로 설명합니다.  각각에 대한 자세한 설명을 읽기 전에 OMS 아키텍처에서 각각의 OMS 구성 요소에 대한 개요를 참조할 수 있습니다.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight and Analytics](media/operations-management-suite-overview/icon-insight-analytics.png) Insight and Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)을 통해 운영 체제 및 응용 프로그램에서 생성하는 로그 및 성능 데이터를 수집하고 상관 관계를 분석하고 검색하며 조치를 취할 수 있습니다. 통합 검색 및 사용자 지정 대시보드를 사용하여 실제 위치에 상관없이 모든 워크로드 및 서버에서 수백만 개의 레코드를 쉽게 분석할 수 있는 실시간 운영 정보를 제공합니다.

수집할 데이터 및 분석 논리를 정의하는 Log Analytics에 솔루션을 쉽게 추가할 수 있습니다.  솔루션에는 최소한의 구성 또는 구성 없이 에이전트에 자동으로 배달하는 추가 기능이 포함될 수 있습니다.  시스템과 응용 프로그램 간 데이터의 상관 관계를 분석하기 위해 개별 솔루션에서 제공하는 분석 도구를 사용하는 것 이외에 전체 데이터 집합에 대한 사용자 지정 검색을 실행할 수 있습니다.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automation and Control](media/operations-management-suite-overview/icon-automation-control.png) Automation and Control
Azure Automation은 PowerShell을 기반으로 하며 Azure 클라우드에서 실행되는 [Runbooks](../automation/automation-runbook-types.md)을 사용하여 관리 프로세스를 자동화합니다.  Runbook은 AWS(Amazon Web Services)와 같은 다른 클라우드의 리소스를 포함하여 PowerShell로 관리할 수 있는 제품 또는 서비스에 액세스할 수 있습니다.  또한 Runbook을 로컬 데이터 센터의 서버에서 실행하여 로컬 리소스를 관리할 수 있습니다.

Azure Automation은 [PowerShell DSC](../automation/automation-dsc-overview.md)를 사용하여 구성 관리를 제공합니다.  Azure에서 호스팅되는 DSC 리소스를 만들고 관리하며 클라우드 및 온-프레미스 시스템에 적용하여 구성을 정의하고 자동으로 적용할 수 있습니다.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![보호 및 복구](media/operations-management-suite-overview/icon-protection-recovery.png) 보호 및 재해 복구
[Azure Backup](http://azure.microsoft.com/documentation/services/backup)은 자본 투자 없이 최소한의 운영 비용으로 응용 프로그램 데이터를 보호하고 수년 간 보존하며  SQL Server, SharePoint와 같은 응용 프로그램 워크로드 이외에 물리적 및 가상 Windows Server의 데이터를 백업할 수 있습니다.  또한 이중화 및 장기 보관을 위해 System Center DPM(Data Protection Manager)에서 Azure 백업을 사용하여 보호 데이터를 Azure로 복제할 수 있습니다.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 는 온-프레미스 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터, 물리적 Windows/Linux 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터를 보조 데이터 센터로 복제하거나 데이터 센터를 Azure로 복제하여 확장할 수 있습니다. Site Recovery는 또한 워크로드에 대해 간단한 장애 조치(Failover) 및 복구를 제공합니다. SQL Server AlwaysOn과 같은 재해 복구 메커니즘과 통합되며 여러 컴퓨터에서 계층화되는 워크로드의 간단한 장애 조치(Faiover)에 대한 복구 계획을 제공합니다.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS 보안 및 규정 준수](media/operations-management-suite-overview/icon-security-compliance.png) 보안 및 규정 준수
보안 및 규정 준수는 인프라의 보안 위험을 식별, 평가, 완화합니다.  이러한 OMS 기능은 Log Analytics에서 에이전트 시스템의 로그 데이터 및 구성을 분석하는 여러 솔루션을 통해 구현되어 환경의 지속적 보안을 보장합니다.

* [보안 및 감사 솔루션](oms-security-getting-started.md)은 관리되는 시스템에서 보안 이벤트를 수집 및 분석하여 의심스러운 활동을 식별합니다.
* [맬웨어 방지 솔루션](../log-analytics/log-analytics-malware.md)은 관리되는 시스템에서 맬웨어 방지 보호 상태에 대해 보고합니다.  
* 시스템 업데이트 솔루션은 패칭이 필요한 시스템을 쉽게 식별할 수 있도록 관리되는 시스템에서 보안 업데이트 및 기타 업데이트에 대한 분석을 수행합니다.

## <a name="next-steps"></a>다음 단계
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)에 대해 알아보기
* [Azure Automation](../automation/automation-intro.md)에 대해 알아보기
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)에 대해 알아보기
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)에 대해 알아보기

