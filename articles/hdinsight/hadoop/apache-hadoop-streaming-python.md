---
title: HDInsight에서 Python MapReduce 작업 개발 - Azure | Microsoft Docs
description: MapReduce 작업을 스트리밍하는 데 Python을 사용하는 방법 알아보기 Hadoop은 Java 이외의 언어로 작성하기 위해 MapReduce용 스트리밍 API를 제공합니다.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: b5e19f81c3e869347f21ab3c70a70016196b946d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>HDInsight용 Python 스트리밍 MapReduce 프로그램 개발

MapReduce 작업을 스트리밍하는 데 Python을 사용하는 방법 알아보기 Hadoop은 MapReduce용 스트리밍 API를 제공합니다. 이 API를 사용하여 Java 이외의 언어로 map 및 reduce 함수를 작성할 수 있습니다. 이 문서의 단계는 맵을 구현하고 Python의 구성 요소를 줄입니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터의 Linux 기반 Hadoop

  > [!IMPORTANT]
  > 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* 텍스트 편집기

  > [!IMPORTANT]
  > 텍스트 편집기에서 줄 끝으로 LF를 사용해야 합니다. CRLF의 줄 끝을 사용하는 경우 Linux 기반 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 오류가 발생할 수 있습니다.

* `ssh` 및 `scp` 명령 또는 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>단어 개수

이 예제는 매퍼와 리듀서를 사용하여 python에서 구현된 기본 단어 수입니다. mapper는 문장을 개별 단어로 끊고 reducer는 출력하기 위해 단어와 개수를 집계합니다.

다음 순서도는 map 및 reduce 단계가 어떻게 진행되는지 보여 줍니다.

![mapreduce 프로세스의 그림](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce 스트리밍

Hadoop을 사용하면 작업에서 사용되는 map 및 reduce 논리를 포함하는 파일을 지정할 수 있습니다. map 및 reduce 논리에 대한 특정 요구 사항은 다음과 같습니다.

* **입력**: map 및 reduce 구성 요소는 STDIN에서 입력 데이터를 읽어야 합니다.
* **출력**: map 및 reduce 구성 요소는 STDOUT에 출력 데이터를 작성해야 합니다.
* **데이터 형식**: 소비되고 생성되는 데이터는 탭 문자로 구분하는 키/값 쌍이어야 합니다

Python은 STDIN에서 읽을 수 있는 `sys` 모듈 및 STDOUT에 출력하는 `print`를 사용하여 이러한 요구 사항을 쉽게 처리할 수 있습니다. 나머지 작업은 키와 값 사이에 탭(`\t`) 문자를 사용하여 데이터 서식을 지정하기만 하면 됩니다.

## <a name="create-the-mapper-and-reducer"></a>mapper 및 reducer 만들기

1. `mapper.py`라는 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. **reducer.py**라는 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>PowerShell을 사용하여 실행

파일이 올바른 줄 끝을 가지고 있는지 확인하려면 다음 PowerShell 스크립트를 사용합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

다음 PowerShell 스크립트를 사용하여 파일을 업로드하고 작업을 실행하고 출력을 확인합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>SSH 세션에서 실행

1. 개발 환경의 `mapper.py` 및 `reducer.py` 파일과 동일한 디렉터리에서 다음 명령을 사용합니다.

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    `username`은 클러스터의 SSH 사용자 이름으로, `clustername`은 클러스터 이름으로 바꿉니다.

    이 명령은 로컬 시스템에서 헤드 노드로 파일을 복사합니다.

    > [!NOTE]
    > SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다. 예: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

2. SSH를 사용하여 클러스터에 연결합니다.

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

3. mapper.py 및 reducer.py가 올바른 줄 끝을 가지고 있는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. 다음 명령을 사용하여 MapReduce 작업을 시작합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    다음은 명령어의 일부입니다.

   * **hadoop-streaming.jar**: 스트리밍 MapReduce 작업을 수행할 때 사용합니다. 제공하는 외부 MapReduce 코드로 Hadoop에 접속합니다.

   * **-files**: MapReduce 작업에 지정된 파일을 추가합니다.

   * **-mapper**: Hadoop mapper로 사용될 파일을 지정합니다.

   * **-reducer**: Hadoop reducer로 사용할 파일을 지정합니다.

   * **-input**: 단어 수를 계산할 입력 파일을 지정합니다.

   * **-output**: 출력 내용을 작성할 디렉터리를 지정합니다.

    MapReduce 작업이 작동하면 프로세스는 백분율로 표시됩니다.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. 출력을 보려면 다음 명령을 사용합니다.

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    이 명령은 단어 목록과 해당 단어가 나타난 횟수를 표시합니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 스트리밍 MapRedcue 작업을 사용하는 방법을 배웠으므로 이제 아래 링크를 사용하여 Azure HDInsight에서 작업하는 다른 방법을 살펴봅니다.

* [HDInsight에서 하이브 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)
