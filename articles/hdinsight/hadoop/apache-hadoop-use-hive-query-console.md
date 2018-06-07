---
title: HDInsight의 쿼리 콘솔에서 Hadoop Hive 사용 - Azure | Microsoft Docs
description: 브라우저에서 웹 기반 쿼리 콘솔을 사용하여 HDInsight Hadoop 클러스터에 대한 Hive 쿼리를 실행하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 04a6ad67fec4145d8f9164743b08f9e105778091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="run-hive-queries-using-the-query-console"></a>쿼리 콘솔을 사용하여 Hive 쿼리 실행
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

이 문서에서는 브라우저에서 HDInsight Hadoop 클러스터의 Hive 쿼리를 실행하려면 HDInsight 쿼리 콘솔을 사용하는 방법에 대해 배웁니다.

> [!IMPORTANT]
> HDInsight 쿼리 콘솔은 Windows 기반 HDInsight 클러스터에서만 사용할 수 있습니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.
>
> HDInsight 3.4 이상의 경우 웹 브라우저에서 Hive 쿼리 실행에 대한 자세한 내용은 [Ambari Hive 보기에서 Hive 쿼리 실행](apache-hadoop-use-hive-ambari-view.md)을 참조하세요.

## <a id="prereq"></a>필수 조건
이 문서의 단계를 완료하려면 다음이 필요합니다.

* Windows 기반 HDInsight Hadoop 클러스터
* 최신 웹 브라우저

## <a id="run"></a> 쿼리 콘솔을 사용하여 Hive 쿼리 실행
1. 웹 브라우저를 열고 **https://CLUSTERNAME.azurehdinsight.net**으로 이동합니다. 여기서 **CLUSTERNAME**은 HDInsight 클러스터의 이름입니다. 메시지가 표시되면 클러스터를 만들 때 사용한 사용자 이름과 암호를 입력합니다.
2. 페이지 위쪽에 있는 링크 중 **Hive 편집기**를 선택합니다. HDInsight 클러스터에서 실행하려는 HiveQL 문을 입력하는 데 사용할 수 있는 양식이 표시됩니다.

    ![Hive 편집기](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    `Select * from hivesampletable` 텍스트를 다음 HiveQL 문으로 바꿉니다.

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    이러한 문은 다음 작업을 수행합니다.

   * **DROP TABLE**: 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.
   * **CREATE EXTERNAL TABLE**: Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

     > [!NOTE]
     > 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.
     >
     >
   * **ROW FORMAT**: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
   * **STORED AS TEXTFILE LOCATION**: 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.
   * **SELECT**: **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 경우 이 값을 포함하는 행이 3개 있으므로 **3** 값이 반환되어야 합니다.
   * **INPUT__FILE__NAME LIKE '%.log'** - .log로 끝나는 파일의 데이터만 반환하도록 Hive에 지시합니다. 데이터를 포함하는 sample.log 파일로 검색을 제한하며, 정의한 스키마와 일치하지 않는 다른 예제 데이터 파일의 데이터가 반환되지 않도록 합니다.
3. **제출**을 클릭합니다. 페이지 아래쪽의 **작업 세션** 에는 작업에 대한 세부 정보가 표시됩니다.
4. **상태** 필드가 **완료**로 변경되면 작업에 대한 **세부 정보 보기**를 선택합니다. 세부 정보 페이지의 **작업 출력**에는 `[ERROR]    3`이 포함됩니다. 이 필드 아래의 **다운로드** 단추를 사용하여 작업의 출력을 포함하는 파일을 다운로드할 수 있습니다.

## <a id="summary"></a>요약
여기에서 볼 수 있듯이 Query 콘솔은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

Hive 쿼리 콘솔을 사용하여 Hive 작업을 실행하는 방법에 대한 자세한 내용을 보려면 쿼리 콘솔의 위쪽에서 **시작** 을 선택한 다음 제공되는 샘플을 사용합니다. 각 샘플은 샘플에 사용된 HiveQL 문의 설명을 포함하여 Hive를 사용하여 데이터를 분석하는 프로세스를 안내합니다.

## <a id="nextsteps"></a>다음 단계
HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Hive와 함께 Tez를 사용하는 경우 디버깅 정보에 대한 다음 문서를 참조하세요.

* [Windows 기반 HDInsight 클러스터에서 Tez UI 사용](../hdinsight-debug-tez-ui.md)
* [Linux 기반 HDInsight에서 Ambari Tez 보기 사용](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
