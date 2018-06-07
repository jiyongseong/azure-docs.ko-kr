---
title: Data Factory를 사용하여 Azure Table 저장소 간 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 저장소에서 Azure Table 저장소로 데이터를 복사하거나, Table 저장소에서 지원되는 싱크 저장소로 데이터를 복사하는 방법을 알아봅니다.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ef43037ff33b693256c82459eec2e4b3beab4d9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Table 저장소 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - 일반 공급](v1/data-factory-azure-table-connector.md)
> * [버전 2 - 미리 보기](connector-azure-table-storage.md)

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 Azure Table 저장소 간에 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급되는 Data Factory 버전 1을 사용하는 경우 [버전 1의 Table 저장소 커넥터](v1/data-factory-azure-table-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

지원되는 모든 원본 데이터 저장소의 데이터를 Table 저장소에 복사할 수 있습니다. 또한 Table 저장소의 데이터를 지원되는 모든 싱크 데이터 저장소에 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure Table 커넥터는 계정 키 및 서비스 공유 액세스 서명 인증을 모두 사용하여 데이터를 복사할 수 있습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Table 저장소에 특정한 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

### <a name="use-an-account-key"></a>계정 키 사용

계정 키를 사용하여 Azure Storage 연결된 서비스를 만들 수 있습니다. 데이터 팩터리에 Storage에 대한 전역 액세스를 제공합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureStorage**로 설정해야 합니다. |예 |
| connectionString | connectionString 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

**예제:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>서비스 공유 액세스 서명 인증 사용

공유 액세스 서명을 사용하여 Storage 연결된 서비스를 만들 수도 있습니다. 이 서비스는 저장소의 모든/특정 리소스에 대해 제한된/시간 제한 액세스를 데이터 팩터리에 제공합니다.

공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 이를 사용하여 저장소 계정의 개체에 대해 지정된 시간 동안 지정된 권한 집합이 있는 제한된 권한을 클라이언트에 부여할 수 있습니다. 계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 저장소 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 저장소 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

> [!IMPORTANT]
> Data Factory는 이제 서비스 공유 액세스 서명만 지원하고, 계정 공유 액세스 서명은 지원하지 않습니다. 이러한 두 유형 및 이를 생성하는 방법에 대한 자세한 내용은 [공유 액세스 서명 유형](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)을 참조하세요. Azure Portal 또는 Azure Storage 탐색기에서 생성된 공유 액세스 서명 URL은 계정 공유 액세스 서명이며 지원되지 않습니다.

> [!TIP]
> 다음 PowerShell 명령을 실행하여 저장소 계정에 대한 서비스 공유 액세스 서명을 생성할 수 있습니다. 자리 표시자를 바꾸고 필요한 권한을 부여합니다.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

서비스 공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureStorage**로 설정해야 합니다. |예 |
| sasUri | Blob, 컨테이너 또는 테이블과 같은 Storage 리소스에 대한 공유 액세스 서명 URI를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니오 |

**예제:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

공유 액세스 서명 URI를 만들 때 고려해야 할 사항은 다음과 같습니다.

- 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방법에 따라 개체에 대한 적절한 읽기/쓰기 권한을 설정합니다.
- **만료 시간**을 적절하게 설정합니다. Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않도록 합니다.
- URI는 필요에 따라 적절한 테이블 수준에서 만들어야 합니다.

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Table 데이터 집합에서 지원되는 속성의 목록을 제공합니다.

Azure Table 간에 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성은 **AzureTable**로 설정해야 합니다. |예 |
| tableName |연결된 서비스에서 참조하는 Table 저장소 데이터베이스 인스턴스의 테이블 이름입니다. |예 |

**예제:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마

Azure Table과 같이 스키마가 없는 데이터 저장소의 경우 Data Factory 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

* 데이터 집합 정의에서 **structure** 속성을 사용하여 데이터 구조를 지정하는 경우 Data Factory는 이 구조를 스키마로 인식합니다. 이 경우 열 값이 행에 포함되어 있지 않으면 null 값이 제공됩니다.
* 데이터 집합 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 전체 스키마가 첫 번째 행에 포함되어 있지 않으면 복사 활동의 결과에서 일부 열이 누락됩니다.

스키마가 없는 데이터 원본의 경우 **structure** 속성을 사용하여 데이터 구조를 지정하는 것이 가장 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Table 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="azure-table-as-a-source-type"></a>Azure Table을 원본 형식으로

Azure Table에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureTableSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 활동 source의 type 속성은 **AzureTableSource**로 설정해야 합니다. |예 |
| AzureTableSourceQuery |사용자 지정 Table 저장소 쿼리를 사용하여 데이터를 읽습니다. 다음 섹션의 예제를 참조하세요. |아니오 |
| azureTableSourceIgnoreTableNotFound |테이블의 예외가 존재하지 않도록 허용할지 여부를 나타냅니다.<br/>허용되는 값은 **True** 및 **False**(기본값)입니다. |아니오 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 예제

Azure Table 열이 날짜/시간 형식인 경우:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Azure Table 열이 문자열 형식인 경우:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

파이프라인 매개 변수를 사용하는 경우 이전 샘플에 따라 날짜/시간 값을 적절한 형식으로 캐스팅합니다.

### <a name="azure-table-as-a-sink-type"></a>Azure Table을 싱크 형식으로

Azure 테이블로 데이터를 복사하려면 복사 작업의 싱크 형식을 **AzureTableSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 활동 sink의 type 속성은 **AzureTableSink**로 설정해야 합니다. |예 |
| azureTableDefaultPartitionKeyValue |싱크에서 사용할 수 있는 기본 파티션 키 값입니다. |아니오 |
| azureTablePartitionKeyName |값이 파티션 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 "AzureTableDefaultPartitionKeyValue"가 파티션 키로 사용됩니다. |아니오 |
| azureTableRowKeyName |열 값이 행 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 각 행에 GUID를 사용합니다. |아니오 |
| azureTableInsertType |Azure Table에 데이터를 삽입하는 모드입니다. 이 속성은 출력 테이블에서 파티션 및 행 키가 일치하는 기존 행의 값을 바꿀지 또는 병합할지 제어합니다. <br/><br/>허용되는 값은 **merge**(기본값) 및 **replace**입니다. <br/><br> 이 설정은 테이블 수준이 아닌 행 수준에 적용됩니다. 두 옵션 모두 출력 테이블에서 입력에 존재하지 않는 행을 삭제하지 않습니다. merge 및 replace 설정이 작동하는 방법을 알아보려면 [Insert Or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx)(엔터티 삽입 또는 병합) 및 [Insert Or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx)(엔터티 삽입 또는 바꾸기)를 참조하세요. |아니요 |
| writeBatchSize |writeBatchSize 또는 writeBatchTimeout에 도달하면 Azure Table에 데이터를 삽입합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout |writeBatchSize 또는 writeBatchTimeout에 도달하면 Azure Table에 데이터를 삽입합니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:20:00"(20 분)입니다. |아니요(기본값: 90초 - 저장소 클라이언트의 기본 시간 제한) |

**예제:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

대상 열을 azureTablePartitionKeyName으로 사용할 수 있기 전에 **"translator"** 속성을 사용하여 원본 열을 대상 열에 매핑합니다.

다음 예제에서는 DivisionID 원본 열이 DivisionID 대상 열에 매핑됩니다.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID"가 파티션 키로 지정됩니다.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure 테이블에 대한 데이터 형식 매핑

Azure Table 간에 데이터를 복사하는 경우 Azure Table 데이터 형식에서 Data Factory 중간 데이터 형식으로 다음과 같은 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

Azure Table에서 데이터를 이동하는 경우 Azure Table OData 형식에서 .NET 형식으로 또는 그 반대로 다음과 같은 [Azure Table에서 정의된 매핑](https://msdn.microsoft.com/library/azure/dd179338.aspx)이 사용됩니다.

| Azure Table 데이터 형식 | 데이터 팩터리 중간 데이터 형식 | 세부 정보 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |바이트 배열은 최대 64KB입니다. |
| Edm.Boolean |bool |부울 값입니다. |
| Edm.DateTime |Datetime |UTC(협정 세계시)로 표현되는 64비트 값입니다. 지원되는 DateTime 범위는 서기 1601년 1월 1일 자정에 시작합니다. (서기), UTC입니다. 범위는 9999년 12월 31일 끝납니다. |
| Edm.Double |double |64비트 부동 소수점 값입니다. |
| Edm.Guid |Guid |전역적으로 고유한 128 비트 식별자입니다. |
| Edm.Int32 |Int32 |32비트 정수입니다. |
| Edm.Int64 |Int64 |64비트 정수입니다. |
| Edm.String |문자열 |UTF-16으로 인코딩된 값입니다. 문자열 값은 최대 64KB입니다. |

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
