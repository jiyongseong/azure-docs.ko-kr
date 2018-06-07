---
title: Azure Data Factory를 사용하여 Salesforce 간에 데이터 복사 | Microsoft Docs
description: 데이터 팩터리 파이프라인의 복사 작업을 사용하여 Salesforce에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 Salesforce로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: f4de97ef2df5351ac7e8574717ee1439b54a90e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Salesforce 간에 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - 일반 공급](v1/data-factory-salesforce-connector.md)
> * [버전 2 - 미리 보기](connector-salesforce.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Salesforce 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급되는 데이터 팩터리 버전 1을 사용하는 경우 [버전 1의 Salesforce 저장소 커넥터](v1/data-factory-salesforce-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Salesforce 에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Salesforce에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Salesforce 커넥터는 다음을 지원합니다.

- Salesforce 개발자, Professional, Enterprise 또는 Unlimited Edition.
- Salesforce 프로덕션, 샌드박스 및 사용자 지정 도메인 간에 데이터 복사

## <a name="prerequisites"></a>필수 조건

Salesforce에서 API 권한을 사용하도록 설정해야 합니다. 자세한 내용은 [권한 집합에 따라 Salesforce에서 API 액세스를 사용하도록 설정](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)을 참조하세요.

## <a name="salesforce-request-limits"></a>Salesforce 요청 제한

Salesforce에는 총 API 요청 수와 동시 API 요청 수에 대한 제한이 있습니다. 다음 사항에 유의하세요.

- 동시 요청 수가 이 제한을 초과하면 제한이 발생하며 임의 오류가 표시됩니다.
- 총 요청 수가 이 제한을 초과하면 Salesforce 계정은 24시간 동안 차단됩니다.

두 시나리오 모두에서 "REQUEST_LIMIT_EXCEEDED" 오류 메시지가 나타날 수 있습니다. 자세한 내용은 [Salesforce 개발자 제한](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) 문서의 "API 요청 제한" 섹션을 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Salesforce 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Salesforce 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 |형식 속성은 **Salesforce**로 설정되어야 합니다. |예 |
| environmentUrl | Salesforce 인스턴스의 URL을 지정합니다. <br> - 기본값은 `"https://login.salesforce.com"`입니다. <br> - 샌드박스에서 데이터를 복사하려면 `"https://test.salesforce.com"`을 지정합니다. <br> - 사용자 지정 도메인에서 데이터를 복사하려면 예를 들어 `"https://[domain].my.salesforce.com"`을 지정합니다. |아니오 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예 |
| 암호 |사용자 계정으로 password를 지정합니다.<br/><br/>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| securityToken |사용자 계정에 대한 보안 토큰을 지정합니다. 보안 토큰을 재설정 및 가져오기 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)를 참조하세요. 일반적인 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요.<br/><br/>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에 연결된 서비스에 통합 런타임이 없는 경우 원본은 아니요, 싱크는 예입니다. |

>[!IMPORTANT]
>데이터를 Salesforce에 복사할 때 기본 Azure Integration Runtime을 복사실행에 사용할 수 없습니다. 즉, 원본에 연결된 서비스에 지정된 통합 런타임이 없는 경우, Salesforce 인스턴스에 가까운 위치로 명시적으로 [Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir)을 만듭니다. 다음 예제와 같이 Salesforce에 연결된 서비스를 연결합니다.

**예: 데이터 팩터리에 자격 증명 저장**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Key Vault에 자격 증명 저장**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Salesforce 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Salesforce 간에 데이터를 복사하려면 데이터 집합의 형식 속성을 **SalesforceObject**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **SalesforceObject**로 설정되어야 합니다.  | 예 |
| objectApiName | 데이터를 검색할 Salesforce 개체 이름입니다. | 원본에는 아니요이고 싱크에는 예입니다 |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 **API 이름**에 "__c" 부분이 필요합니다.

![데이터 팩터리 Salesforce 연결 API 이름](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**예제:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>이전 버전과의 호환성을 위해, Salesforce에서 데이터를 복사할 때 이전 "RelationalTable" 형식 데이터 집합을 사용할 경우 이 형식도 그대로 작동하지만 "SalesforceObject" 형식으로 전환하는 것이 좋습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **RelationalTable**로 설정해야 합니다. | 예 |
| tableName | Salesforce에 있는 테이블의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Salesforce 원본 및 싱크에서 지원하는 속성의 목록을 제공합니다.

### <a name="salesforce-as-a-source-type"></a>Salesforce를 원본 형식으로

Salesforce에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SalesforceSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성을 **SalesforceSource**로 설정해야 합니다. | 예 |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. SQL-92 쿼리 또는 [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리를 사용할 수 있습니다. 예는 `select * from MyTable__c`입니다. | 아니요(데이터 집합에서 "tableName"이 지정된 경우) |
| readBehavior | 기존 레코드를 쿼리할지, 아니면 삭제된 항목을 포함하여 모든 레코드를 쿼리할지 여부를 나타냅니다. 지정하지 않으면 기본 동작은 전자입니다. <br>허용되는 값: **query**(기본값), **queryAll**입니다.  | 아니오 |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 **API 이름**에 "__c" 부분이 필요합니다.

![데이터 팩터리 Salesforce 연결 API 이름 목록](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>이전 버전과의 호환성을 위해, Salesforce에서 데이터를 복사할 때 이전 "RelationalSource" 형식 데이터 집합을 사용할 경우 이 형식도 그대로 작동하지만 "SalesforceSource" 형식으로 전환하는 것이 좋습니다.

### <a name="salesforce-as-a-sink-type"></a>Salesforce를 싱크 형식으로

Salesforce에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SalesforceSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 형식 속성은 **SalesforceSink**로 설정해야 합니다. | 예 |
| writeBehavior | 작업의 쓰기 동작입니다.<br/>허용되는 값은 **Insert** 및 **Upsert**입니다. | 아니요(기본값: 삽입) |
| externalIdFieldName | Upsert 작업의 외부 ID 필드 이름입니다. 지정된 필드는 Salesforce 개체에서 "외부 ID필드"로 정의되어야 합니다. 해당하는 입력 데이터에서 NULL 값을 가질 수 없습니다. | "Upsert"에서 예 |
| writeBatchSize | 각 일괄 처리에서 Salesforce에 작성된 데이터의 행 수입니다. | 아니요(기본값: 5,000) |
| ignoreNullValues | 쓰기 작업 중에 입력 데이터에서 NULL 값을 무시할지 여부를 나타냅니다.<br/>허용되는 값은 **true** 및 **false**입니다.<br>- **True**: Upsert나 업데이트 작업을 수행할 때 대상 개체의 데이터를 변경하지 않고 유지합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.<br/>- **False**: Upsert나 업데이트 작업을 수행할 때 대상 개체의 데이터를 NULL로 업데이트합니다. 삽입 작업을 수행할 때 NULL 값을 삽입합니다. | 아니요(기본값: false) |

**예: 복사 작업의 Salesforce 싱크**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>쿼리 팁

### <a name="retrieve-data-from-a-salesforce-report"></a>Salesforce 보고서에서 데이터 검색

쿼리를 `{call "<report name>"}`으로 지정하여 Salesforce 보고서에서 데이터를 검색할 수 있습니다. 예는 `"query": "{call \"TestReport\"}"`입니다.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Salesforce 휴지통에서 삭제된 레코드 검색

임시 삭제된 쿼리를 Salesforce 휴지통에서 쿼리하려면 쿼리에 **"IsDeleted = 1"** 을 지정하면 됩니다. 예: 

* 삭제된 레코드만 쿼리하려면 "select * from MyTable__c **where IsDeleted= 1**"을 지정합니다.
* 기존 레코드와 삭제된 레코드를 포함하여 모든 레코드를 쿼리하려면 "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"을 지정합니다.

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>DateTime 열에서 Where 문을 사용하여 데이터를 검색합니다.

SOQL 또는 SQL 쿼리를 지정할 때 DateTime 형식 차이에 주의해야 합니다. 예: 

* **SOQL 샘플**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 샘플**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Salesforce에 대한 데이터 형식 매핑

Salesforce에서 데이터를 복사할 경우 Salesforce 데이터 형식에서 데이터 팩터리 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Salesforce 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| 자동 번호 |문자열 |
| 확인란 |BOOLEAN |
| 통화 |Double |
| Date |Datetime |
| 날짜/시간 |Datetime |
| Email |문자열 |
| Id |문자열 |
| 관계 조회 |문자열 |
| 다중 선택 선택 목록 |문자열 |
| Number |Double |
| 백분율 |Double |
| Phone |문자열 |
| 선택 목록 |문자열 |
| 텍스트 |문자열 |
| 텍스트 영역 |문자열 |
| 텍스트 영역(Long) |문자열 |
| 텍스트 영역(Rich) |문자열 |
| 텍스트(암호화됨) |문자열 |
| URL |문자열 |

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.