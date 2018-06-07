---
title: Azure에 대한 MongoDB, Angular 및 노드 자습서 - 2부 | Microsoft Docs
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 2부
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
ms.openlocfilehash: 9d3c3209807a201f11d4d0a4ddd905f332a3951f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>Angular 및 Azure Cosmos DB를 사용하여 MongoDB 앱 만들기 - 2부: Angular CLI를 사용하여 Node.js Express 앱 만들기 

이 다중 파트 자습서에서는 Express, Angular Azure Cosmos DB 데이터베이스를 포함한 Node.js로 작성된 새 [MongoDB API](mongodb-introduction.md) 앱을 만드는 방법을 보여줍니다.

자습서의 2부는 [소개](tutorial-develop-mongodb-nodejs.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Angular CLI 및 TypeScript 설치
> * Angular를 사용하여 새 프로젝트 만들기
> * Express 프레임워크를 사용하여 앱 빌드
> * Postman에서 앱 테스트

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>필수 조건

자습서의 이 부분을 시작하기 전에 [소개 비디오](tutorial-develop-mongodb-nodejs.md)를 시청했는지 확인합니다.

또한 이 자습서를 사용하려면 다음이 필요합니다. 
* [Node.js](https://nodejs.org/) 버전 8.4.0 이상
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) 또는 즐겨 찾는 코드 편집기

> [!TIP]
> 이 자습서에서는 응용 프로그램을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 응용 프로그램을 가져올 수 있습니다.

## <a name="install-the-angular-cli-and-typescript"></a>Angular CLI 및 TypeScript 설치

1. Windows 명령 프롬프트 또는 Mac 터미널 창을 열고 Angular CLI를 설치합니다.

    ```bash
    npm install -g @angular/cli
    ```

2. 프롬프트에서 다음 명령을 입력하여 TypeScript를 설치합니다. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Angular CLI를 사용하여 새 프로젝트 만들기

1. 명령 프롬프트에서 새 프로젝트를 만들려는 폴더로 변경하고 다음 명령을 실행합니다. 이 명령은 새 폴더 및 angular-cosmosdb라는 프로젝트를 만들고 새 앱에 필요한 Angular 구성 요소를 설치합니다. 또한 src/client 폴더에 소스 코드를 설치(-sd src/client)하고, 최소한의 설치를 사용하고(--minimal), 프로젝트에서 Sass를 사용하도록 지정합니다(플래그로 지정된 CSS 유사 구문 --style scss).

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. 명령이 완료되면 src/client 폴더로 디렉터리를 변경합니다.

    ```bash
    cd angular-cosmosdb
    ```

3. 그런 다음 Visual Studio Code에서 폴더를 엽니다.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Express 프레임워크를 사용하여 앱 빌드

1. Visual Studio Code의 **탐색기** 창에서 **src** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 폴더**를 클릭하고 새 폴더 이름을 *server*로 지정합니다.

2. **탐색기** 창에서 **server** 폴더를 마우스 오른쪽 단추로 클릭하고, **새 파일**을 클릭하고, 새 파일 이름을 *index.js*로 지정합니다.

3. 명령 프롬프트로 다시 돌아가서 본문 파서를 설치하는 다음 명령을 사용합니다. 이렇게 하면 앱에서 API를 통해 전달되는 JSON 데이터를 구문 분석할 수 있습니다.

    ```bash
    npm i express body-parser --save
    ```

4. Visual Studio Code에서 index.js 파일에 다음 코드를 복사합니다. 이 코드에서는 다음을 수행합니다.
    * 참조 Express
    * 요청 본문에서 JSON 데이터를 읽기 위해 본문 파서 가져오기
    * 경로라는 기본 제공 기능 사용
    * 쉽게 코드의 위치를 찾을 수 있도록 루트 변수 설정
    * 포트 설정
    * Express 시작
    * 서버를 제공하는 데 사용하려고 하는 미들웨어를 사용하는 방법을 앱에 지시합니다
    * dist 폴더에 있는 모든 항목을 제공하여 고정 콘텐츠로 사용
    * 응용 프로그램 제공 및 서버에 없는 모든 GET 요청에 index.html 제공(딥 링크의 경우)
    * app.listen에서 서버 시작
    * arow 함수를 사용하여 활성 포트 기록
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Visual Studio Code의 **탐색기** 창에서 **server** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 파일**을 클릭합니다. 새 파일의 이름을 *routes.js*로 지정합니다. 

6. 다음 코드를 **routes.js**에 복사합니다. 이 코드에서는 다음을 수행합니다.
   * Express 라우터 참조
   * Heroes 가져오기
   * 정의된 Heroes에 JSON 다시 전송

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. 수정한 모든 파일 저장 

8. Visual Studio Code에서 **디버그** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png)을 클릭하고, 기어 단추 ![Visual Studio Code의 기어 단추](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png)를 클릭하고, **Node.js**를 선택하여 구성을 만듭니다.

   Visual Studio Code에서 새로운 launch.json 파일이 열립니다.

8. launch.json 파일의 11줄에서 `"program": "${file}"`을 `"program": "${workspaceRoot}/src/server/index.js"`로 변경하고 파일을 저장합니다.

9. **디버깅 시작** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png)을 클릭하여 앱을 실행합니다.

    앱이 오류 없이 실행되어야 합니다.

## <a name="use-postman-to-test-the-app"></a>Postman을 사용하여 앱 테스트

1. 이제 Postman을 열고 GET 상자에 `http://localhost:3000/api/heroes`를 배치합니다. 

2. **보내기** 단추를 클릭하고 앱에서 json 응답을 가져옵니다. 

    이 응답에서는 앱을 로컬로 실행된다고 표시합니다. 

    ![요청 및 응답을 보여주는 Postman](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Angular CLI를 사용하여 Node.js 프로젝트 만들기
> * Postman을 사용하여 앱 테스트

UI를 빌드하는 자습서의 다음 단계로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Angular를 사용하여 UI 빌드](tutorial-develop-mongodb-nodejs-part3.md)
