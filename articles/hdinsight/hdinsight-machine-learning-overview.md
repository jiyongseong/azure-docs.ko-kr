---
title: 기계 학습 개요 - Azure HDInsight | Microsoft Docs
description: HDInsight의 기계 학습 옵션에 대해 설명합니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: b80c49b67c7cca835ae08346561984e168e3df4e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight의 기계 학습

HDInsight를 사용하면 빅 데이터와 함께 기계 학습을 수행하여 대량(페타바이트, 심지어 엑사바이트)의 구조적, 비구조적 및 고속 이동 데이터에서 가치 있는 통찰력을 얻을 수 있습니다. HDInsight에는 SparkML과 MLlib, R, Hive 및 Microsoft Cognitive Toolkit와 같은 여러 가지 기계 학습 옵션이 있습니다.

## <a name="sparkml-and-mllib"></a>SparkML 및 MLlib

[HDInsight Spark](spark/apache-spark-overview.md)는 Azure에서 호스팅되는 [Spark](http://spark.apache.org/) 제품으로, 메모리 내 처리를 지원하여 빅 데이터 분석을 향상시키는 오픈 소스 통합형 병렬 데이터 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다. 이 분산 환경에 알고리즘 모델링 기능을 제공하는 확장 가능한 두 가지 기계 학습 라이브러리로 MLlib와 SparkML이 있습니다. MLlib에는 RDD에 기반하여 빌드된 원래의 API가 포함되어 있습니다. SparkML은 ML 파이프라인을 생성하기 위해 데이터 프레임에 기반하여 빌드된 높은 수준의 고급 API를 제공하는 새로운 패키지입니다. SparkML은 아직 MLlib의 모든 기능을 지원하지는 않지만 MLlib를 Spark의 표준 기계 학습 라이브러리로 대체합니다.

[MMLSpark](https://github.com/Azure/mmlspark)는 Apache Spark용 Microsoft Machine Learning 라이브러리입니다. 이 라이브러리는 Spark에서 데이터 과학자의 생산성을 높이고, 실험 속도를 높이고, 매우 큰 데이터 집합에서 최첨단 기계 학습 기술(딥 러닝 포함)을 활용하도록 설계되었습니다. MMLSpark는 확장 가능한 ML 모델(예: 인덱싱 문자열)을 작성하고, 데이터를 기계 학습 알고리즘에 필요한 레이아웃으로 강제 변환하고, 기능 벡터를 어셈블할 때 SparkML의 하위 수준 API에 기반한 계층을 제공합니다. MMLSpark 라이브러리는 PySpark에서 모델을 작성하기 위한 이러한 일반적인 작업을 간소화합니다.

## <a name="r"></a>R

[R](https://www.r-project.org/)은 현재 세계에서 가장 인기 있는 통계 프로그래밍 언어입니다. 오픈 소스 데이터 시각화 도구이며, 250만 명이 넘는 사용자와 더불어 계속 성장 중인 커뮤니티가 참여하고 있습니다. 번창하는 사용자를 기반으로 하고 8,000개 이상의 적용된 패키지를 제공하는 R은 기계 학습이 필요한 많은 회사에 적합한 선택 사항입니다. 대량 데이터 집합 및 모델과 함께 사용할 준비가 된 R Server를 사용하여 HDInsight 클러스터를 만들 수 있습니다. 이 기능은 데이터 과학자 및 통계 전문가에게 클러스터 설치 및 유지 관리에 대한 오버헤드 없이 HDInsight를 통해 주문형으로 크기 조정할 수 있는 친숙한 R 인터페이스를 제공합니다.

![R Server를 사용한 예측 학습](./media/hdinsight-machine-learning-overview/r-training.png)

클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 위치를 제공합니다.  또한 ScaleR의 Hadoop Map Reduce 또는 Spark 계산 컨텍스트를 사용하여 클러스터 노드에서 R 스크립트를 실행하는 옵션도 제공됩니다.

Spark가 있는 HDInsight의 R Server를 사용하면 Spark 계산 컨텍스트를 통해 클러스터 노드에서 학습을 병렬화할 수 있습니다. 필요에 따라 사용 가능한 모든 코어를 병렬로 사용하여 에지 노드에서 R 스크립트를 직접 실행할 수 있습니다. 또는 에지 노드의 코드를 실행하여 클러스터의 모든 노드에 분산된 처리를 시작할 수 있습니다. 또한 Spark가 있는 HDInsight의 R Server를 사용하면 원하는 경우 오픈 소스 R 패키지의 병렬화 기능을 사용할 수도 있습니다.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning 및 Hive

Azure Machine Learning은 예측 분석을 모델링하는 도구를 제공할 뿐 아니라, 예측 모델을 사용할 준비가 된 웹 서비스로 배포하는 데 사용할 수 있는 완전한 관리 서비스도 제공합니다. Azure Machine Learning은 클라우드 기반의 완벽한 예측 분석 솔루션으로, 예측 모델을 생성, 테스트, 운영 및 관리하는 데 사용할 수 있습니다. 대형 알고리즘 라이브러리에서 선택하고, 웹 기반 스튜디오를 사용하여 모델을 작성하고, 이 모델을 웹 서비스로 쉽게 배포합니다.

![Microsoft Azure Machine Learning을 사용하여 Hadoop에 액세스할 수 있는 고급 분석 만들기](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

[Hive 쿼리](../machine-learning/team-data-science-process/create-features-hive.md)를 사용하여 HDInsight Hadoop 클러스터의 데이터에 대한 기능을 만듭니다. *기능 엔지니어링*은 원시 데이터로부터 학습 프로세스를 용이하게 하는 기능을 만들어 학습 알고리즘의 예측 능력을 향상시키도록 합니다. Azure ML에서 HiveQL 쿼리를 실행하고, [데이터 가져오기 모듈](../machine-learning/studio/import-data.md)을 사용하여 Hive에서 처리되고 Blob 저장소에 저장된 데이터에 액세스할 수 있습니다.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트

[딥 러닝](https://www.microsoft.com/en-us/research/group/dltc/)은 인간 두뇌의 생물학적 프로세스에서 영감을 얻은 신경망을 사용하는 기계 학습의 한 분야입니다. 많은 연구자들은 딥 러닝이 인공 지능을 향상시키기 위한 유망한 접근 방식이라고 봅니다. 딥 러닝의 예로 언어 변환기, 이미지 인식 시스템 및 컴퓨터 추론이 있습니다.

딥 러닝에서 자체의 작업을 진행하도록 지원하기 위해 Microsoft는 추가 비용이 없고 사용하기 쉬운 오픈 소스 [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)를 개발했습니다. 이 도구 키트는 광범위한 Microsoft 제품, 규모에 맞게 딥 러닝을 배포해야 하는 요구 사항이 있는 전세계 회사 및 최신의 알고리즘과 기술에 관심이 있는 학생들이 사용하고 있습니다. 

## <a name="see-also"></a>참고 항목

### <a name="scenarios"></a>시나리오

* [Machine Learning과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Mahout을 사용하여 영화 추천 생성](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive 및 Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive 및 Azure Machine Learning 종단 간 시나리오](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [HDInsight에서 Spark를 사용하는 기계 학습](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>딥 러닝 리소스

* [Spark를 사용한 딥 러닝 도구 키트](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Spark에서 Cognitive Toolkit 및 Tensorflow를 사용하여 처치 곤란한 병렬 이미지 분류](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
