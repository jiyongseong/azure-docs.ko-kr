---
title: '자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행 | Microsoft Docs'
description: Azure HDInsight의 Spark 클러스터에서 데이터를 로드하고 대화형 쿼리를 실행하는 방법을 알아봅니다.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/17/2018
ms.openlocfilehash: eeb0f8134d21d42c8401f58828160d613e8ef92b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302052"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행

이 자습서에서는 csv 파일에서 데이터 프레임을 만드는 방법과 Azure HDInsight의 Apache Spark 클러스터에 대해 대화형 Spark SQL 쿼리를 실행하는 방법을 알아봅니다. Spark에서 데이터 프레임은 명명된 열로 구성된 데이터의 분산된 컬렉션입니다. 데이터 프레임은 관계형 데이터베이스의 테이블이나 R/Python의 데이터 프레임과 개념적으로 동일합니다.
 
이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * csv 파일에서 데이터 프레임 만들기
> * 데이터 프레임에서 쿼리 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 완료합니다.

## <a name="create-a-dataframe-from-a-csv-file"></a>csv 파일에서 데이터 프레임 만들기

응용 프로그램은 SQLContext 개체를 사용하여 기존 RDD(복원력 있는 분산 데이터 집합), Hive 테이블 또는 데이터 원본에서 데이터 프레임을 만들 수 있습니다. 다음 스크린샷에서는 이 자습서에 사용되는 HVAC.csv 파일의 스냅숏을 보여줍니다. csv 파일에는 모든 HDInsight Spark 클러스터가 함께 제공됩니다. 이 데이터는 건물의 온도 변화를 캡처합니다.
    
![대화형 Spark SQL 쿼리용 데이터의 스냅숏](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "대화형 Spark SQL 쿼리용 데이터의 스냅숏")


1. 필수 조건 섹션에서 만든 Jupyter Notebook을 엽니다.
2. 노트북의 빈 셀에 다음 코드를 붙여넣은 다음 **Shift+Enter**를 눌러 해당 코드를 실행합니다. 코드는 이 시나리오에 필요한 형식을 가져옵니다.

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Jupyter에서 대화형 쿼리를 실행하면 웹 브라우저 창 또는 탭 캡션에 노트북 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

    ![대화형 Spark SQL 쿼리 상태](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "대화형 Spark SQL 쿼리 상태")

3. 다음 코드를 실행하여 데이터 프레임 및 임시 테이블(**hvac**)을 만듭니다. 

    ```PySpark
    # Create an RDD from sample data
    csvFile = spark.read.csv('wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > PySpark 커널을 사용하여 Notebook을 만들면 첫 번째 코드 셀을 실행할 때 SQL 컨텍스트가 자동으로 만들어집니다. 컨텍스트를 명시적으로 만들 필요가 없습니다.


## <a name="run-queries-on-the-dataframe"></a>데이터 프레임에서 쿼리 실행

테이블이 만들어지면 데이터에 대한 대화형 쿼리를 실행할 수 있습니다.

1. 노트북의 빈 셀에서 다음 코드를 실행합니다.

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   이제 Notebook에서 PySpark 커널이 사용되므로 **hvac** 임시 테이블에서 대화형 SQL 쿼리를 직접 실행할 수 있습니다.

   다음과 같은 테이블 형식 출력이 표시됩니다.

     ![대화형 Spark 쿼리 결과의 테이블 출력](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "대화형 Spark 쿼리 결과의 테이블 출력")

3. 다른 시각화로 결과를 볼 수도 있습니다. 동일한 출력에 대한 영역형 그래프를 보려면 **영역**을 선택한 다음 표시된 것처럼 다른 값을 설정합니다.

    ![대화형 Spark 쿼리 결과의 영역 그래프](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "대화형 Spark 쿼리 결과의 영역 그래프")

10. Notebook의 **파일** 메뉴에서 **닫기 및 검사점**을 선택합니다. 

11. 이제 [다음 자습서](apache-spark-use-bi-tools.md)를 시작하는 경우 Notebook을 열어 둡니다. 그렇지 않은 경우 Notebook을 종료하여 Notebook의 **파일** 메뉴에서 클러스터 리소스를 해제하고 **닫기 및 중지**를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

HDInsight를 사용하면 데이터가 Azure Storage 또는 Azure Data Lake Store에 저장되므로 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 다음 자습서의 작업을 바로 수행하려는 경우 클러스터를 유지할 수 있습니다.

Azure Portal에서 클러스터를 열고 **삭제**를 선택합니다.

![HDInsight 클러스터 삭제](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight Spark 클러스터와 기본 저장소 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* Spark 데이터 프레임을 만듭니다.
* 데이터 프레임에 대해 Spark SQL을 실행합니다.

다음 문서로 진행하여 Spark에 등록된 데이터를 Power BI와 같은 BI 분석 도구로 가져오는 방법을 확인하세요. 
> [!div class="nextstepaction"]
> [BI 도구를 사용하여 데이터 분석](apache-spark-use-bi-tools.md)

