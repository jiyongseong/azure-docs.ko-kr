---
title: Data Factory를 사용하여 Azure SQL Data Warehouse 간에 데이터 복사 | Microsoft Docs
description: 지원되는 원본 저장소에서 Azure SQL Data Warehouse로 (또는) SQL Data Warehouse에서 지원되는 싱크 저장소로 Data Factory를 사용하여 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9ba48a9072a85e7d8e6e9fb17957efbf27711df8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886853"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Data Warehouse 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [버전 2 - 미리 보기](connector-azure-sql-data-warehouse.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Data Warehouse 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Azure SQL Data Warehouse 커넥터](v1/data-factory-azure-sql-data-warehouse-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Data Warehouse에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 모든 지원되는 원본 데이터 저장소에서 Azure SQL Data Warehouse로 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure SQL Data Warehouse 커넥터는 다음을 지원합니다.

- 서비스 주체 또는 MSI(관리 서비스 ID)와 함께 **SQL 인증** 및 **Azure Active Directory 응용 프로그램 토큰 인증**을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로 **PolyBase** 또는 대량 삽입을 사용하여 데이터를 로드합니다. 보다 나은 복사 성능을 위해 전자가 **권장**됩니다.

> [!IMPORTANT]
> PolyBase는 SQL 인증을 지원하나, Azure Active Directory 인증은 지원하지 않습니다.

> [!IMPORTANT]
> Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) [Azure SQL Server 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성합니다. 자체 호스팅 Integration Runtime을 사용하여 데이터를 복사하는 경우 Azure SQL Database에 연결하는 데 사용되는 컴퓨터의 IP를 포함하는 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Data Warehouse 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Data Warehouse 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **AzureSqlDW** | 예 |
| connectionString |connectionString 속성에 대한 Azure SQL Data Warehouse 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
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
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

2. 아직 완료하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. AAD 사용자는 AAD 사용자 또는 AAD 그룹일 수 있습니다. MSI로 그룹에 관리자 역할을 부여한 경우 관리자가 DB에 대한 모든 액세스 권한을 가지므로 아래 3 및 4단계는 건너뜁니다.

3. 최소한의 모든 사용자 변경 권한이 있는 AAD ID로 SSMS와 같은 도구를 사용하여 데이터를 복사할 데이터 웨어하우스에 연결하고 다음 T-SQL을 실행하여 **서비스 주체에 대해 포함된 데이터베이스 사용자를 만듭니다**. [여기](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)에서 포함된 데이터베이스 사용자에 대해 자세히 알아보세요.
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 예를 들어 다음을 실행합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. ADF에서 Azure SQL Data Warehouse 연결된 서비스를 구성합니다.


**서비스 주체 인증을 사용한 연결된 서비스 예제:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

데이터 팩터리는 이 특정 데이터 팩터리를 나타내는 [MSI(관리 서비스 ID)](data-factory-service-identity.md)에 연결할 수 있습니다. 사용자의 데이터 웨어하우스에 액세스하고 데이터를 복사할 수 있도록 이 지정된 팩터리를 허용하는 Azure SQL Data Warehouse 인증에 대한 이 서비스 ID를 사용할 수 있습니다.

> [!IMPORTANT]
> PolyBase는 현재 MSI 인증에 대해 지원되지 않습니다.

AAD 응용 프로그램 토큰 인증을 기반으로 한 MSI를 사용하려면 다음 단계를 수행합니다.

1. **Azure AD에서 그룹을 만들고 팩터리 MSI를 그룹의 구성원으로 만듭니다**.

    a. Azure Portal에서 데이터 팩터리 서비스 ID를 찾습니다. 데이터 팩터리 -> 속성으로 이동하여 -> **서비스 ID**를 복사합니다.

    나. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 모듈을 설치하고, `Connect-AzureAD` 명령을 사용하여 로그인하고, 다음 명령을 실행하여 그룹을 만들고 데이터 팩터리 MSI를 구성원으로 추가합니다.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. 아직 완료하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다.

3. 최소한의 모든 사용자 변경 권한이 있는 AAD ID로 SSMS와 같은 도구를 사용하여 데이터를 복사할 데이터 웨어하우스에 연결하고 다음 T-SQL을 실행하여 **AAD 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다**. [여기](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)에서 포함된 데이터베이스 사용자에 대해 자세히 알아보세요.
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자에 대해 수행하듯이 **AAD 그룹에 필요한 권한을 부여**합니다. 예를 들어 다음을 실행합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. ADF에서 Azure SQL Data Warehouse 연결된 서비스를 구성합니다.

**MSI 인증을 사용한 연결된 서비스 예제:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Azure SQL Data Warehouse 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Azure SQL Data Warehouse 간에 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureSqlDWTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **AzureSqlDWTable**로 설정해야 합니다. | 예 |
| tableName |연결된 서비스가 참조하는 Azure SQL Data Warehouse 인스턴스에서 테이블 또는 뷰의 이름입니다. | 예 |

**예제:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Data Warehouse 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse를 원본으로

Azure SQL Data Warehouse에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlDWSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성을 **SqlDWSource**로 설정해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. |아니오 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아니오 |

**주의할 사항:**

- **sqlReaderQuery**가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL Data Warehouse 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
- "sqlReaderQuery" 또는 "sqlReaderStoredProcedureName" 중 하나를 지정하지 않으면 JSON 데이터 집합의 "structure" 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 Azure SQL Data Warehouse에 대해 실행합니다. 데이터 집합 정의에 "structure"가 없는 경우 모든 열은 테이블에서 선택됩니다.
- **sqlReaderStoredProcedureName**을 사용하는 경우에도 데이터 집합 JSON에서 더미 **tableName** 속성을 지정해야 합니다.

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse 싱크로

Azure SQL Data Warehouse에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlDWSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 형식 속성은 **SqlDWSink**로 설정해야 합니다. | 예 |
| allowPolyBase |BULKINSERT 메커니즘 대신 PolyBase(있는 경우)를 사용할지 여부를 나타냅니다. <br/><br/> **SQL Data Warehouse로 데이터를 로드하는 데 PolyBase를 사용하는 것이 좋습니다.** 제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True**(기본값) 및 **False**입니다.  |아니오 |
| polyBaseSettings |**allowPolybase** 속성이 **true**로 설정된 경우 지정될 수 있는 속성의 그룹입니다. |아니오 |
| rejectValue |쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다.<br/><br/>**외부 테이블 만들기(Transact-SQL)** 토픽의 [인수](https://msdn.microsoft.com/library/dn935021.aspx) 섹션에 있는 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. <br/><br/>허용되는 값은 0(기본값), 1, 2, ...입니다. |아니오 |
| rejectType |rejectValue 옵션을 리터럴 값 또는 백분율로 지정할지 여부를 지정합니다.<br/><br/>허용되는 값은 **값**(기본값) 및 **백분율**입니다. |아니오 |
| rejectSampleValue |PolyBase가 거부된 행의 비율을 다시 계산하기 전에 검색할 행 수를 결정합니다.<br/><br/>허용되는 값은 1, 2, ...입니다. |예. **rejectType**이 **백분율**인 경우 |
| useTypeDefault |PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.<br/><br/>허용되는 값은 **True**, **False**(기본값)입니다. |아니오 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 PolyBase가 사용되지 않는 경우에만 적용됩니다.<br/><br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. PolyBase가 사용되지 않는 경우에만 적용됩니다.<br/><br/>허용되는 값은 시간 범위입니다. 예: “00:30:00”(30분). |아니오 |
| preCopyScript |각 실행 시 Azure SQL Data Warehouse에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아니오 |(#repeatability-during-copy). |쿼리 문입니다. |아니오 |

**예제:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

다음 섹션에서 PolyBase를 사용하여 효율적으로 SQL Data Warehouse에 로드하는 방법에 대해 자세히 알아봅니다.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드

**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** 를 사용하는 것은 처리량이 높은 많은 양의 데이터를 Azure SQL Data Warehouse에 로드하는 효율적인 방법입니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하여 처리량의 증가를 확인할 수 있습니다. 자세히 비교하려면 [복사 성능 참조 번호](copy-activity-performance.md#performance-reference)를 참조하세요. 사용 사례가 있는 연습을 보려면 [Azure Data Factory를 통해 Azure SQL Data Warehouse에 15분 이내 1TB 로드](connector-azure-sql-data-warehouse.md)를 참조하세요.

* 원본 데이터 형식이 **Azure Blob 또는 Azure Data Lake Store**에 있고 형식이 PolyBase와 호환될 경우 PolyBase를 사용하여 Azure SQL Data Warehouse로 직접 복사할 수 있습니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-using-polybase)** 를 참조하세요.
* 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우 대신 **[PolyBase를 사용하여 준비한 복사본](#staged-copy-using-polybase)** 기능을 사용할 수 있습니다. 데이터를 PolyBase 호환 형식으로 자동으로 변환하고 Azure Blob Storage에 저장하여 향상된 처리량을 제공하기도 합니다. 그런 다음 SQL Data Warehouse에 데이터를 로드합니다.

> [!IMPORTANT]
> PolyBase는 현재 AAD 응용 프로그램 토큰 인증을 기반으로 한 MSI(관리 서비스 ID)에 대해 지원되지 않습니다.

### <a name="direct-copy-using-polybase"></a>PolyBase를 사용하여 직접 복사

SQL Data Warehouse PolyBase는 Azure Blob 및 Azure Data Lake Store(서비스 주체 사용)를 원본으로 직접 지원하며 특정 파일 형식 요구 사항이 적용됩니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우 PolyBase를 사용하여 원본 데이터 저장소에서 Azure SQL Data Warehouse로 직접 복사할 수 있습니다. 그렇지 않을 경우, [PolyBase를 사용하여 준비한 복사본](#staged-copy-using-polybase)을 사용할 수 있습니다.

> [!TIP]
> Data Lake Store에서 SQL Data Warehouse로 데이터를 효율적으로 복사하려면 [SQL Data Warehouse와 함께 Data Lake Store를 사용할 경우 Azure Data Factory에서 데이터의 정보를 쉽고 편리하게 발견할 수 있음](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)에서 자세한 내용을 참조하세요.

조건을 충족하지 않는 경우 Azure 데이터 팩터리는 설정을 확인한 후 데이터 이동을 위한 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **원본에 연결된 서비스**의 형식은 **AzureStorage** 또는 서비스 주체 인증이 적용된 **AzureDataLakeStore**입니다.
2. **입력 데이터 집합**은 **AzureBlob** 또는 **AzureDataLakeStoreFile** 형식이고 `type` 속성의 서식 형식은 다음 구성이 포함된 **OrcFormat**, **ParquetFormat** 또는 **TextFormat**입니다.

   1. `rowDelimiter`는 **\n**이어야 합니다.
   2. `nullValue`가 **빈 문자열**("")으로 설정되어 있거나 `treatEmptyAsNull`이 **true**로 설정되어 있습니다.
   3. `encodingName`이 **기본값**인 **utf-8**로 설정됩니다.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` 및 `skipLineCount`는 지정되지 않습니다.
   5. `compression`은 **no compression**, **GZip** 또는 **Deflate**일 수 있습니다.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. 파이프라인에서 복사 작업에 대한 **BlobSource** 또는 **AzureDataLakeStore**에는 `skipHeaderLineCount` 설정이 없습니다.
4. 파이프라인에서 복사 작업에 대한 **SqlDWSink**에는 `sliceIdentifierColumnName` 설정이 없습니다. (PolyBase는 한 번의 실행으로 모든 데이터를 업데이트하거나 아무 것도 업데이트하지 않도록 보장합니다. **반복성**을 달성하려면 `sqlWriterCleanupScript`를 사용할 수 있습니다.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>PolyBase를 사용하여 준비한 복사본

원본 데이터가 이전 섹션에서 도입된 조건을 충족하지 않는 경우 일시적으로 스테이징한 Azure Blob Storage를 통해 데이터를 복사하도록 설정할 수 있습니다(Premium Storage일 수 없음). 이 경우 Azure Data Factory는 PolyBase의 데이터 형식 요구 사항을 충족시키기 위해 데이터에 변환을 수행한 다음 PolyBase를 사용하여 SQL Data Warehouse로 데이터를 로드하고 Blob Storage에서 임시 데이터를 삭제합니다. 스테이징 Azure Blob을 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](copy-activity-performance.md#staged-copy)를 참조하세요.

이 기능을 사용하려면 중간 Blob Storage가 있는 Azure Storage 계정을 나타내는 [Azure Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties)를 만든 후 다음 코드에 표시된 복사 작업에 대해 `enableStaging` 및 `stagingSettings` 속성을 지정합니다.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>PolyBase를 사용하는 경우 모범 사례

다음 섹션에서는 [Azure SQL Data Warehouse에 대한 모범 사례](../sql-data-warehouse/sql-data-warehouse-best-practices.md)에 나와 있는 추가 모범 사례를 제공합니다.

### <a name="required-database-permission"></a>필수 데이터베이스 권한

PolyBase를 사용하려면 SQL Data Warehouse에 데이터를 로드하는 데 사용되는 사용자에게 대상 데이터베이스에 대한 ["CONTROL" 권한](https://msdn.microsoft.com/library/ms191291.aspx)이 필요합니다. 이를 달성하기 위한 한 가지 방법으로 해당 사용자를 "db_owner" 역할의 구성원으로 추가합니다. 이를 수행하는 방법에 대해서는 [이 섹션](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)을 참조하세요.

### <a name="row-size-and-data-type-limitation"></a>행 크기 및 데이터 형식 제한 사항

Polybase 부하는 **1MB**보다 작고 VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)로 로드할 수 없는 행을 로드할 때만 사용됩니다. [여기](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)를 참조합니다.

1MB보다 큰 크기의 행을 포함한 원본 데이터가 있는 경우 각 항목의 최대 행 크기가 제한을 초과하지 않는 정도의 작은 테이블로 원본 테이블을 수직으로 분할하는 것이 좋습니다. 작은 테이블은 PolyBase를 사용하여 로드될 수 있고 Azure SQL Data Warehouse에서 병합될 수 있습니다.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse 리소스 클래스

가능한 최상의 처리량을 달성하려면 PolyBase를 통해 SQL Data Warehouse에 데이터를 로드하는 데 사용되는 사용자에게 더 큰 리소스 클래스를 할당하는 것이 좋습니다.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 tableName

다음 테이블은 스키마와 테이블 이름의 다양한 조합에 대한 JSON 데이터 집합에서 **tableName** 속성을 지정하는 방법에 대한 예제를 제공합니다.

| DB 스키마 | 테이블 이름 | tableName JSON 속성 |
| --- | --- | --- |
| dbo |MyTable |MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable 또는 [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] 또는 [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

다음 오류가 표시하는 경우 tableName 속성에 지정한 값에 관한 문제일 수 있습니다. tableName JSON 속성에 대한 값을 지정하는 올바른 방법은 테이블을 참조하세요.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>기본값이 있는 열

현재 데이터 팩터리의 PolyBase 기능은 대상 테이블과 동일한 열 수를 허용합니다. 가령 네 개의 열이 있고 그 중 한 열이 기본 값으로 정의된 테이블이 있다고 합시다. 입력 데이터는 여전히 네 개의 열을 포함해야 합니다. 3열 입력 데이터 집합을 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 값은 특별한 형태의 기본값입니다. 열이 null을 허용하면 해당 열에 대한 입력 데이터(Blob)는 비어 있을 수 있습니다(입력 데이터 집합에서 누락될 수 없음). PolyBase는 Azure SQL Data Warehouse의 해당 항목에 NULL을 삽입합니다.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 데이터 형식 매핑

Azure SQL Data Warehouse 간에 데이터를 복사하는 경우 Azure SQL Data Warehouse 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Data Warehouse 데이터 형식 | Data Factory 중간 데이터 형식 |
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