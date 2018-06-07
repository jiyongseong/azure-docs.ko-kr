---
title: Azure Search의 비디오, 샘플 및 자습서 | Microsoft Docs
description: Microsoft Azure에서 호스팅되는 클라우드 서비스인 Azure Search에 대해 만들어진 모든 비디오, 샘플, 데모 및 자습서의 중앙 목록입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: heidist
ms.openlocfilehash: e71a8abcebb95f2d34dee2f26dc2c7f341465a55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-search-code-samples-demos-videos-and-tutorials"></a>Azure Search: 코드 샘플, 데모, 비디오 및 자습서
다음 비디오, 데모, 자습서 및 샘플 앱으로 빠르게 기능을 활용해 보세요.

## <a name="learn"></a>유용한 정보

Azure Search를 처음 사용하는 경우 여기에서 시작합니다. 약 1시간 동안 이러한 모든 리소스를 검토할 수 있습니다.

| 제목 | 설명 | Date |
|-------|-------------|------|
| [이 소개 비디오 시청](https://channel9.msdn.com/Events/Connect/2016/138) | 기능, 서비스 만들기 및 쿼리에 대한 9m38s의 소개 내용입니다. | 2016년 11월 |
| [ 서비스 만들기](search-get-started-portal.md) | 포털에서 Azure Search를 설정하기 위한 단계입니다. | 2017년 2월 |
| [인덱스 쿼리](search-get-started-portal.md) | 기본 제공 샘플 데이터를 사용하여 만들고 쿼리합니다. 포털의 도구는 쿼리 형식 및 검색 매개 변수에 대해 알아볼 수 있는 빠른 방법입니다. | 2017년 2월 |
| [다음 단계:<br/> .NET의 코드](search-howto-dotnet-sdk.md) | C# 연습에서는 Azure Search .NET 라이브러리를 사용하여 연결, 인덱싱 및 쿼리하는 방법을 보여 줍니다. | 2016년 11월 |
| [다음 단계:<br/> REST의 코드](search-create-index-rest-api.md) | 임의 플랫폼 및 프로그래밍 언어에로 Azure Search 인덱스를 만들기 위한 REST 호출 및 JSON 페이로드를 검토합니다. | 2016년 12월 |
| [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md) | Azure Search 전체 텍스트 검색 워크로드의 아키텍처 및 개요 | 2017년 3월 |

## <a name="code-samples"></a>코드 샘플

샘플 및 연습을 통한 코드 기반 탐색입니다.

| 코드 샘플 | 설명 |
|-------|-------------|
| [.NET 클라이언트 라이브러리 시작](https://github.com/Azure-Samples/search-dotnet-getting-started) | .NET 클라이언트 라이브러리를 사용하는 ASP.NET MVC 5 응용 프로그램입니다. |
| [REST API 시작](https://github.com/Azure-Samples/search-rest-api-getting-started) | REST API 코드 샘플입니다. |
| [소스: NYC 작업 웹 사이트 데모](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) | ASP.NET MVC를 사용하는 NYC 작업 데이터에 대해 라이브 데모 Azure Search 사이트의 소스입니다. |
| [소스: Tate Art Gallery 데모](https://github.com/liamca/azure-search-tate-art-gallery/) | 아트 갤러리 데이터를 통한 라이브 데모 Azure Search 사이트에 대한 소스를 제공하며 컬렉션 데이터 형식 및 중첩된 데이터 구조, 패싯 및 필터, 사용자 이름 및 미리보기 이미지를 보여줍니다.|

특정 기능 조합 및 시나리오에 대한 추가 코드 샘플은 [ **Azure 샘플 > Azure Search**](https://github.com/azure-samples?utf8=%E2%9C%93&query=search)에서 찾을 수 있습니다.

## <a name="online-demos"></a>온라인 데모
검색 환경에 대한 Azure Search를 사용하는 모든 기능을 갖춘 웹 응용 프로그램입니다. 이러한 링크는 Azure Search를 사용하는 앱을 실제로 사용해볼 수 있는 환경을 제공합니다.

| 링크 | 설명 |
|-------|-------------|
|[검색 분석기 데모](http://alice.unearth.ai) | 표준 Lucene 분석기, Lucene 영어 분석기 및 Microsoft의 자연어 프로세서의 쿼리 결과를 보여주는 라이브 데모 웹 사이트입니다. 각 검색 용어 입력에 대해 각 분석기의 출력을 나란히 비교하여 볼 수 있습니다. |
|[Azure Search에 대한 NYC(뉴욕시) 구직 포털 데모](http://aka.ms/azjobsdemo) | NYC 오픈 데이터 이니셔티브의 데이터를 사용하여 Azure Search 기능을 설명하는 라이브 ASP.NET 웹 사이트입니다. 이 앱은 [단순 쿼리 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx)을 사용합니다. 코드를 수정하여 [Lucene 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 사용할 수 있습니다. |
|[Tate Art Gallery 데모](https://rawgit.com/liamca/azure-search-tate-art-gallery/master/Demo/index.html) | Tate Art Gallery의 샘플 데이터를 사용하여 호스트된 ASP.NET 웹 사이트입니다. 먼저 검색어로 "modern", "photo" 및 "portrait" 등을 사용해 보세요.
|[Azure Search 데모 사이트](https://searchsamples.azurewebsites.net/#/) | Azure Search 데모 앱의 모음입니다. Azure Search를 사용하여 미디어 검색, 지리적 검색 및 제품 재고 앱을 시험 사용합니다. |

## <a name="courseware"></a>교육 과정

Azure Search에 대한 다중 세션 데모 및 실습입니다.

| 링크 | 설명 | Date |
|------|-------------|------|
[Xamarin 및 Azure Search를 사용하여 지능형 플랫폼 간 모바일 응용 프로그램 빌드](https://mva.microsoft.com/en-US/training-courses/building-intelligent-crossplatform-mobile-applications-using-xamarin-and-azure-search-16890) | 자동 완성, 전체 텍스트 검색 및 페이징, 필터링, 조회, moreLikeThis, Machine Learning, 점수 매기기 및 튜닝, 분석 | 2016년 12월 |
[Windows Azure Search 사용](https://mva.microsoft.com/en-US/training-courses/using-windows-azure-search-10540) | 이전 기능이지만 여전히 적절한 크기 조정, 지리 공간 검색, 언어 분석, Cortana 음성 인식에 대한 드릴다운 | 2015년 5월 | 

## <a name="video-archive"></a>참조용 비디오

| 링크 | 릴리스 날짜 | 세부 정보 |
| --- | --- | --- |
| [Channel9 빌드 2016: "학습 및 적용하는 응용 프로그램: Azure Machine Learning + Search"](https://channel9.msdn.com/Events/Build/2016/P458) |2016년 5월 |17분. Liam Cavanagh는 Azure Search 응용 프로그램에 ‘권장' 기능 추가의 이점 및 기계 학습에서 사용자 기본 설정에 대한 통찰력을 얻는 방법을 설명합니다. |
| [Channel9 빌드 2016: "학습 및 적용하는 지능형 데이터 기반 응용 프로그램"](https://channel9.msdn.com/Events/Build/2016/B857) |2016년 5월 |1시간 정교한 데이터 기반 응용 프로그램을 만들도록 Machine Learning 및 Azure Search를 함께 사용할 수 있는 방법을 알아봅니다. |
| [Channel9 제공 데이터: "Azure Search의 사용자 지정 분석기, 검색 분석 및 포털 쿼리"](https://channel9.msdn.com/Shows/Data-Exposed/Custom-Analyzers-Search-Analytics--Portal-Querying-in-Azure-Search) |2016년 1월 |15분 사용자 지정 분석기를 사용하면 토크나이저 및 토큰 파일을 구성할 수 있습니다. 검색 분석을 사용하면 사용자가 입력한 검색 용어와 검색 인덱스의 효율에 대한 통찰력을 가질 수 있습니다. 검색 탐색기는 포털 내부에서 쿼리를 시도할 수 있는 기본 제공 쿼리 도구입니다. |
| [Channel9 제공 데이터: "Azure Search 향상"](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-Enhancements) |2015년 11월 |17분. StackExchange 데이터 수집. Azure Portal에서 인덱스 작성. 인덱서가 지원되는 데이터 원본을 크롤링하여 Azure Search 인덱스로 데이터를 끌어 넣는 방식을 알아봅니다. 언어 분석기. Lucene 쿼리 식. |
| [AzureCon 2015 : Azure Search에 대한 모범 사례](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications/) |2015년 9월 |웹 또는 모바일 시나리오용 앱 개발 시 확장, 성능 최적화 및 코딩 효율을 달성하는 방법에 관한 실용적인 조언입니다. 게시자: Pablo Castro |
| [빌드 2015: 최신 데이터](http://channel9.msdn.com/Events/Build/2015/2-663) |2015년 6월 |세션에서 29분 동안 Azure Search가 최근의 데이터 혁명에서 차지하는 위치에 대해 알아봅니다. Shawn Bice 및 Pablo Castro가 제공합니다. |
| [빌드 2015: Azure Search를 사용하여 모바일 및 WebApp에서 우수한 검색 경험 구축](http://channel9.msdn.com/Events/Build/2015/2-745) |2015년 6월 |1시간 Microsoft 빌드 2015에서 Pablo Castro가 제공합니다. |
| [Elastic{ON} 2015: Microsoft의 실시간 검색 지원](https://www.elastic.co/elasticon/2015/sf/powering-real-time-search-at-microsoft) |2015년 3월 |16분. Elastic{ON} 2015에서 Pablo Castro가 프레젠테이션을 통해 Azure Search 및 Microsoft의 다른 그룹에서 ElasticSearch를 사용하는 방법을 설명합니다. |
| [Channel9 제공 데이터: "Azure Search 일반 공급 및 새로운 기능"](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-General-Availability-and-Whats-New) |2015년 3월 |12분. Azure Search가 일반 공급("GA")됩니다. 게시자: Liam Cavanagh |
| [Channel9 제공 데이터: “Azure Search 제안”](https://channel9.msdn.com/Shows/Data-Exposed/DataExposedAzureSearchSuggestions) |2015년 2월 |15분 제안(사용자가 단어를 입력하는 동안 검색어 후보 표시하는 사전 입력 또는 자동 완성 쿼리라고도 함). Azure Search에서 유사 일치는 제안을 통해 구현됩니다. |
| [Channel9 제공 데이터: “SQL Server에서 Azure Search로의 동기화”](http://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-to-Azure-Search-Synchronization) |2014년 12월 |16분. 데이터베이스 트랜잭션을 동기화합니다. Azure SQL 관계형 데이터를 Azure Search에 로드합니다. |
| [TechEd Europe: Azure Search에 대한 심층 분석](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) |2014년 10월 |1 시간 15 분. Azure Search에 대한 빠르고 심층적인 데모. 게시자: Pablo Castro |
| [Channel9 제공 데이터: “지리 공간 데이터와 Azure Search”](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) |2014년 9월 |18분. 지리 공간 검색 시나리오. |
| [Channel9 클라우드 커버: 에피소드 152](http://channel9.msdn.com/Shows/Cloud%20Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) |2014년 9월 |25분 Fiddler를 통해 일반적인 쿼리와 지리 공간적인 쿼리를 생성하고 반환되는 결과를 이해하는 방법에 대한 기술적인 기능 설명 |
| [Channel9 제공 데이터: “Azure Search 소개”](https://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search) |2014년 8월 |22분. 공개 미리 보기 상태인 Azure Search의 아키텍처 및 기능. |

## <a name="tutorial-archive"></a>참조용 자습서

| 연습 | 설명 |
| --- | --- |
| [D3.js에서 Azure Search를 사용하여 대화형 데이터 시각화를 구축하는 방법](https://azure.microsoft.com/blog/2015/07/14/how-to-use-azure-search-with-d3-js-to-build-interactive-data-visualizations/) |사용자 지정 앱에 비주얼 검색 기능을 추가하는 방법을 설명하는 블로그 게시물과 샘플 코드입니다. |
| [NodeJS에서 Azure Search 시작](search-get-started-nodejs.md) |NodeJS에서 Azure Search를 사용하는 방법을 알아봅니다. |
| [Java에서 Azure Search 시작](search-get-started-java.md) |Eclipse 및 Apache Tomcat 서버를 사용하여 Azure Search를 사용하는 샘플 응용 프로그램을 구축하고 실행할 Java 프로그래머를 위한 자습서입니다. |
| [Azure Search 시나리오 및 기능](https://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/) |현재 기능으로 사용할 수 있는 기본 시나리오에 대해 설명하는 블로그 게시물입니다. |
| [검색 응용 프로그램에 제안을 추가하는 방법](https://azure.microsoft.com/blog/2015/01/20/azure-search-how-to-add-suggestions-auto-complete-to-your-search-applications/) |일반적으로 자동 완성이라고 하는 제안 기능을 구현하는 방법을 알아봅니다. 이 데모에서는 Azure Search 제안을 활용하는 MVC4 응용 프로그램 개발, JavaScript에서 Twitter typeahead.js 라이브러리를 사용한 효과적인 자동 제안 구성 요소 개발, 유사 일치 제안을 사용하여 사용자가 단어를 잘못 입력한 경우에도 제안을 표시하도록 하는 방법 등의 다양한 주제를 자세히 설명합니다. |
| [TechNet 가상 실습: Microsoft Azure Search 응용 프로그램 구축](http://go.microsoft.com/?linkid=9874663) |개발 환경, 도구, 설명서 및 데이터를 제공 하는 가상 실습입니다. 실습을 완료하려면 평가판 구독 또는 개인 Azure 구독이 있는 Azure 계정이 필요합니다. |
| [SQL Server 데이터와 Azure Search 동기화](https://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) |Azure Search 인덱스에 SQL Server 관계형 데이터베이스의 데이터를 로드하는 방법을 설명하는 블로그 게시물입니다. |

