---
title: Azure DMZ 예제 – NSG를 사용하여 간단한 DMZ 빌드| Microsoft Docs
description: NSG(네트워크 보안 그룹)를 사용하여 DMZ 빌드
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>예 1 – Azure Resource Manager 템플릿으로 NSG를 사용하여 간단한 DMZ 빌드
[보안 경계 모범 사례 페이지로 돌아가기][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager 템플릿](virtual-networks-dmz-nsg.md)
> * [클래식 - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

이 예에서는 4개의 Windows Server 및 네트워크 보안 그룹이 포함된 기본 DMZ를 만듭니다. 이 예제는 각 단계를 자세히 이해할 수 있도록 각각의 관련 템플릿 섹션을 설명합니다. 트래픽 시나리오 섹션에서는 DMZ에서 방어 계층을 진행하는 방법에 대한 심층적인 단계별 설명도 제공합니다. 마지막으로, 참조 섹션에서는 다양한 시나리오를 사용하여 테스트 및 실험하기 위한 환경을 구축하는 전체 템플릿 코드와 지침을 제공합니다. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![NSG와 인바운드 DMZ][1]

## <a name="environment-description"></a>환경 설명
이 예에서 구독은 다음 리소스를 포함합니다.

* 단일 리소스 그룹
* 두 서브넷 “프런트 엔드” 및 “백 엔드”를 사용하는 Virtual Network
* 서브넷 모두에 적용되는 네트워크 보안 그룹
* 응용 프로그램 웹 서버("IIS01")를 나타내는 Windows 서버
* 응용 프로그램 백 엔드 서버("AppVM01", "AppVM02")를 나타내는 두 Windows 서버
* DNS 서버("DNS01")를 나타내는 Windows 서버
* 응용 프로그램 웹 서버에 연결된 공용 IP 주소

참조 섹션에서 이 예제에서 설명된 환경을 구축하는 Azure Resource Manager 템플릿에 대한 링크가 있습니다. VM 및 Virtual Network 구축은 예제 템플릿으로 수행하지만 이 문서에서는 자세히 설명하지 않습니다. 

**이 환경을 구축하려면**(자세한 지침은 이 문서의 참조 섹션에 있습니다.)

1. Azure Resource Manager 템플릿 배포: [Azure 빠른 시작 템플릿][Template]
2. 샘플 응용 프로그램 설치: [샘플 응용 프로그램 스크립트][SampleApp]

>[!NOTE]
>이 인스턴스의 백 엔드 서버에 RDP하려면 IIS 서버는 "점프 상자"로 사용됩니다. 먼저 RDP에서 IIS 서버로 그 다음에 IIS 서버 RDP에서 백 엔드 서버입니다. 또는 공용 IP는 보다 쉬운 RDP를 위해 각 서버 NIC와 연결할 수 있습니다.
> 
>

다음 섹션에서는 네트워크 보안 그룹을 설명하고 Azure Resource Manager 템플릿의 핵심 줄을 살펴보는 방법으로 이 예제의 작동 방식을 자세히 설명합니다.

## <a name="network-security-groups-nsg"></a>네트워크 보안 그룹(NSG)
이 예제에서는 NSG 그룹을 빌드한 후 6개의 규칙을 로드합니다. 

>[!TIP]
>일반적으로 특정 "허용" 규칙을 먼저 만든 후 보다 일반적인 "거부" 규칙을 만들어야 합니다. 할당된 우선순위에 따라 먼저 평가할 규칙이 결정됩니다. 특정 규칙에 적용할 트래픽이 발견되면 규칙을 더 이상 평가하지 않습니다. NSG 규칙은 인바운드 또는 아웃바운드 방향으로 적용할 수 있습니다(서브넷 관점에서).
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

인터넷으로 트래픽을 허용하는 기본 아웃바운드 규칙이 있습니다. 이 예에서는 아웃바운드 트래픽을 허용하고 아웃바운드 규칙을 수정하지 않습니다. 양방향에서 트래픽에 보안 정책을 적용하려면 사용자 정의 라우팅이 필요하며 [보안 경계 모범 사례 페이지][HOME]의 "예제 3"에서 이에 대해 살펴봅니다.

각 규칙은 다음과 같이 더 자세히 설명되어 있습니다.

1. 규칙을 저장하도록 네트워크 보안 그룹 리소스를 인스턴스화해야 합니다.

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. 이 예의 첫 번째 규칙에서는 백 엔드 서브넷에서 DNS 서버에 대해 모든 내부 네트워크 간 DNS 트래픽을 허용합니다. 규칙은 몇 가지 중요한 매개 변수를 포함합니다.
  * "destinationAddressPrefix" - 규칙은 "기본 태그"라는 특수한 주소 유형의 접두사를 사용할 수 있습니다. 이러한 태그는 더 큰 범주의 주소 접두사를 해결하는 쉬운 방법을 허용하는 시스템 제공 식별자입니다. 이 규칙은 기본 태그 “Internet"을 사용하여 VNet 외의 모든 주소를 나타냅니다. 다른 접두사 레이블은 VirtualNetwork 및 AzureLoadBalancer입니다.
  * "Direction"은 이 규칙이 적용되는 트래픽 흐름의 방향을 나타냅니다. 방향은 서브넷 또는 Virtual Machine의 관점에서 옵니다(이 NSG가 바인딩되는 위치에 따라). 따라서 Direction이 "Inbound"이고 트래픽이 서브넷에 들어가는 경우 규칙이 적용되고 서브넷에서 나가는 트래픽에는 이 규칙이 적용되지 않습니다.
  * "Priority"는 트래픽 흐름이 평가되는 순서를 설정합니다. 번호가 낮을수록 우선순위가 높습니다. 특정 트래픽 흐름에 규칙이 적용되는 경우 더 이상 규칙이 처리되지 않습니다. 따라서 우선순위 1인 규칙에서는 트래픽을 허용하고 우선순위 2인 규칙에서는 트래픽을 거부하고 두 규칙 모두 트래픽에 적용된다면 트래픽 흐름이 허용됩니다(규칙 1의 우선순위가 높으므로 해당 규칙이 적용되고 다른 규칙은 적용되지 않음).
  * "Access"는 이 규칙의 영향을 받는 트래픽을 차단("Deny")하거나 허용("Allow")할지를 나타냅니다.

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. 이 규칙은 RDP 트래픽이 인터넷에서 바인딩된 서브넷에 있는 모든 서버의 RDP 포트로 흐르도록 허용합니다. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. 이 규칙은 웹 서버를 호출하기 위해 인바운드 인터넷 트래픽을 허용합니다. 이 규칙은 라우팅 동작을 변경하지 않습니다. 규칙은 IIS01을 대상으로 하는 트래픽만을 전달하도록 허용합니다. 따라서 인터넷에서 트래픽이 이 규칙에서 허용하는 대상으로 웹 서버를 포함하는 경우 추가 규칙의 처리를 중지합니다. (우선순위 140의 규칙에서 모든 다른 인바운드 인터넷 트래픽이 차단됩니다.) HTTP 트래픽만 처리하는 경우 대상 포트 80만 허용하도록 이 규칙을 추가로 제한할 수 있습니다.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. 이 규칙은 IIS01 서버에서 AppVM01 서버로 트래픽 전달을 허용합니다. 이후 규칙은 백 엔드 트래픽에 대한 모든 다른 프런트엔드를 차단합니다. 추가해야 할 포트를 알고 있다면 이 규칙을 개선할 수 있습니다. 예를 들어, IIS 서버가 AppVM01에서 SQL Server만 연결하는 경우 대상 포트 범위를 "*"(모두)에서 1433(SQL 포트)으로 변경해야 하므로 AppVM01에서 웹 응용 프로그램이 손상될 수 있는 인바운드 공격에 대한 취약성을 줄일 수 있습니다.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. 이 규칙은 네트워크 상의 모든 서버에 인터넷에서 트래픽을 거부합니다. 우선순위 110 및 120의 규칙을 사용하여 방화벽에 대한 인바운드 인터넷 트래픽 및 서버의 RDP 포트만 허용하고 다른 모든 항목은 차단합니다. 이 규칙은 모든 예기치 않은 흐름을 차단하는 "유사 시 대기" 규칙입니다.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. 마지막 규칙은 프런트엔드 서브넷에서 백 엔드 서브넷으로 트래픽을 거부합니다. 이 규칙은 인바운드 전용 규칙이므로 역방향 트래픽이 허용됩니다(백 엔드에서 프런트 엔드로).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>트래픽 시나리오
#### <a name="allowed-internet-to-web-server"></a>(*허용*) 인터넷에서 웹 서버
1. 인터넷 사용자는 IIS01 NIC와 연결된 NIC의 공용 IP 주소에서 HTTP 페이지를 요청합니다.
2. 공용 IP 주소는 IIS01 쪽으로 VNet에 트래픽을 전달합니다(웹 서버).
3. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
  1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
  2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
  3. NSG 규칙 3(인터넷에서 IIS01로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 웹 서버 IIS01의 내부 IP 주소를 호출합니다(10.0.1.5).
5. IIS01이 웹 트래픽을 수신 대기하고 이 요청을 수신하며 요청 처리를 시작합니다.
6. IIS01은 AppVM01에서 SQL Server에 정보를 요청합니다.
7. 프런트엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
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

#### <a name="allowed-rdp-to-iis-server"></a>(*허용*) RDP에서 IIS 서버
1. 인터넷에서 서버 관리자는 IIS01 NIC와 연결된 NIC의 공용 IP 주소에서 IIS01에 대한 RDP 세션을 요청합니다(이 공용 IP 주소는 포털 또는 PowerShell을 통해 찾을 수 있음).
2. 공용 IP 주소는 IIS01 쪽으로 VNet에 트래픽을 전달합니다(웹 서버).
3. 프런트 엔드 서브넷에서 인바운드 규칙 처리를 시작합니다.
  1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
  2. NSG 규칙 2(RDP)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 아웃바운드 규칙이 없는 경우 기본 규칙이 적용되고 반환 트래픽이 허용됩니다.
5. RDP 세션이 사용하도록 설정됩니다.
6. IIS01에서 사용자 이름과 암호를 묻습니다.

>[!NOTE]
>이 인스턴스의 백 엔드 서버에 RDP하려면 IIS 서버는 "점프 상자"로 사용됩니다. 먼저 RDP에서 IIS 서버로 그 다음에 IIS 서버 RDP에서 백 엔드 서버입니다.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*거부*) RDP - 백 엔드
1. 인터넷 사용자가 서버 AppVM01로 RDP하려고 합니다.
2. 이 서버 NIC와 연결된 공용 IP 주소가 없으므로 이 트래픽은 VNet을 입력할 수 없고 서버에 도달하지 않습니다.
3. 그러나 어떤 이유로 공용 IP 주소가 활성화된 경우 NSG 규칙 2(RP)는 이 트래픽을 허용합니다.

>[!NOTE]
>이 인스턴스의 백 엔드 서버에 RDP하려면 IIS 서버는 "점프 상자"로 사용됩니다. 먼저 RDP에서 IIS 서버로 그 다음에 IIS 서버 RDP에서 백 엔드 서버입니다.
>
>

#### <a name="denied-web-to-backend-server"></a>(*거부*) 웹 - 백 엔드 서버
1. 인터넷 사용자가 AppVM01에 있는 파일에 액세스하려고 합니다.
2. 이 서버 NIC와 연결된 공용 IP 주소가 없으므로 이 트래픽은 VNet을 입력할 수 없고 서버에 도달하지 않습니다.
3. 어떤 이유로 공용 IP 주소가 활성화된 경우 NSG 규칙 5(인터넷에서 VNet으로)는 이 트래픽을 차단합니다.

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*거부*) DNS 서버에서 웹 DNS 조회
1. 인터넷 사용자가 DNS01에 있는 내부 DNS 레코드를 조회하려고 합니다.
2. 이 서버 NIC와 연결된 공용 IP 주소가 없으므로 이 트래픽은 VNet을 입력할 수 없고 서버에 도달하지 않습니다.
3. 어떤 이유로 공용 IP 주소가 활성화된 경우 NSG 규칙 5(인터넷에서 VNet으로)는 이 트래픽을 차단합니다.(참고: 요청 원본 주소는 인터넷이며 규칙 1은 원본으로 로컬 VNet에만 적용되므로 해당 규칙 1(DNS)은 적용되지 않습니다.)

#### <a name="denied-sql-access-on-the-web-server"></a>(*거부*) 웹 서버에서 SQL 액세스
1. 인터넷 사용자가 IIS01에서 SQL 데이터를 요청합니다.
2. 이 서버 NIC와 연결된 공용 IP 주소가 없으므로 이 트래픽은 VNet을 입력할 수 없고 서버에 도달하지 않습니다.
3. 어떤 이유로 공용 IP 주소가 활성화된 경우 프런트 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
  1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
  2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
  3. NSG 규칙 3(인터넷에서 IIS01로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 IIS01의 내부 IP 주소를 호출합니다(10.0.1.5).
5. IIS01이 포트 1433에서 수신 대기하지 않으므로 요청에 대한 응답이 없습니다.

## <a name="conclusion"></a>결론
이 예제는 백 엔드 서브넷을 인바운드 트래픽과 격리하는 비교적 간단하고 직접적인 방법입니다.

네트워크 보안 경계에 대한 더 많은 예와 개요는 [여기][HOME]에서 찾을 수 있습니다.

## <a name="references"></a>참조
### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
이 예제는 Microsoft에서 유지 관리하는 GitHub 리포지토리에서 미리 정의된 Azure Resource Manager 템플릿을 사용하고 해당 커뮤니티를 열 수 있습니다. 이 템플릿은 GitHub에서 바로 배포하거나 다운로드한 후 필요에 맞게 수정할 수 있습니다. 

기본 템플릿은 “azuredeploy.json”이라는 파일에 있습니다. 이 템플릿을 배포하도록 PowerShell 또는 CLI를 통해 제출할 수 있습니다(연결된 "azuredeploy.parameters.json" 파일과 함께). 가장 쉬운 방법은 GitHub에서 README.md 페이지의 "Azure에 배포" 단추를 사용하는 것입니다.

GitHub 및 Azure Portal에서 이 예제를 작성하는 템플릿을 배포하려면 이 단계를 따릅니다.

1. 브라우저에서 [템플릿][Template]으로 이동합니다.
2. "Azure에 배포" 단추(또는 이 템플릿의 그래픽 표시를 보려면 "시각화" 단추)를 클릭합니다.
3. 매개 변수 블레이드에서 Storage 계정, 사용자 이름 및 암호를 입력한 다음 **확인**을 클릭합니다.
5. 이 배포에 대한 리소스 그룹을 만듭니다.(기존 것을 사용할 수 있지만 최상의 결과를 위해 새 것을 권장합니다.)
6. 필요한 경우 VNet에 맞게 구독 및 위치 설정을 변경합니다.
7. **약관 검토**를 클릭하고 약관을 읽은 후 **구입**을 클릭하여 동의합니다.
8. **만들기**를 클릭하여 이 템플릿의 배포를 시작합니다.
9. 배포가 성공적으로 완료되면 내부에서 구성된 리소스를 보기 위해 이 배포에 대해 만든 리소스 그룹으로 이동합니다.

>[!NOTE]
>이 템플릿을 통해 IIS01 서버로 RDP할 수 있습니다(포털에서 IIS01에 대한 공용 IP 찾기). 이 인스턴스의 백 엔드 서버에 RDP하려면 IIS 서버는 "점프 상자"로 사용됩니다. 먼저 RDP에서 IIS 서버로 그 다음에 IIS 서버 RDP에서 백 엔드 서버입니다.
>
>

이 배포를 제거하려면 리소스 그룹을 삭제합니다. 모든 자식 리소스도 삭제됩니다.

#### <a name="sample-application-scripts"></a>샘플 응용 프로그램 스크립트
템플릿이 성공적으로 실행되면 간단한 웹 응용 프로그램을 사용하는 웹 서버와 앱 서버를 설정하여 이 DMZ 구성을 이용한 테스트를 허용할 수 있습니다. 이에 대한 샘플 응용 프로그램 및 기타 DMZ 예제를 설치하는 방법은 다음 링크를 통해 제공됩니다. [샘플 응용 프로그램 스크립트][SampleApp]

## <a name="next-steps"></a>다음 단계

* 이 예제 배포
* 샘플 응용 프로그램 빌드
* 이 DMZ를 통해 다양한 트래픽 흐름 테스트

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "NSG와 인바운드 DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md