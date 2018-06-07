---
title: Azure 자가 진행식 데모의 서비스 맵 솔루션 | Microsoft Docs
description: 서비스 맵은 Windows 및 Linux 시스템의 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑하는 Azure의 솔루션입니다. 서비스 맵을 사용하여 웹 응용 프로그램에서 시뮬레이션된 문제를 식별하고 진단하는 방법을 설명하는 자가 진행식 데모입니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>자가 진행식 데모 - 서비스 맵
이것은 Azure의 [서비스 맵 솔루션](monitoring-service-map.md)을 사용하여 웹 응용 프로그램에서 시뮬레이션된 문제를 식별하고 진단하기 위해 단계별로 안내하는 자가 진행식 데모입니다. 서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 다른 서비스 및 솔루션에서 수집된 데이터를 통합하여 성능을 분석하고 문제를 식별하는 데에도 도움을 줍니다. 또한 근본적인 문제를 식별하기 위해 [Log Analytics에서 로그 검색](../log-analytics/log-analytics-log-searches.md)을 사용하여 수집된 데이터를 드릴다운합니다.


## <a name="scenario-description"></a>시나리오 설명
방금 ACME 고객 포털 응용 프로그램에 성능 문제가 있다는 알림을 받았습니다. 유일한 정보는 이러한 문제가 오늘 오전 4시(PST) 정도에 발생했다는 점입니다. 웹 서버 집합이 아닌 포털이 종속된 모든 구성 요소를 전적으로 확신할 수 없습니다. 

## <a name="components-and-features-used"></a>사용된 구성 요소 및 기능
- [서비스 맵 솔루션](monitoring-service-map.md)
- [Log Analytics 로그 검색](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>연습

### <a name="1-connect-to-the-oms-experience-center"></a>1. OMS 환경 센터에 연결
이 연습에서는 샘플 데이터에 완벽한 Log Analytics 환경을 제공하는 [Operations Management Suite 환경 센터](https://experience.mms.microsoft.com/)를 사용합니다. 이 링크를 따라 시작하고 정보를 입력한 다음 **Insight and Analytics** 시나리오를 선택합니다.


### <a name="2-start-service-map"></a>2. 서비스 맵 시작
**서비스 맵** 타일을 클릭하여 서비스 맵 솔루션을 시작합니다.

![서비스 맵 타일](media/monitoring-walkthrough-servicemap/tile.png)

서비스 맵 콘솔이 표시됩니다. 왼쪽 창에는 서비스 맵 에이전트가 설치된 환경에 있는 컴퓨터 목록이 표시됩니다. 이 목록에서 보려는 컴퓨터를 선택합니다.

![컴퓨터 목록](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. 컴퓨터 보기
이미 알고 있는 AcmeWFE001 및 AcmeWFE002라는 웹 서버에서 시작하는 것이 적절합니다. **AcmeWFE001**을 클릭합니다. AcmeWFE001의 맵 및 모든 해당 종속성을 표시합니다. 선택한 컴퓨터에서 실행 중인 프로세스 및 통신하는 외부 서비스를 확인할 수 있습니다.

![웹 서버](media/monitoring-walkthrough-servicemap/web-server.png)

웹 응용 프로그램의 성능에 관심이 있으므로 **AcmeAppPool(IIS 앱 풀)** 프로세스를 클릭합니다. 이 프로세스의 세부 정보를 표시하고 해당 종속성을 강조 표시합니다. 

![앱 풀](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. 기간 변경

문제가 오전 4시에 발생했다면 당시에 상황이 어땠는지 살펴보겠습니다. **시간 범위**를 클릭하고 20분의 기간을 두고 오전 4시(PST)로 시간을 변경(현재 날짜를 유지하고 현지 표준 시간대 조정)합니다.

![시간 선택](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. 경고 보기

이제 **acmetomcat** 종속성에 경고가 표시되면 잠재적인 문제가 있다는 것을 확인할 수 있습니다. **acmetomcat**에서 경고 아이콘을 클릭하여 경고에 대한 정보를 표시합니다. 여기서 중요한 CPU 사용률을 확인하고 자세한 내용을 보기 위해 확장할 수 있습니다. 이 때문에 성능이 저하될 수 있습니다. 

![경고](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. 성능 보기

**acmetomcat**을 자세히 살펴보겠습니다. **acmetomcat**의 오른쪽 상단을 클릭하고 **서버 맵 로드**를 선택하여 이 컴퓨터의 세부 데이터 및 종속성을 표시합니다. 의문점을 확인하기 위해 해당 성능 카운터를 좀 더 살펴볼 수 있습니다. **성능** 탭을 선택하여 시간 범위 동안 [Log Analytics에서 수집된 성능 카운터](../log-analytics/log-analytics-data-sources-performance-counters.md)를 표시합니다. 프로세서와 메모리에서 주기적으로 급격한 변화를 확인할 수 있습니다.

![성능](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. 변경 내용 추적 보기
이 높은 사용률을 일으킨 원인을 확인해 보겠습니다. **요약** 탭을 클릭합니다. Log Analytics가 실패한 연결, 중요한 경고 및 소프트웨어 변경과 같이 컴퓨터에서 수집한 정보가 제공됩니다. 관련 최신 정보가 포함된 섹션이 이미 확장되어 있어야 하고, 다른 섹션을 확장하여 포함된 정보를 검사할 수 있습니다.


**변경 내용 추적**이 열려 있지 않은 경우 확장합니다. 그러면 [변경 내용 추적 솔루션](../log-analytics/log-analytics-change-tracking.md)에서 수집한 정보를 표시합니다. 이 기간 동안 소프트웨어가 변경되었습니다. **소프트웨어**를 클릭하여 세부 정보를 가져옵니다. 오전 4시 직후에 백업 프로세스가 컴퓨터에 추가되었으므로 리소스를 과도하게 사용하는 원인으로 표시됩니다.

![변경 내용 추적](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. 로그 검색에서 세부 정보 보기
Log Analytics 작업 영역에서 수집된 자세한 성능 정보를 확인하여 추가로 확인할 수 있습니다. **경고** 탭을 다시 클릭한 다음 **높은 CPU** 경고 중 하나를 클릭합니다. **로그 검색에 표시**를 클릭합니다. 그러면 작업 영역에 저장된 모든 데이터에 대해 [검색 로그](../log-analytics/log-analytics-log-searches.md)를 수행할 수 있는 [로그 검색] 창이 열립니다. 서비스 맵에서 관심이 있는 경고를 검색하기 위한 쿼리가 이미 입력되었습니다. 

![로그 검색](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. 저장된 검색 열기
이 경고를 생성한 성능 컬렉션에 대한 몇 가지 자세한 내용을 가져올 수 있는지 확인하고, 해당 백업 프로세스로 인해 문제가 발생했는지에 대한 의문을 확인해 보겠습니다. 시간 범위를 **6시간**으로 변경합니다. **즐겨찾기**를 클릭하고 **서비스 맵**에 대한 저장된 검색까지 아래로 스크롤합니다. 이 분석을 위해 특별히 이러한 쿼리를 만들었습니다. **acmetomcat의 CPU에 따른 상위 5개 프로세스**를 클릭합니다.

![저장된 검색](./media/monitoring-walkthrough-servicemap/saved-search.png)


이 쿼리는 **acmetomcat**의 프로세스에서 사용하는 상위 5개 프로세스의 목록을 반환합니다. 쿼리를 검사하여 로그 검색에 사용되는 쿼리 언어 소개로 이동할 수 있습니다. 다른 컴퓨터에 대한 프로세스에 관심이 있는 경우 해당 정보를 검색하도록 쿼리를 수정할 수 있습니다.

이 경우에 백업 프로세스가 앱 서버의 CPU 중 약 60%를 일관되게 사용하고 있는 것을 확인할 수 있습니다. 새 프로세스로 인해 성능 문제가 발생한 것이 분명합니다. 솔루션은 새 백업 소프트웨어를 응용 프로그램 서버에서 제거하는 것이어야 합니다. 실제로 Azure Automation에서 관리되는 DSC(Desired State Configuration)를 사용하여 이 프로세스가 이러한 중요한 시스템에서 실행되지 않도록 하는 정책을 정의할 수 있습니다.


## <a name="summary-points"></a>요약 지점
- [서비스 맵](monitoring-service-map.md)은 해당 서버 및 종속성을 모두 알 수 없더라도 전체 응용 프로그램의 보기를 제공합니다.
- 서비스 맵은 다른 관리 솔루션에서 수집된 데이터를 표시하여 응용 프로그램 및 기본 인프라와 관련된 문제를 식별하는 데 도움을 줍니다.
- [로그 검색](../log-analytics/log-analytics-log-searches.md)을 사용하면 Log Analytics 작업 영역에 수집된 특정 데이터를 드릴다운할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
- [서비스 맵](monitoring-service-map.md)에 대해 자세히 알아봅니다.
- 서비스 맵을 [배포 및 구성](monitoring-service-map-configure.md)합니다.
- 서비스 맵에 필요하고 에이전트에서 저장한 운영 데이터가 저장되는 [Log Analytics](../log-analytics/log-analytics-overview.md)에 대해 자세히 알아봅니다.