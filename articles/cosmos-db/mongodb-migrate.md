---
title: MongoDB용 Azure Cosmos DB API와 함께 mongoimport 및 mongorestore 사용 | Microsoft Docs
description: mongoimport 및 mongorestore를 사용하여 데이터를 MongoDB API 계정으로 가져오는 방법을 알아봅니다.
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 36d098a76e57b65ba82c24ed81ebbe3d21489a9f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: MongoDB 데이터 가져오기 

MongoDB API와 함께 사용하기 위해 MongoDB에서 Azure Cosmos DB 계정으로 데이터를 마이그레이션하려면 다음을 수행해야 합니다.

* [MongoDB Download Center](https://www.mongodb.com/download-center)에서 *mongoimport.exe* 또는 *mongorestore.exe*를 다운로드합니다.
* [MongoDB API 연결 문자열](connect-mongodb-account.md)을 가져옵니다.

MongoDB에서 데이터를 가져와 Azure Cosmos DB SQL API에 사용하려는 경우 [데이터 마이그레이션 도구](import-data.md)를 사용해서 데이터를 가져와야 합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 연결 문자열 검색
> * mongoimport를 사용하여 MongoDB 데이터 가져오기
> * mongorestore를 사용하여 MongoDB 데이터 가져오기

## <a name="prerequisites"></a>필수 조건

* 처리량 늘리기: 데이터 마이그레이션 기간은 개별 컬렉션 또는 컬렉션 집합에 대해 설정한 처리량에 따라 다릅니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. [Azure Portal](https://portal.azure.com)에서 처리량을 늘리는 방법에 대한 자세한 내용은 [Azure Cosmos DB의 성능 수준 및 가격 책정 계층](performance-levels.md)을 참조하세요.

* SSL 사용: Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 있습니다. 계정과 상호 작용하는 경우 SSL을 사용해야 합니다. 나머지 문서의 절차에서는 mongoimport 및 mongorestore에 대해 SSL을 사용하도록 설정하는 방법을 설명합니다.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>연결 문자열 정보(호스트, 포트, 사용자 이름 및 암호) 찾기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Cosmos DB** 항목을 클릭합니다.
2. **구독** 창에서 계정 이름을 선택합니다.
3. **연결 문자열** 블레이드에서 **연결 문자열**을 클릭합니다.

   오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.

   ![연결 문자열 블레이드](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>mongoimport를 사용하여 MongoDB API로 데이터 가져오기

데이터를 Azure Cosmos DB 계정으로 가져오려면 다음 템플릿을 사용합니다. *호스트*, *사용자 이름* 및 *암호*를 계정과 관련된 값으로 채웁니다.  

템플릿:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

예:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>mongorestore를 사용하여 MongoDB API로 데이터 가져오기

데이터를 MongoDB API 계정으로 복원하려면 다음 템플릿을 사용하여 가져오기를 실행합니다. *호스트*, *사용자 이름* 및 *암호*를 계정과 관련된 값으로 채웁니다.

템플릿:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

예:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>성공적인 마이그레이션 가이드

1. 컬렉션을 미리 만들어 크기 조정:
        
    * 기본적으로 Azure Cosmos DB는 1,000RU(요청 단위/초)로 새 MongoDB 컬렉션을 프로비전합니다. mongoimport, mongorestore 또는 mongomirror를 사용하여 마이그레이션을 시작하기 전에 [Azure Portal](https://portal.azure.com) 또는 MongoDB 드라이버 및 도구에서 모든 컬렉션을 미리 만듭니다. 컬렉션이 10GB보다 큰 경우 적절한 분할 키로 [분할/파티션된 컬렉션](partition-data.md)을 만들어야 합니다.

    * [Azure Portal](https://portal.azure.com)에서 단일 파티션 컬렉션에 대해 1,000RU/초 및 분할된 컬렉션에 대해 2,500RU/초부터 컬렉션의 처리량을 증가시킵니다. 처리량이 높을수록 제한을 피하고 마이그레이션 시간을 단축할 수 있습니다. Azure Cosmos DB에서는 시간당 청구되므로 마이그레이션 후 바로 처리량을 줄여 비용을 절감할 수 있습니다.

    * 컬렉션 수준에서 RU/초를 프로비전하는 것 외에도, 부모 데이터베이스 수준에서 컬렉션 집합에 대한 RU/초를 프로비전할 수도 있습니다. 이렇게 하려면 데이터베이스 및 컬렉션을 미리 만들고, 각 컬렉션에 대해 분할 키를 정의해야 합니다.

    * 선호하는 도구, 드라이버 또는 SDK를 통해 분할된 컬렉션을 만들 수 있습니다. 이 예제에서는 Mongo Shell을 사용하여 분할된 컬렉션을 만듭니다.

        ```
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        결과:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

2. 단일 문서 쓰기에 대해 대략적인 RU 요금 계산:

    a. MongoDB 셸에서 Azure Cosmos DB MongoDB 데이터베이스에 연결합니다. [Azure Cosmos DB에 MongoDB 응용 프로그램 연결](connect-mongodb-account.md)에서 지침을 찾을 수 있습니다.
    
    나. MongoDB 셸에서 샘플 문서 중 하나를 사용하여 샘플 삽입 명령을 실행합니다.
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    다. ```db.runCommand({getLastRequestStatistics: 1})```를 실행하고 다음과 같은 응답을 수신합니다.
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. 요청 요금을 기록해 둡니다.
    
3. 컴퓨터에서 Azure Cosmos DB 클라우드 서비스까지의 대기 시간을 확인합니다.
    
    a. 다음 명령을 사용하여 MongoDB 셸에서 자세한 로깅 정보를 표시합니다. ```setVerboseShell(true)```
    
    나. 데이터베이스에 대해 간단한 쿼리를 실행합니다. ```db.coll.find().limit(1)``` 다음과 같은 응답을 수신합니다.

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. 마이그레이션 이전에 삽입된 문서를 제거하여 중복된 문서가 없도록 합니다. 다음 명령을 사용하여 문서를 제거할 수 있습니다. ```db.coll.remove({})```

5. 대략적인 *batchSize* 및 *numInsertionWorkers* 값을 계산합니다.

    * *batchSize*에 대해 프로비전된 총 RU를 3단계의 단일 문서 쓰기에서 사용한 RU로 나눕니다.
    
    * 계산된 *batchSize* <= 24이면 이 숫자를 *batchSize* 값으로 사용합니다.
    
    * 계산된 *batchSize* > 24이면 *batchSize* 값을 24로 설정합니다.
    
    * *numInsertionWorkers*의 경우 *numInsertionWorkers =  (프로비전된 처리량 * 초 단위 대기 시간)/(배치 크기 * 단일 쓰기에 사용한 RU)* 수식을 사용합니다.
        
    |자산|값|
    |--------|-----|
    |batchSize| 24 |
    |프로비전된 RU | 10000 |
    |대기 시간 | 0.100s |
    |1개 문서 쓰기에 대해 청구된 RU | 10RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RU x 0.1s) / (24 x 10 RU) = 4.1666*

6. 최종 마이그레이션 명령을 실행합니다.

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>다음 단계

다음 자습서로 진행하여 Azure Cosmos DB로 MongoDB 데이터를 쿼리하는 방법을 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
>[MongoDB 데이터를 쿼리하는 방법](../cosmos-db/tutorial-query-mongodb.md)
