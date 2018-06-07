---
title: Azure Cosmos DB로 Studio 3T(MongoChef) 사용 | Microsoft Docs
description: Azure Cosmos DB MongoDB API 계정으로 Studio 3T를 사용하는 방법을 알아봅니다.
keywords: MongoChef, Studio 3T
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: anhoh
ms.openlocfilehash: 98f1a83ad2470d4e133167b6c4d140a0aa34e114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: MongoDB API 계정으로 Studio 3T 사용

Azure Cosmos DB MongoDB API 계정에 연결하려면 다음을 수행해야 합니다.

* [Studio 3T](https://studio3t.com/)(이전의 MongoChef)를 다운로드하여 설치합니다.
* MongoDB 계정에 대한 Azure Cosmos DB [연결 문자열](connect-mongodb-account.md) 정보가 필요합니다.

## <a name="create-the-connection-in-studio-3t"></a>Studio 3T에서 연결 만들기
Azure Cosmos DB 계정을 Studio 3T 연결 관리자에 추가하려면 다음 단계를 수행합니다.

1. [Azure Cosmos DB에 MongoDB 응용 프로그램 연결](connect-mongodb-account.md) 문서의 지침을 사용하여 MongoDB API 계정에 대한 Azure Cosmos DB 연결 정보를 검색합니다.

    ![연결 문자열 페이지의 스크린샷](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. **연결**을 클릭하여 연결 관리자를 열려고 **새 연결**을 클릭합니다.

    ![Studio 3T 연결 관리자의 스크린샷](./media/mongodb-mongochef/ConnectionManager.png)
3. **새 연결** 창의 **서버** 탭에서 Azure Cosmos DB 계정의 호스트(FQDN) 및 포트를 입력합니다.

    ![Studio 3T 연결 관리자 서버 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. **새 연결** 창의 **인증** 탭에서 **기본(MONGODB CR 또는 SCARM-SHA-1)** 인증 모드를 선택하고, 사용자 이름 및 암호를 입력합니다.  기본 인증 DB(관리자)를 그대로 사용하거나 고유한 값을 지정합니다.

    ![Studio 3T 연결 관리자 인증 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. **새 연결** 창의 **SSL** 탭에서 **SSL 프로토콜을 사용하여 연결** 확인란 및 **서버 자체 서명된 SSL 인증서 허용** 라디오 단추를 확인합니다.

    ![Studio 3T 연결 관리자 SSL 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. **연결 테스트** 단추를 클릭하여 연결 정보의 유효성을 검사하고 **확인**을 클릭하여 새 연결 창으로 돌아간 다음 **저장**을 클릭합니다.

    ![Studio 3T 연결 테스트 창의 스크린샷](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서 만들기
Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서를 만들려면 다음 단계를 수행합니다.

1. **연결 관리자**에서 연결을 강조 표시하고 **연결**을 클릭합니다.

    ![Studio 3T 연결 관리자의 스크린샷](./media/mongodb-mongochef/ConnectToAccount.png)
2. 호스트를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 추가**를 선택합니다.  데이터베이스 이름을 지정하고 **확인**을 클릭합니다.

    ![Studio 3T 데이터베이스 추가 옵션의 스크린샷](./media/mongodb-mongochef/AddDatabase1.png)
3. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 추가**를 선택합니다.  컬렉션 이름을 지정하고 **만들기**를 클릭합니다.

    ![Studio 3T 컬렉션 추가 옵션의 스크린샷](./media/mongodb-mongochef/AddCollection.png)
4. **컬렉션** 메뉴 항목을 클릭한 다음 **문서 추가**를 클릭합니다.

    ![Studio 3T 문서 추가 메뉴 항목의 스크린샷](./media/mongodb-mongochef/AddDocument1.png)
5. 문서 추가 대화 상자에서 다음을 붙여 넣고 **문서 추가**를 클릭합니다.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 이번에는 다음 내용이 포함된 다른 문서를 추가합니다.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. 샘플 쿼리를 실행합니다. 예를 들어 'Andersen' 성을 가진 가족을 검색하고 부모 및 상태 필드를 반환합니다.

    ![MongoChef 쿼리 결과의 스크린샷](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB MongoDB API [샘플](mongodb-samples.md)을 살펴봅니다.
