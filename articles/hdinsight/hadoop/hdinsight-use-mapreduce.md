---
title: HDInsight의 Hadoop 및 MapReduce | Microsoft Docs
description: HDInsight 클러스터의 Hadoop에서 MapReduce 작업을 실행하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 131e3065da4ccfb20d63856844a302a94806fd19
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>HDInsight에서 Hadoop과 MapReduce 사용

HDInsight 클러스터에서 MapReduce 작업을 실행하는 방법에 대해 알아보세요. 다음 표를 사용하여 HDInsight에서 MapReduce를 사용하는 다양한 방법을 확인할 수 있습니다.

| **사용 기능**... | **...다음을 수행합니다** | ... **클러스터 운영 체제** | ... **클라이언트 운영 체제** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |**REST**를 사용하여 작업 원격 제출(예제 용도 cURL) |Linux 또는or Windows |Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** |Linux 또는or Windows |Windows |

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.
>

## <a id="whatis"></a>MapReduce란

Hadoop MapReduce는 방대한 양의 데이터를 처리하는 작업을 작성하기 위한 소프트웨어 프레임워크입니다. 입력 데이터는 독립적인 청크로 분할됩니다. 각 청크는 클러스터의 노드에서 동시에 처리됩니다. MapReduce 작업은 두 함수로 구성됩니다.

* **매퍼**: 입력된 데이터를 소비하고 분석하며(일반적으로 필터 및 정렬 작업) 튜플을 내보냅니다.(키-값 쌍)

* **리듀서**: 매퍼에서 나온 튜플을 소배하고 매퍼 데이터에서 더 작고 결합된 결과를 생성하는 요약 작업을 수행합니다.

다음 다이어그램에서는 기본 단어 계산 MapReduce 작업 예제를 보여줍니다.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

이 작업의 출력은 텍스트에서 각 단어가 발생한 횟수입니다.

* 매퍼는 입력 텍스트의 각 줄을 입력으로 가져오고 해당 줄을 단어로 구분합니다. 단어 뒤에 1이 표시되는 단어가 발생할 때마다 키/값 쌍을 내보냅니다. 리듀서로 보내기 전에 출력이 정렬됩니다.
* 리듀서가 각 단어의 개별 발생 수를 합산하고 단어 뒤에 발생 합계가 포함된 단일 키/값 쌍을 내보냅니다.

MapReduce는 다양한 언어로 구현할 수 있습니다. Java는 가장 일반적인 구현으로 해당 문서의 데모 용도로 사용됩니다.

## <a name="development-languages"></a>개발 언어

Java 및 Java Virtual Machine을 기반으로 하는 언어 또는 프레임워크는 MapReduce 작업으로 직접 실행할 수 있습니다. 이 문서에서 사용된 예는 Java MapReduce 응용 프로그램입니다. C#, Python, 독립 실행형 실행 파일 등의 비-Java 언어는 **Hadoop 스트리밍**을 사용해야 합니다.

Hadoop 스트리밍은 STDIN 및 STDOUT을 통해 매퍼 및 리듀서와 통신합니다. 매퍼와 리듀서는 STDIN에서 한 번에 한 줄씩 데이터를 읽고 STDOUT에 출력을 씁니다. 매퍼 및 리듀서가 읽거나 내보낸 각 줄은 탭 문자로 구분된 키/값 쌍의 형식이어야 합니다.

    [key]/t[value]

자세한 내용은 [Hadoop 스트리밍](http://hadoop.apache.org/docs/r1.2.1/streaming.html)을 참조하세요.

HDInsight에서 Hadoop 스트리밍을 사용하는 예를 보려면 다음 문서를 참조하세요.

* [C# MapReduce 작업 개발](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Python MapReduce 작업 개발](apache-hadoop-streaming-python.md)

## <a id="data"></a>예제 데이터

HDInsight는 `/example/data` 및 `/HdiSamples` 디렉터리에 저장되는 다양한 예제 데이터 집합을 제공합니다. 이러한 디렉터리는 클러스터의 기본 저장소에 있습니다. 이 문서에서는 `/example/data/gutenberg/davinci.txt` 파일을 사용합니다. 이 파일에는 레오나르도 다빈치의 메모장이 포함되어 있습니다.

## <a id="job"></a>예제 MapReduce

MapReduce 단어 수 세기 응용 프로그램 예가 HDInsight 클러스터에 포함되어 있습니다. 이 예제는 클러스터의 기본 저장소인 `/example/jars/hadoop-mapreduce-examples.jar`에 있습니다.

다음 Java 코드는 `hadoop-mapreduce-examples.jar` 파일에 포함된 MapReduce 응용 프로그램의 원본입니다.

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

고유한 MapReduce 응용 프로그램을 쓰는 지침은 다음 문서를 참조하세요.

* [HDInsight용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight용 Python MapReduce 프로그램 개발](apache-hadoop-streaming-python.md)

## <a id="run"></a>MapReduce 실행

HDInsight는 다양한 메서드를 사용하여 HiveQL 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

| **사용 기능**... | **...다음을 수행합니다** | ... **클러스터 운영 체제** | ... **클라이언트 운영 체제** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |**REST** |Linux 또는or Windows |Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** |Linux 또는or Windows |Windows |

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a id="nextsteps"></a>다음 단계

HDInsight에서 데이터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight용 Python 스트리밍 MapReduce 프로그램 개발](apache-hadoop-streaming-python.md)

* [HDInsight에서 Hive 사용][hdinsight-use-hive]

* [HDInsight에서 Pig 사용][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
