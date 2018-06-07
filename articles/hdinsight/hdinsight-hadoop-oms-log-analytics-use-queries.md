---
title: Azure Log Analytics를 쿼리하여 Azure HDInsight 클러스터 모니터링 | Microsoft Docs
description: Azure Log Analytics에서 쿼리를 실행하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 61467d702f3123085fd7e067a8d56c30331c5bc6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics를 쿼리하여 HDInsight 클러스터 모니터링

Azure HDInsight 클러스터에서 Azure Log Analytics를 사용하는 방법에 대한 몇 가지 기본적인 시나리오를 살펴봅니다.

* [HDInsight 클러스터 메트릭 분석](#analyze-hdinsight-cluster-metrics)
* [특정 로그 메시지 검색](#search-for-specific-log-messages)
* [이벤트 경고 만들기](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터에서 Azure Log Analytics를 사용하도록 구성되어 있어야 합니다. 지침은 [HDInsight 클러스터에서 Azure Log Analytics 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조하세요.

* [Log Analytics에 HDInsight 클러스터 관리 솔루션 추가](hdinsight-hadoop-oms-log-analytics-management-solutions.md)에서 설명한 대로 [Log Analytics](../operations-management-suite/operations-management-suite-overview.md) 작업 영역에 HDInsight 클러스터와 관련된 관리 솔루션이 추가되어 있어야 합니다.

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight 클러스터 메트릭 분석

HDInsight 클러스터에 대한 특정 메트릭을 조회하는 방법을 알아봅니다.

1. Azure Portal에서 Azure Log Analytics에 연결한 HDInsight 클러스터를 엽니다.
2. **모니터링**을 클릭한 다음 **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. 왼쪽 메뉴에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. Azure Log Analytics를 사용하도록 구성된 모든 HDInsight 클러스터에 대해 모든 메트릭을 검색하려면 검색 상자에 다음 쿼리를 입력한 다음 **ENTER**를 누릅니다.

        `search *` 

    ![모든 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "모든 메트릭 검색")

    출력은 다음과 유사합니다.

    ![모든 메트릭 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "모든 메트릭 검색 출력")

5. 왼쪽 창의 **형식**에서 심층적으로 분석하려는 메트릭을 선택하고 **적용**을 클릭합니다. 다음 스크린샷은 `metrics_resourcemanager_queue_root_default_CL` 형식을 선택한 것입니다. 

    > [!NOTE]
    > **[+]자세히** 단추를 클릭하여 원하는 메트릭을 찾을 수도 있습니다. 또한 **적용** 단추는 목록의 아래쪽에 있으므로 스크롤해야 볼 수 있습니다.
    > 
    >    

    텍스트 상자의 쿼리가 다음 스크린샷에서 강조 표시된 상자에 표시된 내용으로 변경됩니다.

    ![특정 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "특정 메트릭 검색")

6. 이 특정 메트릭을 자세히 분석할 수 있습니다. 예를 들어 다음 쿼리를 사용하여 클러스터 이름별로 분류되고 10분 간격으로 사용된 리소스의 평균에 기반한 기존 출력을 구체화할 수 있습니다.

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 사용된 리소스의 평균에 따라 구체화하는 대신 다음 쿼리를 사용하여 10분 간격으로 최대 리소스가 사용된 시점(90번째 및 95번째 백분위수)에 따라 결과를 구체화할 수 있습니다.

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>특정 로그 메시지 검색

특정 시간 범위 중의 오류 메시지를 검색하는 방법을 알아봅니다. 다음에 나오는 단계는 관심 있는 오류 메시지에 도달할 수 있는 방법에 대한 하나의 예입니다. 찾으려는 오류를 확인할 수 있는 속성을 사용하면 됩니다.

1. Azure Portal에서 Azure Log Analytics에 연결한 HDInsight 클러스터를 엽니다.
2. **모니터링**을 클릭하고 **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. OMS 포털의 홈 화면에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. Azure Log Analytics를 사용하도록 구성된 모든 HDInsight 클러스터의 모든 오류 메시지를 검색하려면 검색 상자에 다음 쿼리를 입력한 다음 **ENTER**를 누릅니다. 

         search "Error"

    다음 출력과 유사한 결과가 표시됩니다.

    ![모든 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "모든 오류 검색 출력")

5. 왼쪽 창의 **형식** 범주 아래에서 심층적으로 분석하려는 오류 형식을 선택하고 **적용**을 클릭합니다.  결과가 사용자가 선택한 오류 형식만 표시하도록 구체화된 것을 볼 수 있습니다.
7. 왼쪽 창에서 사용할 수 있는 옵션을 사용하여 이 특정 오류 목록을 자세히 분석할 수 있습니다. 예를 들면 다음과 같습니다. 

    - 특정 작업자 노드의 오류 메시지 보기

        ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "특정 오류 검색 출력")

    - 특정 시간에 발생한 오류 보기

        ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "특정 오류 검색 출력")

9. 특정 오류 보기 **[+]자세히 표시**를 클릭하면 실제 오류 메시지를 볼 수 있습니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "특정 오류 검색 출력")

## <a name="create-alerts-for-tracking-events"></a>추적 이벤트에 대한 경고 만들기

경고를 만드는 첫 번째 단계는 경고를 트리거하는 쿼리에 도달하는 것입니다. 간단히 하기 위해 HDInsight 클러스터에서 실행되는 실패한 응용 프로그램 목록을 제공하는 다음 쿼리를 사용하겠습니다.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

경고를 만들려는 모든 쿼리를 사용할 수 있습니다.

1. Azure Portal에서 Azure Log Analytics에 연결한 HDInsight 클러스터를 엽니다.
2. **모니터링**을 클릭한 다음 **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. OMS 포털의 홈 화면에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. 경고를 만들려는 항목에 대해 다음 쿼리를 실행한 다음 **ENTER**를 누릅니다.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. 페이지 위쪽에서 **경고**를 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "쿼리를 입력하여 경고 만들기")

4. **경고 규칙 추가** 창에서 쿼리 및 기타 세부 정보를 입력하여 경고를 만든 다음 **저장**을 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "쿼리를 입력하여 경고 만들기")

    스크린샷은 경고 쿼리가 출력을 반환하면 메일 알림을 보내는 구성을 보여 줍니다.

5. 기존 경고를 편집하거나 삭제할 수도 있습니다. 이렇게 하려면 OMS 포털의 모든 페이지에서 **설정** 아이콘을 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "쿼리를 입력하여 경고 만들기")

6. **설정** 페이지에서 **경고**를 클릭하여 만든 경고를 확인합니다. 경고를 사용하거나 사용하지 않도록 설정하거나, 편집하거나, 삭제할 수도 있습니다. 자세한 내용은 [Log Analytics에서 경고 규칙 작업](../log-analytics/log-analytics-alerts-creating.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

* [Log Analytics 작업](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics에서 경고 규칙 만들기](../log-analytics/log-analytics-alerts-creating.md)
