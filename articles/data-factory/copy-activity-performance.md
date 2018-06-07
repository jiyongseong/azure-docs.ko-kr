---
title: Azure Data Factory의 복사 작업 성능 및 조정 가이드 | Microsoft Docs
description: 복사 작업을 사용할 때 Azure Data Factory의 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 알아봅니다.
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
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: c43973a7e5070676fc0f32a4c8923d57a479f884
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>복사 작업 성능 및 조정 가이드
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-activity-performance.md)
> * [버전 2 - 미리 보기](copy-activity-performance.md)


Azure Data Factory 복사 작업은 최고 수준의 보안, 안정성 및 고성능 데이터 로드 솔루션을 제공합니다. 풍부하게 다양한 클라우드 및 온-프레미스 데이터 저장소에서 매일 수십 테라바이트의 데이터를 복사할 수 있습니다. 초고속 데이터 로드 성능은 핵심적인 "빅 데이터" 문제인 고급 분석 솔루션을 구축하고 모든 데이터에서 깊은 통찰을 얻는 데 집중할 수 있도록 하는 열쇠입니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1의 복사 작업 성능](v1/data-factory-copy-activity-performance.md)을 참조하세요.

Azure는 엔터프라이즈급 데이터 저장소 및 데이터 웨어하우스 솔루션 세트를 제공하고 복사 작업은 쉽게 구성 및 설정할 수 있는 고도로 최적화된 데이터 로드 환경을 제공합니다. 단일 복사 작업 만을 사용하여 다음을 수행할 수 있습니다.

* **1.2Gbps** 속도로 **Azure SQL Data Warehouse**에 데이터 로드 -
* **1.0 Gbps** 속도로 **Azure Blob 저장소**에 데이터 로드
* **1.0 Gbps** 속도로 **Azure Data Lake Store**에 데이터 로드

이 문서에서는 다음을 설명합니다.

* [성능 참조 번호](#performance-reference)와 프로젝트를 계획하는 데 도움이 되는 싱크 데이터 저장소.
* [클라우드 데이터 이동 단위](#cloud-data-movement-units), [병렬 복사](#parallel-copy), [준비된 복사](#staged-copy)를 포함한 다양한 시나리오에서 복사 처리량을 높일 수 있는 기능.
* [성능 조정 지침](#performance-tuning-steps) .

> [!NOTE]
> 복사 작업에 대해 전반적으로 잘 알지 못하는 경우, 이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md)를 참조하세요.
>

## <a name="performance-reference"></a>성능 참조

참조로 아래 테이블에 사내 테스트에 따른 **단일 복사 작업 실행**에서 지정된 원본 및 싱크 쌍에 대한 복사 처리량(**MBps**)이 나와 있습니다. 비교를 위해 [클라우드 데이터 이동 단위](#cloud-data-movement-units) 또는 [자체 호스팅 Integration Runtime 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime)(여러 노드)의 다른 설정이 어떻게 복사 성능에 도움이 되는지도 설명합니다.

![성능 매트릭스](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory 버전 2에서 복사 작업이 Azure Integration Runtime에서 실행될 때 최소 클라우드 데이터 이동 단위는 2입니다. 지정되지 않은 경우 [클라우드 데이터 이동 단위](#cloud-data-movement-units)에서 사용 중인 기본 데이터 이동 단위를 참조하세요.

주의할 사항:

* 처리량은 다음 수식을 사용하여 계산됩니다. [원본에서 읽은 데이터의 크기]/[복사 작업 실행 기간]
* 테이블의 성능 참조 번호는 [TPC-H](http://www.tpc.org/tpch/) 데이터 집합을 사용하여 단일 복사 작업 실행을 통해 측정된 것입니다.
* Azure 데이터 저장소에서는 원본 및 싱크는 동일한 Azure 지역에 있습니다.
* 온-프레미스와 클라우드 데이터 저장소 간 하이브리드 복사의 경우 각 자체 호스팅 Integration Runtime 노드는 아래 사양을 사용하는 데이터 저장소에서 분리된 컴퓨터에서 실행되었습니다. 단일 작업이 실행 중인 경우 복사 작업은 테스트 컴퓨터의 CPU, 메모리 또는 네트워크 대역폭의 작은 부분만 사용합니다.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 코어 2.20GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>메모리</td>
        <td>128GB</td>
    </tr>
    <tr>
        <td>네트워크</td>
        <td>인터넷 인터페이스: 10Gbps. 인트라넷 인터페이스: 40Gbps</td>
    </tr>
    </table>


> [!TIP]
> 허용되는 기본 최대 DMU(데이터 이동 단위)보다 많은 DMU를 활용하여 더 많은 처리량을 획득할 수 있으며, 클라우드 간 복사 작업 실행의 경우 32입니다. 예를 들어 100개 DMU를 사용하면 **1.0GBps** 속도로 Azure Blob에서 Azure Data Lake Store로 데이터 복사를 수행할 수 있습니다. 이 기능과 지원되는 시나리오에 대한 자세한 내용은 [클라우드 데이터 이동 단위](#cloud-data-movement-units) 섹션을 참조하세요. DMU를 더 많이 요청하려면 [Azure 지원](https://azure.microsoft.com/support/)에 문의하세요.

## <a name="cloud-data-movement-units"></a>클라우드 데이터 이동 단위

**클라우드 데이터 이동 단위(DMU)** 는 Data Factory 내 단일 단위의 힘(CPU, 메모리, 네트워크 자원 할당의 조합)을 나타내는 척도입니다. **DMU는 [자체 호스팅 Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)이 아닌 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)**에만 적용됩니다.

**복사 작업 실행을 위한 최소 클라우드 데이터 이동 단위는 2입니다.** 지정하지 않은 경우 다음 표의 다양한 복사 시나리오에서 사용되는 기본 DMU의 목록을 참조하세요.

| 복사 시나리오 | 서비스에 따라 결정되는 기본 DMU |
|:--- |:--- |
| 파일 기반 저장소 간의 데이터 복사 | 파일 수와 크기에 따라 4~32 |
| 그 밖의 모든 복사 시나리오 | 4 |

기본값을 재정의하려면 **cloudDataMovementUnits** 속성에 대한 값을 지정합니다. **cloudDataMovementUnits** 속성에 **허용되는 값**은 **256까지**입니다. 런타임 시 복사 작업에서 사용하는 **실제 클라우드 DMU 수**는 데이터 패턴에 따라 구성된 값 이하입니다. 특정 복사 원본 및 싱크에 대해 더 많은 단위를 구성할 때 얻을 수 있는 성능상 이점 수준에 대한 자세한 내용은 [성능 참조](#performance-reference)를 참조하세요.

작업 실행을 모니터링할 때 복사 작업 출력에서 각 복사 실행에 대해 실질적으로 사용되는 클라우드 데이터 이동 단위를 확인할 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-overview.md#monitoring)을 참조하세요.

> [!NOTE]
> 더 높은 처리량을 위해 더 많은 클라우드 DMU가 필요하면 [Azure 지원](https://azure.microsoft.com/support/)에 문의하시기 바랍니다. 8 이상의 설정은 현재 **Blob Storage/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP에서 여러 파일을 다른 클라우드 데이터 저장소로 복사**하는 경우에만 작동합니다.
>

**예제:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>클라우드 데이터 이동 단위 청구 요소 

복사 작업의 총 시간을 기준으로 요금이 청구된다는 점을 기억하는 것이 **중요**합니다. 데이터 이동에 대해 청구되는 총 기간은 전체 DMU의 기간 합계입니다. 두 클라우드 단위로 1시간이 걸렸던 복사 작업이 이제 8개의 클라우드에서 15분이 소요된다면 전체 청구 금액은 거의 동일한 상태로 유지됩니다.

## <a name="parallel-copy"></a>병렬 복사 

**parallelCopies** 속성을 사용하여 복사 작업에 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성을 병렬로 원본에서 읽어오거나 싱크 데이터 저장소에 쓰는 복사 작업 내 최대 스레드 수라고 생각할 수 있습니다.

각각의 복사 작업 실행에 대해, Data Factory는 원본 데이터 저장소의 데이터를 대상 데이터 저장소에 복사하는 데 사용할 병렬 복사의 수를 결정합니다. 사용되는 병렬 복사의 기본 수는 사용 중인 원본 및 싱크의 유형에 따라 달라집니다.

| 복사 시나리오 | 서비스에 의해 결정되는 기본 병렬 복사 개수 |
| --- | --- |
| 파일 기반 저장소 간의 데이터 복사 |파일 크기 및 클라우드 데이터 이동 단위의 수(DMU)에 따라, 두 클라우드 데이터 저장소 간에 데이터를 복사하거나, 자체 호스팅 Integration Runtime 컴퓨터의 물리적 구성에 사용됩니다. |
| 원본 데이터 저장소의 데이터를 Azure Table Storage에 복사 |4 |
| 그 밖의 모든 복사 시나리오 |1 |

[!TIP]
> 파일 기반 저장소 간에 데이터를 복사할 때 기본 동작(자동 결정됨)은 대개 최상의 처리량을 허용합니다. 

데이터 저장소를 호스트하는 컴퓨터에서 로드를 제어하거나 복사 성능을 조정하기 위해 기본 값을 재정의하고 **parallelCopies** 속성의 값을 지정하도록 선택할 수 있습니다. 값은 1 이상의 정수여야 합니다. 런타임 시, 최고의 성능을 위해 복사 작업은 설정된 값 이하의 값을 사용합니다.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

주의할 사항:

* 파일 기반 저장소간에 데이터를 복사하면 **parallelCopies**에서 파일 수준 병렬 처리를 결정합니다. 단일 파일에서 수행되는 청크는 자동으로 투명하게 발생하며, 주어진 원본 데이터 저장소 유형에 가장 적합한 청크 크기를 사용하여 parallelCopies에 대해 병렬 및 직각으로 데이터를 로드하도록 설계되었습니다. 런타임 시 데이터 이동 서비스에서 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 파일의 수를 넘지 않습니다. 복사 동작이 **mergeFile**인 경우 복사 작업은 파일 수준 병렬 처리의 이점을 활용할 수 없습니다.
* **parallelCopies** 속성 값을 지정할 때는 복사 작업을 하이브리드 복사 등에서 제공하는 경우 원본과 싱크 데이터 저장소 및 자체 호스팅 Integration Runtime의 로드 증가를 고려합니다. 동일한 데이터 저장소에 대해 실행되는 여러 활동이 있거나 동일한 활동의 동시 실행이 있는 경우 특히 발생합니다. 데이터 저장소나 자체 호스팅 Integration Runtime이 로드로 인해 과부하가 걸리면 로드 감소를 위해 **parallelCopies** 값을 낮춥니다.
* 파일 기반이 아닌 저장소에서 파일 기반인 저장소로 데이터를 이동할 경우 데이터 이동 서비스는 **parallelCopies** 속성을 무시합니다. 병렬 처리를 지정하더라도 이 경우에는 적용되지 않습니다.
* **parallelCopies**는 **cloudDataMovementUnits**와 직교되는 관계입니다. 전자는 모든 클라우드 데이터 이동 단위에서 계산됩니다.

## <a name="staged-copy"></a>준비된 복사

원본 데이터 저장소에서 싱크 데이터 저장소에 데이터를 복사할 경우 중간 준비 저장소로 Blob 저장소를 사용하도록 선택할 수 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양한 데이터 저장소에서 SQL Data Warehouse에 데이터를 수집하고자 합니다.** SQL Data Warehouse는 많은 양의 데이터를 SQL Data Warehouse에 로드하는 처리량이 높은 메커니즘인 PolyBase를 사용합니다. 단, 원본 데이터가 Blob Storage 또는 Azure Data Lake Store에 있어야 하고 추가 조건을 충족해야 합니다. Blob Storage 또는 Azure Data Lake Store가 아닌 데이터 저장소에서 데이터를 로드하는 경우 중간 준비 Blob Storage를 통해 데이터 복사를 활성화할 수 있습니다. 이 경우 Data Factory는 PolyBase의 요구 사항을 충족하는지 확인하기 위해 필요한 데이터 변환을 수행합니다. 그런 다음 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 효과적으로 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
- **때로는 느린 네트워크 연결을 통해 하이브리드 데이터 이동(즉, 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사하려면)을 수행하는 데 오랜 시간이 걸립니다.** 성능 향상을 위해 준비된 복사를 사용하여 데이터를 온-프레미스에서 압축하여 데이터를 클라우드의 준비 데이터 저장소로 이동한 다음 대상 데이터 저장소에 로드하기 전에 준비 저장소에 데이터를 압축 해제하는 것이 시간을 줄일 수 있습니다.
- **기업 IT 정책 때문에 포트 80 및 포트 443 이외의 포트를 열지 않으려고 합니다**. 예를 들어 온-프레미스 데이터 저장소에서 Azure SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크에 데이터를 복사할 경우 Windows 방화벽 및 회사 방화벽 모두에 대한 포트 1433에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서는 준비된 복사가 자체 호스팅 Integration Runtime을 사용하여 먼저 포트 443에서 HTTP나 HTTPS를 경유하여 Blob Storage 준비 인스턴스에 데이터를 복사한 다음 데이터를 Blob Storage 준비에서 SQL Database나 SQL Data Warehouse로 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 Blob Storage(직접 준비)로 복사됩니다. 그 다음, 데이터가 준비 데이터 저장소에서 싱크 데이터 저장소로 복사됩니다. Data Factory는 사용자에 대한 2단계 흐름을 자동으로 관리합니다. 또한 Data Factory는 데이터 이동이 완료된 후에 준비 저장소에서 임시 데이터도 정리합니다.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용한 데이터 이동을 활성화하면 원본 데이터 저장소에서 중간 또는 준비 데이터 저장소로 데이터를 이동하기 전에 데이터를 압축할지 및 중간 또는 준비 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하기 전에 압축할지 여부를 지정할 수 있습니다.

현재는 준비 저장소를 사용하여 두 온-프레미스 데이터 저장소 간에 데이터를 복사할 수 없습니다.

### <a name="configuration"></a>구성

복사 작업에 **enableStaging** 설정을 구성하여 데이터를 대상 데이터 저장소에 로드하기 전에 Blob 저장소에서 준비할지 여부를 지정합니다. **enableStaging**을 `TRUE`로 설정한 경우 다음 표에 나열된 추가 속성을 지정해야 합니다. Azure Storage 또는 준비를 위한 Storage 공유 액세스 서명 연결된 서비스가 아직 없는 경우 만들어야 합니다.

| 속성 | 설명 | 기본값 | 필수 |
| --- | --- | --- | --- |
| **enableStaging** |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |아니요 |
| **linkedServiceName** |중간 준비 저장소로 사용할 Storage 인스턴스를 참조하여 이름을 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 연결 서비스로 지정합니다. <br/><br/> PolyBase를 통해 SQL Data Warehouse로 데이터를 로드하는 데 공유 액세스 서명을 포함한 저장소를 사용할 수 없습니다. 다른 모든 시나리오에서는 사용할 수 있습니다. |해당 없음 |예, **enableStaging**이 TRUE로 설정된 경우입니다. |
| **path** |준비 데이터를 포함할 Blob 저장소 경로를 지정합니다. 경로를 제공하지 않으면 서비스는 임시 데이터를 저장하는 컨테이너를 만듭니다. <br/><br/> 공유 액세스 서명을 포함한 저장소를 사용하거나 특정 위치에 임시 데이터가 필요한 경우에만 경로를 지정합니다. |해당 없음 |아니요 |
| **enableCompression** |대상에 복사하기 전에 데이터를 압축할지 여부를 지정합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |아니요 |

앞의 표에 설명된 속성이 있는 복사 작업의 샘플 정의는 다음과 같습니다.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

복사 기간 및 복사 유형의 두 단계에 따라 요금이 청구됩니다.

* 클라우드 복사 중에 준비 저장소를 사용할 경우(클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소에 데이터를 복사, 둘 다 Azure Integration Runtime 사용) [1단계 및 2단계 복사 기간의 합]x[클라우드 복사 단가]로 요금이 청구됩니다.
* 하이브리드 복사 중에 준비를 사용하는 경우(온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사, 한 준비 저장소가 자체 호스팅 Integration Runtime 사용) [하이브리드 복사 기간]x[하이브리드 복사 단가]+[클라우드 복사 기간]x[클라우드 복사 단가]로 요금이 청구됩니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

다음 단계에 따라 복사 작업으로 Data Factory 서비스의 성능을 조정하는 것이 좋습니다.

1. **기초 설정**. 개발 단계 중 대표적인 샘플 데이터에 대한 복사 작업을 사용하여 파이프라인을 테스트합니다. [복사 작업 모니터링](copy-activity-overview.md#monitoring)에서 실행 정보 및 성능 특성을 파악합니다.

2. **성능 진단 및 최적화**. 확인되는 성능이 기대에 미치지 못하는 경우 성능 병목 상태를 식별해야 합니다. 그런 다음 성능을 최적화하여 병목 현상의 효과를 제거하거나 줄입니다. 성능 진단에 대한 자세한 내용은 이 문서의 범위를 벗어나지만 다음과 같이 몇 가지 일반적인 고려 사항이 있습니다.

   * 성능 기능:
     * [병렬 복사](#parallel-copy)
     * [클라우드 데이터 이동 단위](#cloud-data-movement-units)
     * [준비된 복사](#staged-copy)
     * [자체 호스팅 Integration Runtime 확장성](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [자체 호스팅 Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [원본](#considerations-for-the-source)
   * [싱크](#considerations-for-the-sink)
   * [직렬화 및 역직렬화](#considerations-for-serialization-and-deserialization)
   * [압축](#considerations-for-compression)
   * [열 매핑](#considerations-for-column-mapping)
   * [기타 고려 사항](#other-considerations)

3. **전체 데이터 집합으로 구성 확장**. 실행 결과 및 성능에 만족하면 정의 및 파이프라인을 확장하여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="considerations-for-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime의 고려 사항

복사 작업이 자체 호스팅 Integration Runtime에서 실행될 경우 다음에 유의합니다.

**설정**: 전용 컴퓨터를 사용하여 Integration Runtime을 호스팅하는 것이 좋습니다. [자체 호스팅 Integration Runtime 사용을 위한 고려 사항](concepts-integration-runtime.md)을 참조하세요.

**규모 확장**: 하나 이상의 노드가 있는 단일 논리 자체 호스팅 Integration Runtime은 동시에 여러 개의 복사 작업 실행을 사용할 수 있습니다. 동시 복사 작업 실행 수가 많거나 복사할 데이터 양이 많은 하이브리드 데이터 이동이 절실한 경우 복사를 지원하도록 더 많은 리소스를 프로비전할 수 있도록 [자체 호스팅 Integration Runtime 규모 확장](create-self-hosted-integration-runtime.md#high-availability-and-scalability)을 고려해 보세요.

## <a name="considerations-for-the-source"></a>원본에 대한 고려 사항

### <a name="general"></a>일반

기본 데이터 저장소가 다른 실행 중인 워크로드에 의해 과부화되지 않도록 합니다.

Microsoft 데이터 저장소의 경우 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하며 처리량을 최대화할 수 있도록 하는 데이터 저장소 특정 [모니터링 및 튜닝 항목](#performance-reference)을 참조하세요.

* **Blob Storage에서 SQL Data Warehouse**로 데이터를 복사하는 경우에는, 성능을 높이기 위해 **PolyBase**를 사용하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* **HDFS에서 Azure Blob/Azure Data Lake Store**로 데이터를 복사할 때는 **DistCp**를 사용하여 성능을 높일 수 있습니다. 자세한 내용은 [DistCp를 사용하여 HDFS에서 데이터 복사](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)를 참조하세요.
* **Redshift에서 Azure SQL Data Warehouse/Azure BLob/Azure Data Lake Store**로 데이터를 복사할 때는 **UNLOAD**를 사용하여 성능을 높일 수 있습니다. 자세한 내용은 [UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 참조하세요.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **평균 파일 크기 및 파일 개수**: 복사 작업은 데이터를 한 번에 하나씩 전송합니다. 동일한 양의 데이터를 이동하는 경우 각 파일에 대한 부트스트랩 단계이기 때문에 적은 수의 큰 파일보다는 많은 수의 작은 파일로 데이터가 구성되는 경우 전체 처리량은 느려집니다. 따라서 가능하면 작은 파일을 더 큰 파일에 결합하여 처리량을 높입니다.
* **파일 형식 및 압축**: 성능을 향상하는 다양한 방법은 [직렬화/역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **데이터 패턴**: 테이블 스키마는 복사본 처리량에 영향을 줍니다. 행 크기가 크면 동일한 양의 데이터를 복사하는 데 작은 행 크기보다 더 나은 성능을 제공합니다. 원인은 데이터베이스가 적은 수의 행을 포함하는 더 적은 배치의 데이터보다 더욱 효율적으로 검색할 수 있기 때문입니다.
* **쿼리 또는 저장 프로시저**: 데이터를 보다 효율적으로 가져오기 위해 복사 작업 원본에서 지정한 쿼리 또는 저장 프로시저의 논리를 최적화합니다.

## <a name="considerations-for-the-sink"></a>싱크에 대한 고려 사항

### <a name="general"></a>일반

기본 데이터 저장소가 다른 실행 중인 워크로드에 의해 과부화되지 않도록 합니다.

Microsoft 데이터 저장소의 경우 데이터 저장소에 대한 [모니터링 및 튜닝 항목](#performance-reference)을 참조하세요. 이러한 항목에서 데이터 저장소 성능 특성을 이해하고 응답 시간을 최소화하고 처리량을 최대화하는 방법을 파악할 수 있습니다.

* **Blob Storage에서 SQL Data Warehouse**로 데이터를 복사하는 경우에는, 성능을 높이기 위해 **PolyBase**를 사용하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 참조하세요.
* **HDFS에서 Azure Blob/Azure Data Lake Store**로 데이터를 복사할 때는 **DistCp**를 사용하여 성능을 높일 수 있습니다. 자세한 내용은 [DistCp를 사용하여 HDFS에서 데이터 복사](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)를 참조하세요.
* **Redshift에서 Azure SQL Data Warehouse/Azure BLob/Azure Data Lake Store**로 데이터를 복사할 때는 **UNLOAD**를 사용하여 성능을 높일 수 있습니다. 자세한 내용은 [UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 참조하세요.

### <a name="file-based-data-stores"></a>파일 기반 데이터 저장소

* **복사 동작**: 서로 다른 파일 기반 저장소에서 데이터를 복사하는 경우 복사 작업에는 **copyBehavior** 속성을 통해 3가지 옵션이 제공됩니다. 계층 구조를 유지하고 평면화하며 파일을 병합합니다. 계층 구조를 유지 또는 평면화하는 작업은 성능 오버 헤드가 거의 또는 전혀 발생하지 않는 반면 파일을 병합하는 작업은 성능 오버 헤드가 증가합니다.
* **파일 형식 및 압축**: 성능을 개선하는 다양한 방법은 [직렬화/역직렬화에 대한 고려 사항](#considerations-for-serialization-and-deserialization) 및 [압축에 대한 고려 사항](#considerations-for-compression) 섹션을 참조하세요.

### <a name="relational-data-stores"></a>관계형 데이터 저장소

* **복사 동작**: **sqlSink**에 대해 설정된 속성에 따라 복사 작업은 대상 데이터베이스에 데이터를 다양한 방식으로 기록합니다.
  * 기본적으로 데이터 이동 서비스는 대량 복사 API를 사용하여 추가 모드에 데이터를 삽입하며 이는 최상의 성능을 제공합니다.
  * 싱크에 저장 프로시저를 구성하는 경우 데이터베이스는 대량 로드가 아닌, 한 번에 한 행씩 데이터를 적용합니다. 성능이 크게 저하됩니다. 데이터 집합이 크면 적용할 수 있을 때 **preCopyScript** 속성을 사용하도록 전환하는 것이 좋습니다.
  * 실행한 각 복사 작업에 **preCopyScript** 속성을 구성하는 경우 서비스는 스크립트를 트리거한 다음 대량 복사 API를 사용하여 데이터를 삽입합니다. 예를 들어 최신 데이터를 사용하여 전체 테이블을 덮어쓰려면 원본에서 새 데이터를 대량으로 로드하기 전에 먼저 스크립트를 지정하여 모든 레코드를 삭제할 수 있습니다.
* **데이터 패턴 및 배치 크기**:
  * 테이블 스키마는 복사본 처리량에 영향을 줍니다. 동일한 양의 데이터를 복사하려면 데이터베이스가 데이터에서 적은 배치를 보다 효율적으로 커밋할 수 있기 때문에 행 크기가 크면 행 크기가 작은 경우 보다 더 성능이 나아집니다.
  * 복사 작업은 일련의 배치로 데이터를 삽입합니다. **writeBatchSize** 속성을 사용하여 배치에서 행 수를 설정할 수 있습니다. 데이터에 작은 크기의 행이 있으면 높은 값을 가진 **writeBatchSize** 속성을 설정하여 적은 수의 배치 오버헤드와 높은 처리량의 혜택을 얻을 수 있습니다. 데이터의 행 크기가 큰 경우 **writeBatchSize**를 늘릴 때 주의하세요. 이 값이 높으면 데이터베이스에 오버로드가 발생하여 복사에 실패할 수 있습니다.

### <a name="nosql-stores"></a>NoSQL 저장소

* **테이블 저장소**:
  * **파티션**: 인터리브 파티션에 데이터를 작성하면 성능이 크게 저하됩니다. 파티션 키로 원본 데이터를 정렬할 수 있으므로 데이터는 파티션에 차례로 효율적으로 삽입되거나 논리를 조정하여 단일 파티션에 데이터를 기록할 수 있습니다.

## <a name="considerations-for-serialization-and-deserialization"></a>직렬화 및 역직렬화에 대한 고려 사항

입력 데이터 집합 또는 출력 데이터 집합이 파일인 경우 직렬화 및 역직렬화가 발생할 수 있습니다. 복사 작업에서 지원하는 파일 형식에 대한 세부 정보는 [지원되는 파일 및 압축 형식](supported-file-formats-and-compression-codecs.md)을 참조하세요.

**복사 동작**:

* 파일 기반 데이터 저장소 간에 파일 복사:
  * 입력 및 출력 데이터 집합 모두가 동일한 파일 형식이거나 파일 형식 설정이 없을 때 데이터 이동 서비스는 직렬화/역직렬화 없이 **이진 복사**를 실행합니다. 원본 및 싱크 파일 형식 설정이 서로 다른 시나리오에 비해 더 나은 처리량이 나타납니다.
  * 입력 및 출력 데이터 집합이 모두 텍스트 형식이고 인코딩 형식만 다른 경우 데이터 이동 서비스는 인코딩 변환만 수행합니다. 이진 복사에 비해 성능 오버헤드를 유발하는 어떠한 직렬화 및 역직렬화도 수행하지 않습니다.
  * 입력 및 출력 데이터 집합이 파일 형식이 다르거나 구분 기호와 같이 구성이 다른 경우 데이터 이동 서비스는 원본 데이터를 deserialize하여 스트리밍, 변환한 다음 표시된 출력 형식으로 직렬화합니다. 이 작업으로 다른 시나리오에 비해 훨씬 더 심각한 성능 오버헤드가 발생합니다.
* 파일 기반이 아닌 데이터 저장소 간에 파일을 복사하는 경우(예를 들어 파일 기반 저장소에서 관계형 저장소로) 직렬화 및 역직렬화 단계가 필요합니다. 이 단계로 상당한 성능 오버헤드가 발생합니다.

**파일 형식**: 선택한 파일 형식에 따라 복사 성능이 달라질 수 있습니다. 예를 들어 Avro는 데이터를 사용하여 메타데이터를 저장하는 간단한 이진 형식입니다. 처리 및 쿼리에 대한 Hadoop 에코시스템을 광범위하게 지원합니다. 그러나 Avro는 텍스트 형식에 비해 복사 처리량이 더 낮은 직렬화 및 역직렬화의 경우 더 비쌉니다. 파일 형식의 선택은 처리 흐름 전체적으로 수행합니다. 원본 데이터 저장소에 저장하거나 외부 시스템에서 추출될 데이터 형식에서 시작하여 저장소로 가장 적절한 형식, 분석 처리 및 쿼리, 그리고 보고 및 시각화 도구에 대한 데이터 마트에 데이터를 내보낼 형식 등이 포함됩니다. 경우에 따라 읽기 및 쓰기 성능에 대해 최적이 아닌 파일 형식은 전체 분석 프로세스를 고려할 때 적합한 선택일 수 있습니다.

## <a name="considerations-for-compression"></a>압축에 대한 고려 사항

입력 또는 출력 데이터 집합이 파일인 경우 대상에 데이터를 쓸 때 복사 작업을 설정하여 압축하거나 압축을 해제할 수 있습니다. 압축을 선택할 때 입력/출력(I/O) 및 CPU 간에 균형을 유지합니다. 계산 리소스에서 데이터를 압축하는 데 추가 비용이 발생합니다. 대신에, 네트워크 I/O 및 저장소는 감소합니다. 데이터에 따라 전체 복사 처리량이 향상되는 것을 볼 수 있습니다.

**코덱**: 압축 코덱마다 각기 장점이 있습니다. 예를 들어 bzip2는 가장 낮은 복사 처리량을 갖지만 처리를 위해 분할될 수 있으므로 bzip2로 최상의 Hive 쿼리 성능을 얻게 됩니다. Gzip는 가장 균형 있는 옵션을 제공하고 가장 흔하게 사용됩니다. 종단 간 시나리오에 가장 적합한 코덱을 선택합니다.

**수준**: 각 압축 코덱의 경우 빠른 압축 및 최적 압축이라는 두 옵션 중에서 선택할 수 있습니다. 파일이 최적으로 압축되지 않은 경우에도 가장 빠르게 압축된 옵션은 데이터를 최대한 빨리 압축합니다. 최적으로 압축된 옵션은 압축에 더 많은 시간을 사용하고 최소한의 데이터를 생성합니다. 두 옵션 모두 테스트하여 어떤 옵션이 사용자에게 더 나은 성능을 제공하는지 확인할 수 있습니다.

**고려 사항**: 온-프레미스 저장소와 클라우드 간에 더 많은 데이터를 복사하려면 압축을 활성화한 상태에서 [준비된 복사](#staged-copy)를 사용하는 것이 좋습니다. 임시 저장소 사용은 회사 네트워크의 대역폭과 Azure 서비스가 제한하는 요소이고 입력 데이터 집합과 출력 데이터 집합이 모두 압축되지 않은 형식인 경우 유용합니다.

## <a name="considerations-for-column-mapping"></a>열 매핑에 대한 고려 사항

복사 작업에서 **columnMappings** 속성을 설정하여 입력 열의 전체 또는 하위 집합을 출력 열에 매핑할 수 있습니다. 데이터 이동 서비스는 원본에서 데이터를 읽은 후에 데이터를 싱크에 쓰기 전에 데이터에 열 매핑을 수행해야 합니다. 이 추가 처리는 복사 처리량을 감소시킵니다.

원본 데이터 저장소를 쿼리할 수 있는 경우 예를 들어 SQL Database 또는 SQL Server와 같은 관계형 저장소이거나 테이블 저장소 또는 Azure Cosmos DB와 같은 NoSQL 저장소인 경우 열 매핑을 사용하는 대신, 열 필터링 및 재정렬 논리를 **query** 속성에 푸시하는 것이 좋습니다. 이러한 방식으로 데이터 이동 서비스가 원본 데이터 저장소에서 데이터를 읽는 동안 프로젝션이 발생하며 이는 훨씬 효율적입니다.

자세한 정보는 [복사 작업 스키마 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

## <a name="other-considerations"></a>기타 고려 사항

복사할 데이터의 크기가 크면 비즈니스 논리를 조정하여 데이터 파티션을 추가하고 복사 작업이 더 자주 실행되게 예약하여 각 복사 작업 실행의 데이터 크기를 줄일 수 있습니다.

Data Factory에서 동시에 동일한 데이터 저장소에 연결해야 하는 데이터 집합 및 복사 작업 수에 주의하세요. 많은 동시 복사 작업은 데이터 저장소를 제한하고 성능 저하, 복사 작업 내부 재시도 및 일부 경우 실행 오류를 발생시킬 수 있습니다.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>샘플 시나리오: 온-프레미스 SQL Server에서 Blob 저장소로 복사

**시나리오**: 파이프라인은 온-프레미스 SQL Server에서 Blob 저장소로 CSV 형식으로 데이터를 복사하도록 작성됩니다. 복사 작업을 더 빠르게 하려면 CSV 파일이 bzip2 형식으로 압축되어야 합니다.

**테스트 및 분석**: 복사 작업의 처리량이 2MBps보다 적고 성능 벤치마크보다 훨씬 더 느립니다.

**성능 분석 및 튜닝**: 성능 문제를 해결하기 위해 데이터가 처리되고 이동되는 방법을 살펴보겠습니다.

1. **데이터 읽기**: Integration Runtime 은 SQL Server에 연결을 열고 쿼리를 보냅니다. SQL Server는 데이터 스트림을 인트라넷을 통해 Integration Runtime으로 전송하여 응답합니다.
2. **데이터 직렬화 및 압축**: Integration Runtime은 데이터 스트림을 CSV 형식으로 직렬화하고 데이터를 bzip2 스트림으로 압축합니다.
3. **데이터 쓰기**: Integration Runtime은 인터넷을 통해 Blob 저장소로 bzip2 스트림을 업로드합니다.

보이는 대로 데이터를 처리하고 SQL Server > LAN > Integration Runtime > WAN > Blob Storage 순으로 이동합니다. SQL Server -> LAN -> 게이트웨이 -> WAN -> Blob 저장소 **전반적인 성능은 파이프라인을 통해 최소 처리량에서 제어됩니다**.

![데이터 흐름](./media/copy-activity-performance/case-study-pic-1.png)

다음 중 하나 이상의 요인으로 성능 병목 현상이 발생할 수 있습니다.

* **원본**: SQL Server 자체가 과도한 로드로 인해 처리량이 낮아집니다.
* **자체 호스팅 Integration Runtime**:
  * **LAN**: Integration Runtime이 SQL Server 컴퓨터에서 멀리 떨어져 있고 낮은 대역폭 연결을 사용합니다.
  * **Integration Runtime**: Integration Runtime은 다음 작업을 수행하는 로드 제한에 도달했습니다.
    * **직렬화**: CSV 형식에 대한 데이터 스트림을 직렬화하면 처리량이 느려집니다.
    * **압축**: 느린 압축 코덱을 선택했습니다(예: Core i7 2.8MBps의 bzip2).
  * **WAN**: 회사 네트워크 및 Azure 서비스 간의 대역폭이 낮습니다(예: T1 = 1,544kbps, T2 = 6,312kbps).
* **싱크**: Blob 저장소의 처리량이 낮습니다. (이 시나리오에서는 해당 SLA가 최소 60MBps를 보장하므로 가능성이 없습니다.)

이 경우 bzip2 데이터 압축은 전체 파이프라인을 느리게 만들 수 있습니다. gzip 압축 코덱을 전환하면 병목 상태를 완화할 수 있습니다.

## <a name="reference"></a>참고 자료

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Azure Storage(Blob 저장소 및 테이블 저장소 포함): [Azure Storage 확장성 목표](../storage/common/storage-scalability-targets.md) 및 [Azure Storage 성능 및 확장성 검사 목록](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: [성능을 모니터링](../sql-database/sql-database-single-database-monitor.md)하고 DTU(데이터베이스 트랜잭션 단위) 비율을 확인할 수 있습니다.
* Azure SQL Data Warehouse: 해당 기능은 DWU(데이터 웨어하우스 단위)로 측정됩니다. [Azure SQL Data Warehouse의 계산 능력 관리(개요)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조하세요.
* Azure Cosmos DB: [Azure Cosmos DB의 성능 수준](../cosmos-db/performance-levels.md)
* 온-프레미스 SQL Server: [성능에 대한 모니터링 및 튜닝](https://msdn.microsoft.com/library/ms189081.aspx)
* 온-프레미스 파일 서버: [파일 서버에 대한 성능 튜닝](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 스키마 매핑](copy-activity-schema-and-type-mapping.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)
