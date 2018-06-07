---
title: Azure 네트워크 보안 개요 | Microsoft Docs
description: Azure 리소스 간에 네트워크 트래픽 흐름을 제어하기 위한 보안 옵션에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 618ed0f72886fff1c2de11e2fd856f6cc065a7b3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="network-security"></a>네트워크 보안

네트워크 보안 그룹을 사용하여 가상 네트워크에서 리소스에 대한 네트워크 트래픽을 제한할 수 있습니다. 네트워크 보안 그룹에는 원본 또는 대상 IP 주소, 포트 및 프로토콜에 따라 인바운드 또는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. 

## <a name="network-security-groups"></a>네트워크 보안 그룹

각 네트워크 인터페이스에는 하나의 연결된 네트워크 보안 그룹이 있거나 없을 수 있습니다. 각 네트워크 인터페이스는 [가상 네트워크](virtual-networks-overview.md) 서브넷에 위치합니다. 서브넷에는 하나의 연결된 네트워크 보안 그룹이 있거나 없을 수 있습니다. 

보안 규칙이 서브넷에 적용되는 경우 서브넷에 있는 모든 리소스에 적용됩니다. 네트워크 인터페이스 외에도 HDInsight, Virtual Machine Scale Sets 및 서브넷에 배포된 응용 프로그램 서비스 환경과 같이 다른 Azure 서비스의 인스턴스가 있을 수 있습니다.

네트워크 보안 그룹이 네트워크 인터페이스 및 해당 네트워크 인터페이스가 위치한 서브넷에 연결된 경우 해당 네트워크 보안 그룹을 적용하는 방법은 다음과 같습니다.

- **인바운드 트래픽**: 네트워크 인터페이스가 위치한 서브넷에 연결된 네트워크 보안 그룹을 먼저 평가합니다. 서브넷에 연결된 네트워크 보안 그룹을 통해 허용된 모든 트래픽은 네트워크 인터페이스에 연결된 네트워크 보안 그룹에 의해 평가됩니다. 예를 들어 인터넷의 포트 80을 통해 가상 머신에 대한 인바운드 액세스가 필요할 수 있습니다. 네트워크 인터페이스 및 해당 네트워크 인터페이스가 위치한 서브넷에 네트워크 보안 그룹을 연결하는 경우 서브넷에 연결된 네트워크 보안 그룹 및 네트워크 인터페이스는 포트 80을 허용해야 합니다. 서브넷 또는 해당 서브넷이 위치한 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 통한 포트 80만을 허용하는 경우 기본 보안 규칙으로 인해 통신에 실패합니다. 자세한 내용은 [기본 보안 규칙](#default-security-rules)을 참조하세요. 예를 들어 서브넷 또는 네트워크 인터페이스에 대한 네트워크 보안 그룹 및 포트 80 인바운드 트래픽을 허용하는 규칙을 포함하는 네트워크 보안 그룹에만 적용한 경우 통신에 성공합니다. 
- **아웃바운드 트래픽**: 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 먼저 평가합니다. 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 통해 허용된 모든 트래픽은 서브넷에 연결된 네트워크 보안 그룹에 의해 평가됩니다.

네트워크 인터페이스와 서브넷에 네트워크 보안 그룹이 적용되는 시점을 항상 알 수는 없습니다. 네트워크 인터페이스의 [유효 보안 규칙](virtual-network-network-interface.md#view-effective-security-rules)을 확인하여 네트워크 인터페이스에 적용되는 집계 규칙을 쉽게 볼 수 있습니다. Azure Network Watcher에서 [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용하여 통신이 네트워크 인터페이스 간에 허용되는지 여부를 결정할 수도 있습니다. 이 도구에서는 통신이 허용되는지 여부 및 어떤 네트워크 보안 규칙이 트래픽을 허용하거나 거부하는지를 알려줍니다.
 
> [!NOTE]
> 네트워크 보안 그룹이 Resource Manager 배포 모델에서 네트워크 인터페이스가 아닌 배포 클래식 배포 모델에 배포된 서브넷 또는 가상 머신과 클라우드 서비스에 연결됩니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

동일한 네트워크 보안 그룹은 선택한 만큼의 개별 네트워크 인터페이스 및 서브넷에 적용될 수 있습니다.

## <a name="security-rules"></a>보안 규칙

네트워크 보안 그룹은 Azure 구독 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 내에서 필요한 만큼 0개 또는 많은 규칙을 포함합니다. 각 규칙은 다음 속성을 지정합니다.

|자산  |설명  |
|---------|---------|
|Name|네트워크 보안 그룹 내에서 고유한 이름입니다.|
|우선 순위 | 100~4096 사이의 숫자입니다. 낮은 번호의 우선 순위가 더 높기 때문에 규칙은 낮은 번호가 높은 번호보다 먼저 처리되는 우선 순위 순서로 처리됩니다. 트래픽이 규칙과 일치하면 처리가 중지됩니다. 따라서 우선 순위가 높은 규칙과 동일한 특성을 가진 우선 순위가 낮은 규칙(높은 번호)은 처리되지 않습니다.|
|원본 또는 대상| 모두 또는 개별 IP 주소, CIDR 블록(예: 예제 10.0.0.0/24), 서비스 태그 또는 응용 프로그램 보안 그룹입니다. [태그 서비스](#service-tags) 및 [응용 프로그램 보안 그룹](#application-security-groups)에 대해 자세히 알아봅니다. 범위, 서비스 태그 또는 응용 프로그램 보안 그룹을 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 규칙에서 여러 개별 IP 주소와 범위를 지정하는 기능(여러 서비스 태그 또는 응용 프로그램 그룹을 지정할 수 없음)은 보강된 보안 규칙이라고 합니다. [보강된 보안 규칙](#augmented-security-rules)에 관해 자세히 알아보세요. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 여러 개의 IP 주소 및 IP 주소 범위를 지정할 수 없습니다.|
|프로토콜     | TCP, UDP 또는 TCP, UDP 및 ICMP를 포함하는 모두입니다. 단독으로 ICMP를 지정할 수 없으므로 ICMP가 필요한 경우 모두를 사용합니다. |
|방향| 규칙이 인바운드 또는 아웃바운드 트래픽에 적용되는지 여부입니다.|
|포트 범위     |개별 포트나 포트의 범위를 지정할 수 있습니다. 예를 들어 80 또는 10000-10005과 같이 지정할 수 있습니다. 범위를 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 동일한 보안 규칙에 여러 개의 포트 또는 포트 범위를 지정할 수 없습니다.   |
|조치     | 허용 또는 거부        |

NSG 보안 규칙은 5 튜플 정보(원본, 원본 포트, 대상, 대상 포트 및 프로토콜)를 사용하는 우선 순위를 통해 평가되어 트래픽을 허용하거나 거부합니다. 기존 연결에 대한 흐름 레코드가 생성되고, 흐름 레코드의 연결 상태에 따라 통신이 허용되거나 거부되며, 이를 통해 NSG의 상태를 저장할 수 있습니다. 예를 들어 포트 80을 통해 모든 주소에 대한 아웃바운드 보안 규칙을 지정하는 경우 아웃바운드 트래픽에 대한 응답에 인바운드 보안 규칙을 지정하지 않아도 됩니다. 통신이 외부에서 시작된 경우 인바운드 보안 규칙을 지정하기만 하면 됩니다. 반대의 경우도 마찬가지입니다. 포트를 통해 인바운드 트래픽이 허용되는 경우 포트를 통해 트래픽에 응답하도록 아웃바운드 보안 규칙을 지정하지 않아도 됩니다.
흐름을 사용하는 보안 규칙을 제거해도 기존 연결이 중단되지 않을 수 있습니다. 연결이 중단되고 몇 분 이상 어느 방향으로도 트래픽이 흐르지 않으면 트래픽 흐름이 중단됩니다.

보안 규칙을 만들 경우 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="augmented-security-rules"></a>보강된 보안 규칙

보강된 규칙은 가상 네트워크에 대한 보안 정의를 간소화하여 더 적은 규칙으로 크고 복잡한 네트워크 보안 정책을 정의할 수 있도록 합니다. 여러 개의 포트, 여러 개의 명시적 IP 주소, 서비스 태그 및 응용 프로그램 보안 그룹을 이해하기 쉬운 단일 보안 규칙으로 결합할 수 있습니다. 규칙의 원본, 대상 및 포트 필드에서 보강된 규칙을 사용합니다. 규칙을 만들 때 여러 명시적 IP 주소, CIDR 범위 및 포트를 지정할 수 있습니다. 보안 규칙 정의를 간단히 유지 관리하려면 보강된 보안 규칙을 서비스 태그 또는 응용 프로그램 보안 그룹과 결합합니다. 

보강된 보안 규칙을 만들 경우 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="default-security-rules"></a>기본 보안 규칙

네트워크 보안 그룹이 서브넷 또는 네트워크 인터페이스에 연결되지 않으면 모든 트래픽은 허용된 인바운드 대상이나 아웃바운드 원본, 서브넷 또는 네트워크 인터페이스입니다. 네트워크 보안 그룹이 생성되는 즉시 Azure에서는 네트워크 보안 그룹 내에서 다음과 같은 기본 규칙을 만듭니다.

### <a name="inbound"></a>인바운드

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|모두|허용|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|모두|허용|

#### <a name="denyallinbound"></a>DenyAllInbound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|모두|거부|

### <a name="outbound"></a>아웃바운드

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 모두 | 허용 |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | 인터넷 | 0-65535 | 모두 | 허용 |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 모두 | 거부 |

**원본** 및 **대상** 열에서 *VirtualNetwork*, *AzureLoadBalancer* 및 *인터넷*은 IP 주소가 아닌 [서비스 태그](#tags)입니다. 프로토콜 열에서 **모두**는 TCP, UDP 및 ICMP를 포함합니다. 규칙을 만들 때 TCP, UDP 또는 모두를 지정할 수 있지만 ICMP만을 지정할 수는 없습니다. 따라서 규칙에 ICMP가 필요한 경우 프로토콜에서 *모두*를 선택해야 합니다. **소스** 및 **대상** 열에서 *0.0.0.0/0*은 모든 주소를 나타냅니다.
 
기본 규칙을 제거할 수 없지만 더 높은 우선 순위의 규칙을 만들어서 재정의할 수 있습니다.

## <a name="service-tags"></a>서비스 태그

 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 다음 서비스 태그는 보안 규칙 정의에서 사용할 수 있습니다. 해당 이름은 [Azure 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 따라 조금씩 다릅니다.

* **VirtualNetwork**(Resource Manager)(클래식의 경우 **VIRTUAL_NETWORK**): 이 태그에는 가상 네트워크 주소 공간(가상 네트워크에 대해 정의된 모든 CIDR 범위), 연결된 모든 온-프레미스 주소 공간 및 [피어링](virtual-network-peering-overview.md)된 가상 네트워크 또는 [가상 네트워크 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 연결된 가상 네트워크가 포함됩니다.
* **AZURE_LOADBALANCER**(Resource Manager) (클래식의 경우 **AzureLoadBalancer**): 이 태그는 Azure의 인프라 부하 분산 장치를 나타내며, 태그는 Azure의 상태 검색이 시작되는 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653)로 변환됩니다. Azure Load Balancer를 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다.
* **Internet**(Resource Manager) (클래식의 경우 **INTERNET**): 이 태그는 가상 네트워크 외부에 있고 공용 인터넷에서 연결할 수 있는 IP 주소 공간을 나타냅니다. 주소 범위에는 [Azure에서 소유하는 공용 IP 주소 공간](https://www.microsoft.com/download/details.aspx?id=41653)이 포함됩니다.
* **AzureTrafficManager**(리소스 관리자에만 해당): 이 태그는 Azure Traffic Manager 프로브 IP의 IP 주소 공간을 나타냅니다. Traffic Manager 프로브 IP에 대한 자세한 내용은 [Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)에서 찾을 수 있습니다.
* **Storage**(Resource Manager에만 해당): 이 태그는 Azure Storage 서비스의 IP 주소 공간을 나타냅니다. 값의 *저장소*를 지정하는 경우 트래픽은 저장소에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 저장소에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure Storage에 액세스를 허용하려는 경우 *Storage.EastUS*를 서비스 태그로 지정할 수 있습니다. 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. 이 태그를 통해 표시되는 모든 주소 접두사는 **인터넷** 태그를 통해서도 표시됩니다.
* **Sql**(Resource Manager에만 해당): 이 태그는 Azure SQL Database 및 Azure SQL Data Warehouse 서비스의 주소 접두사를 나타냅니다. 값의 *Sql*을 지정하는 경우 트래픽은 Sql에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 Sql에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure SQL Database에 액세스를 허용하려는 경우 *Sql.EastUS*를 서비스 태그로 지정할 수 있습니다. 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 SQL Database 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. 이 태그를 통해 표시되는 모든 주소 접두사는 **인터넷** 태그를 통해서도 표시됩니다.
* **AzureCosmosDB**(Resource Manager에만 해당): 이 태그는 Azure Cosmos Database 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureCosmosDB*를 지정하는 경우 트래픽은 AzureCosmosDB에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureCosmosDB 액세스를 허용하려면 AzureCosmosDB.[region name] 형식으로 지역을 지정하면 됩니다.
* **AzureKeyVault**(Resource Manager에만 해당): 이 태그는 Azure KeyVault 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureKeyVault*를 지정하는 경우 트래픽은 AzureKeyVault에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureKeyVault 액세스를 허용하려면 AzureKeyVault.[region name] 형식으로 지역을 지정하면 됩니다.

> [!NOTE]
> Azure 서비스의 서비스 태그는 사용되는 특정 클라우드의 주소 접두사를 나타냅니다. 지역 서비스 태그는 국가 클라우드에서는 지원되지 않고 전역 형식(예: Storage, Sql)에서만 지원됩니다.

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 경로를 추가합니다. 경로의 주소 접두사는 해당하는 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.

## <a name="application-security-groups"></a>응용 프로그램 보안 그룹

응용 프로그램 보안 그룹을 사용하면 네트워크 보안을 응용 프로그램 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다. 이 기능을 사용하면 명시적 IP 주소를 수동으로 유지 관리하지 않고 대규모 보안 정책을 다시 사용할 수 있습니다. 플랫폼은 명시적 IP 주소 및 여러 규칙 집합의 복잡성을 처리하여 비즈니스 논리에 집중할 수 있도록 합니다.

응용 프로그램 보안 그룹을 보안 규칙의 원본 및 대상으로 지정할 수 있습니다. 보안 정책이 정의되면 가상 머신을 만들고 응용 프로그램 보안 그룹에 가상 머신의 네트워크 인터페이스를 할당할 수 있습니다. 가상 컴퓨터 내에서 각 네트워크 인터페이스의 응용 프로그램 보안 그룹 멤버 자격에 따라 정책이 적용됩니다. 다음 예제에서는 구독에서 모든 웹 서버에 응용 프로그램 보안 그룹을 사용하는 방법을 설명합니다.

1. *WebServers*라는 응용 프로그램 보안 그룹을 만듭니다.
2. *MyNSG*라는 네트워크 보안 그룹을 만듭니다.
3. 네트워크 보안 그룹에 인바운드 보안 규칙을 만들어 원본 주소의 *인터넷* 서비스 태그 및 대상 주소로 *WebServers* 응용 프로그램 보안 그룹을 지정하고 포트 80 및 443을 허용합니다.
4. 웹 서버 응용 프로그램을 실행하는 가상 머신을 배포합니다. 가상 머신의 네트워크 인터페이스를 *WebServers* 응용 프로그램 보안 그룹의 멤버로 지정합니다. 그런 다음 가상 머신에 대해 포트 80 및 443을 허용합니다. *WebServers* 응용 프로그램 보안 그룹의 멤버로 지정할 때 만든 후속 웹 서버에 대해서도 포트를 사용할 수 있습니다. 

다른 규칙을 만드는 경우 대상으로 다른 응용 프로그램 보안 그룹을 지정하기 때문에 해당 규칙이 이전 예제에서 웹 서버에 적용되지 않습니다. 응용 프로그램 보안 그룹을 지정하는 규칙은 응용 프로그램 보안 그룹의 멤버인 네트워크 인터페이스에만 적용됩니다. 보강된 보안 규칙 및 서비스 태그와 결합된 응용 프로그램 보안 그룹을 통해 최소 수의 네트워크 보안 그룹을 만들어 구독 내에서 네트워크 보안을 관리할 수 있습니다.
 
응용 프로그램 보안 그룹을 만들고 보안 규칙에서 지정하는 경우 제한에 대해 자세히 알아보려면 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

응용 프로그램 보안 그룹에는 다음과 같은 제약 사항이 있습니다.

-   응용 프로그램 보안 그룹에 할당된 모든 네트워크 인터페이스는 응용 프로그램 보안 그룹에 할당된 첫 번째 네트워크 인터페이스가 있는 가상 네트워크와 동일한 가상 네트워크에 있어야 합니다. 예를 들어 *ASG1*이라는 응용 프로그램 보안 그룹에 할당된 첫 번째 네트워크 인터페이스가 *VNet1*이라는 가상 네트워크에 있는 경우 *ASG1*에 할당되는 모든 후속 네트워크 인터페이스는 *VNet1*에 있어야 합니다. 서로 다른 가상 네트워크의 네트워크 인터페이스를 동일한 응용 프로그램 보안 그룹에 추가할 수 없습니다.
- 응용 프로그램 보안 그룹을 보안 규칙의 원본 및 대상으로 지정하는 경우 두 응용 프로그램 보안 그룹에 있는 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다. 예를 들어, ASG1이 VNet1에서 네트워크 인터페이스를 포함하고 ASG2가 VNet2에서 네트워크 인터페이스를 포함하는 경우 규칙에서 ASG1을 원본으로 ASG2를 대상으로 할당할 수 없으며 모든 네트워크 인터페이스는 VNet1에 있어야 합니다.

## <a name="azure-platform-considerations"></a>Azure 플랫폼 고려 사항

- **호스트 노드의 가상 IP:** DHCP, DNS 및 상태 모니터링과 같은 기본 인프라 서비스는 가상화된 호스트 IP 주소 168.63.129.16 및 169.254.169.254를 통해 제공됩니다. 이러한 공용 IP 주소는 Microsoft에 속하며, 이 용도로 모든 지역에서 유일하게 사용되는 가상화된 IP 주소입니다. 해당 주소는 가상 머신을 호스트하는 서버 컴퓨터(호스트 노드)의 실제 IP 주소에 매핑합니다. 호스트 노드는 DHCP 릴레이, DNS 재귀 확인자, 부하 분산 장치 상태 검색 및 컴퓨터 상태 검색에 대한 검색 소스 등의 역할을 합니다. 이러한 IP 주소의 통신은 공격이 아닙니다. 이러한 IP 주소 간에 트래픽을 차단하는 경우 가상 머신이 제대로 작동하지 않을 수 있습니다.
- **라이선싱(키 관리 서비스):** 가상 머신에서 실행되는 Windows 이미지는 사용이 허가되어 있어야 합니다. 사용 허가를 위해 라이선스 요청이 이러한 쿼리를 처리하는 키 관리 서비스 호스트 서버로 전송됩니다. 요청은 1688 포트를 통해 아웃바운드로 수행됩니다. [기본 경로 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) 구성을 사용한 배포에 대해 이 플랫폼 규칙은 사용하지 않도록 설정됩니다.
- **부하가 분산된 풀의 가상 머신**: 적용되는 원본 포트와 주소 범위는 부하 분산 장치가 아닌 원래 컴퓨터에서 가져옵니다. 대상 포트와 주소 범위는 부하 분산 장치가 아닌 대상 컴퓨터에서 가져옵니다.
- **Azure 서비스 인스턴스**: HDInsight, 응용 프로그램 서비스 환경 및 Virtual Machine Scale Sets와 같은 몇 가지 Azure 서비스의 인스턴스는 가상 네트워크 서브넷에 배포됩니다. 가상 네트워크에 배포할 수 있는 서비스의 전체 목록은 [Azure 서비스에 대한 가상 네트워크](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)를 참조하세요. 리소스를 배포한 서브넷에 네트워크 보안 그룹을 적용하기 전에 각 서비스에 대한 포트 요구 사항을 잘 이해하도록 합니다. 서비스에 필요한 포트를 거부하는 경우 서비스가 제대로 작동하지 않습니다.
- **아웃바운드 전자 메일 보내기**: 인증된 SMTP 릴레이 서비스(일반적으로 587 TCP 포트를 통해 연결되지만 종종 다른 방법도 사용)를 활용하여 Azure Virtual Machines에서 전자 메일을 보내는 것이 좋습니다. SMTP 릴레이 서비스는 타사 전자 메일 공급자에서 메시지를 거부할 가능성을 최소화하기 위해 보낸 사람 신뢰도를 특수화합니다. 이러한 SMTP 릴레이 서비스는 Exchange Online Protection 및 SendGrid를 포함하지만 여기에 제한되지 않습니다. SMTP 릴레이 서비스는 구독 유형에 관계 없이 Azure에서 제한되지 않고 사용할 수 있습니다. 

  2017년 11월 15일까지 Azure 구독을 만든 경우 SMTP 릴레이 서비스를 사용할 수 있을 뿐만 아니라 TCP 포트 25를 통해 직접 전자 메일을 보낼 수 있습니다. 2017년 11월 15일 이후에 구독을 만든 경우 포트 25를 통해 직접 전자 메일을 보낼 수 없습니다. 포트 25를 통한 아웃바운드 통신 동작은 다음과 같이 구독 형식에 따라 다릅니다.

     - **기업 계약**: 아웃바운드 포트 25 통신이 허용됩니다. Azure 플랫폼의 제한 없이 가상 머신에서 외부 전자 메일 공급자로 직접 아웃바운드 전자 메일을 보낼 수 있습니다. 
     - **종량제:** 모든 리소스에서 아웃바운드 포트 25 통신이 차단되었습니다. 가상 머신에서 전자 메일을 외부 전자 메일 공급자로 직접 보내야 하는 경우(인증된 SMTP 릴레이를 사용하지 않음) 제한을 제거하도록 요청할 수 있습니다. 요청은 Microsoft의 재량에 따라 검토되고 승인되어 부패 방지 검사를 수행한 후에 허가됩니다. 요청하려면 *기술*, *Virtual Network 연결*, *전자 메일을 보낼 수 없습니다(SMTP/포트 25).* 라는 문제 형식의 지원 사례를 엽니다. 지원 사례에는 인증된 SMTP 릴레이를 통하지 않고 구독에서 메일 공급자로 직접 전자 메일을 보내야 하는 이유에 대한 세부 정보가 포함됩니다. 구독이 제외되는 경우 예외 날짜 이후에 만든 가상 머신만이 포트 25를 통해 아웃바운드로 통신할 수 있습니다.
     - **CSP(클라우드 서비스 공급자), MSDN, Azure Pass, Azure in Open, Education, BizSpark 및 평가판**: 25 아웃바운드 포트 통신이 모든 리소스에서 차단됩니다. 요청이 허가되지 않기 때문에 제한을 제거하도록 요청할 수 없습니다. 가상 머신에서 전자 메일을 보내야 하는 경우 SMTP 릴레이 서비스를 사용해야 합니다.

  Azure에서 25 포트를 통해 전자 메일을 보낼 수 있도록 허용하는 경우 Microsoft는 전자 메일 공급자에서 가상 머신의 인바운드 전자 메일을 수락하도록 보장할 수 없습니다. 특정 공급자가 가상 머신의 메일을 거부하는 경우 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 공급자와 직접 작업하거나 인증된 SMTP 릴레이 서비스를 사용해야 합니다. 


## <a name="next-steps"></a>다음 단계

* [네트워크 보안 그룹 만들기](tutorial-filter-network-traffic.md)에 대해 알아보세요.
