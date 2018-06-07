---
title: "Traffic Manager란 | Microsoft Docs"
description: "이 문서는 Traffic Manager란 무엇이며 응용 프로그램에 대한 올바른 트래픽 라우팅 선택인지를 이해하도록 도움을 줍니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-traffic-manager"></a>Traffic Manager 개요

Microsoft Azure Traffic Manager를 사용하면 다양한 데이터 센터에서 서비스 끝점에 대한 사용자 트래픽의 배포를 제어할 수 있습니다. Traffic Manager에서 지원되는 서비스 끝점은 Azure VM, Web Apps 및 클라우드 서비스를 포함합니다. 또한 외부, Azure가 아닌 끝점으로 Traffic Manager를 사용할 수 있습니다.

Traffic Manager는 DNS(Domain Name System)를 사용하여 클라이언트 요청을 트래픽 라우팅 방법 및 끝점의 상태를 기준으로 가장 적절한 끝점으로 리디렉션합니다. Traffic Manager는 다양한 응용 프로그램 요구와 자동 장애 조치(failover)에 맞는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 및 [끝점 모니터링 옵션](traffic-manager-monitoring.md)을 제공합니다. Traffic Manager는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.

## <a name="traffic-manager-benefits"></a>Traffic Manager 이점

Traffic Manager은 다음과 같은 작업에 도움이 될 수 있습니다.

* **중요 응용 프로그램의 가용성 향상**

    Traffic Manager는 끝점을 모니터링하여 끝점이 중단될 경우 자동 장애 조치를 제공함으로써 응용 프로그램에 대한 높은 가용성을 제공합니다.

* **고성능 응용 프로그램의 응답성 향상**

    Azure를 통해 전 세계에 있는 데이터 센터에서 클라우드 서비스나 웹 사이트를 실행할 수 있습니다. Traffic Manager는 클라이언트에서 가장 낮은 네트워크 대기 시간으로 트래픽을 끝점에 연결하여 응용 프로그램의 응답성을 향상시킵니다.

* **가동 중지 시간 없이 서비스 유지 관리 수행**

    가동 중지 시간 없이 응용 프로그램에 계획된 유지 관리 작업을 수행할 수 있습니다. Traffic Manager는 유지 관리가 진행 중인 동안 트래픽을 대체 끝점으로 전달합니다.

* **온-프레미스와 클라우드 기반 응용 프로그램의 결합**

    Traffic Manager는 "클라우드로 버스트", "클라우드로 마이그레이션" 및 "클라우드로 장애 조치(Failover)" 시나리오를 포함한 하이브리드 클라우드 및 온-프레미스 배포에 사용할 수 있도록 외부, Azure가 아닌 끝점을 지원합니다.

* **복잡한 대규모 배포를 위한 트래픽 분산**

    [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용하여 트래픽 라우팅 방법을 더 크고 복잡한 배포에 대한 요구 사항을 지원하는 복잡하고 유연한 규칙을 만들도록 결합할 수 있습니다.

## <a name="how-traffic-manager-works"></a>트래픽 관리자 작동 방식

Azure Traffic Manager를 사용하면 응용 프로그램 끝점에 트래픽 분산을 제어할 수 있습니다. 끝점은 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다.

트래픽 관리자는 다음과 같은 두 가지 주요 이점을 제공합니다.

1. 여러 가지 [트래픽 라우팅 메서드](traffic-manager-routing-methods.md)
2. [끝점 상태 연속 모니터링](traffic-manager-monitoring.md) 및 끝점이 실패할 경우 자동 장애 조치(failover)

클라이언트가 서비스에 연결하려고 시도하면 먼저 IP 주소에 대한 서비스의 DNS 이름을 확인합니다. 그런 다음 클라이언트는 해당 IP 주소에 연결하여 서비스에 액세스합니다.

**가장 중요한 사항은 트래픽 관리자가 DNS 수준에서 작동한다는 점입니다.**  Traffic Manager는 DNS를 사용하여 트래픽 라우팅 메서드의 규칙에 따라 클라이언트를 특정 서비스 끝점에 연결합니다. 클라이언트는 선택한 끝점에 **직접** 연결됩니다. Traffic Manager는 프록시 또는 게이트웨이가 아닙니다. Traffic Manager는 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

### <a name="traffic-manager-example"></a>트래픽 관리자 예제

Contoso Corp에서 새 파트너 포털을 개발했습니다. 이 포털의 URL은 https://partners.contoso.com/login.aspx입니다. 응용 프로그램은 3개의 Azure 지역에서 호스팅됩니다. 가용성을 개선하고 전역 성능을 최대화하려면 Traffic Manager를 사용하여 클라이언트 트래픽을 사용 가능한 가장 가까운 끝점에 배포합니다.

이 구성을 만들기 위해 다음 단계를 완료합니다.

1. 3개의 서비스 인스턴스를 배포합니다. 이러한 배포의 DNS 이름은 'contoso us.cloudapp.net', 'contoso eu.cloudapp.net' 및 'contoso asia.cloudapp.net'입니다.
2. 'contoso.trafficmanager.net'이라는 Traffic Manager 프로필을 만들고 3개의 끝점에서 '성능' 트래픽 라우팅 방법을 사용하도록 구성합니다.
* DNS CNAME 레코드를 사용하여 베니티 도메인 이름 'partners.contoso.com'이 'contoso.trafficmanager.net'을 가리키도록 구성합니다.

![트래픽 관리자 DNS 구성][1]

> [!NOTE]
> Azure 트래픽 관리자에서 베니티 도메인을 사용할 때는 베니티 도메인 이름을 트래픽 관리자 도메인 이름으로 가리키는 CNAME을 사용해야 합니다. DNS 표준은 도메인의 'apex'(또는 루트)에서 CNAME을 만들 수 없습니다. 따라서 'contoso.com'('naked' 도메인이라고도 함)에 대한 CNAME을 만들 수 없습니다. 'contoso.com'의 도메인(예: 'www.contoso.com')에 대해서만 CNAME을 만들 수 있습니다. 이 제한을 해결하려면 간단한 HTTP 리디렉션을 사용하여 대체 이름(예: 'www.contoso.com')으로 'contoso.com'에 대한 요청을 보내는 것이 좋습니다.

### <a name="how-clients-connect-using-traffic-manager"></a>트래픽 관리자를 사용하여 클라이언트를 연결하는 방법

이전 예제를 계속하면 클라이언트가 https://partners.contoso.com/login.aspx 페이지를 요청하는 경우 클라이언트는 다음 단계를 수행하여 DNS 이름을 확인하고 연결을 설정합니다.

![트래픽 관리자를 사용하여 연결 설정][2]

1. 클라이언트는 DNS 쿼리를 구성된 재귀 DNS 서비스로 전송하여 'partners.contoso.com'이라는 이름을 확인합니다. '로컬 DNS' 서비스라고도 하는 재귀 DNS 서비스는 DNS 도메인을 직접 호스트하지 않습니다. 대신 클라이언트에서는 DNS 이름을 확인하는 데 필요한 인터넷에 권한 있는 다양한 DNS 서비스를 연결하는 작업을 오프로드합니다.
2. DNS 이름을 확인하기 위해 재귀 DNS 서비스는 'contoso.com' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 이러한 이름 서버에 연결하여 'partners.contoso.com' DNS 레코드를 요청합니다. contoso.com DNS 서버는 contoso.trafficmanager.net을 가리키는 CNAME 레코드를 반환합니다.
3. 다음으로 재귀 DNS 서비스는 Azure Traffic Manager 서비스에서 제공하는 'trafficmanager.net' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 해당 DNS 서버에 'contoso.trafficmanager.net' DNS 레코드에 대한 요청을 보냅니다.
4. 트래픽 관리자 이름 서버가 요청을 받습니다. 다음 항목에 따라 끝점을 선택합니다.

    - 각 끝점의 구성된 상태(사용하지 않는 끝점은 반환되지 않음)
    - 트래픽 관리자 상태 검사에서 확인된 각 끝점의 현재 상태. 자세한 내용은 [트래픽 관리자 끝점 모니터링](traffic-manager-monitoring.md)을 참조하세요.
    - 선택된 트래픽 라우팅 메서드. 자세한 내용은 [Traffic Manager 라우팅 메서드](traffic-manager-routing-methods.md)를 참조하세요.

5. 선택한 끝점은 다른 DNS CNAME 레코드로 반환됩니다. 이 경우에 contoso us.cloudapp.net을 반환한다고 가정하겠습니다.
6. 다음으로 재귀 DNS 서비스가 'cloudapp.net' 도메인에 대한 이름 서버를 찾습니다. 이러한 이름 서버에 연결하여 'contoso-us.cloudapp.net' DNS 레코드를 요청합니다. 미국 기반 서비스 끝점의 IP 주소를 포함하는 DNS 'A' 레코드가 반환됩니다.
7. 재귀 DNS 서비스는 결과를 통합하고 클라이언트에 단일 DNS 응답을 반환합니다.
8. 클라이언트는 DNS 결과를 받고 지정 IP 주소에 연결합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 응용 프로그램 서비스 끝점에 연결합니다. 해당 끝점은 HTTPS 끝점이므로 클라이언트는 필요한 SSL/TLS 핸드셰이크를 수행한 다음 ‘/login.aspx’ 페이지에 대해 HTTP GET 요청을 합니다.

재귀 DNS 서비스는 받는 DNS 응답을 캐시합니다. 클라이언트 장치의 DNS 확인자도 결과를 캐시합니다. 캐싱을 통해 후속 DNS 쿼리는 다른 이름 서버를 쿼리하는 대신 캐시의 데이터를 사용하여 더 신속하게 답변을 받을 수 있습니다. 캐시의 기간은 각 DNS 레코드의 'TTL(time-to-live)' 속성에 의해 결정됩니다. 짧은 값은 캐시가 빨리 만료되므로 Traffic Manager 이름 서버에 여러 차례의 왕복이 발생합니다. 긴 값은 실패한 끝점에서 트래픽을 멀리 이동하는 데 더 긴 시간이 걸립니다. Traffic Manager를 사용하면 Traffic Manager DNS 응답에 사용되는 TTL을 0초에서 2,147,483,647초([RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)에 따른 최대 범위) 사이로 구성할 수 있으므로 응용 프로그램의 요구에 가장 맞는 값을 선택할 수 있습니다.

## <a name="pricing"></a>가격

가격 책정 정보는 [Traffic Manager 가격 책정](https://azure.microsoft.com/pricing/details/traffic-manager/)을 참조하세요.

## <a name="faq"></a>FAQ

Traffic Manager에 대한 자주 묻는 질문과 답변은 [Traffic Manager FAQ](traffic-manager-FAQs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

트래픽 관리자 [끝점 모니터링 및 자동 장애 조치(failover)](traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.

트래픽 관리자 [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

