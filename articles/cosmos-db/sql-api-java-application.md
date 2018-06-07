---
title: Azure Cosmos DB를 사용한 Java 응용 프로그램 개발 자습서 | Microsoft Docs
description: 이 Java 웹 응용 프로그램 자습서에서는 Azure Cosmos DB 및 SQL API를 사용하여 Azure Websites에 호스트된 Java 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다.
keywords: 응용 프로그램 개발, 데이터베이스 자습서, java 응용 프로그램, java 웹 응용 프로그램 자습서, azure, Microsoft azure
services: cosmos-db
documentationcenter: java
author: dennyglee
manager: kfile
ms.assetid: 0867a4a2-4bf5-4898-a1f4-44e3868f8725
ms.service: cosmos-db
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/22/2017
ms.author: denlee
ms.openlocfilehash: 2124e22ca5ab47b5e1836384132014cc0b356ff1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Azure Cosmos DB 및 SQL API를 사용하여 Java 웹 응용 프로그램 빌드
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

이 Java 웹 응용 프로그램 자습서에서는 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스를 사용하여 Azure App Service Web Apps에 호스트된 Java 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 항목에서는 다음 내용을 배웁니다.

* Eclipse에서 기본 JSP(JavaServer Pages) 응용 프로그램을 빌드하는 방법.
* [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java)를 사용하여 Azure Cosmos DB 서비스로 작업하는 방법입니다.

이 Java 응용 프로그램 자습서에서는 다음 이미지에 표시된 것처럼 작업을 생성 및 검색하고 완료로 표시할 수 있게 해주는 웹 기반 작업 관리 응용 프로그램을 만드는 방법을 보여 줍니다. 할 일 목록에 있는 각 작업은 Azure Cosmos DB에서 JSON 문서로 저장됩니다.

![My ToDo List Java 응용 프로그램](./media/sql-api-java-application/image1.png)

> [!TIP]
> 이 응용 프로그램 개발 자습서에서는 이전에 Java를 사용한 경험이 있다고 가정합니다. Java 또는 [필수 구성 요소 도구](#Prerequisites)를 처음 사용하는 경우 GitHub에서 전체 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 프로젝트를 다운로드하고 [이 문서의 끝에 있는 지침](#GetProject)을 사용하여 이 프로젝트를 빌드하는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드를 이해할 수 있습니다.  
> 
> 

## <a id="Prerequisites"></a>이 Java 웹 응용 프로그램 자습서의 필수 구성 요소
이 응용 프로그램 개발 자습서를 시작하기 전에 다음이 있어야 합니다.

*  Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [JDK(Java Development Kit) 7 이상](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
* [Java 런타임 환경(예: Tomcat 또는 Jetty)을 사용하는 Azure 웹 사이트](../app-service/app-service-web-get-started-java.md)

이러한 도구를 처음 설치하는 경우, coreservlets.com에서 제공되는 단계별 설치 지침을 따르세요. 이 지침은 [자습서: TomCat7 설치 및 Eclipse에서 사용](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) 문서의 빠른 시작 섹션에서 확인할 수 있습니다.

## <a id="CreateDB"></a>1단계: Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. 계정이 있거나 이 자습서에 Azure Cosmos DB 에뮬레이터를 사용하고 있는 경우 [2단계: Java JSP 응용 프로그램 만들기](#CreateJSP)로 건너뛸 수 있습니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>2단계: Java JSP 응용 프로그램 만들기
JSP 응용 프로그램을 만들려면 다음을 수행합니다.

1. 먼저, Java 프로젝트를 만듭니다. Eclipse를 시작한 후 **파일**, **새로 만들기**, **동적 웹 프로젝트**를 차례로 클릭합니다. **동적 웹 프로젝트**가 사용 가능한 프로젝트로 나열되지 않았으면 다음을 수행합니다. **파일**, **새로 만들기**, **프로젝트**…를 차례로 클릭하고 **웹**을 확장한 후 **동적 웹 프로젝트**를 클릭하고 **다음**을 클릭합니다.
   
    ![JSP Java 응용 프로그램 개발](./media/sql-api-java-application/image10.png)
2. **프로젝트 이름** 상자에 프로젝트 이름을 입력하고 **대상 런타임** 드롭다운 메뉴에서 선택적으로 값(예: Apache Tomcat v7.0)을 선택한 후 **마침**을 클릭합니다. 대상 런타임을 선택하면 Eclipse를 통해 프로젝트를 로컬로 실행할 수 있습니다.
3. Eclipse의 Project Explorer 보기에서 프로젝트를 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.
4. **새 JSP 파일** 대화 상자에서 파일 이름을 **index.jsp**로 지정합니다. 다음 그림에 표시된 것처럼 상위 폴더를 **WebContent**로 유지하고 **다음**을 클릭합니다.
   
    ![새 JSP 파일 만들기 - Java 웹 응용 프로그램 자습서](./media/sql-api-java-application/image11.png)
5. **JSP 템플릿 선택** 대화 상자에서 이 자습서의 목적에 따라, **새 JSP 파일(html)**을 선택한 후 **마침**을 클릭합니다.
6. index.jsp 파일이 Eclipse에서 열리면 **Hello World!**를 표시하도록 텍스트를 추가합니다. 기존 <body> 요소 내. 업데이트된 <body> 콘텐츠는 다음 코드와 같이 나타납니다.
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. index.jsp 파일을 저장합니다.
8. 2단계에서 대상 런타임을 설정했으면 **프로젝트**, **실행**을 차례로 클릭하여 JSP 응용 프로그램을 로컬로 실행할 수 있습니다.
   
    ![Hello World - Java 응용 프로그램 자습서](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>3단계: SQL Java SDK 설치
SQL Java SDK 및 해당 종속성을 가져오는 가장 쉬운 방법은 [Apache Maven](http://maven.apache.org/)을 사용하는 것입니다.

이렇게 하려면 다음 단계를 수행해서 프로젝트를 maven 프로젝트로 변환해야 합니다.

1. 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **구성**을 클릭한 후 **Maven 프로젝트로 변환**을 클릭합니다.
2. **새 POM 만들기** 창에서 기본값을 수락하고 **마침**을 클릭합니다.
3. **프로젝트 탐색기**에서 pom.xml 파일을 엽니다.
4. **종속성** 탭의 **종속성** 창에서 **추가**를 클릭합니다.
5. **종속성 선택** 창에서 다음을 수행합니다.
   
   * **그룹 ID** 상자에 com.microsoft.azure를 입력합니다.
   * **아티팩트 ID** 상자에 azure-documentdb를 입력합니다.
   * **버전** 상자에 1.5.1을 입력합니다.
     
   ![SQL Java 응용 프로그램 SDK 설치](./media/sql-api-java-application/image13.png)
     
   * 또는 텍스트 편집기를 통해 Group ID 및 Artifact ID에 대한 종속성 XML을 pom.xml에 직접 추가합니다.
     
        <dependency> <groupId>com.microsoft.azure</groupId> <artifactId>azure-documentdb</artifactId> <version>1.9.1</version> </dependency>
6. **확인**을 클릭하면 Maven이 SQL Java SDK를 설치합니다.
7. pom.xml 파일을 저장합니다.

## <a id="UseService"></a>4단계: Java 응용 프로그램에서 Azure Cosmos DB 서비스 사용
1. 먼저 TodoItem.java에서 TodoItem 개체를 정의하겠습니다.
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    이 프로젝트에서는 [Project Lombok](http://projectlombok.org/) 을 사용해서 생성자, getter, setter 및 작성기를 생성합니다. 또는 이 코드를 수동으로 작성하거나 IDE에서 생성하도록 할 수도 있습니다.
2. Azure Cosmos DB 서비스를 호출하려면 새 **DocumentClient**를 인스턴스화해야 합니다. 일반적으로 각 후속 요청에 대해 새 클라이언트를 생성하는 것보다는 **DocumentClient** 를 다시 사용하는 것이 가장 좋습니다. **DocumentClientFactory**에 클라이언트를 래핑하면 클라이언트를 다시 사용할 수 있습니다. DocumentClientFactory.java에서 [1단계](#CreateDB)에서 클립보드에 저장한 URI 및 기본 키 값을 붙여 넣어야 합니다. [YOUR\_ENDPOINT\_HERE]를 해당 URI로 바꾸고 [YOUR\_KEY\_HERE]를 해당 기본 키로 바꿉니다.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. 이제 DAO(Data Access Object)를 만들어서 영구적인 ToDo 항목을 Azure Cosmos DB로 추상화합니다.
   
    ToDo 항목을 컬렉션에 저장하려면 클라이언트는 유지할 데이터베이스 및 컬렉션(셀프 link로 참조)을 알고 있어야 합니다. 일반적으로 데이터베이스로의 추가 왕복을 방지할 수 있을 때 데이터베이스 및 컬렉션을 캐시하는 것이 좋습니다.
   
    다음 코드에서는 데이터베이스 및 컬렉션이 있는 경우 검색하고 없는 경우 새로 만드는 방법을 보여 줍니다.
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. 다음 단계에서는 TodoItems를 컬렉션에 저장하는 코드를 작성합니다. 이 예제에서는 [Gson](https://code.google.com/p/google-gson/) 을 사용하여 TodoItem POJO(Plain Old Java Object)를 JSON 문서에 직렬화하고 역직렬화합니다.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Azure Cosmos DB 데이터베이스 및 컬렉션과 마찬가지로 문서도 셀프 link로 참조됩니다. 다음 도우미 함수에서는 셀프 link가 아닌 다른 특성(예: "id")으로 문서를 검색해 보겠습니다.
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. 5단계의 도우미 메서드를 사용하여 ID로 TodoItem JSON 문서를 검색한 다음 POJO로 역직렬화할 수 있습니다.
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. 또한 DocumentClient를 사용하여 SQL을 사용하는 TodoItem의 컬렉션 또는 목록을 가져올 수 있습니다.
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. 여러 가지 방법으로 DocumentClient가 포함된 문서를 업데이트할 수 있습니다. 여기의 Todo 목록 응용 프로그램에서는 TodoItem이 완료되었는지 여부를 전환할 수 있게 하려고 합니다. 이를 위해서는 문서 내에서 "complete" 특성을 업데이트해야 합니다.
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. 끝으로, 목록에서 TodoItem을 삭제할 수 있는 기능이 필요합니다. 이를 위해 앞서 작성한 도우미 메서드를 사용하여 셀프 link를 검색한 다음 클라이언트에 삭제하도록 지시할 수 있습니다.
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>5단계: Java 응용 프로그램 개발 프로젝트의 나머지 부분 연결
이제 재미있는 부분을 마쳤으므로 빠른 사용자 인터페이스를 빌드하고 DAO에 연결하기만 하면 됩니다.

1. 먼저 DAO를 호출하는 컨트롤러를 빌드하겠습니다.
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    보다 복잡한 응용 프로그램에서는 컨트롤러에서 DAO 위에 복잡한 비즈니스 논리를 수용할 수 있습니다.
2. 다음으로 컨트롤러에 HTTP 요청을 서블릿을 만듭니다.
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. 사용자에게 표시할 웹 사용자 인터페이스가 필요합니다. 앞에서 만든 index.jsp를 다시 작성하겠습니다.
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. 끝으로, 웹 사용자 인터페이스와 서블릿을 연결하는 클라이언트 쪽 JavaScript를 작성합니다.
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. 멋집니다! 이제 응용 프로그램을 테스트하는 일만 남았습니다. 응용 프로그램을 로컬로 실행하고 항목 이름과 범주를 입력하고 **작업 추가**를 클릭하여 Todo 항목을 추가합니다.
6. 항목이 표시되면 확인란을 설정/해제하고 **작업 업데이트**를 클릭하여 항목의 완료 여부를 업데이트할 수 있습니다.

## <a id="Deploy"></a>6단계: Azure 웹 사이트에 Java 응용 프로그램 배포
Azure 웹 사이트에서는 Java 응용 프로그램을 간단히 배포할 수 있습니다. 즉, 응용 프로그램을 WAR 파일로 내보내고 소스 제어(예: Git) 또는 FTP를 통해 업로드하면 됩니다.

1. 응용 프로그램을 WAR 파일로 내보내려면 **프로젝트 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **내보내기**를 클릭한 후 **WAR 파일**을 클릭합니다.
2. **WAR 내보내기** 창에서 다음을 수행합니다.
   
   * 웹 프로젝트 상자에 azure-documentdb-java-sample을 입력합니다.
   * 대상 상자에서 WAR 파일을 저장할 대상을 선택합니다.
   * **Finish**를 클릭합니다.
3. 이제 WAR 파일이 준비되었으므로 간단히 Azure 웹 사이트의 **webapps** 디렉터리로 업로드하면 됩니다. 파일 업로드에 대한 자세한 내용은 [Azure App Service Web Apps에 Java 응용 프로그램 추가](../app-service/web-sites-java-add-app.md)를 참조하세요.
   
    WAR 파일이 webapps 디렉터리에 업로드되면 런타임 환경에서 이 파일이 추가되었음을 감지하고 자동으로 로드합니다.
4. 완료된 제품을 보려면 http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/로 이동하고 작업 추가를 시작합니다.

## <a id="GetProject"></a>GitHub에서 프로젝트 가져오기
이 자습서의 모든 샘플은 GitHub의 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 프로젝트에 포함되어 있습니다. Todo 프로젝트를 Eclipse로 가져오려면 [필수 조건](#Prerequisites) 섹션에 나열된 소프트웨어 및 리소스가 있는지 확인한 후 다음을 수행합니다.

1. [Project Lombok](http://projectlombok.org/)을 설치합니다. Lombok은 프로젝트에서 생성자, getter, setter를 생성하는 데 사용됩니다. lombok.jar 파일을 다운로드한 다음에는 두 번 클릭하여 설치하거나 명령줄을 사용해서 설치합니다.
2. Eclipse가 열려 있으면 닫고 다시 시작해서 Lombok을 로드합니다.
3. Eclipse의 **파일** 메뉴에서 **가져오기**를 클릭합니다.
4. **가져오기** 창에서 **Git**, **Git의 프로젝트**, **다음**을 차례로 클릭합니다.
5. **리포지토리 원본 선택** 화면에서 **URI 복제**를 클릭합니다.
6. **원본 Git 리포지토리** 화면의 **URI** 상자에 https://github.com/Azure-Samples/java-todo-app.git를 입력한 다음, **다음**을 클릭합니다.
7. **분기 선택** 화면에서 **마스터**가 선택되었는지 확인하고 **다음**을 클릭합니다.
8. **로컬 대상** 화면에서 **찾아보기**를 클릭하여 리포지토리를 복사할 수 있는 폴더를 선택한 후 **다음**을 클릭합니다.
9. **프로젝트 가져오기에 사용할 마법사 선택** 화면에서 **기존 프로젝트 가져오기**가 선택되었는지 확인하고 **다음**을 클릭합니다.
10. **프로젝트 가져오기** 화면에서 **DocumentDB** 프로젝트를 선택 취소한 후 **마침**을 클릭합니다. DocumentDB 프로젝트에는 종속성으로 추가할 Azure Cosmos DB Java SDK가 포함됩니다.
11. **프로젝트 탐색기**에서 azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java로 이동하고 HOST 및 MASTER_KEY 값을 각각 해당 Azure Cosmos DB 계정의 URI 및 기본 키로 바꾸고 파일을 저장합니다. 자세한 내용은 [1단계를 참조하세요. Azure Cosmos DB 데이터베이스 계정 만들기](#CreateDB)를 참조하세요.
12. **프로젝트 탐색기**에서 **azure-documentdb-java-sample**을 마우스 오른쪽 단추로 클릭하고 **빌드 경로**를 클릭한 후 **빌드 경로 구성**을 클릭합니다.
13. **Java 빌드 경로** 화면의 오른쪽 창에서 **라이브러리** 탭을 선택한 후 **외부 JAR 추가**를 클릭합니다. lombok.jar 파일의 위치로 이동하고 **열기**를 클릭한 후 **확인**을 클릭합니다.
14. 12단계를 수행해서 **속성** 창을 다시 열고 왼쪽 창에서 **대상 런타임**을 클릭합니다.
15. **대상 런타임** 화면에서 **새로 만들기**를 클릭하고 **Apache Tomcat v7.0**을 선택한 후 **확인**을 클릭합니다.
16. 12단계를 수행해서 **속성** 창을 다시 열고 왼쪽 창에서 **프로젝트 패싯**을 클릭합니다.
17. **프로젝트 패싯** 화면에서 **동적 웹 모듈** 및 **Java**를 선택한 후 **확인**을 클릭합니다.
18. 화면 하단에 있는 **서버** 탭에서 **로컬 호스트의 Tomcat v7.0 서버**를 마우스 오른쪽 단추로 클릭하고 **추가 및 제거**를 클릭합니다.
19. **추가 및 제거** 창에서 **azure-documentdb-java-sample**을 **구성됨** 상자로 이동하고 **마침**을 클릭합니다.
20. **서버** 탭에서 **로컬 호스트의 Tomcat v7.0 서버**를 마우스 오른쪽 단추로 클릭하고 **다시 시작**을 클릭합니다.
21. 브라우저에서 http://localhost:8080/azure-documentdb-java-sample/로 이동하고 작업 목록에 추가하기 시작합니다. 기본 포트 값을 변경한 경우 8080을 선택한 값으로 변경합니다.
22. 프로젝트를 Azure 웹 사이트에 배포하려면 [6단계. Azure 웹 사이트에 응용 프로그램 배포](#Deploy)를 참조하세요.

