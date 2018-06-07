---
title: System Center Operations Manager와 서비스 맵 통합 | Microsoft Docs
description: 서비스 맵은 Windows 및 Linux 시스템의 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 서비스 맵을 사용하여 Operations Manager에 자동으로 분산 응용 프로그램 다이어그램을 만드는 방법을 설명합니다.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 6fbc49584b040f952fdff147207864d2d1f6377e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>System Center Operations Manager와 서비스 맵 통합
  > [!NOTE]
  > 이 기능은 공개 미리 보기 상태입니다.
  > 
  
서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 서비스 맵을 사용하면 생각하는 방식대로 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다. 자세한 내용은 [서비스 맵 설명서]( monitoring-service-map.md)를 참조하세요.

서비스 맵과 System Center Operations Manager 간의 이러한 통합을 통해 서비스 맵의 동적 종속성 맵을 기준으로 하는 분산 응용 프로그램 다이어그램을 Operations Manager에서 자동으로 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 일련의 서버를 관리하는 Operations Manager 관리 그룹(2012 R2 이상)
* 서비스 맵 솔루션이 사용하도록 설정된 Log Analytics 작업 영역.
* Operations Manager로 관리되고 있고 서비스 맵으로 데이터를 전송하는 서버 집합(하나 이상의 서버) Windows 및 Linux 서버가 지원됩니다.
* Log Analytics 작업 영역과 연결된 Azure 구독에 액세스할 수 있는 서비스 주체. 자세한 내용은 [서비스 주체 만들기](#creating-a-service-principal)로 이동합니다.

## <a name="install-the-service-map-management-pack"></a>서비스 맵 관리 팩 설치
Operations Manager와 서비스 맵의 통합은 Microsoft.SystemCenter.ServiceMap 관리 팩 번들(Microsoft.SystemCenter.ServiceMap.mpb)을 가져와야 가능합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=55763)에서 관리 팩 번들을 다운로드할 수 있습니다. 이 번들에는 다음 관리 팩이 포함됩니다.
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>서비스 맵 통합 구성
서비스 맵 관리 팩을 설치하면 **관리** 창의 **Operations Management Suite** 아래에 **Service Map**이라는 새 노드가 표시됩니다. 

서비스 맵 통합을 구성하려면 다음을 수행합니다.

1. 구성 마법사를 열려면 **서비스 맵 개요** 창에서 **작업 영역 추가**를 클릭합니다.  

    ![서비스 맵 개요 창](media/monitoring-service-map/scom-configuration.png)

2. **연결 구성** 창에서 서비스 주체의 테넌트 이름 또는 ID, 응용 프로그램 ID(사용자 이름 또는 클라이언트 ID라고도 함) 및 암호를 입력하고 **다음**을 클릭합니다. 자세한 내용은 [서비스 주체 만들기](#creating-a-service-principal)로 이동합니다.

    ![연결 구성 창](media/monitoring-service-map/scom-config-spn.png)

3. **구독 선택** 창에서 Azure 구독, Azure 리소스 그룹(Log Analytics 작업 영역을 포함하는 그룹), Log Analytics 작업 영역을 선택하고 **다음**을 클릭합니다.

    ![Operations Manager 구성 작업 영역](media/monitoring-service-map/scom-config-workspace.png)

4. **컴퓨터 그룹 선택** 창에서 Operations Manager에 동기화하려는 [서비스 맵 컴퓨터 그룹]을 선택합니다. **컴퓨터 그룹 추가/제거**를 클릭하고, **사용 가능한 컴퓨터 그룹** 목록에서 그룹을 선택하고, **추가**를 클릭합니다.  그룹 선택이 완료되면 **확인**을 클릭하여 완료합니다.
    
    ![Operations Manager 구성 컴퓨터 그룹](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. **서버 선택** 창에서 Operations Manager 및 서비스 맵 간에 동기화하려는 서버가 있는 서비스 맵 서버 그룹을 구성합니다. **서버 추가/제거**를 클릭합니다.   
    
    통합 기능을 통해 서버에 대한 분산 응용 프로그램 다이어그램을 만들려면 서버가 다음 조건을 충족해야 합니다.

    * Operations Manager에서 관리됨
    * 서비스 맵에서 관리됨
    * 서비스 맵 서버 그룹에 나열됨

    ![Operations Manager 구성 그룹](media/monitoring-service-map/scom-config-group.png)

6. 선택 사항: Log Analytics와 통신할 관리 서버 리소스 풀을 선택하고 **작업 영역 추가**를 클릭합니다.

    ![Operations Manager 구성 리소스 풀](media/monitoring-service-map/scom-config-pool.png)

    Log Analytics 작업 영역을 구성 및 등록하는 데 1분 정도 걸릴 수 있습니다. 구성된 후에 Operations Manager는 첫 번째 서비스 맵 동기화를 시작합니다.

    ![Operations Manager 구성 리소스 풀](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>서비스 맵 모니터링
Log Analytics 작업 영역이 연결되면 새 폴더인 Service Map이 Operations Manager 콘솔의 **모니터링** 창에 표시됩니다.

![Operations Manager 모니터링 창](media/monitoring-service-map/scom-monitoring.png)

서비스 맵 폴더에는 4개의 노드가 있습니다.
* **활성 경고**: Operations Manager와 서비스 맵 간의 통신에 대한 모든 활성 경고를 나열합니다.  이러한 경고는 Operations Manager에 동기화되는 Log Analytics 경고가 아닙니다. 

* **서버**: 서비스 맵에서 동기화하도록 구성된 모니터링 대상 서버를 나열합니다.

    ![Operations Manager 서버 모니터링 창](media/monitoring-service-map/scom-monitoring-servers.png)

* **컴퓨터 그룹 종속성 보기**: 서비스 맵에서 동기화되는 모든 컴퓨터 그룹을 나열합니다. 원하는 그룹을 클릭하여 해당 그룹의 배포 응용 프로그램 다이어그램을 볼 수 있습니다.

    ![Operations Manager 분산 응용 프로그램 다이어그램](media/monitoring-service-map/scom-group-dad.png)

* **서버 종속성 보기**: 서비스 맵에서 동기화되는 모든 서버를 나열합니다. 원하는 서버를 클릭하면 해당 서버의 분산 응용 프로그램 다이어그램을 볼 수 있습니다.

    ![Operations Manager 분산 응용 프로그램 다이어그램](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>작업 영역 편집 또는 삭제
**서비스 맵 개요** 창(**관리** 창 --> Operations Management Suite**Operations Management Suite** > **서비스 맵**)을 통해 구성된 작업 영역을 편집하거나 삭제할 수 있습니다. 현재는 하나의 Log Analytics 작업 영역만 구성할 수 있습니다.

![Operations Manager 작업 영역 편집 창](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>규칙 및 재정의 구성
_Microsoft.SystemCenter.ServiceMapImport.Rule_ 규칙은 서비스 맵에서 주기적으로 정보를 가져오기 위해 생성됩니다. 동기화 타이밍을 변경하려면 규칙 재정의를 구성할 수 있습니다(**제작** 창 > **규칙** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Operations Manager 재정의 속성 창](media/monitoring-service-map/scom-overrides.png)

* **Enabled** – 자동 업데이트를 사용하거나 사용하지 않도록 설정합니다. 
* **IntervalMinutes**: 업데이트 간 시간을 다시 설정합니다. 기본 간격은 1시간입니다. 서버 맵을 더 자주 동기화하려면 값을 변경할 수 있습니다.
* **TimeoutSeconds**: 요청 시간이 초과되기 전의 시간을 다시 설정합니다. 
* **TimeWindowMinutes**: 데이터를 쿼리하기 위한 기간을 다시 설정합니다. 기본값은 60분입니다. 서비스 맵에서 허용되는 최대값은 60분입니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

현재 디자인은 다음과 같은 문제 및 제한 사항을 나타냅니다.
* 하나의 Log Analytics 작업 영역에만 연결할 수 있습니다.
* **제작** 창을 통해 서비스 맵 서버 그룹에 서버를 수동으로 추가할 수는 있지만, 이러한 서버에 대한 맵은 즉시 동기화되지 않으며  다음 동기화 주기 동안에 서비스 맵에서 동기화됩니다.
* 관리 팩에서 만든 배포 응용 프로그램 다이어그램을 변경하는 경우 이러한 변경 내용은 다음 동기화에서 서비스 맵을 통해 덮어 쓰여집니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기
서비스 주체 만들기에 대한 공식적인 Azure 설명서를 보려면 다음을 참조하세요.
* [PowerShell을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI를 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>사용자 의견
서비스 맵 또는 이 설명서에 대한 의견이 있습니까? 기능을 제안하거나 기존 제안에 투표할 수 있는 [사용자 의견 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)를 방문하세요.
