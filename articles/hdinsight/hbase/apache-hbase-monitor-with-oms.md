---
title: Azure Log Analytics를 사용하여 HBase 모니터링 - Azure HDInsight | Microsoft Docs
description: Azure Log Analytics를 사용하여 HDInsight HBase 클러스터를 모니터링합니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-hbase-with-log-analytics"></a>Log Analytics를 사용하여 HBase 모니터링

HDInsight HBase 모니터링은 Azure Log Analytics를 사용하여 HDInsight 클러스터 노드에서 HDInsight HBase 성능 메트릭을 수집합니다. [모니터]는 HBase 관련 시각화 및 대시보드, 메트릭을 검색하는 도구 및 사용자 지정 모니터링 규칙과 경고를 만드는 기능을 제공합니다. 여러 Azure 구독에서 여러 HDInsight HBase 클러스터에 대한 메트릭을 모니터링할 수 있습니다.

Log Analytics는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는 [Azure](../../operations-management-suite/operations-management-suite-overview.md)의 서비스입니다. Log Analytics는 클라우드 및 온-프레미스 환경의 리소스 및 다른 모니터링 도구에서 생성된 데이터를 수집하여 여러 원본에 대한 분석을 제공합니다.

[Log Analytics 관리 솔루션](../../log-analytics/log-analytics-add-solutions.md)은 Log Analytics에 기능을 추가하여 추가 데이터 및 분석 도구를 제공합니다. Log Analytics 관리 솔루션은 특정 영역에 대한 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다. 또한 솔루션은 수집할 새 레코드 종류를 정의할 수 있으며. 이러한 레코드는 로그 검색 또는 새 사용자 인터페이스 기능을 사용하여 분석할 수 있습니다.

[Insight & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/)는 기본 Log Analytics 플랫폼을 기반으로 합니다. Log Analytics 기능을 사용하도록 선택하고, 서비스에 수집된 GB당 요금을 지불하거나, 작업 영역을 Insight & Analytics 계층으로 전환하여 서비스에서 관리하는 노드당 요금을 지불할 수 있습니다. Insight & Analytics는 Log Analytics에서 제공하는 기능의 상위 집합을 제공합니다. HBase 모니터링 솔루션은 Log Analytics 또는 Insight & Analytics와 함께 사용할 수 있습니다.

HDInsight HBase 모니터링 솔루션을 프로비전할 때 Log Analytics 작업 영역을 만듭니다. 각 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션이 있는 고유한 Log Analytics 환경입니다. 구독에 여러 작업 영역을 만들어 프로덕션 및 테스트와 같은 여러 환경을 지원할 수 있습니다.

## <a name="provision-hdinsight-hbase-monitoring"></a>HDInsight HBase 모니터링 프로비전

1. Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** 창의 **Marketplace** 아래에서 **모니터링 + 관리**를 선택합니다.
3. **모니터링 + 관리** 창에서 **모두 표시**를 선택합니다.

    ![모니터링 + 관리 창](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. 목록에서 **관리 솔루션** 밴드를 찾습니다. **관리 솔루션**의 오른쪽에서 **자세히**를 선택합니다.

    ![모니터링 + 관리 창](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. **관리 솔루션** 창에서 작업 영역에 추가할 HDInsight HBase 모니터링 관리 솔루션을 선택합니다.

    ![관리 솔루션 창](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. 관리 솔루션 창에서 관리 솔루션에 대한 정보를 검토한 다음, **만들기**를 선택합니다. 
7. *관리 솔루션 이름* 창에서 관리 솔루션과 연결할 기존 작업 영역을 선택하거나 새 Log Analytics 작업 영역을 만든 다음, 해당 작업 영역을 선택합니다.
8. Azure 구독, 리소스 그룹, 위치에 대한 작업 영역 설정을 적절하게 변경합니다. 
    ![솔루션 작업 영역](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. **만들기**를 선택합니다.  
10. 작업 영역에서 이 새 관리 솔루션을 사용하려면 **Log Analytics** > ***작업 영역 이름*** > **솔루션**으로 차례로 이동합니다. 관리 솔루션에 대한 항목이 목록에 표시됩니다. 항목을 선택하여 해당 솔루션으로 이동합니다.

    ![Log Analytics 솔루션](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. HDInsight HBase 모니터링 솔루션에 대한 창이 나타납니다.

    ![HDInsight HBase 솔루션 창](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Log Analytics에 데이터를 보내도록 HDInsight HBase 클러스터를 아직 구성하지 않았기 때문에 [요약] 타일에 어떤 데이터도 표시되지 않습니다.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Log Analytics에 HDInsight HBase 클러스터 연결

HDInsight HBase 모니터링에서 제공하는 도구를 사용하려면, 해당 지역 서버, 헤드 노드 및 ZooKeeper 노드에서 Log Analytics로 메트릭을 전송하도록 클러스터를 구성해야 합니다. 이 구성은 HDInsight HBase 클러스터에 대해 스크립트 동작을 실행하여 수행됩니다.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Log Analytics 작업 영역 ID 및 작업 영역 키 가져오기

클러스터의 노드에서 Log Analytics를 통해 인증할 수 있게 하려면 Log Analytics 작업 영역 ID 및 작업 영역 키가 필요합니다. 이러한 값을 가져오려면 다음을 수행합니다.

1. Azure Portal의 [HBase 모니터링] 창에서 [개요]를 선택합니다.

    ![HBase 모니터링 솔루션 창](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. [OMS 포털]을 선택하여 새 브라우저 탭 또는 창에서 OMS 포털을 시작합니다.

    ![OMS 포털 선택](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. OMS 포털 홈에서 [설정]을 선택합니다.

    ![OMS 포털](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. [연결된 원본], [Linux 서버]를 차례로 선택합니다.

    ![연결된 원본, Linux 서버를 차례로 선택](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. 스크립트 동작에 필요한 값이므로 작업 영역 ID 및 기본 키 값이 표시됩니다.

### <a name="run-the-script-action"></a>스크립트 동작 실행

HDInsight HBase 클러스터에서 데이터 수집을 사용하도록 설정하려면 클러스터의 모든 노드에 대해 스크립트 동작을 실행합니다.

1. Azure Portal에서 HDInsight HBase 클러스터에 대한 창으로 이동합니다.
2. **스크립트 동작**을 선택합니다.

    ![스크립트 동작](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. **새로운 항목 제출**을 선택합니다.

    ![새로운 항목 제출](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. **스크립트 동작 제출**에서 스크립트 유형을 `"- Custom"`으로 설정합니다.
5. 이 스크립트에 대한 이름을 제공합니다.
6. **Bash 스크립트 URI**에 대해 다음 URI에 붙여넣습니다.

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. **노드 유형**에 대해 세 가지(**헤드**, **지역**, **ZooKeeper**)를 모두 선택합니다.
8. **매개 변수** 텍스트 상자에서 각 값을 따옴표로 묶고 공백으로 구분한 작업 영역 ID와 작업 영역 키를 입력합니다.

        "<Workspace ID>" "<Workspace Key>"

9. 새 노드가 클러스터에 추가될 때 동작을 다시 실행하려면 **이 스크립트 동작 계속 다시 실행**을 선택합니다.

    ![스크립트 동작 설정](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. **만들기**를 선택합니다.
11. 스크립트 동작을 실행하는 데 몇 분이 걸립니다. [스크립트 동작] 창에서 상태를 모니터링할 수 있습니다.

    ![스크립트 동작 실행](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. 스크립트 동작이 완료되면 목록에서 스크립트 이름 옆에 녹색 확인 표시가 나타납니다.

    ![스크립트 동작 완료](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>HDInsight HBase 모니터링 솔루션 보기

스크립트 동작이 완료되면 몇 분 내에 모니터링 솔루션에 데이터가 표시됩니다.

1. Azure Portal에서 HDInsight HBase 솔루션의 창으로 이동합니다.
2. **개요** 탭을 선택합니다.
3. **요약** 아래에 모니터링되는 지역 서버의 수를 나타내는 타일이 표시됩니다.

    ![모니터링 요약 타일](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. 지역 서버 수가 있는 타일을 선택합니다. 기본 대시보드가 표시됩니다.
5. 이 대시보드는 지역에 대한 통계, 사용 중인 WAL(미리 쓰기 로그) 수, Log Analytics 검색 모음(예: 지역 서버 로그, Phoenix 로그 및 예외) 및 관련 메트릭을 시각화하기 위한 인기 있는 차트 모음에 대한 액세스를 제공합니다. 

    ![기본 대시보드](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. 이러한 항목 중 하나를 선택하면 쿼리를 구체화하고 데이터를 자세히 탐색할 수 있는 로그 검색 보기로 드릴다운합니다.

## <a name="next-steps"></a>다음 단계

* [Log Analytics에서 경고 만들기](../../log-analytics/log-analytics-alerts-creating.md)
* [Azure Log Analytics에서 로그 검색을 사용하여 데이터 찾기](../../log-analytics/log-analytics-log-searches.md)
