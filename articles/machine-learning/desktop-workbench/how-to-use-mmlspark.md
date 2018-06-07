---
title: MMLSpark Machine Learning을 사용하는 방법 | Microsoft Docs
description: Azure Machine Learning에 MMLSpark 라이브러리를 사용하는 방법을 안내합니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 253f58721b709b044069c388521daf351264f74d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Apache Spark에 대한 Microsoft Machine Learning 라이브러리를 사용하는 방법

## <a name="introduction"></a>소개

[Apache Spark에 대한 Microsoft Machine Learning 라이브러리](https://github.com/Azure/mmlspark)(MMLSpark)는 큰 데이터 집합을 위한 간편하게 확장 가능한 기계 학습 모델을 제공합니다. SparkML 파이프라인과 [Microsoft Cognitive 도구 키트](https://github.com/Microsoft/CNTK) 및 [OpenCV](http://www.opencv.org/)가 통합되어 다음과 같은 일을 할 수 있습니다. 
 * 이미지 데이터 수신 및 전처리
 * 미리 학습된 심층 학습 모델을 사용하여 이미지 및 텍스트를 기능화
 * 암시적 기능화를 사용하여 분류 및 회귀 모델을 학습하고 점수 매기기

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 단계별로 실행하려면 다음을 수행해야 합니다.
- [Azure Machine Learning Workbench 설치](../service/quickstart-installation.md)
- [Azure HDInsight Spark 클러스터 설치](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Docker 컨테이너에서 실험 실행

Azure Machine Learning Workbench는 사용자가 Docker 컨테이너에서 실험을 실행하면 로컬로 또는 원격 VM에서 MMLSpark를 사용하도록 구성되었습니다. 이 기능 덕분에 사용자는 손쉽게 PySpark 모델을 디버그 및 실험한 후 클러스터에서 여러 규모로 실행할 수 있습니다. 

예제를 사용하려면 새 프로젝트를 만들고 "성인 인구에 대한 MMLSpark" 갤러리 예제를 선택합니다. 프로젝트 대시보드에서 계산 컨텍스트로 "Docker"를 선택하고 "실행"을 클릭합니다. Azure Machine Learning Workbench가 PySpark 프로그램을 실행할 Docker 컨테이너를 빌드한 후 프로그램을 실행합니다.

실행이 완료되면 Azure Machine Learning Workbench의 실행 기록 보기에서 결과를 볼 수 있습니다.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark 클러스터에 MMLSpark 설치.

이 단계와 다음 단계를 완료하려면 먼저 [Azure HDInsight Spark 클러스터를 만들어야 합니다](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

기본적으로 Azure Machine Learning Workbench는 사용자가 실험을 실행하면 클러스터에 MMLSpark 패키지를 설치합니다. 사용자는 프로젝트 폴더에 있는 _aml_config/spark_dependencies.yml_ 파일을 편집하여 이 동작을 제어하고 다른 Spark 패키지를 설치할 수 있습니다.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

[스크립트 작업](https://github.com/Azure/mmlspark#hdinsight)을 사용하여 HDInsight Spark 클러스터에 직접 MMLSpark를 설치할 수도 있습니다.

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>계산 대상으로 Azure HDInsight Spark 클러스터 설치

"파일" 메뉴로 이동한 후 "명령 프롬프트 열기"를 클릭하여 Azure Machine Learning Workbench에서 CLI 창을 엽니다.

CLI 창에서 다음 명령을 실행합니다.

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

이제 클러스터를 프로젝트의 계산 대상으로 사용할 수 있습니다.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark 클러스터에서 실험 실행.

"성인 인구에 대한 MMLSpark" 예제의 프로젝트 대시보드로 돌아갑니다. 계산 대상으로 현재 클러스터를 선택하고 실행을 클릭합니다.

Azure Machine Learning Workbench가 Spark 작업을 클러스터에 제출합니다. 실행 기록 보기에서 진행률을 모니터링하고 결과를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
MMLSpark 라이브러리 및 예제에 대한 내용은 [MMLSpark GitHub 리포지토리](https://github.com/Azure/mmlspark)를 참조하세요.

*Apache®, Apache Spark 및 Spark®는 미국 및/또는 기타 국가에서 Apache Software Foundation의 등록 상표 또는 상표입니다.*
