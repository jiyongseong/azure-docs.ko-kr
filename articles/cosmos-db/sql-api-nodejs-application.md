---
title: Azure Cosmos DB용 Node.js 웹앱 빌드 | Microsoft Docs
description: 이 Node.js 자습서에서는 Microsoft Azure Cosmos DB를 사용하여 Azure Websites에 호스트된 Node.js Express 웹 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 설명합니다.
keywords: 응용 프로그램 개발, 데이터베이스 자습서, node.js 알아보기, node.js 자습서
services: cosmos-db
documentationcenter: nodejs
author: SnehaGunda
manager: kfile
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 6a7d1b961245a47015bdb96fd8653d04586238b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="_Toc395783175"></a>Azure Cosmos DB를 사용하여 Node.js 웹 응용 프로그램 빌드
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

이 Node.js 자습서에서는 Azure Cosmos DB 및 SQL API를 사용하여 Azure Websites에 호스팅된 Node.js Express 응용 프로그램의 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 작업을 만들고 검색하고 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램인 ToDo 응용 프로그램을 빌드합니다. 작업은 Azure Cosmos DB에 JSON 문서로 저장됩니다. 이 자습서는 앱을 만들고 배포하는 과정을 안내하고 각 코드 조각에서 발생하는 상황에 대해 설명합니다.

![이 Node.js 자습서에서 만든 My Todo List 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

자습서를 완료할 시간이 없고 전체 솔루션을 가져오려는 경우 [GitHub][GitHub]에서 전체 샘플 솔루션을 구할 수 있습니다. 앱을 실행하는 방법에 대한 지침은 [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) 파일을 참조하세요.

## <a name="_Toc395783176"></a>필수 조건
> [!TIP]
> 이 Node.js 자습서에서는 Node.js 및 Azure Websites를 이전에 사용해본 경험이 있다고 가정합니다.
> 
> 

이 문서의 지침을 따르기 전에 다음이 있는지 확인해야 합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 버전 v0.10.29 이상 Node.js 6.10 이상을 사용하는 것이 좋습니다.
* [Express 생성기](http://www.expressjs.com/starter/generator.html)(`npm install express-generator -g`를 통해 설치 가능)
* [Git][Git].

## <a name="_Toc395637761"></a>1단계: Azure Cosmos DB 데이터베이스 계정 만들기
Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. 계정이 있거나 이 자습서에 Azure Cosmos DB 에뮬레이터를 사용하고 있는 경우 [2단계: 새 Node.js 응용 프로그램 만들기](#_Toc395783178)로 건너뛸 수 있습니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>2단계: 새 Node.js 응용 프로그램 만들기
이제 [Express](http://expressjs.com/) 프레임워크를 사용해서 기본적인 Hello World Node.js 프로젝트를 만드는 방법을 알아보겠습니다.

1. Node.js 명령 프롬프트와 같이 줄겨찾는 터미널을 엽니다.
2. 새 응용 프로그램을 저장하려는 디렉터리로 이동합니다.
3. Express 생성기를 사용해서 **todo**라는 새로운 응용 프로그램을 생성합니다.
   
        express todo
4. 새 **todo** 디렉터리를 열고 종속성을 설치합니다.
   
        cd todo
        npm install
5. 새 응용 프로그램을 실행합니다.
   
        npm start
6. 브라우저에서 [http://localhost:3000](http://localhost:3000)으로 이동하여 새 응용 프로그램을 확인할 수 있습니다.
   
    ![Node.js 알아보기 - 브라우저 창에 표시된 Hello World 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    그런 다음 응용 프로그램을 중지하려면 터미널 창에서 Ctrl+C를 누른 다음 **y**를 클릭하여 배치 작업을 종료합니다(Windows 컴퓨터만 해당).

## <a name="_Toc395783179"></a>3단계: 추가 모듈 설치
**package.json** 파일은 프로젝트 루트에 생성되는 파일 중 하나입니다. 이 파일에는 Node.js 응용 프로그램에 필요한 추가 모듈의 목록이 들어 있습니다. 나중에 이 응용 프로그램을 Azure Websites에 배포할 때, 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정하는 데 이 파일을 사용합니다. 이 자습서를 위해 패키지 두 개를 더 설치해야 합니다.

1. 다시 터미널에서 npm을 통해 **async** 모듈을 설치합니다.
   
        npm install async --save
2. npm을 통해 **documentdb** 모듈을 설치합니다. 이 모듈에서 중요한 모든 Azure Cosmos DB 기능이 수행됩니다.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>4단계: 노드 응용 프로그램에서 Azure Cosmos DB 서비스 사용
초기 설정 및 구성이 모두 완료되었으므로 이제 Azure Cosmos DB를 사용하여 일부 코드를 작성하겠습니다.

### <a name="create-the-model"></a>모델 만들기
1. 프로젝트 디렉터리에서 package.json 파일과 동일한 디렉터리에 **models**라는 새 디렉터리를 만듭니다.
2. **models** 디렉터리에서 **task-model.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.
3. 동일한 **models** 디렉터리에서 **cosmosdb-manager.js**라는 다른 새 파일을 만듭니다. 이 파일에는 응용 프로그램 전체에서 사용할 몇 가지 유용하고, 다시 사용할 수 있는 코드가 포함됩니다. 
4. 다음 코드를 **cosmosdb-manager.js**에 복사합니다.
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. **cosmosdb-manager.js** 파일을 저장하고 닫습니다.
6. **task-model.js** 파일의 시작 부분에서 위에서 만든 **DocumentDBClient** 및 **cosmosdb-manager.js**를 참조하도록 다음 코드를 추가합니다. 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. 그런 다음, Task 개체를 정의하고 내보내는 코드를 추가합니다. 이 코드는 작업 개체를 초기화하고 사용할 데이터베이스 및 문서 컬렉션을 설정합니다.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. 이제 다음 코드를 추가하여 Task 개체에서 추가 메서드를 정의합니다. 이러한 메서드를 통해 Azure Cosmos DB에 저장된 데이터를 조작할 수 있습니다.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. **task-model.js** 파일을 저장하고 닫습니다. 

### <a name="create-the-controller"></a>컨트롤러 만들기
1. 프로젝트의 **routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만듭니다. 
2. 아래 코드를 **tasklist.js**에 추가합니다. 이 코드는 **tasklist.js**에서 사용되는 DocumentDBClient 및 async 모듈을 로드합니다. 또한 앞에서 정의한 **Task** 개체의 인스턴스가 전달되는 **TaskList** 함수도 정의되었습니다.
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. **showTasks, addTask** 및 **completeTasks**에 사용된 메서드를 추가하여 **tasklist.js** 파일에 계속 추가합니다.
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. **tasklist.js** 파일을 저장하고 닫습니다.

### <a name="add-configjs"></a>config.js 추가
1. 프로젝트 디렉터리에서 **config.js**라는 새 파일을 만듭니다.
2. 다음을 **config.js**에 추가합니다. 이 파일은 응용 프로그램에 필요한 구성 설정 및 값을 정의합니다.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. **config.js** 파일에서 [Microsoft Azure Portal](https://portal.azure.com)에 있는 Azure Cosmos DB 계정의 [키] 페이지에 있는 값을 사용하여 HOST 및 AUTH_KEY 값을 업데이트합니다.
4. **config.js** 파일을 저장하고 닫습니다.

### <a name="modify-appjs"></a>app.js 수정
1. 프로젝트 디렉터리에서 **app.js** 파일을 엽니다. 이 파일은 이전에 Express 웹 응용 프로그램을 만들 때 생성되었습니다.
2. 다음 코드를 **app.js**의 맨 위에 추가합니다.
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. 이 코드는 사용할 구성 파일을 정의하고, 이 파일의 값을 곧 사용할 몇 가지 변수로 읽어들입니다.
4. **app.js** 파일에서 다음 두 줄을
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    다음 코드 조각으로 바꿉니다.
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. 이러한 줄은 Azure Cosmos DB에 대한 새로운 연결을 사용해서 **TaskModel** 개체의 새 인스턴스를 정의하고(**config.js**에서 읽은 값 사용), 작업 개체를 초기화한 후 폼 작업을 **TaskList** 컨트롤러의 메서드에 바인딩합니다. 
6. 끝으로, **app.js** 파일을 저장하고 닫으면 작업이 거의 완료됩니다.

## <a name="_Toc395783181"></a>5단계: 사용자 인터페이스 작성
이제 사용자가 실제로 응용 프로그램을 조작할 수 있도록 사용자 인터페이스를 작성하겠습니다. 만든 Express 응용 프로그램은 **Jade** 를 뷰 엔진으로 사용합니다. Jade에 대한 자세한 내용은 [http://jade-lang.com/](http://jade-lang.com/)을 참조하세요.

1. **views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일에 대한 전역 템플릿으로 사용됩니다. 이 단계에서는 멋진 모습의 웹 사이트를 쉽게 디자인할 수 있게 해주는 도구 키트인 [Twitter Bootstrap](https://github.com/twbs/bootstrap)을 사용하도록 이 파일을 수정합니다. 
2. **views** 폴더에 있는 **layout.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    이렇게 하면 응용 프로그램에 대한 일부 HTML을 렌더링하고 콘텐츠 페이지의 레이아웃을 제공할 수 있는 **콘텐츠**라는 **블록**을 만들도록 **Jade** 엔진에 알립니다.

    이 **layout.jade** 파일을 저장하고 닫습니다.

3. 이제 응용 프로그램에 사용되는 뷰인 **index.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

이렇게 하면 레이아웃이 확장되고 앞에서 **layout.jade** 파일에 있던 **content** 자리 표시자의 콘텐츠가 제공됩니다.
   
이 레이아웃에서 HTML 폼 두 개를 만들었습니다.

첫 번째 폼에는 데이터에 대한 테이블 및 컨트롤러의 **/completetask** 메서드에 게시하여 항목을 업데이트할 수 있게 해주는 단추 및 데이터 테이블이 포함됩니다.
    
두 번째 폼에는 컨트롤러의 **/addtask** 메서드에 게시하여 새 항목을 만들 수 있게 해주는 단추와 2개의 입력 필드가 포함됩니다.

응용 프로그램이 작동하는 데 필요한 모든 작업이 완료되었습니다.

## <a name="_Toc395783181"></a>6단계: 로컬에서 응용 프로그램 실행
1. 로컬 컴퓨터에서 응용 프로그램을 테스트하려면 터미널에서 `npm start`를 실행하여 응용 프로그램을 시작한 다음, [http://localhost:3000](http://localhost:3000) 브라우저 페이지를 새로 고칩니다. 이제 페이지가 아래 이미지처럼 표시됩니다.
   
    ![브라우저 창에 표시된 MyTodo List 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > layout.jade 파일이나 index.jade 파일에서 들여쓰기에 대한 오류가 발생하면 두 파일의 처음 두 줄을 공백 없이 왼쪽에 맞춥니다. 처음 두 줄 앞에 공백이 있으면 제거하고 두 파일을 모두 저장한 다음 브라우저 창을 새로 고칩니다. 

2. [항목], [항목 이름] 및 [범주] 필드를 사용하여 새 작업을 입력한 다음 **항목 추가**를 클릭합니다. 그러면 이러한 속성을 포함한 문서를 Azure Cosmos DB에 만듭니다. 
3. 페이지가 업데이트되어 새로 만든 항목을 ToDo 목록에 표시합니다.
   
    ![할 일 모음에 새 항목이 포함된 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. 작업을 완료하려면 완료 열의 확인란을 선택한 후 **작업 업데이트**를 클릭하면 됩니다. 그러면 이미 만든 문서가 업데이트되고 보기에서 제거됩니다.

5. 응용 프로그램을 중지하려면 터미널 창에서 Ctrl+C를 누른 다음 **Y**를 클릭하여 배치 작업을 종료합니다.

## <a name="_Toc395783182"></a>7단계: Azure Websites에 응용 프로그램 개발 프로젝트 배포
1. 아직 Azure 웹 사이트에 대해 git 리포지토리를 사용하도록 설정하지 않은 경우 사용하도록 설정합니다. [Azure App Service에 로컬 Git 배포](../app-service/app-service-deploy-local-git.md) 항목에서 이 작업을 수행하는 방법에 대한 지침을 찾을 수 있습니다.
2. Azure 웹 사이트를 git 원격으로 추가합니다.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. 원격 푸시로 배포합니다.
   
        git push azure master
4. 몇 초 후에 git이 웹 응용 프로그램 게시를 완료하고 Azure에서 실행 중인 작업을 볼 수 있는 브라우저가 실행됩니다.

    축하합니다! 지금까지 Azure Cosmos DB를 사용하여 첫 Node.js Express 웹 응용 프로그램을 작성하고 Azure Websites에 게시했습니다.

    이 자습서의 참조 응용 프로그램 전체를 다운로드하거나 참조하려면 [GitHub][GitHub]에서 다운로드할 수 있습니다.

## <a name="_Toc395637775"></a>다음 단계

* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하고 싶으신가요? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [Azure Cosmos DB 계정 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [Azure Cosmos DB 설명서](https://docs.microsoft.com/azure/cosmos-db/)를 살펴봅니다.

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

