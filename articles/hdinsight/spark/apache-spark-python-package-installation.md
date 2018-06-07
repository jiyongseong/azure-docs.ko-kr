---
title: 스크립트 작업 - Azure HDInsight의 Jupyter를 사용해 Python 패키지 설치 | Microsoft Docs
description: 스크립트 작업을 사용하여 HDInsight Spark 클러스터와 함께 제공되는 Jupyter 노트북에서 외부 python 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 4d9d1e0aaf6a1c0155f9ab74a5e63302635a0c11
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에서 스크립트 작업을 사용하여 Jupyter Notebook용 외부 python 패키지 설치
> [!div class="op_single_selector"]
> * [셀 매직 사용](apache-spark-jupyter-notebook-use-external-packages.md)
> * [스크립트 작업 사용](apache-spark-python-package-installation.md)
>
>

클러스터에 기본적으로 포함되지 않는 외부의 커뮤니티 제공 **python** 패키지를 사용하도록 HDInsight(Linux)의 Apache Spark 클러스터를 구성하는 스크립트 작업 사용 방법을 알아봅니다.

> [!NOTE]
> `%%configure` 매직을 사용하여 외부 패키지를 사용하도록 Jupyter Notebook을 구성할 수도 있습니다. 지침에 대해서는 [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)을 참조하세요.
> 
> 

사용할 수 있는 패키지의 전체 목록은 [패키지 인덱스](https://pypi.python.org/pypi)를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) 또는 [conda-forge](https://conda-forge.org/feedstocks/)를 통해 제공되는 패키지를 설치할 수 있습니다.

이 문서에서는 클러스터에서 스크립트 작업을 사용하여 [TensorFlow](https://www.tensorflow.org/) 패키지를 설치하고 Jupyter 노트북을 통해 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

   > [!NOTE]
   > Hdinsight Linux에 Spark 클러스터가 아직 없는 경우 클러스터를 만드는 동안 스크립트 작업을 실행할 수 있습니다. [사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)에 대한 설명서를 참조하세요.
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter 노트북에서 외부 패키지 사용

1. [Azure Portal](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   

2. Spark 클러스터 블레이드의 왼쪽 창에서 **스크립트 작업**을 클릭합니다. 헤드 노드 및 작업자 노드에 TensorFlow를 설치하는 사용자 지정 작업을 실행합니다. 기본 스크립트는 https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh[사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)의 설명서를 참조할 수 있습니다.

   > [!NOTE]
   > 클러스터에 두 개의 python 설치 프로그램이 있습니다. Spark는 `/usr/bin/anaconda/bin`에 있는 Anaconda python 설치 프로그램을 사용합니다. `/usr/bin/anaconda/bin/pip` 및 `/usr/bin/anaconda/bin/conda`를 통해 사용자 지정 작업에서 해당 설치 프로그램을 참조하세요.
   > 
   > 

3. PySpark Jupyter 노트북을 엽니다.

    ![새 Jupyter 노트북 만들기](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "새 Jupyter 노트북 만들기")

4. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

    ![노트북 이름 제공](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "노트북 이름 제공")

5. 이제 `import tensorflow` 및 hello world 예제를 실행합니다. 

    복사할 코드:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    결과는 다음과 유사하게 표시됩니다.
    
    ![TensorFlow 코드 실행](./media/apache-spark-python-package-installation/execution.png "TensorFlow 코드 실행")

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
* [HDInsight의 Apache Spark 클러스터에서 Jupyter 노트북과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
