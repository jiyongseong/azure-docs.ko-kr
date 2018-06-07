---
title: "Azure Traffic Manager 성능 고려 사항 | Microsoft Docs"
description: "Traffic Manager의 성능 및 Traffic Manager 사용 시 웹 사이트의 성능을 테스트 하는 방법에 대한 이"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager 성능 고려 사항

이 페이지에서는 Traffic Manager를 사용할 때의 성능 고려 사항에 대해 설명합니다. 다음과 같은 시나리오를 고려해 보세요.

WestUS 및 EastAsia 지역에 웹 사이트 인스턴스가 있습니다. 인스턴스 중 하나가 트래픽 관리자 프로브에 대한 상태 검사를 실패합니다. 응용 프로그램 트래픽은 정상 지역으로 전송됩니다. 이 장애 조치는 예상되는 동작이지만 성능은 이제 멀리 떨어진 지역으로 이동하는 트래픽의 대기 시간에 따라 문제가 될 수 있습니다.

## <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager 성능 고려 사항

Traffic Manager가 웹 사이트에 미칠 수 있는 유일한 성능 영향은 초기 DNS 조회입니다. Traffic Manager 프로필의 이름에 대한 DNS 요청은 trafficmanager.net 영역을 호스팅하는 Microsoft DNS 루트 서버에서 처리됩니다. Traffic Manager는 Traffic Manager 정책 및 검색 결과에 기반하여 Microsoft DNS 루트 서버에 정보를 표시하고 정기적으로 업데이트합니다. 따라서 초기 DNS 조회 중에도 DNS 쿼리가 Traffic Manager에 전송되지 않습니다.

Traffic Manager는 DNS 이름 서버, API 서비스, 저장소 계층 및 서비스를 모니터링 하는 끝점 등 여러 구성 요소로 이루어져 있습니다. Traffic Manager 서비스 구성 요소가 실패해도 Traffic Manager 프로필과 연결된 DNS 이름에 아무런 영향이 없습니다. Microsoft DNS 서버에 있는 레코드는 변경되지 않고 유지됩니다. 그러나 끝점 모니터링 및 DNS 업데이트는 일어나지 않습니다. 따라서, Traffic Manager는 기본 사이트가 작동을 중단하는 경우 DNS가 장애 조치 사이트를 가리키도록 업데이트하지 못합니다.

DNS 이름 확인은 신속하고 결과는 캐시됩니다. 초기 DNS 조회 속도는 클라이언트가 이름 확인을 위해 사용하는 DNS 서버에 따라 다릅니다. 일반적으로 클라이언트는 50ms 이내에 DNS 조회를 완료할 수 있습니다. 조회 결과는 DNS TTL(Time-to-Live)기간 동안 캐시됩니다. Traffic Manager에 대한 기본 TTL은 300 초입니다.

트래픽은 Traffic Manager를 통해 전달되지 않습니다. DNS 조회가 완료되면 클라이언트는 웹 사이트 인스턴스에 대한 IP 주소를 갖습니다. 클라이언트는 해당 주소에 직접 연결하고 Traffic Manager를 통해 전달하지 않습니다. 사용자가 선택한 Traffic Manager 정책은 DNS 성능에 아무런 영향도 미치지 않습니다. 그러나 성능 라우팅 방법은 응용 프로그램 환경에 부정적인 영향을 미칠 수 있습니다. 예를 들어 사용자의 정책에 따라 트래픽을 북아메리카로부터 아시아에 호스트된 인스턴스로 리디렉션하는 경우 해당 세션에 대한 네트워크 대기 시간이 성능 문제가 될 수도 있습니다.

## <a name="measuring-traffic-manager-performance"></a>Traffic Manager 성능 측정

Traffic Manager 프로필의 성능 및 동작을 이해하는 데 사용할 수 있는 여러 웹 사이트가 있습니다. 이러한 대다수의 사이트는 무료이지만 제한이 있을 수도 있습니다. 일부 사이트는 유료로 향상된 모니터링 및 보고를 제공합니다.

이러한 사이트에 있는 도구는 DNS 대기 시간를 측정하고 전 세계의 클라이언트 위치에 대한 확인된 IP 주소를 표시합니다. 이러한 도구 대부분은 DNS 결과를 캐시하지 않습니다. 따라서 도구는 테스트가 실행될 때마다 전체 DNS 조회를 보여줍니다. 사용자 고유의 클라이언트에서 테스트할 경우 전체 DNS 조회 성능을 TTL 기간 동안에만 한 번 경험합니다.

## <a name="sample-tools-to-measure-dns-performance"></a>DNS 성능을 측정하는 샘플 도구

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS는 많은 성능 도구를 제공합니다. DNS 비교 도구를 사용하면 DNS 이름을 확인하는 데 걸리는 시간 및 다른 DNS 서비스 공급자들과 비교할 때 어떤지 확인할 수 있습니다.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    가장 간단한 도구 중 하나는 WebSitePulse입니다. DNS 확인 시간, 첫 번째 바이트, 마지막 바이트 및 기타 성능 통계를 보려면 URL을 입력하십시오. 3개의 서로 다른 테스트 위치를 선택할 수 있습니다. 이 예에서는 첫 번째 실행에서 DNS 조회에 0.204초가 소요됨을 보여줍니다.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    결과가 캐시되므로 동일한 Traffic Manager 끝점에 대한 두 번째 테스트의 경우 DNS 조회에 0.002초가 소요됩니다.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA 앱 가상 모니터](https://asm.ca.com/en/checkit.php)

    이전에 Watchmouse Check Website 도구로 알려진 이 사이트는 여러 지리적 지역의 DNS 확인 시간을 동시에 보여줍니다. DNS 확인 시간, 연결 시간 및 여러 지리적 위치에서의 속도를 보려면 URL을 입력하십시오. 이 테스트를 사용하면 어떤 호스티드 서비스가 전 세계의 서로 다른 위치에 대해 반환되는지 볼 수 있습니다.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    이 도구는 웹 페이지의 각 요소에 대한 성능 통계를 제공합니다. Page Analysis 탭은 DNS 조회에 소요된 시간의 백분율을 보여줍니다.

* [Azure DNS란?](http://www.whatsmydns.net/)

    이 사이트는 20곳의 서로 다른 위치에서 DNS 조회를 수행하여 지도에 결과를 표시합니다.

* [Dig 웹 인터페이스](http://www.digwebinterface.com)

    이 사이트에서는 CNAME 및 A 기록을 포함한 보다 자세한 DNS 정보를 보여줍니다. 'Colorize output' 및 'Stats' 옵션을 확인하고 네임서버에서 ‘All’을 선택합니다.

## <a name="next-steps"></a>다음 단계

[Traffic Manager 트래픽 라우팅 방법 정보](traffic-manager-routing-methods.md)

[Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)

[Traffic Manager 작업(REST API 참조)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)

