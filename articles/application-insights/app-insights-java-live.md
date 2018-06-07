---
title: 이미 라이브 상태인 Java 웹앱용 Application Insights
description: 서버에서 이미 실행 중인 웹 응용 프로그램 모니터링 시작
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: b8ddd43fb19b3800840180c458268301b5ec26f6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>이미 라이브 상태인 Java 웹앱용 Application Insights


J2EE 서버에서 이미 실행 중인 웹 응용 프로그램이 있는 경우 코드를 변경하거나 프로젝트를 다시 컴파일할 필요 없이 [Application Insights](app-insights-overview.md) 를 사용하여 모니터링을 시작할 수 있습니다. 이 옵션을 사용하면 서버로 전송된 HTTP 요청, 처리되지 않은 예외 및 성능 카운터에 대한 정보를 얻을 수 있습니다.

[Microsoft Azure](https://azure.com)를 구독해야 합니다.

> [!NOTE]
> 이 페이지의 절차는 런타임에서 웹앱에 SDK를 추가합니다. 이 런타임 계측은 소스 코드를 업데이트하거나 다시 작성하지 않을 경우에 유용합니다. 하지만 가능하면 [소스 코드에 SDK를 추가](app-insights-java-get-started.md) 할 것을 권장합니다. 그러면 사용자 활동을 추적하는 코드를 작성하는 등 추가 옵션이 제공됩니다.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights 계측 키 가져오기
1. [Microsoft Azure 포털](https://portal.azure.com)
2. 새 Application Insights 리소스를 만들고 Java 웹 응용 프로그램에 대한 응용 프로그램 종류를 설정합니다.
   
    ![이름을 채우고 Java 웹 앱을 선택하여 만들기 클릭](./media/app-insights-java-live/02-create.png)

    리소스가 몇 초 후에 생성됩니다.

4. 새 리소스를 열고 계측 키를 가져옵니다. 코드 프로젝트에 이 키를 곧바로 붙여넣어야 합니다.
   
    ![새 리소스 개요에서 속성을 클릭하고 계측 키 복사](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. SDK 다운로드
1. [Java용 Application Insights SDK](https://aka.ms/aijavasdk)를 다운로드합니다. 
2. 서버에서 프로젝트 이진 파일이 로드된 원본 디렉터리로 SDK 콘텐츠를 추출합니다. Tomcat을 사용하는 경우 이 디렉터리는 일반적으로 `webapps/<your_app_name>/WEB-INF/lib`에 위치합니다.

각 서버 인스턴스와 각 앱에 대해 이를 반복해야 합니다.

## <a name="3-add-an-application-insights-xml-file"></a>3. Application Insights xml 파일 추가
SDK를 추가한 폴더에 ApplicationInsights.xml을 만듭니다. 다음 XML을 삽입합니다.

Azure 포털에서 가져온 계측 키를 대체합니다.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* 계측 키는 원격 분석의 모든 항목과 함께 전송되며 리소스에서 표시하도록 Application Insights에 알려줍니다.
* HTTP 요청 구성 요소는 선택 사항입니다. 자동으로 포털에 요청 및 응답 시간에 대한 원격 분석을 보냅니다.
* 이벤트 상관 관계는 HTTP 요청 구성 요소에 추가됩니다. 이는 서버가 수신하는 요청마다 식별자를 할당하며 'Operation.Id' 속성으로 원격 분석의 모든 항목에 이 식별자를 속성으로 추가합니다. [진단 검색](app-insights-diagnostic-search.md)에서 필터를 설정하여 각 요청과 연결된 원격 분석의 상관 관계를 지정할 수 있습니다.

## <a name="4-add-an-http-filter"></a>4. HTTP 필터 추가
프로젝트에서 web.xml 파일을 찾아 열고, 응용 프로그램 필터가 구성된 웹 앱 노드 아래 다음 코드 조각을 병합합니다.

가장 정확한 결과를 얻으려면 필터를 다른 모든 필터 전에 매핑해야 합니다.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. 방화벽 예외를 확인합니다.
[예외를 설정하여 나가는 데이터를 내보내야](app-insights-ip-addresses.md)할 수 있습니다.

## <a name="6-restart-your-web-app"></a>6. 웹앱을 다시 시작합니다.
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Application Insights에서 원격 분석 보기
[Microsoft Azure 포털](https://portal.azure.com)의 Application Insights 리소스로 돌아갑니다.

HTTP 요청에 대한 원격 분석은 개요 블레이드에 표시됩니다. (없는 경우 몇 초 정도 기다린 다음 새로고침을 클릭합니다.)

![샘플 데이터](./media/app-insights-java-live/5-results.png)

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다. 

![](./media/app-insights-java-live/6-barchart.png)

또한 요청 속성 검토 시 요청 및 예외 사항과 관련된 원격 분석 이벤트를 볼 수 있습니다.

![](./media/app-insights-java-live/7-instance.png)

[메트릭에 대해 자세히 알아봅니다.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>다음 단계
* [웹 페이지에 원격 분석을 추가](app-insights-javascript.md) 하여 페이지 보기 및 사용자 메트릭을 모니터링합니다.
* [웹 테스트를 설정](app-insights-monitor-web-app-availability.md) 하여 응용 프로그램이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.
* [로그 추적 캡처](app-insights-java-trace-logs.md)
* [이벤트 및 로그를 검색](app-insights-diagnostic-search.md) 하여 문제를 진단할 수 있습니다.

