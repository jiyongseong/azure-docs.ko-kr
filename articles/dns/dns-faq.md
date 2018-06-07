---
title: Azure DNS FAQ | Microsoft Docs
description: Azure DNS에 대한 질문과 대답
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: e0eb39ced1d88d2e0b6128493304f112f9c685fa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-dns-faq"></a>Azure DNS FAQ

## <a name="about-azure-dns"></a>Azure DNS 정보

### <a name="what-is-azure-dns"></a>Azure DNS란?

Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스팅됩니다. 또한 사용 가능한 가장 가까운 DNS 서버에서 각 DNS 쿼리에 응답하도록 애니캐스트 네트워킹을 사용합니다. Azure DNS는 도메인에 대해 빠른 성능과 고가용성을 제공합니다.

Azure DNS 서비스는 Azure Resource Manager를 기반으로 합니다. 이 경우 역할 기반 액세스 제어, 감사 로그 및 리소스 잠금과 같은 리소스 관리자 기능의 이점을 누릴 수 있습니다. 도메인과 레코드는 Azure 포털, Azure PowerShell cmdlet 및 플랫폼 간 Azure CLI를 통해 관리할 수 있습니다. 자동 DNS 관리가 필요한 응용 프로그램은 REST API 및 SDK를 통해 서비스와 통합할 수 있습니다.

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 비용은 얼마입니까?

Azure DNS 요금 청구 모델은 Azure DNS에 호스트되는 DNS 영역의 수와 수신되는 DNS 쿼리 수를 기준으로 합니다. 할인은 사용량에 따라 제공됩니다.

자세한 내용은 [Azure DNS 가격 책정 페이지](https://azure.microsoft.com/pricing/details/dns/)를 참조하세요.

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS에 대한 SLA는 무엇인가요?

Azure는 유효한 DNS 요청에 대해 99.99% 이상의 시간 동안 하나 이상의 Azure DNS 이름 서버에서 응답을 제공할 것을 보장합니다.

자세한 내용은 [Azure DNS SLA 페이지](https://azure.microsoft.com/support/legal/sla/dns)를 참조하세요.

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>'DNS 영역'이란 무엇인가요? DNS 도메인과 같은 것인가요? 

도메인은 Domain Name System의 고유 이름입니다(예: 'contoso.com').


DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. 예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 여러 DNS 레코드를 포함할 수 있습니다. 이러한 레코드는 DNS 영역 'contoso.com'에서 호스트됩니다.

도메인 이름은 *이름만*으로 구성되지만 DNS 영역은 도메인 이름에 대한 DNS 레코드를 포함하는 데이터 리소스입니다. Azure DNS를 사용하면 DNS 영역을 호스트하고 Azure에서 도메인에 대한 DNS 레코드를 관리할 수 있습니다. 또한 인터넷의 DNS 쿼리에 응답하기 위해 DNS 이름 서버를 제공합니다.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS를 사용하기 위해 DNS 도메인 이름을 구입해야 하나요? 

그럴 필요는 없습니다.

Azure DNS에서 DNS 영역을 호스트하기 위해 도메인을 구입할 필요는 없습니다. 도메인 이름을 소유하지 않고도 언제든지 DNS 영역을 만들 수 있습니다. 이 영역에 대한 DNS 쿼리는 해당 영역에 할당된 Azure DNS 이름 서버로 전송되는 경우에만 확인됩니다.

DNS 영역을 전역 DNS 계층 구조에 연결하려면 도메인 이름을 구입해야 합니다. 이 연결을 통해 전 세계 어디에서든지 DNS 쿼리를 수행하여 DNS 영역을 찾고 DNS 레코드로 응답을 받을 수 있습니다.

## <a name="azure-dns-features"></a>Azure DNS 기능

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS는 DNS 기반 트래픽 라우팅 또는 끝점 장애 조치(failover)를 지원하나요?

DNS 기반 트래픽 라우팅 및 끝점 장애 조치(failover)는 Azure Traffic Manager에 의해 제공됩니다. Azure Traffic Manager는 Azure DNS와 함께 사용할 수 있는 별도 Azure 서비스입니다. 자세한 내용은 [Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md)를 참조하세요.

Azure DNS는 지정된 DNS 레코드에 대한 각 DNS 쿼리가 항상 동일한 DNS 응답을 수신하는 '고정' DNS 도메인 호스트만 지원합니다.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS는 도메인 이름 등록을 지원하나요?

번호 Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인을 구입하려면 타사 도메인 이름 등록 기관을 사용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

도메인 구입은 Azure 백로그에서 추적되는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)할 수 있습니다.

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS에서는 DNSSEC를 지원하나요?

번호 Azure DNS는 현재 DNSSEC를 지원하지 않습니다.

DNSSEC는 Azure DNS 백로그에서 추적되는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)할 수 있습니다.

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS에서는 영역 전송(AXFR/IXFR)을 지원하나요?

번호 Azure DNS는 현재 영역 전송을 지원하지 않습니다. DNS 영역은 [Azure CLI를 사용하여 Azure DNS로 가져올 수 있습니다](dns-import-export.md). 그런 후 [Azure DNS 관리 포털](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell cmdlet](dns-operations-recordsets.md) 또는 [CLI 도구](dns-operations-recordsets-cli.md)를 통해 DNS 레코드를 관리할 수 있습니다.

영역 전송은 Azure DNS 백로그에서 추적되는 기능입니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)할 수 있습니다.

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS에서는 URL 리디렉션을 지원하나요?

번호 URL 리디렉션 서비스는 실제로 DNS 서비스가 아닙니다. 따라서 DNS 수준이 아닌 HTTP 수준에서 작동합니다. URL을 번들로 묶는 일부 DNS 공급자는 서비스를 전체 제품의 일부로 리디렉션합니다. 현재 이 기능은 Azure DNS에서 지원되지 않습니다.

URL 리디렉션 기능은 Azure DNS 백로그에 추적됩니다. 피드백 사이트를 사용하여 [이 기능에 대한 지원을 등록](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)할 수 있습니다.

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Azure DNS는 TXT 레코드 집합에 대해 확장된 ASCII 인코딩(8비트) 집합을 지원하나요?

예. 최신 버전의 Azure REST API, SDK, PowerShell 및 CLI를 사용하는 경우 Azure DNS는 TXT 레코드 집합에 대해 확장된 ASCII 인코딩 집합을 지원합니다(2017-10-01보다 오래된 버전. 그렇지 않은 경우 SDK 2.1이 확장된 ASCII 집합을 지원하지 않음). 예를 들어, 사용자가 TXT 레코드의 값으로, 확장된 ASCII 문자 \128을 포함하는 문자열(예: "abcd\128efgh")을 제공할 경우 Azure DNS는 내부 표현에 이 문자의 바이트 값(즉, 128)을 사용합니다. DNS 확인 시에도 이 바이트 값이 응답에 반환됩니다. 또한 확인이 주 목적이라면 "abc"와 "\097\098\099"를 바꿔서 사용해도 됩니다. 

TXT 레코드에 대해 [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) 영역 파일 마스터 형식 이스케이프 규칙을 따릅니다. 예를 들어, 이제 '\'는 실제로 RFC에 대해 모든 항목을 이스케이프합니다. TXT 레코드 값으로 "A\B"를 지정하면 단지 "AB"로 표시되고 확인됩니다. 실제로 확인 시 TXT 레코드에 "A\B"가 포함되하려게 면 "\"를 다시 이스케이프해야 합니다. 즉, "A\\B"로 지정합니다. 

현재, 이 지원은 Azure Portal에서 만든 TXT 레코드에는 사용할 수 없습니다. 

## <a name="using-azure-dns"></a>Azure DNS 사용

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Azure DNS 및 다른 DNS 공급자를 사용하여 도메인을 공동 호스트할 수 있나요?

예. Azure DNS에서는 다른 DNS 서비스와의 도메인 공동 호스팅을 지원합니다.

이렇게 하려면 도메인의 NS 레코드(도메인에 대한 DNS 쿼리를 수신하는 공급자 제어)가 두 공급자의 이름 서버를 가리키도록 수정해야 합니다. 이러한 NS 레코드는 Azure DNS, 다른 공급자 및 부모 영역(일반적으로 도메인 이름 등록자를 통해 구성됨)의 세 위치에서 수정할 수 있습니다. DNS 위임에 대한 자세한 내용은 [DNS 도메인 위임](dns-domain-delegation.md)을 참조하세요.

또한 도메인에 대한 DNS 레코드가 두 DNS 공급자 간에 동기화되는지도 확인해야 합니다. Azure DNS는 현재 DNS 영역 전송을 지원하지 않습니다. DNS 레코드는 [Azure DNS 관리 포털](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [PowerShell cmdlet](dns-operations-recordsets.md) 또는 [CLI 도구](dns-operations-recordsets-cli.md)를 사용해서 DNS 동기화해야 합니다.

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>4개의 Azure DNS 이름 서버 모두에 도메인을 위임해야 하나요?

예. Azure DNS는 오류 격리 및 복원력 증가를 위해 각 DNS 영역에 4개의 이름 서버를 할당합니다. Azure DNS SLA를 충족하려면 4개의 이름 서버 모두에 도메인을 위임해야 합니다.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS에 대한 사용량 제한은 얼마나 되나요?

Azure DNS를 사용할 경우 다음과 같은 기본 제한이 적용됩니다.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>리소스 그룹 또는 구독 간에 Azure DNS 영역을 이동할 수 있나요?

예. 리소스 그룹 또는 구독 간에 DNS 영역을 이동할 수 있습니다.

DNS 영역을 이동할 때 DNS 쿼리는 아무런 영향도 받지 않습니다. 영역에 할당된 이름 서버는 동일하게 유지되고 DNS 쿼리는 정상 처리량으로 처리됩니다.

DNS 영역을 이동하는 방법에 대한 자세한 내용 및 지침을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 변경 내용이 적용되는 데 시간이 얼마나 걸리나요?

새 DNS 영역 및 DNS 레코드는 일반적으로 Azure DNS 이름 서버에 몇 초 안에 신속하게 적용됩니다.

기존 DNS 레코드에 대한 변경은 조금 더 오래 걸릴 수 있지만 60초 이내에 Azure DNS 이름 서버에 적용됩니다. 이 경우 Azure DNS 외부의 DNS 캐싱(DNS 클라이언트 및 DNS 재귀 확인자에 의한) 또한 DNS 변경 내용이 적용되는 데 소요되는 시간에 영향을 줄 수 있습니다. 이러한 캐시 기간은 각 레코드 집합의 TTL(Time-To-Live) 속성을 사용하여 제어할 수 있습니다.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>실수로 삭제되지 않도록 내 DNS 영역을 보호하려면 어떻게 해야 하나요?

Azure DNS는 Azure Resource Manager를 사용하여 관리되며 Azure Resource Manager가 제공하는 Access Control 기능을 활용합니다. 역할 기반 Access Control는 DNS 영역 및 레코드 집합에 대한 읽기 또는 쓰기 액세스 권한이 있는 사용자를 제어하는 데 사용할 수 있습니다. 실수로 DNS 영역 및 레코드 집합을 수정하거나 삭제하지 않도록 하기 위해 리소스 잠금을 적용할 수 있습니다.

자세한 내용은 [DNS 영역 및 레코드 보호](dns-protect-zones-recordsets.md)를 참조하세요.

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Azure DNS에서 SPF 레코드를 설정하려면 어떻게 해야 하나요?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>IPv6를 통해 Azure DNS 이름 서버가 확인하나요? 

예. Azure DNS 이름 서버는 IPv4 및 IPv6 주소를 모두 포함하는 이중 스택입니다. DNS 영역에 할당된 Azure DNS 이름 서버에 대한 IPv6 주소를 찾으려면 nslookup과 같은 도구를 사용할 수 있습니다(예: `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Azure DNS에서 IDN(국제 도메인 이름)을 설정하려면 어떻게 해야 하나요?

IDN(국제 도메인 이름)은 '[punycode](https://en.wikipedia.org/wiki/Punycode)'로 각 DNS 이름을 인코딩하여 사용할 수 있습니다. DNS 쿼리는 이러한 punycode로 인코딩된 이름을 사용하여 수행됩니다.

먼저 영역 이름 또는 레코드 집합 이름을 punycode로 변환하여 Azure DNS에서 IDN(국제 도메인 이름)을 구성할 수 있습니다. Azure DNS는 현재 punycode로/부터의 기본 제공 변환을 지원하지 않습니다.

## <a name="private-dns"></a>개인 DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 '개인' 도메인을 지원하나요?
'개인' 도메인에 대한 지원은 개인 영역 기능을 사용하여 구현됩니다.  이 기능은 현재 공개 미리 보기로 사용할 수 있습니다.  개인 영역은 인터넷 연결 Azure DNS 영역과 동일한 도구를 사용하여 관리되지만 지정된 가상 네트워크 내에서만 확인할 수 있습니다.  자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

현재, 개인 영역은 Azure Portal에서 지원되지 않습니다. 

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>개인 영역 컨텍스트에서 등록 가상 네트워크와 확인 가상 네트워크 간의 차이점은 무엇인가요? 
가상 네트워크는 DNS 개인 영역에 두 가지 방법, 즉 등록 가상 네트워크 또는 확인 가상 네트워크를 통해 연결할 수 있습니다. 두 경우 모두, 가상 네트워크의 가상 머신이 개인 영역에 있는 레코드를 성공적으로 확인할 수 있습니다. 그러나 가상 네트워크를 등록 가상 네트워크로 지정하는 경우 Azure는 가상 네트워크의 가상 머신에 대한 영역에 DNS 레코드를 자동으로 등록(동적 등록)합니다. 또한 등록 가상 네트워크의 가상 머신이 삭제되면 Azure가 연결된 개인 영역에서 해당 DNS 레코드도 자동으로 제거합니다. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 개인 영역이 여러 Azure 지역에서 작동하나요?
예. 모든 가상 네트워크가 개인 영역에 대한 확인 가상 네트워크로 지정되어 있으므로 가상 네트워크를 명시적으로 피어링하지 않아도, Azure 지역의 가상 네트워크 간 DNS 확인을 위해 개인 영역을 사용할 수 있습니다. 고객은 TCP/HTTP 트래픽이 지역 간을 흐를 수 있도록 가상 네트워크를 피어링해야 할 수 있습니다. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>개인 영역을 위해 가상 네트워크에서 인터넷에 연결되어야 하나요?
번호 개인 영역은 가상 네트워크와 연동해서 작동하므로, 고객은 가상 머신에 대한 도메인이나 가상 네트워크 내부 및 가상 네트워크 간의 기타 리소스를 관리할 수 있습니다. 이름 확인을 위해 인터넷 연결이 필요하지는 않습니다. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>확인을 위해 여러 가상 네트워크에서 동일한 개인 영역을 사용할 수 있나요? 
예. 고객은 최대 10개의 확인 가상 네트워크를 단일 개인 영역에 연결할 수 있습니다.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>다른 구독에 속하는 가상 네트워크를 개인 영역에 확인 가상 네트워크로 추가할 수 있나요? 
예. 사용자에게 가상 네트워크 뿐만 아니라 개인 DNS 영역에 대해 쓰기 작업 권한이 있기만 하면 됩니다. 쓰기 권한은 여러 RBAC 역할에 할당할 수 있습니다. 예를 들어, 클래식 네트워크 참가자 RBAC 역할에는 가상 네트워크에 대한 쓰기 권한이 있습니다. RBAC 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>고객이 가상 머신을 삭제하는 경우, 개인 영역에서 자동으로 등록된 가상 컴퓨터 DNS 레코드도 자동으로 삭제되나요?
예. 등록 가상 네트워크 내에서 가상 머신을 삭제하면 해당 네트워크가 등록 가상 네트워크이기 때문에 영역에 등록된 DNS 레코드가 자동으로 삭제됩니다. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>(등록 가상 네트워크에서) 개인 영역에 자동으로 등록된 가상 머신 레코드를 수동으로 삭제할 수 있나요? 
번호 이때 등록 가상 네트워크에서 개인 영역에 자동으로 등록된 가상 머신 DNS 레코드를 고객은 볼 수도 없고 편집할 수도 없습니다. 그러나 이러한 자동으로 등록된 DNS 레코드를 영역에서 수동으로 만든 DNS 레코드로 바꿀 수 있습니다(덮어쓰기). 이 문제에 대한 다음 질문과 대답을 참조하세요.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>등록 가상 네트워크에서 (자동으로 등록된) 기존 가상 머신과 동일한 호스트가 있는 개인 영역으로 새 DNS 레코드를 수동으로 만들려고 하면 어떻게 되나요? 
등록 가상 네트워크에 기존(자동으로 등록된) 가상 머신과 동일한 호스트 이름을 갖는 개인 영역에 새 DNS 레코드를 수동으로 만들려고 하면 자동으로 등록된 가상 머신 레코드를 새 DNS 레코드로 덮어쓸 수 있도록 합니다. 또한 이후에 해당 영역에서 수동으로 생성된 이 DNS 레코드를 삭제하려고 하면 삭제는 성공하며, 가상 머신이 여전히 존재하고 개인 IP가 연결되어 있으므로 자동 등록이 다시 발생합니다(해당 영역에 DNS 레코드가 자동으로 다시 생성됨). 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>개인 영역에서 등록 가상 네트워크의 연결을 해제하면 어떻게 되나요? 가상 네트워크의 자동으로 등록된 가상 머신 레코드가 해당 영역에서도 제거되나요?
예. 개인 영역에서 등록 가상 네트워크의 연결을 해제하면(연결된 등록 가상 네트워크를 제거하도록 DNS 영역 업데이트) Azure는 해당 영역에서 자동으로 등록된 가상 머신 레코드를 모두 제거합니다. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>개인 영역에 연결된 등록(또는 확인) 가상 네트워크를 삭제하면 어떻게 되나요? 영역에서 등록(또는 확인) 가상 네트워크로 연결된 가상 네트워크를 가상 네트워크의 연결을 해제하기 위해 개인 영역을 수동으로 업데이트해야 하나요?
예. 먼저 개인 영역에서 연결을 해제하지 않은 상태로 등록(또는 확인) 가상 네트워크를 삭제하는 경우, Azure에서 삭제 작업이 성공적으로 수행되지만, 가상 네트워크는 개인 영역에서 자동으로 연결 해제됩니다. 수동으로 개인 영역에서 가상 네트워크 연결을 해제해야 합니다. 이러한 이유로, 연결을 삭제하기 전에 먼저 개인 영역에서 가상 네트워크의 연결을 해제하는 것이 좋습니다.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>기본 FQDN(internal.cloudapp.net)을 사용하는 DNS 확인은 개인 영역(예: contoso.local)이 가상 네트워크에 연결되어 있어도 제대로 작동하나요? 
예. 개인 영역 기능은 Azure에서 제공한 internal.cloudapp.net 영역을 사용하여 기본 DNS 확인을 대체하지 않으며, 추가 기능 또는 향상된 기능으로 제공됩니다. 두 경우 모두(Azure에서 제공한 internal.cloudapp.net을 신뢰하는지 또는 사용자 고유의 개인 영역을 신뢰하는지 관계없음) 확인하려는 영역의 FQDN을 사용하는 것이 좋습니다. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>연결된 가상 네트워크 내의 가상 머신에 사용되는 DNS 접미사가 개인 영역의 DNS 접두사로 변경되나요? 
번호 이때, 연결된 가상 네트워크의 가상 머신에 사용된 DNS 접미사는 기본 Azure 제공 접미사("*. internal.cloudapp.net")로 유지됩니다. 그러나 가상 머신의 이 DNS 접미사를 개인 영역의 DNS 접미사로 수동으로 변경할 수 있습니다. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>이 미리 보기 기간 동안 개인 영역에 대한 제한 사항이 있나요?
예. 공개 미리 보기 중에 다음과 같은 제한 사항이 적용됩니다.
* 개인 영역당 1개의 등록 가상 네트워크
* 개인 영역당 최대 10개의 확인 가상 네트워크
* 지정된 가상 네트워크가 1개의 개인 영역에만 등록 가상 네트워크로 연결될 수 있습니다.
* 지정된 가상 네트워크가 최대 10개의 개인 영역에 확인 가상 네트워크로 연결될 수 있습니다.
* 등록 가상 네트워크가 지정된 경우, 개인 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Powershell/CLI/API에서 보거나 검색할 수 없지만 VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.
* 역방향 DNS는 등록 가상 네트워크의 개인 IP 공간에 대해서만 작동합니다.
* 개인 영역에 등록되지 않은 개인 IP(예: 확인 가상 네트워크로 개인 영역에 연결된 가상 네트워크에 있는 가상 머신의 개인 IP)에 대한 역방향 DNS는 DNS 접미사로 “internal.cloudapp.net”를 반환하지만 이 접미사는 확인할 수 없습니다.   
* 가상 네트워크는 처음에(예: 처음으로) 등록 또는 확인 가상 네트워크로 개인 영역에 연결될 때 비어 있어야 합니다(예: NIC가 연결된 가상 머신이 없음). 그러나 가상 네트워크가 나중에 등록 또는 확인 가상 네트워크로 다른 개인 영역에 연결할 때는 비어 있지 않을 수 있습니다. 
* 이때 Azure와 온-프레미스 네트워크 간에 확인이 가능하도록 조건부 전달은 지원되지 않습니다. 고객이 다른 메커니즘을 통해 이 시나리오를 실현할 수 있는 방법에 대한 설명서는 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>개인 영역의 영역 또는 레코드에 대해 할당량 또는 제한이 있나요?
개인 영역에 대해 구독당 허용되는 영역 수 또는 영역별 레코드 집합 수에 대한 별도 제한은 없습니다. [여기](../azure-subscription-service-limits.md#dns-limits)에 설명된 대로 공용 및 개인 영역은 전체 DNS 제한을 초과하지 않아야 합니다.

### <a name="is-there-portal-support-for-private-zones"></a>개인 영역에 대한 포털 지원이 있나요?
이미 비포털 메커니즘(API/PowerShell/CLI/SDK)을 통해 생성된 개인 영역을 Azure Portal에서는 볼 수 있지만, 고객은 새 개인 영역을 만들거나 가상 네트워크와의 연결을 관리할 수 없습니다. 또한 등록 가상 네트워크로 연결된 가상 네트워크의 경우 자동으로 등록된 VM 레코드를 포털에서 볼 수 없습니다. 

## <a name="next-steps"></a>다음 단계

[Azure DNS에 대해 자세히 알아보기](dns-overview.md)
<br>
[개인 도메인에 Azure DNS 사용에 대해 자세히 알아보기](private-dns-overview.md)
<br>
[DNS 영역 및 레코드에 대해 자세히 알아보기](dns-zones-records.md)
<br>
[Azure DNS 시작](dns-getstarted-portal.md)

