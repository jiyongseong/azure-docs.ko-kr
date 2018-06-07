---
title: "Azure Portal을 사용하여 Log Analytics로 Service Fabric 응용 프로그램 평가 | Microsoft Docs"
description: "Azure Portal에서 Log Analytics의 Service Fabric 솔루션을 사용하여 Service Fabric 응용 프로그램, 마이크로 서비스, 노드 및 클러스터의 위험과 상태를 평가할 수 있습니다."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nini
ms.openlocfilehash: 10c5394935c59b21139c0e141c348ce20a7c0800
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Azure Portal에서 Service Fabric 응용 프로그램 및 마이크로 서비스 평가

> [!div class="op_single_selector"]
> * [리소스 관리자](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Service Fabric 기호](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

이 문서에서는 Log Analytics에서 Service Fabric 솔루션을 사용하여 Service Fabric 클러스터 간의 문제를 파악 및 해결하는 방법에 대해 설명합니다.

Service Fabric 솔루션은 Azure WAD 테이블에서 이 데이터를 수집하여 Service Fabric VM에서 Azure 진단 데이터를 사용합니다. 그러면 Log Analytics에서는 **Reliable Service 이벤트**, **행위자 이벤트**, **작업 이벤트**, **사용자 지정 ETW 이벤트**를 포함한 Service Fabric 프레임워크 이벤트를 읽어옵니다. 솔루션 대시보드를 통해 Service Fabric 환경에서 주목할 만한 문제와 관련 이벤트를 볼 수 있습니다.

솔루션을 시작하려면 Service Fabric 클러스터를 Log Analytics 작업 영역에 연결해야 합니다. 세 가지 시나리오를 고려해야 합니다.

1. Service Fabric 클러스터를 배포하지 않은 경우 ***Log Analytics 작업 영역에 연결된 Service Fabric 클러스터 배포***의 단계에 따라 새 클러스터를 배포하고 Log Analytics에 보고하도록 구성합니다.
2. Service Fabric 클러스터의 보안과 같은 다른 관리 솔루션을 사용하기 위해 호스트에서 성능 카운터를 수집해야 하는 경우, ***VM 확장이 설치된 Log Analytics 작업 영역에 연결된 Service Fabric 클러스터 배포***의 단계를 따릅니다.
3. Service Fabric 클러스터를 이미 배포했고 Log Analytics에 연결하려는 경우 ***기존 저장소 계정을 Log Analytics에 추가***의 단계를 따릅니다.

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 연결된 Service Fabric 클러스터를 배포합니다.
이 템플릿은 다음을 수행합니다.

1. Log Analytics 작업 영역에 이미 연결된 Azure Service Fabric 클러스터를 배포합니다. 템플릿을 배포하는 동안 새 작업 영역을 만들 수 있는 옵션이 제공되거나 기존의 Log Analytics 작업 영역의 이름을 입력합니다.
2. 진단 저장소 계정을 Log Analytics 작업 영역에 추가합니다.
3. Log Analytics 작업 영역에서 Service Fabric 솔루션을 사용하도록 설정합니다.

[![Azure에 배포](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

위의 배포 단추를 선택하면 편집할 매개 변수가 있는 Azure Portal로 이동합니다. 새 Log Analytics 작업 영역 이름을 입력하는 경우 새 리소스 그룹을 만들어야 합니다.

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

약관에 동의하고 **만들기**를 클릭하여 배포를 시작합니다. 배포가 완료되면 새 작업 영역 및 클러스터가 생성되고 WADServiceFabric*Event, WADWindowsEventLogs 및 WADETWEvent 테이블이 추가되는 것이 보입니다.

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>VM 확장이 설치된 Log Analytics 작업 영역에 연결된 Service Fabric 클러스터를 배포합니다.

이 템플릿은 다음을 수행합니다.

1. Log Analytics 작업 영역에 이미 연결된 Azure Service Fabric 클러스터를 배포합니다. 새 작업 영역을 만들거나 기존 작업 영역을 사용할 수 있습니다.
2. 진단 저장소 계정을 Log Analytics 작업 영역에 추가합니다.
3. Log Analytics 작업 영역에서 Service Fabric 솔루션을 사용하도록 설정합니다.
4. Service Fabric 클러스터의 각 가상 머신 확장 집합에 MMA 에이전트 확장을 설치합니다. MMA 에이전트가 설치되면 노드에 대한 성능 메트릭을 볼 수 있습니다.

[![Azure에 배포](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

위와 동일한 단계에 따라 필요한 매개 변수를 입력하고 배포를 시작합니다. 다시 한 번 새 작업 영역, 클러스터 및 생성된 모든 WAD 테이블이 표시됩니다.

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>성능 데이터 보기

노드에서 성능 데이터를 보려면:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Azure 포털에서 Log Analytics 작업 영역을 시작합니다.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- 왼쪽 창의 설정으로 이동하고 데이터 >> Windows 성능 카운터 >> "Add the selected performance counters(선택한 성능 카운터 추가)": ![Service Fabric](./media/log-analytics-service-fabric/7.png)을 선택합니다.
- 로그 검색에서 다음 쿼리를 사용하여 노드에 대한 주요 메트릭을 살펴봅니다.

    a. 최근 1시간 동안 모든 노드에 대한 평균 CPU 사용률을 비교하여 어떤 노드에 문제가 있으며 노드에 급증이 나타나는 시간 간격을 확인합니다.

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    나. 다음 쿼리로 각 노드에서 사용 가능한 메모리에 대한 비슷한 꺾은선형 차트를 확인합니다.

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    각 노드에 대해 사용 가능한 정확한 평균 값(MB)을 보여 주는 모든 노드에 대한 목록을 보려면 다음 쿼리를 사용합니다.

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    다. 시간별 평균, 최소, 최대 및 75 백분위 CPU 사용량을 검사하여 특정 노드로 드릴 다운하려는 경우 다음 쿼리(컴퓨터 필드 대체)를 사용하여 수행할 수 있습니다.

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

[Operations Management Suite 블로그](https://blogs.technet.microsoft.com/msoms/tag/metrics/)의 Log Analytics에서 성능 메트릭에 대한 자세한 정보를 참조하세요.


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>기존 저장소 계정을 Log Analytics에 추가

이 템플릿에서는 단순히 기존 저장소 계정을 신규 또는 기존 Log Analytics 작업 영역에 추가합니다.

[![Azure에 배포](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> 리소스 그룹 선택 시, 기존의 Log Analytics 작업 영역으로 작업 중인 경우 “기존 사용”을 선택하고 Log Analytics 작업 영역을 포함하는 리소스 그룹을 검색합니다. 그렇지 않은 경우 새 작업 영역을 만듭니다.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

이 템플릿을 배포하면 Log Analytics 작업 영역에 연결된 저장소 계정을 볼 수 있습니다. 이 인스턴스에서는 위에서 만든 Exchange 작업 영역에 하나 이상의 저장소 계정을 추가했습니다.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Service Fabric 이벤트 보기

배포를 완료하고 작업 영역에서 Service Fabric 솔루션을 사용하도록 설정했으면 Log Analytics 포털에서 **Service Fabric** 타일을 선택하여 Service Fabric 대시보드를 실행합니다. 대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 10개의 이벤트를 나열합니다. 각 열의 오른쪽 아래쪽에 있는 **모두 보기**를 클릭하거나 열 제목을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| **Service Fabric 이벤트** | **description** |
| --- | --- |
| 주목할 만한 문제 |RunAsyncFailures RunAsynCancellations 및 노드 다운 같은 문제를 표시합니다. |
| 작업 이벤트 |응용 프로그램 업그레이드 및 배포와 같은 주목할 만한 작업 이벤트입니다. |
| Reliable Service 이벤트 |Runasyncinvocations와 같은 주목할 만한 Reliable Service 이벤트입니다. |
| 행위자 이벤트 |행위자 메서드, 행위자 활성화 및 비활성화 등에 의해 throw된 예외와 같은 마이크로 서비스에 의해 생성된 주목할 만한 행위자 이벤트입니다. |
| 응용 프로그램 이벤트 |응용 프로그램으로 생성된 모든 사용자 지정 ETW 이벤트입니다. |

![Service Fabric 대시보드](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric 대시보드](./media/log-analytics-service-fabric/sf4.png)

다음 표에서는 데이터 수집 방법 및 Service Fabric에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | Operations Manager 에이전트 | Azure Storage | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10분 |

> [!NOTE]
> 대시보드 맨 위에서 **Data based on last 7 days(최근 7일에 따른 데이터)**를 클릭하여 Service Fabric 솔루션에서 이러한 이벤트 범위를 변경할 수 있습니다. 최근 7일, 1일 또는 6시간 내에 생성된 이벤트를 보여 줄 수 있습니다. 또는 **사용자 지정**을 선택하고 사용자 지정 날짜 범위를 지정할 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Service Fabric 이벤트 데이터를 볼 수 있습니다.
