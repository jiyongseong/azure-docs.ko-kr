---
title: Azure HDInsight에서 Apache Spark 클러스터 관련 문제 해결 | Microsoft Docs
description: Azure HDInsight의 Apache Spark 클러스터 관련 문제 및 이를 해결하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 664c97117de793209007843fa23c98f52c2b079d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에 대한 알려진 문제

이 문서는 HDInsight Spark 공개 미리 보기에 대한 모든 알려진 문제를 추적합니다.  

## <a name="livy-leaks-interactive-session"></a>Livy 누수 대화형 세션
Livy가 여전히 활성 상태인 대화형 세션으로 재시작하는 경우(Ambari에서 또는 헤드 노드 0 가상 머신 재부팅으로 인해) 대화형 작업 세션이 손실됩니다. 따라서 새 작업은 수락됨 상태에서 멈출 수 있습니다.

**해결 방법:**

다음 절차에 따라 문제를 해결합니다.

1. 헤드 노드로 ssh합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 실행하여 Livy를 통해 시작한 대화형 작업의 응용 프로그램 ID를 찾습니다. 
   
        yarn application –list
   
    작업이 명시적으로 지정된 이름 없이 Livy 대화형 세션으로 시작된 경우 기본 작업 이름은 Livy가 됩니다. Jupyter 노트북으로 시작된 Livy 세션의 경우 작업 이름은 remotesparkmagics_ *로 시작합니다. 
3. 다음 명령을 실행하여 해당 작업을 중지합니다. 
   
        yarn application –kill <Application ID>

새 작업 실행이 시작됩니다. 

## <a name="spark-history-server-not-started"></a>Spark 기록 서버가 시작되지 않음
클러스터가 만들어진 후 Spark 기록 서버가 자동으로 시작되지 않습니다.  

**해결 방법:** 

Ambari에서 기록 서버를 수동으로 시작합니다.

## <a name="permission-issue-in-spark-log-directory"></a>Spark 로그 디렉터리에 대한 사용 권한 문제
hdiuser는 spark-submit을 사용하여 작업을 제출할 때 다음과 같은 오류가 발생합니다.

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
또한 드라이버 로그가 기록되지 않습니다. 

**해결 방법:**

1. hdiuser를 Hadoop 그룹에 추가합니다. 
2. 클러스터를 만든 후에 /var/log/spark에 777 권한을 제공합니다. 
3. Ambari를 사용하여 777 권한으로 디렉터리가 되도록 spark 로그 위치를 업데이트합니다.  
4. sudo로 spark-제출을 실행합니다.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix 커넥터가 지원되지 않음

HDInsight Spark 클러스터는 Spark-Phoenix 커넥터를 지원하지 않습니다.

**해결 방법:**

대신 Spark-HBase 커넥터를 사용해야 합니다. 자세한 내용은 [Spark-HBase 커넥터 사용 방법](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)을 참조하세요.

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter Notebook 관련 문제
다음은 Jupyter Notebook과 관련된 몇 가지 알려진 문제입니다.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>파일 이름에 ASCII가 아닌 문자가 있는 Notebook
Jupyter 노트북 파일 이름에는 비 ASCII 문자를 사용하지 마세요. Jupyter UI를 통해 ASCII가 아닌 파일 이름을 갖는 파일을 업로드하려고 하면 오류 메시지 없이 실패합니다. Jupyter에서는 해당 파일을 업로드할 수 없도록 하지만 보이는 오류를 throw하지 않습니다.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>더 큰 Notebook을 로드하는 중 오류
더 큰 Notebook을 로드할 때 **`Error loading notebook`** 오류가 표시될 수 있습니다.  

**해결 방법:**

이 오류가 발생했다고 해서 데이터가 손상되거나 손실된 것은 아닙니다.  Notebook은 여전히 `/var/lib/jupyter`의 디스크에 있으며 클러스터에 대한 SSH를 통해 액세스할 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

SSH를 사용하여 클러스터에 연결한 경우 Notebook을 사용자의 클러스터에서 로컬 컴퓨터에(SCP 또는 WinSCP를 사용하여) 백업으로 복사하여 Notebook의 중요 데이터 손실을 방지할 수 있습니다. 그런 다음 포트 8001의 헤드 노드에 대한 SSH 터널을 통해 게이트웨이를 거치지 않고 Jupyter에 액세스할 수 있습니다.  여기에서 노트북의 출력을 지우고 다시 저장하여 노트북의 크기를 최소화할 수 있습니다.

나중에 이 오류가 발생하지 않도록 하려면 몇 가지 모범 사례를 따라야 합니다.

* 노트북 크기를 작게 유지하는 것이 중요합니다. Jupyter에 다시 전송된 Spark 작업의 출력은 노트북에 보관됩니다.  RDD의 데이터 프레임에서 `.collect()`를 실행하지 못하도록 하는 것이 Jupyter의 모범 사례입니다. 그 대신 RDD의 콘텐츠를 살펴보려면 출력이 너무 커지지 않도록 `.take()` 또는 `.sample()`의 실행을 고려합니다.
* 또한 노트북을 저장할 때 모든 출력을 지워서 크기를 줄입니다.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>노트북 초기 시작이 예상보다 오래 걸리는 경우
Jupyter Notebook에서 Spark 매직을 사용한 첫 번째 코드 문의 경우 1분 이상이 걸릴 수 있습니다.  

**설명:**

이는 첫 번째 코드 셀이 실행될 때 발생합니다. 백그라운드에서 세션 구성이 시작되고 Spark, SQL 및 Hive 컨텍스트가 설정됩니다. 이러한 컨텍스트가 설정된 후 첫 번째 문이 실행되므로 문이 완료되는 데 시간이 오래 걸린 것 같은 느낌이 듭니다.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>세션 만들기에서 Jupyter 노트북 시간 제한
Spark 클러스터에 리소스가 부족할 때 Jupyter 노트북에서 Spark 및 PySpark 커널은 세션을 만들려고 할 때 시간 초과가 됩니다. 

**해결 방법:** 

1. 다음과 같은 방법으로 Spark 클러스터의 리소스를 확보합니다.
   
   * Close and Halt 메뉴로 이동하거나 노트북 탐색기에서 종료를 클릭하여 다른 Spark 노트북을 중지합니다.
   * YARN에서 다른 Spark 응용 프로그램을 중지합니다.
2. 시작하려는 노트북을 다시 시작합니다. 이제 세션을 만들기 위한 충분한 리소스를 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목
* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

