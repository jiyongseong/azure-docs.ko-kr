---
title: "DMZ 예제 - 방화벽, UDR, NSG를 사용하여 응용 프로그램을 보호하는 DMZ 빌드 | Microsoft Docs"
description: "방화벽, UDR(사용자 정의 라우팅), NSG(네트워크 보안 그룹)를 사용하여 DMZ 빌드"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: fdb3c5cbd3acee90386352c6f180a71aa81f54fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>예제 3 - 방화벽, UDR, NSG로 네트워크를 보호하는 DMZ 구축
[보안 경계 모범 사례 페이지로 돌아가기][HOME]

이 예제에서는 방화벽이 포함된 DMZ, 4개의 Windows Server, 사용자 정의 라우팅, IP 전달, 네트워크 보안 그룹을 만듭니다. 또한 각 단계를 자세히 이해할 수 있도록 각각의 관련 명령에 대해 안내합니다. 트래픽 시나리오 섹션에서는 DMZ에서 방어 계층을 진행하는 방법에 대한 심층적인 단계별 설명도 제공합니다. 마지막으로, 참조 섹션에서는 다양한 시나리오를 사용하여 테스트 및 실험하기 위한 환경을 구축하는 전체 코드와 지침을 제공합니다. 

![NVA, NSG, UDR을 사용하는 양방향 DMZ][1]

## <a name="environment-setup"></a>환경 설정
이 예에서는 다음을 포함하는 구독이 있습니다.

* 세 클라우드 서비스: "SecSvc001", "FrontEnd001", "BackEnd001"
* "SecNet", "FrontEnd", "BackEnd"의 세 서브넷을 포함하는 가상 네트워크 "CorpNetwork"
* 네트워크 가상 어플라이언스(이 예제의 경우 SecNet 서브넷에 연결된 방화벽)
* 응용 프로그램 웹 서버("IIS01")를 나타내는 Windows 서버
* 응용 프로그램 백 엔드 서버("AppVM01", "AppVM02")를 나타내는 두 Windows 서버
* DNS 서버("DNS01")를 나타내는 Windows Server

아래 참조 섹션에는 위에서 설명한 대부분의 환경을 빌드할 PowerShell 스크립트가 나와 있습니다. VM 및 가상 네트워크 구축은 예제 스크립트로 수행하지만 이 문서에서는 자세히 설명하지 않습니다.

환경을 구축하려면

1. 참조 섹션에 포함된 네트워크 구성 xml 파일 저장(지정된 시나리오에 맞게 이름, 위치, IP 주소로 업데이트됨)
2. 스크립트의 사용자 변수를 스크립트를 실행할 환경에 맞게 업데이트(구독, 서비스 이름 등)
3. PowerShell에서 스크립트 실행

**참고**: PowerShell 스크립트에 표시된 영역은 네트워크 구성 xml 파일에 표시된 영역과 일치해야 합니다.

스크립트를 성공적으로 실행하면 다음과 같은 사후 스크립트 단계를 수행할 수 있습니다.

1. 방화벽 규칙을 설정합니다(아래 방화벽 규칙 설명 섹션에서 설명).
2. 또는 참조 섹션의 두 스크립트를 사용하여 간단한 웹 응용 프로그램을 사용하는 웹 서버와 앱 서버를 설정하여 이 DMZ 구성을 이용한 테스트를 허용합니다.

스크립트가 성공적으로 실행되면 방화벽 규칙을 완료해야 합니다. 이 내용은 방화벽 규칙 섹션에서 설명합니다.

## <a name="user-defined-routing-udr"></a>UDR(사용자 정의 라우팅)
기본적으로 다음과 같은 시스템 경로는 다음으로 정의되어 있습니다.

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal은 항상 특정 네트워크의 VNet의 주소 접두사로 정의됩니다(즉, 각각의 특정 VNet이 정의된 방식에 따라 VNet에서 VNet으로 변경됨). 나머지 시스템 경로는 정적이며 기본값은 위와 같습니다.

우선 순위에 대해서는 경로가 LPM(Longest Prefix Match) 방법으로 처리되므로 표에서 가장 구체적인 경로는 지정된 대상 주소에 적용됩니다.

따라서 로컬 네트워크(10.0.0.0/16)로 전송할 트래픽(예: DNS01 서버로 보낼 트래픽의 경우 10.0.2.4)은 10.0.0.0/16 경로로 인해 VNet을 통해 대상으로 라우팅됩니다. 달리 말해, 10.0.2.4에서는 10.0.0.0/16 경로가 가장 구체적인 경로입니다. 10.0.0.0/8 및 0.0.0.0/0도 적용되지만 이보다 구체적이지 않기 때문에 이 트래픽에 영향을 미치지 않습니다. 따라서 10.0.2.4로 이동하는 트래픽은 로컬 VNet에 다음 홉이 있으며 대상으로 라우팅됩니다.

예를 들어 10.1.1.1로 보낼 트래픽의 경우 10.0.0.0/16이 적용되지 않지만 10.0.0.0/8이 가장 구체적이고 다음 홉이 Null이므로 이 트래픽은 삭제됩니다("블랙홀"). 

대상이 Null 접두사 또는 VNETLocal 접두사에 적용되지 않을 경우 가장 구체적이지 않은 경로인  0.0.0.0/0을 따르며 다음 홉인 인터넷으로 라우팅되고 Azure의 인터넷 에지를 넘어 라우팅됩니다.

경로 테이블에 두 개의 동일한 접두사가 있을 경우 경로 "source" 특성을 기준으로 하는 기본 설정 순서는 다음과 같습니다.

1. "VirtualAppliance" = 테이블에 수동으로 추가된 사용자 정의 경로
2. "VPNGateway" = 동적 네트워크 프로토콜에서 추가한 동적 경로(하이브리드 네트워크와 함께 사용할 경우 BGP). 이러한 경로는 동적 프로토콜에서 피어 네트워크의 변경 사항을 자동으로 반영하면서 시간에 따라 바뀔 수 있습니다.
3. "Default" = 위의 경로 테이블에 나와 있는 시스템 경로, 로컬 VNet, 정적 항목.

> [!NOTE]
> ExpressRoute 및 VPN 게이트웨이와 함께 UDR(사용자 정의 라우팅)를 사용하여 아웃바운드 및 인바운드 크로스-프레미스 트래픽을 NVA(네트워크 가상 어플라이언스)로 강제로 라우팅할 수 있습니다.
> 
> 

#### <a name="creating-the-local-routes"></a>로컬 경로 만들기
이 예제에서는 프런트 엔드 및 백 엔드 서브넷당 하나씩 두 개의 라우팅 테이블이 필요합니다. 각 테이블에는 지정된 서브넷에 적절한 정적 경로가 로드되어 있습니다. 이 예제의 목적상 각 테이블에 3개의 경로가 있습니다.

1. 로컬 서브넷 트래픽이 방화벽을 우회하도록 다음 홉이 정의되지 않은 로컬 서브넷 트래픽
2. 다음 홉이 방화벽으로 정의된 가상 네트워크 트래픽, 이 경우 로컬 VNet 트래픽이 직접 라우팅되도록 하는 기본 규칙을 재정의합니다.
3. 다음 홉이 방화벽으로 정의된 나머지 트래픽(0/0)

라우팅 테이블이 생성되면 해당 서브넷에 바인딩됩니다. 프런트 엔드 서브넷 라우팅 테이블이 생성되고 서브넷에 바인딩되면 다음과 같아집니다.

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


이 예제에서는 다음 명령을 사용하여 경로 테이블을 빌드하고 사용자 정의 경로를 만든 다음, 경로 테이블을 서브넷에 바인딩합니다. 아래에서 달러 기호로 시작하는 모든 항목(예: $BESubnet)은 본 문서의 참조 섹션의 스크립트 중 사용자 정의 변수입니다.

1. 가장 먼저 기본 라우팅 테이블을 만들어야 합니다. 이 코드 조각은 백 엔드 서브넷을 위한 테이블을 만드는 방식을 보여줍니다. 스크립트에서는 프런트 엔드 서브넷에 대해 해당 테이블도 만듭니다.
   
     New-AzureRouteTable -Name $BERouteTableName `
   
         -Location $DeploymentLocation `
         -Label "Route table for $BESubnet subnet"
2. 경로 테이블이 만들어지면 특정 사용자 정의 경로를 추가할 수 있습니다. 이 코드 조각에서는 모든 트래픽(0.0.0.0/0)이 가상 어플라이언스를 통해 라우팅됩니다(스크립트 앞부분에서 가상 어플라이언스를 만들었을 때 변수인 $VMIP[0]을 사용하여 할당된 IP 주소를 전달). 스크립트에서는 프런트 엔드 테이블에 해당 규칙도 만듭니다.
   
     Get-AzureRouteTable $BERouteTableName | `
   
         Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
         -NextHopType VirtualAppliance `
         -NextHopIpAddress $VMIP[0]
3. 위의 경로 항목은 기본 "0.0.0.0/0" 경로를 재정의하지만, VNet 내의 트래픽이 네트워크 가상 어플라이언스가 아닌 대상으로 직접 라우팅되도록 하는 기본 10.0.0.0/16 규칙이 존재합니다. 이 동작을 해결하려면 다음 규칙을 추가해야 합니다.
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
4. 이 시점에서 선택을 해야 합니다. 위의 두 경로에서는 모든 트래픽이 방화벽으로 라우팅되어 평가를 받으며, 단일 서브넷 내에 있는 트래픽도 마찬가지입니다. 서브넷 내에 있는 트래픽이 방화벽의 관여 없이 로컬로 라우팅되도록 하려는 경우에는 이렇게 해도 좋지만, 타사의 매우 구체적인 규칙을 추가할 수 있습니다. 이 경로는 로컬 서브넷을 가리키는 모든 주소가 직접 라우팅할 수 있음을 나타냅니다(NextHopType = VNETLocal).
   
        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
5. 마지막으로 사용자 정의 경로를 사용하여 만들고 채운 라우팅 테이블이 이제 서브넷에 바인딩됩니다. 스크립트에서는 프런트 엔드 경로 테이블도 프런트 엔드 서브넷에 바인딩됩니다. 백 엔드 서브넷의 바인딩 스크립트는 다음과 같습니다.
   
     Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
   
        -SubnetName $BESubnet `
        -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP 전달
UDR에는 IP 전달이라는 도우미 기능이 있습니다. IP 전달은 어플라이언스로 구체적 주소 지정이 되지 않은 트래픽을 수신한 다음 해당 트래픽을 최종 대상으로 전달할 수 있도록 하는 가상 어플라이언스의 설정입니다.

예를 들어 AppVM01에서 보낸 트래픽이 DNS01 서버에 요청할 경우 UDR이 이 요청을 방화벽으로 라우팅합니다. IP 전달을 사용하도록 설정한 경우 어플라이언스(10.0.0.4)에서 DNS01 대상(10.0.2.4)의 트래픽을 수락한 다음 최종 대상(10.0.2.4)으로 전달합니다. 방화벽에서 IP 전달을 사용하도록 설정하지 않은 경우에는 경로 테이블에 방화벽이 다음 홉으로 지정된 경우에도 어플라이언스에서 트래픽을 수락하지 않습니다. 

> [!IMPORTANT]
> 사용자 정의 라우팅과 연계하여 IP 전달을 사용하도록 설정하는 것이 중요합니다.
> 
> 

IP 전달 설정은 단일 명령이며 VM을 만들 때 지정할 수 있습니다. 이 예제의 흐름상 코드 조각은 스크립트 끝을 향하며 UDR 명령으로 그룹화되어 있습니다.

1. 가상 어플라이언스인 VM 인스턴스(이 경우 방화벽)를 호출하고 IP 전달을 사용하도록 설정합니다. 달러 기호로 시작하는 빨간색 항목(예: $VMName[0])은 이 문서의 참조 섹션에 있는 스크립트 중 사용자 정의 변수입니다. 대괄호 안의 0, [0]은 VM 어레이에서 첫 번째 VM을 나타냅니다. 예제 스크립트를 수정하지 않고 사용하려면 첫 번째 VM(VM 0)이 방화벽이어야 합니다.
   
     Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
   
        Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>네트워크 보안 그룹(NSG)
이 예제에서는 NSG 그룹을 빌드한 후 단일 규칙을 로드합니다. 그런 다음 이 그룹을 프런트 엔드 및 백 엔드 서브넷에만 바인딩합니다(SecNet 제외). 선언적으로 다음 규칙을 빌드합니다.

1. 인터넷에서 전체 VNet(모든 서브넷)으로 보내는 모든 트래픽(모든 포트)을 거부함

이 예제에서 NSG를 사용했지만 기본 목적은 수동 구성 시 실수가 발생할 경우를 대비하여 두 번째 방어 계층을 만드는 것입니다. 인터넷에서 프런트 엔드 또는 백 엔드 서브넷으로 보내는 모든 인바운드 트래픽을 차단하려고 합니다. 트래픽은 SecNet 서브넷에서 방화벽으로만 이동해야 합니다. 그런 다음 적절할 경우 프런트 엔드 또는 백 엔드 서브넷으로 이동할 수 있습니다. 또한 UDR 규칙을 적용할 경우 프런트 엔드 또는 백 엔드 서브넷으로 이동한 트래픽은 UDR 기능을 통해 방화벽으로 전달됩니다. 방화벽은 이러한 동작을 비대칭 흐름으로 보고 아웃바운드 트래픽을 삭제합니다. 따라서 프런트 엔드와 백 엔드 서브넷을 보호하는 세 보안 계층이 있습니다. 1) FrontEnd001 및 BackEnd001 클라우드 서비스에는 열린 끝점이 없으며, 2) NSG는 인터넷 트래픽을 거부하며, 3) 방화벽은 비대칭 트래픽을 삭제합니다.

이 예제의 네트워크 보안 그룹에서 흥미로운 점 하나는 아래와 같이 보안 서브넷을 포함한 전체 가상 네트워크에 대해 인터넷 트래픽을 거부하는 하나의 규칙만 포함되어 있다는 점입니다. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

하지만 NSG가 프런트 엔드 및 백 엔드 서브넷에만 바인딩되어 있기 때문에 이 규칙은 보안 서브넷에 대한 인바운드 트래픽에서 처리되지 않습니다. 그 결과 NSG 규칙에 VNet의 주소로 인터넷 트래픽을 거부하도록 설정되어 있는 경우에도 NSG가 보안 서브넷에 바인딩되지 않았기 때문에 트래픽이 보안 서브넷으로 이동합니다.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>방화벽 규칙
방화벽에서 전달 규칙을 만들어야 합니다. 방화벽은 모든 인바운드, 아웃바운드, 인트라-VNet 트래픽을 차단 또는 전달하기 때문에 많은 방화벽 규칙이 필요합니다. 또한 모든 인바운드 트래픽은 방화벽으로 처리하기 위해 다른 포트에 있는 보안 서비스 공용 IP 주소에 도달합니다. 나중에 재작업을 하지 않도록 서브넷 및 방화벽 규칙을 설정하기 전에 논리적 흐름을 다이어그램으로 그리는 것이 가장 좋습니다. 다음 그림은 이 예제의 방화벽 규칙을 논리적으로 나타낸 것입니다.

![방화벽 규칙에 대한 논리 뷰][2]

> [!NOTE]
> 관리 포트는 사용한 네트워크 가상 어플라이언스를 기준으로 달라집니다. 이 예제에서는 포트 22, 801, 807을 사용하는 Barracuda NextGen 방화벽을 참조합니다. 사용하는 장치 관리에 사용되는 정확한 포트를 찾으려면 어플라이언스 공급업체에서 제공한 설명서를 참조하세요.
> 
> 

### <a name="logical-rule-description"></a>논리적 규칙 설명
위의 논리적 다이어그램에서는 방화벽이 보안 서브넷에 유일한 리소스이기 때문에 해당 서브넷이 표시되어 있지 않습니다. 이 다이어그램은 방화벽 규칙과 이러한 방화벽 규칙이 실제 라우팅된 경로가 아닌 트래픽 흐름을 논리적으로 허용 또는 거부하는 방식을 보여줍니다. 또한 RDP 트래픽에 선택된 외부 포트는 높은 범위의 포트(8014 – 8026)이며 쉽게 판독할 수 있도록 로컬 IP 주소의 마지막 두 8진수와 정렬하기 위해 선택된 것입니다(예: 로컬 서버 주소 10.0.1.4는 외부 포트 8014와 연결됨). 하지만 이보다 높으면서 충돌하지 않는 포트도 사용할 수 있습니다.

이 예제에서는 7가지 유형의 규칙이 필요하며 아래에서 이러한 규칙 유형에 대해 설명합니다.

* 외부 규칙(인바운드 트래픽용):
  1. 방화벽 관리 규칙: 이 앱 리디렉션 규칙은 트래픽이 네트워크 가상 어플라이언스의 관리 포트로 전달되도록 허용합니다.
  2. RDP 규칙(각 Windows Server용): 이 4가지 규칙(각 서버당 하나)은 RDP를 통한 개별 서버의 관리를 허용합니다. 이 규칙은 사용하는 네트워크 가상 어플라이언스의 기능에 따라 하나의 규칙으로 묶을 수 있습니다.
  3. 응용 프로그램 트래픽 규칙: 프런트 엔드 웹 트래픽용 규칙과 백 엔드 트래픽(예: 웹 서버에서 데이터 계층으로)용 규칙의 2가지 응용 프로그램 트래픽 규칙이 있습니다. 이러한 규칙은 서버를 배치하는 네트워크 아키텍처 및 트래픽 흐름(트래픽이 흐르는 방향, 사용하는 포트)에 따라 다르게 구성합니다.
     * 첫 번째 규칙은 실제 응용 프로그램 트래픽이 응용 프로그램 서버에 도달하도록 허용합니다. 다른 규칙이 보안, 관리 등을 허용하지만 응용 프로그램 규칙은 외부 사용자 또는 서비스가 응용 프로그램에 액세스하도록 허용하는 규칙입니다. 이 예제에서는 포트 80에 단일 웹 서버가 있으므로 단일 방화벽 응용 프로그램 규칙이 인바운드 트래픽을 외부 IP로 리디렉션한 다음 웹 서버의 내부 IP 주소로 리디렉션합니다. 리디렉션된 트래픽 섹션은 내부 서버로 NAT 작업이 수행됩니다.
     * 두 번째 응용 프로그램 트래픽 규칙은 웹 서버가 임의 포트를 통해 AppVM01 서버(AppVM02 제외)와 통신하도록 하는 백 엔드 규칙입니다.
* 내부 규칙(인트라-VNet 트래픽용)
  1. 인터넷 아웃바운드 규칙: 이 규칙은 모든 네트워크의 트래픽이 선택한 네트워크로 전달되도록 허용합니다. 이 규칙은 일반적으로 방화벽에 이미 제공되어 있는 기본 규칙이지만 비활성화된 상태입니다. 이 예제에서는 이 규칙을 사용하도록 설정해야 합니다.
  2. DNS 규칙: 이 규칙은 DNS(포트 53) 트래픽만 DNS 서버로 전달되도록 허용합니다. 이 환경에서는 프런트 엔드에서 백 엔드로 이동하는 대부분의 트래픽이 차단되며 이 규칙은 임의의 로컬 서브넷의 DNS만 특별히 허용합니다.
  3. 서브넷 대 서브넷 규칙: 이 규칙은 백 엔드 서브넷의 모든 서버가 프런트 엔드 서브넷의 모든 서버에 연결할 수 있도록 허용합니다(그 반대의 경우는 거부).
* 유사 시 대기 규칙(위의 조건을 충족하지 않는 트래픽용):
  1. 모든 트래픽 거부 규칙: 이 규칙은 언제나 우선 순위 측면에서 마지막 규칙이 되어야 하며, 트래픽 흐름이 위의 규칙을 모두 충족하지 않을 경우 이 규칙에 의해 삭제됩니다. 이 규칙은 기본 규칙으로 일반적으로 활성화되어 있으며 수정이 필요하지 않는 경우가 대부분입니다.

> [!TIP]
> 두 번째 응용 프로그램 트래픽 규칙의 경우 이 예제에서는 쉬운 설명을 위해 모든 포트를 허용하지만, 실제 시나리오에서는 이 규칙의 공격 표면을 줄이기 위해 가장 구체적인 포트와 주소 범위를 사용해야 합니다.
> 
> 

<br />

> [!IMPORTANT]
> 위의 규칙을 만든 다음에는 각 규칙의 우선 순위를 검토하여 트래픽이 바라는 대로 허용 또는 거부되는지 확인하는 것이 중요합니다. 이 예제에서는 규칙이 우선 순위 순서로 나와 있습니다. 규칙 순서를 잘못 정할 경우 방화벽에서 잠기기 쉽습니다. 최소한, 방화벽 자체의 관리를 언제나 절대적으로 가장 높은 우선 순위 규칙으로 만들어야 합니다.
> 
> 

### <a name="rule-prerequisites"></a>규칙 필수 조건
방화벽을 실행하는 가상 컴퓨터는 공용 끝점이어야 한다는 한 가지 필수 조건을 충족해야 합니다. 방화벽이 트래픽을 처리하려면 적절한 공용 끝점을 열어야 합니다. 이 예제에는 1) 방화벽 및 방화벽 규칙을 관리하는 관리 트래픽, 2) Windows Server를 관리하는 RDP 트래픽, 3) 응용 프로그램 트래픽의 세 가지 유형의 트래픽이 있습니다. 이러한 트래픽 유형은 위 방화벽 규칙의 논리적 보기에서 상반부에 표시된 세 가지 주요 트래픽 유형입니다.

> [!IMPORTANT]
> 여기서는 **모든** 트래픽이 방화벽을 통해 수신된다는 점이 가장 중요합니다. 따라서 원격 데스크톱이 IIS01 서버에 연결하려면 이 데스크톱이 프런트 엔드 클라우드 서비스 및 프런트 엔드 서브넷에 있는 경우에도 포트 8014에서 방화벽으로 RDP를 지정한 다음 방화벽에서 RDP 요청을 IIS01 RDP 포트로 내부적으로 라우팅하도록 허용해야 합니다. IIS01에 대해 (포털이 볼 수 있는 한) 직접적인 RDP 경로가 없기 때문에 Azure 포털의 "연결" 단추는 작동하지 않습니다. 즉, 인터넷의 모든 연결이 보안 서비스 및 포트(예: secscv001.cloudapp.net:xxxx)로 연결되어야 함을 의미합니다. 위 다이어그램에서 외부 포트 매핑과 내부 IP 및 포트를 참조하세요.
> 
> 

끝점은 예제 스크립트와 같이 VM을 만들거나 빌드를 게시할 때 열 수 있으며 아래 코드 조각에 표시되어 있습니다. 달러 기호로 시작하는 항목(예: $VMName[$i])은 이 문서의 참조 섹션에 있는 스크립트 중 사용자 정의 변수입니다. 대괄호 안의 "$i", [$i]는 VM 어레이에서 특정 VM의 어레이 번호를 나타냅니다.

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

여기서는 변수 사용으로 인해 분명히 표시되어 있지 않지만 끝점은 보안 클라우드 서비스에서 **만** 열립니다. 이유는 방화벽에서 모든 인바운드 트래픽을 처리하도록 하기 위해서입니다(라우팅, NAT 수행, 삭제).

관리 클라이언트를 PC에 설치하여 방화벽을 관리하고 필요에 따라 구성을 만들어야 합니다. 장치를 관리하는 방법은 방화벽(또는 다른 NVA) 공급업체의 설명서를 참조하세요. 이 섹션 및 다음 섹션인 방화벽 규칙 만들기의 나머지 부분에서는 공급업체 관리 클라이언트를 통한 방화벽 자체(즉, Azure 포털 또는 PowerShell 아님) 구성에 대해 설명합니다.

이 예에 사용된 클라이언트 다운로드 및 Barracuda 연결에 대한 지침은 [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

방화벽에 로그인한 다음 방화벽 규칙을 만들기 전에 규칙을 더욱 쉽게 만들 수 있는 두 가지 필수 구성 요소 개체 클래스, 즉, 네트워크 및 서비스 개체가 있습니다.

이 예제에서는 3개의 명명된 네트워크 개체를 정의해야 합니다(프런트 엔드 서브넷 및 백 엔드 서브넷용 네트워크 개체, DNS 서버의 IP 주소용 네트워크 개체). 명명된 네트워크를 만들려면 가장 먼저 Barracuda NG Admin 클라이언트 대시보드를 열고 구성 탭을 탐색한 다음 작동 구성 섹션에서 규칙 집합을 클릭하고 방화벽 개체 메뉴에서 "네트워크"를 클릭한 다음 네트워크 편집 메뉴에서 새로 만들기를 클릭합니다. 이제 이름과 접두사를 추가하여 네트워크 개체를 만들 수 있습니다.

![프런트 엔드 네트워크 개체 만들기][3]

그러면 프런트 엔드 서브넷에 대해 명명된 네트워크가 만들어집니다. 백 엔드 서브넷에서도 유사한 개체를 만들어야 합니다. 이제 방화벽 규칙에서 서브넷을 이름으로 더욱 쉽게 참조할 수 있습니다.

DNS 서버 개체의 경우:

![DNS 서버 개체 만들기][4]

이 단일 IP 주소 참조는 문서 뒷부분의 DNS 규칙에서 활용합니다.

두 번째 필수 구성 요소 개체는 서비스 개체입니다. 이러한 개체는 각 서버에 대한 RDP 연결 포트를 나타냅니다. 기존 RDP 서비스 개체는 기본 RDP 포트인 3389에 바인딩되어 있으므로 새 서비스를 만들어 외부 포트(8014-8026)의 트래픽을 허용할 수 있습니다. 또한 새 포트를 기존 RDP 서비스에 추가할 수 있지만 여기서는 간단히 설명하기 위해 각 서버의 개별 규칙을 만듭니다. 서버의 새 RDP 규칙을 만들려면 가장 먼저 Barracuda NG Admin 클라이언트 대시보드를 열고 구성 탭을 탐색한 다음 작동 구성 섹션에서 규칙 집합을 클릭하고 방화벽 개체 메뉴에서 "서비스"를 클릭한 다음 서비스 목록을 아래로 스크롤하여 "RDP" 서비스를 선택합니다. 마우스 오른쪽 단추로 복사를 클릭하여 선택한 다음 마우스 오른쪽 단추로 붙여넣기를 클릭하여 선택합니다. 이제 RDP-Copy1 서비스 개체를 편집할 수 있습니다. 마우스 오른쪽 단추로 RDP-Copy1을 클릭하고 편집을 선택합니다. 그러면 다음과 같은 서비스 개체 편집 창이 나타납니다.

![기본 RDP 규칙 복사본][5]

그런 다음 값을 편집하여 특정 서버에 대한 RDP 서비스를 나타낼 수 있습니다. AppVM01의 경우 새 서비스 이름, 설명 및 그림 8 다이어그램에 사용된 외부 RDP 포트를 나타내기 위해 위의 기본 RDP 규칙을 수정해야 합니다. 포트는 RDP 기본값인 3389에서 이 특정 서버에 사용 중인 외부 포트로 변경됩니다. AppVM01의 경우 외부 포트는 8025입니다. 수정된 서비스는 다음과 같습니다.

![AppVM01 규칙][6]

이 프로세스를 반복하여 나머지 서버(AppVM02, DNS01, IIS01)에 대한 RDP 서비스를 만들어야 합니다. 이러한 서비스를 만들면 다음 섹션에서 규칙을 더욱 간단하고 분명하게 만들 수 있습니다.

> [!NOTE]
> 방화벽에는 두 가지 이유로 RDP 서비스가 필요하지 않습니다. 1) 첫 번째, 방화벽 VM이 Linux 기반 이미지이므로 VM 관리용 포트 22에 RDP 대신 SSH를 사용하며, 2) 관리 연결성을 허용하기 위해 아래에서 설명하는 첫 번째 관리 규칙에 포트 22 및 다른 두 개 관리 포트를 허용합니다.
> 
> 

### <a name="firewall-rules-creation"></a>방화벽 규칙 만들기
이 예제에는 세 가지 유형의 방화벽 규칙을 사용하며 각각 고유한 아이콘이 있습니다.

응용 프로그램 리디렉션 규칙: ![응용 프로그램 리디렉션 아이콘][7]

대상 NAT 규칙: ![대상 NAT 아이콘][8]

전달 규칙: ![전달 아이콘][9]

이러한 규칙에 대한 자세한 내용은 Barracuda 웹 사이트에 있습니다.

다음 규칙을 만들거나 기존 기본 규칙을 확인하려면 우선 Barracuda NG Admin 클라이언트 대시보드를 열고 구성 탭으로 이동한 후 작동 구성 섹션에서 규칙 집합을 클릭합니다. "Main Rules"라는 그리드에 방화벽에서 활성화되거나 비활성화된 기존 규칙이 표시됩니다. 이 그리드의 오른쪽 위 모서리에 작은 녹색 "+" 단추를 클릭하여 새 규칙을 만듭니다(참고: 방화벽은 변경에 대해 "잠금" 상태일 수 있으며 "잠금" 단추가 표시되고 규칙을 만들거나 편집할 수 없는 경우 이 단추를 클릭하여 규칙 집합을 "잠금 해제"하고 편집을 허용합니다). 기존 규칙을 편집하려는 경우 해당 규칙을 선택하고 마우스 오른쪽 단추를 클릭한 후 규칙 편집을 선택합니다.

규칙을 만들고/또는 수정한 다음에는 방화벽으로 푸시하고 활성화해야 합니다. 그렇지 않을 경우 변경 내용이 적용되지 않습니다. 푸시 및 활성화 프로세스는 자세한 규칙 설명 아래에서 자세히 설명합니다.

이 예제를 완료하는 데 필요한 각 규칙의 자세한 내용은 아래에서 설명합니다.

* **방화벽 관리 규칙**: 이 앱 리디렉션 규칙은 트래픽이 네트워크 가상 어플라이언스의 관리 포트로 전달되도록 허용합니다. 이 예제에서는 Barracuda NextGen 방화벽입니다. 관리 포트는 801, 807이며 22를 선택할 수도 있습니다. 외부 및 내부 포트가 동일합니다(즉, 포트 변환을 하지 않음). SETUP-MGMT-ACCESS가 기본 규칙이며 기본적으로 사용하도록 설정되어 있습니다(Barracuda NextGen Firewall 버전 6.1).
  
    ![방화벽 관리 규칙][10]

> [!TIP]
> 이 규칙에서 원본 주소 공간은 Any이며, 관리 IP 주소 범위가 알려진 경우 이 범위를 줄이면 관리 포트에 대한 공격 표면도 줄어듭니다.
> 
> 

* **RDP 규칙**: 이러한 대상 NAT 규칙은 RDP를 통한 개별 서버의 관리를 허용합니다.
  이 규칙을 만드는 데 필요한 4가지 중요 필드가 있습니다.
  
  1. 원본 - 어디에서나 RDP를 허용합니다. 원본 필드에 "Any" 참조를 사용합니다.
  2. 서비스 - 앞에서 만든 적절한 서비스 개체를 사용합니다. 이 경우 외부 포트인 "AppVM01 RDP"는 서버의 로컬 IP 주소 및 포트 3386(기본 RDP 포트)으로 리디렉션됩니다. 이 규칙은 특히 RDP에서 AppVM01에 액세스하기 위한 규칙입니다.
  3. 대상 - 정적 IP를 사용하는 경우 *방화벽의 로컬 포트*, "DCHP 1 Local IP" 또는 eth0이어야 합니다. 네트워크 어플라이언스에 여러 로컬 인터페이스가 있는 경우에는 서수(eth0, eth1, etc)가 달라질 수 있습니다. 이 포트는 방화벽에서 전송을 시작하는 포트입니다(수신 포트와 동일할 수 있음). 실제로 라우팅되는 대상은 대상 목록 필드에 있습니다.
  4. 리디렉션 - 이 섹션은 가상 어플라이언스에 최종적으로 이 트래픽을 리디렉션할 위치를 알려줍니다. 가장 간단한 리디렉션은 IP 및 포트(선택 사항)를 대상 목록 필드에 배치하는 것입니다. 사용한 포트가 없을 경우 인바운드 요청의 대상 포트가 사용됩니다(즉, 변환이 수행되지 않음). 포트가 지정된 경우에는 IP 주소와 함께 포트에 NAT 작업이 수행됩니다.
     
     ![방화벽 RDP 규칙][11]
     
     총 4개의 RDP 규칙을 만들어야 합니다. 
     
     | 규칙 이름 | 서버 | 부여 | 대상 목록 |
     | --- | --- | --- | --- |
     | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> 원본 및 서비스 필드 범위를 좁히면 공격 표면이 줄어듭니다. 기능을 허용하면서 가장 제한적인 범위를 사용해야 합니다.
> 
> 

* **응용 프로그램 트래픽 규칙**: 프런트 엔드 웹 트래픽용 규칙과 백 엔드 트래픽(예: 웹 서버에서 데이터 계층으로)용 규칙의 2가지 응용 프로그램 트래픽 규칙이 있습니다. 이러한 규칙은 서버를 배치하는 네트워크 아키텍처 및 트래픽 흐름(트래픽이 흐르는 방향, 사용하는 포트)에 따라 다릅니다.
  
    가장 먼저 웹 트래픽의 프런트 엔드 규칙에 대해 설명합니다.
  
    ![방화벽 웹 규칙][12]
  
    이 대상 NAT 규칙은 실제 응용 프로그램 트래픽이 응용 프로그램 서버에 도달하도록 허용합니다. 다른 규칙이 보안, 관리 등을 허용하지만 응용 프로그램 규칙은 외부 사용자 또는 서비스가 응용 프로그램에 액세스하도록 허용하는 규칙입니다. 이 예제에서는 포트 80에 단일 웹 서버가 있으므로 단일 방화벽 응용 프로그램 규칙이 인바운드 트래픽을 외부 IP로 리디렉션한 다음 웹 서버의 내부 IP 주소로 리디렉션합니다.
  
    **참고**: 대상 목록 필드에 할당된 포트가 없으므로 웹 서버의 리디렉션에 인바운드 포트 80(또는 선택한 서비스에 대해 443)을 사용합니다. 웹 서버가 포트 8080과 같은 다른 포트에서 수신 대기 중인 경우 포트 리디렉션도 허용할 수 있도록 대상 목록 필드를 10.0.1.4:8080으로 업데이트할 수 있습니다.
  
    다음 응용 프로그램 트래픽 규칙은 웹 서버가 임의 서비스를 통해 AppVM01 서버(AppVM02 제외)와 통신하도록 하는 백 엔드 규칙입니다.
  
    ![방화벽 AppVM01 규칙][13]
  
    이 전달 규칙은 프런트 엔드 서브넷의 모든 IIS 서버가 임의 프로토콜을 사용하여 웹 응용 프로그램에 필요한 데이터에 액세스하면서 임의 포트의 AppVM01(IP 주소 10.0.2.5)에 도달할 수 있도록 허용합니다.
  
    이 스크린샷에서는 대상 필드에서 대상으로 10.0.2.5를 나타내기 위해 "\<explicit-dest\>"가 사용되었습니다. 이 필드는 그림과 같이 명시적일 수도 있으며 명명된 네트워크 개체(DNS 서버의 필수 조건과 같은 방식)일 수도 있습니다. 사용할 방법은 방화벽 관리자가 선택할 수 있습니다. 명시적 대상으로 10.0.2.5를 추가하려면 \<explicit-dest\> 아래 첫 번째 빈 행을 두 번 클릭하고 표시되는 창에서 주소를 입력합니다.
  
    이 전달 규칙에서는 내부 트래픽이므로 NAT 작업이 필요하지 않으며 연결 방법을 "No SNAT"으로 설정할 수 있습니다.
  
    **참고**: 이 규칙의 원본 네트워크는 프런트 엔드 서브넷의 모든 리소스이며, 하나의 웹 서버 또는 알려진 특정 수의 웹 서버가 있는 경우는 전체 프런트 엔드 서브넷이 아닌 정확한 IP 주소에 해당하는 더욱 구체적인 네트워크 개체 리소스를 만들 수 있습니다.

> [!TIP]
> 이 규칙은 샘플 응용 프로그램을 쉽게 설정 및 사용할 수 있도록 "Any" 서비스를 사용합니다. 또한 단일 규칙에 ICMPv4(ping)를 허용합니다. 하지만 권장되는 방식은 아닙니다. 이 경계에서 공격 표면을 줄일 수 있도록 응용 프로그램이 가능한 한 포트와 프로토콜("서비스")를 최소한으로 좁혀야 합니다.
> 
> 

<br />

> [!TIP]
> 이 규칙이 사용 중인 명시적 대상 참조를 나타내지만 방화벽 구성에서 일관적 방식을 사용해야 합니다. 손쉬운 판독과 지원을 위해 전체적으로 명명된 네트워크 개체를 사용하는 것이 좋습니다. 여기에서는 대체 참조 방법을 보여주기 위해서만 explicit-dest를 사용했으며 일반적으로 권장되지 않습니다(특히 복잡한 구성의 경우).
> 
> 

* **인터넷 아웃바운드 규칙**: 이 전달 규칙은 모든 원본 네트워크의 트래픽이 선택한 대상 네트워크로 전달되도록 허용합니다. 이 규칙은 일반적으로 Barracuda NextGen 방화벽에 이미 제공되어 있는 기본 규칙이지만 비활성화된 상태입니다. 이 규칙을 마우스 오른쪽 단추로 클릭하면 규칙 활성화 명령에 액세스할 수 있습니다. 여기에 표시된 규칙은 본 문서의 필수 조건 섹션에서 참조로 만든 두 개의 로컬 서브넷을 이 규칙의 원본 속성에 추가하여 수정했습니다.
  
    ![방화벽 아웃바운드 규칙][14]
* **DNS 규칙**: 이 전달 규칙은 DNS(포트 53) 트래픽만 DNS 서버로 전달되도록 허용합니다. 이 환경에서는 프런트 엔드에서 백 엔드로 이동하는 대부분의 트래픽이 차단되며 이 규칙은 특히 DNS를 허용합니다.
  
    ![방화벽 DNS 규칙][15]
  
    **참고**: 이 스크린샷에는 연결 방법이 포함되어 있습니다. 이 규칙은 내부 IP-내부 IP 주소 트래픽용이므로 NAT 작업이 필요하지 않으며 이 연결 방법은 이 전달 규칙에 대해 "No SNAT"로 설정되었습니다.
* **서브넷 대 서브넷 규칙**: 이 규칙은 활성화된 기본 규칙이며 백 엔드 서브넷의 모든 서버가 프런트 엔드 서브넷의 모든 서버에 연결할 수 있도록 허용하도록 수정되었습니다. 이 규칙은 모든 내부 트래픽이므로 연결 방법을 No SNAT로 설정할 수 있습니다.
  
    ![방화벽 인트라-VNet 규칙][16]
  
    **참고**: 양방향 확인란이 선택되어 있지 않으며(대부분의 규칙에서도 선택하지 않음) 이 규칙을 "단방향"으로 만듭니다. 백 엔드 서브넷에서 프런트 엔드 네트워크로 연결을 시작할 수 있지만 그 반대는 허용되지 않습니다. 이 확인란을 선택할 경우 이 규칙에서 양방향 트래픽이 허용되며 논리적 다이어그램에 따라 바람직하지 않습니다.
* **모든 트래픽 거부 규칙**: 이 규칙은 언제나 우선 순위 측면에서 마지막 규칙이 되어야 하며, 트래픽 흐름이 위의 규칙을 모두 충족하지 않을 경우 이 규칙에 의해 삭제됩니다. 이 규칙은 기본 규칙으로 일반적으로 활성화되어 있으며 수정이 필요하지 않는 경우가 대부분입니다. 
  
    ![방화벽 거부 규칙][17]

> [!IMPORTANT]
> 위의 규칙을 만든 다음에는 각 규칙의 우선 순위를 검토하여 트래픽이 바라는 대로 허용 또는 거부되는지 확인하는 것이 중요합니다. 이 예제에서는 Barracuda Management Client의 전달 규칙의 주 표에 규칙이 올바른 순서로 표시되어 있습니다.
> 
> 

## <a name="rule-activation"></a>규칙 활성화
규칙 집합을 논리적 다이어그램 사양에 맞게 수정한 다음에는 규칙 집합을 방화벽에 업로드하고 활성화해야 합니다.

![방화벽 규칙 활성화][18]

관리 클라이언트의 오른쪽 위 모서리에 단추 클러스터가 있습니다. "변경 내용 보내기" 단추를 클릭하여 규칙을 방화벽으로 전송한 다음 "활성화" 단추를 클릭합니다.

방화벽 규칙 집합을 활성화하면 이 예제 환경 빌드가 완료된 것입니다.

## <a name="traffic-scenarios"></a>트래픽 시나리오
> [!IMPORTANT]
> **모든** 트래픽이 방화벽을 통해 수신된다는 점이 가장 중요합니다. 따라서 원격 데스크톱이 IIS01 서버에 연결하려면 이 데스크톱이 프런트 엔드 클라우드 서비스 및 프런트 엔드 서브넷에 있는 경우에도 포트 8014에서 방화벽으로 RDP를 지정한 다음 방화벽에서 RDP 요청을 IIS01 RDP 포트로 내부적으로 라우팅하도록 허용해야 합니다. IIS01에 대해 (포털이 볼 수 있는 한) 직접적인 RDP 경로가 없기 때문에 Azure 포털의 "연결" 단추는 작동하지 않습니다. 즉, 인터넷에서 연결할 경우 모두 보안 서비스 및 포트(예: secscv001.cloudapp.net:xxxx)로 연결됨을 의미합니다.
> 
> 

이 시나리오에서는 다음 방화벽 규칙을 적용해야 합니다.

1. 방화벽 관리
2. RDP to IIS01
3. RDP to DNS01
4. RDP to AppVM01
5. RDP to AppVM02
6. 웹에 대한 앱 트래픽
7. AppVM01에 대한 앱 트래픽
8. 인터넷 아웃바운드
9. DNS01에 대한 프런트 엔드
10. 인트라-서브넷 트래픽(프런트 엔드에 대한 백 엔드만)
11. 모두 거부

실제 방화벽 규칙 집합은 이 이외에 많은 규칙이 있으며, 특정 방화벽의 규칙은 여기에 나열된 우선 순위와 다른 우선 순위를 가집니다. 이 목록 및 관련 번호는 이 11개 규칙과 이러한 규칙의 상대적 우선 순위 간 관련성을 제공하기 위한 것입니다. 달리 말해, 실제 방화벽에서 "RDP to IIS01"은 5번 규칙이 될 수 있으며 "방화벽 관리" 규칙 아래에 있고 "RDP to DNS01" 규칙 위에 있는 한 이 목록의 의도에 따라 정렬됩니다. 또한 이 목록은 아래 시나리오에서 간단하게 나타내기 위해 활용됩니다. 예: "FW Rule 9 (DNS)". 또한 트래픽 시나리오가 RDP와 관련이 없을 경우 간단하게 나타내기 위해 4개의 RDP 규칙을 모두 "RDP 규칙"이라고 합니다.

또한 프런트 엔드 및 백 엔드 서브넷에서 인바운드 인터넷 트래픽의 네트워크 보안 그룹이 적용되어 있다는 점을 상기하십시오.

#### <a name="allowed-internet-to-web-server"></a>(허용) 인터넷에서 웹 서버
1. 인터넷 사용자가 SecSvc001.CloudApp.Net에서 HTTP 페이지를 요청합니다(인터넷 클라우드 서비스).
2. 클라우드 서비스가 포트 80에서 열린 끝점을 통해 10.0.0.4:80의 방화벽 인터페이스로 트래픽을 전달합니다.
3. 보안 서브넷에 할당된 NSG가 없으므로 시스템 NSG 규칙에서 방화벽에 대한 트래픽을 허용합니다.
4. 트래픽이 방화벽의 내부 IP 주소에 도달합니다(10.0.1.4).
5. 방화벽에서 규칙 처리를 시작합니다.
   1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   3. FW 규칙 6(앱: 웹)이 적용되고 트래픽이 허용되며 방화벽에서 10.0.1.4 (IIS01)로 NAT 작업을 수행합니다.
6. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(인터넷 차단)이 적용되지 않고(이 트래픽은 방화벽에서 NAT 작업을 수행하므로 이제 원본 주소가 보안 서브넷에 있는 방화벽이며 프런트 엔드 서브넷 NSG에서 "로컬" 트래픽으로 인식하므로 허용됨) 다음 규칙으로 이동합니다.
   2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
7. IIS01이 웹 트래픽을 수신 대기하고 이 요청을 수신하며 요청 처리를 시작합니다.
8. IIS01이 백 엔드 서브넷에서 AppVM01에 대한 FTP 세션을 시작합니다.
9. 프런트 엔드 서브넷의 UDR 경로에서 방화벽이 다음 홉이 됩니다.
10. 프런트 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
11. 방화벽에서 규칙 처리를 시작합니다.
    1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
    3. FW 규칙 6(앱: 웹)이 적용되지 않고 다음 규칙으로 이동합니다.
    4. FW 규칙 7(앱: 백 엔드)이 적용되지 않고 트래픽이 허용됩니다. 방화벽은 트래픽을 10.0.2.5(AppVM01)로 전달합니다.
12. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
    1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
13. AppVM01에서 요청을 수신하며 세션을 시작하고 응답합니다.
14. 백 엔드 서브넷의 UDR 경로에서 방화벽이 다음 홉이 됩니다.
15. 백 엔드 서브넷에 아웃바운드 NSG 규칙이 없으므로 응답이 허용됩니다.
16. 이 경우 연결된 세션에서 트래픽이 반환되므로 방화벽이 응답을 웹 서버(IIS01)로 전달합니다.
17. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
    1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
18. IIS 서버에서 응답을 수신하고 AppVM01로 트랜잭션을 완료한 다음 HTTP 응답 빌드를 완료하면 이 HTTP 응답이 요청자에게 전송됩니다.
19. 프런트 엔드 서브넷에 아웃바운드 NSG 규칙이 없으므로 응답이 허용됩니다.
20. 이 HTTP 응답은 방화벽에서 수락하여 연결된 NAT 세션에 대한 응답이므로 응답이 방화벽에 도달합니다.
21. 그런 다음 방화벽에서 인터넷 사용자에게 응답을 다시 리디렉션합니다.
22. 응답이 허용되는 프런트 엔드 서브넷에 아웃바운드 NSG 규칙 또는 UDR 홉이 없으므로 인터넷 사용자가 요청된 웹 페이지를 수신합니다.

#### <a name="allowed-internet-rdp-to-backend"></a>(허용) 인터넷 RDP에서 백 엔드
1. 인터넷의 서버 관리자가 SecSvc001.CloudApp.Net:8025를 통해 AppVM01에 대한 RDP 세션을 요청합니다. 여기서 8025는 사용자가 "RDP to AppVM01" 방화벽 규칙에 할당한 포트 번호입니다.
2. 클라우드 서비스가 포트 8025에서 열린 끝점을 통해 10.0.0.4:8025의 방화벽 인터페이스로 트래픽을 전달합니다.
3. 보안 서브넷에 할당된 NSG가 없으므로 시스템 NSG 규칙에서 방화벽에 대한 트래픽을 허용합니다.
4. 방화벽에서 규칙 처리를 시작합니다.
   1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. FW 규칙 2(RDP IIS)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. FW 규칙 3(RDP DNS01)가 적용되지 않고 다음 규칙으로 이동합니다.
   4. FW 규칙 4(RDP AppVM01)가 적용되고 트래픽이 허용됩니다. 방화벽은 10.0.2.5:3386(AppVM01의 RDP 포트)으로 NAT를 수행합니다.
5. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(인터넷 차단)이 적용되지 않고(이 트래픽은 방화벽에서 NAT 작업을 수행하므로 이제 원본 주소가 보안 서브넷에 있는 방화벽이며 백 엔드 서브넷 NSG에서 "로컬" 트래픽으로 인식하므로 허용됨) 다음 규칙으로 이동합니다.
   2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
6. AppVM01이 RDP 트래픽을 수신 대기하다 응답합니다.
7. 아웃바운드 NSG 규칙이 없으므로 기본 규칙이 적용되고 반환 트래픽이 허용됩니다.
8. UDR에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 라우팅합니다.
9. 이 경우 연결된 세션에서 트래픽이 반환되므로 방화벽이 응답을 인터넷 사용자에게 전달합니다.
10. RDP 세션이 사용하도록 설정됩니다.
11. AppVM01에서 사용자 이름과 암호를 묻습니다.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(허용) DNS 서버에서 웹 서버 DNS 조회
1. 웹 서버인 IIS01은 www.data.gov에서 데이터 피드를 요구하지만 주소를 확인해야 합니다.
2. VNet에 대한 네트워크 구성에서 DNS01(백 엔드 서브넷에서 10.0.2.4)을 주 DNS 서버로 나열하며 IIS01은 DNS01로 DNS 요청을 보냅니다.
3. UDR에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 라우팅합니다.
4. 프런트 엔드 서브넷에 바인딩된 아웃바운드 NSG 규칙이 없으며 트래픽이 허용됩니다.
5. 방화벽에서 규칙 처리를 시작합니다.
   1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   3. FW 규칙 6-7(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. FW 규칙 8(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   5. FW 규칙 9(DNS)이 적용되지 않고 트래픽이 허용됩니다. 방화벽은 트래픽을 10.0.2.4(DNS01)로 전달합니다.
6. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
7. DNS 서버에서 요청을 수신합니다.
8. DNS 서버에는 캐시된 주소가 없고 인터넷에서 루트 DNS 서버를 요청합니다.
9. UDR에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 라우팅합니다.
10. 백 엔드 서브넷에 아웃바운드 NSG 규칙이 없고 트래픽이 허용됩니다.
11. 방화벽에서 규칙 처리를 시작합니다.
    1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
    3. FW 규칙 6-7(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
    4. FW 규칙 8(인터넷에 연결)이 적용되고 트래픽이 허용됩니다. 세션은 인터넷에서 루트 DNS 서버에 대한 SNAT입니다.
12. 인터넷 DNS 서버가 응답하고, 이 세션은 방화벽에서 시작되었으므로 방화벽에서 응답을 수락합니다.
13. 연결된 세션이므로 방화벽에서 시작 서버인 DNS01로 응답을 전달합니다.
14. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
    1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
15. DNS 서버가 응답을 수신 및 캐시한 다음 IIS01에 대한 초기 요청에 다시 응답합니다.
16. 백 엔드 서브넷의 UDR 경로에서 방화벽이 다음 홉이 됩니다.
17. 백 엔드 서브넷에 아웃바운드 NSG 규칙이 없고 트래픽이 허용됩니다.
18. 방화벽에서 연결된 세션이며 방화벽에서 IIS 서버로 다시 응답을 전달합니다.
19. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
    1. 백 엔드 서브넷에서 프런트 엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
    2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙에서 이 트래픽을 허용하므로 트래픽이 허용됩니다.
20. IIS01이 DNS01에서 응답을 수신합니다.

#### <a name="allowed-backend-server-to-frontend-server"></a>(허용) 백 엔드 서버에서 프런트 엔드 서버
1. RDP를 통해 AppVM02에 로그인한 관리자가 Windows 파일 탐색기를 통해 IIS01 서버로부터 직접 파일을 요청합니다.
2. 백 엔드 서브넷의 UDR 경로에서 방화벽이 다음 홉이 됩니다.
3. 백 엔드 서브넷에 아웃바운드 NSG 규칙이 없으므로 응답이 허용됩니다.
4. 방화벽에서 규칙 처리를 시작합니다.
   1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   3. FW 규칙 6-7(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. FW 규칙 8(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   5. FW 규칙 9(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   6. FW 규칙 10(인트라-서브넷)이 적용되고 트래픽이 허용됩니다. 방화벽은 트래픽을 10.0.1.4(IIS01)로 전달합니다.
5. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
6. 올바른 인증 및 권한이 있는 경우 IIS01에서 요청을 수락하고 응답합니다.
7. 프런트 엔드 서브넷의 UDR 경로에서 방화벽이 다음 홉이 됩니다.
8. 프런트 엔드 서브넷에 아웃바운드 NSG 규칙이 없으므로 응답이 허용됩니다.
9. 방화벽의 기존 세션이므로 이 응답이 허용되고 방화벽에서 AppVM02로 응답을 반환합니다.
10. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
    1. NSG 규칙 1(인터넷 차단)이 적용되지 않고 다음 규칙으로 이동합니다.
    2. 기본 NSG 규칙에서는 서브넷 대 서브넷 트래픽을 허용하며 트래픽이 허용되고 정지 NSG 규칙이 처리됩니다.
11. AppVM02에서 응답을 수신합니다.

#### <a name="denied-internet-direct-to-web-server"></a>(거부) 인터넷에서 웹 서버로 직접
1. 인터넷 사용자가 FrontEnd001.CloudApp.Net 서비스를 통해 웹 서버 IIS01에 액세스하려고 합니다.
2. HTTP 트래픽을 위해 열린 끝점이 없으므로 클라우드 서비스를 통과하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 프런트 엔드 서브넷의 NSG(인터넷 차단)가 이 트래픽을 차단합니다.
4. 마지막으로 프런트 엔드 서브넷 UDR 경로가 IIS01에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 전송하며, 방화벽은 이를 비대칭 트래픽으로 인식하고 아웃바운드 응답을 삭제합니다. 따라서 클라우드 서비스를 통해 인터넷과 IIS01 간 3개 이상의 독립 계층이 있으므로 무단/부적절한 액세스를 방지합니다.

#### <a name="denied-internet-to-backend-server"></a>(거부) 인터넷에서 백엔드 서버
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 AppVM01에서 파일에 액세스하려고 합니다.
2. 파일 공유를 위해 열린 끝점이 없으므로 클라우드 서비스를 전달하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 NSG(인터넷 차단)가 이 트래픽을 차단합니다.
4. 마지막으로 UDR 경로가 AppVM01에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 전송하며, 방화벽은 이를 비대칭 트래픽으로 인식하고 아웃바운드 응답을 삭제합니다. 따라서 클라우드 서비스를 통해 인터넷과 AppVM01 간 3개 이상의 독립 계층이 있으므로 무단/부적절한 액세스를 방지합니다.

#### <a name="denied-frontend-server-to-backend-server"></a>(거부) 프런트 엔드 서버에서 백 엔드 서버
1. IIS01이 손상되어 백 엔드 서브넷 서버를 스캔하려는 악성 코드가 실행 중인 경우를 가정해 보겠습니다.
2. 프런트 엔드 서브넷 UDR 경로가 IIS01에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 보냅니다. 이 작업은 손상된 VM에서 변경할 수 있는 것이 아닙니다.
3. 방화벽에서 해당 트래픽을 처리하고, AppVM01에 대한 요청이거나 DNS 조회를 위한 DNS 서버에 대한 요청일 경우 방화벽에서 해당 트래픽을 허용할 수 있습니다(FW 규칙 7 및 9로 인해). 기타 트래픽은 FW 규칙 11(모두 거부)에서 차단합니다.
4. 방화벽에 고급 위협 감지를 사용하도록 설정한 경우(본 문서에서 다루지 않음. 특정 네트워크 어플라이언스의 고급 위협 기능에 대해서는 공급업체 설명서 참조) 트래픽에 고급 위협 규칙임을 나타내는 알려진 서명 또는 패턴이 포함되어 있으면 본 문서에서 설명하는 기본 전달 규칙이 허용하는 트래픽도 차단될 수 있습니다.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(거부) DNS 서버에서 인터넷 DNS 조회
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 DNS01에서 내부 DNS 레코드를 조회하려고 합니다. 
2. DNS 트래픽을 위해 열린 끝점이 없으므로 클라우드 서비스를 통과하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 프런트 엔드 서브넷의 NSG 규칙(인터넷 차단)에서 이 트래픽을 차단합니다.
4. 마지막으로 백 엔드 서브넷 UDR 경로가 DNS01에서 다음 홉인 방화벽으로 아웃바운드 트래픽을 전송하며, 방화벽은 이를 비대칭 트래픽으로 인식하고 아웃바운드 응답을 삭제합니다. 따라서 클라우드 서비스를 통해 인터넷과 DNS01 간 3개 이상의 독립 계층이 있으므로 무단/부적절한 액세스를 방지합니다.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(거부) 방화벽을 통해 인터넷에서 SQL 액세스
1. 인터넷 사용자가 SecSvc001.CloudApp.Net에서 SQL 데이터를 요청합니다(인터넷 클라우드 서비스).
2. SQL을 위해 열린 끝점이 없으므로 클라우드 서비스를 전달하지 않고 방화벽에 도달하지 않습니다.
3. 어떤 이유로 SQL 끝점이 열린 경우 방화벽에서 규칙 처리를 시작합니다.
   1. FW 규칙 1(FW 관리)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. FW 규칙 2-5(RDP 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   3. FW 규칙 6-7(응용 프로그램 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. FW 규칙 8(앱 규칙)이 적용되지 않고 다음 규칙으로 이동합니다.
   5. FW 규칙 9(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   6. FW 규칙 10(인트라-서브넷)이 적용되지 않고 다음 규칙으로 이동합니다.
   7. FW 규칙 11(모두 거부)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.

## <a name="references"></a>참조
### <a name="main-script-and-network-config"></a>기본 스크립트 및 네트워크 구성
PowerShell 스크립트 파일에 전체 스크립트를 저장합니다. 네트워크 구성을 "NetworkConf2.xml"이라는 파일에 저장합니다.
필요에 따라 사용자 정의 변수를 수정합니다. 스크립트를 실행한 다음 위의 방화벽 규칙 설정 지침을 따릅니다.

#### <a name="full-script"></a>전체 스크립트
이 스크립트는 사용자 정의 변수를 기반으로 합니다.

1. Azure 구독에 연결
2. 새 저장소 계정 만들기
3. 네트워크 구성 파일에 정의된 대로 새 VNet 및 3개 서브넷을 만듭니다.
4. 5개의 가상 머신(1개 방화벽, 4개 Windows Server VM)을 빌드합니다.
5. 다음을 포함하여 UDR을 구성합니다.
   1. 2개의 새 경로 테이블 만들기
   2. 테이블에 경로를 추가합니다.
   3. 적절한 서브넷에 테이블을 바인딩합니다.
6. NVA에서 IP 전달을 사용하도록 설정합니다.
7. 다음을 포함하여 NSG를 구성합니다.
   1. NSG 만들기
   2. 규칙 추가
   3. 적절한 서브넷에 대해 NSG 바인딩

이 PowerShell 스크립트를 인터넷 연결된 PC 또는 서버에서 로컬로 실행해야 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하는 경우 PowerShell에서 경고 또는 기타 정보 메시지가 표시될 수 있습니다. 빨간색 오류 메시지만 심각한 문제입니다.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>네트워크 구성 파일
업데이트된 위치로 이 xml 파일을 저장하고 이 파일에 대한 링크를 위의 스크립트에 있는 $NetworkConfigFile 변수에 추가합니다.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>샘플 응용 프로그램 스크립트
이에 대한 샘플 응용 프로그램 및 기타 DMZ 예제를 설치하는 방법은 다음 링크를 통해 제공됩니다. [샘플 응용 프로그램 스크립트][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "NVA, NSG, UDR을 사용하는 양방향 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "방화벽 규칙에 대한 논리적 보기"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "프런트 엔드 네트워크 개체 만들기"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "DNS 서버 개체 만들기"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "기본 RDP 규칙의 복사본"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 규칙"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "응용 프로그램 리디렉션 아이콘"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "대상 NAT 아이콘"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "전달 아이콘"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "방화벽 관리 규칙"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "방화벽 RDP 규칙"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "방화벽 웹 규칙"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "방화벽 AppVM01 규칙"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "방화벽 아웃바운드 규칙"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "방화벽 DNS 규칙"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "VNet 내 방화벽 규칙"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "방화벽 거부 규칙"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "방화벽 규칙 활성화"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
