---
title: "Azure Cosmos DB 및 Functions를 사용하여 구조화되지 않은 데이터 저장 | Microsoft Docs"
description: "Azure Functions 및 Cosmos DB를 사용하여 구조화되지 않은 데이터 저장"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 이벤트 처리, Cosmos DB, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 
ms.service: functions
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: b9bb71adf85490fe68bf6b73133017c5e9c377e1
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2018
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Azure Functions 및 Azure Cosmos DB를 사용하여 구조화되지 않은 데이터 저장

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 구조화되지 않은 데이터 및 JSON 데이터를 저장하기 좋습니다. Cosmos DB를 Azure Functions와 함께 사용하면 관계형 데이터베이스에 데이터를 저장하는 데 필요한 것보다 훨씬 적은 코드를 사용하여 쉽고 빠르게 데이터를 저장할 수 있습니다.

> [!NOTE]
> 이때 Azure Cosmos DB 트리거, 입력 바인딩 및 출력 바인딩은 SQL API 및 Graph API 계정에서만 작동합니다.

Azure Functions에서 입력 및 출력 바인딩은 함수에서 외부 서비스 데이터로 연결하기 위한 선언적 방식을 제공합니다. 이 토픽에서는 기존 C# 함수를 업데이트하여 구조화되지 않은 데이터를 Cosmos DB 문서에 저장하는 출력 바인딩을 추가하는 방법을 알아봅니다. 

![Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-cosmosdb.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="add-an-output-binding"></a>출력 바인딩 추가

1. 함수 앱과 함수를 모두 확장합니다.

1. 페이지 오른쪽 위에서 **통합** 및 **+ 새 출력**을 선택합니다. **Azure Cosmos DB**를 선택하고 **선택**을 클릭합니다.

    ![Cosmos DB 출력 바인딩 추가](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

3. 다음 표에 지정된 대로 **Azure Cosmos DB 출력** 설정을 사용합니다. 

    ![Cosmos DB 출력 바인딩 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **문서 매개 변수 이름** | taskDocument | 코드에서 Cosmos DB 개체를 참조하는 이름. |
    | **데이터베이스 이름** | taskDatabase | 문서를 저장할 데이터베이스의 이름. |
    | **컬렉션 이름** | TaskCollection | 데이터베이스 컬렉션 이름. |
    | **true이면 Cosmos DB 데이터베이스 및 컬렉션을 만듭니다.** | 선택 | 아직 컬렉션이 없으므로 지금 만듭니다. |

4. **Azure Cosmos DB 문서 연결** 레이블 옆에 있는 **새로 만들기**를 선택하고 **+ 새로 만들기**를 선택합니다. 

5. 다음 표에 지정된 대로 **새 계정** 설정을 사용합니다. 

    ![Cosmos DB 연결 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

    | 설정      | 제안 값  | 설명                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | 데이터베이스의 이름 | Azure Cosmos DB 데이터베이스의 고유한 ID  |
    | **API** | SQL | SQL API를 선택합니다. 이때 Azure Cosmos DB 트리거, 입력 바인딩 및 출력 바인딩은 SQL API 및 Graph API 계정에서만 작동합니다. |
    | **구독** | Azure 구독 | Azure 구독  |
    | **리소스 그룹** | myResourceGroup |  함수 앱이 포함된 기존 리소스 그룹을 사용합니다. |
    | **위치**:  | WestEurope | 함수 앱 또는 저장된 문서를 사용하는 다른 앱과 가까운 위치를 선택합니다.  |

6. **확인**을 클릭하여 데이터베이스를 만듭니다. 데이터베이스를 만드는 데 몇 분 정도 걸릴 수 있습니다. 데이터베이스가 생성되면 데이터베이스 연결 문자열이 함수 앱 설정으로 저장됩니다. 이 앱 설정의 이름이 **Azure Cosmos DB 계정 연결**에 삽입됩니다. 
 
8. 연결 문자열이 설정된 후에는 **저장**을 선택하여 바인딩을 만듭니다.

## <a name="update-the-function-code"></a>함수 코드 업데이트

기존 C# 함수 코드를 다음 코드로 바꿉니다.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```
이 코드 샘플은 HTTP 요청 쿼리 문자열을 읽고 `taskDocument` 개체의 필드에 할당합니다. `taskDocument` 바인딩은 이 바인딩 매개 변수의 개체 데이터 중에서 바인딩된 문서 데이터베이스에 저장할 데이터를 보냅니다. 함수가 처음으로 실행될 때 데이터베이스가 생성됩니다.

## <a name="test-the-function-and-database"></a>함수 및 데이터베이스 테스트

1. 오른쪽 창을 확장하고 **테스트**를 선택합니다. **쿼리** 아래에서 **+ 매개 변수 추가**를 클릭하고 쿼리 문자열에 다음 매개 변수를 추가합니다.

    + `name`
    + `task`
    + `duedate`

2. **실행**을 클릭하고 200 상태가 반환되는지 확인합니다.

    ![Cosmos DB 출력 바인딩 구성](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

1. Azure Portal의 왼쪽에서 아이콘 표시줄을 확장하고, 검색 필드에 `cosmos`를 입력하고, **Azure Cosmos DB**를 선택합니다.

    ![Cosmos DB 서비스 검색](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png)

2. Azure Cosmos DB 계정을 선택한 다음 **데이터 탐색기**를 선택합니다. 

3. **컬렉션** 노드를 확장하고, 새 문서를 선택하고, 문서에 일부 추가 메타데이터와 함께 쿼리 문자열 값이 포함되어 있는지 확인합니다. 

    ![Cosmos DB 항목 확인](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

구조화되지 않은 데이터를 Azure Cosmos DB에 저장하는 HTTP 트리거에 성공적으로 바인딩을 추가했습니다.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Cosmos DB 데이터베이스에 바인딩하는 자세한 내용은 [Azure Functions Cosmos DB 바인딩](functions-bindings-cosmosdb.md)을 참조하세요.
