---
title: 인덱스 만들기(포털 - Azure Search) | Microsoft Docs
description: Azure Portal을 사용하여 인덱스를 만듭니다.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: quickstart
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: 722f1eb989fb8c160def4024b1aa967a47b87697
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203872"
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Search 인덱스 만들기

Azure Portal에서 기본 제공되는 인덱스 디자이너를 사용하여 Azure Search 서비스에서 실행할 [검색 인덱스](search-what-is-an-index.md)를 프로토타입화하거나 만듭니다. 

또는 [.NET](search-create-index-dotnet.md) 또는 [REST](search-create-index-rest-api.md) API를 사용하여 인덱스를 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) 및 [Azure Search 서비스](search-create-service-portal.md)를 사용하는 것으로 가정합니다.  

## <a name="find-your-search-service"></a>검색 서비스 찾기
1. Azure Portal 페이지에 로그인하고 [구독에 대한 검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 검토합니다.
2. Azure Search 서비스를 선택합니다.

## <a name="name-the-index"></a>인덱스 이름 지정

1. 페이지 맨 위에 있는 명령 모음에서 **인덱스 추가** 단추를 클릭합니다.
2. Azure Search 인덱스를 명명합니다. 
   * 문자로 시작합니다.
   * 소문자, 숫자 또는 대시(“-”)만 사용합니다.
   * 이름은 60자로 제한합니다.

  인덱스 이름은 인덱스로의 연결 및 Azure Search REST API에서 HTTP 요청을 보내는 데 사용된 끝점 URL의 일부가 됩니다.

## <a name="define-the-fields-of-your-index"></a>인덱스의 필드 정의

인덱스 컴퍼지션은 인덱스에서 검색 가능한 데이터를 정의하는 *필드 컬렉션*을 포함합니다. 구체적으로 말하면, 개별적으로 업로드하는 문서 구조를 지정합니다. 필드 컬렉션에는 필드가 사용되는 방식을 결정하는 인덱스 특성과 함께 이름 및 형식이 지정된 필수 및 옵션 필드가 포함됩니다.

1. **인덱스 추가** 블레이드에서 **필드 >** 를 클릭하여 필드 정의 블레이드를 엽니다. 

2. Edm.String 형식으로 생성된 *키* 필드를 허용합니다. 기본적으로 필드 이름은 *id*로 지정되지만 문자열이 [명명 규칙](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)을 충족하기만 하면 이름을 변경할 수 있습니다. 키 필드는 모든 Azure Search 인덱스에 필수이며 문자열이어야 합니다.

3. 업로드할 문서를 완전히 지정하는 필드를 추가합니다. 문서는 *id*, *호텔 이름*, *주소*, *도시* 및 *지역*으로 구성되며 인덱스에서 각 항목에 해당 필드를 만듭니다. 특성 설정에 대한 도움말은 [아래 섹션의 디자인 지침](#design)을 검토하세요.

4. 필요에 따라 필터 식에 내부적으로 사용되는 필드를 추가합니다. 검색 작업에서 필드를 제외하도록 필드의 특성을 설정할 수 있습니다.

5. 완료되면 **확인**을 클릭하여 인덱스를 저장하고 만듭니다.

## <a name="tips-for-adding-fields"></a>필드 추가를 위한 팁

포털에서 인덱스를 만들 경우 키보드를 많이 사용해야 합니다. 이 워크플로에 따라 단계를 최소화합니다.

1. 먼저, 이름을 입력하고 데이터 형식을 설정하여 필드 목록을 작성합니다.

2. 다음으로, 각 특성의 맨 위에 있는 확인란을 사용하여 모든 필드에 대한 설정을 일괄적으로 사용한 후 필요하지 않은 일부 필드의 확인란은 선택적으로 해제합니다. 예를 들어 문자열 필드는 일반적으로 검색할 수 있습니다. 이와 같이 **조회 가능** 및 **검색 가능**을 클릭하여 검색 결과에서 필드 값을 반환할 수 있을 뿐만 아니라 해당 필드에서 전체 텍스트 검색을 허용할 수 있습니다. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>특성 설정을 위한 디자인 지침

언제든지 새 필드를 추가할 수 있지만 기존 필드 정의는 인덱스 수명 동안 잠겨 있습니다. 이러한 이유로 개발자는 일반적으로 포털을 사용하여 간단한 인덱스를 만들거나 아이디어를 테스트하거나 포털 페이지를 사용하여 설정을 조회합니다. 인덱스를 쉽게 다시 작성할 수 있도록 코드 기반 접근 방식을 따르는 경우 인덱스 대한 빈번한 반복 디자인이 보다 효율적입니다.

인덱스를 저장하기 전에 분석기 및 확인기를 필드와 연결합니다. 각 탭 페이지를 클릭하여 언어 분석기 및 확인기를 인덱스 정의에 추가해야 합니다.

문자열 필드는 보통 **검색 가능** 및 **조회 가능**으로 표시됩니다.

검색 결과 범위를 좁히는 데 사용되는 필드는 **정렬 가능**, **필터링 가능** 및 **패싯 가능**이 있습니다.

필드 특성에 따라 필드가 사용되는 방식(즉, 전체 텍스트 검색, 패싯 탐색, 정렬 작업 등)이 결정됩니다. 아래 표에서 각 특성에 대해 설명합니다.

|특성|설명|  
|---------------|-----------------|  
|**검색 가능**|전체 텍스트 검색 가능하며, 인덱싱 중에 단어 분리 등의 어휘 분석이 적용됩니다. 검색 가능 필드를 “sunny day” 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 “sunny”와 “day”로 분할됩니다. 자세한 내용은 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)을 참조하세요.|  
|**필터링 가능**|**$filter** 쿼리에서 참조됩니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필터링 가능 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 f를 “sunny day”로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. |  
|**정렬 가능**|기본적으로 시스템은 점수에 따라 결과를 정렬하지만 문서에 있는 필드를 기반으로 정렬하도록 구성할 수 있습니다. 형식이 `Collection(Edm.String)`인 필드는 **정렬 가능**으로 설정할 수 없습니다. |  
|**패싯 가능**|일반적으로 카테고리별 적중 횟수가 포함된 검색 결과를 표시하는 데 사용됩니다(예: 특정 도시의 호텔). 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. **필터링 가능**, **정렬 가능** 또는 **패싯 가능**이고 형식이 `Edm.String`인 필드는 최대 32KB일 수 있습니다. 자세한 내용은 [인덱스 만들기(REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.|  
|**key**|인덱스 내의 문서에 대한 고유 식별자입니다. 정확히 하나의 필드를 키 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다.|  
|**조회 가능**|검색 결과에서 필드를 반환할 수 있는지 여부를 결정합니다. *이익률* 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `true` for `key` 로 설정해야 합니다.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>예제 API 섹션에 사용된 호텔 인덱스 만들기

Azure Search API 설명서에는 간단한 *호텔* 인덱스를 제공하는 코드 예제가 들어 있습니다. 아래 스크린샷에서는 인덱스 정의 중에 지정된 프랑스어 분석기를 포함한 인덱스 정의를 확인할 수 있으며 이것은 포털에서 연습으로 다시 만들 수 있습니다.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>다음 단계

Azure Search 인덱스를 만든 후에는 다음 단계로 이동할 수 있습니다. [검색 가능한 데이터를 인덱스에 업로드](search-what-is-data-import.md).

또는 인덱스를 보다 자세히 살펴볼 수도 있습니다. 필드 컬렉션 외에도 인덱스는 분석기, 확인기, 점수 매기기 프로필 및 CORS 설정도 지정합니다. 포털은 필드, 분석기 및 확인기와 같은 가장 일반적인 요소를 정의하기 위한 탭 페이지를 제공합니다. 다른 요소를 만들거나 수정하려면 REST API 또는 .NET SDK를 만들면 됩니다.

## <a name="see-also"></a>참고 항목

 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)  
 [Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

