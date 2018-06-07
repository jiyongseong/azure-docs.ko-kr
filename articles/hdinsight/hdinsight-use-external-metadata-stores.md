---
title: 외부 메타데이터 저장소 사용 - Azure HDInsight | Microsoft Docs
description: HDInsight 클러스터로 외부 메타데이터 저장소를 사용합니다.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2eadee1a8695450e2219031ea1a65ee3624f26b5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight에서 외부 메타데이터 저장소 사용

HDInsight의 Hive metastore는 Hadoop 아키텍처의 핵심 부분입니다. metastore는 Spark, Interactive Query(LLAP), Presto 또는 Pig와 같은 다른 빅 데이터 액세스 도구에서 사용할 수 있는 중앙 스키마 리포지토리입니다. HDInsight는 Azure SQL Database를 Hive metastore로 사용합니다.

![HDInsight Hive 메타데이터 저장소 아키텍처](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

두 가지 방법을 이용하여 HDInsight 클러스터에 대해 metastore를 설정할 수 있습니다.

* [기본 metastore](#default-metastore)
* [사용자 지정 metastore](#custom-metastore)

## <a name="default-metastore"></a>기본 metastore

기본적으로 HDInsight는 모든 클러스터 유형의 metastore를 프로비전합니다. 사용자 지정 metastore를 지정할 수 있습니다. 기본 metastore에 대한 고려 사항은 다음과 같습니다.
- 추가 비용이 없습니다. HDInsight는 추가 비용 없이 모든 클러스터 유형의 metastore를 프로비전합니다.
- 각 기본 metastore는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 metastore와 메타데이터도 삭제됩니다.
- 기본 metastore를 다른 클러스터와 공유할 수 없습니다.
- 기본 metastore는 DTU(데이터베이스 트랜잭션 단위)가 5개로 제한되는 기본 Azure SQL DB를 사용합니다.
이 기본 metastore는 일반적으로 여러 클러스터가 필요하지 않은 상대적으로 단순하고 클러스터의 수명 주기 이상 메타데이터를 보존할 필요가 없는 워크로드에 사용됩니다.


## <a name="custom-metastore"></a>사용자 지정 metastore

HDInsight는 프로덕션 클러스터에 권장되는 사용자 지정 metastore도 지원합니다.
- 사용자 고유의 Azure SQL Database를 metastore로 지정합니다.
- metastore의 수명 주기가 클러스터의 수명 주기에 연결되어 있지 않으므로 메타데이터를 손실하지 않고 클러스터를 만들고 삭제할 수 있습니다. HDInsight 클러스터를 삭제하고 다시 만든 후에도 Hive 스키마와 같은 메타데이터가 유지됩니다.
- 사용자 지정 metastore를 사용하면 해당 metastore에 여러 클러스터 및 클러스터 유형을 첨부할 수 있습니다. 예를 들어 HDInsight의 여러 대화형 쿼리, Hive 및 Spark 클러스터에서 단일 metastore를 공유할 수 있습니다.
- 선택한 성능 수준에 따라 metastore(Azure SQL DB) 비용을 지불합니다.
- 필요에 따라 metastore를 확장할 수 있습니다.


![HDInsight Hive 메타데이터 저장소 사용 사례](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>클러스터를 만드는 동안 사용자 지정 metastore 선택

클러스터를 만드는 동안 클러스터로 이전에 만든 Azure SQL Database를 가리키거나 클러스터가 만들어진 후 SQL 데이터베이스를 구성할 수 있습니다. 이 옵션은 Azure Portal에서 새 Hadoop, Spark 또는 대화형 Hive 클러스터를 만드는 동안 [저장소] > [Metastore 설정]으로 지정됩니다.

![HDInsight Hive 메타데이터 저장소 Azure Portal](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Azure Portal 또는 Ambari 구성(Hive > Advanced)에서 사용자 지정 metastore에 클러스터를 추가할 수 있습니다.

![HDInsight Hive 메타데이터 저장소 Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive metastore 모범 사례

다음은 몇 가지 일반적인 HDInsight Hive metastore 모범 사례입니다.

- 가능한 경우 사용자 지정 metastore를 사용합니다. 이를 통해 계산 리소스(실행 중인 클러스터)와 metastore에 저장된 메타데이터를 구분할 수 있습니다.
- 50 DTU 및 250GB의 저장소를 제공하는 S2 계층으로 시작합니다. 병목 상태가 발생하는 경우 데이터베이스를 확장할 수 있습니다.
- 특정 HDInsight 클러스터 버전에 대해 만든 metastore는 여러 가지 HDInsight 클러스터 버전 간에 공유할 수 없습니다. 여러 가지 Hive 버전에서는 다양한 스키마를 사용합니다. 예를 들어 Hive 1.2와 Hive 2.1 클러스터 모두와 metastore를 공유할 수 없습니다.
- 사용자 지정 metastore를 정기적으로 백업합니다.
- metastore와 HDInsight 클러스터를 동일한 영역에 유지합니다.
- Azure Portal 또는 Azure Log Analytics와 같은 Azure SQL Database 모니터링 도구를 사용하여 성능 및 가용성 확인을 위해 metastore를 모니터링합니다.

## <a name="oozie-metastore"></a>Oozie Metastore

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다.  Oozie는 Apache MapReduce, Pig, Hive 등에 대한 Hadoop 작업을 지원합니다.  Oozie는 metastore를 사용하여 현재 및 완료된 워크플로에 대한 세부 정보를 저장합니다. Oozie 사용 시 성능을 높이기 위해 Azure SQL Database를 사용자 지정 metastore로 사용할 수 있습니다. 클러스터를 삭제한 후 metastore가 Oozie 작업 데이터에 대한 액세스를 제공할 수도 있습니다.

Azure SQL Database를 사용하여 Oozie metastore를 만드는 방법에 대한 지침은 [워크플로에 대해 Oozie 사용](hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md)
