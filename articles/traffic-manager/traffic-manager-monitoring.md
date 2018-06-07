---
title: Azure Traffic Manager 끝점 모니터링 | Microsoft Docs
description: 이 문서는 Traffic Manager가 끝점 모니터링 및 자동 끝점 장애 조치를 사용하여 Azure 고객이 고가용성 응용 프로그램을 어떻게 배포할 수 있도록 하는지 이해하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 6d07bd333e4d1663e37a840975cde2d9c73cec9c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211721"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager 끝점 모니터링

Azure Traffic Manager에는 기본 제공된 끝점 모니터링 및 자동 끝점 장애 조치가 포함됩니다. 이 기능을 통해 Azure 지역 장애를 포함하여 끝점 장애에 대해 복원력 있는 고가용성 응용 프로그램을 제공할 수 있습니다.

## <a name="configure-endpoint-monitoring"></a>끝점 모니터링 구성

끝점 모니터링을 구성하려면 Traffic Manager 프로필에서 다음 설정을 지정해야 합니다.

* **프로토콜**. HTTP, HTTPS 또는 TCP를 Traffic Manager가 상태 검사를 위해 끝점을 검색할 때 사용하는 프로토콜로 선택합니다. HTTPS 모니터링은 SSL 인증서가 유효한지 여부를 확인하지 않으며 인증서가 있는지만 확인합니다.
* **포트**. 요청에 사용될 포트를 선택합니다.
* **경로**. 이 구성 설정은 경로 설정을 지정해야 하는 HTTP 및 HTTPS 프로토콜에 대해서만 유효합니다. TCP 모니터링 프로토콜에 대해 이 설정을 제공하면 오류가 발생합니다. HTTP 및 HTTPS 프로토콜의 경우 모니터링 기능이 액세스하는 웹 페이지 또는 파일의 상대 경로와 이름을 제공합니다. 슬래시(/)는 상대 경로로 유효한 항목입니다. 이 값은 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
* **검색 간격**. 이 값은 Traffic Manager 검색 에이전트에서 끝점의 상태를 검사하는 빈도를 지정합니다. 여기서 30초(일반 검색) 및 10초(빠른 검색)의 두 값을 지정할 수 있습니다. 값을 지정하지 않으면 프로필이 기본값인 30초로 설정됩니다. 빠른 검색 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정](https://azure.microsoft.com/pricing/details/traffic-manager) 페이지를 참조하세요.
* **허용되는 오류 수** 이 값은 Traffic Manager 검색 에이전트가 해당 끝점을 비정상 상태로 표시하기 전에 허용하는 오류 수를 지정합니다. 해당 값의 범위는 0에서 9 사이일 수 있습니다. 값이 0이면 단일 모니터링 오류로 인해 해당 끝점이 비정상 상태로 표시될 수 있습니다. 값을 지정하지 않으면 기본값 3이 사용됩니다.
* **모니터링 제한 시간**. 이 속성은 상태 검사 프로브가 끝점으로 전송될 때 Traffic Manager 검색 에이전트가 해당 검사를 오류로 간주하기 전에 기다려야 하는 시간을 지정합니다. 검색 간격이 30초로 설정된 경우 제한 시간 값을 5-10초 사이로 설정할 수 있습니다. 값을 지정하지 않으면 기본값 10초가 사용됩니다. 검색 간격이 10초로 설정된 경우 제한 시간 값을 5-9초 사이로 설정할 수 있습니다. 제한 시간 값을 지정하지 않으면 기본값 9초가 사용됩니다.

![Traffic Manager 끝점 모니터링](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**그림 1: Traffic Manager 끝점 모니터링**

## <a name="how-endpoint-monitoring-works"></a>끝점 모니터링의 작동 방식

모니터링 프로토콜이 HTTP 또는 HTTPS로 설정된 경우 Traffic Manager 검색 에이전트는 주어진 프로토콜, 포트 및 상대 경로를 사용하여 끝점에 GET 요청을 수행합니다. 200-OK 응답이 반환되면 해당 끝점은 정상 상태로 간주됩니다. 응답이 다른 값이거나 지정된 제한 시간 내에 응답이 수신되지 않으면 Traffic Manager 검색 에이전트는 허용되는 오류 수 설정에 따라 다시 시도합니다(이 설정이 0이면 다시 시도하지 않음). 연속 오류 수가 허용되는 오류 수 설정보다 크면 해당 끝점은 비정상 상태로 표시됩니다. 

모니터링 프로토콜이 TCP인 경우 Traffic Manager는 지정된 포트를 사용하여 TCP 연결 요청을 시작합니다. 끝점이 연결 설정 응답으로 요청에 응답하면 해당 상태 검사는 성공으로 표시되고 Traffic Manager 검색 에이전트가 TCP 연결을 다시 설정합니다. 응답이 다른 값이거나 지정된 제한 시간 내에 응답이 수신되지 않으면 Traffic Manager 검색 에이전트는 허용되는 오류 수 설정에 따라 다시 시도합니다(이 설정이 0이면 다시 시도하지 않음). 연속 오류 수가 허용되는 오류 수 설정보다 크면 해당 끝점은 비정상 상태로 표시됩니다.

모든 경우에서 Traffic Manager는 여러 위치에서 검색하며, 각 지역 내에서 연속 오류가 결정됩니다. 이는 끝점이 검색 간격에 사용되는 설정보다 높은 빈도로 Traffic Manager에서 상태 프로브를 받고 있음을 의미합니다.

>[!NOTE]
>HTTP 또는 HTTPS 모니터링 프로토콜의 경우 끝점 쪽의 일반적인 방식은 응용 프로그램 내에서 사용자 지정 페이지를 구현하는 것입니다(예: /health.aspx). 이 경로를 모니터링에 사용하여 성능 카운터 검사 또는 데이터베이스 가용성 확인과 같은 응용 프로그램별 검사를 수행할 수 있습니다. 이러한 사용자 지정 검사에 따르면 페이지는 적절한 HTTP 상태 코드를 반환합니다.

Traffic Manager 프로필의 모든 끝점은 모니터링 설정을 공유합니다. 서로 다른 끝점에 대해 다양한 모니터링 설정을 사용해야 하는 경우 [중첩된 Traffic Manager 프로필](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)을 만들 수 있습니다.

## <a name="endpoint-and-profile-status"></a>끝점 및 프로필 상태

Traffic Manager 프로필 및 끝점을 사용하거나 사용하지 않도록 설정할 수 있습니다. 그러나 Traffic Manager의 자동 설정 및 프로세스에 따라 끝점 상태가 변경될 수도 있습니다.

### <a name="endpoint-status"></a>끝점 상태

특정 끝점을 사용하거나 사용하지 않도록 설정할 수 있습니다. 정상 상태인 기본 서비스는 영향을 받지 않습니다. 끝점의 상태를 변경하여 Traffic Manager 프로필에서의 끝점 가용성을 제어합니다. 끝점 상태가 Disabled이면 Traffic Manager는 해당 상태를 확인하지 않으며 끝점은 DNS 응답에 포함되지 않습니다.

### <a name="profile-status"></a>프로필 상태

프로필 상태 설정을 사용하여 특정 프로필을 사용하거나 사용하지 않도록 설정할 수 있습니다. 끝점 상태가 단일 끝점에 영향을 주지만 프로필 상태는 모든 끝점을 포함한 전체 프로필에 영향을 줍니다. 프로필을 사용하지 않도록 설정하면 끝점의 상태가 확인되지 않고 끝점이 DNS 응답에 포함되지 않습니다. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) 응답 코드가 DNS 쿼리에 대해 반환됩니다.

### <a name="endpoint-monitor-status"></a>끝점 모니터 상태

끝점 모니터 상태는 끝점의 현재 상태를 보여 주는 Traffic Manager 생성 값입니다. 이 설정은 수동으로 변경할 수 없습니다. 끝점 모니터 상태는 끝점 모니터링의 결과와 구성된 끝점 상태가 조합된 것입니다. 끝점 모니터 상태의 가능한 값은 다음 표에 나와 있습니다.

| 프로필 상태 | 끝점 상태 | 끝점 모니터 상태 | 메모 |
| --- | --- | --- | --- |
| 사용 안 함 |사용 |비활성 |프로필이 사용되지 않도록 설정되었습니다. 끝점 상태는 Enabled여도 프로필 상태(Disabled)가 우선적으로 적용됩니다. Disabled 프로필의 끝점은 모니터링되지 않습니다. NXDOMAIN 응답 코드가 DNS 쿼리에 대해 반환됩니다. |
| &lt;일부&gt; |사용 안 함 |사용 안 함 |끝점이 사용되지 않도록 설정되었습니다. 사용하지 않도록 설정된 끝점이 모니터링되지 않습니다. 끝점이 DNS 응답에 포함되지 않으므로 트래픽을 수신하지 않습니다. |
| 사용 |사용 |온라인 |끝점은 모니터링되며 정상 상태입니다. DNS 응답에 포함되며 트래픽을 수신할 수 있습니다. |
| 사용 |사용 |성능 저하됨 |끝점 모니터링 상태 검사에 실패했습니다. 끝점이 DNS 응답에 포함되지 않으며 트래픽을 수신하지 않습니다. <br>단, 모든 끝점의 성능이 저하된 경우는 예외입니다. 이 경우 모든 끝점이 쿼리 응답에 반환되는 것으로 간주됩니다.</br>|
| 사용 |사용 |CheckingEndpoint |끝점이 모니터링되지만 첫 번째 검사 결과가 아직 수신되지 않았습니다. CheckingEndpoint는 일반적으로 프로필에서 끝점을 추가하거나 사용하도록 설정한 직후에 나타나는 일시적 상태입니다. 이 상태의 끝점은 DNS 응답에 포함되며 트래픽을 수신할 수 있습니다. |
| 사용 |사용 |중지됨 |끝점이 가리키는 클라우드 서비스 또는 웹앱이 실행되지 않습니다. 클라우드 서비스 또는 웹앱 설정을 확인합니다. 이 동작은 끝점이 중첩된 끝점 형식이고 자식 프로필이 사용되지 않거나 비활성 상태인 경우에도 발생할 수 있습니다. <br>Stopped 상태의 끝점은 모니터링되지 않습니다. DNS 응답에 포함되지 않으며 트래픽을 수신하지 않습니다. 단, 모든 끝점의 성능이 저하된 경우는 예외입니다. 이 경우 모든 끝점이 쿼리 응답에 반환되는 것으로 간주됩니다.</br>|

중첩된 끝점의 끝점 모니터 상태가 계산되는 방식에 대한 자세한 내용은 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

>[!NOTE]
> 중지된 엔드포인트 상태는 웹 응용 프로그램이 표준 계층 이상에서 실행되고 있지 않은 경우 App Service에서 발생할 수 있습니다. 자세한 내용은 [App Service와 Traffic Manager 통합](/azure/app-service/web-sites-traffic-manager)을 참조하세요.

### <a name="profile-monitor-status"></a>프로필 모니터 상태

프로필 모니터 상태는 구성된 프로필 상태와 모든 끝점에 대한 끝점 모니터 상태 값을 조합한 것입니다. 가능한 값은 다음 표에 설명되어 있습니다.

| 프로필 상태(구성된 대로) | 끝점 모니터 상태 | 프로필 모니터 상태 | 메모 |
| --- | --- | --- | --- |
| 사용 안 함 |&lt;일부&gt; 또는 정의된 끝점이 없는 프로필 |사용 안 함 |프로필이 사용되지 않도록 설정되었습니다. |
| 사용 |하나 이상의 끝점 상태가 Degraded입니다. |성능 저하됨 |끝점에 주의가 필요한지 확인하려면 개별 끝점 상태 값을 검토합니다. |
| 사용 |하나 이상의 끝점 상태가 Online입니다. Degraded 상태의 끝점이 없습니다. |온라인 |서비스가 트래픽을 허용합니다. 별도의 작업이 필요하지 않습니다. |
| 사용 |하나 이상의 끝점 상태가 “CheckingEndpoint”입니다. Online 또는 Degraded 상태인 끝점이 없습니다. |CheckingEndpoints |이러한 전환 상태는 프로필을 만들거나 사용하도록 설정하면 발생합니다. 처음으로 끝점 상태가 검사됩니다. |
| 사용 |프로필의 모든 끝점 상태는 Disabled 또는 Stopped이거나 프로필에 정의된 끝점이 없습니다. |비활성 |끝점이 활성 상태가 아니지만 프로필은 여전히 Enabled입니다. |

## <a name="endpoint-failover-and-recovery"></a>끝점 장애 조치 및 복구

Traffic Manager는 비정상 끝점을 포함하는 모든 끝점의 상태를 정기적으로 검사합니다. Traffic Manager는 끝점이 정상 상태가 되는지 감지한 후 다시 회전에 포함시킵니다.

다음 이벤트 중 하나가 발생할 경우 끝점이 비정상 상태입니다.
- 모니터링 프로토콜이 HTTP 또는 HTTPS인 경우:
    - 200이 아닌 응답을 받았습니다(다양한 2xx 코드 또는 301/302 리디렉션).
- 모니터링 프로토콜이 TCP인 경우: 
    - Traffic Manager가 연결을 다시 설정하기 위해 보낸 동기화 요청에 대한 응답으로 ACK 또는 SYN-ACK 이외의 응답을 받았습니다.
- 시간 초과. 
- 끝점에 도달하지 못하게 하는 다른 모든 연결 문제입니다.

실패한 검사의 문제 해결에 대한 자세한 내용은 [Azure Traffic Manager의 Degraded 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)을 참조하세요. 

그림 2의 다음 타임라인은 모니터링 프로토콜이 HTTP이고, 검색 간격이 30초이며, 허용되는 오류 수가 3개이고, 제한 시간 값이 10초이며, DNS TTL이 30초인 설정을 가진 Traffic Manager 끝점의 모니터링 프로세스에 대한 자세한 설명입니다.

![Traffic Manager 끝점 장애 조치 및 장애 복구 순서](./media/traffic-manager-monitoring/timeline.png)

**그림 2: Traffic Manager 끝점 장애 조치(failover) 및 복구 순서**

1. **GET**. 각 끝점에서 Traffic Manager 모니터링 시스템은 모니터링 설정에 지정된 경로에 대해 GET 요청을 수행합니다.
2. **200 OK**. 모니터링 시스템에서 10초 내에 HTTP 200 OK 메시지가 반환됩니다. 이 응답을 수신하면 서비스를 사용할 수 있는 것으로 인식합니다.
3. **확인 간격 30초**. 끝점 상태 검사가 30초마다 반복됩니다.
4. **서비스를 사용할 수 없음**. 서비스를 사용할 수 없게 됩니다. Traffic Manager는 다음 상태 검사까지 알지 못합니다.
5. **모니터링 경로 액세스 시도**. 모니터링 시스템은 GET 요청을 수행하지만 10초의 기간 내에 응답을 수신하지 않습니다(또는 200 이외의 응답을 수신할 수 있음). 30초 간격으로 3회 더 시도합니다. 그 중 한 번의 시도가 성공하면 시도 횟수가 다시 설정됩니다.
6. **상태가 Degraded로 설정**. 네 번째 연속 실패 후 모니터링 시스템은 사용할 수 없는 끝점을 Degraded로 표시합니다.
7. **트래픽이 다른 끝점으로 우회됨**. Traffic Manager DNS 이름 서버가 업데이트되고 Traffic Manager에 의해 DNS 쿼리에 대한 응답으로 끝점이 더 이상 반환되지 않습니다. 새 연결은 사용 가능한 다른 끝점으로 전달됩니다. 그러나 이 끝점을 포함하는 이전 DNS 응답은 재귀 DNS 서버 및 DNS 클라이언트에 의해 계속 캐시될 수 있으므로 클라이언트는 DNS 캐시가 만료될 때까지 끝점을 계속 사용합니다. DNS 캐시가 만료되면 클라이언트는 새 DNS 쿼리를 만들고 다른 끝점으로 전달됩니다. 캐시 기간은 Traffic Manager 프로필의 TTL 설정에 의해 제어됩니다(예: 30초).
8. **계속 상태 검사**. Traffic Manager는 Degraded 상태에 있는 끝점의 상태를 계속해서 검사합니다. Traffic Manager는 끝점이 정상 상태가 되면 이를 감지합니다.
9. **서비스가 다시 온라인 상태가 됩니다**. 서비스를 사용할 수 있습니다. 모니터링 시스템에서 다음 상태 검사를 수행할 때까지 끝점은 Traffic Manager에서 Degraded 상태로 유지됩니다.
10. **서비스에 대한 트래픽 다시 시작**. Traffic Manager는 GET 요청을 전송하고 200 OK 상태 응답을 수신합니다. 서비스는 정상 상태로 돌아왔습니다. Traffic Manager 이름 서버는 업데이트되고 DNS 응답에서 서비스의 DNS 이름을 전달하기 시작합니다. 다른 끝점으로 반환되는 캐시된 DNS 응답이 만료되고 다른 끝점에 대한 기존 연결이 종료됨에 따라 트래픽이 또 다시 끝점으로 반환됩니다.

    > [!NOTE]
    > Traffic Manager는 DNS 수준에서 작동하므로 모든 끝점에 대한 기존 연결에 영향을 미칠 수 없습니다. 끝점 간에 트래픽을 전달할 때는(프로필 설정을 변경하거나 장애 조치 또는 장애 복구 중) Traffic Manager가 사용 가능한 끝점에 새 연결을 전달합니다. 그러나 다른 끝점은 해당 세션이 종료될 때까지 기존 연결을 통해 트래픽을 계속 수신할 수 있습니다. 트래픽이 기존 연결에서 비워지도록 하기 위해 응용 프로그램은 각 끝점에 사용된 세션 기간을 제한해야 합니다.

## <a name="traffic-routing-methods"></a>트래픽 라우팅 방법

끝점이 Degraded 상태가 되면 더 이상 DNS 쿼리에 대한 응답으로 반환되지 않습니다. 대신 대체 끝점이 선택되어 반환됩니다. 프로필에 구성된 트래픽 라우팅 방법은 대체 끝점을 선택하는 방법을 결정합니다.

* **우선 순위**. 끝점은 우선 순위가 정해진 목록을 구성합니다. 목록에 사용 가능한 첫 번째 끝점은 항상 반환됩니다. 끝점 상태가 Degraded면 사용 가능한 다음 끝점이 반환됩니다.
* **가중치 적용**. 사용 가능한 모든 끝점이 할당된 가중치 및 다른 사용 가능한 끝점의 가중치를 기반으로 임의로 선택됩니다.
* **성능**. 최종 사용자에게 가장 가까운 끝점이 반환됩니다. 해당 끝점을 사용할 수 없는 경우 Traffic Manager가 트래픽을 다음으로 가까운 Azure 지역의 끝점으로 이동합니다. [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)을 사용하여 성능 트래픽 라우팅에 대한 대체 장애 조치 계획을 구성할 수 있습니다.
* **지리적**. 쿼리 요청 IP에 따라 지리적 위치를 제공하도록 매핑된 끝점이 반환됩니다. 해당 끝점을 사용할 수 없는 경우 지리적 위치는 프로필의 한 끝점에만 매핑할 수 있으므로 장애 조치(failover)할 다른 끝점이 선택되지 않습니다(자세한 내용은 [FAQ](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) 참조). 모범 사례로, 지리적 라우팅을 사용하는 경우 고객이 둘 이상의 끝점을 프로필 끝점으로 지정하여 중첩된 Traffic Manager 프로필을 사용하는 것이 좋습니다.

자세한 내용은 [Traffic Manager 트래픽 라우팅 방법](traffic-manager-routing-methods.md)을 참조하세요.

> [!NOTE]
> 일반적인 트래픽 라우팅 동작의 한 가지 예외는 모든 적격 끝점이 Degraded 상태에 있을 때 발생합니다. Traffic Manager는 “최선”을 다하고 *모든 Degraded 상태 끝점이 실제로 Online 상태인 것처럼 응답*합니다. 이 동작은 DNS 응답에 어떠한 끝점도 반환하지 않는 것보다 바람직한 방법입니다. Disabled 또는 Stopped 끝점은 모니터링되지 않으므로 트래픽에 적합한 것으로 간주되지 않습니다.
>
> 이 문제는 다음과 같은 서비스의 부적절한 구성 때문에 주로 발생합니다.
>
> * ACL[액세스 제어 목록]이 Traffic Manager 상태 검사를 차단합니다.
> * Traffic Manager 프로필에서 모니터링 포트 또는 프로토콜이 잘못 구성되었습니다.
>
> 이러한 동작은 Traffic Manager 상태 검사가 제대로 구성되지 않는 경우 발생하며 트래픽 라우팅에서 Traffic Manager가 제대로 *작동하는* 것처럼 보일 수 있습니다. 하지만 이 경우에 전체 응용 프로그램 가용성에 영향을 미치는 끝점 장애 조치는 발생할 수 없습니다. 프로필에 Degraded 상태 대신 Online 상태가 표시되는지 확인하는 것이 중요합니다. Online 상태는 Traffic Manager 상태 검사가 예상대로 작동 중임을 나타냅니다.

실패한 상태 검사의 문제 해결에 대한 자세한 내용은 [Azure Traffic Manager의 Degraded 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)을 참조하세요.



## <a name="next-steps"></a>다음 단계

[Traffic Manager 작동 방식](traffic-manager-how-traffic-manager-works.md)

Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.

[Traffic Manager 프로필을 만드는](traffic-manager-manage-profiles.md)

Traffic Manager 끝점에서 [Degraded 상태 문제를 해결](traffic-manager-troubleshooting-degraded.md)합니다.
