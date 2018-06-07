---
title: 사용자 지정 MapReduce 프로그램 실행 - Azure HDInsight | Microsoft Docs
description: HDInsight에서 사용자 지정 MapReduce 프로그램을 실행하는 경우 및 방법입니다.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 94d199642b409a2fd087ec1543651031a907d09f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="run-custom-mapreduce-programs"></a>사용자 지정 MapReduce 프로그램 실행

HDInsight 등의 Hadoop 기반 빅 데이터 시스템에서는 광범위한 도구와 기술을 사용하여 데이터를 처리할 수 있습니다. 다음 표에서는 각각의 주요 장점과 고려 사항에 대해 설명합니다.

| 쿼리 메커니즘 | 장점 | 고려 사항 |
| --- | --- | --- |
| **HiveQL을 사용하는 Hive** | <ul><li>변경할 수 없는 대량 데이터의 일괄 처리 및 분석, 데이터 요약, 주문형 쿼리를 위한 뛰어난 솔루션입니다. 익숙한 SQL 유사 구문을 사용합니다.</li><li>이 솔루션을 사용하면 쉽게 분할 및 인덱싱할 수 있는 영구적 데이터 테이블을 생성할 수 있습니다.</li><li>동일한 데이터에 대해 여러 개의 외부 테이블과 뷰를 만들 수 있습니다.</li><li>데이터 저장 및 처리를 위해 다양한 스케일 아웃 및 내결함성 기능을 제공하는 간단한 데이터 웨어하우스 구현을 지원합니다.</li></ul> | <ul><li>원본 데이터에 적어도 식별 가능한 구조가 있어야 합니다.</li><li>실시간 쿼리 및 행 수준 업데이트에는 적합하지 않습니다. 대규모 데이터 집합에 대한 일괄 작업에 가장 적합합니다.</li><li>일부 유형의 복잡한 처리 작업은 수행하지 못할 수도 있습니다.</li></ul> |
| **Pig Latin을 사용하는 Pig** | <ul><li>데이터를 집합으로 조작하고, 데이터 집합을 병합 및 필터링하고, 레코드 또는 레코드 그룹에 함수를 적용하고, 열 정의, 값 그룹화, 열을 행으로 변환 등을 통해 데이터를 재구성하기 위한 뛰어난 솔루션입니다.</li><li>워크플로 기반 접근 방법을 데이터에 대한 작업 시퀀스로 사용할 수 있습니다.</li></ul> | <ul><li>SQL 사용자는 Pig Latin이 HiveQL보다 더 낯설고 사용하기 어려울 수도 있습니다.</li><li>일반적으로 기본 출력은 텍스트 파일이므로 Excel 등의 시각화 도구에서 사용하기 어려울 수 있습니다. 일반적으로 출력 위에 Hive 테이블을 놓습니다.</li></ul> |
| **사용자 지정 Map/Reduce** | <ul><li>Map/Reduce 단계와 실행을 완벽하게 제어할 수 있습니다.</li><li>이 경우 쿼리를 최적화하여 클러스터의 성능을 최대화하거나 서버와 네트워크의 부하를 최소화할 수 있습니다.</li><li>잘 알려진 광범위한 언어로 구성 요소를 작성할 수 있습니다.</li></ul> | <ul><li>자체 Map/Reduce 구성 요소를 만들어야 하므로 Pig 또는 Hive를 사용하는 것보다 더 어렵습니다.</li><li>데이터 집합을 결합해야 하는 프로세스는 구현하기 더 어렵습니다.</li><li>사용 가능한 테스트 프레임워크가 있지만 코드가 Hadoop 작업 스케줄러에 의해 제어되는 일괄 작업으로 실행되므로 코드 디버그가 일반 응용 프로그램보다 더 복잡합니다.</li></ul> |
| **HCatalog** | <ul><li>저장소의 경로 정보를 추상화하므로 관리가 더 용이하며 사용자가 데이터 저장 위치를 몰라도 됩니다.</li><li>데이터 가용성 등의 이벤트 알림을 사용할 수 있으므로 Oozie 등의 다른 도구가 작업이 발생한 시기를 감지할 수 있습니다.</li><li>키별 분할을 비롯한 관계형 데이터 뷰를 노출하고 데이터에 쉽게 액세스할 수 있도록 합니다.</li></ul> | <ul><li>기본적으로 RCFile, CSV 텍스트, JSON 텍스트, SequenceFile 및 ORC 파일 형식을 지원하지만 다른 형식의 경우 사용자 지정 SerDe를 작성해야 할 수도 있습니다.</li><li>HCatalog는 스레드로부터 안전하지 않습니다.</li><li>Pig 스크립트에서 HCatalog 로더를 사용하는 경우 열의 데이터 형식에 대한 몇 가지 제한 사항이 있습니다. 자세한 내용은 Apache HCatalog 설명서에서 [HCatLoader 데이터 형식](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes)을 참조하세요.</li></ul> |

일반적으로 이러한 접근 방법 중 필요한 결과를 제공할 수 있는 가장 간단한 방법을 사용합니다. 예를 들어 Hive만 사용하여 이러한 결과를 얻을 수도 있지만 더 복잡한 시나리오의 경우 Pig를 사용하거나 자체 Map/Reduce 구성 요소를 작성해야 할 수도 있습니다. 또한 Hive 또는 Pig를 실험한 후 사용자 지정 Map/Reduce 구성 요소가 처리 세분화 및 최적화를 허용하여 더 나은 성능을 제공할 수 있다고 결정할 수 있습니다.

## <a name="custom-mapreduce-components"></a>사용자 지정 Map/Reduce 구성 요소

Map/Reduce 코드는 **Map** 및 **Reduce** 구성 요소로 구현된 두 개의 개별 함수로 이루어집니다. **Map** 구성 요소는 여러 클러스터 노드에서 병렬로 실행되며, 각 노드에서 노드의 데이터 하위 집합에 매핑이 적용됩니다. **Reduce** 구성 요소는 모든 맵 함수의 결과를 정렬 및 요약합니다. 이러한 두 구성 요소에 대한 자세한 내용은 [HDInsight의 Hadoop에 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

대부분의 HDInsight 처리 시나리오에서는 Pig 또는 Hive와 같은 상위 수준 추상화를 사용하는 것이 더 간단하고 효율적입니다. Hive 스크립트 내에서 사용할 사용자 지정 Map/Reduce 구성 요소를 만들어 보다 정교한 처리를 수행할 수도 있습니다.

사용자 지정 Map/Reduce 구성 요소는 일반적으로 Java로 작성됩니다. Hadoop은 C#, F#, Visual Basic, Python, JavaScript 등의 다른 언어로 개발된 구성 요소를 사용할 수 있게 해주는 스트리밍 인터페이스를 제공합니다.

* 사용자 지정 Java MapReduce 프로그램 개발 연습은 [HDInsight에서 Hadoop용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)을 참조하세요.
* Python을 사용하는 예제를 보려면 [HDInsight용 Python 스트리밍 MapReduce 프로그램 개발](apache-hadoop-streaming-python.md)을 참조하세요.

다음 조건에 대한 자체 Map/Reduce 구성 요소를 만드는 것이 좋습니다.

* 데이터를 구문 분석하고 사용자 지정 논리를 통해 데이터에서 구조화된 정보를 가져와서 완전히 구조화되지 않은 데이터를 처리해야 합니다.
* UDF를 만들지 않고 Pig 또는 Hive로 표현하기 어려운(또는 불가능한) 복잡한 작업을 수행하려고 합니다. 예를 들어 외부 지오코딩 서비스를 사용하여 원본 데이터의 위도 및 경도 좌표나 IP 주소를 지리적 위치 이름으로 변환해야 할 수도 있습니다.
* Hadoop 스트리밍 인터페이스를 사용하여 Map/Reduce 구성 요소에서 기존 .NET, Python 또는 JavaScript 코드를 다시 사용하려고 합니다.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>사용자 지정 MapReduce 프로그램 업로드 및 실행

가장 일반적인 MapReduce 프로그램은 Java로 작성되고 jar 파일로 컴파일됩니다.

1. MapReduce 프로그램을 개발, 컴파일 및 테스트한 후에는 `scp` 명령을 사용하여 jar 파일을 헤드 노드에 업로드합니다.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **USERNAME**을 클러스터의 SSH 사용자 계정으로 바꿉니다. **CLUSTERNAME** 을 클러스터 이름으로 바꿉니다. 암호를 사용하여 SSH 계정을 보호할 경우 암호를 입력하라는 메시지가 나타납니다. 인증서를 사용하는 경우, `-i` 매개 변수를 사용하여 개인 키를 지정해야 합니다.

2. [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)를 사용하여 클러스터에 연결합니다.

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. SSH 세션에서 YARN을 통해 MapReduce 프로그램을 실행합니다.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    이 명령은 MapReduce 작업을 YARN에 제출합니다. 입력된 파일은 `/example/data/sample.log`이며 출력 디렉터리는 `/example/data/logoutput`입니다. 입력 파일과 모든 출력 파일은 클러스터의 기본 저장소에 저장됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Hadoop에서 MapReduce 스트리밍과 함께 C# 사용](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight에서 Hadoop용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [HDInsight용 Python 스트리밍 MapReduce 프로그램 개발](apache-hadoop-streaming-python.md)
* [Eclipse용 Azure 도구 키트를 사용하여 HDInsight 클러스터용 Spark 응용 프로그램 만들기](../spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Hive 및 Pig와 함께 Python UDF(사용자 정의 함수) 사용](python-udf-hdinsight.md)
