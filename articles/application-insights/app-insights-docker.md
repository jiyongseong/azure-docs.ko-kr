---
title: Azure Application Insights에서 Docker 응용 프로그램 모니터링 | Microsoft Docs
description: Docker 성능 카운터, 이벤트 및 예외는 컨테이너식 앱에서 보낸 원격 분석과 함께 Application Insights에 표시될 수 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: a0476e2f0bf08f76b45e1342ec38137e46008cb1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153696"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Application Insights에서 Docker 응용 프로그램 모니터링
[Docker](https://www.docker.com/) 컨테이너의 수명 주기 이벤트 및 성능 카운터를 Application Insights에서 차트로 표시할 수 있습니다. 호스트의 컨테이너에 [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) 이미지를 설치하면 호스트는 물론 다른 이미지에 대한 성능 카운터가 표시됩니다.

Docker를 사용하여 모든 종속성이 포함된 경량 컨테이너에 앱을 배포합니다. Docker 엔진을 실행하는 모든 호스트 컴퓨터에서 앱이 실행됩니다.

Docker 호스트에서 [Application Insights 이미지](https://hub.docker.com/r/microsoft/applicationinsights/)를 실행하면 다음과 같은 장점이 있습니다.

* 호스트에서 실행하는 모든 컨테이너에 대한 수명 주기 원격 분석 – 시작, 중지 등
* 모든 컨테이너에 대한 성능 카운터 CPU, 메모리, 네트워크 사용량 외 다수
* 컨테이너에서 실행되는 앱에 [Java용 Application Insights SDK를 설치하면](app-insights-java-live.md) 해당 앱에 대한 모든 원격 분석에 컨테이너와 호스트 컴퓨터를 식별하는 추가적인 속성이 포함됩니다. 예를 들어 둘 이상의 호스트에서 실행되는 앱 인스턴스가 있다면 앱 원격 분석을 호스트별로 쉽게 필터링할 수 있습니다.

![예제](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Application Insights 리소스 설정
1. [Microsoft Azure Portal](https://azure.com)에 로그인하고 앱에 대한 Application Insights 리소스를 열거나 [새 리소스를 만듭니다](app-insights-create-new-resource.md). 
   
    *어떤 리소스를 사용해야 하나요?* 호스트에서 실행하는 앱을 다른 사람이 개발한 경우에는 [새로운 Application Insights 리소스를 만들어야 합니다](app-insights-create-new-resource.md). 이 리소스에서 원격 분석을 보고 분석하게 됩니다. (앱 형식에 대해 '일반'을 선택합니다.)
   
    해당 앱의 개발자인 경우에는 각 앱에 [Application Insights SDK를 추가](app-insights-java-live.md) 하셨기를 바랍니다. 실제로 모든 리소스가 단일 비즈니스 응용 프로그램의 구성 요소라면, 하나의 리소스에 원격 분석을 보내도록 모든 리소스를 구성하고, 동일한 리소스를 사용하여 Docker 수명 주기 및 성능 데이터를 표시하게 됩니다. 
   
    세 번째 시나리오는 사용자가 대부분의 앱을 개발했지만 별도의 리소스를 사용하여 앱의 원격 분석을 나타내는 경우입니다. 대개 이런 경우에는 Docker 데이터에 대해서도 별도의 리소스를 만들려고 할 것입니다. 
2. Docker 타일을 추가합니다. **타일 추가**를 선택하고 갤러리에서 Docker 타일을 끌어다 놓은 후 **완료**를 클릭합니다. 
   
    ![예제](./media/app-insights-docker/03.png)

> [!NOTE]
> Application Insights의 개요 창은 이제 잠기고 갤러리에서 타일 추가를 허용하지 않습니다. Azure 대시보드 인터페이스를 통해 위에서 설명한 것처럼 Docker 타일을 여전히 추가할 수 있습니다.

3. **Essentials** 드롭다운을 클릭하고 계측 키를 복사합니다. 이 키를 사용하여 SDK에 원격 분석을 보낼 위치를 알립니다.

    ![예제](./media/app-insights-docker/02-props.png)

원격 분석을 확인하기 위해 곧 돌아와야 하므로 해당 브라우저 창을 열어둡니다.

## <a name="run-the-application-insights-monitor-on-your-host"></a>호스트에서 Application Insights 모니터링 실행
원격 분석을 표시할 위치를 확보했으니, 원격 분석 데이터를 수집하고 보낼 컨테이너식 앱을 설정할 수 있습니다.

1. Docker 호스트에 연결합니다. 
2. 계측 키를 아래 명령에 넣어 편집하고 실행합니다.
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Application Insights 이미지는 Docker 호스트당 하나만 필요합니다. 응용 프로그램을 여러 개의 Docker 호스트에 표시하는 경우, 이 명령을 호스트마다 반복합니다.

## <a name="update-your-app"></a>앱 업데이트
응용 프로그램이 [Java용 Application Insights SDK](app-insights-java-get-started.md)를 사용하여 계측되는 경우에는, 프로젝트의 ApplicationInsights.xml 파일에서 `<TelemetryInitializers>` 요소 아래에 다음 줄을 추가합니다.

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

이것은 앱에서 보내는 모든 원격 분석 항목에 컨테이너 및 호스트 ID 같은 Docker 정보를 추가합니다.

## <a name="view-your-telemetry"></a>원격 분석 보기
Azure 포털에서 Application Insights 리소스로 돌아갑니다.

Docker 타일을 클릭합니다.

특히 Docker 엔진에서 다른 컨테이너가 실행되고 있는 경우 Docker 앱에서 데이터가 도착하는 것을 곧 확인할 수 있습니다.

다음은 표시될 수 있는 몇 가지 보기입니다.

### <a name="perf-counters-by-host-activity-by-image"></a>호스트별 성능 카운터, 이미지별 활동
![예제](./media/app-insights-docker/10.png)

![예제](./media/app-insights-docker/11.png)

자세한 내용을 보려면 호스트 또는 이미지 이름을 클릭합니다.

보기를 사용자 지정하려면 차트, 그리드 제목을 차례로 클릭하거나 차트 추가를 사용합니다. 

[메트릭 탐색기에 대해 자세히 알아봅니다](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Docker 컨테이너 이벤트
![예제](./media/app-insights-docker/13.png)

개별 이벤트를 조사하려면 [검색](app-insights-diagnostic-search.md)을 클릭합니다. 검색 및 필터링하여 원하는 이벤트를 찾습니다. 자세한 내용을 보려면 이벤트를 클릭합니다.

### <a name="exceptions-by-container-name"></a>컨테이너 이름별 예외
![예제](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>앱 원격 분석에 추가되는 Docker 컨텍스트
AI SDK를 사용하여 계측되는 응용 프로그램에서 보내는 요청 원격 분석은 Docker 컨텍스트를 사용하여 보강됩니다.

![예제](./media/app-insights-docker/16.png)

프로세서 시간 및 사용 가능한 메모리 성능 카운터는 Docker 컨테이너 이름으로 그룹화되고 보강됩니다.

![예제](./media/app-insights-docker/15.png)

## <a name="q--a"></a>질문과 대답
*Docker에서 얻을 수 없는 어떤 기능을 Application Insights가 제공하나요?*

* 컨테이너 및 이미지별로 성능 카운터의 자세한 분석 결과를 제공합니다.
* 하나의 대시보드에서 컨테이너 및 앱 데이터를 통합합니다.
* [원격 분석을 내보냅니다](app-insights-export-telemetry.md) .

*앱 자체에서 원격 분석을 가져오려면 어떻게 해야 하나요?*

* Application Insights SDK를 앱에 설치합니다. [Java 웹앱](app-insights-java-get-started.md), [Windows 웹앱](app-insights-asp-net.md)에 대한 방법을 알아봅니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>다음 단계

* [Java용 Application Insights](app-insights-java-get-started.md)
* [Node.js용 Application Insights](app-insights-nodejs.md)
* [ASP.NET용 Application Insights](app-insights-asp-net.md)
