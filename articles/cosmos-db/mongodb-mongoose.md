---
title: Azure Cosmos DB에 Mongoose 프레임워크 사용 | Microsoft Docs
description: Node.js Mongoose 앱을 Azure Cosmos DB에 연결하는 방법을 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: romitgirdhar
manager: kfile
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 4ce86c6a3fb3d191f101329025d0aedf266ce79e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: Azure Cosmos DB에 Mongoose 프레임워크 사용

이 자습서에서는 Azure Cosmos DB에 데이터를 저장할 때 [Mongoose 프레임워크](http://mongoosejs.com/)를 사용하는 방법을 보여 줍니다. 이 연습에서는 Azure Cosmos DB용 MongoDB API를 사용합니다. 잘 모르는 사람도 있겠지만, Mongoose는 Node.js에 있는 MongoDB용 개체 모델링 프레임워크이며 응용 프로그램 데이터를 모델링하기 위한 간단한 스키마 기반 솔루션을 제공합니다.

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) 버전 v0.10.29 이상

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 보겠습니다. 사용하려는 계정이 이미 있는 경우 [Node.js 응용 프로그램 설치](#SetupNode)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [Node.js 응용 프로그램 설정](#SetupNode)으로 건너뜁니다.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Node.js 응용 프로그램 설정

>[!Note]
> 응용 프로그램 자체를 설정하는 대신 샘플 코드를 연습하려는 경우 이 자습서에 사용되는 [샘플](https://github.com/Azure-Samples/Mongoose_CosmosDB)을 복제하고 Azure Cosmos DB에서 Node.js Mongoose 응용 프로그램을 빌드합니다.

1. 선택한 폴더에 Node.js 응용 프로그램을 만들려면 노드 명령 프롬프트에서 다음 명령을 실행합니다.

    ```npm init```

    질문에 답변하면 프로젝트를 이동할 준비가 됩니다.

1. 폴더에 새 파일을 추가하고 이름을 ```index.js```로 지정합니다.
1. ```npm install``` 옵션 중 하나를 사용하여 필요한 패키지를 설치합니다.
    * Mongoose: ```npm install mongoose --save```
    * Dotenv(.env 파일에서 비밀을 로드하려는 경우): ```npm install dotenv --save```

    >[!Note]
    > ```--save``` 플래그는 package.json 파일에 종속성을 추가합니다.

1. index.js 파일의 종속성을 가져옵니다.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Cosmos DB 연결 문자열과 Cosmos DB 이름을 ```.env``` 파일에 추가합니다.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. index.js의 끝에 다음 코드를 추가하여 Mongoose 프레임워크로 Azure Cosmos DB에 연결합니다.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > 여기서 환경 변수는 ‘dotenv’ npm 패키지를 사용하여 process.env.{variableName}(으)로 로드됩니다.

    Azure Cosmos DB에 연결되면 Mongoose에서 개체 모델 설정을 시작할 수 있습니다.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Azure Cosmos DB에 Mongoose를 사용할 경우 주의할 사항

Mongoose는 사용자가 만드는 각 모델에 대한 새 MongoDB 컬렉션을 은밀하게 만듭니다. 그러나 Azure Cosmos DB는 컬렉션당 청구 모델을 사용하므로 조밀하게 채워지지 않은 여러 개체 모델이 있는 경우 가장 비용 효율적인 방법이 아닐 수 있습니다.

이 연습에서는 두 모델을 모두 다룹니다. 먼저 컬렉션당 하나의 데이터 형식을 저장하는 연습을 합니다. 이는 사실상 Mongoose에 대한 동작입니다.

또한 Mongoose에는 [판별자](http://mongoosejs.com/docs/discriminators.html)라는 개념이 있습니다. 판별자는 스키마 상속 메커니즘입니다. 판별자를 통해 동일한 기본 MongoDB 컬렉션에서 스키마가 겹치는 여러 모델을 사용할 수 있습니다.

동일한 컬렉션에 다양한 데이터 모델을 저장한 다음 쿼리 시 필터 절을 사용하여 필요한 데이터만 가져올 수 있습니다.

### <a name="one-collection-per-object-model"></a>개체 모델당 하나의 컬렉션

기본 Mongoose 동작은 개체 모델을 만들 때마다 MongoDB 컬렉션을 만드는 것입니다. 이 섹션에서는 MongoDB for Azure Cosmos DB를 사용하여 이 작업을 수행하는 방법을 설명합니다. 대량의 데이터를 포함하는 개체 모델이 있는 경우 Azure Cosmos DB에 이 방법을 사용하는 것이 좋습니다. 이는 Mongoose의 기본 운영 모델이므로 Mongoose를 잘 알고 있다면 이 동작도 이미 알고 있을 것입니다.

1. ```index.js```를 다시 엽니다.

1. ‘Family’에 대한 스키마 정의를 만듭니다.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. ‘Family’에 대한 개체를 만듭니다.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. 마지막으로, 이 개체를 Azure Cosmos DB에 저장하겠습니다. 그러면 은밀하게 컬렉션이 생성됩니다.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. 이제 다른 스키마와 개체를 만들겠습니다. 이번에는 가족이 관심을 가질 만한 ‘Vacation Destinations’에 대한 항목을 만들겠습니다.
    1. 이전과 마찬가지로 스키마를 만들겠습니다.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. 샘플 개체(이 스키마에 여러 개체를 추가할 수 있음)를 만들고 저장합니다.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. 이제 Azure Portal로 이동하면 Azure Cosmos DB에 두 개의 컬렉션이 생성된 것을 확인할 수 있습니다.

    ![Node.js 자습서 - 컬렉션 이름을 강조 표시한 Azure Cosmos DB 계정을 표시하는 Azure Portal 스크린샷 - 노드 데이터베이스][mutiple-coll]

1. 마지막으로, Azure Cosmos DB에서 데이터를 읽겠습니다. 기본 Mongoose 운영 모델을 사용하고 있으므로 읽기는 Mongoose를 사용한 다른 읽기와 동일합니다.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Mongoose 판별자를 사용하여 단일 컬렉션에 데이터 저장

이 방법에서는 [Mongoose 판별자](http://mongoosejs.com/docs/discriminators.html)를 사용하여 각 Azure Cosmos DB 컬렉션의 비용에 최적화합니다. 판별자를 사용하면 차별화 ‘키’를 정의할 수 있으며, 이 키를 통해 여러 개체 모델을 저장, 구별 및 필터링할 수 있습니다.

이 예제에서는 기본 개체 모델을 만들고, 차별화 ‘키’를 정의하고, ‘Family’ 및 ‘VacationDestinations’를 기본 모델의 확장으로 추가합니다.

1. 기본 구성을 설정하고 판별자 키를 정의하겠습니다.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. 그런 다음 공통 개체 모델을 정의하겠습니다.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. 이제 ‘Family’ 모델을 정의합니다. 이 예제에서는 ```mongoose.model``` 대신 ```commonModel.discriminator```를 사용합니다. 또한 mongoose 스키마에 기본 구성을 추가합니다. 따라서 이 예제의 discriminatorKey는 ```FamilyType```입니다.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. 마찬가지로, 이번에는 ‘VacationDestinations’에 대한 다른 스키마를 추가하겠습니다. 여기서 DiscriminatorKey는 ```VacationDestinationsType```입니다.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. 마지막으로, 모델에 대한 개체를 만들고 저장합니다.
    1. ‘Family’ 모델에 개체를 추가하겠습니다.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. 그런 다음 ‘VacationDestinations’ 모델에 개체를 추가하고 저장하겠습니다.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. 이제 Azure Portal로 돌아가면 ‘Family’ 및 ‘VacationDestinations’ 데이터가 둘 다 포함된 ```alldata```라는 하나의 컬렉션만 표시됩니다.

    ![Node.js 자습서 - 강조 표시된 컬렉션 이름 - 노드 데이터베이스와 함께 Azure Cosmos DB 계정을 표시하는 Azure Portal 스크린샷][alldata]

1. 또한 각 개체에 ```__type```이라는 다른 특성이 있습니다. 이 특성은 두 가지 개체 모델을 구별하는 데 도움이 됩니다.

1. 마지막으로, Azure Cosmos DB에 저장된 데이터를 읽겠습니다. Mongoose는 모델을 기준으로 데이터 필터링을 처리합니다. 따라서 데이터를 읽을 때는 다른 특별한 조치가 필요하지 않습니다. 모델(이 예제에서는 ```Family_common```)을 지정하면 Mongoose가 ‘DiscriminatorKey’를 기준으로 필터링을 처리합니다.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

보시다시피 Mongoose 판별자를 쉽게 이용할 수 있습니다. 따라서 Mongoose 프레임워크를 사용하는 앱이 있을 경우 이 자습서에 따라 과도한 변경 없이 Azure Cosmos DB의 MongoDB API에서 응용 프로그램을 작동하고 실행할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

[MongoDB 기능 및 구문에 대한 MongoDB API 지원](mongodb-feature-support.md)에서 Azure Cosmos DB MongoDB API에서 지원하는 MongoDB 작업, 운영자, 단계, 명령 및 옵션에 대해 자세히 알아봅니다.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png