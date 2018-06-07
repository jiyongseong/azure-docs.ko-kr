---
title: Azure Data Factory에서 데이터 집합 만들기 | Microsoft Docs
description: offset 및 anchorDateTime과 같은 속성을 사용하는 예제를 통해 Azure Data Factory에서 데이터 집합을 만드는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a572824225c0d83af698c4ff18d6b297b1ebb729
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="datasets-in-azure-data-factory"></a>Azure 데이터 팩터리의 데이터 집합
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](data-factory-create-datasets.md)
> * [버전 2 - 미리 보기](../concepts-datasets-linked-services.md)

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기 상태인 Data Factory 버전 2 서비스를 사용 중인 경우 [V2의 데이터 집합](../concepts-datasets-linked-services.md)을 참조하세요.

이 문서에서는 데이터 집합의 정의, 데이터 집합을 JSON 형식으로 정의하는 방법, Azure Data Factory 파이프라인에서 데이터 집합을 사용하는 방법에 대해 설명합니다. 데이터 집합 JSON 정의의 각 섹션(예: 구조, 가용성 및 정책)에 대한 세부 정보를 제공합니다. 또한 데이터 집합 JSON 정의에서 **offset**, **anchorDateTime** 및 **style** 속성을 사용하기 위한 예제도 제공합니다.

> [!NOTE]
> Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요. 데이터 팩터리를 실제로 만들어 본 경험이 없는 경우 [데이터 변환 자습서](data-factory-build-your-first-pipeline.md) 및 [데이터 이동 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하면 더 나은 이해를 얻을 수 있습니다. 

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. **파이프라인**은 함께 하나의 작업을 수행하는 **활동**의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 복사 활동을 사용하여 온-프레미스 SQL Server에서 Azure Blob 저장소로 데이터를 복사할 수 있습니다. 그런 다음 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 Hive 활동을 사용하여 출력 데이터를 생성하도록 Blob 저장소의 데이터를 처리합니다. 마지막으로 두 번째 복사 활동을 사용하여 BI(비즈니스 인텔리전스) 보고 솔루션의 기반이 되는 Azure SQL Data Warehouse로 출력 데이터를 복사합니다. 파이프라인 및 활동에 대한 자세한 내용은 [Azure Data Factory의 파이프라인 및 활동](data-factory-create-pipelines.md) 문서를 참조하세요.

활동은 0개 이상의 입력 **데이터 집합**을 받고, 하나 이상의 출력 데이터 집합을 생성할 수 있습니다. 파이프라인에서 입력 데이터 집합은 활동에 대한 입력을 나타내고, 출력 데이터 집합은 활동에 대한 출력을 나타냅니다. 데이터 집합은 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 집합은 파이프라인에서 데이터를 읽어들여야 하는 Blob 저장소의 Blob 컨테이너와 폴더를 지정합니다. 

데이터 집합을 만들기 전에 **연결된 서비스**를 만들어 데이터 저장소를 데이터 팩터리에 연결합니다. 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 집합은 SQL 테이블, 파일, 폴더, 문서를 비롯한 연결된 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Storage 연결된 서비스는 저장소 계정을 데이터 팩터리에 연결합니다. Azure Blob 데이터 집합은 blob 컨테이너 및 처리할 입력 blob이 포함된 폴더를 나타냅니다. 

샘플 시나리오는 다음과 같습니다. 데이터베이스를 Blob 저장소에서 Azure SQL Database로 복사하려면 두 개의 연결된 서비스, 즉 Azure Storage 및 Azure SQL Database를 만듭니다. 그런 다음 두 개의 데이터 집합, 즉 Azure Blob 데이터 집합(Azure Storage 연결된 서비스 참조), Azure SQL 테이블 데이터 집합(Azure SQL Database 연결된 서비스 참조)을 만듭니다. Azure Storage 및 Azure SQL Database 연결된 서비스에는 Data Factory가 런타임에 Azure Storage 및 Azure SQL Database 각각에 연결하는 데 사용하는 연결 문자열이 포함되어 있습니다. Azure Blob 데이터 집합은 Blob 저장소에 입력 Blob을 포함하는 Blob 컨테이너와 Blob 폴더를 지정합니다. Azure SQL 테이블 데이터 집합은 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 Data Factory의 파이프라인, 활동, 데이터 집합 및 연결된 서비스 간의 관계를 보여 줍니다. 

![파이프라인, 활동, 데이터 집합, 연결된 서비스 간 관계](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>데이터 집합 JSON
Data Factory의 데이터 집합은 다음과 같이 JSON 형식으로 정의됩니다.

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

다음 표에서는 위의 JSON에서 속성을 설명합니다.   

| 자산 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| 이름 |데이터 집합의 이름입니다. 명명 규칙은 [Azure Data Factory - 명명 규칙](data-factory-naming-rules.md) 을 참조하세요. |예 |해당 없음 |
| 형식 |데이터 집합의 형식입니다. Data Factory에서 지원하는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 집합 형식](#Type)을 참조하세요. |예 |해당 없음 |
| structure |데이터 집합의 스키마입니다.<br/><br/>자세한 내용은 [데이터 집합 구조](#Structure)를 참조하세요. |아니오 |해당 없음 |
| typeProperties | 형식 속성은 형식마다 다릅니다(예: Azure Blob, Azure SQL 테이블). 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 집합 형식](#Type)을 참조하세요. |예 |해당 없음 |
| external | 데이터 집합이 데이터 팩터리 파이프라인에 의해 명시적으로 생성되는지를 지정하는 부울 플래그입니다. 활동에 대한 입력 데이터 집합이 현재 파이프라인에서 생성되지 않으면 이 플래그를 true로 설정합니다. 파이프라인에서 첫 번째 활동의 입력 데이터 집합에 대해서는 이 플래그를 true로 설정합니다.  |아니오 |false |
| availability | 데이터 집합 생성에 대한 처리 기간(예: 매시간 또는 매일) 또는 조각화 모델을 정의합니다. 각 데이터 단위는 데이터 조각이라는 작업 실행으로 사용되고 생성됩니다. 출력 데이터 집합의 가용성을 매일(frequency - Day, interval - 1)로 설정하면 조각이 매일 생성됩니다. <br/><br/>자세한 내용은 [데이터 집합 가용성](#Availability)을 참조하세요. <br/><br/>데이터 집합 조각화 모델에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요. |예 |해당 없음 |
| policy |데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다. <br/><br/>자세한 내용은 [데이터 집합 정책](#Policy)을 참조하세요. |아니오 |해당 없음 |

## <a name="dataset-example"></a>데이터 집합 예제
다음 예제에서 데이터 집합은 Azure SQL 데이터베이스에서 **MyTable**이라는 테이블을 나타냅니다.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

다음 사항에 유의하세요.

* **type**은 AzureSqlTable로 설정됩니다.
* AzureSqlTable 형식과 관련된 **tableName** 형식 속성은 MyTable로 설정됩니다.
* **linkedServiceName**은 다음 JSON 코드 조각으로 정의된 AzureSqlDatabase 형식의 연결된 서비스를 가리킵니다. 
* **availability frequency**는 Day로 설정되고, **interval**은 1로 설정됩니다. 즉 데이터 집합 조각이 매일 생성됩니다.  

**AzureSqlLinkedService**는 다음과 같이 정의됩니다.

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

앞의 JSON 코드 조각에서

* **type**은 AzureSqlDatabase로 설정됩니다.
* **connectionString** 형식 속성은 SQL 데이터베이스에 연결하는 정보를 지정합니다.  

여기서 볼 수 있듯이 연결된 서비스는 Azure SQL 데이터베이스에 연결하는 방법을 정의합니다. 데이터 집합은 파이프라인의 활동에 대한 입력 및 출력으로 사용되는 테이블을 정의합니다.   

> [!IMPORTANT]
> 데이터 집합이 파이프라인에서 생성되지 않으면 **external**로 표시되어야 합니다. 이 설정은 파이프라인에서 첫 번째 작업의 입력에 일반적으로 적용됩니다.   


## <a name="Type"></a> 데이터 집합 형식
데이터 집합의 형식은 사용하는 데이터 저장소에 따라 달라집니다. Data Factory에서 지원하는 데이터 저장소 목록은 다음 표를 참조하세요. 해당 데이터 저장소에 대해 연결된 서비스 및 데이터 집합을 만드는 방법을 알아보려면 데이터 저장소를 클릭합니다.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> *가 있는 데이터 저장소는 서비스(IaaS)로서 온-프레미스 또는 Azure 인프라에 있을 수 있습니다. 이러한 데이터 저장소에는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 설치해야 합니다.

이전 섹션의 예제에서 데이터 집합의 형식은 **AzureSqlTable**로 설정되어 있습니다. 마찬가지로 Azure Blob 데이터 집합의 경우 데이터 집합의 형식이 다음 JSON과 같이 **AzureBlob**으로 설정됩니다.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>데이터 집합 구조
**structure** 섹션은 선택 사항입니다. 열의 이름 및 데이터 형식 컬렉션을 포함하여 데이터 집합의 스키마를 정의합니다. 형식을 변환하고 열을 원본에서 대상으로 매핑하는 데 사용되는 형식 정보를 structure 섹션에 제공합니다. 다음 예제에서 데이터 집합에는 세 개의 열, 즉 `slicetimestamp`, `projectname` 및 `pageviews`가 있습니다. 형식은 각각 String, String 및 Decimal입니다.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

structure의 각 열에는 다음과 같은 속성이 포함됩니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 이름 |열의 이름입니다. |예 |
| 형식 |열의 데이터 형식입니다.  |아니오 |
| culture |type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 .NET 기반 culture(문화권)입니다. 기본값은 `en-us`입니다. |아니오 |
| format |type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 format(서식) 문자열입니다. |아니오 |

다음 지침은 structure 정보를 포함할 시기 및 **structure** 섹션에 포함할 내용을 결정하는 데 도움이 됩니다.

* **구조화된 데이터 원본의 경우** 원본 열을 싱크 열에 매핑하고 해당 이름이 동일하지 않은 경우에만 structure 섹션을 지정합니다. 이러한 종류의 구조화된 데이터 원본은 데이터 스키마 및 형식 정보를 데이터 자체와 함께 저장합니다. 구조화된 데이터 원본의 예로 SQL Server, Oracle 및 Azure 테이블이 있습니다. 
  
    구조화된 데이터 원본에 대해 형식 정보를 이미 사용할 수 있으므로 "structure" 섹션을 포함할 때 형식 정보를 포함하면 안됩니다.
* **읽기 데이터 원본(특히 Blob 저장소)에 대한 스키마의 경우** 스키마 또는 형식 정보를 데이터와 함께 저장하지 않고도 데이터를 저장하도록 선택할 수 있습니다. 이러한 유형의 데이터 원본인 경우 원본 열을 싱크 열로 매핑할 때 구조가 포함됩니다. 또한 데이터 집합이 복사 활동에 대한 입력인 경우 구조가 포함되고 원본 데이터 집합의 데이터 형식을 싱크의 네이티브 형식으로 변환해야 합니다. 
    
    Data Factory는 structure에 형식 정보를 제공하기 위해 **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset 및 Timespan** 값을 지원합니다. 이러한 값은 CLS(공용 언어 사양) 규격 .NET 기반 type 값입니다.

데이터 팩터리는 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사할 때 형식 변환을 자동으로 수행합니다. 
  

## <a name="dataset-availability"></a>데이터 집합 가용성
데이터 집합의 **availability** 섹션은 데이터 집합에 대한 처리 기간(매시간, 매일, 매주 등)을 정의합니다. 활동 기간에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.

다음 availability 섹션에서는 매시간 출력 데이터 집합을 생성하거나 매시간 입력 데이터 집합을 사용할 수 있도록 지정합니다.

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

파이프라인에 다음과 같은 시작 및 종료 시간이 있는 경우  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

출력 데이터 집합은 파이프라인 시작 시간 및 종료 시간 내에서 시간별로 생성됩니다. 따라서 이 파이프라인에서는 각 활동 기간(오전 12-1시, 오전 1-2시, 오전 2-3시, 오전 3-4시, 오전 4-5시)마다 하나씩 5개의 데이터 집합 조각이 생성됩니다. 

다음 표에서는 availability 섹션에서 사용할 수 있는 속성을 설명합니다.

| 자산 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| frequency |데이터 집합 조각 생성을 위한 시간 단위를 지정합니다.<br/><br/><b>지원되는 빈도</b>: 분, 시, 일, 주, 월 |예 |해당 없음 |
| interval |빈도에 대한 승수를 지정합니다.<br/><br/>"frequency x interval"은 조각을 생성하는 빈도를 결정합니다. 예를 들어 데이터 집합을 매시간 조각화해야 하는 경우 <b>frequency</b>를 <b>Hour</b>로, <b>interval</b>을 <b>1</b>로 설정합니다.<br/><br/>**frequency**를 **Minute**로 지정하는 경우 interval을 15 이상으로 설정해야 합니다. |예 |해당 없음 |
| style |간격의 시작/끝에서 조각을 생성해야 하는지 여부를 지정합니다.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>**frequency**를 **Month**로 설정하고 **style**을 **EndOfInterval**로 설정하는 경우 조각을 매월 마지막 일에 생성합니다. **style**을 **StartOfInterval**로 설정하는 경우 조각을 매월 첫째 일에 생성합니다.<br/><br/>**frequency**를 **Day**로 설정하고 **style**을 **EndOfInterval**로 설정하는 경우 조각을 매일 마지막 시간에 생성합니다.<br/><br/>**frequency**를 **Hour**로 설정하고 **style**을 **EndOfInterval**로 설정하는 경우 조각을 매시간 끝에 생성합니다. 예를 들어 오후 1-2시 기간에 대한 조각은 오후 2시에 생성됩니다. |아니오 |EndOfInterval |
| anchorDateTime |스케줄러에서 사용하는 시간에 절대 위치를 정의하여 데이터 집합 조각 경계를 계산합니다. <br/><br/>이 속성에 지정된 frequency보다 더 세분화된 날짜 부분이 있으면 이 날짜 부분은 무시됩니다. 예를 들어 **간격**이 **매시간**(frequency: Hour 및 interval: 1)이고 **anchorDateTime**에 **분 및 초**가 포함되는 경우 **anchorDateTime**의 분 및 초 부분은 무시됩니다. |아니오 |01/01/0001 |
| offset |모든 데이터 집합 조각의 시작과 끝이 이동에 의한 Timespan입니다. <br/><br/>**anchorDateTime** 및 **offset**이 모두 지정되면 결합된 결과로 나타납니다. |아니오 |해당 없음 |

### <a name="offset-example"></a>offset example
기본적으로 조각은 매일(`"frequency": "Day", "interval": 1`) 오전 12시(자정) UTC(협정 세계시)에 시작합니다. 대신, 시작 시간을 UTC 시간으로 오전 6시로 설정하려면 다음 코드 조각과 같이 오프셋을 설정합니다. 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime 예제
다음 예제에서는 데이터 집합이 23시간마다 생성됩니다. 첫 번째 조각은 **anchorDateTime**에 지정된 시간(`2017-04-19T08:00:00`(UTC)으로 설정됨)에 시작됩니다.

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>offset/style 예제
다음 데이터 집합은 매월 3일, 오전 8시(`3.08:00:00`)에 생성됩니다.

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>데이터 집합 정책
데이터 집합 정의의 **policy** 섹션은 데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다.

### <a name="validation-policies"></a>정책 유효성 검사
| 정책 이름 | 설명 | 적용 대상 | 필수 | 기본값 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |**Azure Blob 저장소**의 데이터가 최소 크기 요구 사항(MB)을 충족하는지 확인합니다. |Azure Blob 저장소 |아니오 |해당 없음 |
| minimumRows |**Azure SQL Database** 또는 **Azure 테이블**에서 데이터가 최소 행 수를 포함하는지 확인합니다. |<ul><li>Azure SQL 데이터베이스</li><li>Azure 테이블</li></ul> |아니오 |해당 없음 |

#### <a name="examples"></a>예
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>외부 데이터 집합
외부 데이터 집합은 데이터 팩토리에서 실행 중인 파이프라인에 의해 생성되지 않습니다. 데이터 집합이 **external**로 표시된 경우 **ExternalData** 정책을 정의하여 데이터 집합 조각 가용성의 동작에 영향을 미칠 수 있습니다.

Data Factory에서 데이터 집합을 생성하지 않는 한 **external**로 표시되어야 합니다. 이 설정은 일반적으로 활동 또는 파이프라인 체이닝을 사용하지 않는 한 파이프라인의 첫 번째 활동에 대한 입력에 적용됩니다.

| Name | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| dataDelay |지정된 조각에 대한 외부 데이터의 가용성 검사를 지연하는 시간입니다. 예를 들어 이 설정을 사용하여 시간별 검사를 지연할 수 있습니다.<br/><br/>이 설정은 현재 시간에만 적용됩니다.  예를 들어 현재 오후 1시이고 이 값이 10 분이라면 유효성 검사는 오후 1시 10분에 시작합니다.<br/><br/>이전 시간에는 이 설정으로 조각에 영향을 주지 않습니다. **조각 종료 시간** + **dataDelay** < **현재 시간**인 경우 조각은 지연 없이 처리됩니다.<br/><br/>23:59보다 큰 시간은 `day.hours:minutes:seconds` 형식을 사용하여 지정해야 합니다. 예를 들어 24시간을 지정하려면 24:00:00을 사용하는 대신 1.00:00:00을 사용합니다. 24:00:00을 사용하면 24일(24.00:00:00)로 처리됩니다. 1일 4시간의 경우 1:04:00:00을 지정합니다. |아니요 |0 |
| retryInterval |실패 이후 다음 다시 시도까지의 대기 시간입니다. 이 설정은 현재 시간에 적용됩니다. 이전 시도가 실패한 경우 다음 시도는 **retryInterval** 기간 이후입니다. <br/><br/>오후 1시가 되면 첫 번째 시도를 시작합니다. 첫 번째 유효성 검사를 완료하는 데 걸리는 시간이 1분이고 작업이 실패한 경우 다음 다시 시도는 1시 + 1분(기간) + 1분(다시 시도 간격) = 오후 1시 2분입니다. <br/><br/>과거 조각의 경우 지연이 없습니다. 재시도는 곧바로 이뤄집니다. |아니요 |00:01:00 (1분) |
| retryTimeout |각 재시도에 대한 제한 시간입니다.<br/><br/>이 속성을 10분으로 설정하면 유효성 검사를 10분 내에 완료해야 합니다. 유효성 검사를 수행하는 데 10분 보다 오래 걸리는 경우 재시도는 제한 시간을 초과합니다.<br/><br/>모든 유효성 검사 시도에서 시간을 초과하면 조각이 **TimedOut**으로 표시됩니다. |아니오 |00:10:00 (10분) |
| maximumRetry |외부 데이터의 가용성을 검사하는 횟수입니다. 허용되는 최대값은 10입니다. |아니오 |3 |


## <a name="create-datasets"></a>데이터 집합 만들기
다음 도구 또는 SDK 중 하나를 사용하여 데이터 집합을 만들 수 있습니다. 

- 복사 마법사 
- Azure portal
- Visual Studio
- PowerShell
- Azure Resource Manager 템플릿
- REST API
- .NET API

다음 도구 또는 SDK 중 하나를 사용하여 파이프라인 및 데이터 집합을 만들기 위한 단계별 지침은 다음 자습서를 참조하세요.
 
- [데이터 변환 활동을 사용하여 파이프라인 빌드](data-factory-build-your-first-pipeline.md)
- [데이터 이동 활동을 사용하여 파이프라인 빌드](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

파이프라인을 만들고 배포한 후에 Azure Portal 블레이드 또는 모니터링 및 관리 앱을 사용하여 파이프라인을 관리하고 모니터링할 수 있습니다. 단계별 지침은 다음 항목을 참조하세요. 

- [Azure Portal 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)
- [모니터링 및 관리 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>범위가 지정된 데이터 집합
**datasets** 속성을 사용하여 파이프라인으로 범위가 지정되는 데이터 집합을 만들 수 있습니다. 이러한 데이터 집합은 다른 파이프라인의 활동이 아니라 이 파이프라인 내의 활동에서만 사용할 수 있습니다. 다음 예제에서는 파이프라인 내에서 사용되는 두 개의 데이터 집합(InputDataset-rdc 및 OutputDataset-rdc)이 있는 파이프라인을 정의합니다.  

> [!IMPORTANT]
> 범위가 지정된 데이터 집합은 일회성 파이프라인(**pipelineMode**가 **OneTime**으로 설정된 경우)에서만 지원됩니다. 자세한 내용은 [일회성 파이프라인](data-factory-create-pipelines.md#onetime-pipeline) 을 참조하세요.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>다음 단계
- 파이프라인에 대한 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요. 
- 파이프라인을 예약하고 실행하는 방법에 대한 자세한 내용은 [Azure Data Factory 예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요. 
