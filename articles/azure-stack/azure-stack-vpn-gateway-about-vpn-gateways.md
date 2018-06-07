---
title: Azure 스택에 대 한 VPN 게이트웨이에 대 한 | Microsoft Docs
description: 에 대해 알아보고 Azure 스택과 함께 사용 하 여 VPN 게이트웨이 구성 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: 7e489db0d9a65b850df41360ce11616d518c5265
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Azure 스택에 대 한 VPN 게이트웨이에 대 한
*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*


Azure 가상 네트워크와 온-프레미스 사이트 간의 네트워크 트래픽을 보낼 수 있습니다, 전에 가상 네트워크에 대 한 가상 네트워크 게이트웨이 만들어야 합니다.

VPN Gateway는 공용 연결을 통해 암호화된 트래픽을 전송하는 가상 네트워크 게이트웨이의 유형입니다. Azure에서 안전 하 게 Azure 스택에서 가상 네트워크와 가상 네트워크 간에 트래픽을 보낼 VPN 게이트웨이 사용할 수 있습니다. 가상 네트워크와 VPN 장치에 연결 된 다른 네트워크 간에 안전 하 게 트래픽을 보낼 수도 있습니다.

가상 네트워크 게이트웨이를 만들 때 만들려는 게이트웨이 유형을 지정합니다. Azure 스택은 한 유형의 가상 네트워크 게이트웨이 지원: 'Vpn' 유형입니다.

가상 네트워크마다 두 개의 가상 네트워크 게이트웨이를 포함할 수 있으며 각 유형은 하나씩만 포함할 수 있습니다. 선택한 설정에 따라 단일 VPN Gateway에 대한 여러 연결을 만들 수 있습니다. 예를 들어 다중 사이트 연결 구성입니다.

> [!NOTE]
> Azure에서에 연결 하는 모든 연결에서 선택 하면 VPN 게이트웨이 SKU에 대 한 대역폭 처리량을 분할 해야 합니다.  Azure 스택에서 VPN 게이트웨이 SKU에 대 한 대역폭 값에 연결 된 각 연결 리소스에 적용 됩니다.     

> 예를 들어 Azure에서 기본 VPN 게이트웨이 SKU 수용할 수에 집계 처리량을 100mbps 약 합니다.  해당 VPN 게이트웨이에 대 한 두 개의 연결을 만들면 연결이 하나 사용 하는 경우 대역폭 50mbps, 50mbps 다음은 다른 연결에 사용할 수 있습니다.   

> Azure 스택에서 *각* 100mbps 처리량의 할당 된 기본 VPN 게이트웨이 SKU에 연결 합니다.

## <a name="configuring-a-vpn-gateway"></a>VPN Gateway 구성
VPN Gateway 연결은 특정 설정으로 구성된 여러 리소스에 따라 다릅니다. 대부분의 리소스를 특정 순서로 구성해야 하지만 어떤 경우에는 개별적으로 구성할 수 있습니다.

### <a name="settings"></a>설정
각 리소스에 선택하는 설정은 성공적인 연결을 만드는 데 매우 중요합니다. VPN 게이트웨이에 대 한 설정과 개별 리소스에 대 한 정보를 참조 하십시오. [에 대 한 VPN 게이트웨이 설정에 대 한 Azure 스택](azure-stack-vpn-gateway-settings.md)합니다. 게이트웨이 유형, VPN 유형, 연결 유형, 게이트웨이 서브넷, 로컬 네트워크 게이트웨이 및 고려할 수 있는 다양 한 리소스 설정 이해 하는 데 유용한 정보를 찾을 수 있습니다.

### <a name="deployment-tools"></a>배포 도구
수 만들고 Azure 포털 등의 한 구성 도구를 사용 하 여 리소스를 구성 합니다. 나중에 추가 리소스를 구성 하거나 해당 하는 경우 기존 리소스를 수정할 수는 PowerShell과 같은 다른 도구를 전환할 수 있습니다. 현재, Azure Portal에서 모든 리소스 및 리소스 설정을 구성할 수 없습니다. 각 연결 토폴로지에 대한 문서의 지침은 특정 구성 도구가 필요한지 여부를 지정합니다.

## <a name="connection-topology-diagrams"></a>연결 토폴로지 다이어그램
VPN Gateway 연결에 사용할 수 있는 다양한 구성이 있다는 사실을 꼭 기억하시기 바랍니다. 필요에 맞는 최적의 구성을 확인 합니다. 아래 섹션에서는 다음과 같은 VPN Gateway 연결에 대한 정보 및 토폴로지 다이어그램을 볼 수 있습니다. 다음 섹션에는 다음에 나열된 테이블이 포함되어 있습니다.

- 사용 가능한 배포 모델
- 사용 가능한 구성 도구
- 사용할 수 있는 경우 문서로 직접 이동하는 링크

다이어그램 및 설명은 다음 섹션의 요구 사항에 맞게 연결 토폴로지를 선택할 수 있습니다. 다이어그램은 기본 초기 토폴로지를 보여 주지만 다이어그램을 지침으로 사용하여 더 복잡한 구성을 작성할 수 있습니다.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>사이트 간 및 다중 사이트(IPsec/IKE VPN 터널)
### <a name="site-to-site"></a>사이트 간
S2S(사이트 간) VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통한 연결입니다. 이 연결 유형은 할당된 공용 IP 주소를 가지고 NAT 다음에 위치하지 않는 온-프레미스에 있는 VPN 장치를 필요로 합니다. S2S 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.    
![사이트 간 VPN 연결 구성 예](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>다중 사이트
사이트 간 연결에서 변형된 연결 유형입니다. 가상 네트워크 게이트웨이에서 일반적으로 여러 온-프레미스 사이트에 연결하는 둘 이상의 VPN 연결을 만듭니다. 여러 연결을 사용하는 경우 경로 기반 VPN 유형(클래식 VNet을 사용하는 경우 동적 게이트웨이)을 사용해야 합니다. 각 가상 네트워크가 하나의 VPN Gateway만 사용할 수 있으므로 게이트웨이를 통한 모든 연결은 사용 가능한 대역폭을 공유합니다. 이는 "다중 사이트" 연결이라고 합니다.   
![Azure VPN Gateway 다중 사이트 연결 예제](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>게이트웨이 SKU
Azure 스택에 대 한 가상 네트워크 게이트웨이 만들 때 SKU에 사용 하려는 게이트웨이 지정 합니다. 다음 VPN 게이트웨이 Sku가 지원 됩니다.
- Basic
- Standard
- HighPerformance

Standard 또는 Basic을 통해 더 높은 게이트웨이 통해 Basic, Standard 또는 HighPerformance 같은 SKU를 선택 하면 더 많은 Cpu 및 네트워크 대역폭 게이트웨이에 할당 됩니다. 결과적으로, 게이트웨이 가상 네트워크에 네트워크 처리량을 지원할 수 있습니다.

Azure 스택 UltraPerformance 게이트웨이 Express 경로와 단독으로 사용 하는 SKU를 지원 하지 않습니다.

SKU를 선택 하면 다음을 고려 하십시오.
- Azure 스택 정책 기반 게이트웨이 지원 하지 않습니다.
- 프로토콜 BGP (경계 게이트웨이)는 기본 SKU에서 지원 되지 않습니다.
- ExpressRoute VPN 게이트웨이 함께 사용할 Azure 스택의 구성은 지원 되지 않습니다
- 활성-활성 S2S VPN Gateway 연결은 HighPerformance SKU에서만 구성할 수 있습니다.

## <a name="estimated-aggregate-throughput-by-sku"></a>SKU 기준으로 예상된 총 처리량
다음 테이블에서는 게이트웨이 형식과 게이트웨이 SKU에 의한 예상 총 처리량을 보여 줍니다.

|   | VPN 게이트웨이 처리량 *(1)* | VPN 게이트웨이 최대 IPsec 터널 *(2)* |
|-------|-------|-------|
|**Basic SKU** ***(3)***    | 100Mbps  | 10    |
|**표준 SKU**       | 100Mbps  | 10    |
|**고성능 SKU** | 200Mbps    | 5 |
***(1)***  있는 VPN 처리량은 하지 크로스-프레미스 연결에 대 한 보장된 처리량 인터넷을 통해. 가능한 최대 처리량 측정값입니다.  
***(2)***  최대 터널 번호는 모든 구독에 대 한 Azure 스택 배포 당 총 합니다.  
***(3)***  기본 SKU에 대 한 BGP 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계
에 대 한 자세한 내용은 [VPN 게이트웨이에 대 한 설정을](azure-stack-vpn-gateway-settings.md) Azure 스택에 대 한 합니다.
