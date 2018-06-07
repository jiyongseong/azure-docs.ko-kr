---
title: Jupyter를 로컬로 설치하고 Azure HDInsight Spark 클러스터에 연결 | Microsoft Docs
description: 컴퓨터에 로컬로 Jupyter 노트북을 설치하고 Azure HDInsight에서 Apache Spark 클러스터에 연결하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: eea61586054f34142d77f16333fe70a66d95d529
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>컴퓨터에 Jupyter 노트북을 설치하고 HDInsight에서 Apache Spark에 연결

이 문서에서는 Spark Magic이 있는 사용자 지정 PySpark(Python용) 및 Spark(Scala용) 커널을 사용하여 Jupyter Notebook을 설치한 후 해당 노트북을 HDInsight 클러스터에 연결하는 방법을 알아봅니다. 로컬 컴퓨터에 Jupyter를 설치하는 여러 가지 이유와 몇 가지 어려운 문제가 있을 수 있습니다. 이에 대한 자세한 내용은 이 문서의 끝에 있는 [내 컴퓨터에 Jupyter를 설치해야 하는 이유](#why-should-i-install-jupyter-on-my-computer) 섹션을 참조하세요.

Jupyter 및 Spark Magic을 컴퓨터에 설치하는 것과 관련된 세 가지 주요 단계가 있습니다.

* Jupyter 노트북 설치
* Spark Magic과 함께 PySpark 및 Spark 커널 설치
* HDInsight에서 Spark 클러스터에 액세스하도록 Spark Magic 구성

HDInsight 클러스터의 Jupyter Notebook에 사용할 수 있는 사용자 지정 커널 및 Spark Magic에 대한 자세한 내용은 [HDInsight의 Apache Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
여기에 나열된 필수 구성 요소는 Jupyter를 설치하기 위한 것이 아니며 Jupyter 노트북이 설치되면 HDInsight 클러스터를 노트북을 연결하기 위한 것입니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="install-jupyter-notebook-on-your-computer"></a>컴퓨터에 Jupyter 노트북 설치

Jupyter 노트북을 설치하려면 먼저 Python을 설치해야 합니다. Python 및 Jupyter 둘 다 [Anaconda 배포](https://www.continuum.io/downloads)의 일부로 사용할 수 있습니다. Anaconda를 설치할 때 Python 배포를 설치하게 됩니다. Anaconda를 설치한 후 적절한 명령을 실행하여 Jupyter 설치를 추가합니다.

1. 사용하는 플랫폼용 [Anaconda 설치 관리자](https://www.continuum.io/downloads) 를 다운로드하고 설치 프로그램을 실행합니다. 설치 마법사를 실행하는 동안 PATH 변수에 Anaconda를 추가하는 옵션을 선택해야 합니다.
2. 다음 명령을 실행하여 Jupyter를 설치합니다.

        conda install jupyter

    Jupyter 설치에 대한 자세한 내용은 [Anaconda를 사용하여 Jupyter 설치](http://jupyter.readthedocs.io/en/latest/install.html)를 참조하세요.

## <a name="install-the-kernels-and-spark-magic"></a>커널 및 Spark Magic 설치

Spark Magic, PySpark 및 Spark 커널을 설치 하는 방법에 대한 지침은 GitHub에서 [sparkmagic 설명서](https://github.com/jupyter-incubator/sparkmagic#installation)의 설치 지침을 따르세요. Spark Magic 설명서의 첫 번째 단계에서는 Spark Magic 설치를 요구합니다. 연결하려는 HDInsight 클러스터의 버전에 따라 링크의 첫 번째 단계를 다음 명령으로 바꿉니다. 그런 다음, Spark Magic 설명서의 나머지 단계를 따릅니다. 다른 커널을 설치하려면 Spark Magic 설치 지침 섹션에서 3단계를 수행해야 합니다.

* 클러스터 v3.4의 경우 `pip install sparkmagic==0.2.3`을 실행하여 sparkmagic 0.2.3을 설치합니다.

* 클러스터 v3.5 및 v3.6의 경우 `pip install sparkmagic==0.11.2`를 실행하여 sparkmagic 0.11.2를 설치합니다.

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에 연결하도록 Spark Magic 구성

이 섹션에서는 이전에 설치한 Spark Magic이 Azure HDInsight에서 이미 만든 Apache Spark 클러스터에 연결되도록 구성합니다.

1. Jupyter 구성 정보는 일반적으로 사용자 홈 디렉터리에 저장됩니다. OS 플랫폼에서 홈 디렉터리를 찾으려면 다음 명령을 입력합니다.

    Python 셸을 시작합니다. 명령 창에서 다음을 입력합니다.

        python

    Python 셸에서 다음 명령을 입력하여 홈 디렉터리를 찾습니다.

        import os
        print(os.path.expanduser('~'))

2. 홈 디렉터리로 이동하고 **.sparkmagic** 폴더가 아직 없는 경우 새로 만듭니다.
3. 이 폴더에 **config.json** 이라는 파일을 만들고 그 안에 다음 JSON 코드 조각을 추가합니다.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. **{USERNAME}**, **{CLUSTERDNSNAME}** 및 **{BASE64ENCODEDPASSWORD}**를 적합한 값으로 바꿉니다. 즐겨 사용하는 프로그래밍 언어 또는 온라인의 다양한 유틸리티를 사용하여 실제 암호에 대한 base64 인코딩 암호를 생성할 수 있습니다.

5. `config.json`에서 올바른 하트 비트 설정을 구성합니다. `kernel_python_credentials` 및 이전에 추가한 `kernel_scala_credentials` 조각과 같은 수준에서 이러한 설정을 추가해야 합니다. 하트비트 설정을 추가하는 방법 및 추가할 위치에 대한 예제를 보려면 [샘플 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)을 참조하세요.

    * `sparkmagic 0.2.3`(v3.4 클러스터)의 경우 다음을 포함합니다.

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * `sparkmagic 0.11.2`(v3.5 및 v3.6 클러스터)의 경우 다음을 포함합니다.

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >세션이 유출되지 않도록 하트 비트가 전송됩니다. 컴퓨터가 절전 모드로 전환되거나 종료되면, 하트비트가 전송되지 않으므로 세션이 삭제됩니다. 클러스터 v3.4의 경우, 이 동작을 사용하지 않도록 설정하려면 Ambari UI에서 Livy 구성 `livy.server.interactive.heartbeat.timeout`를 `0`로 설정할 수 있습니다. 클러스터 v 3.5의 경우, 위의 3.5 구성을 설정하지 않으면 세션이 삭제되지 않습니다.

6. Jupyter를 시작합니다. 명령 프롬프트에서 다음 명령을 사용합니다.

        jupyter notebook

7. Jupyter 노트북을 사용하여 클러스터에 연결할 수 있는지와 커널에서 사용 가능한 Spark Magic을 사용할 수 있는지 확인합니다. 다음 단계를 수행합니다.

    a. 새 Notebook을 만듭니다. 오른쪽 구석에서 **새로 만들기**를 클릭합니다. 기본 커널 **Python2**와 설치하는 두 개의 새 커널 **PySpark** 및 **Spark**가 표시되어야 합니다. **PySpark**를 클릭합니다.

    ![Jupyter 노트북의 커널](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter 노트북의 커널")

    나. 다음 코드 조각을 실행합니다.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    출력을 검색할 수 있으면 HDInsight 클러스터에 대한 연결이 테스트됩니다.

    >[!TIP]
    >다른 클러스터에 연결하도록 노트북 구성을 업데이트하려는 경우 위의 3단계와 같이 새 값 집합으로 config.json을 업데이트합니다.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>내 컴퓨터에 Jupyter를 설치해야 해야 이유는 무엇인가요?
컴퓨터에 Jupyter를 설치한 다음 HDInsight의 Spark 클러스터에 연결하는 데는 여러 가지 이유가 있을 수 있습니다.

* Jupyter 노트북을 Azure HDInsight의 Spark 클러스터에 이미 사용할 수 있더라도 컴퓨터에 Jupyter를 설치하면 로컬에서 노트북을 만들고, 실행 중인 클러스터에 대해 응용 프로그램을 테스트한 다음 클러스터에 노트북을 업로드하는 옵션이 제공됩니다. 노트북에 클러스터를 업로드하려면 클러스터에서 실행되는 Jupyter 노트북을 사용하여 업로드하거나 클러스터와 연결된 저장소 계정의 /HdiNotebooks 폴더에 저장할 수 있습니다. 클러스터에 Notebook을 저장하는 방법에 대한 자세한 내용은 [Jupyter Notebook이 저장되는 위치](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)를 참조하세요.
* 로컬에서 사용할 수 있는 Notebook을 사용하여 응용 프로그램 요구 사항에 따라 다른 Spark 클러스터에 연결할 수 있습니다.
* GitHub를 사용하여 원본 제어 시스템을 구현하고 Notebook에 대한 버전을 제어할 수 있습니다. 여러 사용자가 동일한 Notebook으로 작업할 수 있는 공동 작업 환경이 있을 수도 있습니다.
* 클러스터 없이 로컬로 Notebook을 사용할 수 있습니다. Notebook 또는 개발 환경을 수동으로 관리하기 위해서가 아니라 Notebook을 테스트하기 위해 클러스터가 필요합니다.
* 클러스터에서 Jupyter 설치를 구성하는 것보다 자체 로컬 개발 환경을 구성하는 것이 더 쉬울 수 있습니다.  하나 이상의 원격 클러스터를 구성하지 않고 로컬로 설치한 모든 소프트웨어를 모두 활용할 수 있습니다.

> [!WARNING]
> 로컬 컴퓨터에 설치된 Jupyter를 사용하면 여러 사용자가 동일한 Spark 클러스터에서 동시에 동일한 Notebook을 실행할 수 있습니다. 이러한 상황에서 여러 Livy 세션이 생성됩니다. 문제가 발생하여 디버깅하려는 경우 어떤 사용자에게 어떤 Livy 세션이 속하는지를 추적하는 복잡한 작업이 됩니다.
>
>

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
