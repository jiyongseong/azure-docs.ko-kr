---
title: Azure Data Factory를 사용하여 PostgreSQL에서 데이터 이동 | Microsoft Docs
description: Azure 데이터 팩터리를 사용하여 PostgreSQL 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ee466c85b68ebc72dbd55849db84a473d584ffb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 PostgreSQL에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](data-factory-onprem-postgresql-connector.md)
> * [버전 2 - 미리 보기](../connector-postgresql.md)

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기 상태인 Data Factory 버전 2 서비스를 사용 중인 경우 [V2의 PostgreSQL 커넥터](../connector-postgresql.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 PostgreSQL 데이터베이스에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

온-프레미스 PostgreSQL 데이터 저장소의 데이터를 지원되는 싱크 데이터 저장소로 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 현재 데이터 팩터리는 다른 데이터 저장소에서 PostgreSQL 데이터베이스로 데이터 이동이 아닌 PostgreSQL 데이터베이스에서 다른 데이터 저장소로 데이터 이동만을 지원합니다. 

## <a name="prerequisites"></a>필수 조건

데이터 팩터리 서비스는 데이터 관리 게이트웨이를 사용하여 온-프레미스 PostgreSQL 원본에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

게이트웨이는 PostgreSQL 데이터베이스가 Azure IaaS VM에 호스팅되더라도 필요합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한 데이터 저장소와 동일한 IaaS VM 또는 다른 VM에 게이트웨이를 설치할 수 있습니다.

> [!NOTE]
> 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 을 참조하세요.

## <a name="supported-versions-and-installation"></a>지원되는 버전 및 설치
PostgreSQL 데이터베이스에 연결할 데이터 관리 게이트웨이의 경우 데이터 관리 게이트웨이와 동일한 시스템에 [Ngpsql data provider for PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) 2.0.12 ~ 3.1.9 버전을 설치합니다. PostgreSQL 버전 7.4 이상이 지원됩니다.

## <a name="getting-started"></a>시작
여러 도구/API를 사용하여 온-프레미스 PostgreSQL 데이터 저장소의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다. 

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 
- 또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. 
    - Azure 포털
    - Visual Studio
    - Azure PowerShell
    - Azure Resource Manager 템플릿
    - .NET API
    - REST API

     복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 집합**을 만듭니다. 
3. 입력으로 데이터 집합을, 출력으로 데이터 집합을 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다. 

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API를 사용하는 경우(.NET API 제외) JSON 형식을 사용하여 데이터 팩터리 엔터티를 직접 정의합니다.  온-프레미스 PostgreSQL의 데이터를 복사하는 데 사용되는 데이터 팩터리 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: PostgreSQL에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-postgresql-to-azure-blob) 섹션을 참조하세요. 

다음 섹션에서는 PostgreSQL 데이터 저장소에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 표에서는 PostgreSQL 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 |형식 속성은 **OnPremisesPostgreSql** |예 |
| 서버 |PostgreSQL 서버의 이름입니다. |예 |
| 데이터베이스 |PostgreSQL 데이터베이스의 이름입니다. |예 |
| schema |데이터베이스에서 스키마의 이름입니다. schema 이름은 대/소문자를 구분합니다. |아니오 |
| authenticationType |PostgreSQL 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. |아니요 |
| 암호 |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |아니오 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 PostgreSQL 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

## <a name="dataset-properties"></a>데이터 집합 속성
데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식의 데이터 집합(PostgreSQL 데이터 집합을 포함)에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 PostgreSQL 데이터베이스 인스턴스에서 테이블의 이름입니다. tableName은 대/소문자를 구분합니다. |아니요(**RelationalSource**의 **쿼리**가 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **RelationalSource**(PostgreSQL 포함) 형식인 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `"query": "select * from \"MySchema\".\"MyTable\""` |아니요(**데이터 집합**의 **tableName**이 지정된 경우) |

> [!NOTE]
> 스키마 및 테이블 이름은 대/소문자를 구분합니다. 쿼리에서 `""`(큰따옴표)로 묶습니다.  

**예제:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON 예: PostgreSQL에서 Azure Blob으로 데이터 복사
다음 예제에서는 [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 PostgreSQL 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 에 설명한 싱크로 데이터를 복사할 수 있습니다.   

> [!IMPORTANT]
> 이 샘플은 JSON 코드 조각을 제공합니다. 데이터 팩터리를 만들기 위한 단계별 지침은 포함하지 않습니다. 단계별 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties)형식의 연결된 서비스
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 PostgreSQL 데이터베이스의 쿼리 결과에서 blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**PostgreSQL 연결된 서비스:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Azure Blob 저장소 연결된 서비스:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**PostgreSQL 입력 데이터 집합:**

샘플은 PostgreSQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestamp"라는 열이 포함되어 있다고 가정합니다.

`"external": true` 설정을 사용하는 경우 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 정보가 Data Factory 서비스에 전달됩니다.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 PostgreSQL 데이터베이스의 public.usstates 테이블에서 데이터를 선택합니다.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>PostgreSQL에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

PostgreSQL로 데이터를 이동하는 경우 PostgreSQL 형식에서 .NET 형식으로 이동에 다음 매핑이 사용됩니다.

| PostgreSQL 데이터베이스 형식 | PostgresSQL 별칭 | .NET Framework 형식 |
| --- | --- | --- |
| abstime | |DateTime | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte[], String | &nbsp;
| bit varying [ (n) ] |varbit |Byte[], String |
| 부울 |bool |BOOLEAN |
| box | |Byte[], String |&nbsp;
| bytea | |Byte[], String |&nbsp;
| character [(n)] |char [(n)] |문자열 |
| character varying [(n)] |varchar [(n)] |문자열 |
| cid | |문자열 |&nbsp;
| cidr | |문자열 |&nbsp;
| circle | |Byte[], String |&nbsp;
| date | |DateTime |&nbsp;
| daterange | |문자열 |&nbsp;
| double precision |float8 |Double |
| inet | |Byte[], String |&nbsp;
| intarry | |문자열 |&nbsp;
| int4range | |문자열 |&nbsp;
| int8range | |문자열 |&nbsp;
| 정수 |int, int4 |Int32 |
| interval [fields] [(p)] | |Timespan |&nbsp;
| json : | |문자열 |&nbsp;
| jsonb | |Byte[] |&nbsp;
| line | |Byte[], String |&nbsp;
| lseg | |Byte[], String |&nbsp;
| macaddr | |Byte[], String |&nbsp;
| money | |10진수 |&nbsp;
| numeric [(p, s)] |decimal [(p, s)] |10진수 |
| numrange | |문자열 |&nbsp;
| oid | |Int32 |&nbsp;
| 경로 | |Byte[], String |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| point | |Byte[], String |&nbsp;
| polygon | |Byte[], String |&nbsp;
| real |float4 |단일 |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serial |serial4 |Int32 |
| text | |문자열 |&nbsp;

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 집합의 열을 싱크 데이터 집합의 열로 매핑하는 방법은 [Azure Data Factory의 데이터 집합 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 집합에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
