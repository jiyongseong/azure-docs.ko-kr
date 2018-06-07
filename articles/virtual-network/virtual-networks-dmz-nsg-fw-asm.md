---
title: "DMZ 예제 - 방화벽 및 NSG로 응용 프로그램을 보호하는 DMZ 빌드 | Microsoft Docs"
description: "방화벽 및 NSG(네트워크 보안 그룹)를 사용하여 DMZ 빌드"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>예 2 - 방화벽 및 NSG로 응용 프로그램을 보호하는 DMZ 빌드
[보안 경계 모범 사례 페이지로 돌아가기][HOME]

이 예에서는 방화벽, 4개의 Windows Server 및 네트워크 보안 그룹이 포함된 DMZ를 만듭니다. 또한 각 단계를 자세히 이해할 수 있도록 각각의 관련 명령에 대해 안내합니다. 트래픽 시나리오 섹션에서는 DMZ에서 방어 계층을 진행하는 방법에 대한 심층적인 단계별 설명도 제공합니다. 마지막으로, 참조 섹션에서는 다양한 시나리오를 사용하여 테스트 및 실험하기 위한 환경을 구축하는 전체 코드와 지침을 제공합니다. 

![NVA 및 NSG와 인바운드 DMZ][1]

## <a name="environment-description"></a>환경 설명
이 예에서는 다음을 포함하는 구독이 있습니다.

* 두 클라우드 서비스: "FrontEnd001" 및 "BackEnd001"
* "FrontEnd" 및 "BackEnd"의 두 서브넷을 포함하는 가상 네트워크 "CorpNetwork"
* 서브넷 모두에 적용되는 단일 네트워크 보안 그룹
* 네트워크 가상 어플라이언스(이 예제의 경우 프런트 엔드 서브넷에 연결된 Barracuda NextGen Firewall)
* 응용 프로그램 웹 서버("IIS01")를 나타내는 Windows 서버
* 응용 프로그램 백 엔드 서버("AppVM01", "AppVM02")를 나타내는 두 Windows 서버
* DNS 서버("DNS01")를 나타내는 Windows 서버

> [!NOTE]
> 이 예제에서는 Barracuda NextGen Firewall을 사용하지만 이 예제에 다른 네트워크 가상 어플라이언스를 사용해도 됩니다.
> 
> 

아래 참조 섹션에는 위에서 설명한 대부분의 환경을 빌드할 PowerShell 스크립트가 나와 있습니다. VM 및 가상 네트워크 구축은 예제 스크립트로 수행하지만 이 문서에서는 자세히 설명하지 않습니다.

환경을 구축하려면

1. 참조 섹션에 포함된 네트워크 구성 xml 파일 저장(지정된 시나리오에 맞게 이름, 위치, IP 주소로 업데이트됨)
2. 스크립트의 사용자 변수를 스크립트를 실행할 환경에 맞게 업데이트(구독, 서비스 이름 등)
3. PowerShell에서 스크립트 실행

**참고**: PowerShell 스크립트에 표시된 영역은 네트워크 구성 xml 파일에 표시된 영역과 일치해야 합니다.

스크립트를 성공적으로 실행하면 다음과 같은 사후 스크립트 단계를 수행할 수 있습니다.

1. 방화벽 규칙을 설정합니다(아래 방화벽 규칙 섹션에서 설명).
2. 또는 참조 섹션의 두 스크립트를 사용하여 간단한 웹 응용 프로그램을 사용하는 웹 서버와 앱 서버를 설정하여 이 DMZ 구성을 이용한 테스트를 허용합니다.

다음 섹션에서는 네트워크 보안 그룹과 관련된 대부분의 스크립트 문에 대해 설명합니다.

## <a name="network-security-groups-nsg"></a>네트워크 보안 그룹(NSG)
이 예에서는 NSG 그룹을 빌드한 후 6개의 규칙과 함께 로드합니다. 

> [!TIP]
> 일반적으로 특정 "허용" 규칙을 먼저 만든 후 보다 일반적인 "거부" 규칙을 만들어야 합니다. 할당된 우선순위에 따라 먼저 평가할 규칙이 결정됩니다. 특정 규칙에 적용할 트래픽이 발견되면 규칙을 더 이상 평가하지 않습니다. NSG 규칙은 인바운드 또는 아웃바운드 방향으로 적용할 수 있습니다(서브넷 관점에서).
> 
> 

선언적으로 인바운드 트래픽에 대해 다음 규칙이 빌드됩니다.

1. 내부 DNS 트래픽(포트 53) 허용됨
2. 인터넷에서 모든 VM으로 RDP 트래픽(포트 3389) 허용됨
3. 인터넷에서 NVA(방화벽)로 HTTP 트래픽(포트 80) 허용됨
4. IIS01에서 AppVM1로 모든 트래픽(모든 포트) 허용됨
5. 인터넷에서 전체 VNet(두 서브넷)으로 모든 트래픽(모든 포트) 거부됨
6. 프런트 엔드 서브넷에서 백 엔드 서브넷으로 모든 트래픽(모든 포트) 거부됨

각 서브넷에 바인딩된 이러한 규칙에서는 HTTP 요청이 인터넷에서 웹 서버로 인바운드되는 경우 규칙 3(허용) 및 5(거부)가 모두 적용되지만 규칙 3이 우선순위가 높기 때문에 규칙 3만 적용되고 규칙 5는 진행되지 않습니다. 따라서 방화벽에 대해 HTTP 요청이 허용됩니다. 동일한 트래픽이 DNS01 서버에 도달하려고 시도하는 경우 규칙 5(거부)는 가장 먼저 적용되며 트래픽을 서버에 전달할 수 없습니다. 규칙 6(거부)은 프런트 엔드 서브넷이 백 엔드 서브넷과 통신을 차단합니다(규칙 1 및 4에서 허용된 트래픽 제외)하여 공격자가 프런트 엔드에서 웹 응용 프로그램을 손상시키는 경우 공격자의 "보호된" 백 엔드 네트워크(AppVM01 서버에서 노출되는 리소스만)에 대한 액세스가 제한되므로 백 엔드 네트워크를 보호합니다.

인터넷으로 트래픽을 허용하는 기본 아웃바운드 규칙이 있습니다. 이 예에서는 아웃바운드 트래픽을 허용하고 아웃바운드 규칙을 수정하지 않습니다. 양방향에서 트래픽을 잠그려면 사용자 정의 라우팅이 필요하며 [기본 보안 경계 문서][HOME]에 있는 다양한 예에서 이에 대해 살펴봅니다.

위에서 설명한 NSG 규칙은 [예 1 - NSG를 사용하여 간단한 DMZ 빌드][Example1]의 NSG 규칙과 매우 유사합니다. 이 문서의 NSG 설명을 검토하여 각 NSG 규칙 및 해당 특성에 대해 자세히 살펴봅니다.

## <a name="firewall-rules"></a>방화벽 규칙
관리 클라이언트를 PC에 설치하여 방화벽을 관리하고 필요에 따라 구성을 만들어야 합니다. 장치를 관리하는 방법은 방화벽(또는 다른 NVA) 공급업체의 설명서를 참조하세요. 이 섹션의 나머지 부분에서는 공급업체 관리 클라이언트(예: Azure 포털 또는 PowerShell 아님)를 통한 방화벽 자체 구성에 대해 설명합니다.

이 예에 사용된 클라이언트 다운로드 및 Barracuda 연결에 대한 지침은 [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

방화벽에서 전달 규칙을 만들어야 합니다. 이 예에서는 인터넷 트래픽 인바운드를 방화벽으로 라우팅한 후 웹 서버로 라우팅하므로 하나의 전달 NAT 규칙만 필요합니다. 이 예제에 사용된 Barracuda NextGen Firewall에서 규칙은 이 트래픽을 전달하기 위한 대상 NAT 규칙("Dst NAT")입니다.

다음 규칙을 만들거나 기존 기본 규칙을 확인하려면 Barracuda NG Admin 클라이언트 대시보드에서 시작하여 구성 탭으로 이동한 후 작동 구성 섹션에서 규칙 집합을 클릭합니다. "Main Rules"라는 그리드에 방화벽에 대해 기존 활성 및 비활성화된 규칙이 표시됩니다. 이 그리드의 오른쪽 위 모서리에 작은 녹색 "+" 단추를 클릭하여 새 규칙을 만듭니다(참고: 방화벽은 변경에 대해 "잠금" 상태일 수 있으며 "잠금" 단추가 표시되고 규칙을 만들거나 편집할 수 없는 경우 이 단추를 클릭하여 규칙 집합을 "잠금 해제"하고 편집을 허용합니다). 기존 규칙을 편집하려는 경우 해당 규칙을 선택하고 마우스 오른쪽 단추를 클릭한 후 규칙 편집을 선택합니다.

새 규칙을 만들고 "WebTraffic"과 같은 이름을 제공합니다. 

대상 NAT 규칙 아이콘은 다음과 같습니다. ![대상 NAT 아이콘][2]

규칙 자체는 다음과 같이 표시됩니다.

![방화벽 규칙][3]

여기서 HTTP(포트 80 또는 HTTPS인 경우 443)에 도달하려고 시도하는 방화벽을 호출하는 모든 인바운드 주소는 방화벽의 "DHCP1 로컬 IP" 인터페이스로 전송되고 IP 주소 10.0.1.5의 웹 서버로 리디렉션됩니다. 트래픽이 포트 80에서 들어오고 포트 80에서 웹 서버로 나가므로 포트 변경이 필요하지 않습니다. 그러나 웹 서버가 포트 8080을 수신 대기하는 경우 대상 목록은 10.0.1.5:8080일 수 있으므로 방화벽에서 인바운드 포트 80을 웹 서버에서 인바운드 포트 8080으로 변환합니다.

연결 방법을 나타내야 하며 인터넷의 대상 규칙에 대해 "동적 SNAT"가 가장 적합합니다. 

하나의 규칙만 만들어지지만 우선순위가 바르게 설정되는 것이 중요합니다. 방화벽의 모든 규칙에 대한 그리드에서 이 새로운 규칙은 맨 아래("BLOCKALL" 규칙 아래)에 있으며 진행되지 않습니다. 웹 트래픽에 대해 새로 만든 규칙이 BLOCKALL 규칙 위에 있는지 확인합니다.

규칙을 만들었으면 방화벽에 푸시한 후 활성화해야 합니다. 이 작업을 수행하지 않으면 규칙 변경이 적용되지 않습니다. 푸시 및 활성화 프로세스는 다음 섹션에서 설명됩니다.

## <a name="rule-activation"></a>규칙 활성화
이 규칙을 추가하도록 규칙 집합을 수정하고 규칙 집합을 방화벽에 업로드하고 활성화해야 합니다.

![방화벽 규칙 활성화][4]

관리 클라이언트의 오른쪽 위 모서리에 단추 클러스터가 있습니다. "변경 내용 보내기" 단추를 클릭하여 규칙을 방화벽으로 전송한 다음 "활성화" 단추를 클릭합니다.

방화벽 규칙 집합을 활성화하면 이 예제 환경 빌드가 완료된 것입니다. 필요에 따라 참조 섹션의 빌드 후 스크립트를 실행하여 응용 프로그램을 이 환경에 추가하고 아래 트래픽 시나리오를 테스트할 수 있습니다.

> [!IMPORTANT]
> 웹 서버를 직접 호출하지 않는다는 것을 깨닫는 것이 중요합니다. 브라우저가 FrontEnd001.CloudApp.Net에서 HTTP 페이지를 요청하면 HTTP 끝점(포트 80)이 이 트래픽을 웹 서버가 아닌 방화벽에 전달합니다. 방화벽 다음에는 위에서 만든 규칙에 따라 웹 서버로 요청을 NAT합니다.
> 
> 

## <a name="traffic-scenarios"></a>트래픽 시나리오
#### <a name="allowed-web-to-web-server-through-firewall"></a>(허용) 방화벽을 통해 웹 - 웹 서버
1. 인터넷 사용자가 FrontEnd001.CloudApp.Net에서 HTTP 페이지를 요청합니다(인터넷 연결 클라우드 서비스).
2. 클라우드 서비스는 포트 80에서 열린 끝점을 통해 10.0.1.4:80에서 방화벽 로컬 인터페이스로 트래픽을 전달합니다.
3. 프런트엔드 서브넷은 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 방화벽으로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 방화벽의 내부 IP 주소를 호출합니다(10.0.1.4).
5. 방화벽 전달 규칙은 이것이 포트 80 트래픽인 것을 확인하고 웹 서버 IIS01로 리디렉션합니다.
6. IIS01이 웹 트래픽을 수신 대기하고 이 요청을 수신하며 요청 처리를 시작합니다.
7. IIS01은 AppVM01에서 SQL Server에 정보를 요청합니다.
8. 프런트엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
9. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 방화벽으로)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. NSG 규칙 4(IIS01에서 AppVM01로)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
10. AppVM01은 SQL 쿼리를 수신하고 응답합니다.
11. 백 엔드 서브넷에 아웃바운드 규칙이 없으므로 응답이 허용됩니다.
12. 프런트엔드 서브넷은 인바운드 규칙 처리를 시작합니다.
    1. 백 엔드 서브넷에서 프런트엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
    2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙이 이 트래픽을 허용하므로 트래픽이 허용됩니다.
13. IIS 서버는 SQL 응답을 수신하고 HTTP 응답을 완료하여 요청자에게 보냅니다.
14. 방화벽으로부터 NAT 세션이므로 응답 대상(처음)은 방화벽을 위한 것입니다.
15. 방화벽은 웹 서버로부터 응답을 받아 인터넷 사용자에게 다시 전달합니다.
16. 응답이 허용되는 프런트엔드 서브넷에 아웃바운드 규칙이 없으므로 인터넷 사용자는 요청된 웹 페이지를 수신합니다.

#### <a name="allowed-rdp-to-backend"></a>(허용) RDP - 백 엔드
1. 인터넷의 서버 관리자는 BackEnd001.CloudApp.Net:xxxxx에서 AppVM01에 대해 RDP 세션을 요청합니다.여기서 xxxxx는 RDP에 대해 AppVM01로 임의로 할당된 포트 번호입니다(할당된 포트는 Azure 포털 또는 PowerShell을 통해 확인할 수 있음).
2. 방화벽은 FrontEnd001.CloudApp.Net 주소만 수신 대기하므로 이 트래픽 흐름에는 포함되지 않습니다.
3. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 아웃바운드 규칙이 없는 경우 기본 규칙이 적용되고 반환 트래픽이 허용됩니다.
5. RDP 세션이 사용하도록 설정됩니다.
6. AppVM01에서 사용자 이름과 암호를 묻습니다.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(허용) DNS 서버에서 웹 서버 DNS 조회
1. 웹 서버인 IIS01은 www.data.gov에서 데이터 피드를 요구하지만 주소를 확인해야 합니다.
2. VNet에 대한 네트워크 구성에서 DNS01(백 엔드 서브넷에서 10.0.2.4)을 주 DNS 서버로 나열하며 IIS01은 DNS01로 DNS 요청을 보냅니다.
3. 프런트 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
4. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
5. DNS 서버는 요청을 수신합니다.
6. DNS 서버에는 캐시된 주소가 없고 인터넷에서 루트 DNS 서버를 요청합니다.
7. 백 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
8. 이 세션은 내부적으로 시작되었으므로 인터넷 DNS 서버가 응답하고 응답이 허용됩니다.
9. DNS 서버는 응답을 캐시하고 IIS01에 대한 초기 요청에 다시 응답합니다.
10. 백 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
11. 프런트엔드 서브넷은 인바운드 규칙 처리를 시작합니다.
    1. 백 엔드 서브넷에서 프런트 엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
    2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙에서 이 트래픽을 허용하므로 트래픽이 허용됩니다.
12. IIS01은 DNS01에서 응답을 수신합니다.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(허용) AppVM01에서 웹 서버가 파일 액세스
1. IIS01은 AppVM01에서 파일을 요청합니다.
2. 프런트 엔드 서브넷에 아웃바운드 규칙이 없고 트래픽이 허용됩니다.
3. 백 엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 3(인터넷에서 방화벽으로)이 적용되지 않고 다음 규칙으로 이동합니다.
   4. NSG 규칙 4(IIS01에서 AppVM01로)가 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. AppVM01이 요청을 받아 파일로 응답합니다(액세스 권한이 부여된 것으로 가정).
5. 백 엔드 서브넷에 아웃바운드 규칙이 없으므로 응답이 허용됩니다.
6. 프런트엔드 서브넷은 인바운드 규칙 처리를 시작합니다.
   1. 백 엔드 서브넷에서 프런트엔드 서브넷으로 인바운드 트래픽에 적용되는 NSG 규칙이 없으므로 NSG 규칙이 적용되지 않습니다.
   2. 서브넷 간의 트래픽을 허용하는 기본 시스템 규칙이 이 트래픽을 허용하므로 트래픽이 허용됩니다.
7. IIS 서버가 파일을 수신합니다.

#### <a name="denied-web-direct-to-web-server"></a>(거부) 웹에서 웹 서버로 직접
웹 서버인 IIS01과 방화벽은 동일한 클라우드 서비스에 있으므로 동일한 공용 연결 IP 주소를 공유합니다. 따라서 HTTP 트래픽은 방화벽에 전달됩니다. 요청은 성공적으로 처리되지만 웹 서버로 직접 이동할 수 없으며 지정된 대로 방화벽을 통해 먼저 전달됩니다. 트래픽 흐름은 이 섹션의 첫 번째 시나리오를 참조하세요.

#### <a name="denied-web-to-backend-server"></a>(거부) 웹 - 백 엔드 서버
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 AppVM01에서 파일에 액세스하려고 합니다.
2. 파일 공유를 위해 열린 끝점이 없으므로 클라우드 서비스를 전달하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 NSG 규칙 5(인터넷에서 VNet으로)가 이 트래픽을 차단합니다.

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(거부) DNS 서버에서 웹 DNS 조회
1. 인터넷 사용자가 BackEnd001.CloudApp.Net 서비스를 통해 DNS01에서 내부 DNS 레코드를 조회하려고 합니다.
2. DNS를 위해 열린 끝점이 없으므로 클라우드 서비스를 전달하지 않고 서버에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 NSG 규칙 5(인터넷에서 VNet으로)가 이 트래픽을 차단합니다(참고: 규칙 1(DNS)은 두 가지 이유로 적용되지 않습니다. 첫째, 원본 주소가 인터넷입니다. 이 규칙은 로컬 VNet에만 원본으로 적용됩니다. 또한 이는 허용 규칙이므로 트래픽을 거부하지 않습니다.)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(거부) 방화벽을 통해 웹에서 SQL 액세스
1. 인터넷 사용자가 FrontEnd001.CloudApp.Net에서 SQL 데이터를 요청합니다(인터넷 연결 클라우드 서비스).
2. SQL을 위해 열린 끝점이 없으므로 클라우드 서비스를 전달하지 않고 방화벽에 도달하지 않습니다.
3. 어떤 이유로 끝점이 열린 경우 프런트엔드 서브넷이 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1(DNS)이 적용되지 않고 다음 규칙으로 이동합니다.
   2. NSG 규칙 2(RDP)가 적용되지 않고 다음 규칙으로 이동합니다.
   3. NSG 규칙 2(인터넷에서 방화벽으로)이 적용되고 트래픽이 허용되며 규칙 처리를 중지합니다.
4. 트래픽이 방화벽의 내부 IP 주소를 호출합니다(10.0.1.4).
5. 방화벽에는 SQL에 대한 전달 규칙이 없고 트래픽을 삭제합니다.

## <a name="conclusion"></a>결론
이 방법은 방화벽으로 응용 프로그램을 보호하고 백 엔드 서브넷을 인바운드 트래픽과 격리하는 비교적 직접적인 방법입니다.

네트워크 보안 경계에 대한 더 많은 예와 개요는 [여기][HOME]에서 찾을 수 있습니다.

## <a name="references"></a>참조
### <a name="main-script-and-network-config"></a>기본 스크립트 및 네트워크 구성
PowerShell 스크립트 파일에 전체 스크립트를 저장합니다. 네트워크 구성을 "NetworkConf2.xml"이라는 파일에 저장합니다.
필요에 따라 사용자 정의 변수를 수정합니다. 스크립트를 실행한 다음 위의 방화벽 규칙 설정 지침을 따릅니다.

#### <a name="full-script"></a>전체 스크립트
이 스크립트는 사용자 정의 변수를 기반으로 합니다.

1. Azure 구독에 연결
2. 새 저장소 계정 만들기
3. 네트워크 구성 파일에 정의된 대로 새 VNet 및 두 서브넷 만들기
4. 4개의 Windows Server VM 빌드
5. 다음을 포함하여 NSG 구성
   * NSG 만들기
   * 규칙으로 채우기
   * 적절한 서브넷에 대해 NSG 바인딩

이 PowerShell 스크립트를 인터넷 연결된 PC 또는 서버에서 로컬로 실행해야 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하는 경우 PowerShell에서 경고 또는 기타 정보 메시지가 표시될 수 있습니다. 빨간색 오류 메시지만 심각한 문제입니다.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG와 인바운드 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "대상 NAT 아이콘"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "방화벽 규칙"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "방화벽 규칙 활성화"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
