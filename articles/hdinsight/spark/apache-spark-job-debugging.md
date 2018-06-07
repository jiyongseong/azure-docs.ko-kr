---
title: Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그 | Microsoft Docs
description: YARN UI, Spark UI 및 Spark 기록 서버를 사용하여 Azure HDInsight의 Spark 클러스터에서 실행 중인 작업을 추적하고 디버깅합니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 1ddf8cd38b4d62dc206a9f27e0620f8c7b232ec3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그

이 문서에서는 YARN UI, Spark UI 및 Spark 기록 서버를 사용하여 HDInsight 클러스터에서 실행 중인 Spark 작업을 추적하고 디버깅하는 방법을 알아봅니다. Spark 클러스터에서 사용할 수 있는 Notebook을 통해 Spark 작업(**Machine Learning: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석**)을 시작합니다. 다음 단계를 사용하여 **spark-submit** 등의 다른 방법으로 제출한 응용 프로그램을 추적할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* Notebook, 즉 **[기계 학습: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석](apache-spark-machine-learning-mllib-ipython.md)**을 실행하기 시작했어야 합니다. 이 Notebook을 실행하는 방법은 링크를 따라갑니다.  

## <a name="track-an-application-in-the-yarn-ui"></a>YARN UI에서 응용 프로그램 추적
1. YARN UI를 시작합니다. **클러스터 대시보드**를 클릭한 다음, **YARN**을 클릭합니다.
   
    ![YARN UI 시작](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > 또는 Ambari UI에서 YARN UI를 시작할 수도 있습니다. Ambari UI를 시작하려면 **클러스터 대시보드**, **HDInsight 클러스터 대시보드**를 차례로 클릭합니다. Ambari UI에서 **YARN**, **빠른 링크**, 활성 Resource Manager, **Resource Manager UI**를 차례로 클릭합니다.    
   > 
   > 
2. Jupyter Notebook을 사용하여 Spark 작업을 시작했기 때문에 응용 프로그램은 **remotesparkmagics** 이라는 이름을 갖게 됩니다(Notebook에서 시작된 모든 응용 프로그램에 대한 이름임). 응용 프로그램 이름에 대한 응용 프로그램 ID를 클릭하여 작업에 대한 자세한 정보를 봅니다. 그러면 응용 프로그램 보기를 시작합니다.
   
    ![Spark 응용 프로그램 ID 찾기](./media/apache-spark-job-debugging/find-application-id.png)
   
    Jupyter Notebook에서 시작된 이러한 응용 프로그램의 경우 Notebook을 끝낼 때까지 상태는 항상 **실행 중** 입니다.
3. 응용 프로그램 보기에서 응용 프로그램 및 로그 (stdout/stderr)와 연결된 컨테이너에 대해 알아보기 위해 더 자세히 살펴볼 수 있습니다. 아래와 같이 **추적 URL**에 해당하는 연결을 클릭하여 Spark UI를 시작할 수도 있습니다. 
   
    ![컨테이너 로그 다운로드](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spark UI에서 응용 프로그램 추적
Spark UI에서 이전에 시작한 응용 프로그램에 의해 생성된 Spark 작업을 더 자세히 살펴볼 수 있습니다.

1. Spark UI를 시작하려면 응용 프로그램 보기에서 위의 화면 캡처에 표시된 것처럼 **추적 URL**에 대한 링크를 클릭합니다. Jupyter Notebook에서 실행 중인 응용 프로그램에 의해 시작되는 모든 Spark 작업을 확인할 수 있습니다.
   
    ![Spark 작업 보기](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. **실행자** 탭을 클릭하여 각 실행자에 대한 처리 및 저장소 정보를 봅니다. **Thread Dump** 링크를 클릭하여 호출 스택을 검색할 수도 있습니다.
   
    ![Spark 실행자 보기](./media/apache-spark-job-debugging/view-spark-executors.png)
3. **단계** 탭을 클릭하여 응용 프로그램과 관련된 단계를 봅니다.
   
    ![Spark 단계 보기](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    각 단계에서는 아래와 같이 실행 통계를 볼 수 있는 여러 작업이 있습니다.
   
    ![Spark 단계 보기](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. 단계 세부 정보 페이지에서 DAG 시각화를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **DAG 시각화** 링크를 확장합니다.
   
    ![Spark 단계 DAG 시각화 보기](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG 또는 Direct Aclyic Graph는 응용 프로그램에서 다양한 단계를 나타냅니다. 그래프의 파란색 상자는 각각 응용 프로그램에서 호출한 Spark 작업을 나타냅니다.
5. 단계 세부 정보 페이지에서 응용 프로그램 타임라인 보기를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **이벤트 타임라인** 링크를 확장합니다.
   
    ![Spark 단계 이벤트 타임라인 보기](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Spark 이벤트를 타임라인의 형태로 표시합니다. 타임라인 보기는 작업, 작업 내, 단계 내 등 세 가지 수준에서 사용할 수 있습니다. 위의 이미지는 지정된 단계에 대한 타임라인 보기를 캡처합니다.
   
   > [!TIP]
   > **확대/축소 사용** 확인란을 선택한 경우 타임라인 보기를 좌우로 스크롤할 수 있습니다.
   > 
   > 
6. Spark UI에서 다른 탭도 Spark 인스턴스에 대한 유용한 정보를 제공합니다.
   
   * 저장소 탭 - 응용 프로그램이 RDD를 만드는 경우 저장소 탭에 대한 정보를 찾을 수 있습니다.
   * 환경 탭 - 이 탭은 다음과 같이 Spark 인스턴스에 대한 유용한 정보를 많이 제공합니다. 
     * Scala 버전
     * 클러스터와 연결된 이벤트 로그 디렉터리
     * 응용 프로그램에 대한 실행자 코어 수
     * 등

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Spark 기록 서버를 사용하여 완료된 작업에 대한 정보 찾기
작업이 완료되면 작업에 대한 정보는 Spark 기록 서버에 유지됩니다.

1. Spark 기록 서버를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**, **Spark 기록 서버**를 차례로 클릭합니다.
   
    ![Spark 기록 서버 시작](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > 또는 Ambari UI에서 Spark 기록 서버를 시작할 수도 있습니다. Ambari UI를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**, **HDInsight 클러스터 대시보드의**를 차례로 클릭합니다. Ambari UI에서 **Spark**, **빠른 링크**, **Spark 기록 서버 UI**를 차례로 클릭합니다.
   > 
   > 
2. 완료된 응용 프로그램이 모두 표시됩니다. 자세한 내용은 응용 프로그램 ID를 클릭하여 응용 프로그램에 대해 더 자세히 살펴봅니다.
   
    ![Spark 기록 서버 시작](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>참고 항목
*  [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)

### <a name="for-data-analysts"></a>데이터 분석가

* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight에서 Spark를 사용하는 Application Insight 원격 분석 데이터 분석](apache-spark-analyze-application-insight-logs.md)
* [분산 심층 학습을 위해 Azure HDInsight Spark에서 Caffe 사용](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark 개발자

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)


