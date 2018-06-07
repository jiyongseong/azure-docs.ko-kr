---
title: Cassandra 데이터를 Azure Cosmos DB로 가져오기 | Microsoft Docs
description: CQL Copy 명령을 사용하여 Cassandra 데이터를 Azure Cosmos DB에 복사하는 방법에 대해 알아봅니다.
services: cosmos-db
author: govindk
manager: kfile
documentationcenter: ''
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 64f60e6beb5451d8f5acd382ca8e5672a2d096f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: Cassandra 데이터 가져오기

이 자습서에서는 CQL(Cassandra Query Language) COPY 명령을 사용하여 Cassandra 데이터를 Azure Cosmos DB로 가져오는 방법을 설명합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 연결 문자열 검색
> * cqlsh COPY 명령을 사용하여 데이터 가져오기
> * Spark 커넥터를 사용하여 가져오기 

# <a name="prerequisites"></a>필수 조건

* [Apache Cassandra](http://cassandra.apache.org/download/)를 설치하고 특히 *cqlsh*가 있는지 확인합니다.
* 처리량 늘리기: 데이터 마이그레이션 기간은 테이블에 대해 프로비전한 처리량에 따라 다릅니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. [Azure Portal](https://portal.azure.com)에서의 처리량 증가에 대한 자세한 내용은 [Azure Cosmos DB 컨테이너에 대한 처리량 설정](set-throughput.md)을 참조하세요.
* SSL 사용: Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 SSL을 사용해야 합니다. SSH와 함께 CQL을 사용하는 경우 SSL 정보를 제공할 수 있는 옵션이 있습니다. 

## <a name="find-your-connection-string"></a>연결 문자열 찾기

1. [Azure portal](https://portal.azure.com)의 맨 왼쪽에서 **Azure Cosmos DB**를 클릭합니다.

2. **구독** 창에서 계정 이름을 선택합니다.

3. **연결 문자열**을 클릭합니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.

    ![연결 문자열 페이지](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>cqlsh COPY 사용

Cassandra API에서 사용할 수 있도록 Cassandra 데이터를 Azure Cosmos DB로 가져오려면 다음 지침을 사용하세요.

1. 포털에서 연결 정보를 사용하여 cqhsh에 로그인합니다.
2. [CQL COPY 명령](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)을 사용하여 로컬 데이터를 Apache Cassandra API 끝점에 복사합니다. 대기 시간 문제를 최소화하려면 소스 및 대상이 동일한 데이터 센터에 있는지 확인하세요.

### <a name="guide-for-moving-data-with-cqlsh"></a>데이터를 cqlsh로 이동하기 위한 가이드

1. 테이블을 미리 만들고 크기를 조정합니다.
    * 기본적으로 Azure Cosmos DB는 초당 1,000개의 요청 단위(RU/s)가 있는 새 Cassandra API 테이블을 만듭니다(CQL 기반 만들기는 400RU/s로 프로비전됨). cqlsh를 사용하여 마이그레이션을 시작하기 전에 [Azure Portal](https://portal.azure.com) 또는 cqlsh에서 모든 테이블을 미리 만듭니다. 

    * [Azure Portal](https://portal.azure.com)에서 테이블의 처리량을 기본 처리량(400 또는 1,000RU/s)에서 10,000RU/s로 늘립니다. 처리량이 높을수록 제한을 피하고 마이그레이션 시간을 단축할 수 있습니다. Azure Cosmos DB에서는 시간당 청구되므로 마이그레이션 후 바로 처리량을 줄여 비용을 절감할 수 있습니다.

2. 작업에 대한 RU 비용을 확인합니다. 선택한 내용의 Azure Cosmos DB Cassandra API SDK를 사용하여 이 작업을 수행할 수 있습니다. 다음 예제는 RU 비용 가져오기의 .NET 버전을 보여 줍니다. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. 컴퓨터에서 Azure Cosmos DB 서비스까지의 대기 시간을 확인합니다. Azure 데이터 센터 내에 있는 경우 대기 시간은 단일 밀리초의 낮은 숫자여야 합니다. Azure 데이터 센터 외부에 있는 경우 psping 또는 azurespeed.com을 사용하여 현재 위치로부터 대략적인 대기 시간을 가져올 수 있습니다.   

4. 우수한 성능을 제공하는 매개 변수(NUMPROCESS, INGESTRATE, MAXBATCHSIZE 또는 MINBATCHSIZE)에 대한 적절한 값을 계산합니다. 

5. 최종 마이그레이션 명령을 실행합니다. 이 명령을 실행하면 연결 문자열 정보를 사용하여 cqlsh를 시작한 것으로 간주됩니다.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Spark를 사용하여 데이터 가져오기

Azure 가상 머신의 기존 클러스터에 상주하는 데이터의 경우 Spark를 사용하여 데이터를 가져오는 작업도 가능합니다. 이렇게 하려면 Spark를 한 번 또는 정기적으로 중간자로 설정해야 합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 완료하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 연결 문자열 검색
> * cql copy 명령을 사용하여 데이터 가져오기
> * Spark 커넥터를 사용하여 가져오기 

이제 개념 섹션으로 진행하여 Azure Cosmos DB에 대한 자세한 정보를 확인할 수 있습니다. 

> [!div class="nextstepaction"]
>[Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](../cosmos-db/consistency-levels.md)
