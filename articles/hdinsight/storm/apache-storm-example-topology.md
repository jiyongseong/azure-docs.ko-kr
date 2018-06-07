---
title: HDInsight에서 Apache Storm 토폴로지 예제 | Microsoft Docs
description: Event Hubs와 함께 작동하며 기본 C# 및 Java 토폴로지를 포함하여 HDInsight에서 Apache Storm을 통해 생성 및 테스트된 Storm 토폴로지 예제 목록입니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: f633797147b2090680ebb5ea11f60271addc7f52
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>HDInsight의 Apache Storm에 대한 Storm 토폴로지 및 구성 요소 예제

다음은 HDInsight에서 Apache Storm과 함께 사용하기 위해 Microsoft에서 만들고 유지 관리하는 예제 목록입니다. 이러한 예제에서는 기본 C# 및 Java 토폴로지 만들기부터 Event Hub, Cosmos DB, SQL Database, HDInsight의 HBase, Azure Storage 등의 Azure 서비스 작업에 이르는 다양한 항목을 다룹니다. 또한 일부 예제에서는 SignalR 및 Socket.IO와 같은 Azure가 아니거나 Microsoft가 아닌 기술로 작업하는 방법을 보여 줍니다.

| 설명 | 데모 | 언어/프레임워크 |
|:--- |:--- |:--- |
| [Apache Storm에서 Azure 데이터 레이크 저장소에 쓰기](apache-storm-write-data-lake-store.md) |Azure 데이터 레이크 저장소에 쓰기 |자바 |
| [이벤트 허브 Spout 및 Bolt 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |이벤트 허브 Spout 및 Bolt에 대한 소스 |자바 |
| [HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지 개발][5797064f] |Maven |자바 |
| [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발][16fce2d1] |Visual Studio용 HDInsight 도구 |C#, Java |
| [HDInsight의 Storm(C#)에서 Azure Event Hubs의 이벤트 처리][844d1d81] |Event Hubs |C# 및 Java |
| [HDInsight의 Storm으로 Azure Event Hubs에서 이벤트 처리(Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |자바 |
| [HDInsight의 Storm을 사용하여 Event Hub에서 차량 센서 데이터 처리][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob(WASB) |C#, Java |
| [HDInsight의 Storm을 사용하여 Azure Event Hub에서 HBase로 ETL(추출, 변환 및 로드)][b4b68194] |Event Hubs, HBase |C# |
| [HDInsight의 Storm에서 Azure 서비스로 작업하는 데 사용되는 템플릿 C# Storm 토폴로지 프로젝트][ce0c02a2] |Event Hub, Cosmos DB SQL Database, HBase, SignalR |C#, Java |
| [HDInsight의 Storm을 사용하여 Azure Event Hub에서 읽기에 대한 확장성 벤치마크][d6c540e3] |메시지 처리량, Event Hubs, SQL Database |C#, Java |
| [HDInsight에서 Storm으로 Python 사용](apache-storm-develop-python-topology.md) |Flux 토폴로지를 포함하는 Python 구성 요소 |파이썬 |
| [HDInsight의 Storm에서 Kafka 사용](../hdinsight-apache-storm-with-kafka.md) | Apache Kafka에 Apache Storm 읽기 및 쓰기 | 자바 |

### <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Storm 시작][2b8c3488]
* [HDInsight의 Storm을 사용하여 Storm 토폴로지를 배포 및 관리하는 방법에 대해 알아봅니다.][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "HDInsight 클러스터에서 Storm을 만들고 Storm Dashboard를 사용하여 예제 토폴로지를 배포하는 방법에 대해 알아봅니다."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "배포 및 웹 기반 Storm 대시보드 및 Storm UI 또는 HDInsight 도구를 사용하여 Visual Studio에 대한 토폴로지를 관리하는 방법에 알아봅니다."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "HDInsight Tools for Visual Studio를 사용하여 C# Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
[5797064f]:apache-storm-develop-java-topology.md "기본 단어 개수 토폴로지를 만들어 Maven을 사용하여 Java로 Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "HDInsight의 Storm을 사용하여 Azure Event Hub에서 데이터를 읽고 쓰는 방법에 대해 알아봅니다."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Storm 토폴로지를 사용하여 Azure Event Hub에서 메시지를 읽고, 데이터 참조를 위해 Azure Cosmos DB에서 문서를 읽고, Azure Storage에 데이터를 저장하는 방법에 대해 알아봅니다."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "HDInsight의 Apache Storm을 사용하여 Azure Event Hub에서 읽고 SQL Database에 저장할 때의 처리량을 보여 주는 여러 토폴로지입니다."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure Event Hub에서 데이터를 읽고, 데이터를 집계 및 변환한 다음, HDInsight의 HBase에 저장하는 방법에 대해 알아봅니다."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "이 프로젝트에는 Event Hub, Cosmos DB 및 SQL Database와 같은 다양한 Azure 서비스와 상호 작용하는 토폴로지에 대한 Spout, Bolt 및 템플릿이 포함되어 있습니다."

