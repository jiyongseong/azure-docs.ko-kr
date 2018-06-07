---
title: Azure Search 여러 언어 인덱싱 | Microsoft Docs
description: Azure Search는 Lucene 및 Microsoft 제공 자연어 처리 기술을 통해 56개 언어를 지원합니다.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.openlocfilehash: 278539a2451eb15c7148b75497798e81f5370a57
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Azure Search에서 다국어 문서에 대한 인덱스 만들기
> [!div class="op_single_selector"]
>
> * [포털](search-language-support.md)
> * [REST (영문)](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

인덱스 정의의 검색 가능한 필드에서 한 가지 속성만 설정하면 간단히 언어 분석기를 활용할 수 있습니다. 이제 포털에서 이 단계를 수행할 수 있습니다.

다음은 사용자가 인덱스 스키마를 정의할 수 있는 Azure Search의 Azure Portal 블레이드의 스크린샷입니다. 이 블레이드로부터 사용자가 모든 필드를 만들고 각각에 대한 분석기 속성을 설정할 수 있습니다.

> [!IMPORTANT]
> 처음부터 새 인덱스를 만들거나 새 필드를 기존 인덱스에 추가할 때처럼 필드 정의 중에 언어 분석기를 설정하기만 하면 됩니다. 필드를 만들 때 분석기를 포함한 모든 속성을 완전히 지정했는지 확인합니다. 변경 내용을 저장한 후에는 속성을 편집하거나 분석기 형식을 변경할 수 없습니다.
>
>

## <a name="define-a-new-field-definition"></a>새 필드 정의
1. [Azure Portal](https://portal.azure.com)에 로그인하고 검색 서비스의 서비스 블레이드를 엽니다.
2. 서비스 대시보드 위쪽의 명령 모음에 있는 **인덱스 추가** 를 클릭하여 새 인덱스를 시작하거나, 기존 인덱스를 여러 기존 인덱스에 추가하는 새 필드에 대해 분석기를 설정합니다.
3. 언어 분석기 선택을 위한 분석기 탭 등, 인덱스 스키마 정의 옵션을 제공하는 필드 블레이드가 나타납니다.
4. 필드에서 이름을 입력하고 데이터 형식을 선택하며 필드를 전체 텍스트 검색 가능, 검색 결과에서 검색 가능, 패싯 탐색 구조에서 사용 가능, 정렬 가능 등으로 표시하는 속성을 설정하여 필드 정의를 시작합니다.
5. 다음 필드로 이동하기 전에 **분석기** 탭을 엽니다.

![][1]
*분석기를 선택하려면 필드 블레이드의 분석기 탭을 클릭합니다.*

## <a name="choose-an-analyzer"></a>분석기 선택
1. 스크롤하여 정의하는 필드를 찾습니다.
2. 필드를 **검색 가능**으로 표시하지 않은 경우 지금 확인란을 클릭하여 검색 가능으로 표시합니다.
3. 분석기 영역을 클릭하여 사용 가능한 분석기 목록을 표시합니다.
4. 사용할 분석기를 선택합니다.

![][2]
*각 필드에 대해 지원되는 분석기 중 하나를 선택합니다.*

기본적으로 모든 검색 가능 필드에서는 언어 중립적인 [표준 Lucene 분석기](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) 를 사용합니다. 지원되는 전체 분석기 목록을 보려면 [Azure Search의 언어 지원](https://msdn.microsoft.com/library/azure/dn879793.aspx)을 참조하세요.

필드에 대해 언어 분석기를 선택한 후에는 해당 필드의 모든 인덱싱 및 검색 요청에 해당 분석기를 사용합니다. 다른 분석기를 사용하는 여러 필드에 대해 쿼리가 실행된 경우 이 쿼리는 각 필드에 부합하는 분석기를 통해 독립적으로 처리됩니다.

전세계의 많은 웹 및 모바일 응용 프로그램 서버 사용자가 각기 다른 언어를 사용합니다. 지원되는 각각의 언어에 대해 필드를 만들면 이러한 시나리오를 위한 인덱스를 정의할 수 있습니다.

![][3]
*지원되는 각 언어에 대한 설명 필드가 있는 인덱스 정의*

쿼리를 실행한 에이전트의 언어를 아는 경우 검색 요청의 범위를 **searchFields** 쿼리 매개 변수를 사용하여 특정 필드로 지정할 수 있습니다. 다음 쿼리는 폴란드어로 된 설명에 대해서만 실행됩니다.

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

**탐색기 검색** 을 사용하여 위에 표시된 것과 유사한 쿼리를 붙여 넣으면 포털에서 인덱스를 쿼리할 수 있습니다. 탐색기 검색은 서비스 블레이드의 명령 모음에서 사용할 수 있습니다. 세부 정보는 [포털에서 Azure Search 인덱스 쿼리](search-explorer.md) 를 참조하세요.

간혹 쿼리를 실행한 에이전트의 언어를 알지 못할 수 있습니다. 이 경우 모든 필드에 대해 동시에 쿼리를 실행할 수 있습니다. 필요한 경우 [평가 프로필](https://msdn.microsoft.com/library/azure/dn798928.aspx)을 사용하여 특정 언어로 된 결과에 대한 우선 순위를 정의할 수 있습니다. 아래 예에서는 영어 설명에서 찾은 일치 항목이 폴란드 및 프랑스어 항목 일치보다 상대적으로 높은 점수를 받게 됩니다.

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

.NET 개발자인 경우 [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search)를 사용하여 언어 분석기를 구성할 수 있습니다. 최신 릴리스에는 Microsoft 언어 분석기에 대한 지원도 포함되어 있습니다.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
