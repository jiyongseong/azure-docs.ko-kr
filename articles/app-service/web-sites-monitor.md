---
title: "Azure App Service에서 앱 모니터링 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure App Service에서 앱을 모니터링하는 방법을 알아보세요."
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: fdc4329806d416811352d0d4dbc8dd3bce25aa0b
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>방법: Azure App Service에서 앱 모니터링
[App Service](http://go.microsoft.com/fwlink/?LinkId=529714)는 [Azure Portal](https://portal.azure.com)에서 기본 제공 모니터링 기능을 제공합니다.
Azure Portal에는 앱 및 App Service 계획에 대한 **할당량** 및 **메트릭**을 검토하고 이러한 메트릭을 기반으로 **경고** 및 **크기 조정**을 자동으로 설정하는 기능이 포함됩니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>할당량 및 메트릭 이해
### <a name="quotas"></a>할당량
App Service에서 호스팅되는 응용 프로그램에는 사용 가능한 리소스에 대한 특정 *한도* 가 적용됩니다. 이러한 한도는 해당 앱과 연결된 **App Service 계획**으로 정의됩니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

응용 프로그램이 **무료** 또는 **공유** 계획에 호스팅되는 경우 앱에서 사용할 수 있는 리소스에 대한 한도는 **할당량**으로 정의됩니다.

응용 프로그램이 **기본**, **표준** 또는 **프리미엄** 계획에 호스팅되는 경우 사용할 수 있는 리소스에 대한 한도는 **크기**(작음, 중간, 큼) 및 **App Service 계획**의 **인스턴스 수**(1, 2, 3, ...)로 설정됩니다.

**무료** 또는 **공유** 앱에 대한 **할당량**은 다음과 같습니다.

* **CPU(Short)**
  * 5분 간격으로 이 응용 프로그램에 대해 허용되는 CPU의 양입니다. 이 할당량은 5분마다 재설정됩니다.
* **CPU(Day)**
  * 하루에 이 응용 프로그램에 대해 허용되는 총 CPU 양입니다. 이 할당량은 자정 UTC에 24시간마다 재설정됩니다.
* **메모리**
  * 이 응용 프로그램에 대해 허용되는 총 메모리 양입니다.
* **대역폭**
  * 하루에 이 응용 프로그램에 대해 허용되는 나가는 총 대역폭 양입니다.
    이 할당량은 자정 UTC에 24시간마다 재설정됩니다.
* **파일 시스템**
  * 허용되는 총 저장소 양입니다.

**기본**, **표준** 및 **프리미엄** 계획에 호스팅되는 앱에 적용 가능한 유일한 할당량은 **파일 시스템**입니다.

다양한 App Service SKU에 사용할 수 있는 특정 할당량, 한도 및 기능에 대한 자세한 정보는 [Azure 구독 서비스 제한](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>할당량 적용
응용 프로그램이 **CPU(부족)**, **CPU(일)**, **대역폭** 할당량을 초과하는 경우 할당량이 재설정될 때까지 응용 프로그램이 중지됩니다. 이 시간 중에는 들어오는 모든 요청에서 **HTTP 403**이 발생합니다.
![][http403]

응용 프로그램 **메모리** 할당량을 초과하면 응용 프로그램이 다시 시작됩니다.

**파일 시스템** 할당량을 초과하면 모든 쓰기 작업이 실패하며 여기에는 로그에 대한 쓰기가 포함됩니다.

App Service 계획을 업그레이드하여 앱에서 할당량을 증가 또는 제거할 수 있습니다.

### <a name="metrics"></a>메트릭
**메트릭**은 앱 또는 App Service 계획의 동작에 대한 정보를 제공합니다.

**응용 프로그램**에 대해 사용 가능한 메트릭은 다음과 같습니다.

* **평균 응답 시간**
  * 앱에서 요청을 처리하는 데 소요된 평균 시간(ms)입니다.
* **평균 메모리 작업 집합**
  * 앱에 사용된 평균 메모리 양(MiB)입니다.
* **CPU 시간**
  * 앱에서 사용한 CPU의 양(초)입니다. 이 메트릭에 대한 자세한 내용은 [CPU 시간 및 CPU 비율](#cpu-time-vs-cpu-percentage)을 참조하세요.
* **데이터 입력**
  * 앱에서 사용한 들어오는 대역폭 양(MiB)입니다.
* **데이터 출력**
  * 앱에서 사용한 나가는 대역폭 양(MiB)입니다.
* **Http 2xx**
  * 결과로 나타나는 HTTP 상태 코드가 200 이하 300 초과인 요청 수입니다.
* **Http 3xx**
  * 결과로 나타나는 HTTP 상태 코드가 300 이하 400 초과인 요청 수입니다.
* **Http 401**
  * 결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다.
* **Http 403**
  * 결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다.
* **Http 404**
  * 결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다.
* **Http 406**
  * 결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다.
* **Http 4xx**
  * 결과로 나타나는 HTTP 상태 코드가 400 이하 500 초과인 요청 수입니다.
* **Http 서버 오류**
  * 결과로 나타나는 HTTP 상태 코드가 500 이하 600 초과인 요청 수입니다.
* **메모리 작업 집합**
  * 현재 앱에 사용된 메모리 양(MiB)입니다.
* **요청**
  * 결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다.

**App Service 계획**에 대해 사용 가능한 메트릭은 다음과 같습니다.

> [!NOTE]
> App Service 계획 메트릭은 **기본**, **표준** 및 **프리미엄** 계층의 계획에만 사용할 수 있습니다.
> 
> 

* **CPU 비율**
  * 계획의 모든 인스턴스 간에 사용된 평균 CPU입니다.
* **메모리 비율**
  * 계획의 모든 인스턴스 간에 사용된 평균 메모리입니다.
* **데이터 입력**
  * 계획의 모든 인스턴스 간에 사용된 평균 들어오는 대역폭입니다.
* **데이터 출력**
  * 계획의 모든 인스턴스 간에 사용된 평균 나가는 대역폭입니다.
* **디스크 큐 길이**
  * 저장소에 대기 중인 평균 읽기 및 쓰기 요청 수입니다. 디스크 큐 길이 값이 높으면 과도한 디스크 I/O로 인해 응용 프로그램의 속도가 느려질 수 있음을 나타냅니다.
* **Http 큐 길이**
  * 처리하기 전에 큐에 배치해야 하는 평균 HTTP 요청 수입니다. HTTP 큐 길이 값이 높거나 증가하면 계획이 과부하 상태에 있음을 나타냅니다.

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 시간 및 CPU 비율
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU 사용량을 반영하는 두 가지 메트릭이 있습니다. **CPU 시간** 및 **CPU 비율**

**CPU 시간**은 해당 할당량 중 하나가 앱에 사용된 CPU 분으로 정의되므로 **무료** 또는 **공유** 계획으로 호스팅된 앱에 유용합니다.

**CPU 백분율**은 확장이 가능하므로 **기본**, **표준** 및 **프리미엄** 계획에서 호스팅되는 앱에 유용합니다. CPU 백분율은 모든 인스턴스에서 전반적인 사용량을 나타내는 좋은 지표입니다.

## <a name="metrics-granularity-and-retention-policy"></a>메트릭 세분성 및 보존 정책
응용 프로그램 및 App Service 계획에 대한 메트릭은 다음 세분성 및 보존 정책에 따라 서비스에 의해 기록 및 집계됩니다.

* **분** 세분성 메트릭은 **30시간** 동안 보존됩니다.
* **시** 세분성 메트릭은 **30일** 동안 보존됩니다.
* **일** 세분성 메트릭은 **30일** 동안 보존됩니다.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure Portal에서 할당량 및 메트릭을 모니터링합니다.
[Azure Portal](https://portal.azure.com)의 응용 프로그램에 영향을 주는 다양한 **할당량** 및 **메트릭**의 상태를 검토할 수 있습니다.

![][quotas]
**할당량**은 설정>**할당량** 아래에서 찾을 수 있습니다. UX를 통해 (1) 할당량 이름, (2) 재설정 간격, (3) 현재 한도 및 (4) 현재 값을 검토할 수 있습니다.

리소스 페이지에서 ![][metrics]
**메트릭**에 직접 액세스할 수 있습니다. 또한 (1) 차트를 **클릭**하고 (2) **차트 편집**을 선택하여 차트를 사용자 지정할 수도 있습니다.
여기에서 표시할 (3) **시간 범위**, (4) **차트 종류**, (5) **메트릭**을 변경할 수 있습니다.  

[서비스 메트릭 모니터링](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)에서 메트릭에 대해 자세히 알아볼 수 있습니다.

## <a name="alerts-and-autoscale"></a>경고 및 자동 크기 조정
App 또는 App Service 계획에 대한 메트릭은 경고에 연결될 수 있습니다. 자세한 내용을 알아보려면 [경고 알림 받기](../monitoring-and-diagnostics/insights-alerts-portal.md)를 참조하세요.

기본, 표준 또는 프리미엄 App Service 계획에 호스팅된 App Service 앱은 **자동 크기 조정**을 지원합니다. 자동 크기 조정에서는 App Service 계획 메트릭을 모니터링하는 규칙을 구성할 수 있습니다. 규칙은 필요에 따라 추가 리소스를 제공하기 위해 인스턴스 수를 눌리거나 줄일 수 있습니다. 규칙을 사용하면 응용 프로그램이 과도하게 프로비전될 때 비용을 절감할 수도 있습니다. [확장 방법](../monitoring-and-diagnostics/insights-how-to-scale.md) 및 [Azure Monitor 자동 크기 조정에 대한 모범 사례](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)에서 자동 크기 조정에 대해 자세히 알아볼 수 있습니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
