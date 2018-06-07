---
title: Python 구성 요소를 사용하는 Apache Storm - Azure HDInsight | Microsoft Docs
description: Python 구성 요소를 사용하는 Apache Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
keywords: apache storm python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 0a8930da2d44714573155477d791dd9cb78d872a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight에서 Python을 사용하여 Apache Storm 토폴로지 개발

Python 구성 요소를 사용하는 Apache Storm 토폴로지를 만드는 방법에 대해 알아봅니다. Apache Storm은 여러 언어를 지원하여 한 토폴로지에 여러 언어의 구성 요소를 결합할 수 있습니다. Flux 프레임워크(Storm 0.10.0에서 소개)를 사용하면 Python 구성 요소를 사용하는 솔루션을 쉽게 만들 수 있습니다.

> [!IMPORTANT]
> 이 문서의 정보는 HDInsight 3.6에서 Storm을 사용하여 테스트했습니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

이 프로젝트에 대한 코드는 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Python 2.7 이상

* JDK Java 1.8 이상

* Maven 3

* (선택 사항) 로컬 Storm 개발 환경 - 로컬 Storm 환경은 토폴로지를 로컬로 실행하려는 경우에만 필요합니다. 자세한 내용은 [개발 환경 설정](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html)(영문)을 참조하세요.

## <a name="storm-multi-language-support"></a>Storm 다중 언어 지원

Apache Storm은 모든 프로그래밍 언어로 작성된 구성 요소를 사용하도록 설계되었습니다. 구성 요소에서 [Storm에 대한 Thrift 정의](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)를 사용하는 방법을 이해해야 합니다. Python의 경우 모듈은 Apache Storm 프로젝트의 일부로 제공되므로 Storm과 쉽게 인터페이스할 수 있습니다. 이 모듈은 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)에서 찾을 수 있습니다.

Storm은 JVM(Java Virtual Machine)에서 실행되는 Java 프로세스입니다. 다른 언어로 작성된 구성 요소는 하위 프로세스로 실행됩니다. Storm은 stdin/stdout을 통해 전송되는 JSON 메시지를 사용하여 이러한 하위 프로세스와 통신합니다. 구성 요소 간의 통신에 대한 자세한 내용은 [다중 언어 프로토콜](https://storm.apache.org/documentation/Multilang-protocol.html) (영문) 설명서에서 확인할 수 있습니다.

## <a name="python-with-the-flux-framework"></a>Flux 프레임워크를 사용하는 Python

Flux 프레임워크를 사용하면 구성 요소와 별도로 Storm 토폴로지를 정의할 수 있습니다. Flux 프레임워크는 YAML을 사용하여 Storm 토폴로지를 정의합니다. 다음 텍스트는 YAML 문서에서 Python 구성 요소를 참조하는 방법의 예제입니다.

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

`FluxShellSpout` 클래스는 Spout를 구현하는 `sentencespout.py` 스크립트를 시작하는 데 사용됩니다.

Flux에서는 토폴로지를 포함하는 jar 파일 내의 `/resources` 디렉터리에 Python 스크립트가 있어야 합니다. 따라서 이 예제는 Python 스크립트를 `/multilang/resources` 디렉터리에 저장합니다. `pom.xml`은 다음 XML을 사용하여 이 파일을 포함합니다.

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

앞에서 언급했듯이 Storm에 대한 Thrift 정의를 구현하는 `storm.py` 파일이 있습니다. Flux 프레임워크는 프로젝트를 빌드할 때 `storm.py`를 자동으로 포함하므로 파일을 포함하는 것에 대해 걱정할 필요가 없습니다.

## <a name="build-the-project"></a>프로젝트 빌드

프로젝트의 루트에서 다음 명령을 사용합니다.

```bash
mvn clean compile package
```

이 명령은 컴파일된 토폴로지를 포함하는 `target/WordCount-1.0-SNAPSHOT.jar` 파일을 만듭니다.

## <a name="run-the-topology-locally"></a>로컬로 토폴로지 실행

토폴로지를 로컬로 실행하려면 다음 명령을 사용합니다.

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> 이 명령에는 로컬 Storm 개발 환경이 필요합니다. 자세한 내용은 [개발 환경 설정](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)(영문)을 참조하세요.

토폴로지가 시작되면 다음 텍스트와 비슷한 정보를 로컬 콘솔로 내보냅니다.


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


토폴로지를 중지하려면 __Ctrl + C__를 사용합니다.

## <a name="run-the-storm-topology-on-hdinsight"></a>HDInsight에서 Storm 토폴로지 실행

1. 다음 명령을 사용하여 HDInsight 클러스터의 Storm에 `WordCount-1.0-SNAPSHOT.jar` 파일을 복사합니다.

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    `sshuser`를 클러스터의 SSH 사용자로 바꿉니다. `mycluster`를 클러스터 이름으로 바꿉니다. SSH 사용자의 암호를 입력하라는 메시지가 표시될 수 있습니다.

    SSH 및 SCP 사용에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 파일이 업로드되면 SSH를 사용하여 클러스터에 연결합니다.

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH 세션에서 다음 명령을 사용하여 클러스터에서 토폴로지를 시작합니다.

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Storm UI를 사용하여 클러스터에서 토폴로지를 볼 수 있습니다. Storm UI는 https://mycluster.azurehdinsight.net/stormui에 있습니다. `mycluster`를 클러스터 이름으로 바꿉니다.

> [!NOTE]
> Storm 토폴로지가 시작되면 중지될 때까지 실행됩니다. 토폴로지를 중지하려면 다음 방법 중 하나를 사용합니다.
>
> * 명령줄에서 `storm kill TOPOLOGYNAME` 명령
> * Storm UI의 **종료**(Kill) 단추


## <a name="next-steps"></a>다음 단계

HDInsight와 함께 Python을 사용하는 다른 방법은 다음 문서를 참조하세요.

* [MapReduce 작업을 스트리밍하는 데 Python을 사용하는 방법](../hadoop/apache-hadoop-streaming-python.md)
* [Pig 및 Hive에서 UDF(사용자 정의 함수)를 사용하는 방법](../hadoop/python-udf-hdinsight.md)
