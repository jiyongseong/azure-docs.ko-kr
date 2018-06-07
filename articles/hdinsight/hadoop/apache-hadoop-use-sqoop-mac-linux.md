---
title: Hadoop과 Apache Sqoop - Azure HDInsight | Microsoft Docs
description: Apache Sqoop을 사용하여 HDInsight의 Hadoop과 Azure SQL Database 간에 가져오기 및 내보내기를 수행하는 방법을 알아봅니다.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 38b1c57b9ac666bc908df69b29f72fbd5aea0495
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop을 사용하여 HDInsight의 Hadoop과 SQL Database 간에 데이터 가져오기 및 내보내기

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apache Sqoop을 사용하여 Azure HDInsight의 Hadoop 클러스터와 Azure SQL Database 또는 Microsoft SQL Server Database 사이에서 가져오기 및 내보내기를 수행하는 방법을 알아봅니다. 이 문서의 단계에서는 Hadoop 클러스터의 헤드에서 직접 `sqoop` 명령을 사용합니다. SSH를 사용하여 헤드 노드에 연결하고 이 문서의 명령을 실행합니다.

> [!IMPORTANT]
> 이 문서의 단계는 Linux를 사용하는 HDInsight 클러스터에만 적용됩니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

> [!WARNING]
> 이 문서의 단계에서는 `sqooptest`라는 Azure SQL Database를 이미 만들었다고 가정합니다.
>
> 이 문서에서는 SQL Database에서 테이블을 만들고 쿼리하는 데 사용되는 T-SQL 문을 제공합니다. SQL Database에 이러한 문을 사용할 수 있는 많은 클라이언트가 있습니다. 다음 클라이언트가 권장됩니다.
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) 유틸리티

## <a name="create-the-table-in-sql-database"></a>SQL Database에 테이블 만들기

> [!IMPORTANT]
> [클러스터 및 SQL Database 만들기](hdinsight-use-sqoop.md)에서 만든 HDInsight 클러스터 및 SQL Database를 사용하고 있는 경우 이 섹션의 단계를 무시하십시오. 데이터베이스 테이블은 [클러스터 및 SQL Database 만들기](hdinsight-use-sqoop.md)의 단계 중에 생성된 것입니다.

SQL 클라이언트를 사용하여 SQL Database의 `sqooptest` 데이터베이스에 연결합니다. 그런 후 다음 T-SQL을 사용하여 `mobiledata`라는 테이블을 만듭니다.

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Sqoop 내보내기

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 예를 들어 다음 명령은 `mycluster`라는 클러스터의 기본 헤드 노드에 연결합니다.

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Sqoop이 SQL Database를 볼 수 있는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    확인 메시지가 표시되면 SQL Database 로그인의 암호를 입력합니다.

    이 명령을 실행하면 앞에서 사용한 **sqooptest** 데이터베이스를 포함한 데이터베이스 목록이 반환됩니다.

3. SQL Database에서 Hive **hivesampletable** 테이블의 데이터를 **mobiledata** 테이블로 내보내려면 다음 명령을 사용합니다.

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. 데이터를 내보냈는지 확인하려면 SQL 클라이언트에서 다음 쿼리를 사용하여 내보낸 데이터를 확인합니다.

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    이 명령은 테이블로 가져온 50개 행을 나열합니다.

## <a name="sqoop-import"></a>Sqoop 가져오기

1. 다음 명령을 사용하여 SQL Database의 **mobiledata** 테이블에서 HDInsight의 **wasb:///tutorials/usesqoop/importeddata** 디렉터리로 데이터를 가져옵니다.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    데이터의 필드는 탭 문자로 구분되어 있으며 줄은 줄 바꿈 문자로 종료됩니다.

    > [!IMPORTANT]
    > `wasb:///` 경로는 Azure Storage를 기본 클러스터 저장소로 사용하는 클러스터에 작동합니다. Azure Data Lake Store를 사용하는 클러스터에서는 `adl:///`을 대신 사용합니다.

2. 가져오기가 완료되면 다음 명령을 사용하여 새로운 디렉터리에 데이터를 나열합니다.

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server 사용하기

또한 Sqoop를 사용하여 SQL Server에서 데이터를 가져오고 내보낼 수도 있습니다. SQL Database와 SQL Server의 차이점은 다음과 같습니다.

* HDInsight와 SQL Server가 모두 동일한 Azure Virtual Network에 있어야 합니다.

    예제를 보려면 [온-프레미스 네트워크에 HDInsight 연결](./../connect-on-premises-network.md) 문서를 참조하세요.

    HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](../hdinsight-extend-hadoop-virtual-network.md) 문서를 참조하세요. Azure Virtual Network에 대한 자세한 내용은 [가상 네트워크 개요](../../virtual-network/virtual-networks-overview.md) 문서를 참조하세요.

* SQL Server는 SQL 인증을 허용하도록 구성되어야 합니다. 자세한 내용은 [인증 모드 선택](https://msdn.microsoft.com/ms144284.aspx) 문서를 참조하세요.

* SQL Server를 원격 연결을 허용하도록 구성해야 할 수 있습니다. 자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)(영문)을 참조하십시오.

* 다음 Transact-SQL 문을 사용하여 **mobiledata** 테이블을 만듭니다.

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* HDInsight에서 SQL Server에 연결하는 경우 SQL Server의 IP 주소를 사용해야 할 수 있습니다. 예: 

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>제한 사항

* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL Database에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 대량 삽입을 지원하지 않습니다.

* Batch - Linux 기반 HDInsight에서 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop은 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight와 함께 Oozie 사용](../hdinsight-use-oozie.md): Oozie 워크플로에서 Sqoop 작업을 사용합니다.
* [HDInsight를 사용하여 비행 지연 데이터 분석](../hdinsight-analyze-flight-delay-data.md): Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
