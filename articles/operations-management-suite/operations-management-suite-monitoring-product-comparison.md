---
title: "Microsoft 모니터링 제품 비교 | Microsoft Docs"
description: "Microsoft Operations Management Suite(OMS)란 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.  이 문서는 OMS에 포함된 다양한 서비스를 식별하고 자세한 내용에 대한 링크를 제공합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>Microsoft 모니터링 제품 비교
이 문서는 OMS(Operations Management Suite)의 Log Analytics과 SCOM(System Center Operations Manager)의 차이를 아키텍처, 리소스 모니터링 방식의 논리, 수집한 데이터 분석 방식 측면에서 비교, 설명하며  각각의 차이와 상대적 강점에 대한 기본적 내용을 이해하기 위한 정보를 제공합니다.  

## <a name="basic-architecture"></a>기본 아키텍처
### <a name="system-center-operations-manager"></a>System Center Operations Manager
모든 SCOM 구성 요소는 데이터 센터에 설치하며  [에이전트](http://technet.microsoft.com/library/hh551142.aspx)는 SCOM에서 관리하는 Windows 및 Linux 컴퓨터에 설치합니다.  에이전트는 SCOM 데이터베이스 및 데이터 웨어하우스와 통신하는 [관리 서버](https://technet.microsoft.com/library/hh301922.aspx)와 연결합니다.  에이전트는 도메인 인증에 의존하여 관리 서버에 연결합니다.  신뢰할 수 있는 도메인 밖에 있는 에이전트는 인증서 인증을 수행하거나 [게이트웨이 서버](https://technet.microsoft.com/library/hh212823.aspx)에 연결할 수 있습니다.

SCOM은 운영 데이터용 SQL 데이터베이스와 보고 및 데이터 분석을 지원하는 데이터 웨어하우스용 SQL 데이터베이스가 필요합니다.  [보고 서버](https://technet.microsoft.com/library/hh298611.aspx) 는 SQL Reporting Services를 실행하여 데이터 웨어하우스의 데이터에 대해 보고합니다. 

SCOM은 [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708), [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html) 등의 제품에 관리 팩을 사용하여 클라우드 리소스를 모니터링할 수 있습니다.  이러한 관리 팩은 클라우드 리소스 검색 및 워크플로 실행을 위한 프록시로 하나 이상의 로컬 에이전트를 사용하여 성능과 가용성을 측정합니다.  또한 프록시 에이전트는 [네트워크 장치](https://technet.microsoft.com/library/hh212935.aspx) 와 다른 외부 리소스를 모니터링하는 데 사용됩니다.

운영 콘솔은 관리 서버 중 하나에 연결하는 Windows 응용 프로그램으로 관리자는 운영 콘솔을 사용하여 수집한 데이터를 보고 분석하며 SCOM 환경을 구성할 수 있습니다.  웹 기반 콘솔은 모든 IIS 서버에 호스팅할 수 있으며 브라우저를 통해 데이터 분석을 제공합니다.

![SCOM 아키텍처](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
대부분의 OMS 구성 요소가 Azure 클라우드에 있으므로 최소한의 비용과 관리 활동으로 배포 및 관리할 수 있습니다.  Log Analytics에서 수집한 모든 데이터는 OMS 리포지토리에 저장됩니다.

Log Analytics는 다음 세 가지 원본 중 하나에서 데이터를 수집할 수 있습니다.

* Windows 및 [MMA(Microsoft Management Agent)](https://technet.microsoft.com/library/mt484108.aspx) 또는 Linux 및 [Linux용 Operations Management Suite 에이전트](https://technet.microsoft.com/library/mt622052.aspx)를 실행하는 물리적 및 가상 컴퓨터.  이러한 컴퓨터는 Azure 또는 다른 클라우드에서 온-프레미스 또는 가상 컴퓨터일 수 있습니다.
* Azure의 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 데이터를 수집하는 Azure 저장소 계정.
* [SCOM 관리 그룹에 연결](https://technet.microsoft.com/library/mt484104.aspx).  이 구성에서는 에이전트가 SCOM 데이터베이스에 데이터를 제공하는 SCOM 관리 서버와 통신합니다. 그런 다음 이 데이터는 OMS 데이터 저장소로 배달됩니다.
  관리자는 수집된 데이터를 분석하고 OMS 포털(Azure에 호스팅되어 있으며 모든 브라우저에서 액세스할 수 있음)을 사용하여 Log Analytics를 분석할 수 있습니다.  이 데이터에 액세스하는 모바일 앱은 표준 플랫폼에 사용할 수 있습니다.

![Log Analytics 아키텍처](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>SCOM 및 Log Analytics 통합
SCOM을 Log Analytics의 데이터 원본으로 사용할 경우 하이브리드 모니터링 환경에서 두 제품의 기능을 활용할 수 있습니다.  SCOM에서 계속해서 관리 팩을 실행하는 것 이외에, 운영 콘솔을 통해 OMS에서 기존 SCOM 에이전트를 관리하도록 구성할 수 있습니다.  
연결된 SCOM 관리 그룹의 데이터는 네 가지 방법 중 하나를 사용하여 Log Analytics로 배달됩니다.

* 이벤트 및 성능 데이터는 에이전트에서 수집되어 SCOM으로 배달됩니다.  그런 다음 SCOM의 관리 서버가 Log Analytics로 데이터를 배달합니다.
* IIS 로그와 같은 일부 이벤트 및 보안 이벤트는 계속해서 에이전트에서 Log Analytics로 직접 배달됩니다.
* 일부 솔루션은 에이전트에 추가 소프트웨어를 배달합니다. 또는 추가 데이터를 수집하기 위해 소프트웨어를 설치해야 합니다.  이 데이터는 일반적으로 Log Analytics에 직접 전송됩니다.
* 일부 솔루션은 에이전트에서 발생하지 않는 데이터를 SCOM 관리 서버에서 직접 수집합니다.  예를 들어 [경고 관리 솔루션](https://technet.microsoft.com/library/mt484092.aspx) 은 경고가 생성된 이후 SCOM에서 경고를 수집합니다.

## <a name="monitoring-logic"></a>모니터링 논리
SCOM 및 Log Analytics는 에이전트에서 수집한 데이터와 유사한 데이터를 사용하지만 데이터 수집에 논리를 정의 및 구현하는 방식 및 수집한 데이터를 분석하는 방식이 기본적으로 다릅니다.

### <a name="operations-manager"></a>Operations Manager
SCOM의 모니터링 논리는 모니터링할 구성 요소 검색, 해당 구성 요소의 상태 측정, 분석할 데이터 수집에 대한 논리가 포함된 [관리 팩](https://technet.microsoft.com/library/hh457558.aspx) 에 구현되어 있습니다.  데이터 모니터링은 간단히 이벤트 또는 성능 카운터를 수집하는 작업일 수도 있고 스트립트에 구현된 복잡한 논리를 사용할 수도 있습니다.  완전한 모니터링이 포함된 관리 팩은 하드웨어 및 네트워크 장치 이외에 다양한 [Microsoft 및 타사 응용 프로그램](http://go.microsoft.com/fwlink/?LinkId=82105) 에 사용할 수 있습니다.  사용자 지정 응용 프로그램에 대해 [고유한 관리 팩을 작성](http://aka.ms/mpauthor) 할 수 있습니다.

관리 팩에는 성능 카운터 샘플링, 서비스 상태 확인, 스크립트 실행과 같이 각각 고유한 모니터링 기능을 수행하는 여러 워크플로가 포함되어 있습니다.  각 워크플로는 독립적으로 실행되며 작성할 데이터베이스, 경고 발생 여부와 같은 고유 결과를 정의합니다. 

실행 빈도, 오류로 간주할 임계값, 생성하는 경고의 심각도와 같은 워크플로의 상세 정보를 재정의할 수 있습니다.  또한 고유 워크플로를 추가하여 추가 기능을 제공할 수 있습니다.

![재정의](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

관리 팩은 Operations Manager 데이터베이스에 설치하며 관리 서버에서 에이전트로 자동 배포합니다.  각 에이전트는 관리 팩을 자동으로 다운로드하고 설치한 응용 프로그램과 관련된 워크플로를 로드합니다.  에이전트에서 수집하는 데이터는 다시 관리 서버로 배달되어 SCOM 데이터베이스 및 데이터 웨어하우스로 삽입됩니다.  운영 콘솔을 사용하면 관리 팩에 포함된 사용자 지정 보기, 대시보드, 보고서를 통해 이 데이터를 보고 분석할 수 있습니다.

관리 팩의 배포는 아래 다이어그램에 나와 있습니다.

![관리 팩 흐름](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>이벤트 및 성능 수집
Log Analytics는 Windows 이벤트 로그, IIS 로그, Syslog와 같은 원본을 사용하여 에이전트 시스템에서 이벤트 및 성능 카운터를 수집합니다.  Log Analytics 포털을 통해 수집하는 데이터의 기준을 정의한 다음 로그 쿼리를 사용하여 수집된 데이터를 분석할 수 있습니다.  OMS 작업 영역을 만들 때 표준 기준 집합을 정의하며 특정 애플리케이션에 대한 추가 데이터를 정의할 수 있습니다. 

![Log Analytics에서 이벤트 로그 정의](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

SCOM에는 일반적으로 데이터의 구체적 기준을 정의하는 여러 개의 자세한 워크플로와 대응을 위해 수행해야 하는 작업이 있는 반면 Log Analytics에는 데이터 수집에 대해 더 일반적이 기준이 있습니다.  로그 쿼리와 솔루션은 클라우드에 특정 데이터가 수집된 이후 해당 데이터를 분석하고 조치를 취하는 더욱 선별적 기준을 제공합니다.

#### <a name="solutions"></a>솔루션
솔루션은 데이터 수집 및 분석에 대해 추가 논리를 제공합니다.  솔루션 갤러리에서 OMS 구독에 추가할 솔루션을 선택할 수 있습니다.

![솔루션 갤러리](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

솔루션은 기본적으로 클라우드에서 실행되며 OMS 리포지토리에 수집된 이벤트 및 성능 카운터의 분석을 제공합니다.  또한 수집할 추가 데이터 중 로그 쿼리로 분석할 수 있거나 OMS 대시보드의 솔루션에서 제공하는 추가 사용자 인터페이스로 분석할 수 있는 데이터를 정의할 수 있습니다. 

예를 들어 [변경 추적 솔루션](https://technet.microsoft.com/library/mt484099.aspx) 이 에이전트 시스템에서 구성 변경 사항을 검색하고 OMS 리포지토리에 이벤트를 쓰면 여러 그래픽 뷰로 분석하여 검색된 변경 사항을 요약할 수 있습니다.  요약된 뷰에서 로그 쿼리로 드릴다운하여 솔루션에서 수집한 상세 데이터를 표시할 수 있습니다.

구독에 추가할 솔루션을 선택할 수 있지만 현재는 고유 솔루션을 만드는 기능은 없습니다.  수집할 이벤트 및 성능 카운터를 선택하고 고유 로그 쿼리를 기준으로 사용자 지정 뷰를 만들 수 있습니다.

Log Analytics의 모니터링 논리는 아래 다이어그램에 요약되어 있습니다.

![Log Analytics 솔루션 흐름](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>상태 모니터링
### <a name="operations-manager"></a>Operations Manager
SCOM은 응용 프로그램의 다양한 구성 요소를 모델링하고 각각에 대해 실시간 상태를 제공할 수 있습니다.  여기서 검색된 오류와 성능을 시간별로 볼 수 있을 뿐만 아니라 특정 시간에 응용 프로그램 또는 시스템과 각 구성 요소의 실제 상태를 검증할 수 있습니다.  SCOM은 응용 프로그램 가용 시간을 알기 때문에 SCOM의 상태 엔진은 응용 프로그램의 가용성을 시간별로 분석 및 보고하는 Service Level Agreement(서비스 수준 약정)도 지원합니다.

예를 들어 아래 뷰는 SCOM에서 모니터링하는 SQL 데이터베이스 엔진의 실시간 상태를 보여줍니다.  데이터베이스 엔진 중 하나에 대한 각 데이터베이스의 상태는 뷰 하단에 표시됩니다.

![상태 뷰](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

아래에 데이터베이스 엔진 중 하나에 대한 상태 탐색기와 전반적 상태를 확인하는 데 사용된 모니터가 표시되어 있습니다.  이러한 모니터는 SQL 관리 팩에 정의되어 있으며 SCOM에서 검색한 모든 SQL 데이터베이스 엔진에 대해 실행됩니다.

![상태 탐색기](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

여러 시스템의 구성 요소를 결합하여 분산된 응용 프로그램의 상태를 측정할 수 있습니다.  이 기능은 분산된 여러 구성 요소가 포함된 업무용 응용 프로그램에 특히 유용합니다.  응용 프로그램의 가용성에 롤업되는 각 구성 요소의 상태를 측정하는 모델을 만들 수 있습니다.

Active Directory는 분산된 구성 요소를 분석하는 모델을 제공하는 관리 팩의 한 가지 예입니다.  아래 샘플 다이어그램은 전반적 환경의 상태와 포리스트, 도메인, 도메인 컨트롤러 간 관계를 보여줍니다.  이러한 각 구성 요소에는 위의 SQL 예제와 유사한 하위 구성 요소 및 여러 모니터가 포함되어 있습니다.

![SCOM 다이어그램 뷰](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS에는 응용 프로그램을 모델링하거나 실시간 상태를 측정하는 공통 엔진이 포함되어 있지 않습니다.  개별 솔루션에서 수집된 데이터를 기준으로 특정 서비스의 전반적 상태를 평가할 수 있으며 에이전트에 사용자 지정 논리를 설치하여 실시간 분석을 수행할 수 있습니다.  솔루션은 OMS 리포지토리에 액세스하면서 클라우드에서 실행되기 때문에 일반적으로 관리 팩에서 수행하는 분석보다 깊이 있는 분석을 제공하는 경우가 많습니다. 

예를 들어 [AD 평가 및 SQL 평가 솔루션](https://technet.microsoft.com/library/mt484102.aspx) 은 수집된 데이터를 분석하고 환경의 다양한 측면을 평가합니다.  여기에는 환경의 가용성과 성능을 향상하기 위한 개선 권장 사항이 포함됩니다.

![AD 평가 솔루션](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>데이터 분석
SCOM 및 Log Analytics은 수집된 데이터를 각각 다른 방법으로 분석합니다.  SCOM은 운영 콘솔에 최근 데이터를 다양한 형식으로 분석하기 위한 뷰와 대시보드, 데이터 웨어하우스의 데이터를 탭 형식으로 표시하는 보고서가 있습니다.  Log Analytics는 OMS 리포지터리의 데이터 분석에 대한 완전한 로그 쿼리 언어와 인터페이스를 제공합니다.  SCOM을 Log Analytics의 데이터 원본으로 사용하는 경우 리포지토리에 SCOM에서 수집한 데이터가 포함되므로 Log Analytics 도구를 사용하여 두 시스템의 데이터를 분석할 수 있습니다.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>뷰
운영 콘솔의 뷰를 사용하면 SCOM에서 수집한 데이터를 다양한 형식으로 볼 수 있습니다. 일반적으로 이벤트, 경고, 상태 데이터에는 탭 형식을 사용하고 성능 데이터에는 선 그래프를 사용합니다.  뷰는 데이터에 대해 최소의 분석 또는 통합을 수행하지만 특정 기준으로 필터링할 수도 있습니다. 

![뷰](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

관리 팩은 일반적으로 여러 뷰를 제공하며 모니터링하는 응용 프로그램 또는 시스템을 지원합니다.  여기에는 관리 팩이 검색하는 다양한 개체에 대한 상태 뷰, 검색된 문제에 대한 경고 뷰, 카운터에 대한 성능 뷰 등이 있습니다.

뷰는 미해결 경고, 모니터링 시스템 및 개체의 상태를 포함하여 환경의 현재 상태를 분석하는 데 특히 적합합니다.  근본 원인을 분석하기 위해 특정 경고를 지원하는 상세 이벤트 또는 성능 데이터로 드릴다운할 수 있습니다. 마찬가지로 응용 프로그램의 다양한 구성 요소의 성능 및 상태를 보고 현재 상태를 평가할 수 있습니다.

#### <a name="dashboards"></a>대시보드
운영 콘솔의 대시보드는 기본적으로 뷰와 동일한 데이터를 사용하지만 더 자세한 사용자 지정이 가능하며 풍부한 시각화를 포함할 수 있습니다.  사용자 목적에 맞게 쉽게 사용자 지정 가능한 표준 대시보드 집합을 사용할 수 있습니다.  또한 PowerShell 위젯을 사용하면 PowerShell 쿼리에서 반환한 데이터를 표시할 수 있습니다.

![대시보드](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

개발자는 관리 팩에 포함하는 대시보드에 사용자 지정 구성 요소를 추가할 수 있습니다.  이러한 구성 요소는 다음과 같은 SQL 관리 팩의 대시보드와 같이 특정 응용 프로그램에 맞게 특수 구성이 가능합니다.  이 대시보드는 사용자 지정 버전의 템플릿으로도 사용할 수 있습니다.

![SQL 대시보드](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>보고서
SCOM의 보고서는 데이터 웨어하우스의 데이터를 탭 형식으로 분석합니다.  이러한 보고서는 PDF, CSV, Word 등의 다양한 파일 형식으로 인쇄하여 자동 배달 예약을 할 수 있습니다.  보고서는 데이터 웨어하우스의 데이터를 사용하므로 특히 장기 추세를 분석하는 데 적합합니다.

관리 팩은 일반적으로 특정 응용 프로그램에 대한 사용자 지정 보고서를 제공합니다.  또한 일반 보고서 라이브러리 중에서 선택하여 사용자 응용 프로그램에 맞게 사용자 지정하거나 임시 분석을 수행할 수 있습니다.

다음은 Active Directory 관리 팩에서 수집한 데이터를 보여주는 샘플 성능 보고서입니다.

![보고서](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics에는 사용자 지정 뷰 또는 보고서를 만들지 않고도 여러 응용 프로그램의 데이터에 대해 분석을 수행하는 데 사용할 수 있는 [쿼리 언어](https://technet.microsoft.com/library/mt484120.aspx) 가 있습니다.  OMS는 클라우드에 구현되므로 쿼리 및 데이터 분석 성능은 하드웨어 제한이 적용되지 않으며 수백 만 개의 기록을 포함한 쿼리를 빠르게 분석할 수 있습니다. 

Log Analytics의 쿼리도 다른 기능의 기준입니다.  쿼리를 저장하고 결과를 Excel로 내보내거나 정기적으로 자동 실행되도록 하고 결과가 특정 기준과 일치할 경우 경고를 발생할 수 있습니다.  

![로그 쿼리 흐름](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

다음은 Log Analytics 쿼리의 예입니다.  이 예에서 이름에 "started"가 포함된 모든 이벤트는 반환되어 이벤트 ID 기준으로 그룹화되었습니다.  사용자가 쿼리를 제공하기만 하면 Log Analytics에서 사용자 인터페이스를 동적으로 생성하여 분석을 수행합니다.  목록에서 임의 항목을 선택하면 자세한 이벤트 데이터가 반환됩니다.

![로그 쿼리](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Log Analytics의 쿼리로 임시 분석을 제공하는 기능 이외에, 나중에 사용하기 위해 저장하거나 다음 예제와 같이 [OMS 대시보드](http://technet.microsoft.com/library/mt484090.aspx) 에 추가할 수 있습니다.

![OMS 대시보드](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>다음 단계
* [SCOM(System Center Operations Manager)](https://technet.microsoft.com/library/hh205987.aspx)을 배포합니다.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics)에 등록합니다.  

