---
title: Azure Cosmos DB에 대한 C++ 자습서 | Microsoft Docs
description: C++용 Azure Cosmos DB 인증 SDK를 사용하여 C++ 데이터베이스 및 콘솔 응용 프로그램을 만드는 C++ 자습서입니다. Azure Cosmos DB는 전 세계적인 규모의 데이터베이스 서비스입니다.
services: cosmos-db
documentationcenter: cpp
author: asthana86
manager: kfile
editor: ''
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 12/25/2016
ms.author: aasthan
ms.openlocfilehash: e04723a8fa37d32851dd91cdc45293a549c9ee64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB: SQL API에 대한 C++ 콘솔 응용 프로그램 자습서
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [MongoDB용 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

C++용 Azure Cosmos DB SQL API 인증 SDK에 대한 C++ 자습서를 시작합니다. 이 자습서를 따라 하면 C++ 데이터베이스를 포함하여 Azure Cosmos DB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 만들어집니다.

이 빠른 시작에서 다루는 내용은 같습니다.

* Azure Cosmos DB 계정 만들기 및 연결
* 응용 프로그램 설정
* C++ Azure Cosmos DB 데이터베이스 만들기
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리
* 문서 바꾸기
* 문서 삭제
* C++ Azure Cosmos DB 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/stalker314314/sql-apiCpp)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution) 를 참조하세요.

이제 시작하겠습니다.

## <a name="prerequisites-for-the-c-tutorial"></a>C++ 자습서의 필수 구성 요소
다음 리소스가 있는지 확인합니다.

* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* C++ 언어 구성 요소가 설치된 [Visual Studio 2017](https://www.visualstudio.com/downloads/)입니다. Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [C++ 응용 프로그램 설치](#SetupC++)로 건너뛸 수 있습니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>2단계: C++ 응용 프로그램 설정
1. Visual Studio를 열고 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다. 
2. **새 프로젝트** 창의 **설치됨** 창에서 **Visual C++**를 확장하고 **Win32**, **Win32 콘솔 응용 프로그램**을 차례로 클릭합니다. 프로젝트 이름을 hellodocumentdb로 지정한 다음 **확인**을 클릭합니다. 
   
    ![새 프로젝트 마법사의 스크린샷](media/sql-api-cpp-get-started/hello.png)
3. Win32 응용 프로그램 마법사가 시작되면 **마침**을 클릭합니다.
4. 프로젝트를 만들면 **솔루션 탐색기**에서 **hellodocumentdb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 엽니다. 
   
    ![프로젝트 메뉴에서 NuGet 패키지 관리를 보여 주는 스크린샷](media/sql-api-cpp-get-started/nuget.png)
5. **NuGet: hellodocumentdb** 탭에서 **찾아보기**를 클릭한 다음 *documentdbcpp*를 검색합니다. 결과에서 다음 스크린샷에 표시된 것처럼 DocumentDbCPP를 선택합니다.   
   
    ![DocumentDbCpp 패키지를 강조 표시하는 스크린샷](media/sql-api-cpp-get-started/cpp.png)
   
    이 패키지에서는 DocumentDbCPP에 대한 종속성이 있는 C++ REST SDK에 대한 참조를 설치합니다. 패키지가 프로젝트에 추가되면 코드 작성을 시작하는 설정이 완료됩니다.   

## <a id="Config"></a>3단계: Azure Cosmos DB 데이터베이스에 대한 Azure Portal의 연결 세부 정보 복사
[Azure Portal](https://portal.azure.com)을 불러와서, 만든 Azure Cosmos DB 계정으로 이동합니다. 다음 단계에서는 C++ 코드 조각에서 연결을 설정하기 위해 Azure Portal의 URI 및 기본 키가 필요합니다. 

![Azure Portal에서 Azure Cosmos DB URI 및 키](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>4단계: Azure Cosmos DB 계정에 연결
1. `#include "stdafx.h"` 뒤의 소스 코드에 다음 헤더 및 네임스페이스를 추가합니다.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 그런 다음 main 함수에 다음 코드를 추가하고 계정 구성 및 기본 키를 바꾸어 3단계의 Azure Cosmos DB 설정과 일치시킵니다. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    이제 클라이언트를 시작하는 코드가 있으므로 Azure Cosmos DB 리소스와 함께 작동하는지 살펴보겠습니다.

## <a id="CreateDBColl"></a>5단계: C++ 데이터베이스 및 컬렉션 만들기
이 단계를 수행하기 전에 Azure Cosmos DB를 처음 접하는 경우 데이터베이스, 컬렉션 및 문서가 상호 작용하는 방법을 살펴보겠습니다. [데이터베이스](sql-api-resources.md#databases)는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. [컬렉션](sql-api-resources.md#collections)은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. [Azure Cosmos DB 계층적 리소스 모델 및 개념](sql-api-resources.md)에서 Azure Cosmos DB 계층적 리소스 모델 및 개념에 대해 자세히 알아볼 수 있습니다.

데이터베이스와 해당 컬렉션을 만들려면 main 함수 끝에 다음 코드를 추가합니다. 이전 단계에서 선언한 클라이언트 구성을 사용하여 'FamilyRegistry'라는 데이터베이스 및 'FamilyCollection'라는 컬렉션을 만듭니다.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>6단계: 문서 만들기
[문서](sql-api-resources.md#documents)는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 Azure Cosmos DB에 문서를 삽입할 수 있습니다. main 함수의 끝에 다음 코드를 복사하여 문서를 만들 수 있습니다. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

요약하자면 이 코드는 Azure Portal의 데이터 탐색기에서 쿼리할 수 있는 Azure Cosmos DB 데이터베이스, 컬렉션 및 문서를 만듭니다. 

![C++ 자습서 - 계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>7단계: Azure Cosmos DB 리소스 쿼리
Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](sql-api-sql-query.md)를 지원합니다. 다음 샘플 코드는 SQL 구문을 사용해서 만든 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 만든 문서에 대해 실행할 수 있습니다.

함수는 문서 클라이언트와 함께 데이터베이스 및 컬렉션에 대한 고유한 식별자 또는 리소스 ID를 인수로 사용합니다. main 함수 앞에 이 코드를 추가합니다.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>8단계: 문서 바꾸기
Azure Cosmos DB는 다음 코드에서와 같이 JSON 문서를 바꾸도록 지원합니다. executesimplequery 함수 뒤에 이 코드를 추가합니다.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>9단계: 문서 삭제
Azure Cosmos DB는 JSON 문서를 삭제하도록 지원합니다. replacedocument 함수 뒤에 다음 코드를 복사하고 붙여 넣는 방법으로 수행할 수 있습니다. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>10단계: 데이터베이스 삭제
만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다.

deletedocument 함수 뒤에 다음 코드 조각(함수 정리)을 복사하고 붙여넣어서 데이터베이스와 모든 하위 리소스를 제거합니다.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>11단계: C# 응용 프로그램 모두 함께 실행
이제 다른 Azure Cosmos DB 리소스를 만들기, 쿼리, 수정 및 삭제하는 코드를 추가했습니다.  이제 몇몇 진단 메시지와 함께 hellodocumentdb.cpp에서 main 함수의 이러한 다양한 기능에 대한 호출을 추가하여 마무리합니다.

다음 코드로 응용 프로그램의 main 함수를 대체하여 수행할 수 있습니다. 3단계에서 코드에 복사한 account_configuration_uri 및 primary_key를 덮어 쓰기 때문에 해당 줄을 저장하거나 포털에서의 값을 다시 복사합니다. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

이제 Visual Studio에서 F5 키를 누르거나 터미널 창에서 응용 프로그램을 찾고 실행 파일을 실행하여 코드를 빌드하고 실행할 수 있게 됩니다. 

시작한 앱의 출력이 표시됩니다. 출력은 다음 스크린샷과 일치해야 합니다.

![Azure Cosmos DB C++ 응용 프로그램 출력](media/sql-api-cpp-get-started/console.png)

축하합니다! C++ 자습서를 완료했으며 첫 번째 Azure Cosmos DB 콘솔 응용 프로그램이 있습니다.

## <a id="GetSolution"></a> 전체 C++ 자습서 솔루션 가져오기
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

* [Azure Cosmos DB 계정][create-account].
* GitHub에서 제공하는 [GetStarted](https://github.com/stalker314314/DocumentDBCpp) 솔루션

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB 계정 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)에서 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [Azure Cosmos DB 설명서](https://azure.microsoft.com/documentation/services/cosmos-db/) 페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[create-account]: create-sql-api-dotnet.md#create-account


