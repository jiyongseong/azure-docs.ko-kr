---
title: MongoDB API를 사용하여 Azure Cosmos DB 앱 빌드 | Microsoft Docs
description: 이 문서는 MongoDB용 Azure Cosmos DB API를 사용하여 온라인 데이터베이스를 만드는 자습서입니다.
keywords: MongoDB 예제
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: anhoh
ms.openlocfilehash: 81eff479c94af938918e6a221d45184ca1a84aef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Node.js를 사용하여 Azure Cosmos DB: MongoDB API 앱 빌드
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [MongoDB용 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

이 예제에서는 Node.js를 사용하여 Azure Cosmos DB: MongoDB API 콘솔 앱을 빌드하는 방법을 보여 줍니다.

이 예제를 사용하려면 다음을 수행해야 합니다.

* Azure Cosmos DB: MongoDB API 계정을 [만듭니다](create-mongodb-dotnet.md#create-account).
* MongoDB [연결 문자열](connect-mongodb-account.md) 정보를 검색합니다.

## <a name="create-the-app"></a>앱 만들기

1. *app.js* 파일을 만들고 아래 코드를 복사하여 붙여넣습니다.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **선택 사항**: **MongoDB Node.js 2.2 드라이버**를 사용 중인 경우 다음 코드 조각을 바꾸십시오.

    원본:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    다음으로 바꿔여 합니다.

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. 계정 설정에 따라 *app.js* 파일에서 다음 변수를 수정합니다([연결 문자열](connect-mongodb-account.md)을 찾는 방법 자세히 알아보기).

    > [!IMPORTANT]
    > **MongoDB Node.js 3.0 드라이버**는 Cosmos DB 암호에서 특수 문자를 인코딩할 것을 요구합니다. '=' 문자를 %3D로 인코딩하는지 확인
    >
    > 예: 암호 *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==*가 *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*로 인코딩됩니다
    >
    > **MongoDB Node.js 2.2 드라이버**는 Cosmos DB 암호에서 특수 문자를 인코딩할 것을 요구하지 않습니다.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. 즐겨찾는 터미널을 열고 **npm install mongodb --save**를 설치한 다음 **node app.js**로 앱을 실행합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB: MongoDB API 계정으로 [MongoChef를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
