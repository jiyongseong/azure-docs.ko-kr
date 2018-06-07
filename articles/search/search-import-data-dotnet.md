---
title: 데이터 업로드(.NET - Azure Search) | Microsoft Docs
description: .NET SDK를 사용하여 Azure Search에서 인덱스에 데이터를 업로드하는 방법을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.openlocfilehash: fb8fb153c7393af0e084cb96ee2f5e82fba5b220
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>.NET SDK를 사용하여 Azure Search에 데이터 업로드
> [!div class="op_single_selector"]
> * [개요](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST (영문)](search-import-data-rest-api.md)
> 
> 

이 문서에서는 [Azure Search .NET SDK](https://aka.ms/search-sdk) 를 사용하여 Azure Search 인덱스에 데이터를 가져오는 방법을 보여줍니다.

이 연습을 시작하기 전에 [Azure Search 인덱스를 만들어야](search-what-is-an-index.md)합니다. 이 문서에서는 [.NET SDK를 사용하여 Azure Search 인덱스 만들기](search-create-index-dotnet.md#CreateSearchServiceClient)에서 보여준 것처럼 `SearchServiceClient` 개체를 이미 만들었다고 가정합니다.

> [!NOTE]
> 이 문서의 모든 샘플 코드는 C#으로 작성되었습니다. 전체 소스 코드는 [GitHub](http://aka.ms/search-dotnet-howto)를 참조하세요. 좀 더 구체적인 샘플 코드 연습은 [Azure Search .NET SDK](search-howto-dotnet-sdk.md)를 참조하세요.

.NET SDK를 사용하여 인덱스에 문서를 푸시하기 위해 다음을 수행해야 합니다.

1. `SearchIndexClient` 개체를 만들어서 검색 인덱스에 연결합니다.
2. 추가, 수정 또는 삭제하려는 문서를 포함하는 `IndexBatch`을 만듭니다.
3. `SearchIndexClient`의 `Documents.Index` 메서드를 호출하여 검색 인덱스에 `IndexBatch`를 전송합니다.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>SearchIndexClient 클래스의 인스턴스 만들기
Azure Search .NET SDK를 사용하여 인덱스에 데이터를 가져오려면 `SearchIndexClient` 클래스의 인스턴스를 만들어야 합니다. 이 인스턴스를 직접 생성할 수 있지만 `Indexes.GetClient` 메서드를 호출하는 `SearchServiceClient` 인스턴스가 있는 경우 더 간단합니다. 예를 들어 다음은 `serviceClient`라는 `SearchServiceClient`에서 "호텔"이라는 인덱스에 `SearchIndexClient`를 가져오는 방법입니다.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> 일반적인 검색 응용 프로그램에서 인덱스 관리 및 채우기는 검색 쿼리와는 별도의 구성 요소에 의해 처리됩니다. `Indexes.GetClient`는 다른 `SearchCredentials`를 제공하는 문제를 피할 수 있으므로 인덱스를 생성하기에 편리한 방법입니다. 이는 새 `SearchIndexClient`에 `SearchServiceClient`을(를) 만드는 데 사용하는 관리 키를 눌러 수행됩니다. 그러나 쿼리를 실행하는 일부 응용 프로그램의 경우, `SearchIndexClient` 을(를) 직접 만들어 관리 키 대신에 쿼리 키에서 전달하는 것이 더 낫습니다. 이는 [최소 권한의 원칙](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 과 일치하고 응용 프로그램을 더욱 안전하게 하는 데 도움이 됩니다. 관리 키와 쿼리 키에 대한 자세한 내용은 [Azure Search REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)에서 찾을 수 있습니다.
> 
> 

`SearchIndexClient`에는 `Documents` 속성이 있습니다. 이 속성은 인덱스에서 문서를 추가, 수정, 삭제 또는 쿼리해야 하는 모든 메서드를 제공합니다.

## <a name="decide-which-indexing-action-to-use"></a>사용할 인덱싱 동작 결정
.NET SDK를 사용하여 데이터를 가져오려면 데이터를 `IndexBatch` 개체에 패키지로 만들어야 합니다. `IndexBatch`는 `IndexAction` 개체의 컬렉션을 캡슐화하며 각각은 Azure Search가 해당 문서에 대해 수행할 작업(업로드, 병합, 삭제 등)을 알려주는 문서 및 속성을 포함합니다. 아래에서 어떤 작업을 선택하는지에 따라 특정 필드는 각 문서에 포함되어야 합니다.

| 조치 | 설명 | 각 문서에 대해 필요한 필드 | 메모 |
| --- | --- | --- | --- |
| `Upload` |`Upload` 작업은 새 문서는 삽입하고 기존 문서는 업데이트/교체하는 "upsert"와 비슷합니다. |키, 더하기 정의하려는 기타 필드 |기존 문서를 업데이트/교체하는 경우 요청에 지정되지 않은 필드는 해당 필드를 `null`로 설정합니다. 필드가 이전에 null이 아닌 값으로 설정된 경우에 발생합니다. |
| `Merge` |기존 문서를 지정한 필드로 업데이트합니다. 인덱스에 문서가 없으면 병합이 실패합니다. |키, 더하기 정의하려는 기타 필드 |문서의 기존 필드는 병합에서 지정하는 필드로 바뀝니다. 여기에는 `DataType.Collection(DataType.String)`형식 필드가 포함됩니다. 예를 들어 값이 `["budget"]`인 `tags` 필드가 포함되어 있는 문서에서 `tags`에 대해 `["economy", "pool"]` 값과의 병합을 실행하면 `tags` 필드의 최종 값은 `["economy", "pool"]`이 됩니다. `["budget", "economy", "pool"]`이 아닙니다. |
| `MergeOrUpload` |이 작업은 지정된 키를 포함하는 문서가 인덱스에 이미 있는 경우 `Merge`와 비슷하게 작동합니다. 문서가 없는 경우 새 문서가 있는 `Upload` 와 비슷하게 작동합니다. |키, 더하기 정의하려는 기타 필드 |- |
| `Delete` |삭제 시에는 지정된 문서를 인덱스에서 제거합니다. |키만 |키 필드 외의 지정한 모든 필드는 무시됩니다. 문서에서 개별 필드를 제거하려는 경우 대신 `Merge` 를 사용하고 필드를 명시적으로 Null로 설정합니다. |

다음 섹션에서 보여준 것처럼 다양한 정적 메서드인 `IndexBatch` 및 `IndexAction` 클래스와 함께 사용하려는 동작을 지정할 수 있습니다.

## <a name="construct-your-indexbatch"></a>IndexBatch 생성
이제 문서에서 수행되는 작업을 알고 나면 `IndexBatch`를 생성할 준비가 되었습니다. 아래 예제에서는 몇 가지 다른 작업으로 배치를 만드는 방법을 보여줍니다. 이 예제에서는 "호텔" 인덱스에 있는 문서에 매핑되는 `Hotel` 이라는 사용자 지정 클래스를 사용합니다.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

이 경우에 `IndexAction` 클래스에서 호출된 메서드에 지정된 대로 `Upload`, `MergeOrUpload` 및 `Delete`를 검색 작업으로 사용합니다.

이 예제 "호텔" 인덱스는 문서 수로 채워진다고 가정합니다. `MergeOrUpload`를 사용하는 경우 가능한 문서 필드를 모두 지정하지 않는 방법 및 `Delete`를 사용하는 경우 문서 키(`HotelId`)를 지정하는 방법을 참고합니다.

또한 단일 인덱싱 요청에서 최대 1000개의 문서를 포함할 수 있습니다.

> [!NOTE]
> 이 예제에서는 다른 문서에 다른 동작을 적용하고 있습니다. 배치에서 모든 문서에 `IndexBatch.New`를 호출하는 대신 동일한 작업을 수행하려는 경우 `IndexBatch`라는 기타 정적 메서드를 사용할 수 있습니다. 예를 들어 `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` 또는 `IndexBatch.Delete`를 호출하여 배치를 만들 수 있습니다. 이러한 메서드는 `IndexAction` 개체 대신 문서의 컬렉션을 가져옵니다(이 예제에서 `Hotel` 유형의 개체).
> 
> 

## <a name="import-data-to-the-index"></a>인덱스에 데이터 가져오기
`IndexBatch` 개체를 초기화했다면 `SearchIndexClient` 개체에서 `Documents.Index`를 호출하여 인덱스로 보낼 수 있습니다. 다음 예제에서는 수행해야 하는 몇 가지 추가 단계 뿐만 아니라 `Index`를 호출하는 방법을 보여줍니다.

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

`Index` 메서드에 대한 호출과 관련된 `try`/`catch`를 참고합니다. catch 블록은 인덱싱에 대한 중요한 오류 사례를 다룹니다. Azure Search 서비스가 일괄 처리에서 문서 일부를 인덱싱하는데 실패하는 경우 `Documents.Index`에 의해 `IndexBatchException`이(가) 발생합니다. 이는 부하가 높은 상태에서 서비스되는 동안에 문서를 인덱싱하는 경우 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 응용 프로그램의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

마지막으로 2초 동안 위의 예에서 코드가 지연됩니다. Azure Search 서비스에서 인덱싱이 비동기적으로 발생하기 때문에, 샘플 응용 프로그램은 문서 검색을 위해 잠시 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 응용 프로그램에서는 일반적으로 지연만 필요합니다.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK가 문서를 처리하는 방법
Azure Search.NET SDK가 어떻게 `Hotel` 와(과) 같은 사용자 정의 클래스의 인스턴스를 업로드할 수 있는지 궁금할 수 있습니다. 질문에 대답하려면 [.NET SDK를 사용하여 Azure Search 인덱스 만들기](search-create-index-dotnet.md#DefineIndex)에 정의된 인덱스 스키마에 매핑하는 `Hotel` 클래스를 살펴보겠습니다.

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

먼저 주목할 것은 `Hotel`의 각 공용 속성이 인덱스 정의의 필드와 일치하지만 한 가지 중요한 차이가 있습니다. 각 필드의 이름은 소문자("카멜식 대/소문자")로 시작하지만, `Hotel`의 각 공용 속성 이름은 대문자 문자("파스칼식 대/소문자")로 시작합니다. 이것은 대상 스키마가 응용 프로그램 개발자의 제어 범위를 벗어난 데이터 바인딩을 수행하는 .NET 응용 프로그램의 일반적인 시나리오입니다. 카멜식 대/소문자 속성으로 이름을 지정하면 .NET 이름 지정 지침을 위반하지 않고 속성 이름을 자동으로 `[SerializePropertyNamesAsCamelCase]` 특성을 지닌 카멜식 대/소문자에 매핑하도록 SDK에 명령할 수 있습니다.

> [!NOTE]
> Azure Search .NET SDK는 [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) 라이브러리를 사용하여 사용자 지정 모델 개체를 JSON과 직렬화 및 deserialize합니다. 필요한 경우 직렬화를 사용자 지정할 수 있습니다. 자세한 내용은 [JSON.NET으로 직렬화 사용자 지정](search-howto-dotnet-sdk.md#JsonDotNet)를 참조하세요. 한 가지 예는 위의 샘플 코드에 있는 `DescriptionFr` 속성의 `[JsonProperty]` 특성을 사용하는 것입니다.
> 
> 

`Hotel` 클래스에 대해 두 번째로 중요한 부분은 공용 속성의 데이터 유형입니다. 이러한 속성의 .NET 유형은 인덱스 정의의 동등한 필드 유형에 매핑합니다. 예를 들어, `Category` 문자열 속성은 `DataType.String` 유형인 `category` 필드에 매핑됩니다. `bool?` 및 `DataType.Boolean`, `DateTimeOffset?` 및 `DataType.DateTimeOffset` 사이에는 유사한 유형 매핑이 있습니다. 유형 매핑에 대한 특정 규칙은 [Azure Search .NET SDK 참조](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)에 `Documents.Get` 메서드로 문서화됩니다.

고유한 클래스를 문서로 사용하는 이 기능은 양방향으로 작동합니다. 또한 [다음 문서](search-query-dotnet.md)에서 보여준 것처럼 검색 결과를 검색하고 SDK가 자동으로 사용자가 선택한 유형으로 deserialize하도록 할 수 있습니다.

> [!NOTE]
> Azure Search.NET SDK는 필드 이름을 필드 값에 매핑하는 키/값인 `Document` 클래스를 사용하여 동적 유형의 문서도 지원합니다. 디자인 타임에서 인덱스 스키마를 알 수 없거나 특정 모델 클래스에 바인딩하기 불편한 시나리오에서 유용합니다. 문서를 처리하는 SDK의 모든 메서드에는 `Document` 클래스와 연동하는 오버로드와 제네릭 유형 매개 변수를 취하는 강력한 유형의 오버로드가 있습니다. 후자는 이 문서의 샘플 코드에서 사용됩니다.
> 
> 

**Null 허용 데이터 형식을 사용해야 하는 이유**

고유의 모델 클래스를 Azure Search 인덱스에 매핑하도록 설계하는 경우 `bool` 및 `int` 등과 같은 값 유형의 속성을 Null이 허용되도록 선언하는 것이 좋습니다(예: `bool` 대신 `bool?`). Null이 허용되지 않는 속성을 사용하는 경우 인덱스의 문서가 해당 필드에 대해 Null 값을 포함하지 않도록 **보장** 해야 합니다. SDK와 Azure Search 서비스 모두 이를 적용하는 데 활용할 수 없습니다.

이것은 가상의 문제가 아닙니다. `DataType.Int32` 형식인 기존 인덱스에 새 필드를 추가하는 시나리오를 가정하겠습니다. 인덱스 정의를 업데이트한 후 모든 문서는 해당하는 새 필드에 대해 Null 값을 포함하게 됩니다(Azure Search에서 모든 형식은 Null을 허용하기 때문). 그런 다음 해당 필드에 대해 Null이 허용되지 않는 `int` 속성으로 모델 클래스를 사용하는 경우 문서를 검색하려고 시도할 때 다음과 같은 `JsonSerializationException`이 발생합니다.

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

이러한 이유로 모델 클래스에는 Null을 허용하는 형식을 사용하는 것이 가장 좋습니다.

## <a name="next-steps"></a>다음 단계
Azure Search 인덱스를 채운 후에 문서를 검색하기 위해 쿼리를 발급하기 시작할 준비가 되었습니다. 세부 정보는 [Azure Search 인덱스 쿼리](search-query-overview.md) 를 참조하세요.

