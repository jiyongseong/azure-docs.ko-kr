---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA를 사용하도록 필수 연결 인프라를 구성합니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51089ffa05168d2309bd2a96ec44b2ce0fed75f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778293"
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure(큰 인스턴스)의 SAP HANA 인프라 및 연결 

이 가이드를 읽기에 앞서 일부 사전 정의. [SAP HANA (큰 인스턴스) 개요 및 Azure 상의 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에서 HANA 큰 인스턴스 단위의 두 다른 클래스를 도입했습니다.

- SKU의 'Type I 클래스'인 S72, S72m, S144, S144m, S192 및 S192m.
- SKU의 'Type II 클래스'인 S384, S384m, S384xm, S576m, S768m 및 S960m.

HANA 큰 인스턴스 설명서 전반에 걸쳐 HANA 큰 인스턴스 SKU를 기반으로 하여 궁극적으로 다양한 기능 및 요구를 참조하는 데 클래스 지정자가 사용될 것입니다.

우리가 자주 사용하는 다른 정의는 다음과 같습니다.
- **큰 인스턴스 스탬프:** SAP HANA TDI 인증을 받았고 Azure 내에서 SAP HANA 인스턴스를 전용으로 실행하는 하드웨어 인프라 스택입니다.
- **Azure(큰 인스턴스)에서 SAP HANA:** 다양한 Azure 지역에서 큰 인스턴스 스탬프로 배포된 SAP HANA TDI 인증 하드웨어에서 HANA 인스턴스를 실행하는 Azure 제품에 대한 공식적인 이름입니다. 관련 용어인 **HANA 큰 인스턴스**는 Azure(큰 인스턴스)에서 SAP HANA의 약어이며 기술적 배포 가이드에 널리 사용됩니다.
 

사용자와 Microsoft 엔터프라이즈 계정 팀 간에 Azure(큰 인스턴스)의 SAP HANA 구매를 최종적으로 마무리한 후에 Microsoft에서 HANA 큰 인스턴스 단위를 배포하려면 다음과 같은 정보가 필요합니다.

- 고객 이름
- 비즈니스 연락처 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 네트워킹 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- Azure 배포 지역(2017년 7월 현재 미국 서부, 미국 동부, 오스트레일리아 동부, 오스트레일리아 남동부, 유럽 서부 및 북유럽)
- Azure(큰 인스턴스) SKU(구성)에서 SAP HANA 확인
- HANA 큰 인스턴스의 개요 및 아키텍처 문서에서 이미 자세히 설명했듯이 다음에 배포되는 모든 Azure 지역의 경우:
    - Azure VNet을 HANA 큰 인스턴스에 연결하는 ER-P2P 연결에 대한 /29 IP 주소 범위
    - /24 CIDR 블록(HANA 큰 인스턴스 서버 IP 풀에 사용)
- HANA 큰 인스턴스에 연결하는 모든 Azure VNet의 VNet 주소 공간 특성에 사용된 IP 주소 범위 값
- HANA 큰 인스턴스 시스템 각각에 대한 데이터:
  - 원하는 호스트 이름 - 정규화된 도메인 이름이 이상적.
  - 서버 IP 풀 주소 범위 밖의 HANA 큰 인스턴스 단위의 원하는 IP 주소 - 서버 IP 풀 주소 범위의 처음 30개 IP 주소는 HANA 큰 인스턴스 내에서 내부용으로 예약되어 있습니다.
  - SAP HANA 인스턴스의 SAP HANA SID 이름(필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필수). HANA SID는 NFS 볼륨에서 sidadm에 대한 허락을 생성하기 위해 필요하며, HANA 큰 인스턴스 단위에 연결됩니다. 또한 탑재된 디스크 볼륨의 이름 구성 요소 중 하나로 사용됩니다. 단위에 둘 이상의 HANA 인스턴스를 실행하려는 경우 여러 HANA SID를 나열해야 합니다. 각각에 별도의 볼륨 집합이 할당됩니다.
  - sidadm 사용자가 Linux OS에서 가지고 있는 groupid는 필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필요합니다. 일반적으로 SAP HANA 설치에서는 그룹 ID가 1001인 sapsys 그룹을 생성합니다. sidadm 사용자가 그룹에 포함됩니다.
  - sidadm 사용자가 Linux OS에서 가지고 있는 userid는 필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필요합니다. 단위에서 여러 HANA 인스턴스를 실행하는 경우 모든 <sid>adm 사용자를 나열해야 합니다 
- Azure HANA 큰 인스턴스의 SAP HANA를 직접 연결하는 Azure 구독의 Azure 구독 ID. 이 구독 ID는 HANA 큰 인스턴스 단위로 청구될 Azure 구독을 참조합니다.

정보를 제공한 후 Microsoft는 Azure(큰 인스턴스)에 SAP HANA를 프로비전하고 Azure VNet을 HANA 큰 인스턴스에 연결하고 HANA 큰 인스턴스 단위에 액세스하는 데 필요한 정보를 반환합니다.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure VM을 HANA 큰 인스턴스에 연결

이미 [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에서 언급했듯이 Azure에서 SAP 응용 프로그램 계층을 사용하는 HANA 큰 인스턴스의 최소 배포는 다음과 같습니다.

![Azure(큰 인스턴스)에서 SAP HANA 및 온-프레미스로 연결된 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Azure VNet 쪽을 자세히 살펴보면 다음에 대한 필요성을 인식하게 됩니다.

- SAP 응용 프로그램 계층의 VM을 배포하는 데 사용할 Azure VNet의 정의입니다.
- 이는 자동으로 Azure Vnet의 기본 서브넷이 VM을 배포하는 데 사용되는 실제 서브넷으로 정의됨을 의미합니다.
- 만들어진 Azure VNet에는 하나 이상의 VM 서브넷과 하나의 ExpressRoute 게이트웨이 서브넷이 있어야 합니다. 이러한 서브넷은 다음 섹션에서 지정하고 설명한 대로 IP 주소 범위가 할당되어야 합니다.

따라서 HANA 큰 인스턴스에 대한 Azure VNet 생성에 대해 자세히 살펴보겠습니다.

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>HANA 큰 인스턴스에 대한 Azure VNet 만들기

>[!Note]
>이 HANA 큰 인스턴스에 대한 Azure VNet은 Azure Resource Manager 배포 모델을 사용하여 만들어야 합니다. 일반적으로 클래식 배포 모델로 알려진 이전 Azure 배포 모델은 HANA 큰 인스턴스 솔루션에 지원되지 않습니다.

Azure Portal, PowerShell, Azure 템플릿 또는 Azure CLI를 사용하여 VNet을 만들 수 있습니다([Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) 참조). 다음 예에서 Azure Portal을 통해 생성된 VNet에 대해 살펴봅니다.

Azure Portal을 통해 Azure VNet의 정의를 살펴보기 위해 몇 가지 정의와 그 정의가 다양한 IP 주소 범위에 나열된 것과 어떻게 관련되는지 살펴보겠습니다. **주소 공간**에 대해 이야기할 때 Azure VNet의 사용이 허용된 주소 공간을 의미합니다. 이 주소 공간은 VNet에서 BGP 경로 전파에 사용할 주소 범위이기도 합니다. 이 **주소 공간**은 여기에서 볼 수 있습니다.

![Azure Portal에 표시된 Azure VNet의 주소 공간](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

앞의 경우 10.16.0.0/16으로, Azure VNet은 상당히 크고 넓은 IP 주소 범위를 사용했습니다. 이 VNet 내의 후속 서브넷의 모든 IP 주소 범위가 '주소 공간' 내에 자체 범위를 포함할 수 있음을 의미합니다. 일반적으로는 Azure에서 단일 VNet에 대해서는 이러한 큰 주소 범위를 권장하지 않습니다. 한편, 한 단계 더 나아가 Azure VNet에 정의된 서브넷을 살펴보겠습니다.

![Azure VNet 서브넷 및 해당 IP 주소 범위](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

알다시피 VNet은 첫 번째 VM 서브넷(여기서는 'default'라고 함)과 'GatewaySubnet'이라는 서브넷을 포함합니다.
다음 섹션에서, 그림에서 'default'라고 불리는 서브넷의 IP 주소 범위를 **Azure VM 서브넷 IP 주소 범위**라고 합니다. 다음 섹션에서는 게이트웨이 서브넷의 IP 주소 범위를 **VNet 게이트웨이 서브넷 IP 주소 범위**라고 합니다. 

위의 두 그림에 설명된 경우에서 **VNet 주소 공간**은 **Azure VM 서브넷 IP 주소 범위** 및 **VNet 게이트웨이 서브넷 IP 주소 범위**를 모두 포함합니다. 

IP 주소 범위를 절약하거나 구체적으로 지정해야 하는 경우 VNet의 **VNet 주소 공간**을 각 서브넷에 사용되는 특정 범위로 제한할 수 있습니다. 이 경우 VNet의 **VNet 주소 공간**을 여기 표시된 것처럼 여러 특정 범위로 정의할 수 있습니다.

![두 공간이 있는 Azure VNet 주소 공간](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

이 경우 **VNet 주소 공간**에는 두 개의 공간이 정의되어 있습니다. 이 두 공간은 **Azure VM 서브넷 IP 주소 범위** 및 **VNet 게이트웨이 서브넷 IP 주소 범위**에 대해 정의된 IP 주소 범위와 동일합니다.

이러한 테넌트 서브넷(VM 서브넷)에 원하는 모든 명명 표준을 사용할 수 있습니다. 그러나 Azure(대형 인스턴스) ExpressRoute 회로 상의 SAP HANA에 연결하는 **각 VNet에 대한 하나 및 하나만의 게이트웨이 서브넷이 항상 있어야 합니다**. 그리고 ExpressRoute 게이트웨이가 제대로 배치되도록 하려면 **언제나 이 게이트웨이 서브넷의 이름이 "GatewaySubnet"이어야 합니다**.

> [!WARNING] 
> 게이트웨이 서브넷의 이름이 항상 "GatewaySubnet"이어야 하는 것이 아주 중요합니다.

연속되지 않은 주소 범위를 활용하더라도 여러 VM 서브넷을 사용할 수 있습니다. 그러나 이전에 언급했듯이, 이러한 주소 범위는 집계된 양식이나 VM 서브넷 및 게이트웨이 서브넷의 정확한 범위 목록으로 VNet의 **VNet 주소 공간**에 포함되어야 합니다.

HANA 큰 인스턴스에 연결되는 Azure VNet에 대한 중요한 사실 요약:

- HANA 큰 인스턴스의 초기 배포를 수행하는 경우 **VNet 주소 공간**을 Microsoft에 제출해야 합니다. 
- **VNet 주소 공간**은 Azure VM 서브넷 IP 주소 범위 및 VNet 게이트웨이 서브넷 IP 주소 범위에 대한 범위를 다루는 더 큰 범위일 수 있습니다.
- 또는 VM 서브넷 IP 주소 범위와 VNet 게이트웨이 서브넷 IP 주소 범위의 다양한 IP 주소 범위를 포함하는 여러 범위를 **VNet 주소 공간**으로 제출할 수도 있습니다.
- 정의된 **VNet 주소 공간**은 BGP 라우팅 전파에 사용됩니다.
- 게이트웨이 서브넷의 이름은 **"GatewaySubnet"** 이어야 합니다.
- **VNet 주소 공간**은 Azure에서 HANA 큰 인스턴스 단위로 트래픽을 허용 또는 차단하기 위해 HANA 큰 인스턴스 쪽의 필터로 사용됩니다. Azure VNet의 BGP 라우팅 정보와 HANA 큰 인스턴스 측에서 필터링을 위해 구성된 IP 주소 범위가 일치하지 않으면 연결 문제가 발생할 수 있습니다.
- 게이트웨이 서브넷에 대한 자세한 내용은 'VNet을 HANA 큰 인스턴스 ExpressRoute에 연결' 섹션에서 자세히 설명합니다.



### <a name="different-ip-address-ranges-to-be-defined"></a>다양한 IP 주소 범위 정의 

HANA 큰 인스턴스를 배포하는 데 필요한 IP 주소 범위에 대해서는 이전 섹션에서 이미 소개했습니다. 하지만 중요한 IP 주소 범위가 몇 가지 더 있습니다. 이 내용에 대해 자세히 알아보겠습니다. 초기 배포 요청을 보내기 전에 Microsoft에 모두 제출할 필요가 없는 다음 IP 주소를 정의해야 합니다.

- **VNet 주소 공간:** 이미 이전에 소개한 것처럼 SAP HANA 큰 인스턴스 환경에 연결되는 Azure Virtual Network(VNet)의 주소 공간 매개 변수에 할당한(또는 할당 계획) IP 주소 범위입니다. 이 주소 공간 매개 변수는 이전 그림에서와 같이 Azure VM 서브넷 범위와 Azure 게이트웨이 서브넷 범위로 구성된 여러 줄 값입니다. 이 범위는 온-프레미스, 서버 IP 풀 또는 ER-P2P 주소 범위와 겹치면 안 됩니다. 이런 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 사용되지 않는 하나 이상의 IP 주소 범위를 제공해야 합니다. 예: Azure VM 서브넷(이전 참조)이 10.0.1.0/24이고 Azure 게이트웨이 서브넷(다음 참조)이 10.0.2.0/28이면, Azure VNet 주소 공간은 10.0.1.0/24 및 10.0.2.0/28로 두 줄이 되도록 하는 것이 좋습니다. 주소 공간 값을 집계할 수 있지만 나중에 네트워크의 다른 곳에서 더 큰 주소 공간 내의 사용되지 않은 IP 주소를 우발적으로 재사용하는 것을 방지하기 위해 서브넷 범위와 일치시키는 것이 좋습니다. **VNET 주소 공간은 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**

- **Azure VM 서브넷 IP 주소 범위:** 이미 앞에서 설명한 것처럼 SAP HANA 큰 인스턴스 환경에 연결되는 Azure VNET의 Azure VNet 서브넷 매개 변수에 할당한(또는 할당 계획) IP 주소 범위입니다. 이 IP 주소 범위는 IP 주소를 Azure VM에 할당하는 데 사용됩니다. 이 범위를 벗어난 IP 주소 범위가 SAP HANA 큰 인스턴스 환경에 연결되도록 허용됩니다. 필요한 경우 여러 Azure VM 서브넷을 사용할 수 있습니다. 각 Azure VM 서브넷에 대해 /24 CIDR 블록을 사용하는 것이 좋습니다. 이 주소 범위는 Azure VNet 주소 공간에 사용된 값에 포함되어야 합니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다.

- **VNet 게이트웨이 서브넷 IP 주소 범위:** 사용 계획인 기능에 따라 권장되는 크기는 다음과 같습니다.
   - Ultra-performance ExpressRoute 게이트웨이: /26 주소 블록 - SKU의 Type II 클래스에 필요함
   - High-performance ExpressRoute 게이트웨이(이하)를 사용하고 VPN 및 ExpressRoute의 동시 존재: /27 주소 블록
   - 기타 모든 상황: /28 주소 블록. 이 주소 범위는 “VNet 주소 공간” 값에 사용된 값에 포함되어야 합니다. 이 주소 범위는 Microsoft에 제출해야 하는 Azure VNet 주소 공간 값에 사용된 값에 포함되어야 합니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. 

- **ER-P2P 연결을 위한 주소 범위:** 이 범위는 SAP HANA 큰 인스턴스 ExpressRoute(ER) P2P 연결을 위한 IP 범위입니다. 이 IP 주소 범위는 /29 CIDR IP 주소 범위여야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소 범위와 겹치면 안 됩니다. 이 IP 주소 범위는 Azure VNet ExpressRoute 게이트웨이에서 SAP HANA 큰 인스턴스 서버로 ER 연결을 설정하는 데 사용됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. **이 범위는 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**
  
- **서버 IP 풀 주소 범위:** 이 IP 주소 범위는 개별 IP 주소를 HANA 큰 인스턴스 서버에 할당하는 데 사용됩니다. 권장되는 서브넷 크기는 /24 CIDR 블록이지만 필요한 경우 최소 64개의 IP 주소를 제공하는 것보다 적은 규모일 수 있습니다. 이 범위에서 처음 30개 IP 주소는 Microsoft에서 사용하도록 예약됩니다. 범위 크기를 선택할 때 이 사실을 고려해야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소와 겹치면 안 됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. Azure(큰 인스턴스)에서 SAP HANA에 필요한 특정 IP 주소를 할당하는 데 사용할 /24(권장) 고유 CIDR 블록 **이 범위는 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**
 
위의 IP 주소 범위를 정의하고 계획해야 하지만 모든 항목을 Microsoft에 전송해야 하는 것은 아닙니다. 위의 내용을 요약하면 Microsoft에 이름을 지정해야 하는 IP 주소 범위는 다음과 같습니다.

- Azure VNet 주소 공간
- ER-P2P 연결에 대한 주소 범위
- 서버 IP 풀 주소 범위

HANA 큰 인스턴스에 연결해야 하는 추가 VNet을 추가하려면 Microsoft에 추가하는 새 Azure VNet 주소 공간을 제출해야 합니다. 

다음은 구성하고 궁극적으로 Microsoft에 제공해야 하는 다양한 범위 및 몇 가지 범위 예입니다. 보시다시피, Azure VNet 주소 공간의 값은 첫 번째 예에서는 집계되지 않지만 첫 번째 Azure VM 서브넷 IP 주소 범위와 VNet 게이트웨이 서브넷 IP 주소 범위의 범위에서 정의됩니다. Azure VNet 내에서 여러 VM 서브넷을 사용하는 것은 Azure VNet 주소 공간의 일부로 추가 VM 서브넷의 추가 IP 주소 범위를 구성하고 제출함으로써 적절하게 작동합니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

또한 Microsoft에 제출하는 데이터를 집계하는 가능성도 있습니다. 이 경우 Azure VNet의 주소 공간만 하나의 공간을 포함합니다. 이전 예제에 사용된 IP 주소 범위 사용. 이 집계된 VNet 주소 공간은 다음과 같습니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위의 두 번째 가능성](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

위에서 볼 수 있듯이 Azure VNet의 주소 공간을 정의한 두 개의 작은 범위 대신 4096개의 IP 주소를 포함하는 하나의 더 큰 범위를 포함합니다. 주소 공간의 그러한 큰 정의는 다소 큰 범위를 사용하지 않고 남겨 둡니다. VNet 주소 공간 값이 BGP 경로 전파에 사용되므로 온-프레미스 또는 네트워크의 다른 곳에서 사용하지 않는 범위를 사용하면 라우팅 문제가 발생할 수 있습니다. 따라서 주소 공간을 사용된 실제 서브넷 주소 공간과 밀접하게 연계하는 것이 좋습니다. VNet에서 가동 중단 시간이 발생하지 않고도 필요할 때 새 주소 공간 값을 나중에 언제든지 추가할 수 있습니다.
 
> [!IMPORTANT] 
> ER-P2P, 서버 IP 풀, Azure VNet 주소 공간의 각 IP 주소 범위는 서로 또는 네트워크의 다른 곳에서 사용되는 다른 범위와 겹치지 **않아야** 하며 각각은 분리되어 있어야 하고 앞의 두 그림은 다른 범위의 서브넷이 아님을 보여 줍니다. 범위 간 겹침이 발생하면 Azure VNet은 ExpressRoute 회로에 연결되지 않을 수 있습니다.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>주소 범위를 정의한 후 다음 단계
IP 주소 범위를 정의한 후에는 다음 작업이 수행되어야 합니다.

1. Azure VNet 주소 공간, ER-P2P 연결 및 서버 IP 풀 주소 범위에 대한 IP 주소 범위와 문서 시작 부분에 나열된 다른 데이터를 함께 제출합니다. 이 시점에서 VNet과 VM 서브넷 만들기를 시작할 수도 있습니다. 
2. ExpressRoute 회로는 Microsoft가 Azure 구독과 HANA 큰 인스턴스 스탬프 사이에 만듭니다.
3. 테넌트 네트워크는 Microsoft의 큰 인스턴스 스탬프에서 생성됩니다.
4. Microsoft는 HANA 큰 인스턴스와 통신할 Azure VNet 주소 공간의 IP 주소를 허용하도록 Azure(큰 인스턴스)의 SAP HANA에 네트워킹을 구성합니다.
5. 구매한 특정 Azure(큰 인스턴스)의 SAP HANA SKU에 따라 Microsoft에서 테넌트 네트워크의 계산 단위를 지정하고 저장소를 할당 및 탑재하며 운영 체제(SUSE 또는 Red Hat Linux)를 설치합니다. 이러한 단위에 대한 IP 주소는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 주소를 지정합니다.

배포 프로세스의 끝부분에는 Microsoft에서 다음 데이터를 사용자에게 제공합니다.
- HANA 큰 인스턴스에 Azure VNet을 연결하는 ExpressRoute 회로에 Azure VNet을 연결하는 데 필요한 정보:
     - 권한 부여 키
     - ExpressRoute PeerID
- ExpressRoute 회로 및 Azure VNet을 설정한 후 HANA 큰 인스턴스에 액세스하기 위한 데이터.

[SAP HANA 큰 인스턴스에 대한 종단 간 설치](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) 문서에서 HANA 큰 인스턴스를 연결하는 순서를 확인할 수 있습니다. 대다수의 다음 단계는 해당 문서의 배포 예에 표시됩니다. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>VNet을 HANA 큰 인스턴스 ExpressRoute에 연결

이제 모든 IP 주소 범위를 정의하고 Microsoft에서 데이터를 다시 가져 왔기 때문에 이전에 만든 VNet을 HANA 큰 인스턴스에 연결할 수 있습니다. Azure VNet을 만들면 ExpressRoute 게이트웨이를 VNet에 만들어서 큰 인스턴스 스탬프의 고객 테넌트에 연결되는 ExpressRoute 회로에 VNet을 연결해야 합니다.

> [!NOTE] 
> 새 게이트웨이가 지정된 Azure 구독에서 생성된 다음 지정된 Azure VNet에 연결되기 때문에 이 단계를 완료하는 데 최대 30분 정도가 걸릴 수 있습니다.

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. 그렇지 않으면 게이트웨이를 삭제하고 ExpressRoute 게이트웨이로 다시 만들어야 합니다. ExpressRoute 게이트웨이가 이미 설정된 경우 Azure VNet이 온-프레미스 연결성을 위해 ExpressRoute 회로에 이미 연결되어 있으므로 아래 VNet 연결 섹션을 진행합니다.

- (새로운) [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 VNet에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **Virtual Network 게이트웨이**를 추가한 다음 **ExpressRoute**를 게이트웨이 유형으로 선택합니다.
  - PowerShell을 대신 선택하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하고 사용하여 최적의 환경을 제공합니다. 다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤의 텍스트는 사용자 정보로 업데이트해야 하는 사용자 정의 변수입니다.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(큰 인스턴스)의 SAP HANA에 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

> [!IMPORTANT]
> SKU 유형 S384, S384m, S384xm, S576m, S768m 및 S960m(Type II 클래스 SKU) HANA 큰 인스턴스의 경우 UltraPerformance 게이트웨이 SKU 사용은 필수입니다.

### <a name="linking-vnets"></a>VNet 연결

이제 Azure VNet에는 ExpressRoute 게이트웨이가 있으므로 Microsoft에서 제공하는 권한 부여 정보를 사용하여 이 연결을 위해 만든 Azure(큰 인스턴스) ExpressRoute 회로에 대한 SAP HANA에 ExpressRoute 게이트웨이를 연결합니다. 이 단계는 Azure Portal 또는 PowerShell을 사용하여 수행할 수 있습니다. 포털을 사용하는 것이 좋지만 PowerShell 지침은 다음과 같습니다. 

- 각 연결에 대해 다른 AuthGUID를 사용하여 각 VNet 게이트웨이에 대해 다음 명령을 실행합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 VNet 및 해당 게이트웨이에 지정됩니다. 다른 Azure VNet을 추가하려면 HANA 큰 인스턴스를 Azure에 연결하는 ExpressRoute 회로에 대해 다른 AuthID를 가져와야 합니다. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

게이트웨이를 구독과 연결된 여러 ExpressRoute 회로에 연결하려는 경우 이 단계를 두 번 이상 실행해야 합니다. 예를 들어 VNet을 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 VNet 게이트웨이를 연결해야 할 가능성이 높습니다.

## <a name="adding-more-ip-addresses-or-subnets"></a>더 많은 IP 주소 또는 서브넷 추가

IP 주소 또는 서브넷을 추가하는 경우 Azure Portal, PowerShell 또는 CLI 중 하나를 사용합니다.

이 경우 새 집계 범위를 생성하는 대신 새 IP 주소 범위를 새 범위로 VNet 주소 공간에 추가하는 것이 좋습니다. 두 경우 모두 클라이언트의 HANA 큰 인스턴스 단위에 새 IP 주소 범위 밖의 연결을 허용하려면 이 변경 내용을 Microsoft에 제출해야 합니다. Azure 지원 요청을 열고 새 VNet 주소 공간이 추가되게 할 수 있습니다. 확인을 받은 후에 다음 단계를 수행합니다.

Azure Portal에서 추가 서브넷을 만들려면 [Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하고 PowerShell에서 추가 서브넷을 만들려면 [PowerShell을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요.

## <a name="adding-vnets"></a>VNet 추가

하나 이상의 Azure VNet을 처음 연결한 후에 Azure(큰 인스턴스)의 SAP HANA에 액세스하는 추가 항목을 추가할 수 있습니다. 먼저 Azure 지원 요청을 제출합니다. 이 요청에 특정 Azure 배포를 식별하는 특정 정보 및 Azure VNet 주소 공간의 IP 주소 공간 범위를 모두 포함합니다. Azure의 SAP HANA Service Management에서는 추가 VNet 및 ExpressRoute를 연결해야 하는 데 필요한 정보를 제공합니다. 모든 VNet에 대해 ExpressRoute 회로를 HANA 큰 인스턴스에 연결하는 데 고유한 권한 부여 키가 필요합니다.

새 Azure VNet을 추가하는 단계:

1. 온보딩 프로세스의 첫 번째 단계를 완성하고 _Azure VNet 만들기_ 섹션을 참조하세요.
2. 온보딩 프로세스의 두 번째 단계를 완성하고 _게이트웨이 서브넷 만들기_ 섹션을 참조하세요.
3. 추가 VNet을 HANA 큰 인스턴스 ExpressRoute 회로에 연결하려면, 새 VNet에 대한 정보를 사용하여 Azure 지원 요청을 열고 새 권한 부여 키를 요청합니다.
4. 인증이 완료되었다고 알림을 받으면 Microsoft에서 제공되는 권한 부여 정보를 사용하여 온보딩 프로세스에서 세 번째 단계를 완료합니다. _VNet 연결_ 섹션을 참조하세요.

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 증가

Azure의 SAP HANA Service Management에 문의하세요. Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로 대역폭을 증가시켜야 하는 경우 Azure 지원 요청을 만듭니다. (최대 10GBPS의 단일 회로 대역폭으로 증가하도록 요청할 수 있습니다.) 그러면 작업이 완료된 후에 Azure에서 이보다 속도를 빠르게 하기 위해 필요한 추가 작업 없음이라는 알림이 표시됩니다.

## <a name="adding-an-additional-expressroute-circuit"></a>추가 ExpressRoute 회로 추가

Azure의 SAP HANA Service Management를 참조하여 추가 ExpressRoute 회로가 필요하다는 조언을 받은 경우 새 ExpressRoute 회로를 만들도록 Azure 지원에 요청하고 연결하는 인증 정보를 가져옵니다. VNet에 사용될 주소 공간은 Azure의 SAP HANA Service Management가 권한 부여를 제공하기 위해 요청하기 전에 정의되어야 합니다.

새 회로가 만들어지고 Azure의 SAP HANA Service Management 구성이 완료되면 진행해야 하는 정보를 포함한 알림을 받게 됩니다. 새 VNet을 만들고 이 추가 회로에 연결하기 위해 위에 제공된 단계를 수행합니다. 동일한 Azure 지역에 있는 Azure(큰 인스턴스) ExpressRoute 회로 상의 또 다른 SAP HANA에 이미 연결된 경우 Azure VNet을 이 추가 회로에 연결할 수 없습니다.

## <a name="deleting-a-subnet"></a>서브넷 삭제

VNet 서브넷을 제거하기 위해 Azure Portal, PowerShell 또는 CLI 중 하나를 사용할 수 있습니다. Azure VNet IP 주소 범위/Azure VNet 주소 공간이 집계 범위인 경우 Microsoft의 후속 조치가 없습니다. 단, 삭제된 서브넷을 포함하는 BGP 경로 주소 공간을 VNet이 여전히 전파하고 있습니다. Azure VNet IP 주소 범위/Azure VNet 주소 공간을 삭제된 서브넷에 할당된 여러 IP 주소 범위로 정의한 경우, VNet 주소 공간에서 해당 주소를 삭제하고 Azure의 SAP HANA Service Management에 알려서 Azure(큰 인스턴스)의 SAP HANA 통신 허용 범위에서 제거하도록 합니다.

서브넷을 삭제하려면 서브넷 삭제에 대한 자세한 내용을 제공하는 [서브넷 삭제](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)를 참조하세요.

## <a name="deleting-a-vnet"></a>VNet 삭제

가상 네트워크를 삭제하려면 [가상 네트워크 삭제](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)를 참조하세요. Azure의 SAP HANA Service Management는 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로에 대한 기존 권한 부여를 제거하고 HANA 큰 인스턴스와의 통신에 대한 Azure VNet IP 주소 범위/Azure VNet 주소 공간을 제거합니다.

VNet을 제거하면 제거될 IP 주소 공간 범위를 제공하는 Azure 지원 요청이 열립니다.

모든 항목이 제거되었는지 확인하려면 다음 항목을 삭제합니다.

- ExpressRoute 연결, VNet 게이트웨이, VNet 게이트웨이 공용 IP 및 VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 회로 삭제

추가 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로를 제거하려면 Azure의 SAP HANA Service Management를 통해 Azure 지원 요청을 열고 회로를 삭제하도록 요청합니다. Azure 구독 내에서 필요에 따라 VNet을 삭제하거나 유지할 수 있습니다. 그러나 HANA 큰 인스턴스 ExpressRoute 회로와 연결된 VNet 게이트웨이 간에 연결을 삭제해야 합니다.

VNet도 제거하려면 위의 섹션에서 VNet 삭제에 대한 지침을 따릅니다.


