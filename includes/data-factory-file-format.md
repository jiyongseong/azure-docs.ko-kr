## <a name="specifying-formats"></a>형식 지정
Azure Data Factory는 다음과 같은 서식 유형을 지원합니다.

* [텍스트 형식](#specifying-textformat)
* [JSON 형식](#specifying-jsonformat)
* [Avro 형식](#specifying-avroformat)
* [ORC 형식](#specifying-orcformat)
* [Parquet 형식](#specifying-parquetformat)

### <a name="specifying-textformat"></a>TextFormat 지정
텍스트 파일을 구문 분석하거나 데이터를 텍스트 형식으로 쓰려면 `format` `type` 속성을 **TextFormat**으로 설정합니다. `format` 섹션에서 다음 **선택적** 속성을 지정할 수도 있습니다. 구성 방법은 [TextFormat 예제](#textformat-example) 섹션을 참조하세요.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| columnDelimiter |파일의 열을 구분하는 데 사용되는 문자입니다. 데이터에 존재할 가능성이 없고 희귀하며 인쇄할 수 없는 문자를 사용하는 방법을 고려해 볼 수 있습니다(예: SOH(Head of Start)를 나타내는 "\u0001"을 지정). |문자는 하나만 사용할 수 있습니다. **기본값**은 **쉼표(,)**입니다. <br/><br/>유니코드 문자를 사용하려면 [유니코드 문자](https://en.wikipedia.org/wiki/List_of_Unicode_characters)를 참조하여 해당하는 코드를 가져옵니다. |아니요 |
| rowDelimiter |파일의 행을 구분하는 데 사용되는 문자입니다. |문자는 하나만 사용할 수 있습니다. **기본값**은 읽기의 경우 **["\r\n", "\r", "\n"]** 중 하나이고, 쓰기의 경우 **"\r\n"**입니다. |아니요 |
| escapeChar |입력 파일 내용에서 열 구분 기호를 이스케이프 처리하는 데 사용되는 특수 문자입니다. <br/><br/>한 테이블에 대해 escapeChar 및 quoteChar을 둘 다 지정할 수는 없습니다. |문자는 하나만 사용할 수 있습니다. 기본값은 없습니다. <br/><br/>예: 열 구분 기호로 쉼표(',')를 지정했는데 텍스트에서도 "Hello, world"와 같이 쉼표 문자를 포함하려는 경우에는 이스케이프 문자로 '$'를 정의하고 원본에서 "Hello$, world" 문자열을 사용하면 됩니다. |아니요 |
| quoteChar |문자열 값을 인용하는 데 사용하는 문자입니다. 인용 문자 안의 열 및 행 구분 기호는 문자열 값의 일부분으로 처리됩니다. 이 속성은 입력 데이터 집합과 출력 데이터 집합에 모두 적용됩니다.<br/><br/>한 테이블에 대해 escapeChar 및 quoteChar을 둘 다 지정할 수는 없습니다. |문자는 하나만 사용할 수 있습니다. 기본값은 없습니다. <br/><br/>예: 열 구분 기호로 쉼표(',')를 지정했는데 텍스트에서도 <Hello, world>와 같이 쉼표 문자를 포함하려는 경우에는 인용 문자로 "(큰따옴표)를 정의하고 원본에서 "Hello, world" 문자열을 사용하면 됩니다. |아니요 |
| nullValue |null 값을 나타내는 데 사용되는 하나 이상의 문자입니다. |하나 이상의 문자입니다. **기본값**은 읽기의 경우 **"\N" 및 "NULL"**이고, 쓰기의 경우 **"\N"**입니다. |아니요 |
| encodingName |인코딩 이름을 지정합니다. |유효한 인코딩 이름입니다. [Encoding.EncodingName 속성](https://msdn.microsoft.com/library/system.text.encoding.aspx)을 참조하세요. windows-1250 또는 shift_jis 등을 예로 들 수 있습니다. **기본값**은 **UTF-8**입니다. |아니요 |
| firstRowAsHeader |첫 번째 행을 머리글로 간주할지를 지정합니다. 입력 데이터 집합의 경우 Data Factory는 첫 번째 행을 머리글로 읽습니다. 출력 데이터 집합의 경우에는 첫 번째 행을 머리글로 씁니다. <br/><br/>샘플 시나리오의 경우 [`firstRowAsHeader` 및 `skipLineCount` 사용 시나리오](#scenarios-for-using-firstrowasheader-and-skiplinecount)를 참조하세요. |True<br/>**False(기본값)** |아니요 |
| skipLineCount |입력 파일에서 데이터를 읽을 때 건너뛸 행의 수를 나타냅니다. SkipLineCount와 firstRowAsHeader를 둘 다 지정하면 해당하는 줄을 먼저 건너뛴 다음 입력 파일에서 머리글 정보를 읽습니다. <br/><br/>샘플 시나리오의 경우 [`firstRowAsHeader` 및 `skipLineCount` 사용 시나리오](#scenarios-for-using-firstrowasheader-and-skiplinecount)를 참조하세요. |Integer |아니요 |
| treatEmptyAsNull |입력 파일에서 데이터를 읽을 때 null 또는 빈 문자열을 null 값으로 처리할지를 지정합니다. |**True(기본값)**<br/>False |아니요 |

#### <a name="textformat-example"></a>TextFormat 예제
다음 예제는 TextFormat에 대한 서식 속성 중 일부를 보여줍니다.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

`quoteChar` 대신 `escapeChar`을 사용하려면 `quoteChar`이 있는 줄을 다음 escapeChar로 바꿉니다.

```json
"escapeChar": "$",
```

#### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>FirstRowAsHeader 및 skipLineCount 사용 시나리오
* 파일이 아닌 원본에서 텍스트 파일로 데이터를 복사할 때 SQL 스키마 등의 스키마 메타데이터가 포함된 머리글 줄을 추가하려고 합니다. 이 시나리오의 출력 데이터 집합에서 `firstRowAsHeader`를 true로 지정합니다.
* 머리글 줄이 포함된 텍스트 파일에서 파일이 아닌 싱크로 데이터를 복사할 때 해당 줄을 삭제하려고 합니다. 입력 데이터 집합에서 `firstRowAsHeader`를 true로 지정합니다.
* 텍스트 파일에서 데이터를 복사할 때 시작 부분에서 데이터도 없고 머리글 정보도 없는 몇 줄을 건너뛰려고 합니다. 건너뛸 줄 수를 나타내는 `skipLineCount`를 지정합니다. 파일의 나머지 부분에 헤더 줄이 있으면 `firstRowAsHeader`도 지정할 수 있습니다. `skipLineCount`와 `firstRowAsHeader`를 둘 다 지정하면 먼저 해당 줄을 먼저 건너뛴 다음 입력 파일에서 헤더 정보를 읽습니다.

### <a name="specifying-jsonformat"></a>JsonFormat 지정
**Azure Cosmos DB에(서) JSON 파일을 그대로 가져오거나 내보내려면** Azure Cosmos DB 커넥터의 [JSON 문서 가져오기/내보내기](../articles/data-factory/v1/data-factory-azure-documentdb-connector.md#importexport-json-documents) 섹션에 있는 자세한 정보를 참조하세요.

JSON 파일을 구문 분석하거나 데이터를 JSON 형식으로 쓰려면 `format` `type` 속성을 **JsonFormat**으로 설정합니다. `format` 섹션에서 다음 **선택적** 속성을 지정할 수도 있습니다. 구성 방법은 [JsonFormat 예제](#jsonformat-example) 섹션을 참조하세요.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 사용 가능한 값은 **setOfObjects** 및 **arrayOfObjects**이고 **기본값**은 **setOfObjects**입니다. 이러한 패턴에 대한 자세한 내용은 [JSON 파일 패턴](#json-file-patterns) 섹션을 참조하세요. |아니요 |
| jsonNodeReference | 동일한 패턴으로 배열 필드 내부의 개체에서 데이터를 반복하고 추출하려면 해당 배열의 JSON 경로를 지정합니다. 이 속성은 JSON 파일에서 데이터를 복사할 때만 지원됩니다. | 아니요 |
| jsonPathDefinition | 사용자 지정된 열 이름(소문자로 시작)으로 각 열 매핑에 대한 JSON 경로 식을 지정합니다. 이 속성은 JSON 파일에서 데이터를 복사할 때만 지원되며 개체 또는 배열에서 데이터를 추출할 수 있습니다. <br/><br/> 루트 개체 아래의 필드는 root $로 시작하며, `jsonNodeReference` 속성으로 선택된 배열 내부의 필드는 배열 요소에서 시작합니다. 구성 방법은 [JsonFormat 예제](#jsonformat-example) 섹션을 참조하세요. | 아니요 |
| encodingName |인코딩 이름을 지정합니다. 유효한 인코딩 이름 목록은 [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 속성을 참조하세요. 예: windows-1250 또는 shift_jis **기본값**은 **UTF-8**입니다. |아니요 |
| nestingSeparator |중첩 수준을 구분하는데 사용되는 문자입니다. 기본값은 '.'(점)입니다. |아니요 |

#### <a name="json-file-patterns"></a>JSON 파일 패턴

복사 작업에서는 JSON 파일의 다음 패턴을 구문 분석할 수 있습니다.

- **유형 I: setOfObjects**

    각 파일에 단일 개체 또는 줄로 구분된/연결된 여러 개체가 포함되어 있습니다. 출력 데이터 집합에서 이 옵션을 선택하는 경우 복사 활동에서는 줄마다 개체가 하나씩 포함된(각 개체가 줄로 구분된) JSON 파일 하나를 생성합니다.

    * **단일 개체 JSON 예제**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **줄로 구분된 JSON 예제**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **연결된 JSON 예제**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **유형 II: arrayOfObjects**

    각 파일에 개체 배열이 포함됩니다.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

#### <a name="jsonformat-example"></a>JsonFormat 예제

**사례 1: JSON 파일에서 데이터 복사**

JSON 파일에서 데이터를 복사하는 경우 아래의 두 가지 샘플 유형과 유의할 일반 요소를 참조하세요.

**샘플 1: 개체 및 배열에서 데이터 추출**

이 샘플에서는 테이블 형식 결과에서 하나의 루트 JSON 개체를 하나의 레코드로 매핑하는 것이 예상됩니다. 다음 내용을 포함하는 JSON 파일이 있고  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagmentProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
개체와 배열 모두에서 데이터를 추출하여 다음과 같은 형식으로 Azure SQL 테이블에 복사하려는 경우

| id | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** 형식의 입력 데이터 집합은 다음과 같이 정의됩니다(관련 부분만 있는 부분 정의). 더 구체적으로 살펴보면 다음과 같습니다.

- `structure` 섹션은 테이블 형식 데이터로 변환하는 동안 사용자 지정된 열 이름과 해당 데이터 형식을 정의합니다. 이 섹션은 열 매핑을 수행할 필요가 없는 경우를 제외하고는 **선택적**입니다. 자세한 내용은 [사각형 데이터 집합의 구조 정의 지정](#specifying-structure-definition-for-rectangular-datasets) 섹션을 참조하세요.
- `jsonPathDefinition`은 데이터를 추출할 위치를 나타내는 각 열의 JSON 경로를 지정합니다. 데이터를 배열에서 복사하려면 **array[x].property를 사용하여** xth 개체에서 지정된 된 속성의 값을 추출하거나 **array[*].property**를 사용하여 이러한 속성을 포함하는 모든 개체의 값을 찾을 수 있습니다.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagmentProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagmentProcessRunId": "$.context.custom.dimensions[1].ResourceManagmentProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**샘플 2: 배열에서 동일한 패턴을 사용하여 여러 개체 배열에서 교차 적용**

이 샘플에서는 테이블 형식 결과에서 하나의 루트 JSON 개체가 여러 레코드로 변환하는 것이 예상됩니다. 다음 내용을 포함하는 JSON 파일이 있고  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
배열 내부의 데이터를 평면화하고 일반적인 루트 정보로 크로스 조인하여 Azure SQL 테이블에 데이터를 다음 형식으로 복사하려는 경우

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

**JsonFormat** 형식의 입력 데이터 집합은 다음과 같이 정의됩니다(관련 부분만 있는 부분 정의). 더 구체적으로 살펴보면 다음과 같습니다.

- `structure` 섹션은 테이블 형식 데이터로 변환하는 동안 사용자 지정된 열 이름과 해당 데이터 형식을 정의합니다. 이 섹션은 열 매핑을 수행할 필요가 없는 경우를 제외하고는 **선택적**입니다. 자세한 내용은 [사각형 데이터 집합의 구조 정의 지정](#specifying-structure-definition-for-rectangular-datasets) 섹션을 참조하세요.
- `jsonNodeReference`는 **배열** 주문 줄에서 동일한 패턴을 사용하는 개체에서 데이터를 반복하고 추출하도록 지정합니다.
- `jsonPathDefinition`은 데이터를 추출할 위치를 나타내는 각 열의 JSON 경로를 지정합니다. 다음 예제에서 "ordernumber", "orderdate" 및 "city"는 "$."로 시작하는 JSON 경로를 사용하는 루트 개체 아래에 있지만, "order_pd"및 "order_price"는 "$.를 사용하지 않는 배열 요소에서 파생된 경로로 정의됩니다."

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**주의 사항:**

* `structure` 및 `jsonPathDefinition`이 Data Factory 데이터 집합에 정의되지 않은 경우 복사 작업은 첫 번째 개체에서 스키마를 검색하고 전체 개체를 평면화합니다.
* JSON 입력에 배열이 있는 경우 복사 작업은 기본적으로 전체 배열 값을 문자열로 변환합니다. `jsonNodeReference` 및/또는 `jsonPathDefinition`을 사용하여 데이터를 추출하거나 `jsonPathDefinition`에서 지정하지 않고 건너뛸 수 있습니다.
* 동일한 수준에 중복된 이름이 있는 경우 복사 활동에서는 마지막 이름이 선택됩니다.
* 속성 이름은 대/소문자를 구분합니다. 이름은 같지만 대/소문자가 다른 두 속성은 별도의 두 속성으로 간주됩니다.

**사례 2: JSON 파일에 데이터 쓰기**

SQL Database에 아래 테이블이 있고

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

각 레코드에 대해 아래 형식으로 JSON 개체에 쓰려고 하는 경우
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

**JsonFormat** 형식의 출력 데이터 집합은 다음과 같이 정의됩니다(관련 부분만 있는 부분 정의). 더 구체적으로 말하면 `structure` 섹션은 대상 파일의 사용자 지정된 속성 이름을 정의하며 `nestingSeparator`(기본값: ".")는 이름에서 중첩 레이어를 식별하는 데 사용됩니다. 이 섹션은 원본 열 이름과 비교하여 속성 이름을 변경하거나 일부 속성을 중첩하지 않으려는 경우를 제외하고는 **선택적**입니다.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

### <a name="specifying-avroformat"></a>AvroFormat 지정
Avro 파일을 구문 분석하거나 데이터를 Avro 형식으로 쓰려면 `format` `type` 속성을 **AvroFormat**으로 설정합니다. typeProperties 섹션 내의 Format 섹션에서는 속성을 지정할 필요가 없습니다. 예제:

```json
"format":
{
    "type": "AvroFormat",
}
```

Hive 테이블에서 Avro 형식을 사용하려는 경우 [Apache Hive의 자습서](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)를 참조하세요.

다음 사항에 유의하세요.  

* [복합 데이터 형식](http://avro.apache.org/docs/current/spec.html#schema_complex)은 지원되지 않습니다(레코드, 열거형, 배열, 매핑, 공용 구조체 및 고정).

### <a name="specifying-orcformat"></a>OrcFormat 지정
ORC 파일을 구문 분석하거나 데이터를 ORC 형식으로 쓰려면 `format` `type` 속성을 **OrcFormat**으로 설정합니다. typeProperties 섹션 내의 Format 섹션에서는 속성을 지정할 필요가 없습니다. 예제:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 ORC 파일을 **있는 그대로** 복사하지 않는 경우에는 게이트웨이 컴퓨터에 JRE 8(Java Runtime Environment)을 설치해야 합니다. 64비트 게이트웨이에는 64비트 JRE가 필요하고 32비트 게이트웨이에는 32비트 JRE가 필요합니다. [여기서](http://go.microsoft.com/fwlink/?LinkId=808605)두 버전이 모두 제공됩니다. 적합한 버전을 선택하세요.
>
>

다음 사항에 유의하세요.

* 복합 데이터 형식(구조체, 맵, 목록, 공용 구조체)은 지원되지 않습니다.
* ORC 파일에는 3개의 [압축 관련 옵션](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)(NONE, ZLIB, SNAPPY)이 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 ORC 파일에 쓸 때 Data Factory는 ORC에 대한 기본값인 ZLIB를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다.

### <a name="specifying-parquetformat"></a>ParquetFormat 지정
Parquet 파일을 구문 분석하거나 데이터를 Parquet 형식으로 쓰려면 `format` `type` 속성을 **ParquetFormat**으로 설정합니다. typeProperties 섹션 내의 Format 섹션에서는 속성을 지정할 필요가 없습니다. 예제:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 Parquet 파일을 **있는 그대로** 복사하지 않는 경우에는 게이트웨이 컴퓨터에 JRE 8(Java Runtime Environment)을 설치해야 합니다. 64비트 게이트웨이에는 64비트 JRE가 필요하고 32비트 게이트웨이에는 32비트 JRE가 필요합니다. [여기서](http://go.microsoft.com/fwlink/?LinkId=808605)두 버전이 모두 제공됩니다. 적합한 버전을 선택하세요.
>
>

다음 사항에 유의하세요.

* 복합 데이터 형식(MAP, LIST)은 지원되지 않습니다.
* Parquet 파일에는 압축 관련 옵션인 NONE, SNAPPY, GZIP 및 LZO가 포함되어 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 Parquet 파일에 쓸 때 Data Factory는 Parquet 서식에 대한 기본값인 SNAPPY를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다.
