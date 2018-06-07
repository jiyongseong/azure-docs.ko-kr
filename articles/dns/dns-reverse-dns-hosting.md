---
title: Azure DNS에서 역방향 DNS 조회 영역 호스트 | Microsoft Docs
description: Azure DNS를 사용하여 IP 범위에 대한 역방향 DNS 조회 영역을 호스트하는 방법을 알아봅니다.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: c5d53838ffb77ff8d33fee9d10aea4a74f3c523c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Azure DNS에서 역방향 DNS 조회 영역 호스트

이 문서에서는 Azure DNS에서 할당된 IP 범위에 대한 역방향 DNS 조회 영역을 호스트하는 방법을 설명합니다. 역방향 조회 영역으로 표시되는 IP 범위는 일반적으로 ISP에 의해 조직에 할당되어야 합니다.

Azure 서비스에 할당된 Azure 소유 IP 주소에 역방향 DNS를 구성하려면 [Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](dns-reverse-dns-for-azure-services.md)을 참조하세요.

이 문서를 읽기 전에 [Azure의 역방향 DNS 및 지원 개요](dns-reverse-dns-overview.md)를 이해하고 있어야 합니다.

이 문서에서는 Azure Portal, Azure PowerShell, Azure CLI 1.0 또는 Azure CLI 2.0을 사용하여 첫 번째 역방향 조회 DNS 영역 및 레코드를 만드는 단계를 안내합니다.

## <a name="create-a-reverse-lookup-dns-zone"></a>역방향 조회 DNS 영역 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **허브** 메뉴에서 **새로 만들기** > **네트워킹**을 선택한 다음, **DNS 영역**을 선택합니다.

   !["DNS 영역" 선택](./media/dns-reverse-dns-hosting/figure1.png)

1. **DNS 영역 만들기** 창에서 DNS 영역의 이름을 지정합니다. 영역 이름은 IPv4 및 IPv6 접두사에 대해 다르게 생성됩니다. [IPv4](#ipv4) 또는 [IPv6](#ipv6)에 대한 지침을 사용하여 영역 이름을 지정합니다. 완료되면 **만들기**를 선택하여 영역을 만듭니다.

### <a name="ipv4"></a>IPv4

IPv4 역방향 조회 영역의 이름은 나타내는 IP 범위를 기반으로 합니다. `<IPv4 network prefix in reverse order>.in-addr.arpa` 형식이어야 합니다. 예제는 [Azure의 역방향 DNS 및 지원 개요](dns-reverse-dns-overview.md#ipv4)를 참조하세요.

> [!NOTE]
> Azure DNS에서 클래스 없는 역방향 DNS 조회 영역을 만들 경우 영역 이름에 슬래시(`/`) 대신 하이픈(`-`)을 사용해야 합니다.
>
> 예를 들어 IP 범위 192.0.2.128/26에 대해 `128/26.2.0.192.in-addr.arpa` 대신 `128-26.2.0.192.in-addr.arpa`를 영역 이름으로 사용해야 합니다.
>
> DNS 표준은 두 가지 방법을 모두 지원하지만 Azure DNS는 슬래시(`/`) 문자를 포함하는 DNS 영역 이름을 지원하지 않습니다.

다음 예제에서는 Azure Portal을 통해 Azure DNS에 `2.0.192.in-addr.arpa`라는 클래스 C 역방향 DNS 영역을 만드는 방법을 보여줍니다.

 ![입력란이 채워진 "DNS 영역 만들기" 창](./media/dns-reverse-dns-hosting/figure2.png)

**리소스 그룹 위치**는 리소스 그룹의 위치를 정의합니다. DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

다음 예제에서는 Azure PowerShell 및 Azure CLI를 사용하여 이 작업을 완료하는 방법을 보여 줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

IPv6 역방향 조회 영역의 이름은 `<IPv6 network prefix in reverse order>.ip6.arpa` 형식이어야 합니다.  예제는 [Azure의 역방향 DNS 및 지원 개요](dns-reverse-dns-overview.md#ipv6)를 참조하세요.


다음 예제에서는 Azure Portal을 통해 Azure DNS에 `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa`라는 IPv6 역방향 DNS 조회 영역을 만드는 방법을 보여 줍니다.

 ![입력란이 채워진 "DNS 영역 만들기" 창](./media/dns-reverse-dns-hosting/figure3.png)

**리소스 그룹 위치**는 리소스 그룹의 위치를 정의합니다. DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

다음 예제에서는 Azure PowerShell 및 Azure CLI를 사용하여 이 작업을 완료하는 방법을 보여 줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>역방향 DNS 조회 영역 위임

DNS 역방향 조회 영역을 만들었으므로 해당 영역이 부모 영역에서 위임되도록 해야 합니다. DNS 위임을 사용하면 DNS 이름 확인 프로세스를 통해 역방향 DNS 조회 영역을 호스트하는 이름 서버를 찾을 수 있습니다. 그러면 해당 이름 서버가 주소 범위에 있는 IP 주소에 대한 DNS 역방향 쿼리에 응답할 수 있습니다.

정방향 조회 영역의 경우 DNS 영역을 위임하는 프로세스는 [Azure DNS에 도메인 위임](dns-delegate-domain-azure-dns.md)에 설명되어 있습니다. 역방향 조회 영역에 대한 위임도 동일한 방식으로 작동합니다. 유일한 차이점은 도메인 이름 등록 기관이 아닌 IP 범위를 제공하는 ISP를 통해 이름 서버를 구성해야 한다는 것입니다.

## <a name="create-a-dns-ptr-record"></a>DNS PTR 레코드 만들기

### <a name="ipv4"></a>IPv4

다음 예제에서는 Azure Portal의 역방향 DNS 영역에 PTR 레코드를 만드는 과정을 안내합니다. 다른 레코드 유형을 알아보고 기존 레코드를 수정하려면 [Azure Portal을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요.

1. **DNS 영역** 창의 위쪽에서 **+ 레코드 집합**을 클릭하여 **레코드 집합 추가** 창을 엽니다.

   ![레코드 집합 만들기 단추](./media/dns-reverse-dns-hosting/figure4.png)

1. PTR 레코드에 대한 레코드 집합 이름은 반대 순서로 IPv4 주소의 나머지 부분이어야 합니다. 

   이 예제에서 처음 3개 8진수는 이미 영역 이름(.2.0.192)의 일부로 채워져 있습니다. 따라서 마지막 8진수만 **이름** 상자에 제공됩니다. 예를 들어 IP 주소가 192.0.2.15인 리소스에 대해 레코드 집합 이름을 **15**로 지정할 수 있습니다.  
1. **형식**에는 **PTR**을 선택합니다.  
1. **도메인 이름**에는 IP를 사용하는 리소스의 FQDN(정규화된 도메인 이름)을 입력합니다.
1. 창의 맨 아래에서 **확인**을 선택하여 DNS 레코드를 만듭니다.

 ![입력란이 채워진 "레코드 집합 추가" 창](./media/dns-reverse-dns-hosting/figure5.png)

다음 예제에서는 PowerShell이나 Azure CLI를 사용하여 이 작업을 완료하는 방법을 보여줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

다음 예제에서는 새로운 PTR 레코드를 만드는 과정을 안내합니다. 다른 레코드 유형을 알아보고 기존 레코드를 수정하려면 [Azure Portal을 사용하여 DNS 레코드 및 레코드 집합 관리](dns-operations-recordsets-portal.md)를 참조하세요.

1. **DNS 영역** 창의 위쪽에서 **+ 레코드 집합**을 클릭하여 **레코드 집합 추가** 창을 엽니다.

   ![레코드 집합 만들기 단추](./media/dns-reverse-dns-hosting/figure6.png)

2. PTR 레코드에 대한 레코드 집합 이름은 반대 순서로 IPv6 주소의 나머지 부분이어야 합니다. 제로 압축은 포함하면 안 됩니다. 

   이 예제에서 IPv6의 처음 64비트는 영역 이름의 일부로 이미 채워져 있습니다(0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). 따라서 마지막 64비트만 **이름** 상자에 제공됩니다. IP 주소의 마지막 64비트는 각 16진수 사이에 마침표를 구분 기호로 사용하여 역순으로 입력됩니다. 예를 들어 IP 주소가 2001:0db8:abdc:0000:f524:10bc:1af9:405e인 리소스의 레코드 집합 이름을 **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**라고 지정할 수 있습니다.  
3. **형식**에는 **PTR**을 선택합니다.  
4. **도메인 이름**에는 IP를 사용하는 리소스의 FQDN을 입력합니다.
5. 창의 맨 아래에서 **확인**을 선택하여 DNS 레코드를 만듭니다.

![입력란이 채워진 "레코드 집합 추가" 창](./media/dns-reverse-dns-hosting/figure7.png)

다음 예제에서는 PowerShell이나 Azure CLI를 사용하여 이 작업을 완료하는 방법을 보여줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>레코드 보기

만든 레코드를 보려면 Azure Portal에서 DNS 영역으로 이동합니다. **DNS 영역** 창의 아래쪽에서 DNS 영역에 대한 레코드를 볼 수 있습니다. 기본 NS 및 SOA 레코드와 새로 만든 레코드를 볼 수 있습니다. NS 및 SOA 레코드는 모든 영역에 생성됩니다. 

### <a name="ipv4"></a>IPv4

**DNS 영역** 창에는 IPv4 PTR 레코드가 표시됩니다.

![IPv4 레코드가 있는 "DNS 영역" 창](./media/dns-reverse-dns-hosting/figure8.png)

다음 예제에서는 PowerShell 또는 Azure CLI를 사용하여 PTR 레코드를 보는 방법을 보여줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**DNS 영역** 창에는 IPv6 PTR 레코드가 표시됩니다.

![IPv6 레코드가 있는 "DNS 영역" 창](./media/dns-reverse-dns-hosting/figure9.png)

다음 예제에서는 PowerShell 또는 Azure CLI를 사용하여 레코드를 보는 방법을 보여줍니다.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>FAQ

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Azure DNS에서 내 ISP 할당 IP 블록에 대한 역방향 DNS 조회 영역을 호스트할 수 있나요?

예. Azure DNS에서 고유한 IP 범위에 대해 역방향 조회(ARPA) 영역을 호스팅하는 것은 완전히 지원됩니다.

이 문서에 설명된 대로 Azure DNS에서 역방향 조회 영역을 만든 다음, ISP와 협력하여 [영역을 위임](dns-domain-delegation.md)합니다. 그런 다음 다른 레코드 유형과 동일한 방식으로 각 역방향 조회를 위해 PTR 레코드를 관리할 수 있습니다.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>내 역방향 DNS 조회 영역 호스트 비용은 얼마나 되나요?

Azure DNS에서 ISP 할당 IP 블록에 대해 역방향 DNS 조회 영역을 호스트하는 비용은 [표준 Azure DNS 요율](https://azure.microsoft.com/pricing/details/dns/)로 청구됩니다.

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Azure DNS에서 IPv4 및 IPv6 주소 모두에 대해 역방향 DNS 조회 영역을 호스트할 수 있나요?

예. 이 문서에서는 Azure DNS에서 IPv4 및 IPv6 역방향 DNS 조회 영역을 둘 다 만드는 방법을 설명합니다.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>기존의 역방향 DNS 조회 영역을 가져올 수 있나요?

예. Azure CLI를 사용하여 기존 DNS 영역을 Azure DNS로 가져올 수 있습니다. 이 방법은 정방향 조회 영역 및 역방향 조회 영역에서 모두 사용할 수 있습니다.

자세한 내용은 [Azure CLI를 사용하여 DNS 영역 파일 가져오기 및 내보내기](dns-import-export.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

역방향 DNS에 대한 자세한 내용은 [Wikipedia에서 역방향 DNS 조회](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)를 참조하세요.
<br>
[Azure 서비스에 대한 역방향 DNS 레코드를 관리](dns-reverse-dns-for-azure-services.md)하는 방법을 알아봅니다.
