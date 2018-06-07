---
title: Azure Application Insights의 응용 프로그램 맵 | Microsoft Docs
description: 응용 프로그램 맵을 사용하여 복잡한 응용 프로그램 토폴로지 모니터링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: a02d345c0f624f2d1694b4b9622bf3c82c009429
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="application-map-triage-distributed-applications"></a>응용 프로그램 맵: 분산 응용 프로그램 심사
응용 프로그램 맵을 사용하면 분산된 응용 프로그램의 모든 구성 요소에서 성능 병목 상태 또는 실패 핫스폿을 찾는 데 도움이 됩니다. 맵의 각 노드는 응용 프로그램 구성 요소 또는 해당 종속성을 나타내며 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 응용 프로그램의 부분입니다. 개발자 및 운영 팀은 이러한 응용 프로그램 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다. 

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 맵 미리 보기 환경은 구성 요소를 설정 방식에 관계없이 표시합니다.

## <a name="composite-application-map-preview"></a>복합 응용 프로그램 맵(미리 보기)
*이것은 초기 미리 보기이며, 이 맵에 계속 추가할 예정입니다. 새 환경에 대한 사용자 의견을 환영합니다. 미리 보기와 클래식 환경 간을 쉽게 전환할 수 있습니다.*

[미리 보기 목록](app-insights-previews.md)에서 "복합 응용 프로그램 맵"을 사용하도록 설정하거나 오른쪽 위 구석에 있는 토글에서 "맵 미리 보기"를 클릭합니다. 클래식 환경으로 다시 전환하려면 이 토글을 사용할 수 있습니다.
![맵 미리 보기 사용](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
이 미리 보기는 이전 "다중 역할 응용 프로그램 맵" 미리 보기를 대신합니다. 이제 이 미리 보기를 사용하여 응용 프로그램 구성 요소 종속성의 여러 수준에서 전체 토폴로지를 볼 수 있습니다. 사용자 의견을 주시기 바랍니다. 앞으로 클래식 맵이 지원하는 것과 비슷한 기능을 더 추가할 예정입니다.

여러 수준의 관련된 응용 프로그램 구성 요소의 전체 응용 프로그램 토폴로지를 볼 수 있습니다. 구성 요소는 다른 Application Insights 리소스이거나 단일 리소스 내의 다른 역할일 수 있습니다. 앱은 Application Insights SDK가 설치된 서버 간에 수행된 HTTP 종속성 호출을 따라 구성 요소를 찾습니다. 

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. 미리 보기를 처음 로드할 때 이 구성 요소와 관련된 구성 요소를 검색하기 위해 쿼리 집합이 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 응용 프로그램의 구성 요소 수로 업데이트됩니다. 
![맵 미리 보기](media/app-insights-app-map/preview.png)

"맵 구성 요소 업데이트"를 클릭하면 맵이 해당 시점까지 검색된 모든 구성 요소로 새로 고쳐집니다.
![로드된 맵 미리 보기](media/app-insights-app-map/components-loaded-hierarchical.png)

모든 구성 요소가 단일 Application Insights 리소스 내의 역할인 경우 이 검색 단계가 필요하지 않습니다. 이러한 응용 프로그램에 대한 초기 로드 시에는 모든 구성 요소가 포함됩니다.

새 환경의 주요 목표 중 하나는 수백 가지 구성 요소가 있는 복잡한 토폴로지를 시각화할 수 있는 것입니다. 새로운 환경은 확대/축소를 지원하며 확대 시 세부 정보를 추가합니다. 축소하여 더 많은 구성 요소를 한 눈에 볼 수도 있고, 실패율이 더 높은 구성 요소를 찾을 수 있습니다. 

![확대/축소 수준](media/app-insights-app-map/zoom-levels.png)

모든 구성 요소를 클릭하여 관련 정보를 확인하고, 해당 구성 요소에 대한 성능 및 실패 심사 환경으로 이동합니다.

![플라이아웃](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>클래식 응용 프로그램 맵

지도에서는 다음을 보여 줍니다.

* 가용성 테스트
* 클라이언트 쪽 구성 요소(JavaScript SDK를 사용하여 모니터링됨)
* 서버 쪽 구성 요소
* 클라이언트 및 서버 구성 요소의 종속성

![앱 맵](./media/app-insights-app-map/02.png)

종속성 링크 그룹을 확장하고 축소할 수 있습니다.

![축소](./media/app-insights-app-map/03.png)

SQL, HTTP 등 한 종류의 종속성이 많은 경우 그룹화되어 표시될 수 있습니다. 

![그룹화된 종속성](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>문제 발견
각 노드에는 해당 구성 요소에 대한 부하, 성능 및 실패율과 같은 관련 성능 지표가 있습니다. 

경고 아이콘은 발생 가능한 문제를 강조 표시합니다. 주황색 경고는 요청, 페이지 보기 또는 종속성 호출에서 오류가 발생했음을 의미합니다. 빨간색 경고는 5% 이상의 실패율을 의미합니다. 이러한 임계값을 조정하려는 경우 옵션을 엽니다.

![오류 아이콘](./media/app-insights-app-map/04.png)

활성 경고도 다음과 같이 표시됩니다. 

![활성 경고](./media/app-insights-app-map/05.png)

SQL Azure를 사용하는 경우 성능을 향상시키는 방법에 대한 권장 사항이 있는 경우 나타나는 아이콘이 있습니다. 

![Azure 권장 사항](./media/app-insights-app-map/06.png)

자세한 내용을 보려면 아이콘을 클릭합니다.

![Azure 권장 사항](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>진단 클릭
맵의 각 노드에서 진단하기 위해 클릭할 대상을 지정합니다. 옵션은 노드 유형에 따라 다릅니다.

![서버 옵션](./media/app-insights-app-map/09.png)

Azure에서 호스팅되는 구성 요소의 경우 옵션은 직접 링크를 포함합니다.

## <a name="filters-and-time-range"></a>필터 및 시간 범위
기본적으로 맵은 선택한 시간 범위에 사용할 수 있는 모든 데이터를 요약합니다. 하지만 특정 작업 이름 또는 종속성을 포함하도록 필터링할 수 있습니다.

* 작업 이름: 페이지 보기 및 서버 쪽 요청 형식을 모두 포함합니다. 이 옵션을 사용하면 맵은 선택한 작업에 대해서만 서버/클라이언트 쪽 노드의 KPI를 보여 줍니다. 특정 작업의 컨텍스트에서 호출될 종속성을 보여 줍니다.
* 종속성 기본 이름: AJAX 브라우저 종속성 및 서버 쪽 종속성을 포함합니다. TrackDependency API를 사용하여 사용자 지정 종속성 원격 분석을 보고하는 경우 여기에도 표시됩니다. 지도에 표시되는 종속성을 선택할 수 있습니다. 현재 이 선택 항목은 서버 쪽 요청 또는 클라이언트 쪽 페이지 보기를 필터링하지 않습니다.

![필터 설정](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>필터 저장
적용한 필터를 저장하려면 필터링된 보기를 [대시보드](app-insights-dashboards.md)에 고정합니다.

![대시보드에 고정](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>오류 창
맵에서 노드를 클릭하면 오른쪽에 해당 노드에 대한 오류를 요약해서 보여 주는 오류 창이 표시됩니다. 먼저 오류는 작업 ID별로 그룹화된 후 문제 ID별로 그룹화됩니다.

![오류 창](./media/app-insights-app-map/error-pane.png)

오류를 클릭하면 해당 오류의 가장 최근 인스턴스로 이동됩니다.

## <a name="resource-health"></a>리소스 상태
일부 리소스 형식의 경우 오류 창 상단에 리소스 상태가 표시됩니다. 예를 들어 SQL 노드를 클릭하면 데이터베이스 상태와 발생한 경고가 표시됩니다.

![리소스 상태](./media/app-insights-app-map/resource-health.png)

리소스 이름을 클릭하여 해당 리소스에 대한 표준 개요 메트릭을 볼 수 있습니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>사용자 의견
포털 사용자 의견 옵션을 통해 피드백을 제공해주세요.

![MapLink-1 이미지](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>다음 단계

* [Azure Portal](https://portal.azure.com)
