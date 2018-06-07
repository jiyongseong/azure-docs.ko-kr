---
title: Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화 | Microsoft Docs
description: Microsoft Power BI를 사용하여 Azure HDInsight에서 처리한 대화형 쿼리 Hive 데이터를 시각화하는 방법에 대해 알아봅니다.
keywords: hdinsight,hadoop,hive,대화형 쿼리,대화형 hive,LLAP,directquery
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: b8da1f17b9e477caf9031cf94ee14f3a181e247e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Azure HDInsight에서 직접 쿼리를 사용하여 Microsoft Power BI에서 대화형 쿼리 Hive 데이터 시각화

Microsoft Power BI를 Azure HDInsight 대화형 쿼리 클러스터에 연결하고 직접 쿼리를 사용하여 Hive 데이터를 시각화하는 방법에 대해 알아봅니다. 이 자습서에서는 hivesampletable Hive 테이블의 데이터를 Power BI에 로드합니다. Hive 테이블에는 일부 휴대폰 사용 현황 데이터가 포함되어 있습니다. 그런 다음 전 세계 맵에 사용 현황 데이터를 그림으로 나타냅니다.

![HDInsight Power BI 맵 보고서](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

[Hive ODBC 드라이버](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 활용하여 Power BI Desktop에서 제네릭 ODBC 커넥터를 통해 가져오기를 수행할 수 있습니다. 그러나 BI 워크로드의 경우 Hive 쿼리 엔진의 비대화형 특성을 지정하지 않는 것이 좋습니다. 성능을 개선하기 위해 [대화형 쿼리 HDInsight 커넥터](./apache-hadoop-connect-hive-power-bi-directquery.md) 및 [HDInsight Spark 커넥터](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)를 선택할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 클러스터**. 클러스터는 Hive를 사용한 HDInsight 클러스터 또는 새로 릴리스된 대화형 쿼리 클러스터일 수 있습니다. 클러스터를 만드는 방법은 [클러스터 만들기](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 복사본을 다운로드할 수 있습니다.

## <a name="load-data-from-hdinsight"></a>HDInsight에서 데이터 로드

hivesampletable Hive 테이블은 모든 HDInsight 클러스터와 함께 제공됩니다.

1. Power BI Desktop에 로그인합니다.
2. **홈** 탭을 클릭하고 **외부 데이터** 리본에서 **데이터 가져오기**를 선택한 후 **자세히**를 선택합니다.

    ![HDInsight Power BI 데이터 열기](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. **데이터 가져오기** 창에서 검색 상자에 **hdinsight**를 입력합니다. **HDInsight 대화형 쿼리(베타)**가 표시되지 않으면 Power BI Desktop을 최신 버전으로 업데이트해야 합니다.
4. **HDInsight 대화형 쿼리(베타)**를 클릭하고 **연결**을 클릭합니다.
5. **계속**을 클릭하여 **커넥터 미리 보기** 경고 대화 상자를 닫습니다.
6. **HDInsight 대화형 쿼리**에서 다음 정보를 선택하거나 입력합니다.

    - **서버**: 대화형 쿼리 클러스터 이름을 입력합니다(예: *myiqcluster.azurehdinsight.net*).
    - **데이터베이스**: 이 자습서에서는 **default**를 입력합니다.
    - **데이터 연결 모드**: 이 자습서에서는 **DirectQuery**를 선택합니다.

    ![HDInsight 대화형 쿼리 power bi directquery 연결](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. **확인**을 클릭합니다.
8. HTTP 사용자 자격 증명을 입력한 다음 **확인**을 클릭합니다.  기본 사용자 이름은 **admin**입니다.
9. 왼쪽 창에서 **hivesampletale**을 선택하고 **로드**를 클릭합니다.

    ![HDInsight 대화형 쿼리 power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>데이터 가상화

마지막 절차에서 계속 진행합니다.

1. 시각화 창에서 **맵**을 선택합니다.  지구 아이콘입니다.

    ![HDInsight Power BI 보고서 사용자 지정](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. 필드 창에서 **country** 및 **devicemake**를 선택합니다. 맵에 표시된 데이터를 볼 수 있습니다.
3. 맵을 확장합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 ODBC를 사용하여 Microsoft Power BI로 Hive 데이터 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리 실행](./../hdinsight-connect-hive-zeppelin.md).
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결하고 Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)
