---
title: Azure Data Factory를 사용하여 SAP HANA에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 SAP HANA에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 96d9da90114ae392c3a9e1c22d8e993fd787ada8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP HANA에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-sap-hana-connector.md)
> * [버전 2 - 미리 보기](connector-sap-hana.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP HANA 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용하는 경우 [V1의 SAP HANA 커넥터](v1/data-factory-sap-hana-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

SAP HANA 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 SAP HANA 커넥터는 다음을 지원합니다.

- SAP HANA 데이터베이스의 모든 버전에서 데이터 복사
- SQL 쿼리를 사용한 **행/열 테이블** 및 **HANA 정보 모델**(예: 분석 및 계산 보기)에서 데이터 복사
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사

> [!NOTE]
> SAP HANA 데이터 저장소**로** 데이터를 복사하려면 일반 ODBC 커넥터를 사용합니다. 자세한 내용은 [SAP HANA 싱크](connector-odbc.md#sap-hana-sink)를 참조하세요. 따라서 형식이 다른 SAP HANA 커넥터 및 ODBC 커넥터에 대한 연결된 서비스는 재사용할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

이 SAP HANA 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- Integration Runtime 컴퓨터에 SAP HANA ODBC 드라이버를 설치합니다. SAP HANA ODBC 드라이버는 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 다운로드할 수 있습니다. **SAP HANA CLIENT for Windows**라는 키워드를 사용하여 검색합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP HANA 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP HANA 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **SapHana**로 설정해야 합니다. | 예 |
| 서버 | SAP HANA 인스턴스가 상주하는 서버의 이름. 서버에서 사용자 지정된 포트를 사용하는 경우 `server:port`를 지정합니다. | 예 |
| authenticationType | SAP HANA 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 **Basic** 및 **Windows**입니다. | 예 |
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| 암호 | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예제:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에는 SAP HANA 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

SAP HANA에서 데이터를 복사하려면 데이터 집합의 형식 속성을 **RelationalTable**로 설정합니다. RelationalTable 형식의 SAP HANA 데이터 집합에 대해 지원되는 형식별 속성은 없습니다.

**예제:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-hana-as-source"></a>SAP HANA를 원본으로

SAP HANA에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성을 **RelationalSource**로 설정해야 합니다. | 예 |
| 쿼리 | SAP HANA 인스턴스에서 데이터를 읽을 SQL 쿼리를 지정합니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA에 대한 데이터 형식 매핑

SAP HANA에서 데이터를 복사하는 경우 SAP HANA 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP HANA 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| ALPHANUM | 문자열 |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | Datetime |
| DECIMAL | 10진수 |
| DOUBLE | 단일 |
| INT | Int32 |
| NVARCHAR | 문자열 |
| Real | 단일 |
| SECONDDATE | Datetime |
| SmallInt | Int16 |
| TIME | timespan |
| TIMESTAMP | Datetime |
| TINYINT | Byte |
| VARCHAR | 문자열 |

## <a name="known-limitations"></a>알려진 제한 사항

SAP HANA에서 데이터를 복사하는 경우 몇 가지 알려진 제한 사항이 있습니다.

- NVARCHAR 문자열은 유니코드 문자 최대 길이 4000자에서 잘립니다.
- SMALLDECIMAL은 지원되지 않습니다.
- VARBINARY는 지원되지 않습니다.
- 유효한 날짜는 1899/12/30 ~ 9999/12/31입니다.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.