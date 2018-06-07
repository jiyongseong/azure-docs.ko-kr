---
title: Azure Data Factory의 복사 작업 | Microsoft Docs
description: 지원되는 원본 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 데 사용할 수 있는 Azure Data Factory의 복사 작업에 대해 알아봅니다.
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
ms.date: 05/01/2018
ms.author: jingwang
ms.openlocfilehash: 8ae1402b6821d1b42fa8f2bf9c2f6453a5ce7109
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업

## <a name="overview"></a>개요

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-data-movement-activities.md)
> * [버전 2 - 미리 보기](copy-activity-overview.md)

Azure Data Factory에서는 복사 작업을 사용해 온-프레미스 및 클라우드 간에 데이터를 복사할 수 있습니다. 복사한 데이터는 추가로 변환하고 분석할 수 있습니다. 복사 활동을 통해 BI(비즈니스 인텔리전스) 및 응용 프로그램에서 사용할 수 있도록 변환 및 분석 결과를 게시할 수도 있습니다.

![복사 활동의 역할](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

복사 작업은 [Integration Runtime](concepts-integration-runtime.md)에서 실행됩니다. 다양한 데이터 복사 시나리오에서 Integration Runtime을 다양하게 활용할 수 있습니다.

* 공개적으로 액세스할 수 있는 두 데이터 저장소 간에 데이터를 복사할 때는 안전하고 안정적이며 확장 가능하고 [전체적으로 사용 가능한](concepts-integration-runtime.md#integration-runtime-location) **Azure Integration Runtime**이 복사 작업을 제공할 수 있습니다.
* 액세스 제어가 적용된 온-프레미스나 네트워크(예: Azure Virtual Network)에서 또는 이러한 네트워크로 데이터를 복사할 경우 **자체 호스팅 Integrated Runtime**을 사용하여 데이터 복사를 제공할 수 있습니다.

Integration Runtime을 각각의 원본 및 싱크 데이터 저장소와 연결해야 합니다. 복사 작업이 [사용할 IR을 결정하는 방법](concepts-integration-runtime.md#determining-which-ir-to-use)을 자세히 살펴봅니다.

복사 작업은 다음 단계를 통해 원본의 데이터를 싱크에 복사합니다. 복사 활동을 제공하는 서비스가 다음 작업을 수행합니다.

1. 원본 데이터 저장소에서 데이터를 읽습니다.
2. 직렬화/역직렬화, 압축/압축 해제, 열 매핑을 수행합니다. 이러한 작업은 입력 데이터 집합, 출력 데이터 집합 및 복사 활동의 구성에 따라 수행됩니다.
3. 싱크/대상 데이터 저장소에 데이터를 씁니다.

![복사 작업 개요](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>지원 파일 형식

복사 작업을 사용하여 두 파일 기반 데이터 저장소 간에 **있는 그대로 파일을 복사**할 수 있습니다. 이 때 데이터는 직렬화/역직렬화 없이 효율적으로 복사됩니다.

복사 작업은 지정된 형식의 파일에서의 읽고 쓰기도 지원됩니다. **텍스트, JSON, Avro, ORC 및 Parquet**, 압축 코덱 **GZip, Deflate, BZip2 및 ZipDeflate**가 지원됩니다. 자세한 내용은 [지원되는 파일 및 압축 형식](supported-file-formats-and-compression-codecs.md)을 참조하세요.

예를 들어 다음 복사 작업을 수행할 수 있습니다.

* 온-프레미스 SQL Server에서 데이터를 복사하여 ORC 형식으로 Azure Data Lake Store에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식으로 Azure Blob에 씁니다.
* 온-프레미스 파일 시스템에서 압축된 파일을 복사하고 압축을 푼 다음 Azure Data Lake Store에 씁니다.
* Azure Blob에서 GZip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL Database에 씁니다.

## <a name="supported-regions"></a>지원되는 지역

복사 작업을 지원하는 서비스는 [Azure Integration Runtime 위치](concepts-integration-runtime.md#integration-runtime-location)에 표시된 지역 및 지리에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. 지역별 Data Factory 및 데이터 이동 기능 사용 가능 여부는 [지역별 서비스](https://azure.microsoft.com/regions/#services) 를 참조하세요.

## <a name="configuration"></a>구성

Azure Data Factory에서 복사 작업을 사용하려면 다음이 필요합니다.

1. **원본 및 싱크 데이터 저장소에 대한 연결된 서비스를 만듭니다.** 구성 방법과 지원되는 속성은 커넥터 문서의 "연결된 서비스 속성" 섹션을 참조하세요. 지원되는 커넥터 목록은 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats) 섹션에 있습니다.
2. **원본 및 싱크에 대 한 데이터 집합을 만듭니다**. 구성 방법과 지원되는 속성은 원본 및 싱크 커넥터 문서의 "데이터 집합 속성" 섹션을 참조하세요.
3. **복사 작업을 포함하는 파이프라인을 만듭니다.** 다음 섹션에서 예제를 제공합니다.  

### <a name="syntax"></a>구문

복사 작업의 다음 템플릿은 지원되는 속성의 전체 목록을 포함합니다. 시나리오에 적합한 속성을 지정하세요.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>구문 세부 정보

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업의 type 속성은 **Copy**로 설정해야 합니다. | 예 |
| inputs | 원본 데이터를 가리키는 만든 데이터 집합을 지정합니다. 복사 작업에서는 하나의 입력만 지원합니다. | 예 |
| outputs | 싱크 데이터를 가리키는 만든 데이터 집합을 지정합니다. 복사 작업에서는 하나의 출력만 지원합니다. | 예 |
| typeProperties | 복사 작업을 구성하는 속성의 그룹입니다. | 예 |
| 원본 | 데이터 검색 방법에 대한 해당 속성과 복사 원본 유형을 지정합니다.<br/><br/>자세한 내용은 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 열거된 커넥터 문서의 "복사 작업 속성" 섹션을 참조하세요. | 예 |
| 싱크 | 데이터 쓰기 방법에 대한 해당 속성과 복사 싱크 유형을 지정합니다.<br/><br/>자세한 내용은 [지원되는 데이터 저장소 및 형식](#supported-data-stores-and-formats)에 열거된 커넥터 문서의 "복사 작업 속성" 섹션을 참조하세요. | 예 |
| 번역기 | 원본에서 싱크로의 명시적 열 매핑을 지정합니다. 기본 복사 동작이 요구를 수행할 수 없는 경우 적용됩니다.<br/><br/>자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요. | 아니오 |
| cloudDataMovementUnits | 데이터 복사를 수행할 [Azure Integration Runtime](concepts-integration-runtime.md)의 강도를 지정합니다.<br/><br/>자세한 내용은 [클라우드 데이터 이동 단위](copy-activity-performance.md)를 참조하세요. | 아니오 |
| parallelCopies | 원본에서 데이터를 읽어 싱크에 쓸 때 복사 작업이 사용할 병렬 처리를 지정합니다.<br/><br/>자세한 내용은 [병렬 복사](copy-activity-performance.md#parallel-copy)를 참조하세요. | 아니오 |
| enableStaging<br/>stagingSettings | 데이터를 직접 원본에서 싱크로 복사하는 대신 aa BLOB Storage에서 중간 데이터를 준비하도록 선택합니다.<br/><br/>유용한 시나리오 및 구성 상세 정보는 [준비된 복사](copy-activity-performance.md#staged-copy)를 참조하세요. | 아니오 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 데이터를 원본에서 싱크로 복사할 때 호환되지 않는 행을 처리하는 방법을 선택합니다.<br/><br/>자세한 내용은 [내결함성](copy-activity-fault-tolerance.md)을 참조하세요. | 아니오 |

## <a name="monitoring"></a>모니터링

Azure Data Factory "작성자/모니터" UI에서 또는 프로그래밍 방식으로 복사 작업 실행을 모니터링할 수 있습니다. 그런 후 시나리오의 성능과 구성을 사내 테스트에서 얻은 복사 작업의 [성능 참조](copy-activity-performance.md#performance-reference)와 비교할 수 있습니다.

### <a name="monitor-visually"></a>시각적으로 모니터링

복사 작업 실행을 시각적으로 모니터링하려면 데이터 팩터리 -> **작성자 및 모니터** -> **모니터 탭**으로 이동합니다. 그러면  **작업** 열에 "작업 실행 보기"링크가 있는 파이프라인 실행 목록이 표시됩니다. 

![파이프라인 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

이 파이프라인 실행에서 작업 목록을 보려면 클릭합니다. **동작** 열에는 복사 작업 입력, 출력, 오류(복사 작업 실행이 실패한 경우) 및 세부 정보에 대한 링크가 포함되어 있습니다.

![작업 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

**동작** 아래의 "**세부 정보**" 링크를 클릭하여 복사 작업의 실행 세부 정보 및 성능 특성을 볼 수 있습니다. 원본에서 복사된 데이터 볼륨/행/파일부터, 싱크, 처리량 및 거쳐 지나가는 단계를 포함하는 정보와 복사 시나리오의 해당 기간 및 사용된 구성이 표시됩니다.

**예: Amazon S3에서 Azure Data Lake Store로 복사**
![작업 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**예: 단계적 복사를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse로 복사**
![작업 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>프로그래밍 방식으로 모니터링

복사 작업 실행 세부 정보와 성능 특징은 복사 작업 실행 결과 -> 출력 섹션에도 반환됩니다. 다음은 전체 목록입니다. 해당 복사 시나리오에 해당되는 항목만 표시됩니다. [퀵 스타트 모니터링 섹션](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)에서 작업 실행을 모니터링하는 방법을 알아봅니다.

| 속성 이름  | 설명 | 단위 |
|:--- |:--- |:--- |
| DataRead | 원본에서 읽은 데이터 크기 | Int64 값(**바이트**) |
| DataWritten | 싱크에 쓴 데이터 크기 | Int64 값(**바이트**) |
| filesRead | 파일 저장소에서 데이터를 복사할 경우 복사되는 파일 수입니다. | Int64 값(단위 없음) |
| filesWritten | 파일 저장소로 데이터를 복사할 경우 복사되는 파일 수입니다. | Int64 값(단위 없음) |
| rowsCopied | 복사된 행 수(이진 복사에 적용되지 않음) | Int64 값(단위 없음) |
| rowsSkipped | 생략되는 비호환 행 수. "enableSkipIncompatibleRow"를 true로 설정하여 이 기능을 실행할 수 있습니다. | Int64 값(단위 없음) |
| throughput | 데이터 전송 비율 | 부동 소수점 숫자(**KB/s**) |
| copyDuration | 복사본의 기간 | Int32 값(초) |
| sqlDwPolyBase | 데이터를 SQL Data Warehouse에 복사할 때 PolyBase를 사용합니다. | BOOLEAN |
| redshiftUnload | 데이터를 Redshift로부터 복사할 때 UNLOAD를 사용합니다. | BOOLEAN |
| hdfsDistcp | 데이터를 HDFS로부터 복사할 때 DistCp를 사용합니다. | BOOLEAN |
| effectiveIntegrationRuntime | 활동 실행을 제공하는 데 사용할 Integration Runtime을 `<IR name> (<region if it's Azure IR>)` 형식으로 표시합니다. | 텍스트(문자열) |
| usedCloudDataMovementUnits | 복사 중 유효 클라우드 데이터 이동 단위입니다. | Int32 값 |
| usedParallelCopies | 복사 동안 유효한 parallelCopies입니다. | Int32 값|
| redirectRowPath | "RedirectIncompatibleRowSettings"에서 구성한 Blob Storage에서 생략된 비호환 행의 로그에 대한 경로입니다. 아래 예제를 참조하십시오. | 텍스트(문자열) |
| executionDetails | 복사 작업이 거치는 단계 및 해당 하위 단계, 기간 및 사용되는 구성 등에 대한 세부 정보입니다. 변경 될 수 있으므로 이 섹션의 구문 분석은 권장되지 않습니다. | 배열 |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>스키마 및 데이터 형식 매핑

복사 작업이 원본 데이터를 싱크에 매핑하는 방법은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

## <a name="fault-tolerance"></a>내결함성

기본적으로 복사 작업에서 원본과 싱크 간에 비호환 데이터를 발견하면 데이터 복사를 멈추고 실패를 반환합니다. 복사가 성공하도록 비호환 행은 건너 뛰고 로그로 기록하고, 호환되는 데이터만 복사하도록 명시적으로 구성할 수 있습니다. 자세한 내용은 [복사 활동 내결함성](copy-activity-fault-tolerance.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

Azure Data Factory의 데이터 이동(복사 활동) 성능에 영향을 주는 주요 요인에 대해 설명하는 [복사 작업 성능 및 튜닝 가이드](copy-activity-performance.md)를 참조하세요. 이 문서에서는 내부 테스트 중에 관찰되는 성능 관련 정보도 제공하며, 복사 활동의 성능을 최적화하는 여러 가지 방법에 대해서도 설명합니다.

## <a name="incremental-copy"></a>증분 복사 
Data Factory 버전 2에서는 원본 데이터 저장소에서 대상 데이터 저장소로 델타 데이터를 증분 방식으로 복사하기 위한 시나리오를 지원합니다. [자습서: 증분 방식으로 데이터 복사](tutorial-incremental-copy-overview.md)를 참조하세요. 

## <a name="read-and-write-partitioned-data"></a>분할된 데이터 읽기 및 쓰기
버전 1에서 Azure Data Factory는 SliceStart/SliceEnd/WindowStart/WindowEnd 시스템 변수를 사용하여 분할된 데이터 읽기 또는 쓰기를 지원했습니다. 버전 2에서는 파이프라인 매개 변수와 트리거의 시작 시간/예약된 시간을 매개 변수의 값으로 사용하여 이 동작을 수행할 수 있습니다. 자세한 내용은 [분할된 데이터를 읽거나 쓰는 방법](how-to-read-write-partitioned-data.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 퀵 스타트, 자습서 및 샘플을 참조하세요.

- [한 위치의 데이터를 동일한 Azure Blob Storage의 다른 위치에 복사](quickstart-create-data-factory-dot-net.md)
- [Azure Blob Storage에서 Azure SQL Database로 데이터 복사](tutorial-copy-data-dot-net.md)
- [온-프레미스 SQL Server에서 Azure로 데이터 복사](tutorial-hybrid-copy-powershell.md)
