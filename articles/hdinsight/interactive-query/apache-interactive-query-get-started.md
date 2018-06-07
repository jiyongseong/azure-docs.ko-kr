---
title: Azure HDInsight에서 Interactive Query 사용 | Microsoft Docs
description: HDInsight에서 Interactive Query(Hive LLAP)를 사용하는 방법에 대해 알아봅니다.
keywords: ''
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 9450f60b88c63e2cd818130521d67daf435f0b17
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight에서 대화형 쿼리 사용
Interactive Query(Hive LLAP 또는 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)라고도 함)는 Azure HDInsight [클러스터 유형](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)입니다. Interactive Query에서는 메모리 내 캐싱을 지원하여 Hive 쿼리를 더 강화된 대화형 방식으로 더 빠르게 수행할 수 있습니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Interactive Query 클러스터는 Hadoop 클러스터와 다릅니다. Hive 서비스만 포함합니다. 

> [!NOTE]
> Interactive Query 클러스터의 Hive 서비스는 Ambari Hive 보기, Beeline 및 Microsoft Hive ODBC(Open Database Connectivity) Driver를 통해서만 액세스할 수 있습니다. Hive 콘솔, Templeton, Azure CLI(Azure 명령줄 도구) 또는 Azure PowerShell을 통해서는 액세스할 수 없습니다. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>대화형 쿼리 클러스터 만들기
HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 대화형 쿼리 클러스터 형식을 선택합니다.

## <a name="execute-hive-queries-from-interactive-query"></a>Interactive Query에서 Hive 쿼리 실행
Hive 쿼리를 실행하려면 다음 옵션이 있어야 합니다.

* Power BI 사용

    [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](./apache-hadoop-connect-hive-power-bi-directquery.md) 참조 [Azure HDInsight에서 Power BI를 사용하여 빅 데이터 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 참조하세요.
 
* Zeppelin 사용

    [Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리 실행](../hdinsight-connect-hive-zeppelin.md)을 참조하세요.

* Visual Studio 사용

    [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)을 참조하세요.

* Visual Studio Code 사용

    [Hive, LLAP 또는 pySpark용 Visual Studio Code](../hdinsight-for-vscode.md)를 참조하세요.
* Ambari Hive 보기를 사용하여 Hive 실행
  
    [Azure HDInsight에서 Hadoop과 Hive 보기 사용](../hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요.
* Beeline을 사용하여 Hive 실행
  
    [Beeline를 사용하여 HDInsight에서 Hadoop과 Hive 사용](../hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요.
  
    헤드 노드 또는 빈 에지 노드에서 Beeline을 사용할 수 있습니다. 빈 에지 노드에서 Beeline을 사용하는 것이 좋습니다. 빈 에지 노드를 사용하여 HDInsight 클러스터를 만드는 방법에 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](../hdinsight-apps-use-edge-node.md)을 참조하세요.
* Hive ODBC를 사용하여 Hive 실행
  
    [Microsoft Hive ODBC Driver로 Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요.

JDBC(Java Database Connectivity) 연결 문자열을 찾으려면 다음을 수행합니다.

1. https://\<cluster name\>.AzureHDInsight.net URL을 사용하여 Ambari에 로그인합니다.
2. 왼쪽 메뉴에서 **Hive**를 선택합니다.
3. URL을 복사하려면 클립보드 아이콘을 선택합니다.
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Interactive Query 클러스터를 만드는 방법](../hdinsight-hadoop-provision-linux-clusters.md)에 대해 알아봅니다.
* [Azure HDInsight에서 Power BI를 사용하여 빅 데이터를 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)하는 방법에 대해 알아봅니다.
* [Azure HDInsight에서 Zeppelin을 사용하여 Hive 쿼리를 실행](../hdinsight-connect-hive-zeppelin.md)하는 방법에 대해 알아봅니다.
* [Data Lake Tools for Visual Studio를 사용하여 Hive 쿼리를 실행하는 방법](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries)에 대해 알아봅니다.
* [HDInsight Tools for Visual Studio Code를 사용](../hdinsight-for-vscode.md)하는 방법에 대해 알아봅니다.
* [HDInsight에서 Hadoop과 Hive 보기 사용](../hadoop/apache-hadoop-use-hive-ambari-view.md) 방법에 대해 알아봅니다.
* [Beeline을 사용하여 HDInsight에서 Hive 쿼리를 제출하는 방법](../hadoop/apache-hadoop-use-hive-beeline.md)을 알아봅니다.
* [Microsoft Hive ODBC 드라이버로 Hadoop에 Excel을 연결하는 방법](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 알아봅니다.

