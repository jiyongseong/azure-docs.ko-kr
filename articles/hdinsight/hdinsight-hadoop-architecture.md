---
title: Hadoop 아키텍처 - Azure HDInsight | Microsoft Docs
description: HDInsight 클러스터의 Hadoop 저장소 및 처리에 대해 설명합니다.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5ec5f1f24d3bf953115bfa5023faf81df132f510
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="hadoop-architecture-in-hdinsight"></a>HDInsight의 Hadoop 아키텍처

Hadoop에는 저장소를 제공하는 HDFS(고밀도 파일 시스템)와 처리 제공하는 YARN(Yet Another Resource Negotiator)의 두 가지 핵심 구성 요소가 포함되어 있습니다. 저장소 및 처리 기능을 사용하면 클러스터에서 MapReduce 프로그램을 실행하여 원하는 데이터 처리를 수행할 수 있게 됩니다.

> [!NOTE]
> 일반적으로 HDFS는 HDInsight 클러스터 내에 배포되어 저장소를 제공하지 않습니다. 대신 Hadoop 구성 요소에서 HDFS 호환 인터페이스 계층을 사용합니다. 실제 저장소 용량은 Azure Storage 또는 Azure Data Lake Store에서 제공합니다. Hadoop의 경우 HDFS가 있는 것처럼 HDInsight 클러스터에서 실행되는 MapReduce 작업이 실행되므로 저장소 요구 사항을 지원하기 위해 변경할 필요가 없습니다. HDInsight의 Hadoop에서 저장소는 아웃소싱되지만 YARN 처리는 핵심 구성 요소로 남아 있습니다. 자세한 내용은 [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)를 참조하세요.

이 문서에서는 YARN과 HDInsight에서 응용 프로그램 실행을 조정하는 방법에 대해 소개합니다.

## <a name="yarn-basics"></a>YARN 기본 사항 

YARN은 Hadoop의 데이터 처리를 제어하고 조정합니다. YARN에는 클러스터의 노드에서 프로세스로 실행되는 다음 두 가지 핵심 서비스가 있습니다. 

* ResourceManager 
* NodeManager

ResourceManager는 MapReduce 작업과 같은 응용 프로그램에 클러스터 계산 리소스를 제공합니다. ResourceManager는 이러한 리소스를 컨테이너로 제공하며, 각 컨테이너는 CPU 코어 수 및 RAM 메모리의 할당으로 구성됩니다. 클러스터에서 사용할 수 있는 모든 리소스를 결합한 다음, 지정된 개수의 코어 및 메모리 블록으로 분산한 경우 각 리소스 블록이 컨테이너가 됩니다. 클러스터의 각 노드에는 특정 수의 컨테이너에 대한 용량이 있으므로, 클러스터에는 사용할 수 있는 컨테이너 수에 대해 고정된 제한이 있습니다. 컨테이너에 있는 리소스의 할당은 구성할 수 있습니다. 

MapReduce 응용 프로그램이 클러스터에서 실행될 때, ResourceManager는 실행할 컨테이너를 응용 프로그램에 제공합니다. ResourceManager는 실행 중인 응용 프로그램의 상태와 사용 가능한 클러스터 용량을 추적하고, 리소스가 완료되고 해제될 때 응용 프로그램을 추적합니다. 

또한 ResourceManager는 응용 프로그램의 상태를 모니터링하는 데 사용할 수 있는 웹 사용자 인터페이스를 제공하는 웹 서버 프로세스도 실행합니다. 

사용자가 클러스터에서 실행할 MapReduce 응용 프로그램을 제출하면 해당 응용 프로그램이 ResourceManager에 제출됩니다. 이에 따라 ResourceManager는 사용 가능한 NodeManager 노드에 컨테이너를 할당합니다. NodeManager 노드는 실제로 응용 프로그램이 실행되는 위치입니다. 할당된 첫 번째 컨테이너에서 ApplicationMaster라는 특별한 응용 프로그램을 실행합니다. 이 ApplicationMaster는 제출된 응용 프로그램을 실행하는 데 필요한 리소스를 후속 컨테이너의 형태로 확보해야 합니다. ApplicationMaster는 응용 프로그램의 단계(맵 단계 및 축소 단계)를 검사하고 처리해야 할 데이터의 양을 결정합니다. 그런 다음 ApplicationMaster는 응용 프로그램을 대신하여 ResourceManager에서 리소스를 요청(*협상*)합니다. 이에 따라 ResourceManager는 응용 프로그램을 실행하는 데 사용하도록 클러스터의 NodeManager에서 ApplicationMaster로 리소스를 제공합니다. 

NodeManager는 응용 프로그램을 구성하는 작업을 실행한 다음, 진행 상황과 상태를 ApplicationMaster에 다시 보고합니다. ApplicationMaster는 다시 응용 프로그램의 상태를 ResourceManager에 보고합니다. 이에 따라 ResourceManager는 모든 결과를 클라이언트에 반환합니다.

## <a name="yarn-on-hdinsight"></a>HDInsight의 YARN

모든 HDInsight 클러스터 유형은 YARN을 배포합니다. ResourceManager는 고가용성을 위해 클러스터 내의 첫 번째 및 두 번째 헤드 노드에서 각각 실행되는 주 인스턴스와 보조 인스턴스를 통해 배포됩니다. ResourceManager 인스턴스는 한 번에 하나씩 활성화됩니다. NodeManager 인스턴스는 클러스터의 사용 가능한 작업자 노드에서 실행됩니다.

![HDInsight의 YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Hadoop과 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
