---
title: Azure Search의 데이터 추출, 자연어 처리를 위한 Cognitive Search | Microsoft Docs
description: 인지 기술을 사용하여 Azure Search 인덱싱에서 검색 가능한 콘텐츠를 만들기 위한 데이터 추출, NLP(자연어 처리) 및 이미지 처리입니다.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: cce10ceb190ac90b57e77bfa5903b30b2c249a2c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942162"
---
# <a name="what-is-cognitive-search"></a>Cognitive Search란?

Cognitive search는 [Azure Search](search-what-is-azure-search.md)의 미리 보기 기능이며 인덱싱 워크로드에 AI가 추가된 미국 중남부와 유럽 서부의 모든 계층에서 사용할 수 있습니다. 인덱싱 과정의 데이터 추출, 자연어 처리 및 이미지 처리 작업은 구조화되지 않은 콘텐츠 또는 검색할 수 없는 콘텐츠의 잠재적인 정보를 찾아서 Azure Search에서 검색할 수 있도록 만듭니다.

AI 통합은 검색 인덱스로 이어지는 순차적인 프로세스를 통해 원본 문서를 보강하는 인지 기술(*cognitive skills*)을 통해 이루어집니다. 

![Cognitive Search 파이프라인 다이어그램](./media/cognitive-search-intro/cogsearch-architecture.png "Cognitive Search 파이프라인 개요")

인덱싱 중에 사용되는 기술은 미리 정의되거나 사용자 지정이 가능합니다.

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)은 Cognitive Services API에 사용되는 알고리즘과 동일한 AI 알고리즘을 기반으로 합니다. [Named Entity Recognition](cognitive-search-skill-named-entity-recognition.md), [Key Phrase Extraction](cognitive-search-skill-keyphrases.md) 및 [OCR](cognitive-search-skill-ocr.md)은 그 중 일부입니다. 

+ [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)은 필요한 특수 처리에 맞게 사용자가 개발할 수 있습니다. 사용자 지정 기술의 예에는 금융, 과학 출판물 또는 의약품과 같은 특정 도메인을 대상으로 하는 사용자 지정 엔터티 모듈 또는 문서 분류자가 있습니다.

> [!NOTE]
> Cognitive Search는 공개 미리 보기 상태이며, 현재 기술 집합 실행이 무료로 제공됩니다. 이 기능의 가격은 추후에 발표될 예정입니다.

## <a name="components-of-cognitive-search"></a>Cognitive Search의 구성 요소

Cognitive Search 파이프라인은 데이터 원본을 탐색하고 종단간 인덱스 프로세싱을 제공하는 [Azure Search *인덱서*](search-indexer-overview.md)를 기반으로 합니다. 기술이 인덱서에 연결되어 문서를 가로채서 사용자가 정의한 기술에 따라 문서를 보강합니다. 인덱스가 완료되면 [Azure Search에서 지원하는 모든 쿼리 유형](search-query-overview.md)에서 검색 요청을 통해 콘텐츠에 액세스할 수 있습니다.  인덱서를 처음 접하는 경우, 이 섹션의 단계별 안내를 참조하세요.

### <a name="source-data-and-document-cracking-phase"></a>원본 데이터 및 문서 크래킹 단계

파이프라인의 시작 부분에는 구조화되지 않은 텍스트 또는 텍스트가 아닌 콘텐츠(예: 이미지 및 스캔한 문서 JPEG 파일)가 있습니다. 데이터는 인덱서가 액세스할 수 있는 Azure 데이터 저장소 서비스에 있어야 합니다. 인덱서는 원본 문서에서 텍스트를 추출하기 위해 원본 문서를 "크래킹"할 수 있습니다.

![문서 크래킹 단계](./media/cognitive-search-intro/document-cracking-phase-blowup.png "문서 크래킹")

 지원되는 원본에는 Azure Blob Storage, Azure Table Storage, Azure SQL Database 및 Azure Cosmos DB가 포함됩니다. 텍스트 기반 콘텐츠는 PDF, Word, PowerPoint, CSV 파일 형식에서 추출할 수 있습니다. 전체 목록은 [지원되는 형식](search-howto-indexing-azure-blob-storage.md#supported-document-formats)을 참조하세요.

### <a name="cognitive-skills-and-enrichment-phase"></a>인지 기술 및 보강 단계

보강은 원자성 작업을 수행하는 인지 기술(*cognitive skills*)을 거칩니다. 예를 들어 PDF에서 텍스트 콘텐츠를 확보하면 엔터티 인식 언어 감지 또는 핵심 구 추출을 적용하여 원본에서 기본적으로 사용할 수 없는 새 필드를 인덱스에 생성합니다. 전체적으로 파이프라인에 사용된 기술 컬렉션을 기술 집합(*skillset*)이라고 합니다.  

![보강 단계](./media/cognitive-search-intro/enrichment-phase-blowup.png "보강 단계")

기술 집합은 [미리 정의된 인지 기술](cognitive-search-predefined-skills.md) 또는 사용자가 제공하고 기술 집합에 연결하는 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)을 기반으로 합니다. 기술 집합은 매우 간단하거나 복잡할 수 있으며 처리 유형뿐만 아니라 작업 순서도 결정합니다. 기술 집합 및 인덱서의 일부로 정의된 필드 매핑은 보강 파이프라인을 완벽하게 지정합니다. 이 모든 부분을 한데 모으는 방법에 대한 자세한 정보 [기술 집합 정의](cognitive-search-defining-skillset.md)를 참조하세요.

내부적으로 파이프라인은 보강된 문서 컬렉션을 생성합니다. 보강된 문서의 어느 부분을 검색 인덱스의 인덱싱 가능 필드에 매핑할지 결정할 수 있습니다. 예를 들어 핵심 구 추출과 엔터티 인식 기술을 적용한 경우 새 필드는 보강된 문서의 일부가 되며 인덱스의 필드에 매핑될 수 있습니다. 입/출력에 대한 자세한 내용은 [주석](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

### <a name="search-index-and-query-based-access"></a>검색 인덱스 및 쿼리 기반 액세스

처리가 완료되면 Azure Search에서 텍스트 검색이 가능한 보강된 문서로 구성된 검색 모음이 생성됩니다. [인덱스 쿼리](search-query-overview.md)는 개발자와 사용자가 파이프라인에서 생성된 보강된 콘텐츠에 액세스하는 방법입니다. 

![검색 아이콘이 있는 인덱스](./media/cognitive-search-intro/search-phase-blowup.png "검색 아이콘이 있는 인덱스")

인덱스는 Azure Search에서 만들 수 있는 다른 항목처럼, 사용자 지정 분석기를 보완하거나 유사 항목 검색 쿼리를 호출하거나 필터링된 검색을 추가하거나 채점 프로파일로 실험하여 검색 결과의 모양을 바꿀 수 있습니다.

인덱스는 채점 프로파일 및 동의어 맵과 같은 특정 인덱스에 연결된 필드, 특성 및 기타 구문을 정의하는 인덱스 스키마에서 생성됩니다. 인덱스가 정의되고 채워진 후에는 새 원본 문서나 업데이트된 원본 문서를 가져오기 위해 증분 방식으로 인덱싱할 수 있습니다. 특정 수정에는 전체 다시 빌드가 필요합니다. 스키마 디자인이 안정될 때까지 작은 데이터 집합을 사용해야 합니다. 자세한 내용은 [인덱스를 다시 빌드하는 방법](search-howto-reindex.md)을 참조하세요.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>주요 기능 및 개념

| 개념 | 설명| 링크 |
|---------|------------|-------|
| 기술 집합 | 기술 컬렉션이 포함된 최상위의 명명된 리소스입니다. 기술 집합은 보강 파이프라인입니다. 인덱서가 인덱싱을 수행하는 동안 호출됩니다. | [기술 집합 정의](cognitive-search-defining-skillset.md) |
| 인지 기술 | 보강 파이프라인의 원자성 변환입니다. 대개, 구조를 추출하거나 유추하는 구성 요소이므로 입력 데이터에 대한 이해를 높여줍니다. 거의 항상 출력은 텍스트 기반이며 처리는 자연어 처리이거나 이미지 입력으로부터 텍스트를 추출하거나 생성하는 이미지 처리입니다. 기술을 통한 출력은 인덱스의 필드로 매핑되거나 다운스트림 보강을 위한 입력으로 사용될 수 있습니다. 기술은 Microsoft가 미리 정의해서 제공하거나 사용자가 지정하여 생성하고 배포합니다. | [미리 정의된 기술](cognitive-search-predefined-skills.md) |
| 데이터 추출 | 광범위한 처리를 포함하지만 Cognitive Search에 속하는 명명된 엔터티 인식 기술은 해당 정보를 기본적으로 제공하지 않는 원본으로부터 데이터(엔터티)를 추출하는 데 가장 일반적으로 사용됩니다. | [명명된 엔터티 인식 기술](cognitive-search-skill-named-entity-recognition.md)| 
| 이미지 처리 | 이정표를 인식하는 기능, 이미지에서 텍스트를 추출하는 기능처럼 이미지의 텍스트를 유추합니다. 일반적인 예에는 스캔한 문서(JPEG) 파일에서 문자를 인식하거나 도로 표지판이 포함된 사진에서 거리 이름을 인식하는 OCR이 있습니다. | [이미지 분석 기술](cognitive-search-skill-image-analysis.md) 또는 [OCR 기술](cognitive-search-skill-ocr.md)
| 자연어 처리 | 입력 텍스트에 대한 정보 및 인사이트를 위한 텍스트 처리입니다. 언어 감지, 감정 분석 및 핵심 구 추출은 자연어 처리에 해당하는 기술입니다.  | [핵심 구 추출 기술](cognitive-search-skill-keyphrases.md), [언어 감지 기술](cognitive-search-skill-language-detection.md), [감정 분석 기술](cognitive-search-skill-sentiment.md) |
| 문서 크래킹 | 인덱싱 중에 텍스트가 아닌 원본에서 텍스트 콘텐츠를 추출하거나 만드는 프로세스입니다. OCR(광학 문자 인식)은 하나의 예이며, 일반적으로 인덱서가 응용 프로그램 파일에서 콘텐츠를 추출하는 핵심 인덱서 기능을 말합니다. 원본 파일 위치를 제공하는 데이터 원본과 필드 매핑을 제공하는 인덱서 정의는 문서 크래킹의 핵심 요소입니다. | [인덱서](search-indexer-overview.md)를 참조하세요. |
| 셰이핑 | 추가적인 다운스트림 처리를 위해 텍스트 조각을 더 큰 구조에 통합하거나 반대로 큰 텍스트 청크를 처리할 수 있는 크기로 분해합니다. | [쉐이퍼 기술](cognitive-search-skill-shaper.md), [텍스트 병합기 기술](cognitive-search-skill-textmerger.md), [텍스트 분할 기술](cognitive-search-skill-textsplit.md) |
| 보강된 문서 | 코드로 직접 액세스할 수 없는 일시적인 내부 구조입니다. 처리 중에 보강된 문서가 생성되지만 최종 출력만 검색 인덱스에 유지됩니다. 필드 매핑은 인덱스에 추가할 데이터 요소를 결정합니다. | [보강된 문서에 액세스](cognitive-search-tutorial-blob.md#access-enriched-document)를 참조하세요. |
| 인덱서 |  외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 문서 크래킹을 위한 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. Cognitive Search 보강을 위해 인덱서는 기술 집합을 호출하고 인덱스의 대상 필드에 보강 출력을 연결하는 필드 매핑을 포함합니다. 인덱서 정의에는 파이프라인 작업에 대한 모든 지침과 참조가 포함되며 인덱서를 실행하면 파이프라인이 호출됩니다. | [인덱서](search-indexer-overview.md) |
| 데이터 원본  | Azure에서 지원되는 유형의 외부 데이터 소스에 연결할 때 인덱서에 사용되는 개체입니다. | [인덱서](search-indexer-overview.md)를 참조하세요. |
| 인덱스 | 필드 구조와 사용법을 정의하는 인덱스 스키마로 작성된 Azure Search의 지속형 검색 모음입니다. | [Azure Search의 인덱스](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>시작 단계

**1단계: API가 제공되는 지역에 검색 서비스 만들기** 

+ 미국 중남부
+ 서유럽

**2단계: 워크플로를 완벽하게 습득하기 위한 실무 경험**

+ [빠른 시작(포털)](cognitive-search-quickstart-blob.md)
+ [자습서(HTTP 요청)](cognitive-search-tutorial-blob.md)
+ [예제 사용자 지정 기술(C#)](cognitive-search-create-custom-skill-example.md)

**3단계: API 검토(REST만 해당)**

현재는 REST API만 제공됩니다. 모든 요청에 `api-version=2017-11-11-Preview`를 사용합니다. 다음 API를 사용하여인지 Cognitive Search 솔루션을 빌드합니다. Cognitive Search를 위해 두 개의 API만 추가되거나 확장됩니다. 다른 API는 일반적으로 사용 가능한 버전과 동일한 구문을 포함합니다.

| REST API | 설명 |
|-----|-------------|
| [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 보강된 문서를 만드는 데 사용되는 원본 데이터를 제공하는 외부 데이터 원본을 식별하는 리소스입니다.  |
| [기술 집합 만들기(api-version=2017-11-11-Preview)](ref-create-skillset.md)  | 인덱싱 중에 강화 파이프라인에 사용되는 [미리 정의된 기술](cognitive-search-predefined-skills.md) 및 [사용자 지정 인지 기술](cognitive-search-custom-skill-interface.md)의 사용을 조정하는 리소스입니다. |
| [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Search 인덱스를 표현하는 스키마입니다. 원본 데이터 또는 보강 단계에서 생성되는 필드에 매핑되는 인덱스의 필드(예: 엔터티 인식으로 생성된 조직 이름에 대한 필드)입니다. |
| [인덱서 만들기(api-version=2017-11-11-Preview)](ref-create-skillset.md)  | 데이터 원본, 기술 집합, 원본 및 중간 데이터 구조에서 대상 인덱스로 필드 연결 및 인덱스 자체를 포함하는 인덱싱 중에 사용되는 리소스를 정의하는 구성 요소입니다. 데이터 수집 및 보강을 위한 트리거가 인덱서를 실행합니다. 출력은 기술 집합을 통해 보강된 원본 데이터로 채워진 인덱스 스키마를 기반으로 하는 검색 모음입니다.  |

**검사 목록: 일반적인 워크플로**

1. Azure 원본 데이터에서 대표 샘플 하위 집합을 생성합니다. 인덱싱에 시간이 걸리므로 작은 대표 데이터 집합으로 시작한 다음, 솔루션의 완성도가 높아지면 증분 방식으로 빌드합니다.

1. Azure Search에 [데이터 원본 개체](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 생성하여 데이터 검색을 위한 연결 문자열을 제공합니다.

1. 보강 단계를 통해 [기술 집합](ref-create-skillset.md)을 만듭니다.

1. [인덱스 스키마](https://docs.microsoft.com/rest/api/searchservice/create-index)를 정의합니다. 필드(*Field*) 컬렉션에 원본 데이터의 필드를 포함합니다. 보강 과정에서 만들어진 콘텐츠에 대해 생성된 값을 저장할 필드를 더 추가해야 합니다.

1. 데이터 원본, 기술 집합 및 인덱스를 참조하는 [인덱서](ref-create-skillset.md)를 정의합니다.

1. 인덱서 내에 *outputFieldMappings*를 추가합니다. 이 섹션에서는 기술 집합(3단계)의 출력을 인덱스 스키마(4단계)의 입력 필드에 매핑합니다.

1. Azure Search에서 인덱서를 표현하기 위해 방금 만든 인덱서 생성(*Create Indexer*) 요청(요청 본문에 인덱서 정의가 있는 POST 요청)을 보냅니다. 이 단계는 인덱서를 실행하여 파이프라인을 호출하는 방법에 대한 내용입니다.

1. 쿼리를 실행하여 결과를 평가하고 코드를 수정하여 기술 집합, 스키마 또는 인덱서 구성을 업데이트합니다.

1. 파이프라인을 다시 빌드하기 전에 [인덱서를 다시 설정](search-howto-reindex.md)합니다.

특정 질문 또는 문제에 대한 자세한 내용은 [문제 해결 팁](cognitive-search-concept-troubleshooting.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

+ [Cognitive Search 설명서](cognitive-search-resources-documentation.md)
+ [빠른 시작: 포털 연습에서 Cognitive Search 시도](cognitive-search-quickstart-blob.md)
+ [자습서: Cognitive Search API 알아보기](cognitive-search-tutorial-blob.md)