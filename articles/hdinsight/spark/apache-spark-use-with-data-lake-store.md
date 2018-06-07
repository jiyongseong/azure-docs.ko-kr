---
title: Apache Spark를 사용하여 Azure Data Lake Store의 데이터 분석 | Microsoft Docs
description: Spark 작업을 실행하여 Azure Data Lake 저장소에 저장된 데이터 분석
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: fa6f93231cba46e29206ec312fb82ad120ed45f6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>HDInsight Spark 클러스터를 사용하여 Data Lake Store의 데이터 분석

이 자습서에서는 HDInsight Spark 클러스터와 함께 제공되는 Jupyter Notebook을 사용하여 Data Lake Store 계정에서 데이터를 읽는 작업을 실행합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Data Lake Store 계정 [Azure Portal을 사용하여 Azure Data Lake Store 시작](../../data-lake-store/data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

* Data Lake Store를 저장소로 사용하는 Azure HDInsight Spark 클러스터 - [Azure Portal을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)에 나오는 지침을 따릅니다.

    
## <a name="prepare-the-data"></a>데이터 준비

> [!NOTE]
> 기본 저장소로 Data Lake Store를 사용하여 HDInsight 클러스터를 만든 경우 이 단계를 수행할 필요가 없습니다. 클러스터 만들기 프로세스는 클러스터를 만드는 동안 지정된 Data Lake Store 계정에서 몇 가지 샘플 데이터를 추가합니다. [Data Lake Store에서 HDInsight Spark 클러스터 사용](#use-an-hdinsight-spark-cluster-with-data-lake-store) 섹션으로 건너 뜁니다.
>
>

Data Lake Store를 추가 저장소로, Azure Storage Blob을 기본 저장소로 사용하여 HDInsight 클러스터를 만든 경우 먼저 몇 가지 샘플 데이터를 Data Lake Store 계정으로 복사해야 합니다. HDInsight 클러스터와 연결된 Azure Storage Blob의 샘플 데이터를 사용할 수 있습니다. 이 작업에는 [ADLCopy 도구](http://aka.ms/downloadadlcopy) 를 사용할 수 있습니다. 링크에서 도구를 다운로드하여 설치합니다.

1. 명령 프롬프트를 열고 AdlCopy가 설치된 디렉터리로 이동합니다(일반적으로 `%HOMEPATH%\Documents\adlcopy`).

2. 다음 명령을 실행하여 원본 컨테이너에서 데이터 레이크 저장소로 특정 BLOB를 복사합니다.

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    **/HdiSamples/HdiSamples/SensorSampleData/hvac/**의 **HVAC.csv** 샘플 데이터 파일을 Azure Data Lake Store 계정으로 복사합니다. 코드 조각은 다음과 같습니다.

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > 파일과 경로 이름의 대/소문자가 적절한지 확인합니다.
   >
   >
3. Data Lake Store 계정이 있는 Azure 구독에 대한 자격 증명을 입력하라는 메시지가 표시됩니다. 다음 코드 조각과 유사한 결과가 표시됩니다.

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    데이터 파일(**HVAC.csv**)은 Data Lake Store 계정의 **/hvac** 폴더에 복사됩니다.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Data Lake Store를 포함한 HDInsight Spark 클러스터 사용

1. [Azure Portal](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

    ![새 Jupyter 노트북 만들기](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "새 Jupyter 노트북 만들기")

4. PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 이 시나리오에 필요한 형식을 가져와 시작할 수 있습니다. 이렇게 하려면 셀에 다음 코드 조각을 붙여 넣고 **SHIFT + ENTER**를 누릅니다.

        from pyspark.sql.types import *

    Jupyter에서 작업을 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

     ![Jupyter 노트북 작업 상태](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Jupyter 노트북 작업 상태")

5. Data Lake Store 계정에 복사한 **HVAC.csv** 파일을 사용하여 샘플 데이터를 임시 테이블에 로드합니다. 다음 URL 패턴을 사용하여 Data Lake 저장소 계정의 데이터에 액세스할 수 있습니다.

    * Data Lake Store를 기본 저장소로 사용하는 경우 HVAC.csv는 다음 URL과 비슷한 경로에 있습니다.

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        또는 다음과 같이 축약된 형식을 사용할 수도 있습니다.

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Data Lake Store를 추가 저장소로 사용하는 경우 HVAC.csv는 다음과 같이 복사한 위치에 있습니다.

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     빈 셀에는 다음 코드 예제를 붙여넣습니다. 이때 **MYDATALAKESTORE**를 Data Lake Store 계정 이름으로 바꾸고 **Shift+Enter**를 누릅니다. 이 코드 예제는 **hvac**라는 임시 테이블에 데이터로 등록됩니다.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. PySpark 커널을 사용하기 때문에 이제 `%%sql` 매직을 사용하여 방금 만든 임시 테이블 **hvac**에서 SQL 쿼리를 직접 실행할 수 있습니다. `%%sql` 매직 및 기타 PySpark 커널에서 사용 가능한 매직에 대한 자세한 내용은 [Spark HDInsight 클러스터와 함께 Jupyter Notebook에서 사용 가능한 커널](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)을 참조하세요.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. 작업이 성공적으로 완료되면 기본적으로 다음 테이블 형식의 출력이 표시됩니다.

      ![쿼리 결과 출력 테이블](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "쿼리 결과 출력 테이블")

     다른 시각화로 결과를 볼 수도 있습니다. 예를 들어 동일한 출력에 대한 영역 그래프는 다음과 같습니다.

     ![쿼리 결과 영역 그래프](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "쿼리 결과 영역 그래프")

8. 응용 프로그램 실행을 완료한 후 리소스를 해제하도록 Notebook을 종료해야 합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다. 그러면 Notebook이 종료되고 닫힙니다.


## <a name="next-steps"></a>다음 단계

* [Apache Spark에서 실행할 독립 실행형 Scala 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기](apache-spark-intellij-tool-plugin.md)
* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기](apache-spark-eclipse-tool-plugin.md)
