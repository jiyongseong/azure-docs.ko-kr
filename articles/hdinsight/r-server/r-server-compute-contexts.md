---
title: HDInsight에서 R Server의 Compute 컨텍스트 옵션 - Azure | Microsoft Docs
description: HDInsight에서 R Server 사용자에게 제공되는 다양한 계산 컨텍스트 옵션에 대해 알아봅니다.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 2aa10e1eab6cabe058062519ecc023b88361d742
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>HDInsight에서 R 서버의 Compute 컨텍스트 옵션

Azure HDInsight의 Microsoft R Server는 계산 컨텍스트를 설정하여 호출을 실행하는 방법을 제어합니다. 이 문서에서는 에지 노드 또는 HDInsight 클러스터의 코어에서 실행 병렬 처리 여부 및 방법을 지정하기 위해 사용할 수 있는 옵션을 간략히 설명합니다.

클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 위치를 제공합니다. 에지 노드를 사용하는 경우 에지 노드 서버의 코어에서 RevoScaleR의 병렬화된 분산 함수를 실행하는 옵션이 제공됩니다. 또한 RevoScaleR의 Hadoop Map Reduce 또는 Spark 계산 컨텍스트를 사용하여 클러스터의 노드에서 함수를 실행할 수도 있습니다.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Azure HDInsight의 Microsoft R Server
[Azure HDInsight의 Microsoft R Server](r-server-overview.md)는 R 기반 분석을 위한 최신 기능을 제공합니다. [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") 저장소 계정, Data Lake Store 또는 로컬 Linux 파일 시스템에 있는 컨테이너에 저장된 HDFS를 사용할 수 있습니다. R Server가 오픈 소스 R을 기반으로 하기 때문에 빌드한 R 기반 응용 프로그램은 8000개 이상의 오픈 소스 R 패키지를 적용할 수 있습니다. R Server와 함께 포함된 Microsoft의 빅 데이터 분석 패키지인 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)에서 루틴을 활용할 수도 있습니다.  

## <a name="compute-contexts-for-an-edge-node"></a>에지 노드에 대한 Compute 컨텍스트
일반적으로 에지 노드의 R 서버에서 실행되는 R 스크립트는 해당 노드의 R 인터프리터 내에서 실행됩니다. 예외는 RevoScaleR 함수를 호출하는 단계입니다. RevoScaleR 호출은 RevoScaleR 계산 컨텍스트를 설정하는 방법에 따라 결정된 계산 환경에서 실행됩니다.  에지 노드에서 R 스크립트 실행 시 계산 컨텍스트의 가능한 값은 다음과 같습니다.

- 로컬 순차(*local*)
- 로컬 병렬(*localpar*)
- Map Reduce
- Spark

*local* 및 *localpar* 옵션은 **rxExec** 호출을 실행하는 방법에 따라서만 달라집니다. 이러한 두 옵션은 RevoScaleR **numCoresToUse** 옵션(예: `rxOptions(numCoresToUse=6)`)을 사용하여 다르게 지정하지 않는 한, 사용 가능한 모든 코어에서 병렬 방식으로 다른 rx-function 호출을 실행합니다. 병렬 실행 옵션은 최적의 성능을 제공합니다.

다음 표에는 호출 실행 방법을 설정하는 다양한 계산 컨텍스트 옵션이 요약되어 있습니다.

| Compute 컨텍스트  | 설정 방법                      | 실행 컨텍스트                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| 로컬 순차 | rxSetComputeContext('local')    | 직렬로 실행되는 rxExec 호출을 제외하고, 에지 노드 서버의 코어 간 병렬화된 실행 |
| 로컬 병렬   | rxSetComputeContext('localpar') | 에지 노드 서버의 코어 간 병렬화된 실행 |
| Spark            | RxSpark()                       | HDI 클러스터의 노드에서 Spark를 통해 분산된 실행 병렬 처리 |
| Map Reduce       | RxHadoopMR()                    | HDI 클러스터의 노드에서 Map Reduce를 통해 분산된 실행 병렬 처리 |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>계산 컨텍스트를 결정하기 위한 지침

선택한 세 가지 옵션 중 병렬 처리 실행을 제공하는 옵션은 분석 작업의 성격, 크기 그리고 데이터의 위치에 따라 결정됩니다. 사용할 계산 컨텍스트를 알려주는 수식은 없습니다. 그러나 올바로 선택할 수 있도록, 또는 적어도 벤치마크를 실행하기 전에 선택할 범위를 좁히는 데 도움이 되는 몇 가지 가이드 원칙이 있습니다. 기본 원칙은 다음과 같습니다.

- 로컬 Linux 파일 시스템이 HDFS보다 빠릅니다.
- 데이터가 로컬에 있고 XDF 형식인 경우 반복된 분석이 더 빠릅니다.
- 텍스트 데이터 원본에서 적은 양의 데이터를 스트리밍하는 것이 좋습니다. 데이터 양이 더 많을 경우 분석 전에 XDF로 변환하세요.
- 분석을 위해 에지 노드에 데이터를 복사하거나 스트리밍하는 경우 발생하는 오버헤드는 매우 많은 양의 데이터가 감당할 수 없게 됩니다.
- Hadoop의 분석의 경우 Spark가 Map Reduce보다 빠릅니다.

이러한 원칙을 감안하여 다음 섹션에서는 계산 컨텍스트 선택에 대한 몇 가지 일반적인 규칙을 제공합니다.

### <a name="local"></a>Local
* 분석할 데이터 양이 적고 반복 분석이 필요하지 않은 경우 *local* 또는 *localpar*를 사용하여 분석 루틴으로 직접 스트림합니다.
* 분석할 데이터의 양이 적거나 중간 크기이고 반복 분석이 필요한 경우 로컬 파일 시스템에 복사하고 XDF로 가져와서 *local* 또는 *localpar*를 통해 분석합니다.

### <a name="hadoop-spark"></a>Hadoop Spark
* 분석할 데이터 양이 많은 경우 **RxHiveData** 또는 **RxParquetData**를 사용하여 Spark DataFrame으로 가져오거나 저장소 문제가 아닌 한 HDFS의 XDF로 가져와서 Spark 계산 컨텍스트를 사용하여 분석합니다.

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* 일반적으로 느려질 수 있기 때문에 Spark 계산 컨텍스트를 사용하여 대처할 수 없는 문제가 발생하는 경우에만 Map Reduce 계산 컨텍스트를 사용합니다.  

## <a name="inline-help-on-rxsetcomputecontext"></a>rxSetComputeContext의 인라인 도움말
자세한 내용 및 RevoScaleR 계산 컨텍스트의 예제는 다음과 같이 rxSetComputeContext 메서드에 대한 R의 인라인 도움말을 참조하세요.

    > ?rxSetComputeContext

[Machine Learning 서버 설명서](https://docs.microsoft.com/machine-learning-server/)에서 [분산 컴퓨팅 개요](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)를 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 에지 노드 또는 HDInsight 클러스터의 코어에서 실행 병렬 처리 여부 및 방법을 지정하기 위해 사용할 수 있는 옵션에 대해 알아봤습니다. HDInsight 클러스터에서 R 서버를 사용하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Hadoop용 R 서버 개요](r-server-overview.md)
* [Hadoop용 R Server 시작](r-server-get-started.md)
* [HDInsight에서 R Server의 Azure Storage 옵션](r-server-storage.md)

