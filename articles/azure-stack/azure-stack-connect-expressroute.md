---
title: "Azure 스택 ExpressRoute를 사용 하 여 Azure에 연결"
description: "가상 네트워크 스택에서 Azure ExpressRoute를 사용 하 여 Azure에서 가상 네트워크에 연결 하는 방법."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Azure 스택 ExpressRoute를 사용 하 여 Azure에 연결

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure에서 가상 네트워크를 Azure 스택에서 가상 네트워크를 연결 하는 데 지원 되는 두 방법에는
   * **사이트 간**

     IPsec (IKE v1 및 IKE v2)을 통한 VPN 연결입니다. 이 연결 유형은 VPN 장치 또는 RRAS가 필요합니다. 자세한 내용은 참조 [VPN을 사용 하 여 Azure에 Azure 스택 연결](azure-stack-connect-vpn.md)합니다.
   * **ExpressRoute**

     Azure 스택 배포에서 Azure에 직접 연결 합니다. ExpressRoute는 **하지** 공용 인터넷을 통해 VPN 연결 합니다. Azure express 경로 대 한 자세한 내용은 참조 [ExpressRoute 개요](../expressroute/expressroute-introduction.md)합니다.

이 문서에서는 Azure 스택 Azure에 연결할 ExpressRoute를 사용 하는 예제를 보여 줍니다.
## <a name="requirements"></a>요구 사항
다음은 Azure 스택 및 express 경로 사용 하 여 Azure에 연결 하는 특정 요구 사항입니다.
* Azure의 ExpressRoute 회로 Vnet을 만들려면 Azure 구독입니다.
* A를 통해 ExpressRoute 회로 프로 비전 한 [연결 공급자](../expressroute/expressroute-locations.md)합니다.
* ExpressRoute 회로 WAN 포트에 연결 하는 라우터로 합니다.
* 라우터의 LAN 쪽 Azure 스택 다중 테 넌 트 게이트웨이에 연결 됩니다.
* 라우터가는 LAN 인터페이스와 Azure 스택 다중 테 넌 트 게이트웨이 간의 사이트 간 VPN 연결을 지원 해야 합니다.
* 둘 이상의 테 넌 트 Azure 스택 배포에 추가 되 면 라우터 여러 Vrf (가상 라우팅 및 전달을)를 만들 수 있어야 합니다.

다음 다이어그램에서는 구성을 완료 한 후 개념적 네트워킹 보기를 보여 줍니다.

![개념 다이어그램](media/azure-stack-connect-expressroute/Conceptual.png)

**다이어그램 1**

다음 아키텍처 다이어그램 방법을 여러 테 넌 트 ExpressRoute 라우터를 통해 Azure 스택 인프라에서 Azure에 연결할 Microsoft edge를 보여 줍니다.

![아키텍처 다이어그램](media/azure-stack-connect-expressroute/Architecture.png)

**다이어그램 2**

이 문서에 표시 된 예에서는 개인 피어 링 express 경로 통해 Azure에 연결 하려면 동일한 아키텍처를 사용 합니다. 작업이 수행 되는 ExpressRoute 라우터를 Azure 스택에서 가상 네트워크 게이트웨이에서 사이트 간 VPN 연결을 사용 합니다. 이 문서의 다음 단계에는 Azure에서의 해당 Vnet에 Azure 스택의 두 명의 서로 다른 테 넌 트 로부터 두 개의 Vnet 간의 종단 간 연결을 만드는 방법을 보여 줍니다. 추가 Vnet을 테 넌 트 및 각 테 넌 트에 대 한 단계를 복제 하거나이 예제를 사용 하 여 방금 단일 테 넌 트 VNet 배포할 많은 선택할 수 있습니다.

## <a name="configure-azure-stack"></a>Azure 스택 구성
이제 테 넌 트로 Azure 스택 환경을 설정 하는 데 필요한 리소스를 만듭니다. 다음 단계에 설명 된 수행 해야 합니다. 이러한 지침은 Azure 스택 포털을 사용 하는 리소스를 만드는 방법을 하지만 PowerShell을 사용할 수도 있습니다.

![네트워크 리소스 단계](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>시작하기 전에
구성을 시작 하기 전에 다음을 수행 해야 합니다.
* Azure 스택 배포 합니다.

   Azure 스택 개발 키트를 배포 하는 방법에 대 한 정보를 참조 하십시오. [Azure 스택 개발 키트 배포 빠른 시작](azure-stack-deploy-overview.md)합니다.
* 사용자가 구독할 수 있는 Azure 스택에 제공 합니다.

  자세한 내용은 [Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록](azure-stack-tutorial-tenant-vm.md)합니다.

### <a name="create-network-resources-in-azure-stack"></a>Azure 스택 네트워크 리소스를 만듭니다

각 테 넌 트에 대 한 Azure 스택에 필요한 네트워크 리소스를 만들려면 다음 절차를 사용 합니다.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기
1. 사용자 (테 넌 트) 계정 사용 하 여 사용자 포털에 로그인 합니다.

2. 포털에서 클릭 **새로**합니다.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Marketplace 메뉴에서 **네트워킹**을 선택합니다.

4. 메뉴에서 **가상 네트워크**를 클릭합니다.

5. 다음 표에서를 사용 하 여 적절 한 필드에 값을 입력 합니다.

   |필드  |값  |
   |---------|---------|
   |이름     |Tenant1VNet1         |
   |주소 공간     |10.1.0.0/16|
   |서브넷 이름     |Tenant1 Sub1|
   |서브넷 주소 범위     |10.1.1.0/24|

6. 앞서 만든 구독이 **구독** 필드에 채워지는 것을 볼 수 있습니다.

    a. 리소스 그룹에 대 한 리소스 그룹을 만들 하거나 이미 있는 경우 하나를 선택 **기존 항목 사용**합니다.

    나. 기본 위치를 확인합니다.

    다. **대시보드에 고정**을 클릭합니다.

    d. **만들기**를 클릭합니다.



#### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기
1. 대시보드에서 (Tenant1VNet1)를 만든 가상 네트워크 리소스를 엽니다.
2. 설정 섹션에서 선택 **서브넷**합니다.
3. **게이트웨이 서브넷**을 클릭하여 가상 네트워크에 게이트웨이 서브넷을 추가합니다.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다.
   게이트웨이 서브넷은 특별하며 제대로 작동하려면 특정 이름이 있어야 합니다.
5. 에 **주소 범위** 필드에서 주소 확인 **10.1.0.0/24**합니다.
6. **확인**을 클릭하여 게이트웨이 서브넷을 만듭니다.

#### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기
1. Azure 스택 사용자 포털에서 클릭 **새로**합니다.
   
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 네트워크 리소스 목록에서 **가상 네트워크 게이트웨이**를 선택합니다.
4. **이름** 필드에 **GW1**을 입력합니다.
5. **가상 네트워크** 항목을 클릭하여 가상 네트워크를 선택합니다.
   선택 **Tenant1VNet1** 목록에서 합니다.
6. **공용 IP 주소** 메뉴 항목을 클릭합니다. 경우는 **공용 IP 주소 선택** 섹션 열립니다 클릭 **새로 만들기**합니다.
7. **이름** 필드에 **GW1-PiP**를 입력하고 **확인**을 클릭합니다.
8. **VPN 종류**에 **Route-based**(경로 기반)이 기본으로 선택되어 있습니다.
    이 설정을 유지합니다.
9. **구독** 및 **위치**가 올바른지 확인합니다. 원하는 경우 리소스를 대시보드에 고정할 수 있습니다. **만들기**를 클릭합니다.

#### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이 리소스의 목적은 VPN 연결의 다른 쪽 끝에서 원격 게이트웨이 나타냅니다. 이 예제에서는 원격 대화 상대 ExpressRoute 라우터의 LAN 하위 인터페이스입니다. 이 예에서 테 넌 트 1에 대 한 원격 주소 다이어그램 2에 표시 된 것과 같이 10.60.3.255입니다.

1. Azure Stack 물리적 컴퓨터에 로그인합니다.
2. 사용자 계정 사용 하 여 사용자 포털에 로그인 하 고 클릭 **새로**합니다.
3. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
4. 리소스 목록에서 **로컬 네트워크 게이트웨이**를 선택합니다.
5. 에 **이름** 필드 형식이 **ER-라우터-GW**합니다.
6. 에 대 한는 **IP 주소** 필드 다이어그램 2를 참조 하십시오. 테 넌 트 1에 대 한 express 경로 라우터의 LAN 하위 인터페이스의 IP 주소가 10.60.3.255 없습니다. 사용자 환경에 대 한 라우터의 해당 인터페이스의 IP 주소를 입력 합니다.
7. 에 **주소 공간** 필드를 Azure에 연결 하려면 vnet 주소 공간을 입력 합니다. 이 예제에서는 다이어그램 2를 참조 하십시오. 테 넌 트 1에 대 한 필요한 서브넷이 있는지 확인 **192.168.2.0/24** (Azure에서 허브 Vnet은)와 **10.100.0.0/16** (이것이 스포크 Azure의 VNet에에서). 사용자가 자신의 환경에 대 한 해당 서브넷을 입력 합니다.
   > [!IMPORTANT]
   > 이 예에서는 고정 경로 사용 하는 Azure 스택 게이트웨이 및 express 경로 라우터 간의 사이트 간 VPN 연결에 대 한 가정 합니다.

8. 확인 프로그램 **구독**, **리소스 그룹**, 및 **위치** 모두 정확 하 고 클릭 **만들기**합니다.

#### <a name="create-the-connection"></a>연결 만들기
1. Azure 스택 사용자 포털에서 클릭 **새로**합니다.
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 리소스 목록에서 **연결**을 선택합니다.
4. 에 **기본 사항** 설정 섹션에서 선택 **사이트 (IPSec)** 로 **연결 유형**합니다.
5. 선택 된 **구독**, **리소스 그룹**, 및 **위치** 클릭 **확인**합니다.
6. 에 **설정** 섹션에서 클릭 **가상 네트워크 게이트웨이** 클릭 **GW1**합니다.
7. 클릭 **로컬 네트워크 게이트웨이**를 클릭 하 고 **ER 라우터 GW**합니다.
8. 에 **연결 이름** 필드를 입력 **ConnectToAzure**합니다.
9. 에 **공유 키 (PSK)** 필드를 입력 **abc123** 클릭 **확인**합니다.
10. 에 **요약** 섹션에서 클릭 **확인**합니다.

    연결을 만든 후에 가상 네트워크 게이트웨이에서 사용 되는 공용 IP 주소를 볼 수 있습니다. Azure 스택 포털에는 주소를 확인 하려면 가상 네트워크 게이트웨이를 찾습니다. **개요**, 찾을 **공용 IP 주소**합니다. 이 주소; note 로 사용할는 *내부 IP 주소* (해당 하는 경우 배포에 대 한) 다음 섹션에 있습니다.

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>가상 머신 만들기
VPN 연결을 통해 전송 되는 데이터의 유효성을 검사 하려면 가상 컴퓨터를 Azure 스택 Vnet에 데이터를 송수신 해야 합니다. 가상 컴퓨터를 지금 만들고 가상 네트워크에 VM 서브넷에 배치 합니다.

1. Azure 스택 사용자 포털에서 클릭 **새로**합니다.
2. Marketplace 메뉴에서 **Virtual Machines**를 선택합니다.
3. 가상 컴퓨터 이미지의 목록에서 선택 된 **Windows Server 2016 데이터 센터 Eval** 이미지 및 클릭 **만들기**합니다.
4. 에 **기본 사항** 섹션에 **이름** 필드 형식이 **v m 01**합니다.
5. 유효한 사용자 이름 및 암호를 입력합니다. VM을 만든 후 이 계정을 사용하여 VM에 로그인하게 됩니다.
6. 제공 된 **구독**, **리소스 그룹**, 및 **위치** 클릭 하 고 **확인**합니다.
7. 에 **크기** 섹션,이 인스턴스에 대 한 가상 컴퓨터 크기를 클릭 한 다음 클릭 **선택**합니다.
8. 에 **설정을** 섹션에서 기본값을 사용할 수 있습니다. 하지만 우수한 백로그가 선택한 가상 네트워크는 **Tenant1VNet1** 서브넷으로 설정 되 고 **10.1.1.0/24**합니다. **확인**을 클릭합니다.
9. 설정을 검토는 **요약** 섹션 및 클릭 **확인**합니다.

VNet 연결 하려는 각 테 넌 트의 이전 단계를 반복 **가상 네트워크 및 VM 서브넷을 만들** 통해 **가상 컴퓨터를 만들** 섹션.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>게이트웨이 순회에 NAT 가상 컴퓨터 구성
> [!IMPORTANT]
> Azure 스택 개발 키트 배포용이 섹션이입니다. 다중 노드 배포에 대 한 NAT의 필요 하지 않습니다.

Azure 스택 개발 키트는 완전히 물리적 호스트 배포 된 네트워크에서 분리 합니다. 따라서 게이트웨이에 연결 되어 있는 "외부" VIP 네트워크는 외부에 있지만 네트워크 주소 변환 (NAT)를 수행 하는 라우터 뒤에 숨겨지지 대신 합니다.
 
라우터는 Windows Server 가상 컴퓨터 (**AzS BGPNAT01**) Azure 스택 개발 키트 인프라에서 라우팅 및 원격 액세스 서비스 (RRAS) 역할을 실행 합니다. 사이트 간 VPN 연결 양쪽 끝에 연결할 수 있도록 AzS BGPNAT01 가상 컴퓨터에서 NAT를 구성 해야 합니다.

#### <a name="configure-the-nat"></a>NAT 구성

1. 관리자 계정으로 Azure 스택 물리적 컴퓨터에 로그인 합니다.
2. 복사 및 다음 PowerShell 스크립트를 편집 하 고 관리자 권한 Windows PowerShell ISE에서 실행 합니다. 관리자 암호를 대체 합니다. 반환 된 주소는 프로그램 *외부 BGPNAT 주소*합니다.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. NAT를 구성 하려면 복사 하 고 다음 PowerShell 스크립트를 편집 하 고 관리자 권한 Windows PowerShell ISE에서 실행 합니다. 바꿀 스크립트를 편집 하는 *외부 BGPNAT 주소* 및 *내부 IP 주소* (에서 이전에 기록한 있는 **연결을 만들려면** 섹션).

   예제 다이어그램에는 *외부 BGPNAT 주소* 10.10.0.62은 및 *내부 IP 주소* 192.168.102.1 됩니다.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Azure 구성
Azure 스택 구성을 완료 하 한 했으므로 일부 Azure 리소스를 배포할 수 있습니다. 다음 그림에 샘플 테 넌 트 Azure의 가상 네트워크입니다. Azure의 VNet에 대 한 다른 이름 및 주소 지정 체계를 사용할 수 있습니다. 그러나 Azure 및 Azure 스택의 Vnet의 주소 범위 고유 해야 하며 겹치면 안 됩니다.

![Azure Vnets](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**다이어그램 3**

Azure에 배포 하는 리소스는 Azure 스택에 배포 된 리소스와 비슷합니다. 마찬가지로, 배포:
* 가상 네트워크 및 서브넷
* 게이트웨이 서브넷
* 가상 네트워크 게이트웨이
* 한 연결
* ExpressRoute 회로

다음과 같은 방법으로 예제 Azure 네트워크 인프라 구성 되어 있습니다.
* 표준 (192.168.2.0/24) 허브 및 스포크 (10.100.0.0./16) VNet 모델 사용 됩니다.
* 작업 부하는 스포크 Vnet에에서 배포 하 고 ExpressRoute 회로 허브 VNet에 연결 합니다.
* VNet 피어 링 기능을 사용 하 여 두 개의 Vnet 연결 됩니다.

### <a name="configure-vnets"></a>Vnet 구성
1. Azure 자격 증명을 사용 하 여 Azure 포털에 로그인 합니다.
2. VNet 192.168.2.0/24 주소 공간을 사용 하 여 허브를 만듭니다. 192.168.2.0/25 주소 범위를 사용 하는 서브넷을 만들고 192.168.2.128/27 주소 범위를 사용 하 여 게이트웨이 서브넷을 추가 합니다.
3. 스포크 만들기 VNet과는 10.100.0.0/16를 사용 하 여 서브넷 주소 범위입니다.


Azure에서 가상 네트워크 만들기에 대 한 자세한 내용은 참조 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)합니다.

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute 회로 구성 합니다.

1. ExpressRoute 필수 조건을 검토 [ExpressRoute 필수 구성 요소 및 검사 목록](../expressroute/expressroute-prerequisites.md)합니다.
2. 단계에 따라 [만들기 ExpressRoute 회로 수정 하 고](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Azure 구독을 사용 하 여 ExpressRoute 회로 만들려고 합니다.
3. 호스팅 서비스 공급자/공급자가 express 경로 회로 프로 비전을 사용 하 여 이전 단계에서 서비스 키를 공유 합니다.
4. 단계에 따라 [만들기 ExpressRoute 회로 대 한 피어 링을 수정 하 고](../expressroute/expressroute-howto-routing-portal-resource-manager.md) ExpressRoute 회로에 개인 피어 링을 구성할 수 있습니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

* 단계에 따라 [PowerShell을 사용 하 여 ExpressRoute에 대 한 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-resource-manager.md) 허브 VNet에서에서 ExpressRoute에 대 한 가상 네트워크 게이트웨이 만들려고 합니다.

### <a name="create-the-connection"></a>연결 만들기

* ExpressRoute 회로 연결 하려면 VNet 허브의 단계에 따라 [가상 네트워크를 ExpressRoute 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)합니다.

### <a name="peer-the-vnets"></a>Vnet 피어 링

* 단계를 사용 하 여 허브 및 스포크 Vnet 피어 [Azure 포털을 사용 하 여 가상 네트워크 피어 링 만들기](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)합니다. VNet 피어 링을 구성할 때 다음 옵션을 선택 하면 확인 합니다.
   * 스포크 허브에서: **게이트웨이 전송 허용**
   * 허브에 스포크: **원격 게이트웨이 사용**

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

* 스포크 VNet에 작업 가상 컴퓨터를 배포 합니다.

추가 테 넌 트의 각 ExpressRoute 회로 통해 Azure에 연결 하려면 Vnet에 대해 이러한 단계를 반복 합니다.

## <a name="configure-the-router"></a>라우터 구성

ExpressRoute 라우터 구성 가이드 다음 종단 간 인프라 다이어그램을 사용할 수 있습니다. 이 다이어그램에서는 두 명의 테 넌 트 (1 테 넌 트 및 테 넌 트 2)에 각각 해당 Express 경로 회로 보여 줍니다. 각각은 두 테 넌 트 간의 종단 간 격리를 보장할 수 ExpressRoute 라우터의 LAN 및 WAN 쪽 (가상 라우팅 및 전달을) 자신의 VRF에 연결 됩니다. 샘플 구성에 따라 라우터 인터페이스에 사용 되는 IP 주소를 note 합니다.

![종단 간 다이어그램](media/azure-stack-connect-expressroute/EndToEnd.png)

**다이어그램 4**

IKEv2 VPN 및 BGP Azure 스택에서 사이트 간 VPN 연결을 종료를 지 원하는 모든 라우터를 사용할 수 있습니다. 같은 라우터에 ExpressRoute 회로 사용 하 여 Azure에 연결 하는 데 사용 됩니다. 

다음은 샘플 구성이 다이어그램 4에 표시 된 네트워크 인프라를 지 원하는 Cisco ASR 1000에서입니다.

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>연결 테스트

사이트 간 연결 및 express 경로 회로 설정한 후 연결을 테스트 합니다. 이 작업은 간단 합니다.  Azure VNet에서 만든 가상 컴퓨터 중 하나에 로그온 하 고 Azure 스택 환경에서 또는 그 반대로 만든 가상 컴퓨터를 ping 합니다. 

을 보장 하기 위해 사이트 간 및 express 경로 연결을 통한 트래픽을 보내는 모두 끝에서 가상 컴퓨터의 전용된 IP (DIP) 주소와 가상 컴퓨터의 VIP 주소가 아니라를 ping 해야 합니다. 따라서 찾을 하 고 연결의 반대쪽 끝에서 주소를 확인 해야 합니다.

### <a name="allow-icmp-in-through-the-firewall"></a>ICMP에 방화벽을 통해 허용
기본적으로 Windows Server 2016에는 방화벽을 통해에서 ICMP 패킷을 허용 되지 않습니다. 따라서 테스트에 사용할 각 가상 컴퓨터에 대 한 관리자 권한 PowerShell 창에서 다음 cmdlet을 실행 합니다.


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Azure 스택 가상 컴퓨터를 ping 합니다.

1. 테 넌 트 계정을 사용 하 여 Azure 스택 사용자 포털에 로그인 합니다.
2. 왼쪽 탐색 모음에서 **Virtual Machines**를 클릭합니다.
3. 이전에 만든 가상 컴퓨터를 찾아 클릭 합니다.
4. 가상 컴퓨터에 대 한 섹션을 클릭 하 여 **연결**합니다.
5. 관리자 권한의 PowerShell을 열고 창과 형식 **ipconfig /all**합니다.
6. 출력에 IPv4 주소를 찾아서 기록 합니다. Azure VNet 내에서 가상 컴퓨터에서이 주소를 ping 합니다. 예제 환경에는 10.1.1.x/24 서브넷의 주소가 되었습니다. 사용자 환경에서 주소 달라질 수 있습니다. 그러나 테 넌 트 VNet 서브넷에 대해 만든 서브넷 내 여야 합니다.


### <a name="view-data-transfer-statistics"></a>데이터 전송 통계 보기

연결을 통해 전달 트래픽 양을 확인 하려는 경우 Azure 스택 사용자 포털에 연결 섹션에 맞춰져 있으며이 정보를 찾을 수 있습니다. 이 정보 방금 전송 받은 ping VPN 및 express 경로 연결을 통해 실제로 있는지 확인 하는 또 다른 좋은 방법 이기도 합니다.

1. 테 넌 트 계정을 사용 하 여 Microsoft Azure 스택 사용자 포털에 로그인 합니다.
2. VPN 게이트웨이가 생성 된 위치는 리소스 그룹을 찾아서 선택 된 **연결** 개체 유형입니다.
3. 클릭는 **ConnectToAzure** 목록에 연결 합니다.
4. 에 **연결** 섹션에 대 한 통계를 볼 수 있습니다 **데이터에** 및 **데이터 출력**합니다. 일부 0이 아닌 값을 볼 수 있습니다.

   ![데이터 출력의 데이터](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>다음 단계
[Azure 및 Azure에 앱 배포 스택](azure-stack-solution-pipeline.md)
