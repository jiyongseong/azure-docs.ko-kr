---
title: "ARP 테이블 가져오기: Resource Manager: Azure ExpressRoute 문제 해결 | Microsoft Docs"
description: "이 페이지는 ExpressRoute 회로의 ARP 테이블을 가져오는 방법을 안내합니다."
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Resource Manager 배포 모델에서 ARP 테이블 가져오기
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 클래식](expressroute-troubleshooting-arp-classic.md)
> 
> 

이 문서는 ExpressRoute 회로의 ARP 테이블을 배우는 단계를 안내합니다. 

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 아래 설명한 지침으로 문제를 해결할 수 없다면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 열어야 합니다.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>주소 확인 프로토콜(ARP) 및 ARP 테이블
주소 확인 프로토콜(ARP)은 [RFC 826](https://tools.ietf.org/html/rfc826)에 정의된 계층 2 프로토콜입니다. ARP는 IP 주소로 이더넷 주소(MAC 주소)를 매핑하는 데 사용합니다.

ARP 테이블은 특정 피어링에 대한 ipv4 주소와 MAC 주소 매핑을 제공합니다. ExpressRoute 회로 피어링의 ARP 테이블은 각 인터페이스(기본 및 보조)에 대한 다음 정보를 제공합니다.

1. 온-프레미스 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
2. ExpressRoute 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
3. 매핑 사용 기간

ARP 테이블은 계층 2 구성의 유효성을 검사하고 기본적인 계층 2 연결 문제를 해결하는 데 도움을 줍니다. 

ARP 테이블의 예: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


다음 섹션은 ExpressRoute 에지 라우터가 나타내는 ARP 테이블을 보는 방법을 알려줍니다. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP 테이블을 학습하기 위한 필수 조건
더 진행하기 전에 다음이 있는지 확인합니다.

* 1개 이상 피어링으로 구성된 유효한 ExpressRoute 회로. 연결 공급자가 완벽히 구성한 회로여야 합니다. 사용자(또는 연결 공급자)는 이 회로에서 하나 이상의 피어링을 구성했어야 합니다(Azure 개인, Azure 공용 및 Microsoft).
* 피어링(Azure 개인, Azure 공용 및 Microsoft) 구성에 사용한 IP 주소 범위. [ExpressRoute 라우팅 요구 사항 페이지](expressroute-routing.md) 에서 IP 주소 할당 예제를 검토하여 사용자 측과 ExpressRoute 측에서 인터페이스에 IP 주소를 매핑하는 방법을 파악합니다. [ExpressRoute 피어링 구성 페이지](expressroute-howto-routing-arm.md)를 검토하면 피어링 구성에 관한 정보를 얻을 수 있습니다.
* 네트워킹 팀/연결 공급자가 제공한 해당 IP 주소에서 사용하는 인터페이스 MAC 주소 정보.
* 최신 Azure용 PowerShell 모듈(1.50 버전 이상)이 있어야 합니다.

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 회로의 ARP 테이블 가져오기
이 섹션은 PowerShell을 사용하여 피어링당 ARP 테이블을 보는 방법을 설명합니다. 사용자 또는 연결 공급자는 더 진행하기 전에 피어링을 구성해야 합니다. 각 회로는 두 가지 경로(기본 및 보조)가 있습니다. 각 경로의 ARP 테이블을 독립적으로 확인할 수 있습니다.

### <a name="arp-tables-for-azure-private-peering"></a>Azure 개인 피어링용 ARP 테이블
다음 cmdlet은 Azure 개인 피어링용 ARP 테이블을 제공합니다.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure 공용 피어링용 ARP 테이블
다음 cmdlet은 Azure 공용 피어링용 ARP 테이블을 제공합니다.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 피어링용 ARP 테이블
다음 cmdlet은 Microsoft 피어링용 ARP 테이블을 제공합니다.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>이 정보를 사용하는 방법
피어링의 ARP 테이블을 사용하여 계층 2 구성과 연결의 유효성을 검사할 수 있습니다. 이 섹션은 다양한 시나리오에서 ARP 테이블이 표시되는 모습의 개요를 보여줍니다.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>회로가 작동 상태일 때 ARP 테이블(예상 상태)
* ARP 테이블은 유효한 IP 주소 및 MAC 주소가 포함된 온-프레미스 측의 항목과 Microsoft 측의 유사한 항목이 있습니다. 
* 온-프레미스 IP 주소의 마지막 옥텟은 항상 홀수입니다.
* Microsoft IP 주소의 마지막 옥텟은 항상 짝수입니다.
* 모든 3개 피어링(기본/보조)에 대해 Microsoft 측에 같은 MAC 주소가 나타납니다. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>온-프레미스/연결 공급자 측에 문제가 있을 때 ARP 테이블
온-프레미스 또는 연결 공급자에 문제가 있는 경우 ARP 테이블에 한 개의 항목만 나타나거나 온-프레미스 MAC 주소가 불완전하게 표시됩니다. Microsoft 측에서 사용된 MAC 주소와 IP 주소 사이의 매핑을 보여줍니다. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

또는
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> 이 문제를 디버그하려면 연결 공급자로 지원 요청을 엽니다. ARP 테이블에 MAC 주소에 매핑된 인터페이스의 IP 주소가 없으면 다음 정보를 검토합니다.
> 
> 1. MSEE-PR과 MSEE 사이의 링크에 할당된 /30 서브넷의 첫 번째 IP 주소가 MSEE-PR의 인터페이스에서 사용되는 경우 Azure에서 항상 두 번째 IP 주소를 MSEE에 사용합니다.
> 2. 고객(C-Tag) 및 서비스(S-Tag) VLAN 태그가 MSEE-PR 및 MSEE 쌍 모두와 일치하는지 확인합니다.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Microsoft 측에 문제가 있을 때 ARP 테이블
* Microsoft 측에 문제가 있으면 피어링에 대한 ARP 테이블이 나타나지 않습니다. 
* [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)으로 지원 티켓을 엽니다. 계층 2 연결 문제가 있다고 표시합니다. 

## <a name="next-steps"></a>다음 단계
* ExpressRoute 회로를 위한 계층 3 구성의 유효성 검사
  * 라우트 요약에서 BGP 세션 상태 확인 
  * 라우트 테이블에서 ExpressRoute에 어떤 접두사가 보급되었는지 확인
* 바이트 입출력으로 데이터 전송의 유효성 검사
* 여전히 문제가 해결되지 않을 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 엽니다.

