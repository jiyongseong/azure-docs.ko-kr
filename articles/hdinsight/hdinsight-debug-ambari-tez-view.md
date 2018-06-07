---
title: HDInsight에서 Ambari Tez 보기 사용 - Azure | Microsoft Docs
description: Ambari Tez 뷰를 사용하여 HDInsight에서 Tez 작업을 디버깅하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 98874377f31a435e7dd9736410c123ef623928d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>HDInsight에서 Ambari 뷰를 사용하여 Tez 작업 디버깅

HDInsight의 Ambari Web UI에는 Tez를 사용하는 작업을 이해 및 디버깅하는 데 사용할 수 있는 Tez 보기가 포함되어 있습니다. Tez 뷰를 사용하면 연결된 항목의 그래프로 작업을 시각화하고 각 항목을 자세히 알아보며 통계 및 로깅 정보를 검색할 수 있습니다.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Linux 기반 HDInsight 클러스터입니다. 클러스터를 만드는 단계는 [Linux 기반 HDInsight 사용 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* HTML5를 지원하는 최신 웹 브라우저

## <a name="understanding-tez"></a>Tez 이해

Tez는 기존의 MapReduce 처리보다 빠른 속도를 제공하는 Hadoop의 데이터 처리에 대해 확장 가능한 프레임워크입니다. Linux 기반 HDInsight 클러스터에서는 Hive에 대한 기본 엔진입니다.

Tez는 작업에서 수행해야 하는 작업 순서를 설명하는 DAG(방향성 비순환 그래프)를 만듭니다. 개별 동작은 꼭짓점을 호출하고 전체 작업의 일부를 실행합니다. 꼭짓점에서 설명하는 작업의 실제 실행을 태스크라고 하며 클러스터의 여러 노드에 분산될 수 있습니다.

### <a name="understanding-the-tez-view"></a>Tez 뷰 이해

Tez 보기는 기록 정보 및 실행 중인 프로세스에 대한 정보를 제공합니다. 이 정보는 작업이 클러스터 사이에서 분산된 방식을 보여줍니다. 또한 태스크와 꼭짓점에서 사용하는 카운터 및 작업과 관련된 오류 정보를 표시합니다. 다음과 같은 시나리오에서 유용한 정보를 제공할 수 있습니다.

* 장기 실행 처리를 모니터링하고 맵의 진행 상황을 보며 태스크를 줄입니다.
* 처리를 향상시킬 방법 또는 실패한 이유를 알아보기 위해 성공 또는 실패한 처리에 대한 기록 데이터를 분석합니다.

## <a name="generate-a-dag"></a>DAG 생성

Tez 보기에는 Tez 엔진을 사용하는 작업이 현재 실행 중인 경우 또는 이전에 실행된 경우에만 데이터가 포함됩니다. 간단한 Hive 쿼리는 Tez를 사용하지 않고도 확인할 수 있습니다. 필터링, 그룹화, 정렬, 조인 등을 수행하는 복잡한 쿼리는 Tez 엔진을 사용합니다.

Tez를 사용하는 Hive 쿼리를 실행하려면 다음 단계를 사용합니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. 여기서 **CLUSTERNAME**은 HDInsight 클러스터의 이름입니다.

2. 페이지 위쪽의 메뉴에서 **보기** 아이콘을 선택합니다. 이 아이콘은 여러 개의 사각형처럼 생겼습니다. 표시되는 드롭다운에서 **Hive 보기**를 선택합니다.

    ![Hive 뷰 선택](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. 로드된 Hive 보기에서 다음 쿼리를 [쿼리 편집기]에 붙여넣은 후 **실행**을 클릭합니다.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    작업이 완료되면 **쿼리 프로세스 결과** 섹션에 결과가 표시됩니다. 결과는 다음 텍스트와 유사합니다.

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. **로그** 탭을 선택합니다. 다음 텍스트와 유사한 정보가 표시됩니다.

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    다음 섹션에서 사용하는 **App id** 값을 저장합니다.

## <a name="use-the-tez-view"></a>Tez 뷰 사용

1. 페이지 위쪽의 메뉴에서 **보기** 아이콘을 선택합니다. 표시되는 드롭다운에서 **Tez 보기**를 선택합니다.

    ![Tez 뷰 선택](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Tez 뷰가 로드되면 클러스터에서 현재 실행 중이거나 실행된 Hive 쿼리 목록이 표시됩니다.

    ![모든 DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. 항목이 하나만 있는 경우 이전 섹션에서 실행한 쿼리에 대한 것입니다. 항목이 여러 개 있는 경우 페이지 맨 위에 있는 필드를 사용하여 검색할 수 있습니다.

4. Hive 쿼리에 대한 **쿼리 ID**를 선택합니다. 쿼리에 대한 정보가 표시됩니다.

    ![DAG 세부 정보](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. 이 페이지에 있는 탭에서 다음 정보를 볼 수 있습니다.

    * **쿼리 세부 정보**: Hive 쿼리에 대한 세부 정보입니다.
    * **타임라인**: 각 처리 단계가 진행되는 데 걸린 시간에 대한 정보입니다.
    * **구성**: 이 쿼리에 사용된 구성입니다.

    __쿼리 세부 정보__에서 링크를 사용하여 이 쿼리에 대한 __응용 프로그램__ 또는 __DAG__ 관련 정보를 찾을 수 있습니다.
    
    * __응용 프로그램__ 링크를 클릭하면 이 쿼리의 YARN 응용 프로그램에 대한 정보가 표시됩니다. 여기에서 YARN 응용 프로그램 로그에 액세스할 수 있습니다.
    * __DAG__ 링크를 클릭하면 이 쿼리에 대해 지정된 비순환 그래프 관련 정보가 표시됩니다. 여기에서 DAG의 그래픽 표시를 볼 수 있습니다. DAG 내에서 꼭짓점에 대한 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Tez 뷰를 사용하는 방법을 배웠으므로 [HDInsight에서 Hive 사용](hadoop/hdinsight-use-hive.md)에 대해 자세히 알아봅니다.

Tez에서 자세한 기술 정보는 [Hortonworks의 Tez 페이지](http://hortonworks.com/hadoop/tez/)를 참조하세요.

HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
