---
title: 프리미엄 Azure Redis Cache에 Virtual Network 구성 | Microsoft Docs
description: 프리미엄 계층 Azure Redis Cache 인스턴스에 대한 Virtual Network 지원을 만들고 관리하는 방법에 대해 알아봅니다.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 250c66c3a39519a6eddc1ecb51259ec1944c88a9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>프리미엄 Azure Redis Cache에 Virtual Network 지원을 구성하는 방법 
Azure Redis Cache에는 클러스터링, 지속성, 가상 네트워크 지원 등의 프리미엄 계층 기능을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다. VNet은 클라우드의 개인 네트워크입니다. Azure Redis Cache 인스턴스를 VNet으로 구성한 경우 클라이언트에서만 공개적으로 주소를 지정할 수 없으며, VNet 내의 가상 머신 및 응용 프로그램에서만 액세스할 수 있습니다. 이 문서에서는 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법을 설명합니다.

> [!NOTE]
> Azure Redis Cache는 클래식 및 Resource Manager VNet을 둘 다 지원합니다.
> 
> 

다른 프리미엄 캐시 기능에 대한 자세한 내용은 [Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)를 참조하세요.

## <a name="why-vnet"></a>VNet을 사용하는 이유
[Azure VNet(Virtual Network)](https://azure.microsoft.com/services/virtual-network/) 배포 - Azure Redis Cache 및 서브넷에 대한 보안 및 격리를 향상하고 액세스 제어 정책과 액세스를 추가로 제한하는 기타 기능을 제공합니다.

## <a name="virtual-network-support"></a>가상 네트워크 지원
VNet(Virtual Network) 지원은 캐시를 만드는 중에 **새 Redis 캐시** 블레이드에 구성됩니다. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

프리미엄 가격 책정 계층을 선택한 경우 캐시와 동일한 구독 및 위치에 있는 VNet을 선택하여 Redis VNet 통합을 구성할 수 있습니다. 새 VNet을 사용하려면 먼저 [Azure Portal을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network) 또는 [Azure Portal을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)의 단계에 따라 VNet을 만든 다음 **새 Redis Cache** 블레이드로 돌아가 프리미엄 캐시를 만들고 구성합니다.

새 캐시에 대해 VNet을 구성하려면 **새 Redis Cache** 블레이드에서 **Virtual Network**를 클릭하고 드롭다운 목록에서 원하는 VNet을 선택합니다.

![가상 네트워크][redis-cache-vnet]

**서브넷** 드롭다운 목록에서 원하는 서브넷을 선택한 후 원하는 **고정 IP 주소**를 지정합니다. 클래식 VNet을 사용하는 경우 **고정 IP 주소** 필드는 선택적이며, 아무 항목도 지정하지 않으면 선택한 서브넷에서 하나가 자동으로 선택됩니다.

> [!IMPORTANT]
> Resource Manager VNet에 Azure Redis Cache를 배포하는 경우 캐시가 Azure Redis Cache 인스턴스를 제외하고 다른 리소스를 포함하지 않는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 서브넷의 Resource Manager VNet에 Azure Redis Cache를 배포하려고 하면 배포에 실패합니다.
> 
> 

![가상 네트워크][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure는 각 서브넷 내의 일부 IP 주소를 예약하며, 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 Azure 서비스에 사용되는 3개 이상의 주소와 함께 프로토콜 적합성을 위해 예약됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> 서브넷의 각 Redis 인스턴스는 Azure VNET 인프라에서 사용하는 IP 주소 외에도, 분할된 데이터베이스당 IP 주소 2개, 부하 분산 장치용 추가 IP 주소 1개를 사용합니다. 클러스터되지 않은 캐시는 분할된 데이터베이스 1개가 있는 것으로 간주됩니다.
> 
> 

캐시가 만들어지면 **리소스 메뉴** 블레이드에서 **Virtual Network**를 클릭하여 VNet에 대한 구성을 볼 수 있습니다.

![가상 네트워크][redis-cache-vnet-info]

VNet을 사용하는 경우 Azure Redis Cache 인스턴스에 연결하려면 다음 예제에 표시된 대로 연결 문자열에 캐시의 호스트 이름을 지정합니다.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet FAQ
다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

* [Azure Redis Cache 및 VNet의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [캐시가 VNET에서 작동하는지 확인하려면 어떻게 해야 하나요?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [VNET에서 내 Redis Cache에 연결하려고 하면 원격 인증서가 유효하지 않다는 오류가 표시되는 이유는 무엇인가요?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [표준 또는 기본 캐시에 VNet을 사용할 수 있나요?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [일부 서브넷에서만 Redis Cache 생성이 실패하는 이유는 무엇인가요?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [서브넷 주소 공간 요구 사항은 무엇입니까?](#what-are-the-subnet-address-space-requirements)
* [VNET에서 캐시를 호스팅하는 경우 모든 캐시 기능이 작동하나요?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Azure Redis Cache 및 VNet의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?
Azure Redis Cache가 VNet에 호스트된 경우 다음 표의 포트가 사용됩니다. 

>[!IMPORTANT]
>다음 표의 포트가 차단되면 캐시가 제대로 작동하지 않을 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNet에서 Azure Redis Cache를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.
> 
> 

- [아웃바운드 포트 요구 사항](#outbound-port-requirements)
- [인바운드 포트 요구 사항](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>아웃바운드 포트 요구 사항

7가지 아웃바운드 포트 요구 사항이 있습니다.

- 원하는 경우 클라이언트의 온-프레미스 감사 장치를 통해 인터넷에 대한 모든 아웃바운드 연결을 설정할 수 있습니다.
- 포트 중 3개는 Azure Storage 및 Azure DNS에 서비스하는 Azure 끝점으로 트래픽을 전송합니다.
- 나머지 포트는 다양한 범위에 사용되고 내부 Redis 서브넷 통신에도 사용됩니다. 내부 Redis 서브넷 통신에 필요한 서브넷 NSG 규칙은 없습니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 로컬 IP | 원격 IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |아웃바운드 |TCP |Azure Storage/PKI(인터넷)에 대한 Redis 종속성 | (Redis 서브넷) |* |
| 53 |아웃바운드 |TCP/UDP |DNS(인터넷/VNet)에 대한 Redis 종속성 | (Redis 서브넷) |* |
| 8443 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) | (Redis 서브넷) |
| 10221-10231 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) | (Redis 서브넷) |
| 20226 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |
| 13000-13999 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |
| 15000-15999 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |
| 6379-6380 |아웃바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |


#### <a name="inbound-port-requirements"></a>인바운드 포트 요구 사항

8개의 인바운드 포트 범위 요구 사항이 있습니다. 이러한 범위의 인바운드 요청은 동일한 VNET에서 호스트되는 다른 서비스로부터 인바운드로 진행되거나 Redis 서브넷 통신 내부로 진행됩니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 로컬 IP | 원격 IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |인바운드 |TCP |Redis에 대한 클라이언트 통신, Azure 부하 분산 | (Redis 서브넷) | (redis 서브넷), Virtual Network, Azure Load Balancer |
| 8443 |인바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |
| 8500 |인바운드 |TCP/UDP |Azure 부하 분산 | (Redis 서브넷) |Azure Load Balancer |
| 10221-10231 |인바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷), Azure Load Balancer |
| 13000-13999 |인바운드 |TCP |Redis 클러스터에 대한 클라이언트 통신, Azure 부하 분산 | (Redis 서브넷) |Virtual Network, Azure Load Balancer |
| 15000-15999 |인바운드 |TCP |Redis 클러스터에 대한 클라이언트 통신, Azure 부하 분산 | (Redis 서브넷) |Virtual Network, Azure Load Balancer |
| 16001 |인바운드 |TCP/UDP |Azure 부하 분산 | (Redis 서브넷) |Azure Load Balancer |
| 20226 |인바운드 |TCP |Redis에 대한 내부 통신 | (Redis 서브넷) |(Redis 서브넷) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>추가 VNET 네트워크 연결 요구 사항

가상 네트워크에서 처음에 충족되지 않는 Azure Redis Cache에 대한 네트워크 연결 요구 사항이 있습니다. Azure Redis Cache를 가상 네트워크 내에서 사용하는 경우 제대로 작동되려면 다음 항목이 모두 필요합니다.

* 전세계 Azure Storage 끝점에 아웃바운드 네트워크 연결. Azure Redis Cache 인스턴스와 동일한 지역에 있는 끝점 뿐만 아니라 **다른** Azure 지역에 있는 저장소 끝점을 포함합니다. 다음 DNS 도메인에서 Azure Storage 끝점은 다음을 확인합니다. *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* 및 *file.core.windows.net* 
* *ocsp.msocsp.com*, *mscrl.microsoft.com* 및 *crl.microsoft.com*에 대한 아웃바운드 네트워크 연결입니다. 이 연결은 SSL 기능을 지원하는 데 필요합니다.
* 가상 네트워크에 대한 DNS 구성은 이전 시점에 언급된 끝점 및 도메인을 모두 해결할 수 있어야 합니다. 유효한 DNS 인프라를 구성하고 가상 네트워크에 유지 관리하여 DNS 요구를 충족할 수 있습니다.
* 다음 DNS 도메인에서 확인되는 다음 Azure 모니터링 끝점에 대한 아웃바운드 네트워크 연결: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>캐시가 VNET에서 작동하는지 확인하려면 어떻게 해야 하나요?

>[!IMPORTANT]
>VNET에서 호스트되는 Azure Redis Cache 인스턴스에 연결할 때 캐시 클라이언트는 동일한 VNET 또는 VNET 피어링이 사용하도록 설정된 VNET에 있어야 합니다. 여기에는 모든 테스트 응용 프로그램 또는 진단 핑 도구가 포함됩니다. 클라이언트 응용 프로그램이 호스트된 위치와 상관없이 네트워크 보안 그룹은 클라이언트의 네트워크 트래픽이 Redis 인스턴스에 도달할 수 있도록 구성되어야 합니다.
>
>

이전 섹션에 설명된 대로 포트 요구 사항이 구성되면 다음 단계를 수행하여 캐시가 작동하는지 확인할 수 있습니다.

- 모든 캐시 노드를 [다시 부팅](cache-administration.md#reboot)합니다. [인바운드 포트 요구 사항](cache-how-to-premium-vnet.md#inbound-port-requirements) 및 [아웃바운드 포트 요구 사항](cache-how-to-premium-vnet.md#outbound-port-requirements)에 설명된 대로 모든 필요한 캐시 종속성에 연결할 수 없는 경우 캐시가 다시 시작되지 않을 수 있습니다.
- Azure Portal의 캐시 상태에 보고된 대로 캐시 노드가 다시 시작되었으면 다음 테스트를 수행할 수 있습니다.
  - 캐시와 동일한 VNET 내에 있는 컴퓨터에서 [tcping](https://www.elifulkerson.com/projects/tcping.php)을 사용하여 캐시 끝점(포트 6380 사용)을 ping합니다. 예: 
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    `tcping` 도구에서 포트가 열려 있다고 보고하면 VNET에 있는 클라이언트에서 연결하는 데 캐시를 사용할 수 있습니다.

  - 또 다른 테스트 방법은 캐시에 연결하고 캐시에서 일부 항목을 추가 및 검색하는 테스트 캐시 클라이언트(StackExchange.Redis를 사용하는 간단한 콘솔 응용 프로그램일 수 있음)를 만드는 것입니다. 캐시와 동일한 VNET에 있는 VM에 샘플 클라이언트 응용 프로그램을 설치하고 실행하여 캐시에 대한 연결을 확인합니다.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>VNET에서 내 Redis Cache에 연결하려고 하면 원격 인증서가 유효하지 않다는 오류가 표시되는 이유는 무엇인가요?

VNET의 Redis Cache에 연결하려고 하면 다음과 같은 인증서 유효성 검사 오류가 나타납니다.

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

IP 주소를 통해 호스트에 연결하는 것이 원인일 수 있습니다. 호스트 이름을 사용하는 것이 좋습니다. 즉, 다음을 사용합니다.     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

다음 연결 문자열과 유사한 IP 주소는 사용하지 마세요.

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

DNS 이름을 확인할 수 없는 경우 일부 클라이언트 라이브러리에 StackExchange.Redis 클라이언트에서 제공하는 `sslHost`와 같은 구성 옵션이 포함됩니다. 이 옵션을 사용하면 인증서 유효성 검사에 사용되는 호스트 이름을 재정의할 수 있습니다. 예: 

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>표준 또는 기본 캐시에 VNet을 사용할 수 있나요?
VNet은 프리미엄 캐시에만 사용할 수 있습니다.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>일부 서브넷에서만 Redis Cache 생성이 실패하는 이유는 무엇인가요?
Resource Manager VNet에 Azure Redis Cache를 배포하는 경우 캐시가 다른 리소스를 포함하지 않는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 Resource Manager VNet 서브넷에 Azure Redis Cache를 배포하려고 하면 배포에 실패합니다. 새 Redis Cache를 만들려면 서브넷 내의 기존 리소스를 삭제해야 합니다.

사용할 수 있는 충분한 IP 주소가 있으면, 클래식 VNet에 여러 유형의 리소스를 배포할 수 있습니다.

### <a name="what-are-the-subnet-address-space-requirements"></a>서브넷 주소 공간 요구 사항은 무엇입니까?
Azure는 각 서브넷 내의 일부 IP 주소를 예약하며, 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 Azure 서비스에 사용되는 3개 이상의 주소와 함께 프로토콜 적합성을 위해 예약됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

서브넷의 각 Redis 인스턴스는 Azure VNET 인프라에서 사용하는 IP 주소 외에도, 분할된 데이터베이스당 IP 주소 2개, 부하 분산 장치용 추가 IP 주소 1개를 사용합니다. 클러스터되지 않은 캐시는 분할된 데이터베이스 1개가 있는 것으로 간주됩니다.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>VNET에서 캐시를 호스팅하는 경우 모든 캐시 기능이 작동하나요?
캐시가 VNET의 일부인 경우 VNET의 클라이언트만 캐시에 액세스할 수 있습니다. 결과적으로 이번에는 다음 캐시 관리 기능이 작동하지 않습니다.

* Redis 콘솔 - Redis 콘솔은 VNET 외부에 있는 로컬 브라우저에서 실행되기 때문에 캐시에 연결할 수 없습니다.


## <a name="use-expressroute-with-azure-redis-cache"></a>Azure Redis Cache에서 ExpressRoute 사용

고객은 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 회로를 가상 네트워크 인프라로 연결할 수 있습니다. 따라서 Azure로 온-프레미스 네트워크를 확장합니다. 

기본적으로 새로 만든 ExpressRoute 회로는 VNET에서 강제 터널링(0.0.0.0/0 기본 경로 보급 알림)을 수행하지 않습니다. 결과적으로 아웃바운드 인터넷 연결은 VNET에서 직접 허용되며, 클라이언트 응용 프로그램은 Azure Redis Cache를 포함한 다른 Azure 끝점에 연결할 수 있습니다.

그러나 고객의 일반적인 구성에서는 강제 터널링(기본 경로 보급 알림)을 사용하여 아웃바운드 인터넷 트래픽을 온-프레미스로 강제로 대신 전달합니다. Azure Redis Cache 인스턴스가 종속성과 통신할 수 없도록 온-프레미스에서 아웃바운드 트래픽이 차단된 경우 이 트래픽 흐름은 Azure Redis Cache와의 연결을 끊습니다.

해결책은 하나의(또는 그 이상) UDR(사용자 정의 경로)을 Azure Redis Cache를 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 특정 서브넷 경로는 기본 경로대신 적용됩니다.

가능하면 다음 구성을 사용하는 것이 좋습니다.

* ExpressRoute 구성은 0.0.0.0/0을 보급하고 기본적으로 모든 아웃바운드 트래픽 온-프레미스를 강제로 터널링합니다.
* Azure Redis Cache가 포함된 서브넷에 적용된 UDR은 공용 인터넷에 대한 TCP/IP 트래픽의 작업 경로에 0.0.0.0/0을 정의합니다. 예를 들어 다음 홉 유형을 'Internet'으로 설정하는 방식을 사용합니다.

이러한 단계의 결합된 효과는 서브넷 수준 UDR이 강제된 터널링에 ExpressRoute를 담당하고 Azure Redis Cache에서 아웃바운드 인터넷 액세스를 보장합니다.

ExpressRoute를 사용하여 온-프레미스 응용 프로그램에서 Azure Redis Cache 인스턴스에 연결하는 것은 성능상의 이유로 일반적인 사용 시나리오에 해당하지 않습니다(최상의 성능을 위해 Azure Redis Cache 클라이언트는 Azure Redis Cache와 동일한 하위 지역에 있어야 함).

>[!IMPORTANT] 
>UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선하도록 **구체적이어야** 합니다. 다음 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용하고 따라서 잠재적으로 보다 구체적인 주소 범위를 사용하는 경로 알림에서 실수로 재정의될 수 있습니다.

>[!WARNING]  
>**공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 잘못**한 ExpressRoute 구성에서는 Azure Redis Cache가 지원되지 않습니다. 구성된 공용 피어링이 있는 ExpressRoute 구성은 다양한 Microsoft Azure IP 주소 범위 집합에 대해 Microsoft에서 경로 보급을 받습니다. 이러한 주소 범위의 교차 보급을 개인 피어링 경로에 잘못한 경우 Azure Redis Cache 인스턴스의 서브넷에서 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라에 강제 터널링되는 잘못된 결과를 발생시킵니다. 이 네트워크 흐름은 Azure Redis Cache를 중단합니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 이어진 교차 보급 경로를 중지합니다.


사용자 정의 경로에 대한 배경 정보는 [개요](../virtual-network/virtual-networks-udr-overview.md)를 참조하세요.

ExpressRoute에 대한 자세한 내용은 [ExpressRoute 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

* [Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

