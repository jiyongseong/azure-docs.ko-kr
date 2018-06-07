---
title: Linux에서 Java 웹앱 성능 모니터링 - Azure | Microsoft Docs
description: Application Insights용 CollectD 플러그 인을 사용한 Java 웹 사이트의 포괄적인 응용 프로그램 성능 모니터링
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 56e30ff4ee7765cd0950293999d93a9cf1fe5d36
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Application Insights에서 Linux 성능 메트릭


Linux 시스템 성능 메트릭을[Application Insights](app-insights-overview.md)에서 탐색하려면 [collectd](http://collectd.org/)와 Application Insights 플러그 인을 함께 설치합니다. 이 오픈 소스 솔루션은 다양한 시스템 및 네트워크 통계를 수집합니다.

이미 [Application Insights로 Java 웹 서비스를 계측][java]한 경우 일반적으로 collectd를 사용합니다. 앱의 성능을 향상시키거나 문제를 진단할 수 있도록 더 많은 데이터를 제공합니다. 

![예제 차트](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>계측 키 가져오기
[Microsoft Azure Portal](https://portal.azure.com)에서 데이터를 표시하고 싶은 [Application Insights](app-insights-overview.md) 리소스를 엽니다. (또는 [새 리소스를 만듭니다](app-insights-create-new-resource.md).)

리소스를 식별하는 계측 키의 복사본을 만듭니다.

![모두 찾아보고, 프로그램 리소스를 연 다음, Essentials 드롭다운 목록에서 계측 키 선택 및 복사](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Collectd 및 플러그인을 설치합니다.
Linux 서버 컴퓨터에서:

1. [collectd](http://collectd.org/) 5.4.0 버전 또는 그 이상을 설치합니다.
2. [Application Insights collectd 기록기 플러그 인](https://aka.ms/aijavasdk)을 다운로드합니다. 버전 번호를 메모합니다.
3. 플러그인JAR를 `/usr/share/collectd/java`에 복사합니다.
4. 편집 `/etc/collectd/collectd.conf`:
   * [Java 플러그인](https://collectd.org/wiki/index.php/Plugin:Java) 사용하도록 설정 합니다.
   * 다음 JAR을 포함하는 java.class.path에 대한 JVMArg를 업데이트합니다. 다운로드 한 것과 일치하는 버전 번호를 업데이트합니다.
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * 리소스에서 계측 키를 사용하여 이 코드 조각을 추가합니다.

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

다음은 샘플 구성 파일의 일부입니다.

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

다른 [collectd 플러그인](https://collectd.org/wiki/index.php/Table_of_Plugins)을 구성하여 다른 원본에서 다양한 데이터를 수집할 수 있습니다.

Collectd를 해당 [설명서](https://collectd.org/wiki/index.php/First_steps)에 따라서 다시 시작합니다.

## <a name="view-the-data-in-application-insights"></a>Application Insights에서 데이터 보기
Application Insights 리소스에서 [메트릭 탐색기 및 차트 추가하기][metrics]를 열고, 사용자 지정 범주에서 보려는 메트릭을 선택합니다.

![](./media/app-insights-java-collectd/result.png)

기본적으로 메트릭을 수집한 모든 호트스 컴퓨터의 메트릭이 집계됩니다. 차트 세부 정보 블레이드에서 호스트마다 메트릭을 보려면 그룹화를 설정하고 CollectD 호스트별로 그룹화를 선택합니다.

## <a name="to-exclude-upload-of-specific-statistics"></a>특정 통계의 업로드를 제외하려면
기본적으로 Application Insights 플러그 인은 모든 사용할 수 있는 collectd ‘읽기’ 플러그 인에 의해 수집된 모든 데이터를 보냅니다. 

특정 플로그인 또는 데이터 소스의 데이터를 제외하려면:

* 구성 파일을 편집합니다. 
* `<Plugin ApplicationInsightsWriter>`에서 다음과 같은 지시문 줄을 추가 합니다.

| 지시문 | 결과 |
| --- | --- |
| `Exclude disk` |`disk` 플러그인에 의해 수집된 모든 데이터를 제외 |
| `Exclude disk:read,write` |`disk` 플러그인에서 `read`과 `write`라고 명명된 원본을 제외합니다. |

새 줄을 포함한 별도 지시문입니다.

## <a name="problems"></a>문제가 있습니까?
*포털에 데이터가 표시되지 않습니다.*

* [검색][diagnostic]을 열고 원시 이벤트가 도착했는지 확인합니다. 때로는 메트릭 탐색기에 나타날 때 시간이 오래 걸립니다.
* [나가는 데이터에 대한 방화벽 예외를 설정](app-insights-ip-addresses.md)
* Application insights 플러그인에서 추적을 사용할 수 있습니다. `<Plugin ApplicationInsightsWriter>`에서 이 줄 추가:
  * `SDKLogger true`
* 터미널을 열고 세부정보 표시 모드를 시작하여 어떤 문제가 보고되었는지 확인합니다.
  * `sudo collectd -f`

## <a name="known-issue"></a>알려진 문제

Application Insights 쓰기 플러그 인이 특정 읽기 플러그 인과 호환되지 않습니다. 경우에 따라 일부 플러그 인은 Application Insights 플러그 인에서 부동 소수점 숫자를 예상하는 위치로 “NaN”를 전송합니다.

증상: 수집된 로그에 “AI: ... SyntaxError: 예기치 않은 토큰 N”을 포함하는 오류가 표시됩니다.

해결 방법: 문제 쓰기 플러그 인에 의해 수집된 데이터를 제외합니다. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


