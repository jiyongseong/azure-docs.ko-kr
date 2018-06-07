---
title: Azure에 대한 MongoDB, Angular 및 노드 자습서 - 6부 | Microsoft Docs
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 6부
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
ms.openlocfilehash: 00a8cfebbd995d9a1bb99a691fd84bd742855f78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>Angular 및 Azure Cosmos DB를 사용하여 MongoDB 앱 만들기 - 6부: 앱에 게시, 배치 및 삭제 함수 추가

이 다중 파트 자습서에서는 Express 및 Angular와 Node.js로 작성된 새 [MongoDB API](mongodb-introduction.md) 앱을 만들고 Azure Cosmos DB 데이터베이스에 연결하는 방법을 보여줍니다.

자습서의 6부는 [5부](tutorial-develop-mongodb-nodejs-part5.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Hero 서비스에 게시, 배치 및 삭제 함수 만들기
> * 앱 실행

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>필수 조건

자습서의 이 부분을 시작하기 전에 자습서 [5부](tutorial-develop-mongodb-nodejs-part5.md)의 단계를 완료했는지 확인합니다.

> [!TIP]
> 이 자습서에서는 응용 프로그램을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 응용 프로그램을 가져올 수 있습니다.

## <a name="add-a-post-function-to-the-hero-service"></a>Hero 서비스에 게시 함수 추가

1. Visual Studio Code에서 **분할 편집기** 단추 ![Visual Studio의 분할 편집기 단추](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png)를 눌러 **routes.js** 및 **hero.service.js**를 나란히 엽니다.

    routes.js 7줄이 **hero.service.js**의 5줄에서 `getHeroes` 함수를 호출하는지 확인합니다.  게시, 배치 및 삭제 함수의 동일한 쌍을 만들어야 합니다. 

    ![Visual Studio Code의 routes.js 및 hero.service.js](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Hero 서비스를 코딩하기 시작하겠습니다. 

2. `getHeroes` 함수 뒤, `module.exports` 앞에서 **hero.service.js**에 다음 코드를 복사합니다. 이 코드에서는 다음을 수행합니다.  
   * 새 Hero를 게시하기 위해 Hero 모델을 사용합니다.
   * 응답에 오류가 있는지 확인하고 상태 값 500을 반환하는지 확인합니다.

   ```javascript
   function postHero(req, res) {
     const originalHero = { id: req.body.id, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. **hero.service.js**에서 `module.exports`를 업데이트하여 새로운 `postHero` 함수를 포함합니다. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. **routes.js**에서 `get` 라우터 뒤에 `post` 함수에 라우터를 추가합니다. 이 라우터는 한 번에 하나의 Hero를 게시합니다. 이 방식으로 라우터 파일 구조를 지정하면 사용 가능한 모든 API 끝점을 표시하고 **hero.service.js** 파일에 실제 작업만 남겨둡니다.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. 앱을 실행하여 모두 제대로 작동하는지 확인합니다. Visual Studio Code에서 모든 변경 내용을 저장하고, 왼쪽에서 **디버그** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png)을 클릭하고 **디버깅 시작** 단추 ![Visual Studio Code의 디버깅 시작 아이콘](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png)을 클릭합니다.

6. 이제 인터넷 브라우저로 다시 이동하고 대부분의 컴퓨터에서 F12 키를 눌러 개발자 도구 네트워크 탭을 엽니다. [http://localhost:3000](http://localhost:3000)으로 이동하여 네트워크를 통해 수행된 호출을 감시합니다.

    ![네트워크 활동을 보여주는 Chrome의 네트워킹 탭](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. **새 Hero 추가** 단추를 클릭하여 새 Hero를 추가합니다. ID로 "999", 이름으로 "Fred", 설명으로 "Hello"를 입력한 다음 **저장**을 클릭합니다. 새 Hero에 POST 요청을 보냈는지 네트워킹 탭에 표시됩니다. 

    ![가져오기 및 게시 함수에 대한 네트워크 활동을 보여주는 Chrome의 네트워킹 탭](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    이제 다시 돌아가서 배치 및 삭제 함수를 앱에 추가하겠습니다.

## <a name="add-the-put-and-delete-functions"></a>배치 및 삭제 함수 추가

1. **routes.js**에서 게시 라우터 위에 `put` 및 `delete` 라우터를 추가합니다.

    ```javascript
    router.put('/hero/:id', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:id', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. `checkServerError` 함수 뒤에 있는 **hero.service.js**에 다음 코드를 복사합니다. 이 코드에서는 다음을 수행합니다.
   * `put` 및 `delete` 함수 만들기
   * Hero를 찾았는지 여부에 대한 확인 수행
   * 오류 처리 수행 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       id: parseInt(req.params.id, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ id: originalHero.id }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const id = parseInt(req.params.id, 10);
     Hero.findOneAndRemove({ id: id })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. **hero.service.js**에서 새 모듈을 내보냅니다.

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. 코드를 업데이트했으므로 Visual Studio Code에서 **다시 시작** 단추 ![Visual Studio Code의 다시 시작 단추](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png)를 클릭합니다.

5. 인터넷 브라우저에서 페이지를 새로 고치고 **새 Hero 추가** 단추를 클릭합니다. ID로 "9", 이름으로 "Starlord", 설명으로 "Hi"를 포함한 새 Hero를 추가합니다. **저장** 단추를 클릭하여 새 Hero를 저장합니다.

6. 이제 **Starlord** Hero를 선택하고 설명을 "Hi"에서 "Bye"로 변경하고 **저장** 단추를 클릭합니다. 

    이제 네트워크 탭에서 ID를 선택하여 페이로드를 표시할 수 있습니다. 설명이 현재 "Bye"로 설정되었는지 페이로드에서 확인할 수 있습니다.

    ![페이로드를 보여주는 Heroes 앱 및 네트워킹 탭](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    UI에서 Heroes 중 하나를 삭제하고 삭제 작업을 완료하는 데 걸리는 시간을 확인할 수도 있습니다. "Fred"라는 Hero에서 "삭제" 단추를 클릭해보세요.

    ![함수를 완료하는 시간을 보여주는 Heroes 앱 및 네트워킹 탭](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    페이지를 새로 고치면 네트워크 탭에서는 Heroes를 가져오는 데 걸리는 시간을 표시합니다. 이러한 시간이 빠른 경우, 전 세계에서 데이터의 위치 및 사용자에게 가까운 지역의 지리적 복제 기능에 따라 달라집니다. 다음에 곧 릴리스될 자습서에서 지리적 복제에 대한 자세한 내용을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 앱에 함수 게시, 배치 및 삭제 추가 

이 자습서 시리즈의 추가 비디오를 곧 확인합니다.

