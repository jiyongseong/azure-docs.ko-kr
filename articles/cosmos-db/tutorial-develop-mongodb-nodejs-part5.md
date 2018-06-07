---
title: Azure에 대한 MongoDB, Angular 및 노드 자습서 - 5부 | Microsoft Docs
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 5부
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 7a4b25792577247d3121c5226da55081ed55ff2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Angular 및 Azure Cosmos DB를 사용하여 MongoDB 앱 만들기 - 5부: Mongoose를 사용하여 Azure Cosmos DB에 연결

이 다중 파트 자습서에서는 Express, Angular Azure Cosmos DB 데이터베이스를 포함한 Node.js로 작성된 새 [MongoDB API](mongodb-introduction.md) 앱을 만드는 방법을 보여줍니다.

자습서의 5부는 [4부](tutorial-develop-mongodb-nodejs-part4.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Mongoose를 사용하여 Azure Cosmos DB에 연결
> * Azure Cosmos DB에서 연결 문자열 정보 가져오기
> * Hero 모델 만들기
> * Hero 데이터를 가져올 Hero 서비스 만들기
> * 로컬에서 앱 실행

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>필수 조건

자습서의 이 부분을 시작하기 전에 자습서 [4부](tutorial-develop-mongodb-nodejs-part4.md)의 단계를 완료했는지 확인합니다.

> [!TIP]
> 이 자습서에서는 응용 프로그램을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 응용 프로그램을 가져올 수 있습니다.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Mongoose를 사용하여 Azure Cosmos DB에 연결

1. MongoDB에서 일반적으로 사용되는 API인 mongoose npm 모듈을 설치합니다.

    ```bash
    npm i mongoose --save
    ```

2. 이제 **mongo.js**라는 **서버** 폴더에서 새 파일을 만듭니다. 이 파일에서 Azure Cosmos DB 데이터베이스에 대한 연결 정보를 모두 추가합니다.

3. 다음 코드를 **mongo.js**에 복사합니다. 이 코드에서는 다음을 수행합니다.
    * Mongoose가 필요합니다.
    * ES6/ES2015 이상에 빌드된 기본 프라미스를 사용하도록 Mongo 프라미스를 재정의합니다.
    * 스테이징, 프로덕션 또는 개발 중인지에 따라 특정 작업을 설정할 수 있는 env 파일을 호출합니다. 해당 파일을 곧 만듭니다.
    * env 파일에서 설정할 수 있는 MongoDB 연결 문자열을 포함합니다.
    * Mongoose를 호출하는 연결 함수를 만듭니다.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. 탐색기 창에서 **environment**라는 **서버** 아래에 폴더를 만들고 **environment** 폴더에서 **environment.js**라는 새 파일을 만듭니다.

5. 다음 코드를 **environment.js**에 복사하도록 mongo.js 파일에서 `dbName`, `key` 및 `cosmosPort`을 포함해야 합니다.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>연결 문자열 정보 가져오기

1. **environment.js**에서 `port` 값을 10255로 변경합니다. (Azure Portal에서 Cosmos DB 포트를 찾을 수 있습니다.)

    ```javascript
    const port = 10255;
    ```

2. **environment.js**에서 `accountName` 값을 [4단계](tutorial-develop-mongodb-nodejs-part4.md)에서 만든 Azure Cosmos DB 계정의 이름으로 변경합니다. 

3. 터미널 창에서 다음 CLI 명령을 사용하여 Azure Cosmos DB 계정에 대한 기본 키를 검색합니다. 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>`은 [4단계](tutorial-develop-mongodb-nodejs-part4.md)에서 만든 Azure Cosmos DB 계정의 이름입니다.

4. 기본 키를 `key` 값으로 environment.js 파일에 복사합니다.

    이제 Azure Cosmos DB에 연결하는 데 필요한 모든 정보가 앱에 있습니다. 포털에서 이 정보를 검색할 수도 있습니다. 자세한 내용은 [사용자 지정할 MongoDB 연결 문자열 가져오기](connect-mongodb-account.md#GetCustomConnection)를 참조하세요. 포털의 사용자 이름은 environments.js에서 dbName과 동일합니다. 

## <a name="create-a-hero-model"></a>Hero 모델 만들기

1.  탐색기 창의 **서버** 폴더 아래에서 **hero.model.js** 파일을 만듭니다.

2. 다음 코드를 **hero.model.js**에 복사합니다. 이 코드에서는 다음을 수행합니다.
   * Mongoose가 필요합니다.
   * ID, 이름 및 설명을 포함한 새 스키마를 만듭니다.
   * 스키마를 사용하여 모델을 만듭니다.
   * 모델을 내보냅니다. 
   * Heroes 컬렉션의 이름을 지정합니다(Mongoose 복수 명명 규칙에 따른 컬렉션의 기본 이름인 Heros가 아님).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Hero 서비스 만들기

1.  탐색기 창의 **서버** 폴더 아래에서 **hero.service.js** 파일을 만듭니다.

2. 다음 코드를 **hero.service.js**에 복사합니다. 이 코드에서는 다음을 수행합니다.
   * 방금 만든 모델을 가져옵니다.
   * 데이터베이스에 연결
   * hero.find 메서드를 사용하여 모든 Heroes를 반환하는 쿼리를 정의하는 docquery 변수를 만듭니다.
   * 모든 Heroes 목록을 가져오려면 프라미스에서 docquery.exec로 쿼리를 실행합니다. 여기서 응답 상태는 200입니다. 
   * 상태가 500인 경우 오류 메시지를 다시 보냅니다.
   * 모듈을 사용하기 때문에 Heroes를 가져옵니다. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>routes.js에 Hero 서비스 추가

1. Visual Studio Code의 **routes.js**에서 샘플 Hero 데이터를 보내는 `res.send` 함수를 주석으로 처리하고 대신 `heroService.getHeroes` 함수를 호출하는 줄을 추가합니다.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. **routes.js**에서는 Hero 서비스가 필요합니다.

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. **hero.service.js**에서 다음과 같이 `req` 및 `res` 매개 변수를 사용하도록 getHeroes 함수를 업데이트합니다.

    ```javascript
    function getHeroes(req, res) {
    ```

    여기에서 호출 체인을 검토하고 설명하겠습니다. 먼저 노드 서버를 설정하는 `index.js`의 경우, 경로를 설정하고 정의합니다. Hero 서비스에서 routes.js 파일을 사용하여 getHeroes와 같은 기능을 가져오고 요청 및 응답을 전달하도록 지시합니다. 여기에서 hero.service.js는 모델을 Mongo에 연결할 것이며, 호출하는 경우 getHeroes를 실행하고 200이라는 응답을 반환하게 됩니다. 그런 다음 체인을 통해 다시 돌아옵니다. 

## <a name="run-the-app"></a>앱 실행

1. 이제 앱을 다시 실행하겠습니다. Visual Studio Code에서 모든 변경 내용을 저장하고, 왼쪽에서 **디버그** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)을 클릭하고 **디버깅 시작** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)을 클릭합니다.

3. 이제 브라우저로 돌아가서 개발자 도구 및 네트워크 탭을 연 다음 http://localhost:3000로 이동하면 응용 프로그램이 있습니다.

    ![Azure Portal의 새 Azure Cosmos DB 계정](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Heroes가 아직 앱에 저장되지 않았지만 자습서의 다음 단계에서 배치, 푸시 및 삭제 기능을 추가하므로 Azure Cosmos DB 데이터베이스에 대한 Mongoose 연결을 사용하여 UI의 Heroes를 추가, 업데이트 및 삭제할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Mongoose API를 사용하여 Azure Cosmos DB에 Heroes 앱 연결 
> * 앱에 Heroes 기능 가져오기 추가

자습서의 다음 단계로 진행하여 앱에 게시, 배치 및 삭제 함수를 추가할 수 있습니다.

> [!div class="nextstepaction"]
> [앱에 함수 게시, 배치 및 삭제 추가](tutorial-develop-mongodb-nodejs-part6.md)
