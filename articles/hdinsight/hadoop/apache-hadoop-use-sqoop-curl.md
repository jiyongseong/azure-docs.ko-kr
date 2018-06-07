---
title: HDInsight에서 Curl과 Hadoop Sqoop 사용 - Azure | Microsoft Docs
description: 원격으로 Curl을 사용하여 HDInsight에 Sqoop 작업을 제출하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: a83b87f1ed052c6d21d337eb37bc560efbf118ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Curl을 사용하여 HDInsight에서 Hadoop으로 Sqoop 작업 실행
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight의 Hadoop 클러스터에서 Curl을 사용하여 Sqoop 작업을 실행하는 방법에 대해 배웁니다.

Sqoop 작업을 실행하고 모니터링하며 결과를 검색하는 원시 HTTP 요청을 사용하여 HDInsight와 상호 작용하는 방법을 설명하는 데 Curl을 사용합니다. 이 Curl은 HDInsight 클러스터에서 제공하는 WebHCat REST API(이전의 Templeton)를 사용하여 작동합니다.

## <a name="prerequisites"></a>필수 조건
이 문서의 단계를 완료하려면 다음이 필요합니다.

* [HDInsight에서 Hadoop과 Sqoop 사용](hdinsight-use-sqoop.md#create-cluster-and-sql-database)을 완료하여 HDInsight 클러스터 및 Azure SQL Database로 환경을 구성합니다.
* [Curl](http://curl.haxx.se/). Curl은 HDInsight 클러스터에서(로) 데이터를 전송하는 도구입니다.
* [jq](http://stedolan.github.io/jq/). jq 유틸리티는 REST 요청에서 반환된 JSON 데이터를 처리하는 데 사용합니다.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Curl을 사용하여 Sqoop 작업 제출
> [!NOTE]
> WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.
> 
> 이 섹션의 명령에서 **USERNAME**은 클러스터에 대해 인증할 사용자로 바꾸고 **PASSWORD**는 사용자 계정의 암호로 바꿉니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
> 
> REST API는 [기본 인증](http://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.
> 
> 

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    그러면 다음과 같은 응답이 표시됩니다.

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    이 명령에서 사용된 매개 변수는 다음과 같습니다.
   
   * **-u** - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
   * **-G** - GET 요청임을 나타냅니다.
     
     URL의 시작 부분, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에 대해 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 
2. 다음을 사용하여 Sqoop 작업을 제출합니다.

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-d** - `-G`가 사용되지 않으므로 요청은 POST 메서드로 기본 설정됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.

        * **user.name** - 명령을 실행하는 사용자입니다.

        * **명령** - 실행할 Sqoop 명령입니다.

        * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

    이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. 작업 상태를 확인하려면 다음 명령을 사용합니다. **JOBID** 를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 `{"id":"job_1415651640909_0026"}`인 경우 **JOBID**는 `job_1415651640909_0026`이 됩니다.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    작업이 완료되면 상태는 **SUCCEEDED**가 됩니다.
   
   > [!NOTE]
   > 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. jq는 상태 값을 검색하는 데만 사용됩니다.
   > 
   > 
4. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 **wasb:///example/data/sqoop/curl**)가 포함됩니다. 이 주소는 HDInsight 클러스터에서 사용하는 기본 저장소 컨테이너의 **example/data/sqoop/curl** 디렉터리에 작업의 출력을 저장합니다.
   
    Azure Portal을 사용하여 stderr 및 stdout Blob에 액세스할 수 있습니다.  또한 Microsoft SQL Server Management Studio를 사용하여 log4jlogs 테이블에 업로드된 데이터를 검사할 수도 있습니다.

## <a name="limitations"></a>제한 사항
* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL Database에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 현재 대량 삽입을 지원하지 않습니다.
* Batch - Linux 기반 HDInsight와 함께 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 삽입 작업을 수행합니다.

## <a name="summary"></a>요약
이 문서에서 볼 수 있듯이 원시 HTTP 요청을 사용하여 HDInsight 클러스터의 Sqoop 작업을 실행하고 모니터링하며 결과를 확인할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API 가이드</a>를 참조하세요.

## <a name="next-steps"></a>다음 단계
HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Sqoop 사용](hdinsight-use-sqoop.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

curl 관련 기타 HDInsight 문서:
 
* [Azure REST API를 사용하여 Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [REST를 사용하여 HDInsight에서 Hadoop으로 Hive 쿼리 실행](apache-hadoop-use-hive-curl.md)
* [REST를 사용하여 HDInsight에서 Hadoop으로 MapReduce 작업 실행](apache-hadoop-use-mapreduce-curl.md)
* [cURL을 사용하여 HDInsight에서 Hadoop과 Pig 작업 실행](apache-hadoop-use-pig-curl.md)



