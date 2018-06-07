---
title: Azure 서비스에 대한 가상 네트워크 | Microsoft Docs
description: 가상 네트워크에 리소스를 배포하는 이점을 알아봅니다. 가상 네트워크의 리소스는 트래픽이 인터넷을 트래버스하지 않고 서로 간에, 그리고 온-프레미스 리소스와 통신할 수 있습니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: ecfe3fb6db6b0fb0561e31b3c8aa70b74785b807
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781166"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 서비스에 대한 가상 네트워크 통합

Azure 서비스를 Azure 가상 네트워크에 통합하면 가상 네트워크에 배포된 서비스 인스턴스에서 개인 액세스가 가능합니다.

다음 옵션을 사용하여 Azure 서비스와 가상 네트워크를 통합할 수 있습니다.
- 서비스의 전용 인스턴스를 가상 네트워크에 직접 배포합니다. 이러한 서비스의 전용 인스턴스를 가상 네트워크 내에서 및 온-프레미스에서 개인적으로 액세스할 수 있습니다.
- 서비스 끝점을 통해 가상 네트워크를 서비스로 확장. 서비스 끝점을 통해 개별 서비스 리소스를 가상 네트워크로 보호할 수 있습니다.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>가상 네트워크에 Azure 서비스 배포

공용 IP 주소를 통해 인터넷에 있는 대부분의 Azure 리소스와 통신할 수 있습니다. [가상 네트워크](virtual-networks-overview.md)에 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 배포된 서비스](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

가상 네트워크 내에 서비스를 배포하면 다음과 같은 기능이 제공됩니다.

- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 [사이트 간 VPN(VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 [피어링](virtual-network-peering-overview.md)하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 Azure 서비스에서 인스턴스 상태를 모니터링하고 부하에 따라 필요한 배율을 제공하도록 완벽하게 관리됩니다.
- 서비스 인스턴스는 가상 네트워크의 전용 서브넷에 배포됩니다. 서비스에서 제공한 지침에 따라 서브넷에 대한 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 통해 인바운드 및 아웃바운드 액세스를 열어야 합니다.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>가상 네트워크에 배포할 수 있는 서비스

가상 네트워크에 직접 배포된 각 서비스에는 서브넷 내부 및 외부로 허용되어야 하는 트래픽 유형 및 라우팅에 대한 특정 요구 사항이 있습니다. 자세한 내용은 다음을 참조하세요. 
 
- 가상 머신: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service 환경](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway(내부)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [AKS(Azure Kubernetes Service)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Azure Virtual Network CNI [플러그 인](https://github.com/Azure/acs-engine/tree/master/examples/vnet)을 사용한 [Azure Container Service 엔진](https://github.com/Azure/acs-engine)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): 가상 네트워크(클래식)만
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Cloud Services](https://msdn.microsoft.com/library/azure/jj156091): 가상 네트워크(클래식)만

[내부 Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 배포하여 이전 목록에 있는 많은 리소스의 부하를 분산할 수 있습니다. 일부 경우에는 리소스를 만들 때 서비스에서 자동으로 부하 분산 장치를 만들어 배포합니다.

## <a name="service-endpoints-for-azure-services"></a>Azure 서비스에 대한 서비스 끝점

일부 Azure 서비스는 가상 네트워크에 배포할 수 없습니다. 필요한 경우 가상 네트워크 서비스 끝점을 사용하도록 설정하여 일부 서비스 리소스에 대한 액세스를 특정 가상 네트워크 서브넷으로만 제한할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 끝점](virtual-network-service-endpoints-overview.md)을 참조하세요.

현재 서비스 끝점은 다음 서비스에 대해 지원됩니다. 
- **Azure Storage**: [Azure Storage 계정을 가상 네트워크로 보호](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Azure SQL Database**: [Azure SQL Database를 가상 네트워크로 보호](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>여러 Azure 서비스에서 가상 네트워크 통합

가상 네트워크의 서브넷에 Azure 서비스를 배포하고 중요한 서비스 리소스를 해당 서브넷으로 보호할 수 있습니다. 예를 들어 가상 네트워크에 HDInsight를 배포하고 저장소 계정을 HDInsight 서브넷으로 보호할 수 있습니다.





