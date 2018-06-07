---
title: Azure Functions를 사용하여 데이터베이스 정리 작업 수행 | Microsoft Docs
description: Azure Functions를 사용하여 Azure SQL Database에 연결하여 행을 주기적으로 정리하는 작업을 예약합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions를 사용하여 Azure SQL Database에 연결
이 항목에서는 Azure Functions를 사용하여 Azure SQL Database의 테이블에서 행을 정리하는 예약된 작업을 만드는 방법을 보여 줍니다. 새 C# 스크립트 함수는 Azure Portal에서 미리 정의된 타이머 트리거 템플릿을 기반으로 생성됩니다. 이 시나리오를 지원하려면 함수 앱에서 데이터베이스 연결 문자열을 앱 설정으로 설정해야 합니다. 이 시나리오는 데이터베이스에 대한 대량 작업을 사용합니다. 

함수가 Mobile Apps 테이블에서 개별 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하게 하려면 [Mobile Apps 바인딩](functions-bindings-mobile-apps.md)을 사용해야 합니다.

## <a name="prerequisites"></a>필수 조건

+ 이 항목에서는 타이머 트리거 함수를 사용합니다. 이 함수의 C# 버전을 만들려면 [Azure에서 타이머에 따라 트리거되는 함수 만들기](functions-create-scheduled-function.md) 항목의 단계를 완료합니다.   

+ 이 항목은 AdventureWorksLT 샘플 데이터베이스의 **SalesOrderHeader** 테이블에서 대량 정리 작업을 실행하는 Transact-SQL 명령을 보여 줍니다. AdventureWorksLT 샘플 데이터베이스를 만들려면 [Azure Portal에서 Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 항목의 단계를 완료합니다. 

## <a name="get-connection-information"></a>연결 정보 가져오기

[Azure Portal에서 Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md)를 완료하면 만든 데이터베이스에 대한 연결 문자열을 가져와야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
 
3. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 선택합니다.

4. **데이터베이스 연결 문자열 표시**를 선택하고 **ADO.NET** 연결 문자열 전체를 복사합니다. 

    ![ADO.NET 연결 문자열을 복사합니다.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>연결 문자열 설정 

함수 앱은 Azure에서 함수 실행을 호스트합니다. 함수 앱 설정에 연결 문자열과 다른 비밀 정보를 저장하는 것이 좋습니다. 응용 프로그램 설정을 사용하여 코드로 연결 문자열이 실수로 노출되는 것을 방지합니다. 

1. [Azure에서 타이머에 따라 트리거되는 함수 만들기](functions-create-scheduled-function.md)에서 만든 함수 앱으로 이동합니다.

2. **플랫폼 기능** > **응용 프로그램 설정**을 선택합니다.
   
    ![함수 앱에 대한 응용 프로그램 설정입니다.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. **연결 문자열**까지 아래로 스크롤하고 테이블에 지정된 설정을 사용하여 연결 문자열을 추가합니다.
   
    ![함수 앱 설정에 연결 문자열을 추가합니다.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | 설정       | 제안 값 | 설명             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name**  |  sqldb_connection  | 함수 코드에 저장된 연결 문자열에 액세스하는 데 사용합니다.    |
    | **값** | 복사한 문자열  | 이전 섹션에서 복사한 연결 문자열을 붙여넣고 `{your_username}` 및 `{your_password}` 자리 표시자를 실제 값으로 바꿉니다. |
    | **형식** | SQL Database | 기본 SQL Database 연결을 사용합니다. |   

3. **저장**을 클릭합니다.

이제 SQL Database와 연결하는 C# 함수 코드를 추가할 수 있습니다.

## <a name="update-your-function-code"></a>함수 코드 업데이트

1. 함수 앱의 포털에서 타이머 트리거 함수를 선택합니다.
 
3. 기존 C# 스크립트 함수 코드의 맨 위에 다음 어셈블리 참조를 추가합니다.

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >이 예제의 코드는 포털의 C# 스크립트입니다. 미리 컴파일된 C# 함수를 로컬로 개발하는 경우 대신 로컬 프로젝트의 이러한 어셈블리에 대한 참조를 추가해야 합니다.  

3. 다음 `using` 문을 함수에 추가합니다.
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. 기존 `Run` 함수를 다음 코드로 바꿉니다.
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    이 샘플 명령은 운송 날짜를 기준으로 `Status` 열을 업데이트합니다. 32행의 데이터를 업데이트해야 합니다.

5. **저장**을 클릭하고 **로그** 창에서 다음 함수 실행을 확인한 다음 **SalesOrderHeader** 테이블에서 업데이트된 행 개수를 메모합니다.

    ![함수 로그를 확인합니다.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>다음 단계

다음으로, Logic Apps와 함께 함수를 사용하여 다른 서비스와 통합하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"] 
> [Logic Apps와 통합되는 함수 만들기](functions-twitter-email.md)

Functions에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Functions 개발자 참조](functions-reference.md)  
  함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
* [Azure Functions 테스트](functions-test-a-function.md)  
  함수를 테스트하는 다양한 도구와 기법을 설명합니다.  
