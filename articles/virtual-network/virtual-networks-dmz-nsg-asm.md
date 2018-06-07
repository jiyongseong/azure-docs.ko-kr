---
title: Azure DMZ 예제 – NSG를 사용하여 간단한 DMZ 빌드| Microsoft Docs
description: NSG(네트워크 보안 그룹)를 사용하여 DMZ 빌드
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>예제 1 – 클래식 PowerShell로 NSG를 사용하여 간단한 DMZ 빌드
[보안 경계 모범 사례 페이지로 돌아가기][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager 템플릿](virtual-networks-dmz-nsg.md)
> * [클래식 - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

이 예에서는 4개의 Windows Server 및 네트워크 보안 그룹이 포함된 기본 DMZ를 만듭니다. 이 예제는 각 단계를 자세히 이해할 수 있도록 각각의 관련 PowerShell 명령을 설명합니다. 트래픽 시나리오 섹션에서는 DMZ에서 방어 계층을 진행하는 방법에 대한 심층적인 단계별 설명도 제공합니다. 마지막으로, 참조 섹션에서는 다양한 시나리오를 사용하여 테스트 및 실험하기 위한 환경을 구축하는 전체 코드와 지침을 제공합니다. 

![NSG와 인바운드 DMZ][1]

## <a name="environment-description"></a>환경 설명
이 예에서 구독은 다음 리소스를 포함합니다.

* 두 클라우드 서비스: "FrontEnd001" 및 "BackEnd001"
* "FrontEnd" 및 "BackEnd"의 두 서브넷을 포함하는 Virtual Network "CorpNetwork"
* 서브넷 모두에 적용되는 네트워크 보안 그룹
* 응용 프로그램 웹 서버("IIS01")를 나타내는 Windows 서버
* 응용 프로그램 백 엔드 서버("AppVM01", "AppVM02")를 나타내는 두 Windows 서버
* DNS 서버("DNS01")를 나타내는 Windows 서버

참조 섹션에는 이 예제에서 설명한 대부분의 환경을 빌드하는 PowerShell 스크립트가 있습니다. VM 및 Virtual Network 구축은 예제 스크립트로 수행하지만 이 문서에서는 자세히 설명하지 않습니다. 

환경을 구축하려면

1. 참조 섹션에 포함된 네트워크 구성 xml 파일 저장(지정된 시나리오에 맞게 이름, 위치, IP 주소로 업데이트됨)
2. 스크립트의 사용자 변수를 스크립트를 실행할 환경에 맞게 업데이트(구독, 서비스 이름 등)
3. PowerShell에서 스크립트 실행

>[!Note]
>PowerShell 스크립트에 표시된 영역은 네트워크 구성 xml 파일에 표시된 영역과 일치해야 합니다.
>
>

스크립트가 성공적으로 실행되면 추가로 선택적 단계를 수행할 수 있는데, 참조 섹션의 두 스크립트를 사용하여 간단한 웹 응용 프로그램을 사용하는 웹 서버와 앱 서버를 설정하여 이 DMZ 구성을 이용한 테스트를 허용합니다.

다음 섹션에서는 네트워크 보안 그룹을 설명하고 PowerShell 스크립트의 핵심 줄을 살펴보는 방법으로 이 예제의 작동 방식을 자세히 설명합니다.

## <a name="network-security-groups-nsg"></a>네트워크 보안 그룹(NSG)
이 예제에서는 NSG 그룹을 빌드한 후 6개의 규칙을 로드합니다. 

> [!TIP]
> 일반적으로 특정 "허용" 규칙을 먼저 만든 후 보다 일반적인 "거부" 규칙을 만들어야 합니다. 할당된 우선순위에 따라 먼저 평가할 규칙이 결정됩니다. 특정 규칙에 적용할 트래픽이 발견되면 규칙을 더 이상 평가하지 않습니다. NSG 규칙은 인바운드 또는 아웃바운드 방향으로 적용할 수 있습니다(서브넷 관점에서).
> 
> 

선언적으로 인바운드 트래픽에 대해 다음 규칙이 빌드됩니다.

1. 내부 DNS 트래픽(포트 53) 허용됨
2. 인터넷에서 모든 VM으로 RDP 트래픽(포트 3389) 허용됨
3. 인터넷에서 웹 서버(IIS01)로 HTTP 트래픽(포트 80) 허용됨
4. IIS01에서 AppVM1로 모든 트래픽(모든 포트) 허용됨
5. 인터넷에서 전체 VNet(두 서브넷)으로 모든 트래픽(모든 포트) 거부됨
6. 프런트 엔드 서브넷에서 백 엔드 서브넷으로 모든 트래픽(모든 포트) 거부됨

각 서브넷에 바인딩된 이러한 규칙에서는 HTTP 요청이 인터넷에서 웹 서버로 인바운드되는 경우 규칙 3(허용) 및 5(거부)가 모두 적용되지만 규칙 3이 우선순위가 높기 때문에 규칙 3만 적용되고 규칙 5는 진행되지 않습니다. 따라서 웹 서버에 대해 HTTP 요청이 허용됩니다. 동일한 트래픽이 DNS01 서버에 도달하려고 시도하는 경우 규칙 5(거부)는 가장 먼저 적용되며 트래픽을 서버에 전달할 수 없습니다. 규칙 6(거부)은 프런트 엔드 서브넷이 백 엔드 서브넷과 통신하는 것을 차단(규칙 1 및 4에서 허용된 트래픽 제외)하여 공격자가 프런트 엔드에서 웹 응용 프로그램을 손상시키는 경우 공격자의 "보호된" 백 엔드 네트워크(AppVM01 서버에서 노출되는 리소스만)에 대한 액세스가 제한되므로 이 규칙 집합은 백 엔드 네트워크를 보호합니다.

인터넷으로 트래픽을 허용하는 기본 아웃바운드 규칙이 있습니다. 이 예에서는 아웃바운드 트래픽을 허용하고 아웃바운드 규칙을 수정하지 않습니다. 양방향에서 트래픽을 잠그려면 사용자 정의 라우팅이 필요하며 [보안 경계 모범 사례 페이지][HOME]의 "예제 3"에서 이에 대해 살펴봅니다.

다음과 같이 각 규칙을 보다 상세히 설명합니다.(**참고**: 달러 기호(예: $NSGName)로 시작하는 다음 목록에서 모든 항목은 이 문서의 참조 섹션에서 스크립트의 사용자 정의 변수입니다.)

1. 먼저 규칙을 포함하도록 네트워크 보안 그룹을 빌드해야 합니다.

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. 이 예의 첫 번째 규칙에서는 백 엔드 서브넷에서 DNS 서버에 대해 모든 내부 네트워크 간 DNS 트래픽을 허용합니다. 규칙은 몇 가지 중요한 매개 변수를 포함합니다.
   
   * "Type"은 이 규칙이 적용되는 트래픽 흐름의 방향을 나타냅니다. 방향은 서브넷 또는 Virtual Machine의 관점에서 옵니다(이 NSG가 바인딩되는 위치에 따라). 따라서 Type이 "Inbound"이고 트래픽이 서브넷에 들어가는 경우 규칙이 적용되고 서브넷에서 나가는 트래픽에는 이 규칙이 적용되지 않습니다.
   * "Priority"는 트래픽 흐름이 평가되는 순서를 설정합니다. 번호가 낮을수록 우선순위가 높습니다. 특정 트래픽 흐름에 규칙이 적용되는 경우 더 이상 규칙이 처리되지 않습니다. 따라서 우선순위 1인 규칙에서는 트래픽을 허용하고 우선순위 2인 규칙에서는 트래픽을 거부하고 두 규칙 모두 트래픽에 적용된다면 트래픽 흐름이 허용됩니다(규칙 1의 우선순위가 높으므로 해당 규칙이 적용되고 다른 규칙은 적용되지 않음).
   * "Action"은 이 규칙의 영향을 받는 트래픽을 차단하거나 허용할지를 나타냅니다.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. 이 규칙은 RDP 트래픽이 인터넷에서 바인딩된 서브넷에 있는 모든 서버의 RDP 포트로 흐르도록 허용합니다. 이 규칙은 "VIRTUAL_NETWORK" 및 "INTERNET"이라는 두 가지 특별한 주소 접두사 형식을 사용합니다. 이러한 태그는 주소 접두사로 더 큰 범주의 주소를 간편하게 지정하는 쉬운 방법입니다.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. 이 규칙은 웹 서버를 호출하기 위해 인바운드 인터넷 트래픽을 허용합니다. 이 규칙은 라우팅 동작을 변경하지 않습니다. 규칙은 IIS01을 대상으로 하는 트래픽만을 전달하도록 허용합니다. 따라서 인터넷에서 트래픽이 이 규칙에서 허용하는 대상으로 웹 서버를 포함하는 경우 추가 규칙의 처리를 중지합니다. (우선순위 140의 규칙에서 모든 다른 인바운드 인터넷 트래픽이 차단됩니다.) HTTP 트래픽만 처리하는 경우 대상 포트 80만 허용하도록 이 규칙을 추가로 제한할 수 있습니다.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. 이 규칙은 IIS01 서버에서 AppVM01 서버로 트래픽 전달을 허용합니다. 이후 규칙은 백 엔드 트래픽에 대한 모든 다른 프런트엔드를 차단합니다. 추가해야 할 포트를 알고 있다면 이 규칙을 개선할 수 있습니다. 예를 들어, IIS 서버가 AppVM01에서 SQL Server만 연결하는 경우 대상 포트 범위를 "*"(모두)에서 1433(SQL 포트)으로 변경해야 하므로 AppVM01에서 웹 응용 프로그램이 손상될 수 있는 인바운드 공격에 대한 취약성을 줄일 수 있습니다.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. 이 규칙은 네트워크 상의 모든 서버에 인터넷에서 트래픽을 거부합니다. 우선순위 110 및 120의 규칙을 사용하여 방화벽에 대한 인바운드 인터넷 트래픽 및 서버의 RDP 포트만 허용하고 다른 모든 항목은 차단합니다. 이 규칙은 모든 예기치 않은 흐름을 차단하는 "유사 시 대기" 규칙입니다.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. 마지막 규칙은 프런트엔드 서브넷에서 백 엔드 서브넷으로 트래픽을 거부합니다. 이 규칙은 인바운드 전용 규칙이므로 역방향 트래픽이 허용됩니다(백 엔드에서 프런트 엔드로).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>트래픽 시나리오
#### <a name="allowed-internet-to-web-server"></a>(*허용*) 인터넷에서 웹 서버
1. 인터넷 사용자가 FrontEnd001.CloudApp.Net에서 HTTP 페이지를 요청합니다(인터넷 연결 클라우드 서비스).
2. 클라우드 서비스는 포트 80에서 열린 끝점을 통해 IIS01(웹 서버)로 트래픽을 전달합니다.
3. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 IIS01로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 웹 서버 IIS01의 내부 IP 주소를 호출합니다(10.0.1.5).
5. IIS01이 웹 트래픽을 수신 대기하고 이 요청을 수신하며 요청 처리를 시작합니다.
6. IIS01은 AppVM01에서 SQL Server에 정보를 요청합니다.
7. 프런트 엔드 서브넷에 아웃바운드 규칙이 없으므로 트래픽이 허용됩니다.
8. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 방화벽으로)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. NSG 규칙 4(IIS01에서 AppVM01로)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
9. AppVM01은 SQL 쿼리를 수신하고 응답합니다.
10. 백 엔드 서브넷에 아웃바운드 규칙이 없으므로 응답이 허용됩니다.
11. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
    1. 백 엔드 서브넷에서 프런트엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
    2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙이 이 트래픽을 허용하므로 트래픽이 허용됩니다.
12. IIS 서버는 SQL 응답을 수신하고 HTTP 응답을 완료하여 요청자에게 보냅니다.
13. 응답이 허용되는 프런트 엔드 서브넷에 아웃바운드 규칙이 없으므로 인터넷 사용자는 요청된 웹 페이지를 수신합니다.

#### <a name="allowed-rdp-to-backend"></a>(*허용*) RDP - 백 엔드
1. 인터넷의 서버 관리자는 BackEnd001.CloudApp.Net:xxxxx에서 AppVM01에 대해 RDP 세션을 요청합니다.여기서 xxxxx는 RDP에 대해 AppVM01로 임의로 할당된 포트 번호입니다(할당된 포트는 Azure Portal 또는 PowerShell을 통해 확인할 수 있음).
2. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
3. 아웃바운드 규칙이 없는 경우 기본 규칙이 적용되고 반환 트래픽이 허용됩니다.
4. RDP 세션이 사용하도록 설정됩니다.
5. AppVM01에서 사용자 이름과 암호를 묻습니다.

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*허용*) DNS 서버에서 웹 서버 DNS 조회
1. 웹 서버인 IIS01은 www.data.gov에서 데이터 피드를 요구하지만 주소를 확인해야 합니다.
2. VNet에 대한 네트워크 구성에서 DNS01(백 엔드 서브넷에서 10.0.2.4)을 주 DNS 서버로 나열하며 IIS01은 DNS01로 DNS 요청을 보냅니다.
3. 프런트 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
4. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   * NSG 규칙 1(DNS)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
5. DNS 서버는 요청을 수신합니다.
6. DNS 서버에는 캐시된 주소가 없고 인터넷에서 루트 DNS 서버를 요청합니다.
7. 백 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
8. 이 세션은 내부적으로 시작되었으므로 인터넷 DNS 서버가 응답하고 응답이 허용됩니다.
9. DNS 서버는 응답을 캐시하고 IIS01에 대한 초기 요청에 다시 응답합니다.
10. 백 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
11. 프런트엔드 서브넷은 인바운드 규칙 처리를 시작합니다.
    1. 백 엔드 서브넷에서 프런트 엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
    2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙에서 이 트래픽을 허용하므로 트래픽이 허용됩니다.
12. IIS01이 DNS01에서 응답을 수신합니다.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*허용*) AppVM01에서 웹 서버가 파일 액세스
1. IIS01은 AppVM01에서 파일을 요청합니다.
2. 프런트 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
3. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 IIS01로)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. NSG 규칙 4(IIS01에서 AppVM01로)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. AppVM01이 요청을 받아 파일로 응답합니다(액세스 권한이 부여된 것으로 가정).
5. 백 엔드 서브넷에 아웃바운드 규칙이 없으므로 응답이 허용됩니다.
6. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
   1. 백 엔드 서브넷에서 프런트엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
   2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙이 이 트래픽을 허용하므로 트래픽이 허용됩니다.
7. IIS 서버가 파일을 수신합니다.

#### <a name="denied-web-to-backend-server"></a>(*거부*) 웹 - 백 엔드 서버
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 AppVM01에서 파일에 액세스하려고 합니다.
2. 파일 공유를 위해 열린 끝점이 없으므로 이 트래픽은 클라우드 서비스를 전달하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 NSG 규칙 5(인터넷에서 VNet으로)가 이 트래픽을 차단합니다.

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*거부*) DNS 서버에서 웹 DNS 조회
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 DNS01에서 내부 DNS 레코드를 조회하려고 합니다.
2. DNS를 위해 열린 끝점이 없으므로 이 트래픽은 클라우드 서비스를 전달하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 NSG 규칙 5(인터넷에서 VNet으로)가 이 트래픽을 차단합니다.(참고: 규칙 1(DNS)은 두 가지 이유로 적용되지 않습니다. 첫째, 원본 주소가 인터넷입니다. 이 규칙은 로컬 VNet에만 원본으로 적용됩니다. 또한 이 규칙은 허용 규칙이므로 트래픽을 거부하지 않습니다.)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*거부*) 방화벽을 통해 웹에서 SQL 액세스
1. 인터넷 사용자가 FrontEnd001.CloudApp.Net에서 SQL 데이터를 요청합니다(인터넷 연결 클라우드 서비스).
2. SQL을 위해 열린 끝점이 없으므로 이 트래픽은 클라우드 서비스를 전달하지 않고 방화벽에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 프런트엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 IIS01로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 IIS01의 내부 IP 주소를 호출합니다(10.0.1.5).
5. IIS01이 포트 1433에서 수신 대기하지 않으므로 요청에 대한 응답이 없습니다.

## <a name="conclusion"></a>결론
이 예제는 백 엔드 서브넷을 인바운드 트래픽과 격리하는 비교적 간단하고 직접적인 방법입니다.

네트워크 보안 경계에 대한 더 많은 예와 개요는 [여기][HOME]에서 찾을 수 있습니다.

## <a name="references"></a>참조
### <a name="main-script-and-network-config"></a>기본 스크립트 및 네트워크 구성
PowerShell 스크립트 파일에 전체 스크립트를 저장합니다. 네트워크 구성을 "NetworkConf1.xml"이라는 파일에 저장합니다.
필요에 따라 사용자 정의 변수를 수정하고 스크립트를 실행합니다.

#### <a name="full-script"></a>전체 스크립트
이 스크립트는 사용자 정의 변수를 기반으로 합니다.

1. Azure 구독에 연결
2. 저장소 계정 만들기
3. 네트워크 구성 파일에 정의된 대로 VNet 및 두 서브넷 만들기
4. 4개의 Windows Server VM 빌드
5. 다음을 포함하여 NSG를 구성합니다.
   * NSG 만들기
   * 규칙으로 채우기
   * 적절한 서브넷에 대해 NSG 바인딩

이 PowerShell 스크립트를 인터넷 연결된 PC 또는 서버에서 로컬로 실행해야 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하는 경우 PowerShell에서 경고 또는 기타 정보 메시지가 표시될 수 있습니다. 빨간색 오류 메시지만 심각한 문제입니다.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

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

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
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

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>네트워크 구성 파일
업데이트된 위치로 이 xml 파일을 저장하고 이 파일에 대한 링크를 앞의 스크립트에 있는 $NetworkConfigFile 변수에 추가합니다.

```XML
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
```

#### <a name="sample-application-scripts"></a>샘플 응용 프로그램 스크립트
이에 대한 샘플 응용 프로그램 및 기타 DMZ 예제를 설치하는 방법은 다음 링크를 통해 제공됩니다. [샘플 응용 프로그램 스크립트][SampleApp]

## <a name="next-steps"></a>다음 단계
* 업데이트 및 XML 파일 저장
* PowerShell 스크립트를 실행하여 환경 구축
* 샘플 응용 프로그램 설치
* 이 DMZ를 통해 다양한 트래픽 흐름 테스트

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "NSG와 인바운드 DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

