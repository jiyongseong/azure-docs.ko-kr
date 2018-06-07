---
title: HDInsight|Microsoft Docs에서 Hadoop Pig 사용
description: HDInsight에서 Hadoop와 함께 Pig를 사용하는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d382293ce815f24ad635b50c920d7988b664d79c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>HDInsight에서 Hadoop과 Pig 사용

HDInsight에서 [Apache Pig](http://pig.apache.org/)를 사용하는 방법을 알아봅니다.

Pig는 *Pig Latin*이라는 절차형 언어를 사용하여 Hadoop용 프로그램을 생성하기 위한 플랫폼입니다. Pig는 *MapReduce* 을 만드는 Java를 대체하는 솔루션이며 Azure HDInsight와 함께 포함됩니다. 다음 표를 사용하여 HDInsight에서 Pig를 사용하는 다양한 방법을 검색하세요.

| **이것을 사용** 하세요... | ... **대화형** 셸 | ...**배치** 처리 | ... **클러스터 운영 체제** | ... **클라이언트 운영 체제** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 또는or Windows |Linux, Unix, Mac OS X, 또는 Windows |
| [Hadoop용 .NET SDK](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 또는or Windows |Windows(당분간) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 또는or Windows |Windows |

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a id="why"></a>Pig를 사용하는 이유

Hadoop의 MapReduce를 사용하여 데이터를 처리할 때 문제는 지도 및 reduce 함수만을 사용하여 처리 논리를 구현하는 것입니다. 복잡한 처리를 위해 종종 처리를 같이 연결된 여러 MapReduce 작업으로 나누어야 원하는 결과를 얻을 수 있습니다.

Pig를 사용하면 원하는 출력을 생산하기 위해 데이터가 통과하는 일련의 변환으로 처리를 규정할 수 있게 됩니다.

원하는 출력을 생산하기 위해 Pig Litin 언어를 사용하여 하나 이상의 변환을 거쳐 원시 입력을 데이터 플로로 설명할 수 있습니다. Pig Latin 프로그램이 일반적인 패턴을 따릅니다:

* **부하**: 파일 시스템에서 조작할 데이터 읽기

* **변환**: 데이터 조작

* **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장

### <a name="user-defined-functions"></a>사용자 정의 함수

또한 Pig Latin는 사용자가 Pig Latin의 모델에 어려운 논리를 구현하는 외부 구성 요소를 적용할 수 있도록 사용자 정의 함수(UDF)를 지원합니다.

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) 및 [Pig Latin 참조 설명서 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)를 참조하십시오.

Pig와 UDF를 사용하는 예로, 다음 문서를 참조하십시오:

* [HDInsight에서 HDInsight와 함께 Pig 사용](apache-hadoop-use-pig-datafu-udf.md) - DataFu는 Apache에서 유지 관리하는 유용한 UDF의 컬렉션입니다.
* [HDInsight에서 Pig 및 Hive와 함께 Python 사용](python-udf-hdinsight.md)
* [HDInsight에서 Hive 및 Pig와 함께 C# 사용](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>예제 데이터

HDInsight에서는 `/example/data` 및 `/HdiSamples` 디렉터리에 저장되는 다양한 예제 데이터 집합을 제공합니다. 이러한 디렉터리는 클러스터의 기본 저장소에 있습니다. 이 문서의 Pig 예제는 `/example/data/sample.log`의 *log4j* 파일을 사용합니다.

파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]`  필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

이전 예에서 로그 수준은 ERROR입니다.

> [!NOTE]
> [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 도구 로깅하여 log4j 파일을 생성하고 해당 파일을 사용자의 blob에 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드](../hdinsight-upload-data.md) 를 참조하세요. HDInsight와 함께 Azure Blob Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob Storage 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

## <a id="job"></a>예제 작업

다음 Pig Latin 작업은 HDInsight 클러스터의 기본 저장소에서 `sample.log` 파일을 로드합니다. 그런 다음 입력된 데이터에서 각 로그 수준이 발생한 횟수로 나타나는 일련의 변환을 수행합니다. 결과는 STDOUT에 기록됩니다.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

다음 그림에서는 각 변환으로 인해 데이터에 수행되는 작업을 요약해서 보여 줍니다.

![변환의 그래픽 표현][image-hdi-pig-data-transformation]

## <a id="run"></a>Pig Latin 작업 실행

HDInsight는 다양한 메서드를 사용하여 Pig Latin 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르십시오.

| **이것을 사용** 하세요... | ... **대화형** 셸 | ...**배치** 처리 | ... **클러스터 운영 체제** | ...**클라이언트** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux 또는or Windows |Linux, Unix, Mac OS X, 또는 Windows |
| [Hadoop용 .NET SDK](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux 또는or Windows |Windows(당분간) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux 또는or Windows |Windows |

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="pig-and-sql-server-integration-services"></a>Pig 및 SQL Server Integration Services

SSIS(SQL Server Integration Services)를 사용하여 Pig 작업을 실행할 수 있습니다. Azure Feature Pack for SSIS는 HDInsight에서 Pig 작업을 하는 다음 구성 요소를 제공합니다.

* [Azure HDInsight Pig 작업][pigtask]

* [Azure 구독 연결 관리자][connectionmanager]

[여기][ssispack]에서 Azure Feature Pack for SSIS에 대해 자세히 알아보세요.

## <a id="nextsteps"></a>다음 단계
Scalding을 사용하여 HDInsight와 함께 Pig를 사용하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md)
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Sqoop 사용](hdinsight-use-sqoop.md)
* [HDInsight에서 Oozie 사용](../hdinsight-use-oozie.md)
* [HDInsight에서 MapReduce 작업 사용][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
