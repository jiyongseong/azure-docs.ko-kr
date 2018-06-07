---
title: 복사 작업의 스키마 매핑 | Microsoft 문서
description: Azure Data Factory의 복사 작업에서 데이터를 복사할 때 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법에 대해 알아봅니다.
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
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: 22dbaa3d10d09456a972a7991583dc29b7941db1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="schema-mapping-in-copy-activity"></a>복사 작업의 스키마 매핑
이 문서에서는 Azure Data Factory 복사 작업에서 데이터 복사를 수행할 때 원본 데이터의 스키마 매핑과 데이터 형식을 싱크 데이터에 매핑하는 방법을 설명합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.


## <a name="column-mapping"></a>열 매핑

기본적으로 복사 작업은 [명시적 열 매핑](#explicit-column-mapping)을 구성하지 않은 한 **열 이름을 기준으로 원본 데이터를 싱크에 매핑합니다**. 보다 구체적으로 복사 작업:

1. 원본에서 데이터를 읽고 원본 스키마를 결정합니다.

    * 메타데이터가 있는 데이터베이스/파일(Avro/ORC/Parquet/헤더 있는 텍스트)처럼 데이터 저장소/파일 형식으로 미리 정의된 데이터 원본의 경우, 원본 스키마는 쿼리 결과나 파일 메타데이터에서 추출됩니다.
    * Azure Table/Cosmos DB처럼 유연한 스키마가 있는 데이터 원본의 경우 원본 스키마는 쿼리 결과에서 유추됩니다. 데이터 집합에서 "structure"를 제공하여 이를 덮어쓸 수 있습니다.
    * 헤더 없는 텍스트 파일의 경우 기본 열 이름은 "Prop_0", "Prop_1" 패턴으로 생성되며 데이터 집합에서 "structure"를 제공하여 이를 덮어쓸 수 있습니다.
    * Dynamics 원본의 경우 데이터 집합 “structure” 섹션에서 스키마 정보를 제공해야 합니다.

2. 지정된 경우 명시적 열 매핑을 적용합니다.

3. 싱크에 데이터 쓰기

    * 미리 정의된 스키마가 있는 데이터 저장소의 경우 같은 이름을 갖는 열에 데이터를 씁니다.
    * 고정된 스키마 없는 데이터 저장소 및 파일 형식의 경우 열 이름/메타데이터를 원본 스키마를 기준으로 생성합니다.

### <a name="explicit-column-mapping"></a>명시적 열 매핑

복사 작업의 **typeProperties** 섹션에 **columnMappings**를 지정하여 명시적 열 매핑을 수행할 수 있습니다. 이 시나리오에서는 "structure" 섹션이 입력과 출력 데이터 집합 모두에 필요합니다. 열 매핑은 **원본 데이터 집합 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 집합 "structure"**의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

* 원본 데이터 저장소 쿼리 결과에 입력 데이터 집합 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 집합 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 매핑에서 지정한 것보다 싱크 데이터 집합의 "structure"에 열이 더 많거나 적습니다.
* 중복 매핑

#### <a name="explicit-column-mapping-example"></a>명시적 열 매핑 예

이 예제에서 입력 테이블에는 구조가 있고 그 구조가 온-프레미스 SQL 데이터베이스에 있는 테이블을 가리킵니다.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 예제에서 출력 테이블에는 구조가 있고 그 구조가 Azure SQL Database에 있는 테이블을 가리킵니다.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. 원본의 열과 싱크(**columnMappings**)의 열 간 매핑은 **Translator** 속성을 사용하여 이루어집니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**열 매핑 흐름:**

![열 매핑 흐름 ](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>데이터 형식 매핑

복사 작업에서는 다음 2단계 방식으로 원본 형식을 싱크 형식에 매핑합니다.

1. 원시 원본 형식을 Azure Data Factory 중간 데이터 형식으로 변환
2. Azure Data Factory 중간 데이터 형식을 원시 싱크 형식으로 변환

각 커넥터 항목의 "데이터 형식 매핑" 섹션에서 원시 형식과 중간 형식 간의 매핑을 확인할 수 있습니다.

### <a name="supported-data-types"></a>지원되는 데이터 원본

데이터 팩터리는 다음 중간 데이터 형식을 지원합니다. [데이터 집합 구조](concepts-datasets-linked-services.md#dataset-structure) 구성에서 형식 정보를 입력할 때 아래 값을 지정할 수 있습니다.

* Byte[]
* BOOLEAN
* DateTime
* Datetimeoffset
* 10진수
* Double
* Guid
* Int16
* Int32
* Int64
* 단일
* 문자열
* Timespan

### <a name="explicit-data-type-conversion"></a>명시적 데이터 형식 변환

데이터를 고정 스키마가 있는 데이터 저장소에 복사할 때(예: SQL Server/Oracle) 동ㅇ리한 열의 원본과 싱크 형식이 다르면 명시적 형식 변환을 원본 측에서 정의해야 합니다.

* CSV/Avro 등의 파일 원본의 경우 전체 열 목록이 있는 원본 구조를 통해 형식 변환을 선언해야 합니다(원본 측 열 이름 및 싱크 측 형식).
* 관계형 원본(예: SQL/Oracle)의 경우 형식 변환은 쿼리 문에서의 명시적 형식 캐스팅을 통해 이루어져야 합니다.

## <a name="when-to-specify-dataset-structure"></a>데이터 집합 "structure"를 지정하는 경우

아래 시나리오에서 데이터 집합의 “structure”가 필요합니다.

* 복사 중 파일 원본에 대해 [명시적 데이터 형식 변환](#explicit-data-type-conversion) 적용(입력 데이터 집합)
* 복사 중 [명시적 열 매핑](#explicit-column-mapping) 적용(입력 및 출력 데이터 집합 모두)
* Dynamics 365/CRM 원본에서 복사(입력 데이터 집합)
* 원본이 JSON 파일이 아닐 때 Cosmos DB를 중첩 개체에 복사(출력 데이터 집합)

아래 시나리오에서는 데이터 집합의 “structure”가 필요합니다.

* 헤더 없는 텍스트 파일에서 복사(입력 데이터 집합). 해당하는 싱크 열과 일치하는 텍스트 파일의 열 이름을 지정하여 명시적 열 매핑으로부터 저장할 수 있습니다.
* Azure Table/Cosmos DB 등, 유연한 스키마가 있는 데이터 저장소에서 복사하면(입력 데이터 집합) 각각의 작업 실행 중에 맨 위 행을 기준으로 복사 작업이 스키마를 유추하는 것이 아니라 원하는 데이터(열)가 복사되게 보장할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)
- [복사 작업 성능](copy-activity-performance.md)
