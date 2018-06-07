---
title: Azure Monitor를 사용하여 경고 만들기, 보기 및 관리 | Microsoft Docs
description: 통합된 새로운 Azure 경고 환경을 사용하여 한 곳에서 메트릭 및 로그 경고 규칙을 작성하고, 보고, 관리합니다.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: fdb3ebe3820191a642c4503851b04dd5fc5e6048
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 경고 만들기, 보기 및 관리  

## <a name="overview"></a>개요
이 문서에서는 Azure Portal 내 새 경고 인터페이스를 사용하여 경고를 설정하는 방법을 보여줍니다. 경고 규칙의 정의는 세 부분으로 이루어져 있습니다.
- 대상: 모니터링될 특정 Azure 리소스
- 조건: 신호에서 표시될 때 작업을 트리거해야 하는 특정 조건 또는 논리
- 작업: 알림의 받는 사람에게 보내는 특정 호출 - 이메일, SMS, 웹후크 등

Azure 경고는 또한 모든 경고 규칙에 대한 통합된 보기 및 확인되지 않은 경고 보기를 포함하여 단일 위치에서 관리하는 기능을 제공합니다. [Azure 경고 - 개요](monitoring-overview-unified-alerts.md)에서 기능에 대해 자세히 알아봅니다.

경고는 용어 **로그 경고**를 사용하여 신호가 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-analytics.md)를 기준으로 하는 사용자 지정 쿼리인 경고를 설명합니다. [새로운 메트릭 경고 기능](monitoring-near-real-time-metric-alerts.md)은 특정 Azure 리소스에 대한 [다차원 메트릭](monitoring-metric-charts.md)에 대해 경고하는 기능을 제공합니다. 이러한 리소스에 대한 경고는 **다차원 메트릭 경고**를 만드는 차원에서 추가 필터를 사용할 수 있습니다.


> [!NOTE]
> 반면 Azure 경고는 Azure에서 경고를 만들기 위한 새로운 향상된 환경을 제공합니다. 기존 [경고(클래식)](monitoring-overview-alerts.md) 환경은 계속 사용할 수 있습니다.
>

Azure 경고를 사용하는 방법에 대한 단계별 가이드가 다음에 자세히 설명됩니다.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Azure Portal에서 경고 규칙 만들기
1. [포털](https://portal.azure.com/)에서 **모니터**를 선택하고 모니터 섹션 아래에서 **경고**를 선택합니다.  
    ![모니터링](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. **새 경고 규칙** 단추를 선택하여 Azure에서 새 경고를 만듭니다.
    ![경고 추가](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. 경고 만들기 섹션은 *경고 조건 정의*, *경고 세부 정보 정의* 및 *작업 그룹 정의*로 구성된 세 부분으로 표시됩니다.

    ![규칙 만들기](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  **리소스 선택** 링크를 사용하고 리소스를 선택하여 대상을 지정하여 경고 조건을 정의합니다. *구독*, *리소스 종류*를 선택하고 마지막으로 필요한 *리소스*를 선택하여 필터링합니다.

    >[!NOTE]

    > 진행하기 전에 선택한 리소스에 대해 사용 가능한 신호를 확인합니다.

    ![리소스 선택](./media/monitor-alerts-unified/Alert-SelectResource.png)

 사용자 인터페이스를 통해 한 곳에서 다양한 유형의 경고를 만들 수 있습니다. 원하는 경고의 유형에 따라 다음 단계를 선택합니다.

    - **메트릭 경고**의 경우: 5-7단계를 따르고, 11단계로 직접 이동
    - **로그 경고**의 경우 8단계로 이동합니다.

    > [!NOTE]

    > 활동 로그 경고도 지원되지만 미리 보기에 있습니다. [자세히 알아보기](monitoring-activity-log-alerts-new-experience.md).

5. *메트릭 경고*: **리소스 종류**가 **메트릭**과 같은 신호 유형으로 선택되었는지 확인한 다음, 적절한 **리소스**가 선택되면 *완료* 단추를 클릭하여 [경고 만들기]로 돌아갑니다. 다음으로 **조건 추가** 단추를 사용하여 신호 옵션 목록, 해당 모니터링 서비스 및 나열된 형식에서 특정 신호를 선택합니다. 이전에 선택한 리소스에 사용할 수 있습니다.

    ![리소스 선택](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  모든 [거의 실시간 경고](monitoring-near-real-time-metric-alerts.md)에 사용 가능 리소스는 모니터 서비스 **플랫폼** 및 신호 유형과 함께 **메트릭**으로 나열됩니다.

6. *메트릭 경고*: 신호를 선택하면 경고에 대한 논리를 정의할 수 있습니다. 참조용으로 신호의 기록 데이터는 마지막 6시간에서 지난 주까지 다양한 **기록 표시**를 사용하여 시간 창을 조정하는 옵션과 함께 표시됩니다. 준비된 시각화를 사용하여 조건, 집계 및 마지막으로 임계값의 표시된 옵션에서 **경고 논리**를 선택할 수 있습니다. 제공된 논리의 미리 보기로 조건은 경고가 트리거된 경우를 나타내는 신호 기록과 함께 시각화에 표시됩니다. 마지막으로 **기간** 옵션에서 선택하여 경고가 지정된 조건을 찾아야 하는 기간과 **빈도**를 선택하여 경고가 실행되어야 하는 빈도를 지정합니다.

    ![메트릭에 대한 신호 논리 구성](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *메트릭 경고*: 신호가 메트릭인 경우 경고 창은 여러 데이터 요소 또는 언급된 Azure 리소스에 대한 차원을 사용하여 필터링될 수 있습니다. 

    a. **기록 표시** 드롭다운에서 기간을 선택하여 다른 기간을 시각화합니다. 지원되는 메트릭에 대한 차원을 선택하여 시계열에 대해 필터링할 수 있습니다. 차원 선택은 선택 사항이며 최대 5개의 차원을 사용할 수 있습니다. 

    나. *조건*, *집계* 및 *임계값*의 표시된 옵션에서 **경고 논리**를 선택할 수 있습니다. 제공된 논리의 미리 보기로 조건은 과거에 경고가 트리거되었던 경우를 나타내는 신호 기록과 함께 시각화에 표시됩니다. 

    다. 기간을 지정하려면 **빈도**를 선택하여 경고가 실행되어야 하는 주기와 함께 **기간**을 선택합니다.

    ![다차원 메트릭에 대한 신호 논리 구성](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *로그 경고*: **리소스 종류**가 *Log Analytics* 또는 *Application Insights*와 같은 분석 원본이고 신호 유형이 **로그**인지 확인한 다음, 적절한 **리소스가** 선택되면 *완료*를 클릭합니다. 다음으로 **조건 추가** 단추를 사용하여 리소스 및 *Log Analytics* 또는 *Application Insights*와 같은 선택된 로그 모니터 서비스에 대한 신호 목록 **사용자 지정 로그 검색** 옵션에서 사용할 수 있는 신호 옵션 목록을 봅니다.

   ![리소스 선택 - 사용자 지정 로그 검색](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > 경고 목록은 위 그림과 같이 분석 쿼리를 신호 유형 - **로그(저장된 쿼리)**로 가져올 수 있습니다. 따라서 사용자는 Analytics에서 쿼리를 완료한 후 경고에서 나중에 사용할 수 있게 저장합니다. 쿼리 저장 방법에 대한 자세한 내용은 [Log Analytics에서 로그 검색 사용](../log-analytics/log-analytics-log-searches.md) 또는 [Application Insights 분석의 공유 쿼리](../log-analytics/log-analytics-overview.md)를 참조하세요. 

9.  *로그 경고*: 선택한 후에 경고에 대한 쿼리를 **검색 쿼리** 필드에서 정의할 수 있습니다. 쿼리 구문이 올바르지 않을 경우 필드는 빨간색으로 오류를 표시합니다. 쿼리 구문이 올바른 경우 참조를 위해 정의된 쿼리의 기록 데이터가 마지막 6시간에서 지난 주까지 시간 창을 조정하는 옵션과 함께 그래프로 표시됩니다.

 ![경고 규칙 구성](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > 쿼리 결과에 시간 세부 정보가 있는 경우 기록 데이터 시각화만 표시될 수 있습니다. 쿼리 결과가 요약된 데이터 또는 특정 열 값인 경우 단일 플롯으로 동일하게 표시됩니다.

    >  Application insights를 사용하는 로그 경고의 메트릭 측정 유형의 경우 아래에 설명된 대로 **집계** 옵션을 사용하여 데이터를 그룹화하는 특정 변수를 지정할 수 있습니다.

    ![집계 옵션](./media/monitor-alerts-unified/aggregate-on.png)

10.  *로그 경고*: 준비된 시각화를 사용하여 조건, 집계 및 마지막으로 임계값의 표시된 옵션에서 **경고 논리**를 선택할 수 있습니다. 마지막으로 논리에서 **기간** 옵션을 사용하여 지정된 조건에 대해 평가할 시간을 지정합니다. **빈도**를 선택하여 경고를 실행해야 하는 빈도를 지정합니다.
**로그 경고**의 경우 경고는 다음을 기반으로 할 수 있습니다.
   - *레코드 수*: 쿼리에서 반환된 레코드의 개수가 제공된 값보다 큰 또는 값보다 작은 경우 경고가 생성됩니다.
   - *메트릭 측정*: 결과에서 각 *집계 값*이 제공된 임계값을 초과하고 선택한 값*으로 그룹화*되는 경우 경고가 생성됩니다. 경고에 대한 위반 수는 임계값이 선택한 기간에서 초과된 횟수입니다. 결과 집합에서 모든 위반의 조합에 대해 총 위반을 지정하거나 연속 위반을 지정하여 연속된 샘플에서 위반이 발생해야 한다고 요구할 수 있습니다. [로그 경고 및 종류](monitor-alerts-unified-log.md)에 대해 자세히 알아봅니다.

    > [!TIP]
    > 현재 경고 - 로그 검색에서 경고는 사용자 지정 *기간* 및 *빈도* 값(분)을 사용할 수 있습니다. 값은 5분에서 1440분 (즉) 24시간까지 달라질 수 있습니다. 따라서 예를 들어 경고 기간을 3시간으로 원하는 경우 사용하기 전에 180분으로 변환합니다.

11. 두 번째 단계로 제공된 옵션에서 경고 및 **심각도**에 대한 세부 사항을 자세히 설명하는 **설명**과 함께 **경고 규칙 이름** 필드에서 경고에 대한 이름을 정의합니다. 이러한 세부 정보는 모든 경고 이메일, 알림 또는 Azure Monitor에 의해 수행되는 푸시에서 다시 사용됩니다. 또한 사용자는 **규칙을 만들면 바로 사용** 옵션을 적절히 설정/해제하여 생성 시 경고 규칙을 즉시 활성화하도록 선택할 수 있습니다.

    **로그 경고**의 경우 몇 가지 추가 기능을 경고 정보에 사용할 수 있습니다.

    - **경고 표시 안 함**: 경고 규칙 표시 안 함 기능을 켜면 규칙에 대한 작업이 새 경고를 만든 후 정의된 기간 동안 비활성화됩니다. 규칙은 여전히 실행되고 있으며 조건을 만족하면 경고 레코드를 생성합니다. 중복 작업을 실행하지 않고 문제를 해결할 시간 여유를 갖게 됩니다.

        ![로그 경고에 대한 경고 표시 안 함](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. 세 번째이자 마지막 단계로 경고 조건이 충족될 때 경고 규칙에 대해 **작업 그룹**을 트리거해야 하는지를 지정합니다. 경고와 함께 모든 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들 수 있습니다. 선택한 작업 그룹에 따라 경고가 트리거될 때 Azure는 이메일을 보내고, SMS를 보내고, 웹후크를 호출하고, Azure Runbook을 사용하여 수정하고, ITSM 도구에 푸시하는 등의 작업을 수행합니다. [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보세요.

    **로그 경고**의 경우 기본 작업을 재정의하는 데 몇 가지 추가 기능을 사용할 수 있습니다.

    - **이메일 알림**: 언급된 작업 그룹에 하나 이상의 이메일 작업이 있는 경우 작업 그룹을 통해 전송된 이메일의 *이메일 제목*을 재정의합니다. 메일 본문은 수정할 수 없으며, 이 필드는 이메일 주소에 대한 필드가 **아닙니다**.
    - **사용자 지정 Json 페이로드 포함**: 언급된 작업 그룹에 하나 이상의 웹후크 작업이 있는 경우 작업 그룹에서 사용하는 웹후크 JSON을 재정의합니다. 사용자는 관련된 작업 그룹에 구성된 모든 웹후크에 사용할 JSON 형식을 지정할 수 있습니다. 웹후크 형식에 대한 자세한 내용은 [로그 경고에 대한 웹후크 작업](monitor-alerts-unified-log-webhook.md)을 참조하세요. 웹후크 테스트 옵션은 샘플 JSON을 사용하여 대상별로 형식 및 처리를 확인하고, 레이블이 지정된 이 옵션은 **테스트** 용도로만 의미가 있습니다.

        ![경고 로그에 대한 작업 재정의](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. 모든 필드가 유효하고 녹색 틱인 경우 **경고 규칙 만들기** 단추를 클릭할 수 있으며 Azure Monitor - 경고에서 경고가 생성됩니다. 경고 대시보드에서 모든 경고를 볼 수 있습니다.

    ![규칙 만들기](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="view-your-alerts-in-azure-portal"></a>Azure Portal에서 경고 보기

1. [포털](https://portal.azure.com/)에서 **모니터**를 선택하고 모니터 섹션 아래에서 **경고**를 선택합니다.  

2. **경고 대시보드**가 표시됩니다. 여기에서 모든 Azure Alerts가 통합되며 단일 보드 ![경고 대시보드](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)에 표시됩니다.
3. 상단 왼쪽에서 오른쪽으로 대시보드는 나열된 세부 내용을 보기 위해 클릭할 수 있는 다음을 한눈에 보여 줍니다.
    - *발생한 경고*: 현재 논리를 충족하고 발생한 상태에 있는 경고의 수
    - *총 경고 규칙*: 생성된 하위 텍스트에 있는 경고 규칙의 수, 현재 활성화된 수
4. 사용자가 클릭하여 세부 정보를 볼 수 있는 모든 발생한 경고의 목록이 표시됩니다.
5. 경고를 구체적으로 명시하는 데 도움이 되도록 특정 *구독, 리소스 그룹 및/또는 리소스*를 필터링하기 위해 맨 위에서 드롭다운 옵션을 사용할 수 있습니다. 해결되지 않은 경고의 경우 *필터 경고* 옵션을 사용하여 제공된 키워드 - *이름, 경고 조건, 리소스 그룹 및 대상 리소스*와 일치하는 특정 경고를 찾을 수 있습니다.

## <a name="managing-your-alerts-in-azure-portal"></a>Azure Portal에서 경고 관리
1. [포털](https://portal.azure.com/)에서 **모니터**를 선택하고 모니터 섹션 아래에서 **경고**를 선택합니다.  
2. 위쪽 표시줄에서 **규칙 관리** 단추를 선택하여 규칙 관리 섹션으로 이동합니다. 여기에 비활성화된 경고를 포함한 만들어진 모든 경고 규칙이 나열됩니다.
3. 특정 경고 규칙을 찾기 위해 맨 위에서 드롭다운 필터를 사용할 수 있습니다. 이는 특정 *구독, 리소스 그룹 및/또는 리소스*에 대한 경고 규칙을 최종 목록에 넣도록 허용합니다. 또는 *경고 필터링*을 표시한 경고 규칙 목록 위의 검색 창 사용에 대해 일치하는 규칙만을 표시하도록 *경고 이름, 조건 및 대상 리소스*에 대해 일치되는 키워드를 제공할 수 있습니다.
   ![경고 관리 규칙](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. 특정 경고 규칙을 보거나 수정하려면 클릭 가능한 링크로 표시하는 해당 이름을 클릭합니다.
5. 1) 경고 조건 2) 경고 세부 정보 3) 작업 그룹의 세 단계 구조에서 정의된 경고가 표시됩니다. **대상 조건**을 클릭하여 경고 논리를 수정하거나 이전 논리를 삭제하는 데 휴지통 아이콘을 사용한 후 새 조건을 추가할 수 있습니다. 유사하게 경고 세부 정보 섹션에서 **설명** 및 **심각도**를 수정할 수 있습니다. **새 작업 그룹** 단추를 사용하는 경고에 대한 연결로 작업 그룹을 변경하거나 새 작업 그룹을 만들 수 있습니다.

   ![경고 규칙 수정](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. 상단 패널을 사용하여 경고에 대해 수행된 모든 변경 내용을 커밋하도록 **저장**, 경고에 대해 수행한 변경 내용을 커밋하지 않고 돌아가도록 **취소**, 경고를 비활성화하도록 **비활성화**(이 후 모든 작업을 실행하거나 트리거하지 않음)를 포함하여 경고에 대한 변경 내용을 반영할 수 있습니다. 마지막으로 Azure에서 전체 경고 규칙을 영구적으로 제거하도록 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

- 새 [근 실시간 메트릭 경고](monitoring-near-real-time-metric-alerts.md)에 대해 자세히 알아봅니다.
- 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
- [Azure Alerts의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아보기
- [경고(미리 보기) 환경에서 활동 로그 경고에 대해 알아보기](monitoring-activity-log-alerts-new-experience.md)
