---
title: "Azure Monitor 메트릭 탐색기 | Microsoft Docs"
description: "Azure Monitor 메트릭 탐색기의 새로운 기능 알아보기"
author: vgorbenko
manager: Victor.Mushkatin
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.openlocfilehash: 537dd6d64fe49093dd73d8040cde5a9153a7bd5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor 메트릭 탐색기

이 방법에서는 현재 공개 미리 보기로 제공되는 차세대 Azure Monitor 메트릭 차트 환경을 설명합니다. 새로운 환경에서는 다차원 메트릭 및 차원이 없는 기본 메트릭 모두에 대해 렌더링 차트를 지원합니다. 여러 리소스 종류, 다중 리소스 그룹 및 구독에서 메트릭을 오버레이한 차트를 그릴 수 있습니다. 차원 필터를 적용하고 그룹화하여 다차원 메트릭 차트를 사용자 지정할 수 있습니다. 사용자 지정된 차트를 포함하여 모든 차트를 대시보드에 고정할 수 있습니다.

차원이 없는 기본 메트릭만을 지원하는 이전 환경에 대한 정보를 찾고 경우 [Microsoft Azure 메트릭 개요 가이드](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)에서 "포털을 통해 메트릭에 액세스"라는 섹션을 참조하세요.

## <a name="what-is-azure-monitor-metrics-explorer"></a>Azure Monitor 메트릭 탐색기란?

Azure Monitor 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭 값에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기는 Azure에서 호스트되거나 Azure Monitor 서비스에서 모니터링되는 응용 프로그램 및 인프라와 관련된 다양한 성능 및 가용성 문제를 조사하는 데 필수적인 시작점입니다. 

## <a name="what-are-metrics-in-azure"></a>Azure에서 메트릭이란?

Microsoft Azure의 메트릭은 지남에 따라 수집되고 저장된 일련의 측정된 값과 수입니다. 표준(또는 "플랫폼") 메트릭 및 사용자 지정 메트릭이 있습니다. 표준 메트릭은 Azure 플랫폼 자체에서 제공됩니다. 표준 메트릭은 Azure 리소스의 상태 및 사용량 통계를 반영합니다. 사용자 지정 메트릭은 [사용자 지정 이벤트의 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)를 사용하여 응용 프로그램에서 Azure로 전송되는 반면 사용자 지정 메트릭은 다른 응용 프로그램 특정 메트릭과 함께 Application Insights 리소스에 저장됩니다.

## <a name="what-are-multi-dimensional-metrics"></a>다차원 메트릭이란?

이제 대부분의 Azure 리소스는 다차원 메트릭을 노출합니다. 이러한 메트릭은 하나 이상의 명명된 차원에 대한 여러 일련의 값을 추적합니다. 예를 들어 "사용 가능한 디스크 공간" 메트릭에는 "C:", "D:" 값을 가진 "드라이브"라는 차원이 있을 수 있습니다. 이 값을 사용하면 모든 드라이브 또는 각 드라이브에 개별적으로 사용 가능한 디스크 공간을 볼 수 있습니다. 

아래 예제에서는 "네트워크 처리량"이라는 가상의 메트릭에 대한 두 개의 데이터 집합을 보여줍니다. 첫 번째 데이터 집합에는 차원이 없습니다. 두 번째 데이터 집합에서는 "IP 주소"와 "방향"을 사용이라는 두 개의 차원을 포함한 값을 보여줍니다.

### <a name="network-throughput"></a>네트워크 처리량
(이 메트릭에는 차원이 없습니다.)

 |Timestamp        | 메트릭 값 | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8Kbps | 
   | 8/9/2017 8:15 | 1,141.4Kbps |
   | 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| Timestamp          | 차원 "IP" | 차원 "방향" | 메트릭 값| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP="192.168.5.2" | 방향="전송"    | 646.5Kbps |
   | 8/9/2017 8:14 | IP="192.168.5.2" | 방향="수신" | 420.1Kbps |
   | 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="전송"    | 150.0Kbps | 
   | 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="수신" | 115.2Kbps |
   | 8/9/2017 8:15 | IP="192.168.5.2" | 방향="전송"    | 515.2Kbps |
   | 8/9/2017 8:15 | IP="192.168.5.2" | 방향="수신" | 371.1Kbps |
   | 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="전송"    | 155.0Kbps |
   | 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="수신" | 100.1Kbps |

이 메트릭은 "각 IP 주소에 대한 네트워크 처리량은 무엇이었나요?", "전송된 데이터 및 수신된 데이터의 양은 얼마인가요?"와 같은 질문에 대답할 수 있습니다. 다차원 메트릭은 비차원 메트릭에 대해 추가 분석 및 진단 값을 가집니다. 

## <a name="how-do-i-create-a-new-chart"></a>새 차트를 만들려면 어떻게 할까요?

   > [!NOTE]
   > 이전 메트릭 환경의 일부 기능은 새 메트릭 탐색기에서 아직 사용할 수 없습니다. 새로운 환경이 미리 보기로 제공되는 반면 Azure Monitor의 이전(비차원) 메트릭 보기를 계속 사용할 수 있습니다. 

1. Azure 포털 열기
2. 새 **모니터** 탭으로 이동하고 **메트릭(미리 보기)**을 선택합니다.

   ![메트릭 미리 보기 이미지](./media/monitoring-metric-charts/001.png)

3. **메트릭 선택기**가 자동으로 열립니다. 해당 관련 메트릭을 보려면 목록에서 리소스를 선택합니다. 메트릭을 포함한 리소스만이 목록에 표시됩니다.

   ![메트릭 미리 보기 이미지](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Azure 구독이 여러 개 있는 경우 메트릭 탐색기는 포털 설정 -> 구독 목록별 필터에서 선택된 모든 구독에 걸쳐 리소스를 끌어옵니다. 이를 변경하려면 화면 맨 위에서 포털 설정 기어 아이콘을 클릭하고 사용하려는 구독을 선택합니다.

4. 메트릭 선택하기 전에 일부 리소스 형식(예: 저장소 계정 및 가상 컴퓨터)에서 **하위 서비스**를 선택해야 합니다. 각 하위 서비스는 다른 하위 서비스가 아닌 해당 하위 서비스와 관련된 고유한 메트릭 집합을 가집니다.

   예를 들어 각 Azure Storage에는 저장소 계정의 모든 부분인 하위 서비스 "Blob", "파일", "큐" 및 "테이블"에 대한 메트릭이 있습니다. 그러나 "큐 메시지 수" 메트릭은 다른 저장소 계정 하위 서비스가 아닌 "큐" 하위 서비스에 자연스럽 게 적용됩니다.

   ![메트릭 미리 보기 이미지](./media/monitoring-metric-charts/003.png)

5. 목록에서 메트릭을 선택합니다. 원하는 메트릭의 이름을 일부 알고 있는 경우 입력하여 사용 가능한 메트릭의 필터링된 목록을 볼 수 있습니다.

   ![메트릭 미리 보기 이미지](./media/monitoring-metric-charts/004.png)

6. 메트릭을 선택한 후에 차트는 선택한 메트릭에 대한 기본 집계로 렌더링됩니다. 이 시점에서 **메트릭 선택기**를 닫으려면 바깥쪽을 클릭하면 됩니다. 또한 선택적으로 다른 집계로 차트를 전환할 수 있습니다. 일부 메트릭의 경우 집계를 전환하면 차트에 표시하려는 값을 선택할 수 있습니다. 예를 들어 평균, 최소 및 최대 값을 전환할 수 있습니다. 

7. 메트릭 추가 아이콘을 클릭하고 ![메트릭 아이콘](./media/monitoring-metric-charts/icon001.png) 3~6단계를 반복하여 동일한 차트에서 메트릭을 추가할 수 있습니다.

   > [!NOTE]
   > 일반적으로 하나의 차트에서 다른 단위의 측정값(예: "밀리초" 및 "킬로바이트") 또는 크게 다른 규모를 갖는 메트릭을 사용하지 않습니다. 대신, 여러 차트를 사용하는 것이 좋습니다. 차트 추가 단추를 클릭하여 메트릭 탐색기에서 여러 개의 차트를 만듭니다.

## <a name="how-do-i-apply-filters-to-the-charts"></a>차트에 필터를 적용하려면 어떻게 할까요?

차원을 포함한 메트릭을 보여주는 차트에 필터를 적용할 수 있습니다. 예를 들어 "트랜잭션 수" 메트릭에 차원이 있는 경우 트랜잭션으로부터의 응답 성공 여부를 나타내고 이 차원을 필터링하는 "응답 형식"은 성공적인(또는 실패한) 트랜잭션에 대한 차트 줄을 그립니다. 

### <a name="to-add-a-filter"></a>필터를 추가하려면:

1. 필터 추가 아이콘을 클릭합니다. ![필터 아이콘](./media/monitoring-metric-charts/icon002.png) 차트 위에서

2. 필터링하려는 차원(속성)을 선택합니다.

   ![메트릭 이미지](./media/monitoring-metric-charts/006.png)

3. 차트를 그릴 때 포함하려는 차원 값을 선택합니다(이 예제에서는 성공적인 저장소 트랜잭션을 필터링하도록 표시함).

   ![메트릭 이미지](./media/monitoring-metric-charts/007.png)

4. 필터 값을 선택한 후에 필터 선택기를 닫으려면 바깥쪽을 클릭합니다. 이제 차트에서는 실패한 저장소 트랜잭션 수를 보여줍니다.

   ![메트릭 이미지](./media/monitoring-metric-charts/008.png)

5. 동일한 차트에 여러 필터를 적용하려면 1~4단계를 반복하면 됩니다.

## <a name="how-do-i-segment-a-chart"></a>차트를 분할하려면 어떻게 할까요?

차원별로 메트릭 분할하여 메트릭의 다른 세그먼트가 서로 비교되는 방법을 시각화하고 차원의 외부 세그먼트를 식별할 수 있습니다. 

### <a name="to-segment-a-chart"></a>차트를 분할하려면:

1. 그룹화 추가 아이콘을 클릭합니다.  ![메트릭 이미지](./media/monitoring-metric-charts/icon003.png) 차트 위에서
 
   > [!NOTE]
   > 단일 차트에 여러 개의 필터가 있을 수 있지만 그룹화는 하나만이 가능합니다.

2. 차트를 분할하려는 차원을 선택합니다. 

   ![메트릭 이미지](./media/monitoring-metric-charts/010.png)

   이제 차트에서는 차원의 각 세그먼트에 하나씩 여러 줄을 표시합니다.

   ![메트릭 이미지](./media/monitoring-metric-charts/012.png)

3. **그룹화 선택기**를 닫으려면 바깥쪽을 클릭합니다.

   > [!NOTE]
   > 동일한 차원에서 필터링 및 그룹화를 모두 사용하여 시나리오에 관련이 없는 세그먼트를 숨기고 차트를 더 쉽게 읽을 수 있습니다.

## <a name="how-do-i-pin-charts-to-dashboards"></a>대시보드에 차트를 고정하려면 어떻게 할까요?

차트를 구성한 후에 다른 모니터링 원격 분석의 컨텍스트에서 다시 보거나 팀과 공유할 수 있도록 대시보드에 추가하려고 합니다. 

구성된 차트를 대시보드에 고정하려면:

차트를 구성한 후에 차트의 오른쪽의 위 모서리에서 **차트 작업** 메뉴를 클릭하고 **대시보드에 고정**을 클릭합니다.

   ![메트릭 이미지](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>다음 단계

  메트릭을 사용하여 조치 가능한 대시보드를 만드는 모범 사례에 대한 자세한 내용은 [사용자 지정 KPI 대시보드 만들기](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)를 참고하세요.