---
title: Azure Application Insights에서 SQL로 내보내기 | Microsoft Docs
description: Stream Analytics를 사용하여 Application Insights 데이터를 SQL로 계속 내보냅니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: e410bb87d017a7659c3eaffbedef378aaf7f5716
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>연습: Stream Analytics를 사용하여 Application Insights에서 SQL로 내보내기
이 문서에서는 [연속 내보내기][export] 및 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)을 사용하여 [Azure Application Insights][start]에서 Azure SQL Database로 원격 분석 데이터를 이동하는 방법을 보여줍니다. 

연속 내보내기는 원격 분석 데이터를 JSON 형식으로 Azure Storage로 이동합니다. Azure Stream Analytics를 사용하여 JSON 개체를 구문 분석하고 데이터베이스 테이블에 행을 만들 것입니다.

보다 일반적으로 말하자면, 연속 내보내기는 앱에서 Application Insights에 보내는 원격 분석을 자체적으로 분석하는 방법입니다. 데이터 집계와 같은 원격 분석을 사용하여 이 코드 샘플이 기타 작업을 수행하도록 적용할 수 있습니다.

모니터링하려는 앱이 이미 있다고 가정하고 시작합니다.

이 예제에서는 페이지 보기 데이터를 사용하지만, 동일한 패턴을 사용자 지정 이벤트 및 예외와 같은 다른 데이터 형식으로 쉽게 확장할 수 있습니다. 

## <a name="add-application-insights-to-your-application"></a>응용 프로그램에 Application Insights 추가
시작하기:

1. [웹 페이지용 Application Insights를 설치합니다](app-insights-javascript.md). 
   
    (이 예제에서는 클라이언트 브라우저에서 페이지 보기 데이터를 처리하는 데 초점을 두었지만 [Java](app-insights-java-get-started.md) 또는 [ASP.NET](app-insights-asp-net.md) 앱의 서버 쪽에 대한 Application Insights, 프로세스 요청, 종속성 및 기타 서버 원격 분석도 설정할 수 있습니다.)
2. 앱을 게시하고 Application Insights 리소스에 표시되는 원격 분석 데이터를 확인합니다.

## <a name="create-storage-in-azure"></a>Azure에서 저장소 만들기
연속 내보내기는 항상 Azure Storage 계정에 데이터를 출력하므로 저장소를 먼저 만들어야 합니다.

1. [Azure Portal][portal]에서 구독에 저장소 계정을 만듭니다.
   
    ![Azure Portal에서 새로 만들기, 데이터, 저장소를 선택합니다. 클래식을 선택하고 만들기를 선택합니다. 저장소 이름을 제공합니다.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. 컨테이너 만들기
   
    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. 저장소 액세스 키 복사
   
    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.
   
    ![저장소에서 설정, 키를 열고 기본 액세스 키 복사](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure 저장소로 연속 내보내기 시작
1. Azure 포털에서 응용 프로그램에 대해 만든 Application Insights 리소스를 찾습니다.
   
    ![찾아보기, Application Insights, 응용 프로그램 선택](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. 연속 내보내기를 만듭니다.
   
    ![설정, 연속 내보내기, 추가를 차례로 선택](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    이전에 만든 저장소 계정을 선택합니다.

    ![내보내기 대상 설정](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    보려는 이벤트 유형을 설정합니다.

    ![이벤트 유형 선택](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. 일부 데이터가 누적되도록 합니다. 한동안 사용자가 응용 프로그램을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기](app-insights-metrics-explorer.md)에서 통계 차트가, [진단 검색](app-insights-diagnostic-search.md)에서 개별 이벤트가 표시됩니다. 
   
    또한 데이터를 저장소로 내보냅니다. 
2. 포털 또는 Visual Studio에서 내보낸 데이터를 검사합니다. 포털에서 **찾아보기**, 저장소 계정을 선택한 다음 **컨테이너**를 선택합니다. Visual Studio에서 **보기/클라우드 탐색기**를 선택하고 Azure/저장소를 엽니다. 이 메뉴 옵션이 없는 경우 Azure SDK를 설치해야 합니다. 새 프로젝트 대화 상자를 열고 Visual C#/클라우드/Microsoft Azure SDK for .NET 가져오기를 엽니다.
   
    ![Visual Studio에서 서버 브라우저, Azure, 저장소 열기](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    응용 프로그램 이름 및 계측 키에서 파생된 경로 이름의 공통 부분을 적어 둡니다. 

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 Stream Analytics를 사용하여 데이터를 SQL 데이터베이스로 이동하려고 합니다. 이렇게 하면 흥미로운 많은 쿼리를 쉽게 실행할 수 있습니다.

## <a name="create-an-azure-sql-database"></a>Azure SQL Database 만들기
다시 한 번 [Azure Portal][portal]의 구독에서 시작하여 데이터를 작성할 데이터베이스(및 이미 있는 경우 새 서버)를 만듭니다.

![새로 만들기, 데이터, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

데이터베이스 서버에서 Azure 서비스에 액세스할 수 있는지 확인합니다.

![찾아보기, 서버, 사용자 서버, 설정, 방화벽, Azure에 대한 액세스 허용](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Azure SQL DB에 테이블 만들기
기본 관리 도구로 이전 섹션에서 만든 데이터베이스에 연결합니다. 이 연습에서는 SSMS( [SQL Server 관리 도구](https://msdn.microsoft.com/ms174173.aspx) )를 사용합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

새 쿼리를 만들고 다음 T-SQL을 실행합니다.

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

이 샘플에서 페이지 보기에서 데이터를 사용합니다. 사용 가능한 다른 데이터를 보려면 JSON 출력을 검사하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics 인스턴스 만들기
[Azure Portal](https://portal.azure.com/)에서 Azure Stream Analytics 서비스를 선택하고 새 Stream Analytics 작업을 만듭니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

새 작업이 만들어질 때 **리소스로 이동**을 선택합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>새 입력 추가

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

연속 내보내기 Blob에서 입력을 가져오도록 설정합니다.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

이제 앞에서 기록해 둔 Storage 계정의 기본 액세스 키가 필요합니다. 이 키를 Storage 계정 키로 설정합니다.

#### <a name="set-path-prefix-pattern"></a>경로 접두사 패턴 설정

**날짜 형식을 YYYY-MM-DD(파선 포함)로 설정해야 합니다.**

경로 접두사 패턴은 Stream Analytics가 저장소에서 입력 파일을 찾는 방법을 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

이 예제에서:

* `webapplication27` 은 Application Insights 리소스의 이름으로, **모두 소문자**입니다. 
* `1234...` 은 **대시를 제거한**Application Insights 리소스의 계측 키입니다. 
* `PageViews` 는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](app-insights-export-data-model.md)를 참조합니다.
* `/{date}/{time}` 은 문자로 기록된 패턴입니다.

Application Insights 리소스의 이름 및 iKey를 가져오려면 해당 개요 페이지에서 필수 항목을 열거나 설정을 엽니다.

> [!TIP]
> 샘플 함수를 사용하여 입력 경로가 올바르게 설정되었는지 확인합니다. 실패한 경우 선택한 샘플 시간 범위에 대한 저장소에 데이터가 있는지 확인합니다. 입력 정의를 편집하고 저장소 계정, 경로 접두사 및 날짜 형식이 올바르게 설정되었는지 확인합니다.
> 
> 
## <a name="set-query"></a>쿼리 설정
쿼리 섹션을 엽니다.

기본 쿼리를 다음으로 바꿉니다.

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

처음 몇 가지 속성은 페이지 보기 데이터에만 해당됩니다. 다른 원격 분석 유형 내보내기에 다른 속성이 있습니다. [속성 형식 및 값에 대한 자세한 데이터 모델 참조](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>데이터베이스에 출력 설정
SQL을 출력으로 선택합니다.

![스트림 분석에서 출력 선택](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

SQL 데이터베이스를 지정합니다.

![데이터베이스의 세부 정보 채우기](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

마법사를 닫고 출력이 설정되었다는 알림이 표시될 때까지 기다립니다.

## <a name="start-processing"></a>처리 시작
작업 모음에서 작업을 시작합니다.

![스트림 분석에서 시작 클릭](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

지금부터의 데이터를 처리할 것인지 아니면 이전 데이터부터 처리할 것인지를 선택할 수 있습니다. 후자는 연속 내보내기를 이미 한동안 실행한 경우 유용합니다.

몇 분 후에 SQL Server 관리 도구로 돌아가서 데이터 흐름을 확인합니다. 예를 들어 다음과 같은 쿼리를 사용합니다.

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>관련 문서
* [Stream Analytics를 사용하여 PowerBI로 내보내기](app-insights-export-power-bi.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](app-insights-export-data-model.md)
* [Application Insights에서 연속 내보내기](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

