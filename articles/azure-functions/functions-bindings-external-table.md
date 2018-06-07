---
title: "Azure Functions의 외부 테이블 바인딩(실험)"
description: "Azure Functions에서 외부 테이블 바인딩 사용"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Azure Functions의 외부 테이블 바인딩(실험)

이 문서에서는 Azure Functions에서 SharePoint 및 Dynamics 등의 SaaS 공급자에서 테이블 형식 데이터로 작업하는 방법을 설명합니다. Azure Functions는 외부 테이블에 대한 입력 및 출력 바인딩을 지원합니다.

> [!IMPORTANT]
> 외부 테이블 바인딩은 실험 버전이므로, 절대 GA(일반 공급) 상태가 될 수 없습니다. 이러한 항목은 Azure Functions 1.x에만 포함되어 있으며, Azure Functions 2.x에 추가할 계획이 없습니다. SaaS 공급자의 데이터에 액세스해야 하는 시나리오의 경우 [함수를 호출하는 Logic Apps](functions-twitter-email.md)를 사용하는 것이 좋습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 연결

테이블 바인딩은 타사 SaaS 공급자를 인증하는 데 외부 API 연결을 활용합니다. 

바인딩을 할당할 때는 동일한 리소스 그룹 내에서 새 API 연결을 만들거나 기존 API 연결을 사용할 수 있습니다.

### <a name="available-api-connections-tables"></a>사용 가능한 API 연결(테이블)

|커넥터|트리거|입력|출력|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle 데이터베이스](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> 외부 테이블 연결은 [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)에서도 사용할 수 있습니다

## <a name="creating-an-api-connection-step-by-step"></a>API 연결 만들기: 단계별 방법

1. 함수 앱에 대한 Azure Portal 페이지에서 더하기 기호(**+**)를 선택하여 함수를 만듭니다.

1. **시나리오** 상자에서 **실험적**을 선택합니다.

1. **외부 테이블**을 선택합니다.

1. 언어를 선택합니다.

2. **외부 테이블 연결** 아래에서 기존 연결을 선택하거나 **새로 만들기**를 선택합니다.

1. 새 연결의 경우 설정을 구성하고 **권한 부여**를 선택합니다.

1. **만들기**를 선택하여 함수를 만듭니다.

1. **통합 > 외부 테이블**을 선택합니다.

1. 대상 테이블을 사용할 연결을 구성합니다. 이러한 설정은 SaaS 공급자마다 다릅니다. 예제가 다음 섹션에 포함되어 있습니다.

## <a name="example"></a>예

이 예제에서는 Id, LastName 및 FirstName 열이 있는 "Contact"라는 테이블에 연결합니다. 코드는 테이블에 연락처 엔터티를 나열하고 성명을 기록합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>SQL Server 데이터 원본

SQL Server에서 이 예제와 함께 사용할 테이블을 만들려는 경우 다음 스크립트를 사용합니다. `dataSetName`은 “default”입니다.

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Google Sheets 데이터 원본

Google Docs에서 이 예제와 함께 사용할 테이블을 만들려면 `Contact`라는 워크시트가 있는 스프레드시트를 만듭니다. 커넥터는 스프레드시트 표시 이름을 사용할 수 없습니다. dataSetName으로 내부 이름(굵게)을 사용해야 합니다. 예: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** 열 이름 `Id`, `LastName`, `FirstName`을 첫 번째 행에 추가한 후 후속 행에서 데이터를 채웁니다.

### <a name="salesforce"></a>Salesforce

Salesforce에서 이 예제를 사용하려는 경우 `dataSetName`은 "default"입니다.

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 설명|
|---------|----------------------|
|**type** | `apiHubTable`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. | 
|**연결**| API 연결 문자열을 저장하는 앱 설정을 식별합니다. 이 앱 설정은 통합 UI에서 API 연결을 추가할 때 자동으로 생성됩니다.|
|**dataSetName**|읽을 테이블을 포함하는 데이터 집합의 이름입니다.|
|**tableName**|테이블의 이름입니다.|
|**entityId**|테이블 바인딩을 위해 비워 두어야 합니다.

테이블 형식 커넥터는 데이터 집합을 제공하며 각 데이터 집합은 테이블을 포함합니다. 기본 데이터 집합의 이름은 “default”입니다. 다양한 SaaS 공급자의 데이터 집합 및 테이블의 제목은 아래에 나열됩니다.

|커넥터|데이터 집합|테이블|
|:-----|:---|:---| 
|**SharePoint**|사이트|SharePoint 목록
|**SQL**|데이터베이스|테이블 
|**Google Sheet**|스프레드시트|워크시트 
|**Excel**|Excel 파일|시트 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
