---
title: Microsoft의 다른 기계 학습 제품 - Azure Machine Learning | Microsoft Docs
description: Microsoft에는 Azure Machine Learning 외에도 기계 학습 모델을 작성, 배포 및 관리할 수 있는 다양한 옵션이 있습니다.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: bbb8deeab8368d9c0e6d29c8d7e1e2e0a8805d60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Microsoft의 다른 기계 학습 제품 및 서비스

Microsoft에는 [Azure Machine Learning](overview-what-is-azure-ml.md) 외에도 기계 학습 모델을 작성, 배포 및 관리할 수 있는 다양한 옵션이 있습니다. 
* SQL Server Machine Learning 서비스
* Microsoft Machine Learning 서버
* Azure 데이터 과학 가상 머신
* HDInsight의 Spark MLLib
* 일괄 처리 AI 교육 서비스
* Microsoft Cognitive 도구 키트(CNTK)
* Azure Cognitive Services


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning 서비스
[SQL Server Microsoft Machine Learning 서비스](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)를 통해 R 또는 Python을 사용하여 Machine Learning 모델을 실행, 학습 및 배포할 수 있습니다. 온-프레미스 및 SQL Server 데이터베이스에 있는 데이터를 사용할 수 있습니다. 

온-프레미스 또는 Microsoft SQL Server 내부에 모델을 학습 또는 배포해야 할 때 Microsoft Machine Learning 서비스를 사용합니다. Machine Learning 서비스로 빌드된 모델은 Azure Machine Learning 모델 관리를 사용하여 배포할 수 있습니다. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning 서버 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)는 R 및 Python 프로세스의 병렬 및 분산 워크로드를 호스트하고 관리하기 위한 엔터프라이즈 서버입니다. Microsoft Machine Learning Server는 Linux, Windows, Hadoop 및 Apache Spark에서 실행됩니다. 또한 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/)에서도 사용할 수 있습니다. [Microsoft Machine Learning 패키지](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)를 사용하여 빌드된 솔루션을 위한 실행 엔진을 제공하며 다음과 같은 시나리오를 지원하도록 오픈 소스 R 및 Python을 확장합니다.

- 고성능 분석
- 통계 분석
- Machine Learning
- 매우 큰 데이터 집합

추가 기능은 서버와 함께 설치되는 전용 패키지를 통해 제공됩니다. 개발을 위해 [Visual Studio용 R 도구](https://www.visualstudio.com/vs/rtvs/) 및 [Visual Studio용 Python 도구](https://www.visualstudio.com/vs/python/)와 같은 IDE를 사용할 수 있습니다.

다음을 수행해야 하는 경우 Microsoft Machine Learning Server를 사용합니다.

- 서버에서 R 및 Python으로 작성된 모델 빌드 및 배포
- Hadoop 또는 Spark 클러스터에서 규모 수준으로 R 및 Python 교육 배포

## <a name="azure-data-science-virtual-machine"></a>Azure 데이터 과학 가상 머신
[데이터 과학 가상 머신](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)은 데이터 과학 수행을 위해 특별히 구축된 Microsoft Azure 클라우드의 사용자 지정 가상 머신 환경입니다. 여기에는 고급 분석을 위한 지능형 응용 프로그램 구축에 바로 뛰어들 수 있도록 다수의 유명한 데이터 과학 및 기타 도구가 미리 설치 및 구성되어 있습니다. 데이터 과학 VM은 Windows Server 버전 2016 및 2012와 Ubuntu 16.04 LTS 및 OpenLogic CentOS 7.4 기반 배포판의 Linux VM에서 사용할 수 있습니다. 

단일 노드에서 작업을 실행하거나 호스트해야 하는 경우 데이터 과학 VM을 사용하세요. 또는 단일 컴퓨터에서 처리를 원격으로 강화해야 하는 경우에 사용합니다. 데이터 과학 Virtual Machine은 Azure Machine Learning 실험과 Azure Machine Learning 모델 관리 모두를 대상으로 지원됩니다. 

## <a name="spark-mllib-in-hdinsight"></a>HDInsight의 Spark MLLib
[HDInsight의 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning)를 사용하면 빅 데이터에서 실행 중인 Spark 작업의 일환으로 모델을 만들 수 있습니다. Spark를 사용하면 데이터를 쉽게 변환 및 준비한 다음, 하나의 작업에서 모델 생성을 스케일 아웃할 수 있습니다. Spark MLLib를 통해 만든 모델은 Azure Machine Learning 모델 관리를 통해 배포, 관리 및 모니터링할 수 있습니다. 교육 실행은 Azure Machine Learning 실험을 통해 디스패치되고 관리됩니다. Spark는 Machine Learning Workbench에서 만든 데이터 준비 작업을 스케일 아웃하는 데 사용할 수 있습니다. 

데이터 파이프라인의 일환으로 데이터 처리를 스케일 아웃하고 모델을 만들어야 하는 경우 Spark를 사용합니다. Scala, Java, Python 또는 R에 Spark 작업을 작성할 수 있습니다. 

## <a name="batch-ai-training"></a>일괄 처리 AI 교육 
[Azure 일괄 처리 AI 교육](https://aka.ms/batchaitraining)은 사용자가 모든 프레임워크를 사용하여 AI 모델과 동시에 실험하도록 도움을 주며, 클러스터링된 GPU에 걸친 규모로 해당 모델을 교육합니다. 실행할 작업 요구 사항 및 구성을 설명하면, 나머지는 알아서 처리됩니다. 

일괄 처리 AI 교육을 사용하면 다음과 같이 프레임워크를 사용하여 클러스터링된 GPU에서 심층 교육 작업을 스케일 아웃할 수 있습니다.

- Cognitive 도구 키트
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning 모델 관리를 일괄 처리 AI 교육에서 모델을 생성하여 배포, 관리 및 모니터링하는 데 사용할 수 있습니다.  일괄 처리 AI 교육은 나중에 Azure Machine Learning 실험과 통합됩니다. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive 도구 키트(CNTK)
[Microsoft Cognitive 도구 키트](https://www.microsoft.com/en-us/cognitive-toolkit/)는 신경망을 방향성 그래프의 계산 단계로 설명하는 통합된 심층 학습 도구 키트입니다. 이 방향성 그래프에서 리프 노드는 입력된 값이나 네트워크 매개 변수를 나타내며, 다른 노드는 입력 시 매트릭스 작업을 나타냅니다. Cognitive 도구 키트를 사용하면 피드 정방향 DNN, 나선형 네트워크(CNN) 및 되풀이 네트워크(RNN/LSTM)와 같은 인기 있는 모델 유형을 빠르게 실현하고 결합할 수 있습니다. 여러 GPU 및 서버에서 자동 차별화 및 병렬화를 통해 SGD(추측 기울기 하강, 오류 역전파) 학습을 구현합니다.

심층 학습을 사용하여 모델을 빌드하려는 경우 Cognitive 도구 키트를 사용합니다.  앞의 서비스 중 하나에 Cognitive 도구 키트를 사용할 수 있습니다.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)는 자연스러운 통신 방법을 사용하는 앱을 빌드할 수 있는 약 30가지의 API 집합입니다. 이러한 API를 사용하면 단 몇 줄의 코드로 사용자 앱이 사용자의 요구를 보고, 듣고, 말하고, 이해하며, 해석할 수 있습니다. 다음과 같은 앱에 지능형 기능을 쉽게 추가합니다. 

- 이모티콘 및 감정 검색
- 음성 인식
- LUIS(언어 해석)
- 지식 및 검색

Cognitive Services를 장치 및 플랫폼에서 앱을 개발하는 데 사용할 수 있습니다. API는 끊임없이 개선되며, 설치하기가 매우 쉽습니다. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
