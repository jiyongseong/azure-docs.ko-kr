---
title: Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리 실행 | Microsoft Docs
description: Zeppelin을 사용하여 Hive 쿼리를 실행하는 방법에 대해 알아봅니다.
keywords: hdinsight,hadoop,hive,대화형 쿼리,LLAP
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
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: c8fe65d2eadaede1d99befbf76c4d06fab9598fc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리 실행 

HDInsight 대화형 쿼리 클러스터에는 대화형 Hive 쿼리를 실행하는 데 사용할 수 있는 Zeppelin 노트북이 포함되어 있습니다. 이 문서에서는 Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리를 실행하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 대화형 쿼리 클러스터**. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.  대화형 쿼리 형식을 선택해야 합니다. 

## <a name="create-a-zeppelin-note"></a>Zeppelin 노트 만들기

1. 다음 URL로 이동합니다.

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.

2. Hadoop 사용자 이름 및 암호를 입력합니다. Zeppelin 페이지에서 새 노트를 만들거나 기존 노트를 열 수 있습니다. HiveSample에는 몇 가지 샘플 Hive 쿼리가 포함되어 있습니다.  

    ![HDInsight 대화형 쿼리 zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. **새 노트 만들기**를 클릭합니다.
4. 다음 값을 입력하거나 선택합니다.

    - 노트 이름: 노트의 이름을 입력합니다.
    - 기본 인터프리터: **JDBC**를 선택합니다.

5. **노트 만들기**를 클릭합니다.
6. 다음 Hive 쿼리를 실행합니다.

        %jdbc(hive)
        show tables

    ![HDInsight 대화형 쿼리 zeppelin 쿼리 실행](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    첫 번째 줄의 **%jdbc(hive)** 문은 노트북이 Hive JDBC 인터프리터를 사용한다는 것을 나타냅니다.

    쿼리에서 *hivesampletable*이라는 하나의 Hive 테이블을 반환합니다.

    다음 두 개의 추가 쿼리는 hivesampletable에 대해 실행할 수 있는 Hive 쿼리입니다. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    기존의 Hive에 비해 쿼리 결과를 반환하는 속도가 훨씬 빠릅니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Hadoop에 Excel 연결](hadoop/apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결하고 Hive 쿼리 실행](hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./hdinsight-upload-data.md)
