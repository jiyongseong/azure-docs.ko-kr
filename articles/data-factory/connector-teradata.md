---
title: Azure Data Factory를 사용하여 Teradata에서 데이터 복사 | Microsoft Docs
description: Teradata 데이터베이스의 데이터를 데이터 팩터리에서 싱크로 지원하는 데이터 저장소로 복사할 수 있는 데이터 팩터리 서비스의 Teradata 커넥터에 대해 알아봅니다.
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
ms.openlocfilehash: 287fc502a0a538660da86e028a81fba04bcac903
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Teradata에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-onprem-teradata-connector.md)
> * [버전 2 - 미리 보기](connector-teradata.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Teradata 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Teradata 커넥터](v1/data-factory-onprem-teradata-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Teradata 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Teradata 커넥터는 다음을 지원합니다.

- Teradata **버전 12 이상**
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사

## <a name="prerequisites"></a>필수 조건

이 Teradata 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- Integration Runtime 컴퓨터에 [Teradata용 .NET Data Provider](http://go.microsoft.com/fwlink/?LinkId=278886) 버전 14 이상을 설치합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Teradata 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Teradata 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **Teradata**로 설정되어야 합니다. | 예 |
| 서버 | Teradata 서버의 이름입니다. | 예 |
| authenticationType | Teradata 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 **Basic** 및 **Windows**입니다. | 예 |
| 사용자 이름 | Teradata 데이터베이스에 연결할 사용자 이름을 지정합니다. | 예 |
| 암호 | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예제:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Teradata 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Teradata에서 데이터를 복사하려면 데이터 집합의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **RelationalTable**로 설정해야 합니다. | 예 |
| tableName | Teradata 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Teradata 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="teradata-as-source"></a>Teradata를 원본으로

Teradata에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성을 **RelationalSource**로 설정해야 합니다. | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 집합의 "tableName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Teradata에 대한 데이터 형식 매핑

Teradata에서 데이터를 복사하는 경우 Teradata 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Teradata 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |문자열 |
| Clob |문자열 |
| Date |Datetime |
| 10진수 |10진수 |
| Double |Double |
| Graphic |문자열 |
| 정수  |Int32 |
| Interval Day |timespan |
| Interval Day To Hour |timespan |
| Interval Day To Minute |timespan |
| Interval Day To Second |timespan |
| Interval Hour |timespan |
| Interval Hour To Minute |timespan |
| Interval Hour To Second |timespan |
| Interval Minute |timespan |
| Interval Minute To Second |timespan |
| Interval Month |문자열 |
| Interval Second |timespan |
| Interval Year |문자열 |
| Interval Year To Month |문자열 |
| Number |Double |
| Period(Date) |문자열 |
| Period(Time) |문자열 |
| Period(Time With Time Zone) |문자열 |
| Period(Timestamp) |문자열 |
| Period(Timestamp With Time Zone) |문자열 |
| SmallInt |Int16 |
| Time |timespan |
| Time With Time Zone |문자열 |
| 타임 스탬프 |Datetime |
| Timestamp With Time Zone |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |문자열 |
| VarGraphic |문자열 |
| xml |문자열 |


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.