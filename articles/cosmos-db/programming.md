---
title: Azure Cosmos DB에 대한 서버 쪽 JavaScript 프로그래밍 | Microsoft Docs
description: Azure Cosmos DB를 사용하여 JavaScript에서 저장 프로시저, 데이터베이스 트리거 및 UDF(사용자 정의 함수)를 작성하는 방법을 알아봅니다. 데이터베이스 프로그래밍 팁 등을 가져옵니다.
keywords: 데이터베이스 트리거, 저장 프로시저, 저장 프로시저, 데이터베이스 프로그램, sproc, azure, Microsoft azure
services: cosmos-db
documentationcenter: ''
author: aliuy
manager: kfile
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: b3d7c94e8b1415a24427e1f90f5613d8c181608a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197996"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Azure Cosmos DB 서버 쪽 프로그래밍: 저장 프로시저, 데이터베이스 트리거 및 UDF

Azure Cosmos DB가 언어 통합 트랜잭션 방식으로 JavaScript를 실행하므로 개발자가 기본적으로 [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript로 **저장 프로시저**, **트리거** 및 **UDF(사용자 정의 함수)** 를 작성할 수 있는 방법을 알아봅니다. Javascript 통합을 사용하여 데이터베이스 저장소 파티션 내에서 직접 전달되고 실행될 수 있는 프로그램 논리를 작성할 수 있습니다. 

먼저 Andrew Liu가 Azure Cosmos DB의 서버 쪽 데이터베이스 프로그래밍 모델을 소개하는 다음 비디오를 시청하시기 바랍니다. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

그런 다음 이 문서로 돌아와서 다음 내용을 살펴보세요.  

* JavaScript를 사용해서 저장 프로시저, 트리거 또는 UDF를 작성하려면 어떻게 하나요?
* Cosmos DB는 ACID를 어떻게 보장하나요?
* Cosmos DB에서 트랜잭션이 어떻게 작동하나요?
* 사전 트리거 및 사후 트리거는 무엇이고 어떻게 작성하는가?
* HTTP를 사용해서 RESTful 방식으로 저장 프로시저, 트리거 또는 UDF를 등록하고 실행하는 방법은 무엇인가?
* 저장 프로시저, 트리거 및 UDF를 생성 및 실행하기 위해 사용할 수 있는 Cosmos DB SDK는 무엇인가요?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>저장 프로시저 및 UDF 프로그래밍 소개
이 *"최신 T-SQL로서의 JavaScript"* 접근 방법을 통해 응용 프로그램 개발자는 형식 시스템 불일치 및 개체-관계형 매핑 기술의 복잡성을 벗어날 수 있습니다. 또한 풍부한 응용 프로그램을 작성하기 위해 활용할 수 있는 내재된 많은 장점이 있습니다.  

* **절차적 논리:** 고급 프로그래밍 언어인 JavaScript는 비즈니스 논리를 노출하는 풍부하고 익숙한 인터페이스를 제공합니다. 데이터에 더 가까운 복잡한 작업 시퀀스를 수행할 수 있습니다.
* **원자성 트랜잭션:** Cosmos DB는 단일 저장 프로시저 또는 트리거 내에서 수행되는 데이터베이스 작업의 원자성을 보장합니다. 이 원자성 기능을 사용하면 응용 프로그램이 관련 작업을 단일 배치로 결합하여 모두 성공하거나 모두 실패하도록 할 수 있습니다. 
* **성능:** JSON은 본질적으로 Javascript 언어 형식 시스템에 매핑되고 Cosmos DB의 기본 저장소 단위이기도 하므로 버퍼 풀에서 JSON 문서의 지연 구체화와 같은 많은 최적화를 수행할 수 있으며 요청 시 실행 코드에서 JSON 문서를 사용할 수 있습니다. 데이터베이스에 비즈니스 논리를 전달할 경우 다음과 같은 추가 성능 이점이 있습니다.
  
  * 일괄 처리 – 개발자가 삽입 등의 작업을 그룹화하여 대량 제출할 수 있습니다. 개별 트랜잭션을 만들기 위한 네트워크 트래픽 지연 비용 및 저장소 오버헤드가 크게 줄어듭니다. 
  * 사전 컴파일 – Cosmos DB는 각 호출의 JavaScript 컴파일 비용을 방지하기 위해 저장 프로시저, 트리거 및 UDF(사용자 정의 함수)를 사전 컴파일합니다. 절차적 논리의 바이트 코드 작성 오버헤드가 최소값으로 줄어듭니다.
  * 시퀀싱 – 보조 저장소 작업을 하나 또는 많이 수행하는 파생 작업("트리거")이 필요한 작업이 많습니다. 원자성뿐 아니라 이 특성도 서버로 이동할 경우 성능이 향상됩니다. 
* **캡슐화:** 저장 프로시저를 사용하여 비즈니스 논리를 단일 장소에 그룹화할 수 있으며 다음 두 가지 장점이 있습니다.
  * 원시 데이터 위에 추상 계층이 추가되므로 데이터 설계자가 데이터와 독립적으로 응용 프로그램을 개발할 수 있습니다. 데이터를 직접 처리해야 할 경우, 응용 프로그램에 포함되어야 할 수 있는 가정 때문에 이 추상화 레이어는 데이터에 스키마가 없을 때 유용합니다.  
  * 이 추상화는 스크립트에서의 액세스를 간소화하여 기업이 데이터 보안을 유지할 수 있게 합니다.  

데이터베이스 트리거, 저장 프로시저 및 사용자 지정 쿼리 연산자의 만들기 및 실행은 .NET, Node.js 및 JavaScript를 비롯한 많은 플랫폼의 [Azure Portal](https://portal.azure.com), [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases) 및 [클라이언트 SDK](sql-api-sdk-dotnet.md)를 통해 지원됩니다.

이 자습서에서는 [Q Promise와 함께 Node.js SDK](http://azure.github.io/azure-documentdb-node-q/)를 사용하여 저장 프로시저, 트리거 및 UDF의 구문 및 사용법을 설명합니다.   

## <a name="stored-procedures"></a>저장 프로시저
### <a name="example-write-a-stored-procedure"></a>예: 저장 프로시저 작성
먼저 "Hello World" 응답을 반환하는 단순한 저장 프로시저로 시작하겠습니다.

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


저장 프로시저는 컬렉션당 등록되며 해당 컬렉션에 있는 모든 문서 및 첨부 파일에 대해 작동할 수 있습니다. 다음 조각은 helloWorld 저장 프로시저를 컬렉션에 등록하는 방법을 보여 줍니다. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


저장 프로시저가 등록되면 컬렉션에 대해 실행하고 클라이언트에서 결과를 읽을 수 있습니다. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


컨텍스트 개체는 Cosmos DB 저장소에서 수행할 수 있는 모든 작업에 대한 액세스와 요청 및 응답 개체에 대한 액세스를 제공합니다. 여기서는 응답 개체를 사용하여 클라이언트로 전송되는 응답의 본문을 설정합니다. 자세한 내용은 [Azure Cosmos DB JavaScript 서버 SDK 설명서](http://azure.github.io/azure-documentdb-js-server/)를 참조하세요.  

이 예제를 확장하여 저장 프로시저에 데이터베이스 관련 기능을 더 추가하겠습니다. 저장 프로시저는 컬렉션 내의 문서와 첨부 파일을 만들고 업데이트하고 읽고 쿼리 및 삭제할 수 있습니다.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>예: 문서 작성을 위한 저장 프로시저 작성
다음 코드 조각에서는 컨텍스트 개체를 사용하여 Cosmos DB 리소스를 조작하는 방법을 보여 줍니다.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


이 저장 프로시저는 현재 컬렉션에 만들 문서의 본문을 입력 documentToCreate로 사용합니다. 이러한 모든 작업은 비동기이며 JavaScript 함수 콜백에 따라 달라집니다. 콜백 함수에는 작업이 실패할 경우의 오류 개체 및 만들어진 개체에 각각 사용되는 두 개의 매개 변수가 있습니다. 콜백 내에서 사용자는 예외를 처리하거나 오류를 throw할 수 있습니다. 콜백이 제공되지 않았고 오류가 있는 경우, Azure Cosmos DB 런타임에서 오류를 throw합니다.   

위 예제에서 콜백은 작업이 실패한 경우에 오류를 throw합니다. 그렇지 않으면 만들어진 문서의 ID를 클라이언트에 반환되는 응답의 본문으로 설정합니다. 다음은 이 저장 프로시저가 입력 매개 변수를 사용하여 실행되는 방법을 보여 줍니다.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


문서 본문 배열을 입력으로 사용하고 여러 요청을 통해 각각 개별적으로 만드는 대신 동일한 저장 프로시저 실행에서 모두 만들도록 이 저장 프로시저를 수정할 수 있습니다. 이 저장 프로시저를 사용하여 Cosmos DB에 대한 효율적인 대량 가져오기를 구현할 수 있습니다(이 자습서의 뒷부분에서 설명).   

설명한 예제에서는 저장 프로시저를 사용하는 방법을 보여 주었습니다. 다음으로 자습서의 뒷부분에서 트리거와 UDF(사용자 정의 함수)에 대해 알아봅니다.

### <a name="known-issues"></a>알려진 문제

Azure Portal을 사용하여 저장 프로시저를 정의할 때 입력 매개 변수는 항상 문자열로 저장 프로시저에 전송됩니다. 입력으로 문자열의 배열을 전달하는 경우에도 배열은 문자열로 변환되고 저장 프로시저로 전송됩니다. 이 문제를 해결하기 위해 저장 프로시저 내에서 함수를 정의하여 배열로 문자열을 구문 분석할 수 있습니다. 다음 코드는 배열로 문자열을 구문 분석하는 예입니다. 

``` 
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>데이터베이스 프로그램 트랜잭션
일반적인 데이터베이스의 트랜잭션은 하나의 논리적 작업 단위로 수행되는 작업 시퀀스로 정의할 수 있습니다. 각 트랜잭션에서 **ACID 보장**을 제공합니다. ACID는 원자성, 일관성, 격리 및 내구성의 네 가지 속성을 나타내는 잘 알려진 머리글자어입니다.  

간단히 설명하면, 원자성은 트랜잭션 내부에서 수행된 모든 작업이 하나의 단위로 처리되어 모두 커밋되거나 커밋되지 않도록 합니다. 일관성은 데이터가 트랜잭션 간에 항상 양호한 내부 상태로 유지되도록 합니다. 격리는 두 트랜잭션이 서로를 방해하지 않도록 합니다. 일반적으로 대부분의 상용 시스템은 응용 프로그램 요구에 따라 사용할 수 있는 여러 격리 수준을 제공합니다. 내구성은 데이터베이스에서 커밋된 변경 내용이 항상 유지되도록 합니다.   

Cosmos DB에서 JavaScript는 데이터베이스와 동일한 메모리 공간에 호스트됩니다. 따라서 저장 프로시저 및 트리거 내에서 수행된 요청이 동일한 데이터베이스 세션 범위에서 실행됩니다. 이 기능을 통해 Cosmos DB에서 단일 저장 프로시저/트리거에 속하는 모든 작업에 대해 ACID를 보장할 수 있습니다. 다음 저장 프로시저 정의를 고려해 보세요.

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

이 저장 프로시저는 게임 앱 내의 트랜잭션을 사용하여 단일 작업으로 두 플레이어 간에 항목을 교환합니다. 저장 프로시저는 각각 인수로 전달된 플레이어 ID에 해당하는 두 개의 문서를 읽으려고 합니다. 두 플레이어 문서가 모두 있으면 저장 프로시저가 항목을 교환하여 문서를 업데이트합니다. 이 과정에서 오류가 발생할 경우 JavaScript 예외가 발생하여 암시적으로 트랜잭션이 중단됩니다.

저장 프로시저가 등록된 컬렉션이 단일 파티션 컬렉션인 경우 트랜잭션은 해당 컬렉션 내의 모든 문서로 범위가 지정됩니다. 컬렉션이 분할된 경우 저장 프로시저는 단일 파티션 키의 트랜잭션 범위에서 실행됩니다. 이 경우 각 저장 프로시저 실행에는 트랜잭션이 실행되는 범위에 해당하는 파티션 키 값이 포함되어야 합니다. 자세한 내용은 [Azure Cosmos DB 분할](partition-data.md)을 참조하세요.

### <a name="commit-and-rollback"></a>커밋 및 롤백
트랜잭션은 기본적으로 Cosmos DB의 JavaScript 프로그래밍 모델에 전체 통합됩니다. JavaScript 함수 내부에서 모든 작업은 자동으로 단일 트랜잭션 아래에 래핑됩니다. JavaScript가 예외 없이 완료되면 데이터베이스에 대한 작업이 커밋됩니다. 실제로 관계형 데이터베이스의 "BEGIN TRANSACTION" 및 "COMMIT TRANSACTION" 문은 Cosmos DB에서 암시적입니다.  

스크립트에서 전파된 예외가 있을 경우 Cosmos DB의 JavaScript 런타임이 전체 트랜잭션을 롤백합니다. 앞의 예제에 표시된 대로, 예외 throw는 Cosmos DB의 "ROLLBACK TRANSACTION"과 동등합니다.

### <a name="data-consistency"></a>데이터 일관성
저장 프로시저와 트리거는 항상 Azure Cosmos DB 컨테이너의 주 복제본에서 실행됩니다. 이렇게 하면 저장 프로시저 내부의 읽기에서 강력한 일관성을 제공합니다. 사용자 정의 함수를 사용한 쿼리는 주 복제본이나 모든 보조 복제본에서 실행할 수 있지만 적절한 복제본을 선택하여 요청된 일관성 수준을 충족해야 합니다.

## <a name="bounded-execution"></a>제한된 예외
모든 Cosmos DB 작업은 서버에서 지정된 요청 시간 제한 기간 내에 완료되어야 합니다. 이 제약 조건은 JavaScript 함수(저장 프로시저, 트리거 및 사용자 정의 함수)에도 적용됩니다. 작업이 시간 제한 내에 완료되지 않으면 트랜잭션이 롤백됩니다. JavaScript 함수는 시간 제한 내에 완료되거나 실행을 일괄 처리/다시 시작하는 연속 기반 모델을 구현해야 합니다.  

시간 제한을 처리하는 저장 프로시저 및 트리거 개발을 간소화하기 위해 컬렉션 개체 아래의 모든 함수(문서와 첨부 파일 만들기, 읽기, 바꾸기 및 삭제)는 해당 작업이 완료되는지 여부를 나타내는 부울 값을 반환합니다. 이 값이 false이면 시간 제한이 만료되며 프로시저 실행이 종료되어야 함을 나타냅니다.  수락되지 않은 첫 번째 저장소 작업 전에 대기된 작업은 저장 프로시저가 제시간에 완료되고 더 이상 요청을 대기열에 추가하지 않을 경우 완료됩니다.  

JavaScript 함수는 리소스 사용에 의해서도 제한됩니다. Cosmos DB는 컬렉션별 또는 컨테이너의 집합에 대한 처리량을 예약합니다. 처리량은 요청 단위 또는 RU라고 하는 정규화된 CPU, 메모리 및 IO 사용 단위로 표현됩니다. JavaScript 함수는 짧은 시간 내에 다수의 RU를 사용할 수 있으며, 컬렉션 한도에 도달할 경우 비율이 제한될 수 있습니다. 기본 데이터베이스 작업의 가용성을 위해 리소스를 많이 사용하는 저장 프로시저가 보장될 수도 있습니다.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>예: 데이터베이스 프로그램으로 데이터 대량 가져오기
다음은 문서를 컬렉션으로 대량 가져오기 위해 작성된 저장 프로시저의 예입니다. 저장 프로시저가 createDocument의 부울 반환 값을 검사하여 제한된 실행을 처리한 다음 각 저장 프로시저 호출에 삽입된 문서 수를 사용하여 일괄 처리의 진행 상황을 추적하고 다시 시작하는 방법을 확인합니다.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a> 데이터베이스 트리거
### <a name="database-pre-triggers"></a>데이터베이스 사전 트리거
Cosmos DB는 문서 작업에 의해 실행되거나 트리거되는 트리거를 제공합니다. 예를 들어 문서를 만들 때 사전 트리거를 지정할 수 있습니다. 이 사전 트리거는 문서를 만들기 전에 실행됩니다. 다음 예제에서는 사전 트리거를 사용하여 만드는 문서의 속성 유효성을 검사할 수 있는 방법을 보여 줍니다.

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


트리거에 해당하는 Node.js 클라이언트 쪽 등록 코드는 다음과 같습니다.

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


사전 트리거는 입력 매개 변수를 사용할 수 없습니다. 요청 개체를 사용하여 작업과 연결된 요청 메시지를 조작할 수 있습니다. 여기서는 문서를 만들어 사전 트리거를 실행하며 요청 메시지 본문에 JSON 형식으로 만들 문서가 포함됩니다.   

사용자는 트리거를 등록할 때 트리거 실행에 사용되는 작업을 지정할 수 있습니다. 이 트리거는 TriggerOperation.Create를 사용하여 만들어졌습니다. 즉, 다음 코드에 나와 있는 것처럼 바꾸기 작업에 트리거를 사용하는 것은 허용되지 않습니다.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>데이터베이스 사후 트리거
사전 트리거와 마찬가지로 사후 트리거는 문서 작업과 연결되며 입력 매개 변수를 사용하지 않습니다. 작업이 완료된 **후에** 실행되며 클라이언트에 전송된 응답 메시지에 액세스할 수 있습니다.   

다음 예제는 사후 트리거 작동을 보여 줍니다.

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


다음 샘플에 표시된 대로 트리거를 등록할 수 있습니다.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


이 트리거는 메타데이터 문서를 쿼리하고 새로 만든 문서에 대한 세부 정보로 업데이트합니다.  

한 가지 중요한 사항은 Cosmos DB에서 트리거의 **트랜잭션** 실행입니다. 이 사후 트리거는 원본 문서 만들기와 동일한 트랜잭션의 일부로 실행됩니다. 따라서 사후 트리거에서 예외가 발생할 경우(가령 메타데이터 문서를 업데이트할 수 없는 경우) 전체 트랜잭션이 실패하고 롤백됩니다. 문서가 만들어지지 않고 예외가 반환됩니다.  

## <a id="udf"></a>사용자 정의 함수
UDF(사용자 정의 함수)는 Azure Cosmos DB SQL 쿼리 언어 문법을 확장하고 사용자 지정 비즈니스 논리를 구현하는 데 사용됩니다. UDF는 쿼리 내부에서만 호출할 수 있습니다. 컨텍스트 개체에 액세스할 수 없으며 계산 전용 JavaScript로 사용되어야 합니다. 따라서 UDF는 Cosmos DB 서비스의 보조 복제본에서 실행할 수 있습니다.  

다음 샘플에서는 다양한 수입 브래킷에 대한 비율에 따라 소득세를 계산하는 UDF를 만든 다음 쿼리 내부에서 사용하여 납부한 세금이 $20,000를 초과하는 모든 사람을 찾습니다.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


이후에 다음 샘플과 같이 쿼리에 UDF를 사용할 수 있습니다.

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>JavaScript 언어 통합 쿼리 API
Azure Cosmos DB의 SQL 문법을 사용하여 쿼리를 발급하는 것 외에도 서버 쪽 SDK를 사용하면 SQL의 지식 없이도 흐름 JavaScript 인터페이스를 사용하여 최적화된 쿼리를 수행할 수 있습니다. JavaScript 쿼리 API를 사용하면 조건자 함수를 ECMAScript5의 배열 기본 제공 항목과 익숙한 구문 및 Lodash와 같은 인기 있는 JavaScript 라이브러리가 포함된 연결 가능한 함수 호출에 전달하여 쿼리를 프로그래밍 방식으로 작성할 수 있습니다. 쿼리는 Azure Cosmos DB의 인덱스를 사용하여 효율적으로 실행되도록 JavaScript 런타임으로 구문 분석됩니다.

> [!NOTE]
> `__`(이중 밑줄)은 `getContext().getCollection()`에 대한 별칭입니다.
> <br/>
> 즉, `__` 또는 `getContext().getCollection()`을 사용하여 JavaScript 쿼리 API에 액세스할 수 있습니다.
> 
> 

지원되는 함수는 다음과 같습니다.

<ul>
<li>
<b>chain() ... .value([callback] [, options])</b>
<ul>
<li>
value()로 종료되어야 하는 연결된 호출을 시작합니다.
</li>
</ul>
</li>
<li>
<b>filter(predicateFunction [, options] [, callback])</b>
<ul>
<li>
출력 문서를 결과 집합으로 필터링하기 위해 true/false를 반환하는 조건자 함수를 사용하여 입력을 필터링합니다. 이 기능은 SQL의 WHERE 절과 유사하게 작동합니다.
</li>
</ul>
</li>
<li>
<b>map(transformationFunction [, options] [, callback])</b>
<ul>
<li>
각 입력 항목을 JavaScript 개체 또는 값에 매핑하는 변환 함수에 대해 프로젝션을 적용합니다. 이 기능은 SQL의 SELECT 절과 유사하게 작동합니다.
</li>
</ul>
</li>
<li>
<b>pluck([propertyName] [, options] [, callback])</b>
<ul>
<li>
이 기능은 각 입력 항목에서 단일 속성의 값을 추출하는 맵에 대한 바로 가기입니다.
</li>
</ul>
</li>
<li>
<b>flatten([isShallow] [, options] [, callback])</b>
<ul>
<li>
각 입력 항목의 배열을 단일 배열로 결합하고 평면화합니다. 이 기능은 LINQ의 SelectMany와 유사하게 작동합니다.
</li>
</ul>
</li>
<li>
<b>sortBy([predicate] [, options] [, callback])</b>
<ul>
<li>
주어진 조건자를 사용하여 입력 문서 스트림의 문서를 오름차순으로 정렬하여 새 문서 집합을 생성합니다. 이 기능은 SQL의 ORDER BY 절과 유사하게 작동합니다.
</li>
</ul>
</li>
<li>
<b>sortByDescending([predicate] [, options] [, callback])</b>
<ul>
<li>
주어진 조건자를 사용하여 입력 문서 스트림의 문서를 내림차순으로 정렬하여 새 문서 집합을 생성합니다. 이 기능은 SQL의 ORDER BY x DESC 절과 유사하게 작동합니다.
</li>
</ul>
</li>
</ul>


조건자 및/또는 선택기 함수 안에 포함된 경우 다음과 같은 JavaScript 구문이 Azure Cosmos DB 인덱스에서 직접 실행하도록 자동으로 최적화됩니다.

* 간단한 연산자: = = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* 개체 리터럴을 포함하는 리터럴: {}
* var, 반환

다음 JavaScript 구문은 Azure Cosmos DB 인덱스에 대해 최적화되지 않습니다.

* 제어 흐름(예: if, for, while)
* 함수 호출

자세한 내용은 [서버 쪽 JSDocs](http://azure.github.io/azure-documentdb-js-server/)를 참조하세요.

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>예: JavaScript 쿼리 API를 사용하여 저장된 프로시저 작성
다음 코드 샘플은 저장된 프로시저의 컨텍스트에서 JavaScript 쿼리 API를 사용할 수 있는 방법의 예입니다. 저장된 프로시저는 입력된 매개 변수로 지정된 문서를 삽입하고 입력된 문서의 크기 속성을 기반으로 하는 minSize, maxSize 및 totalSize가 있는 `__.filter()` 메서드를 사용하여 메타데이터 문서를 업데이트합니다.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL-Javascript 치트 시트
다음 표에서 다양한 SQL 쿼리 및 해당 JavaScript 쿼리를 표시합니다.

SQL 쿼리와 마찬가지로, 문서 속성 키(예: `doc.id`)는 대/소문자를 구분합니다.

|SQL| JavaScript Query API|아래 설명|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|1|
|SELECT docs.id, docs.message AS msg, docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|SELECT *<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|SELECT *<br>FROM docs<br>WHERE ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4|
|SELECT docs.id, docs.message AS msg<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

다음 설명에서는 위의 테이블에 있는 각 쿼리를 설명합니다.
1. 모든 문서(연속 토큰과 함께 페이지가 매겨진)의 결과는 있는 그대로입니다.
2. 모든 문서에서 id, message(msg로 별칭이 지정됨) 및 action을 프로젝션합니다.
3. 조건자: id = "X998\_Y998"을 사용하여 문서를 쿼리합니다.
4. Tags 속성이 있는 문서를 쿼리합니다. Tags는 123 값을 포함하는 배열입니다.
5. 조건자 id = "X998_Y998"을 사용하여 문서를 쿼리한 다음 id와 message(msg로 별칭이 지정됨)를 프로젝션합니다.
6. 배열 속성 Tags가 있는 문서를 필터링하고 _ts 타임스탬프 시스템 속성으로 결과 문서를 정렬한 다음, Tags 배열을 프로젝션 및 평면화합니다.


## <a name="runtime-support"></a>런타임 지원
[Azure Cosmos DB JavaScript 서버 쪽 API](http://azure.github.io/azure-documentdb-js-server/)는 [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)에서 표준화된 일반 JavaScript 언어 기능을 대부분 지원합니다.

### <a name="security"></a>보안
JavaScript 저장 프로시저와 트리거는 한 스크립트의 결과가 데이터베이스 수준의 스냅숏 트랜잭션 격리를 통과하지 않고 다른 스크립트로 누출되지 않도록 샌드박스됩니다. 런타임 환경은 풀링되지만 각 실행 후에 컨텍스트가 정리됩니다. 따라서 서로 간의 의도치 않은 파생 작업으로부터 보호됩니다.

### <a name="pre-compilation"></a>사전 컴파일
저장 프로시저, 트리거 및 UDF는 각 스크립트 호출 시 컴파일 비용을 방지하기 위해 암시적으로 바이트 코드 형식으로 사전 컴파일됩니다. 사전 컴파일을 사용하면 저장 프로시저 호출이 빠르며 사용 공간이 적습니다.

## <a name="client-sdk-support"></a>클라이언트 SDK 지원
Azure Cosmos DB [Node.js](sql-api-sdk-node.md) API 외에도 Azure Cosmos DB에는 SQL API용 [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/) 및 [Python SDK](sql-api-sdk-python.md)도 있습니다. 이러한 SDK를 사용하여 저장 프로시저, 트리거 및 UDF를 만들고 실행할 수도 있습니다. 다음 예제에서는 .NET 클라이언트를 사용하여 저장 프로시저를 만들고 실행하는 방법을 보여 줍니다. .NET 유형을 저장 프로시저에 JSON으로 전달하고 다시 읽는 방법을 확인합니다.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);


이 샘플에서는 [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)를 사용하여 사전 트리거를 만들고 트리거를 사용하도록 설정하여 문서를 만드는 방법을 보여 줍니다. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


다음 예제에서는 UDF(사용자 정의 함수)를 만들고 [SQL 쿼리](sql-api-sql-query.md)에 사용하는 방법을 보여 줍니다.

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>REST API
모든 Azure Cosmos DB 작업은 RESTful 방식으로 수행할 수 있습니다. HTTP POST를 사용하여 저장 프로시저, 트리거 및 사용자 정의 함수를 컬렉션 아래에 등록할 수 있습니다. 다음 예제는 저장 프로시저를 등록하는 방법을 보여줍니다.

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


만들려는 저장 프로시저를 본문에 포함하여 URI dbs/testdb/colls/testColl/sprocs에 대해 POST 요청을 실행하면 저장 프로시저가 등록됩니다. 트리거 및 UDF는 각각 /triggers 및 /udfs에 대해 POST를 실행해서 비슷하게 등록할 수 있습니다.
그런 다음 해당 리소스 링크에 대해 POST 요청을 실행해서 이 저장 프로시저를 실행할 수 있습니다.

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


여기서는 저장 프로시저에 대한 입력이 요청 본문에 전달됩니다. 입력은 입력 매개 변수의 JSON 배열로 전달됩니다. 저장 프로시저는 첫 번째 입력을 응답 본문인 문서로 사용합니다. 수신하는 응답은 다음과 같습니다.

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


저장 프로시저와 달리 트리거는 직접 실행할 수 없습니다. 대신, 문서 작업의 일부로 실행됩니다. HTTP 헤더를 사용하여 요청과 함께 실행할 트리거를 지정할 수 있습니다. 다음 코드는 문서 만들기 요청을 보여 줍니다.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


여기서는 요청과 함께 실행할 사전 트리거가 x-ms-documentdb-pre-trigger-include 헤더에 지정됩니다. 마찬가지로, 모든 사후 트리거는 x-ms-documentdb-post-trigger-include 헤더에 지정됩니다. 지정된 요청에 사전 트리거와 사후 트리거를 둘 다 지정할 수 있습니다.

## <a name="sample-code"></a>샘플 코드
[GitHub 리포지토리](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples)에서 더 많은 서버 쪽 코드 예제([bulk-delete](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js) 및 [update](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js) 포함)를 찾을 수 있습니다.

놀라운 저장 프로시저를 공유하시겠습니까? 리포지토리에 참여하고 끌어오기 요청을 만드세요! 

## <a name="next-steps"></a>다음 단계
하나 이상의 저장된 프로시저, 트리거 및 생성된 사용자 정의 함수가 있다면 데이터 탐색기를 사용하여 Azure Portal에서 해당 함수를 로드하고 볼 수 있습니다.

또한 Azure Cosmos DB 서버 쪽 프로그래밍에 대한 자세한 내용을 보려면 경로에서 다음의 참조 자료 및 리소스가 유용합니다.

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [DocumentDB 스튜디오](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [안전하고 이식 가능한 데이터베이스 확장성](http://dl.acm.org/citation.cfm?id=276339) 
* [서비스 지향 데이터베이스 아키텍처](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Microsoft SQL server에서 .NET 런타임 호스팅](http://dl.acm.org/citation.cfm?id=1007669)

