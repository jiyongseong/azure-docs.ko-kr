---
title: 인덱스 만들기(.NET API - Azure Search) | Microsoft Docs
description: Azure Search .NET SDK를 사용하여 코드에 인덱스를 만듭니다.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7e7d1f8110d8470fe7596633563529f397c5551e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>.NET SDK를 사용하여 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [개요](search-what-is-an-index.md)
> * [포털](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST (영문)](search-create-index-rest-api.md)
> 
> 

이 문서는 [Azure Search .NET SDK](https://aka.ms/search-sdk)를 사용하여 Azure Search [인덱스](https://docs.microsoft.com/rest/api/searchservice/Create-Index)를 만드는 프로세스를 안내합니다.

이 가이드를 수행하고 인덱스를 만들기 전에 이미 [Azure Search 서비스를 만들어야](search-create-service-portal.md)합니다.

> [!NOTE]
> 이 문서의 모든 샘플 코드는 C#으로 작성되었습니다. 전체 소스 코드는 [GitHub](http://aka.ms/search-dotnet-howto)를 참조하세요. 좀 더 구체적인 샘플 코드 연습은 [Azure Search .NET SDK](search-howto-dotnet-sdk.md)를 참조하세요.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search 서비스의 관리 API 키 식별
Azure Search 서비스를 프로비전했다면 .NET SDK를 사용하여 서비스 끝점에 대한 요청을 실행할 준비가 거의 된 것입니다. 먼저 프로비전한 검색 서비스에 대해 생성된 관리 API 키 중 하나가 있어야 합니다. .NET SDK는 서비스에 대한 모든 요청에 대해 이 API 키를 전송합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. 서비스의 API 키를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Search 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

* 기본 및 보조 *관리 키* 는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
* *쿼리 키* 는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스를 만들기 위해 기본 또는 보조 관리 키를 사용할 수 있습니다.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>SearchServiceClient 클래스의 인스턴스 만들기
Azure Search .NET SDK 사용을 시작하려면 `SearchServiceClient` 클래스의 인스턴스를 만들어야 합니다. 이 클래스에는 몇 가지 생성자가 있습니다. 검색 서비스 이름과 `SearchCredentials` 개체를 매개 변수로 사용할 생성자입니다. `SearchCredentials` 는 API 키를 래핑합니다.

아래 코드에서는 응용 프로그램의 구성 파일([샘플 응용 프로그램](http://aka.ms/search-dotnet-howto)의 경우 `appsettings.json`)에 저장된 API 키와 검색 서비스 이름의 값을 사용하여 새 `SearchServiceClient`를 만듭니다.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient`에는 `Indexes` 속성이 있습니다. 이 속성은 Azure Search 인덱스를 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공합니다.

> [!NOTE]
> `SearchServiceClient` 클래스는 검색 서비스에 대한 연결을 관리합니다. 너무 많은 연결이 열리는 것을 방지하기 위해 되도록 응용 프로그램에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 해당 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Azure Search 인덱스를 정의합니다.
`Indexes.Create` 메서드에 대한 단일 호출이 인덱스를 만듭니다. 이 메서드는 Azure Search 인덱스를 정의하는 `Index` 개체를 매개 변수로 사용합니다. 다음과 같이 `Index` 개체를 만들고 초기화해야 합니다.

1. `Index` 개체의 `Name` 속성을 인덱스의 이름으로 설정합니다.
2. `Index` 개체의 `Fields` 속성을 `Field` 개체의 배열로 설정합니다. `Field` 개체를 만드는 가장 쉬운 방법은 `FieldBuilder.BuildForType` 메서드를 호출하여 형식 매개 변수에 대한 모델 클래스를 전달합니다. 모델 클래스에는 인덱스 필드에 매핑되는 속성이 있습니다. 이를 통해 모델 클래스의 인스턴스에 대한 검색 인덱스에서 문서를 바인딩할 수 있습니다.

> [!NOTE]
> 모델 클래스를 사용할 계획이 아니라면 `Field` 개체를 직접.만들어 여전히 인덱스를 정의할 수 있습니다. 필드 이름을 데이터 형식(또는 문자열 필드의 분석기)과 함께 생성자에 제공할 수 있습니다. `IsSearchable`, `IsFilterable` 등의 기타 속성을 설정할 수도 있습니다.
>
>

인덱스를 각 필드로 디자인하는 경우 검색 사용자 환경 및 비즈니스 요구를 [적합한 속성](https://docs.microsoft.com/rest/api/searchservice/Create-Index)으로 할당해야 한다는 점을 염두에 두는 것이 중요합니다. 이러한 속성은 어떤 검색 기능(전체 텍스트 검색의 필터링, 패싯, 정렬 등)이 어떤 필드에 적용될지를 제어합니다. 명시적으로 설정하지 않은 속성의 경우 사용자가 특별히 사용하도록 설정하지 않는 한 `Field` 클래스가 기본적으로 해당 검색 기능을 사용 안 함으로 설정합니다.

예를 들어 인덱스 "호텔"의 이름을 지정하고 모델 클래스를 사용해 필드를 정의했습니다. 모델 클래스의 각 속성에는 해당 인덱스 필드의 검색 관련 동작을 결정하는 특성이 있습니다. 모델 클래스는 다음과 같이 정의됩니다.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
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
}
```

응용 프로그램에서 사용되는 방법에 따라 각 속성에 대한 특성을 신중하게 선택했습니다. 예를 들어 호텔을 검색 중인 사용자에게는 `description` 필드에서의 키워드 일치 항목이 유용할 수 있으므로 `IsSearchable` 특성을 `Description` 속성에 추가하여 해당 필드에 대한 전체 텍스트 검색을 사용하도록 설정합니다.

`Key` 특성을 추가하여 형식 `string`의 인덱스에 정확히 하나의 필드가 *키* 필드로 지정되어야 합니다(위 예제의 `HotelId` 참조).

위의 인덱스 정의는 프랑스어 텍스트를 저장하기 위해서 `description_fr` 필드에 언어 분석기를 사용합니다. 언어 분석기에 대한 자세한 내용은 [언어 지원 항목](https://docs.microsoft.com/rest/api/searchservice/Language-support)뿐만 아니라 해당하는 [블로그 게시물](https://azure.microsoft.com/blog/language-support-in-azure-search/)을 참조하세요.

> [!NOTE]
> 기본적으로 모델 클래스 각 속성의 이름은 인덱스에서 해당 필드의 이름으로 사용 됩니다. 모든 속성 이름을 카멜식 대/소문자 필드 이름에 매핑하려는 경우 `SerializePropertyNamesAsCamelCase` 특성을 지닌 클래스를 표시합니다. 다른 이름에 매핑하려는 경우 `JsonProperty` 특성을 위의 `DescriptionFr` 속성처럼 사용할 수 있습니다. `JsonProperty` 특성은 `SerializePropertyNamesAsCamelCase` 특성보다 우선합니다.
> 
> 

모델 클래스를 정의했으므로 인덱스 정의를 아주 쉽게 만들 수 있습니다.

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>인덱스 만들기
`Index` 개체를 초기화한 후에는 `SearchServiceClient` 개체에서 `Indexes.Create`를 호출하여 간단히 인덱스를 만들 수 있습니다.

```csharp
serviceClient.Indexes.Create(definition);
```

요청이 성공하면 메서드가 정상적으로 반환됩니다. 잘못된 매개 변수 등 요청에 문제가 발생하면 메서드가 `CloudException`을 발생시킵니다.

인덱스 관련 작업을 완료하고 삭제하려는 경우 `SearchServiceClient`에서 `Indexes.Delete` 메서드를 호출합니다. 예를 들어 "호텔" 인덱스를 삭제하는 방법입니다.

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> 이 문서의 예제 코드는 간단히 하기 위해 Azure Search .NET SDK의 동기 메서드를 사용합니다. 확장성과 응답성이 유지하기 위해 사용 중인 응용 프로그램에서 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어 위의 예제에서 `Create` 및 `Delete` 대신 `CreateAsync` 및 `DeleteAsync`를 사용할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
Azure Search 인덱스를 만든 후에 데이터를 검색하기 시작할 수 있도록 [콘텐츠를 인덱스에 업로드](search-what-is-data-import.md) 할 준비가 되었습니다.

