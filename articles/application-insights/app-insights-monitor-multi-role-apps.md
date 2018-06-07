---
title: 여러 구성 요소, 마이크로 서비스 및 컨테이너에 대한 Azure Application Insights 지원 | Microsoft Docs
description: 여러 구성 요소 또는 역할로 이루어진 앱에서 성능 및 사용량을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 9b03aff140eec5b355383447f0a815220d6408e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Application Insights(미리 보기)로 다중 구성 요소 응용 프로그램 모니터링

[Azure Application Insights](app-insights-overview.md)에서는 여러 서버 구성 요소, 역할 또는 서비스로 이루어진 앱을 모니터링할 수 있습니다. 구성 요소 및 이들 간의 관계의 상태는 단일 Application Map에 표시됩니다. 자동 HTTP 상관 관계가 있는 여러 구성 요소를 통해 개별 작업을 추적할 수 있습니다. 컨테이너 진단을 응용 프로그램 원격 분석과 통합하고 상호 연결할 수 있습니다. 응용 프로그램의 모든 구성 요소에 대해 단일 Application Insights 리소스를 사용합니다. 

![다중 구성 요소 응용 프로그램 맵](./media/app-insights-monitor-multi-role-apps/app-map.png)

여기서 '구성 요소'는 큰 응용 프로그램의 작동하는 모든 부분을 의미합니다. 예를 들어 일반적인 비즈니스 응용 프로그램은 웹 브라우저에서 실행하는 클라이언트 코드로 구성되며 하나 이상의 웹앱 서비스와 통신하고 백 엔드 서비스를 차례로 사용합니다. 서버 구성 요소는 클라우드에서 온-프레미스에 호스트되거나 Azure 웹 및 작업자 역할이 될 수 있으며 Docker 또는 Service Fabric과 같은 컨테이너에서 실행될 수 있습니다. 

### <a name="sharing-a-single-application-insights-resource"></a>단일 Application Insights 리소스 공유 

여기에서 핵심 기술은 응용 프로그램의 모든 구성 요소에서 동일한 Application Insights 리소스로 원격 분석을 보내되, 필요한 경우 구성 요소를 구분하기 위해 `cloud_RoleName` 속성을 사용하는 것입니다. Application Insights SDK는 원격 분석 구성 요소 내보내기에 `cloud_RoleName` 속성을 추가합니다. 예를 들어 이 SDK는 `cloud_RoleName` 속성에 웹 사이트 이름 또는 서비스 역할 이름을 추가합니다. 이 값은 원격 분석 이니셜라이저(telemetryinitializer)를 통해 재정의할 수 있습니다. Application Map에서는 `cloud_RoleName` 속성을 사용하여 맵에 있는 구성 요소를 식별합니다.

`cloud_RoleName` 속성을 재정의하는 방법에 대한 자세한 내용은 [속성 추가: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer)를 참조하세요.  

경우에 따라 이것이 적합하지 않을 수 있으며 구성 요소 그룹마다 별도의 리소스를 사용하는 것이 좋을 수 있습니다. 예를 들어, 관리 또는 청구 목적으로 서로 다른 리소스를 사용해야 할 수 있습니다. 별도의 리소스를 사용한다는 것은 모든 구성 요소가 단일 Application Map에 표시되지 않으며 [분석](app-insights-analytics.md)에서 구성 요소 간에 쿼리할 수 없음을 의미합니다. 또한 별도의 리소스도 설정해야 합니다.

이 내용을 염두에 두고, 이 문서의 나머지 부분에서는 여러 구성 요소의 데이터를 하나의 Application Insights 리소스로 보내려고 한다고 가정합니다.

## <a name="configure-multi-component-applications"></a>다중 구성 요소 응용 프로그램 구성

다중 구성 요소 Application Map을 얻으려면 다음과 같은 목표를 달성해야 합니다.

* 응용 프로그램의 각 구성 요소에 Application Insights 패키지의 **최신 시험판을 설치**합니다. 
* 응용 프로그램의 모든 구성 요소에 대해 **단일 Application Insights 리소스를 공유**합니다.
* 미리 보기 블레이드에서 **복합 Application Map을 활성화**합니다.

해당 형식에 적절한 방법을 사용하여 응용 프로그램의 각 구성 요소를 구성합니다. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. 최신 시험판 패키지 설치

각 서버 구성 요소에 대한 프로젝트에서 Application Insights 패키지를 업데이트하거나 설치합니다. Visual Studio를 사용하는 경우:

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 
2. **시험판 포함**을 선택합니다.
3. Application Insights 패키지가 업데이트에 표시되면 선택합니다. 

    그렇지 않으면 해당 패키지를 찾아 설치합니다.
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - 게스트 실행 파일로 실행되는 구성 요소 및 Service Fabric 응용 프로그램에서 실행되는 Docker 컨테이너의 경우
    * Microsoft.ApplicationInsights.ServiceFabric.Native - ServiceFabric 응용 프로그램에서 Reliable Services의 경우
    * Microsoft.ApplicationInsights.Kubernetes - Kubernetes의 Docker에서 실행되는 구성 요소의 경우

### <a name="2-share-a-single-application-insights-resource"></a>2. 단일 Application Insights 리소스 공유

* Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 구성** 또는 **Application Insights > 구성**을 선택합니다. 첫 번째 프로젝트의 경우 마법사를 사용하여 Application Insights 리소스를 만듭니다. 후속 프로젝트에 대해 동일한 리소스를 선택합니다.
* Application Insights 메뉴가 없는 경우 수동으로 구성합니다.

   1. [Azure Portal](https://portal,azure.com)에서 다른 구성 요소에 대해 이미 만든 Application Insights 리소스를 엽니다.
   2. 개요 블레이드에서 Essentials 드롭다운 탭을 열고 **계측 키**를 복사합니다.
   3. 프로젝트에서 ApplicationInsights.config를 열고 다음을 삽입합니다. `<InstrumentationKey>your copied key</InstrumentationKey>`

![계측 키를 .config 파일에 복사합니다.](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. 복합 응용 프로그램 맵 활성화

Azure Portal에서 응용 프로그램에 대한 리소스를 엽니다. CONFIGURE 부제 아래에서 미리 보기를 클릭하여 미리 보기 블레이드를 엽니다. 미리 보기 블레이드에서 *복합 Application Map*을 활성화합니다.

### <a name="4-enable-docker-metrics-optional"></a>4. Docker 메트릭 사용(선택 사항) 

구성 요소가 Azure Windows VM에서 호스트되는 Docker에서 실행되는 경우 컨테이너에서 추가 메트릭을 수집할 수 있습니다. [Azure 진단](../monitoring-and-diagnostics/azure-diagnostics.md) 구성 파일에 다음을 삽입합니다.

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>cloud_RoleName을 사용하여 구성 요소 구분

`cloud_RoleName` 속성은 모든 원격 분석에 연결됩니다. 원격 분석을 시작하는 구성 요소(역할 또는 서비스)를 식별합니다. (여러 서버 프로세스 또는 컴퓨터에서 동시에 실행되는 동일한 역할을 구분하는 cloud_RoleInstance와 다릅니다.)

포털에서 이 속성을 사용하여 원격 분석을 필터 또는 분할할 수 있습니다. 이 예제에서는 CRM API 백 엔드에서 실패를 필터링하여 프런트 엔드 웹 서비스의 정보만 표시하도록 실패 블레이드가 필터링됩니다.

![클라우드 역할 이름에 따라 분할된 메트릭 차트](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>구성 요소 간 추적 작업

개별 작업을 처리하는 동안 한 구성 요소에서 다른 구성 요소로의 호출을 추적할 수 있습니다.


![작업에 대한 원격 분석 표시](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

프론트 엔드 웹 서버와 백 엔드 API 간에 이 작업에 대해 상관 관계가 지정된 원격 분석 목록을 클릭합니다.

![구성 요소 검색](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>다음 단계

* [개발, 테스트 및 프로덕션의 원격 분석 구분](app-insights-separate-resources.md)
