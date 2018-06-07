---
title: '자습서: Azure HDInsight에서 Power BI를 사용하여 Apache Spark 데이터 분석 | Microsoft Docs'
description: Microsoft Power BI를 사용하여 Spark 데이터 저장 HDInsight 클러스터 시각화
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ece0132573f25f4d288309d2e7bb6710f8fd9519
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-spark-data-using-power-bi-in-hdinsight"></a>자습서: HDInsight에서 Power BI를 사용하여 Spark 데이터 분석 

Microsoft Power BI를 사용하여 Azure HDInsight의 Apache Spark 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Power BI를 사용하여 Spark 데이터 시각화

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **문서 [자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md)을 완료합니다**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) 및 [Power BI 평가판 구독](https://app.powerbi.com/signupredirect?pbi_source=web)(선택 사항)


## <a name="verify-the-data"></a>데이터 확인

[이전 자습서](apache-spark-load-data-run-query.md)에서 만든 Jupyter 노트북은 `hvac` 테이블을 만드는 코드를 포함합니다. 이 테이블은 **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** 모든 HDInsight Spark 클러스터에서 사용 가능한 CSV 파일을 기반으로 합니다. 데이터를 확인하려면 다음 절차를 따릅니다.

1. Jupyter 노트북에서 다음 코드를 붙여넣은 다음 **Shift + Enter**를 누릅니다. 코드가 테이블의 존재 여부를 확인합니다.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    출력은 다음과 같이 표시됩니다.

    ![Spark에 테이블 표시](./media/apache-spark-use-bi-tools/show-tables.png)

    이 자습서를 시작하기 전에 Notebook을 닫은 경우 `hvactemptable`이 정리되므로 출력에 포함되지 않습니다.  metastore에 저장된 Hive 테이블만(**isTemporary** 열 아래에서 **False**로 표시됨) BI 도구에서 액세스할 수 있습니다. 이 자습서에서는 사용자가 만든 **hvac** 테이블에 연결합니다.

2. 빈 셀에 다음 코드를 붙여넣은 다음 **Shift + Enter**를 누릅니다. 코드가 테이블의 데이터를 확인합니다.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    출력은 다음과 같이 표시됩니다.

    ![Spark의 hvac 테이블에서 행 표시](./media/apache-spark-use-bi-tools/select-limit.png)

3. 노트북의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다. Notebook을 종료하여 리소스를 해제합니다. 

## <a name="visualize-the-data"></a>데이터 시각화

이 섹션에서는 Power BI를 사용하여 Spark 클러스터 데이터에서 시각화, 보고서 및 대시보드를 만듭니다. 

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop에서 보고서 만들기
Spark를 사용하는 첫 번째 단계는 Power BI Desktop에서 클러스터에 연결하고, 클러스터에서 데이터를 로드하고, 해당 데이터를 기반으로 기본 시각화를 만드는 것입니다.

> [!NOTE]
> 이 문서에서 설명하는 커넥터는 현재 미리 보기 상태입니다. [Power BI 커뮤니티](https://community.powerbi.com/) 사이트 또는 [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas)를 통해 피드백을 제공하세요.

1. [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)을 엽니다.
1. **홈** 탭에서 **데이터 가져오기**, **자세히**를 차례로 클릭합니다.

    ![HDInsight Apache Spark에서 Power BI Desktop으로 데이터 가져오기](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Apache Spark BI에서 Power BI로 데이터 가져오기")


2. 검색 상자에 `Spark`를 입력하고 **Azure HDInsight Spark(베타)** 를 선택한 후 **연결**을 클릭합니다.

    ![Apache Spark BI에서 Power BI로 데이터 가져오기](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI에서 Power BI로 데이터 가져오기")

3. 클러스터 URL을 입력하고(`mysparkcluster.azurehdinsight.net` 양식으로), **DirectQuery**를 선택한 다음 **확인**을 클릭합니다.

    Spark와 함께 데이터 연결 모드 중 하나를 사용할 수 있습니다. DirectQuery를 사용하는 경우 변경 내용은 전체 데이터 집합을 새로 고치지 않고 보고서에 반영됩니다. 데이터를 가져오는 경우 변경 내용을 보려면 데이터 집합을 새로 고쳐야 합니다. DirectQuery를 사용하는 방법 및 경우에 대한 자세한 내용은 [Power BI에서 DirectQuery 사용](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)을 참조하세요. 

4. HDInsight 로그인 계정 정보를 입력한 다음 **연결**을 클릭합니다. 기본 계정 이름은 *admin*입니다.

5. `hvac` 테이블을 선택하고 데이터의 미리 보기를 확인하기 위해 기다린 후 **로드**를 클릭합니다.

    ![Spark 클러스터 사용자 이름 및 암호](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark 클러스터 사용자 이름 및 암호")

    Power BI Desktop에 Spark 클러스터에 연결하고 `hvac` 테이블에서 데이터를 로드하는 데 필요한 정보가 있습니다. 테이블 및 해당 열이 **필드** 창에 표시됩니다.  다음 스크린샷을 참조하세요.

6. 각 건물에 대한 대상 온도와 실제 온도 간의 차이를 시각화합니다. 

    1. **시각화** 창에서 **영역형 차트**를 선택합니다. 
    2. **BuildingID** 필드를 **축**으로 끌어 놓고, **ActualTemp** 및 **TargetTemp** 필드를 **값**으로 끌어 놓습니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

        다이어그램은 다음과 같이 표시됩니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

        기본적으로 시각화에서는 **ActualTemp** 및 **TargetTemp**의 합계를 보여 줍니다. 시각화 창에서 **ActualTemp** 및 **TragetTemp** 옆에 있는 아래쪽 화살표를 클릭하면 **Sum**이 선택된 것을 볼 수 있습니다.

    3. 시각화 창에서 **ActualTemp** 및 **TragetTemp** 옆에 있는 아래쪽 화살표를 클릭하고 **Average**를 선택하여 각 건물에 대한 실제 온도와 대상 온도 간의 평균을 구합니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

        데이터 시각화는 스크린샷의 데이터 시각화와 비슷해야 합니다. 커서를 시각화 위로 이동하면 관련 데이터와 함께 도구 설명이 나타납니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

7. **파일**, **저장**을 차례로 클릭하고, 파일에 대한 이름 `BuildingTemperature.pbix`를 입력합니다. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI 서비스에 보고서 게시(선택 사항)

Power BI 서비스를 사용하면 조직 전체에서 보고서 및 대시보드를 공유할 수 있습니다. 이 섹션에서는 먼저 데이터 집합과 보고서를 게시합니다. 그런 다음 보고서를 대시보드에 고정합니다. 대시보드는 보고서에 있는 데이터 하위 집합에 집중하는 데 일반적으로 사용되며 보고서에 하나의 시각화만이 있지만 단계를 수행하는 데 여전히 유용합니다.

1. Power BI Desktop을 엽니다.
2. **홈** 탭에서 **게시**를 클릭합니다.

    ![Power BI Desktop에서 게시](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop에서 게시")

2. 데이터 집합 및 보고서를 게시하려는 작업 영역을 선택한 다음 **선택**을 클릭합니다. 다음 이미지에서 기본 **내 작업 영역**이 선택됩니다.

    ![데이터 집합 및 보고서를 게시하려는 작업 영역 선택](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "데이터 집합 및 보고서를 게시하려는 작업 영역 선택") 

3. 게시에 성공한 후 **Power BI에서 'BuildingTemperature.pbix' 열기**를 클릭합니다.

    ![게시 성공, 클릭하여 자격 증명 입력](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "게시 성공, 클릭하여 자격 증명 입력") 

4. Power BI 서비스에서 **자격 증명 입력**을 클릭합니다.

    ![Power BI 서비스에서 자격 증명 입력](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI 서비스에서 자격 증명 입력")

5. **자격 증명 편집**을 클릭합니다.

    ![Power BI 서비스에서 자격 증명 편집](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI 서비스에서 자격 증명 편집")

6. HDInsight 로그인 계정 정보를 입력한 다음 **로그인**을 클릭합니다. 기본 계정 이름은 *admin*입니다.

    ![Spark 클러스터에 로그인](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark 클러스터에 로그인")

7. 왼쪽 창에서 **작업 영역** > **내 작업 영역** > **보고서**로 이동한 다음 **BuildingTemperature**를 클릭합니다.

    ![왼쪽 창의 보고서 아래에 나열된 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "왼쪽 창의 보고서 아래에 나열된 보고서")

    왼쪽 창의 **데이터 집합** 아래에 나열된 **BuildingTemperature**도 표시됩니다.

    이제 Power BI Desktop에서 만든 시각적 개체를 Power BI 서비스에서 사용할 수 있습니다. 

8. 시각화 위로 커서를 이동한 후 오른쪽 위 모서리의 핀 고정 아이콘을 클릭합니다.

    ![Power BI 서비스의 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI 서비스의 보고서")

9. "새 대시보드"를 선택하고, 이름 `Building temperature`를 입력한 다음 **고정**을 클릭합니다.

    ![새 대시보드에 고정](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "새 대시보드에 고정")

10. 보고서에서 **대시보드로 이동**을 클릭합니다. 

시각적 개체가 대시보드에 고정됩니다. 다른 시각적 개체를 보고서에 추가하고 동일한 대시보드에 고정할 수 있습니다. 보고서 및 대시보드에 대한 자세한 내용은 [Power BI의 보고서](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)및 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)를 참조하세요.

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

- Power BI를 사용하여 Spark 데이터 시각화

다음 문서로 진행하여 Spark에 등록된 데이터를 Power BI와 같은 BI 분석 도구로 가져오는 방법을 확인하세요. 
> [!div class="nextstepaction"]
> [Spark 스트리밍 작업 실행](apache-spark-eventhub-streaming.md)

