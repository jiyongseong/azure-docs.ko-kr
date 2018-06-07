---
title: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook 사용 | Microsoft Docs
description: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b2f47dce058af7a39366c06d0b33117a66ed116a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook 사용

HDInsight Spark 클러스터에는 Spark 작업을 실행하는 데 사용할 수 있는 Zeppelin Notebook이 포함되어 있습니다. 이 문서에서는 HDInsight 클러스터에서 Zeppelin Notebook을 사용하는 방법에 대해 알아 봅니다.

> [!NOTE]
> Zeppelin Notebook은 HDInsight 3.5의 Spark 1.6.3 및 HDInsight 3.6의 Spark 2.1.0에만 사용할 수 있습니다.
>

**필수 조건:**

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="launch-a-zeppelin-notebook"></a>Zeppelin Notebook 시작
1. Spark 클러스터 블레이드에서 **클러스터 대시보드**를 클릭하고 **Zeppelin Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.
   
   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. 새 Notebook을 만듭니다. 헤더 창에서 **노트북**을 클릭하고 **새 메모 만들기**를 클릭합니다.
   
    ![새 Zeppelin Notebook 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "새 Zeppelin Notebook 만들기")
   
    Notebook 이름을 입력한 다음 **노트 만들기**를 클릭합니다.
3. 또한 Notebook 헤더에 연결된 상태가 표시되는지 확인합니다. 오른쪽 위 모서리에 녹색 점으로 표시됩니다.
   
    ![Zeppelin Notebook 상태](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 상태")
4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들면 샘플 데이터 파일인 **hvac.csv**가 **\HdiSamples\SensorSampleData\hvac** 아래 연결된 저장소 계정에 복사됩니다.
   
    새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    **Shift + Enter**를 누르거나 단락에 대한 **재생** 단추를 클릭하여 코드 조각을 실행합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.
   
    ![원시 데이터에서 임시 테이블 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "원시 데이터에서 임시 테이블 만들기")
   
    또한 각 단락에 제목을 제공할 수도 있습니다. 오른쪽 모서리에서 **설정** 아이콘을 클릭하고 **제목 표시**를 클릭합니다.
5. 이제 **hvac** 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    **%sql** 문의 처음 부분은 Notebook에 Livy Scala 인터프리터를 사용하도록 지시합니다.
   
    다음 스크린샷은 출력을 보여 줍니다.
   
    ![Notebook을 사용하여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook을 사용하여 Spark SQL 문 실행")
   
     동일한 출력에 대해 서로 다른 표현 간을 전환하려면 표시 옵션(사각형으로 강조 표시됨)을 클릭합니다. **설정** 을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp_diff**의 평균을 값으로 사용합니다.
6. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 쿼리할 수 있는 값이 포함된 쿼리에 변수 **Temp**를 정의하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 다음 스크린샷은 출력을 보여 줍니다.
   
    ![Notebook을 사용하여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook을 사용하여 Spark SQL 문 실행")
   
    후속 쿼리에 대해서는 드롭다운에서 새 값을 선택하고 쿼리를 다시 실행할 수 있습니다. **설정** 을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp_diff**의 평균을 값으로, **targettemp**를 그룹으로 사용합니다.
7. Livy 인터프리터를 다시 시작하여 응용 프로그램을 종료합니다. 이렇게 하려면 오른쪽 위 모서리의 로그인한 사용자 이름을 클릭하여 인터프리터 설정을 연 다음 **인터프리터**를 클릭합니다.
   
    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")
8. Livy 인터프리터 설정으로 스크롤한 다음 **다시 시작**을 클릭합니다.
   
    ![Livy 인터프리터 다시 시작](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터 다시 시작")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook에서 외부 패키지 사용 방법
HDInsight(Linux)의 Apache Spark 클러스터에서 Zeppelin Notebook을 구성하여 클러스터에 포함되지 않는 외부의 커뮤니티 제공 패키지를 사용할 수 있습니다. 사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](http://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](http://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter Notebook에서 [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하는 방법을 설명합니다.

1. 인터프리터 설정을 엽니다. 오른쪽 위 모서리의 로그인한 사용자 이름을 클릭한 다음 **인터프리터**를 클릭합니다.
   
    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")
2. Livy 인터프리터 설정으로 스크롤한 다음 **편집**을 클릭합니다.
   
    ![인터프리터 설정 변경](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "인터프리터 설정 변경")
3. **livy.spark.jars.packages**라는 새 키를 추가하고 그 값을 `group:id:version` 형식으로 설정합니다. 따라서 [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하려면 키 값을 `com.databricks:spark-csv_2.10:1.4.0`으로 설정해야 합니다.
   
    ![인터프리터 설정 변경](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "인터프리터 설정 변경")
   
    **저장**을 클릭한 다음 Livy 인터프리터를 다시 시작합니다.
4. **팁**: 위에서 입력한 키 값에 도달하는 방법을 이해하려면 여기를 참조하세요.
   
    a. Maven Repository에서 패키지를 찾습니다. 이 자습서에서는 [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)를 사용했습니다.
   
    나. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다.
   
    ![Jupyter Notebook에서 외부 패키지 사용](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter Notebook에서 외부 패키지 사용")
   
    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 저장 위치
Zeppelin Notebook은 클러스터 헤드 노드에 저장됩니다. 따라서 클러스터를 삭제하면 Notebook도 삭제됩니다. 나중에 다른 클러스터에서 사용하기 위해 Notebook을 유지하려면 작업 실행을 완료 한 후 Notebook을 내보내야 합니다. Notebook을 내보내려면 아래 이미지와 같이 **내보내기** 아이콘을 클릭합니다.

![Notebook 다운로드](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Notebook 다운로드")

이렇게 하면 Notebook이 다운로드 위치에 JSON 파일로 저장됩니다.

## <a name="livy-session-management"></a>Livy 세션 관리
Zeppelin Notebook에서 첫 번째 코드 단락을 실행하면 HDInsight Spark 클러스터에 새로운 Livy 세션이 만들어집니다. 이 세션은 이후에 만드는 모든 Zeppelin Notebook에서 공유됩니다. 클러스터 재부팅 등 어떤 이유로 Livy 세션이 종료되면 Zeppelin Notebook에서 작업을 실행할 수 없습니다.

이 경우 Zeppelin Notebook에서 작업 실행을 시작하기 전에 다음 단계를 수행해야 합니다. 

1. Zeppelin Notebook에서 Livy 인터프리터를 다시 시작합니다. 이렇게 하려면 오른쪽 위 모서리의 로그인한 사용자 이름을 클릭하여 인터프리터 설정을 연 다음 **인터프리터**를 클릭합니다.
   
    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")
2. Livy 인터프리터 설정으로 스크롤한 다음 **다시 시작**을 클릭합니다.
   
    ![Livy 인터프리터 다시 시작](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터 다시 시작")
3. 기존 Zeppelin Notebook에서 코드 셀을 실행합니다. 이렇게 하면 HDInsight 클러스터에 새로운 Livy 세션이 만들어집니다.

## <a name="seealso"></a>참고 항목
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
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







