---
title: "OMS(Operations Management Suite)와 통합 | Microsoft Docs"
description: "OMS의 표준 기능을 사용할 수 있을 뿐 아니라 다른 관리 응용 프로그램 및 서비스와 통합하여 하이브리드 관리 환경을 제공하고, 사용자 환경에 고유한 사용자 지정 관리 시나리오를 제공하고, 고객에게 사용자 지정 관리 환경을 제공할 수 있습니다.  이 문서에서는 OMS와 통합하는 여러 옵션을 개략적으로 설명하고 구체적인 기술 정보를 제공하는 문서 링크를 안내합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>OMS(Operations Management Suite)와 통합
Operations Management Suite란 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.  OMS의 표준 기능을 사용할 수 있을 뿐 아니라 다른 관리 응용 프로그램 및 서비스와 통합하여 하이브리드 관리 환경을 제공하고, 사용자 환경에 고유한 사용자 지정 관리 시나리오를 제공하고, 고객에게 사용자 지정 관리 환경을 제공할 수 있습니다.  이 문서에서는 OMS 서비스와 통합하는 여러 옵션을 개략적으로 설명하고 구체적인 기술 정보를 제공하는 문서 링크를 안내합니다. 

## <a name="log-analytics"></a>Log Analytics
Log Analytics에서 수집한 관리 데이터는 Azure에 호스팅되는 리포지토리에 저장됩니다.  리포지토리에 저장된 모든 데이터는 대용량의 데이터를 신속하게 분석하는 로그 검색에 사용할 수 있습니다.  통합을 위한 요구 사항은 분석에 사용할 수 있도록 리포지토리를 새 데이터로 채우거나, 리포지토리의 데이터를 내보내서 새 시각화를 제공하거나, 다른 관리 도구와 통합하는 것일 수 있습니다.

리포지토리의 각 데이터는 레코드로 저장됩니다.  리포지토리를 채울 때 솔루션에서 사용하는 레코드 유형 및 해당 속성에 대한 설명을 사용자에게 제공해야 합니다.  데이터를 검색할 때 작업 중인 데이터에 대한 이 정보가 필요합니다.

![OMS 리포지토리 채우기](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Log Analytics 리포지토리 채우기
OMS 리포지토리를 채우는 여러 가지 방법이 있습니다.  사용할 방법은 원본 데이터의 위치, 데이터의 형식, 지원해야 하는 클라이언트 등의 요소에 따라 달라집니다.  데이터가 리포지토리에 저장되면 데이터가 어떻게 수집되었는지는 문제가 되지 않습니다.

다음 섹션에서는 OMS 리포지토리를 채우는 여러 옵션을 설명합니다.

#### <a name="connected-sources-and-data-sources"></a>연결된 원본 및 데이터 원본
연결된 원본은 OMS 리포지토리를 찾아 데이터를 검색할 수 있는 위치입니다.  데이터 원본 및 솔루션은 연결된 원본에서 실행되고 수집되는 특정 데이터를 정의합니다.  응용 프로그램에서 이러한 데이터 원본 중 하나에 데이터를 쓰는 경우 데이터 원본을 구성하여 데이터를 수집할 수 있습니다.  예를 들어 응용 프로그램에서 Syslog 이벤트를 만드는 경우 Linux 에이전트의 Syslog 데이터 원본에서 이러한 이벤트를 수집할 수 있습니다.

* [Log Analytics의 데이터 원본](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>솔루션
솔루션은 OMS의 기능을 확장합니다.  솔루션은 연결된 원본에서 데이터를 수집하거나 이미 리포지토리에 수집된 레코드에서 분석을 수행할 수 있습니다.  Microsoft에서 제공하는 각 솔루션에는 수집하는 데이터에 대한 세부 정보를 제공하는 개별 문서가 들어 있습니다.

* [Log Analytics의 솔루션](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP 데이터 수집기 API
Log Analytics HTTP 데이터 수집기 API는 Log Analytics 리포지토리에 JSON 데이터를 추가할 수 있는 REST API입니다.  다른 데이터 원본 또는 솔루션 중 하나를 통해 데이터를 제공하지 않는 응용 프로그램이 있을 때 이 API를 활용할 수 있습니다.  API를 호출할 수 있으며 데이터 원본 또는 솔루션의 수집 일정에 의존하지 않는 클라이언트의 리포지토리를 채우는 데 사용할 수 있습니다.

* [Log Analytics HTTP 데이터 수집기 API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Log Analytics 리포지토리에서 데이터 검색
OMS 리포지토리에서 데이터를 검색하는 여러 가지 방법이 있습니다.  사용자가 OMS 콘솔을 사용하여 데이터를 검색하고 다양한 시각화 및 분석 방법을 통해 데이터를 제공하게 할 수 있습니다.  또한 다른 관리 솔루션 같은 외부 프로세스에서 데이터를 검색할 수 있습니다.

#### <a name="log-searches"></a>로그 검색
OMS 리포지토리에 저장된 모든 데이터는 로그 검색을 통해 사용할 수 있습니다.  사용자는 OMS 콘솔에서 본인만의 임시 분석을 수행하거나 특정 로그 검색에 대해 시각화를 이용한 대시보드를 만들 수 있습니다.  솔루션은 미리 정의 된 검색 결과에 따라 시각화를 사용한 사용자 지정 보기를 포함할 수 있습니다.  로그 검색 API를 사용하여 외부 응용 프로그램 또는 관리 도구에서 OMS 리포지토리의 데이터에 액세스할 수 있습니다.  

* [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)
* [Log Analytics 로그 검색 REST API](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>사용자 지정 보기
뷰 디자이너를 사용하면 사용자에게 솔루션의 데이터에 대한 시각화 및 분석을 제공하는 사용자 지정 보기를 OMS 콘솔에 만들 수 있습니다.  각 보기에는 콘솔의 기본 페이지에 표시되는 타일 그리고 사용자가 정의하는 로그 검색을 기반으로 하는 시각화 부분이 포함됩니다.

* [Log Analytics 뷰 디자이너](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics는 시각화 및 분석 도구를 활용할 수 있도록 OMS 리포지토리에서 Power BI로 데이터를 자동으로 내보낼 수 있습니다.  일정에 따라 내보내기를 수행하므로 데이터가 최신 상태로 유지됩니다. 

* [Log Analytics 데이터를 Power BI로 내보내기](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>자동화
OMS는 수집된 데이터에 대한 대응 또는 다른 관리 기능을 수행하는 프로세스를 자동화할 수 있습니다.  OMS는 응용 프로그램에서 데이터를 수집하여 OMS 리포지토리에 삽입할 수 있습니다. 또는 사용자는 리포지토리에서 발견한 데이터에 대한 응답으로 알려진 문제 수정을 자동화할 수 있습니다. 

![자동화](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook
Azure Automation의 Runbook은 Azure 클라우드에서 PowerShell 스크립트 및 워크플로를 실행합니다.  Runbook을 사용하여 Azure의 리소스 또는 클라우드에서 액세스할 수 있는 다른 리소스를 관리할 수 있습니다.  Hybrid Runbook Worker를 사용하는 로컬 데이터 센터에서 Runbook을 실행할 수도 있습니다.  Azure Portal에서 또는 PowerShell이나 Automation API 같은 다양한 메서드를 사용하는 외부 프로세스에서 Runbook을 시작할 수 있습니다.

* [Azure Automation에서 Runbook 시작](../automation/automation-starting-a-runbook.md)
* [Azure Automation cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)
* [Automation REST API](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>경고
경고 규칙은 일정에 따라 자동으로 로그 검색을 실행합니다.  결과가 특정 조건과 일치하는 경우 그 결과로 인한 경고가 Azure Automation에서 runbook을 시작하거나 외부 프로세스를 시작할 수 있는 웹후크를 호출할 수 있습니다.  이러한 두 가지 응답에는 로그 검색에서 반환되는 데이터를 비롯한 경고 세부 정보가 포함될 수 있습니다.

* [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md)
* [Log Analytics 경고 API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Backup 및 Site Recovery
Azure Backup 및 Site Recovery는 엔터프라이즈 데이터를 보호하고 서버 및 응용 프로그램의 가용성을 보장하기 위한 서비스를 제공합니다.  사용자는 이러한 서비스를 활용하여 응용 프로그램에 백업 서비스를 제공하거나 가상 컴퓨터의 장애 조치(Failover)를 시작하는 등의 시나리오를 수행할 수 있습니다.

* [Azure Backup cmdlet](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Azure Site Recovery Cmdlet](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>사용자 지정 솔루션
개발자의 작업 영역에서 또는 고객의 작업 영역에서 실행되도록 통합 논리를 사용자 지정 솔루션에 캡슐화할 수 있습니다.  솔루션에는 완전한 관리 시나리오를 제공하는 다른 리소스 외에도 이 문서의 모든 통합 방법이 포함될 수 있습니다.  솔루션의 리소스는 패키징되므로 솔루션이 제거되면 생성된 모든 리소스가 OMS 작업 영역 및 Azure 구독에서 제거됩니다.

예를 들어 HTTP 데이터 수집기 API를 사용하여 데이터를 수집 및 처리한 다음 Log Analytics 리포지토리를 채우는 Automation runbook이 솔루션에 포함될 수 있습니다.  또한 수집된 데이터를 표시하고 분석하는 사용자 지정 보기를 포함할 수 있습니다.  

* 사용자 지정 솔루션(출시 예정) 만들기    

## <a name="next-steps"></a>다음 단계
* OMS 서비스 자동화에 대한 기술 정보는 [OMS SDK](operations-management-suite-sdk.md)를 참조하세요.  

