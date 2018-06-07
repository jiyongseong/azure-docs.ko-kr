---
title: Data Factory를 사용하여 Azure SQL Database 간에 데이터 복사 | Microsoft Docs
description: 지원되는 원본 데이터 저장소에서 Azure SQL Database로 (또는) SQL Database에서 지원되는 싱크 데이터 저장소로 Data Factory를 사용하여 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 0503b355089fe6bbcc7632ac93fd21e71f268032
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-azure-sql-connector.md)
> * [버전 2 - 미리 보기](connector-azure-sql-database.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Azure SQL Database 커넥터](v1/data-factory-azure-sql-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 모든 지원되는 원본 데이터 저장소에서 Azure SQL Database로 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure SQL Database 커넥터는 다음을 지원합니다.

- 서비스 주체 또는 MSI(관리 서비스 ID)와 함께 **SQL 인증** 및 **Azure Active Directory 응용 프로그램 토큰 인증**을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 첨부하거나 복사 중에 사용자 지정 논리를 사용하여 저장 프로시저를 호출합니다.

> [!IMPORTANT]
> Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) [Azure SQL Server 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성합니다. 자체 호스팅 Integration Runtime을 사용하여 데이터를 복사하는 경우 Azure SQL Database에 연결하는 데 사용되는 컴퓨터의 IP를 포함하는 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **AzureSqlDatabase** | 예 |
| connectionString |connectionString 속성에 대한 Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| servicePrincipalId | 응용 프로그램의 클라이언트 ID를 지정합니다. | 서비스 주체와 함께 AAD 인증을 사용하는 경우 예입니다. |
| servicePrincipalKey | 응용 프로그램의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 AAD 인증을 사용하는 경우 예입니다. |
| tenant | 응용 프로그램이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 서비스 주체와 함께 AAD 인증을 사용하는 경우 예입니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니오 |

다른 인증 형식의 경우 각각의 필수 구성 요소 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증 사용](#using-sql-authentication)
- [AAD 응용 프로그램 토큰 인증 사용 - 서비스 주체](#using-service-principal-authentication)
- [AAD 응용 프로그램 토큰 인증 사용 - 관리 서비스 ID](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>SQL 인증 사용

**SQL 인증을 사용한 연결된 서비스 예제:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>서비스 주체 인증 사용

AAD 응용 프로그램 토큰 인증을 기반으로 한 서비스 주체를 사용하려면 다음 단계를 수행합니다.

1. **[Azure Portal에서 Azure Active Directory 응용 프로그램을 만듭니다](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  응용 프로그램 이름 및 연결된 서비스를 정의하는 데 사용하는 다음 값을 적어둡니다.

    - 응용 프로그램 UI
    - 응용 프로그램 키
    - 테넌트 ID

2. 아직 완료하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. AAD 관리자는 AAD 사용자 또는 AAD 그룹이어야 하지만 서비스 주체일 수는 없습니다. 이 단계가 수행되면, 이후 단계에서 사용자는 AAD ID를 사용하여 서비스 주체에 대해 포함된 데이터베이스 사용자를 만들 수 있습니다.

3. 최소한의 모든 사용자 변경 권한이 있는 AAD ID로 SSMS와 같은 도구를 사용하여 데이터를 복사할 데이터베이스에 연결하고 다음 T-SQL을 실행하여 **서비스 주체에 대해 포함된 데이터베이스 사용자를 만듭니다**. [여기](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)에서 포함된 데이터베이스 사용자에 대해 자세히 알아보세요.
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 예를 들어 다음을 실행합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. ADF에서 Azure SQL Database 연결된 서비스를 구성합니다.


**서비스 주체 인증을 사용한 연결된 서비스 예제:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>관리 서비스 ID 인증 사용

데이터 팩터리는 이 특정 데이터 팩터리를 나타내는 [MSI(관리 서비스 ID)](data-factory-service-identity.md)에 연결할 수 있습니다. 사용자의 데이터베이스에 액세스하고 데이터를 복사할 수 있도록 이 지정된 팩터리를 허용하는 Azure SQL Database 인증에 대한 이 서비스 ID를 사용할 수 있습니다.

AAD 응용 프로그램 토큰 인증을 기반으로 한 MSI를 사용하려면 다음 단계를 수행합니다.

1. **Azure AD에서 그룹을 만들고 팩터리 MSI를 그룹의 구성원으로 만듭니다**.

    a. Azure Portal에서 데이터 팩터리 서비스 ID를 찾습니다. 데이터 팩터리 -> 속성으로 이동하여 -> **서비스 ID**를 복사합니다.

    나. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 모듈을 설치하고, `Connect-AzureAD` 명령을 사용하여 로그인하고, 다음 명령을 실행하여 그룹을 만들고 데이터 팩터리 MSI를 구성원으로 추가합니다.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. 아직 완료하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. AAD 사용자는 AAD 사용자 또는 AAD 그룹일 수 있습니다. MSI로 그룹에 관리자 역할을 부여한 경우 관리자가 DB에 대한 모든 액세스 권한을 가지므로 아래 3 및 4단계는 건너뜁니다.

3. 최소한의 모든 사용자 변경 권한이 있는 AAD ID로 SSMS와 같은 도구를 사용하여 데이터를 복사할 데이터베이스에 연결하고 다음 T-SQL을 실행하여 **AAD 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다**. [여기](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)에서 포함된 데이터베이스 사용자에 대해 자세히 알아보세요.
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자에 대해 수행하듯이 **AAD 그룹에 필요한 권한을 부여**합니다. 예를 들어 다음을 실행합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. ADF에서 Azure SQL Database 연결된 서비스를 구성합니다.

**MSI 인증을 사용한 연결된 서비스 예제:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Azure SQL Database 간에 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureSqlTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **AzureSqlTable**로 설정해야 합니다. | 예 |
| tableName |연결된 서비스가 참조하는 Azure SQL Database 인스턴스에서 테이블 또는 보기의 이름입니다. | 예 |

**예제:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-source"></a>원본으로 Azure SQL Database

Azure SQL Database에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성을 **SqlSource**로 설정해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. |아니오 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아니오 |

**주의할 사항:**

- **sqlReaderQuery** 이 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL Database 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
- "sqlReaderQuery" 또는 "sqlReaderStoredProcedureName" 중 하나를 지정하지 않으면 JSON 데이터 집합의 "structure" 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 Azure SQL Database에 대해 실행합니다. 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.
- **sqlReaderStoredProcedureName**을 사용하는 경우에도 데이터 집합 JSON에서 더미 **tableName** 속성을 지정해야 합니다.

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**저장 프로시저 정의:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-sink"></a>싱크로 Azure SQL Database

Azure SQL Database에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 형식 속성은 **SqlSink**로 설정해야 합니다. | 예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예: “00:30:00”(30분). |아니오 |
| preCopyScript |Azure SQL Database에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아니오 |
| sqlWriterStoredProcedureName |원본 데이터를 대상 테이블에 적용하는 방법(예: 사용자 고유의 비즈니스 논리를 사용하여 upsert 또는 transform 수행)을 정의하는 저장 프로시저의 이름입니다. <br/><br/>이 저장 프로시저는 **배치마다 호출**됩니다. 한 번만 실행되고 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아니오 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |아니오 |

> [!TIP]
> Azure SQL Database로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. UPSERT 또는 추가 비즈니스 논리를 수행하려면 SqlSink에서 저장 프로시저를 사용합니다. 자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

**예 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**예 2: upsert에 대해 복사 중 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>대상 데이터베이스의 ID 열

이 섹션에서는 ID 열이 없는 원본 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사하는 예제를 제공합니다.

**원본 테이블:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**대상 테이블:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

대상 테이블에 ID 열이 있는지 확인합니다.

**원본 데이터 집합 JSON 정의**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**대상 데이터 집합 JSON 정의**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

원본 테이블과 대상 테이블의 스키마가 서로 다릅니다(대상에 ID가 포함된 추가 열이 있음). 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 집합 정의에서 **structure** 속성을 지정해야 합니다.

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL 싱크에서 저장된 프로시저 호출

Azure SQL Database로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 추가 매개 변수로 호출할 수 있습니다.

기본 제공 메커니즘이 용도에 적합하지 않을 때는 저장 프로시저를 사용할 수 있습니다. 보통은 대상 테이블에서 원본 데이터의 최종 삽입 전에 upsert(insert + update, 삽입 + 업데이트) 또는 추가 처리(열 합병, 추가 값 검색, 여러 테이블에 삽입 등)를 수행해야 할 때 저장 프로시저를 사용합니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 ProfileID, State, Category 등의 세 개 열을 갖는 입력 데이터와 싱크 "Marketing" 테이블을 가정해 보겠습니다. “ProfileID” 열을 기준으로 upsert를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

복사 작업의 "SqlSink" 섹션을 다음과 같이 정의합니다.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

데이터베이스에서 "SqlWriterStoredProcedureName"과 동일한 이름으로 저장 프로시저를 정의합니다. 지정된 원본에서 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저의 매개 변수 이름은 데이터 집합에 정의된 "tableName"과 동일해야 합니다.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

Azure SQL Database 간에 데이터를 복사하는 경우 Azure SQL Database 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |BOOLEAN |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| 10진수 |10진수 |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |10진수 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |10진수 |
| nvarchar |String, Char[] |
| real |단일 |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |10진수 |
| sql_variant |개체 * |
| text |String, Char[] |
| 실시간 |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.