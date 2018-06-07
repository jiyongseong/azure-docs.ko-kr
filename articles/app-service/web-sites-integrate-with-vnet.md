---
title: Azure Virtual Network에 앱 통합
description: 신규 또는 기존 Azure 가상 네트워크에 Azure App Service의 앱을 연결하는 방법을 보여 줍니다.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 83f5c64926eb9b718463c415a5478af374245f31
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure Virtual Network에 앱 통합
이 문서는 Azure App Service 가상 네트워크 통합 미리 보기 기능을 설명하고 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)에서 앱에 해당 기능을 설정하는 방법을 보여줍니다. Azure Virtual Network(VNet)는 인터넷에서 사용할 수 없고 라우팅할 있는 네트워크(액세스를 제어하는)에 다수의 Azure 리소스를 배치할 수 있도록 하는 기능입니다. 이러한 네트워크는 다양한 VPN 기술을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. Azure Virtual Network에 대해 자세히 알아보려면 [Azure Virtual Network 개요][VNETOverview]부터 참조하세요. 

Azure App Service에는 두 가지 형태가 있습니다. 

1. 요금제 전체를 지원하는 다중 테넌트 시스템
2. VNet에 배포되는 ASE(App Service Environment) 프리미엄 기능 

이 문서는 App Service 환경이 아니라 VNet 통합 과정에 대해 설명합니다. ASE 기능에 대해 자세히 알아보려면 [App Service 환경 소개][ASEintro]의 정보부터 살펴봅니다.

VNet 통합은 가상 네트워크의 리소스에 대한 액세스를 웹앱에 부여하지만, 가상 네트워크로부터 웹앱에 대한 개인 액세스는 허용하지 않습니다. 개인 사이트 액세스는 Azure Virtual Network와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 합니다. 개인 사이트 액세스는 ILB(내부 부하 분산 장치)로 ASE가 구성된 상태에서만 사용할 수 있습니다. ILB ASE 사용에 대한 자세한 내용을 보려면 [ILB ASE 만들기 및 사용][ILBASE] 문서부터 살펴봅니다. 

VNet 통합을 사용할만한 일반적인 시나리오는 Azure 가상 네트워크의 가상 머신에서 실행되는 웹 서비스 또는 데이터베이스를 웹앱에서 액세스할 수 있도록 설정하는 경우입니다. VNet 통합을 사용하면 VM의 응용 프로그램에 대한 공개 끝점을 노출할 필요가 없지만 인터넷에서 사용할 수 없고 라우팅할 있는 개인 주소를 대신 사용할 수 있습니다. 

VNet 통합 기능에는 다음이 적용됩니다.

* 표준, 프리미엄 또는 격리 요금제가 필요합니다. 
* 클래식 또는 Resource Manager VNet에서 작동합니다. 
* TCP 및 UDP를 지원합니다.
* 웹, 모바일, API 앱 및 함수 앱에서 작동합니다.
* 앱이 한 번에 1개의 VNet에만 연결할 수 있도록 합니다.
* App Service 계획에 VNet을 최대 5개까지 통합할 수 있도록 합니다. 
* App Service 계획에 여러 개의 앱이 동일한 VNet을 사용하도록 허용합니다.
* VNet 게이트웨이의 SLA 때문에 SLA를 99.9% 지원합니다.

VNet 통합이 지원하지 않는 사항에는 다음과 같은 내용이 포함됩니다.

* 드라이브 탑재
* AD 통합 
* NetBios
* 개인 사이트 액세스

### <a name="getting-started"></a>시작
웹앱을 가상 네트워크에 연결하기 전에 다음의 몇 가지 사항에 유의해야 합니다.

* VNet 통합은 **표준**, **프리미엄** 또는 **격리** 요금제에 속하는 앱에서만 작동합니다. 이 기능을 사용하도록 설정한 후에 지원되는 않는 요금제로 App Service 계획을 확장하면 앱에서 사용하는 VNet에 대한 연결이 손실됩니다. 
* 대상 가상 네트워크가 이미 있는 경우 동적 라우팅 게이트웨이와의 지점과 사이트 간 VPN을 사용하도록 설정해야 해당 가상 네트워크를 웹앱에 연결할 수 있습니다. 게이트웨이가 정적 라우팅을 사용하도록 구성된 경우에는 지점 및 사이트 간 VPN(가상 사설망)을 사용하도록 설정할 수 없습니다.
* VNet은 ASP(App Service 계획)와 동일한 구독 내에 있어야 합니다.
* 지점 및 사이트 간 사용 가능한 게이트웨이가 이미 존재하고, 기본 SKU에 없는 경우 지점 및 사이트 간 구성에서 IKEV2를 비활성화해야 합니다.
* VNet과 통합되는 앱은 VNet에 대해 지정된 DNS를 사용합니다.
* 기본적으로 통합하는 앱은 VNet에 정의된 경로를 기반으로 트래픽을 VNet에 라우팅합니다. 

## <a name="enabling-vnet-integration"></a>VNet 통합 사용

앱을 새 가상 네트워크나 기존 가상 네트워크에 연결할 수 있습니다. VNet 생성 외에도 통합의 일환으로 네트워크를 새로 만든 경우, 동적 라우팅 게이트웨이가 미리 구성되고 지점과 사이트 간 VPN을 사용하도록 설정됩니다. 

> [!NOTE]
> 새 가상 네트워크를 구성하려면 몇 분 정도 걸릴 수 있습니다. 
> 
> 

VNet 통합을 사용하도록 설정하려면 앱 설정을 열고 네트워킹을 선택합니다. 표시되는 UI에 세 가지 네트워킹 옵션이 제공됩니다. 이 가이드에서는 VNet 통합에서 하이브리드 연결까지만 다루고 App Service 환경은 이 문서의 뒤쪽에서 다루겠습니다. 

앱이 올바른 요금제에 속하지 않는 경우에는 높은 요금제를 선택하여 요금제를 확대할 수 있도록 UI가 제공됩니다.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>기존 VNet을 사용하여 VNet 통합 사용
VNet 통합 UI를 사용하여 사용자의 VNet 목록에서 항목을 선택할 수 있습니다. 클래식 VNet은 VNet 이름 옆에 있는 괄호 안에 "클래식"이라고 표시하여 클래식임을 나타냅니다. 목록은 Resource Manager VNet이 먼저 나오도록 정렬됩니다. 아래 이미지에서 선택할 수 있는 VNet이 하나만 있는 것을 볼 수 있습니다. VNet이 회색으로 표시되는 이유는 다양하며 다음과 같은 경우가 이유에 포함됩니다.

* VNet이 계정이 액세스 권한을 갖는 구독이 아닌 다른 구독에 속하는 경우
* VNet에 지점과 사이트 간이 사용되도록 설정되지 않은 경우
* VNet에 동적 라우팅 게이트웨이가 없는 경우

![][2]

통합을 사용하도록 설정하려면 통합하려는 VNet을 클릭합니다. VNet을 선택한 후에는 변경 내용이 적용되도록 앱이 자동으로 다시 시작됩니다. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>클래식 VNet에서 지점과 사이트 간 사용
VNet에 게이트웨이가 없거나 지점과 사이트 간이 사용되도록 설정되어 있지 않은 경우에는 먼저 이러한 항목을 설정해야 합니다. 클래식 VNet에 대해 활성화하려면 [Azure Portal][AzurePortal]로 이동하여 Virtual Network(클래식) 목록을 표시합니다. 여기에서 통합하려는 네트워크를 클릭하고 필수 아래에서 VPN 연결이라는 큰 상자를 클릭합니다. 여기에서 지점과 사이트 간 VPN을 만들 수 있고 게이트웨이도 만들 수 있습니다. 지점과 사이트 간 및 게이트웨이를 만드는 환경을 모두 거치고 나면, 준비가 되기까지 약 30분이 남게 됩니다. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Resource Manager VNet에서 지점과 사이트 간 사용
게이트웨이 및 지점-사이트 간 연결을 사용하여 Resource Manager VNet을 구성하려면 [PowerShell을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성][V2VNETP2S]에 설명된 대로 PowerShell을 사용하거나 [Azure Portal을 사용하여 VNet에 지점 및 사이트 간 연결 구성][V2VNETPortal]에 설명된 대로 Azure Portal을 사용할 수 있습니다. 이 기능을 수행하는 UI는 아직 제공되지 않습니다. 지점과 사이트 간 구성에 대한 인증서를 만들 필요가 없습니다. 이 인증서는 WebApp을 VNet에 연결할 때 자동으로 구성됩니다. 

### <a name="creating-a-pre-configured-vnet"></a>미리 구성된 VNet 만들기
게이트웨이 및 지점과 사이트 간을 통해 구성되는 새 VNet을 만들려는 경우 App Service 네트워킹 UI의 기능을 사용하면 됩니다. 단, 이 기능은 Resource Manager VNet에만 해당됩니다. 클래식 게이트웨이 및 지점과 사이트 간을 사용하여 클래식 VNet을 만들려면 네트워킹 사용자 인터페이스를 통해 이 작업을 수동으로 진행해야 합니다. 

VNet 통합 UI를 통해 Resource Manager VNet을 만들려면 **새 Virtual Network 만들기**를 클릭하고 다음 정보를 입력하면 됩니다.

* Virtual Network 이름
* Virtual Network 주소 블록
* 서브넷 이름
* 서브넷 주소 블록
* 게이트웨이 주소 블록
* Point to Site(지점과 사이트 간) 주소 블록

VNet을 다른 네트워크에 연결하려는 경우에는 해당 네트워크와 겹치는 IP 주소 공간을 선택하지 않도록 주의해야 합니다. 

> [!NOTE]
> 게이트웨이를 사용하여 Resource Manager VNet을 만드는 작업은 약 30분 정도 소요되며 현재는 VNet은 앱과 통합되지 않습니다. 게이트웨이를 사용하여 VNet을 만든 후에는 앱 VNet 통합 UI로 돌아와서 새 VNet을 선택해야 합니다.
> 
> 

![][3]

Azure VNet은 일반적으로 개인 네트워크 주소 내에 만들어집니다. 기본적으로 VNet 통합 기능은 이러한 IP 주소 범위를 대상으로 하는 모든 트래픽을 VNet으로 라우팅합니다. 개인 IP 주소 범위는 다음과 같습니다.

* 10.0.0.0/8 - 10.0.0.0 - 10.255.255.255와 같습니다.
* 172.16.0.0/12 - 172.16.0.0 - 172.31.255.255와 같습니다. 
* 192.168.0.0/16 - 192.168.0.0 - 192.168.255.255와 같습니다.

VNet 주소 공간은 CIDR 표기법으로 지정되어야 합니다. CIDR 표기법은 IP 주소와 네트워크 마스크를 나타내는 정수를 사용하여 주소 블록을 지정하는 방법입니다. 참고로, 10.1.0.0/24는 256개의 주소가 되고 10.1.0.0/25는 128개의 주소가 됩니다. /32를 포함하는 IPv4 주소는 1개의 주소입니다. 

여기에서 DNS 서버 정보를 설정하면 같은 내용이 VNet에 대해서도 설정됩니다. VNet을 만든 후에는 VNet 사용자 환경에서 정보를 변경할 수 있습니다. VNet의 DNS를 변경하는 경우 네트워크 동기화 작업을 수행해야 합니다.

VNet 통합 UI를 사용하여 클래식 VNet을 만들면, 앱과 동일한 리소스 그룹에 VNet이 생성됩니다. 

## <a name="how-the-system-works"></a>시스템 작동 방식
이 기능은 내부적으로 지점 및 사이트 간 VPN 기술을 기반으로 VNet에 앱을 연결합니다. Azure App Service에 속하는 앱은 VNet에서 앱을 바로 프로비저닝 할 수 없도록 하는(가상 머신을 통해 수행되기 때문에) 다중 테넌트 시스템 아키텍처를 갖습니다. 지점 및 사이트 간 기술을 기반으로 구축되었기 때문에 네트워크 액세스는 앱을 호스트하는 가상 머신으로만 제한됩니다. 또한 해당 앱 호스트에서도 네트워크 액세스가 추가로 제한되므로 앱은 액세스 가능하도록 구성된 네트워크에만 액세스할 수 있습니다. 

![][4]

가상 네트워크로 DNS 서버를 구성하지 않은 경우에는 IP 주소를 사용해야만 앱이 VNet의 리소스에 도달할 수 있습니다. IP 주소를 사용하면서, 이 기능의 주요 혜택이 사용자의 개인 네트워크에 속하는 개인 주소를 사용할 수 있도록 하는 점이라는 것을 기억합니다. VM 중 하나에 대해 공용 IP 주소를 사용하도록 앱을 설정하면 VNet 통합 기능을 사용하지 않게 되고 인터넷으로 통신을 하게 됩니다.

## <a name="managing-the-vnet-integrations"></a>VNet 통합 관리
VNet에 연결하고 연결을 해제하는 기능은 앱 수준에 있습니다. 다수의 앱에 대한 VNet 통합에 영향을 미칠 수 있는 작업은 ASP 수준에 있습니다. 앱 수준에 표시되는 UI를 통해 VNet에 대한 세부 정보를 얻을 수 있습니다. 대부분의 동일한 정보가 ASP 수준에도 표시됩니다. 

![][5]

네트워크 기능 상태 페이지에서 앱이 VNet에 연결되어 있는지 볼 수 있습니다. 무슨 이유로든 VNet 게이트웨이가 다운되어 있으면 연결 안 됨으로 표시됩니다. 

앱 수준 VNet 통합 UI에 제공되는 정보는 ASP에서 얻는 세부 정보와 동일합니다. 해당 항목은 다음과 같습니다.

* VNet 이름 - 이 링크는 Azure Virtual Network UI를 엽니다.
* 위치 - VNet의 위치를 반영합니다. 다른 위치의 VNet과 통합하는 것이 가능합니다.
* 인증서 상태 - 앱과 VNet 간의 VPN 연결을 보호하기 위해 인증서가 사용됩니다. 동기화되었다는 것을 확인하는 테스트를 반영합니다.
* 게이트웨이 상태 - 어떠한 이유로든 게이트웨이가 다운되면 앱은 VNet의 리소스에 액세스할 수 없습니다. 
* VNet 주소 공간 - VNet에 대한 IP 주소 공간입니다. 
* 지점과 사이트 간 주소 공간 - VNet에 대한 지점과 사이트 간 IP 주소 공간입니다. 이 주소 공간에 속하는 IP 중 하나로부터 들어오는 통신이 앱에 표시됩니다. 
* 사이트 간 주소 공간 - 사이트 간 VPN을 사용하여 VNet을 온-프레미스 리소스나 다른 VNet에 연결할 수 있습니다. 이 항목이 구성되어 있으면 해당 VPN 연결로 정의된 IP 범위가 여기에 표시됩니다.
* DNS 서버 - DNS 서버에 VNet이 구성되어 있으면 여기에 목록이 표시됩니다.
* VNet에 라우팅되는 IP - VNet에 라우팅이 정의된 IP 주소 목록이 있으며 해당 주소가 여기에 표시됩니다. 

VNet 통합의 앱 보기에 가져올 수 있는 유일한 작업은 현재 연결되어 있는 VNet과 앱의 연결을 해제하는 것입니다. 이 작업을 수행하려면 위쪽에서 Disconnect(연결 끊기)를 클릭합니다. 이 동작은 VNet을 변경하지 않습니다. VNet 및 게이트웨이를 비롯한 VNet 구성은 변경되지 않고 유지됩니다. VNet을 삭제하려면 게이트웨이를 포함하여 VNet에 속하는 리소스를 먼저 삭제해야 합니다. 

App Service 계획 보기에는 더 많은 작업이 있습니다. 앱에서 액세스하는 것과 다르게 액세스할 수 있습니다. ASP 네트워킹 UI에 액세스하려면 ASP UI를 열고 스크롤 다운합니다. Network Feature Status(네트워크 기능 상태)라는 UI 요소가 있습니다. VNet 통합에 대해 일부 세부 정보를 제공합니다. 이 UI를 클릭하면 Network Feature Status(네트워크 기능 상태) UI가 열립니다. “관리하려면 여기를 클릭”을 클릭하면 이 ASP의 VNet 통합 목록을 표시하는 UI가 열립니다.

![][6]

통합하는 VNet의 위치를 살펴보면 ASP의 위치를 기억하기 좋습니다. VNet이 또 다른 위치에 있으면 대기 시간 문제를 보게 될 가능성이 훨씬 높아집니다. 

VNet 통합 대상은 이 ASP에서 앱과 통합되어 있는 VNet의 수와 통합할 수 있는 수를 알려주는 항목입니다. 

각 VNet에 대해 추가된 세부 정보를 보려면 해당 VNet을 클릭합니다. 앞에서 언급된 세부 정보 외에 ASP 내에서 해당 VNet을 사용 중인 앱 목록이 표시됩니다. 

기능에 관해 두 가지 주요 동작이 있습니다. 첫 번째는 앱을 VNet에 유지한 채로 트래픽을 전송하는 경로를 추가할 수 있는 기능입니다. 두 번째 동작은 인증서 및 네트워크 정보를 동기화하는 기능입니다.

![][7]

**라우팅** 앞서 언급한 바와 같이, VNet에 정의된 경로는 앱에서 VNet으로 트래픽을 송신하는 데 사용되는 경로입니다. 추가적인 아웃바운드 트래픽을 앱에서 VNet으로 보내려는 고객이 있는 경우 이 기능이 제공됩니다. 그 다음 트래픽에 어떤 일이 발생하는지는 고객이 VNet을 어떻게 구성하느냐에 따라 달라집니다. 

**인증서** 인증서 상태는 VPN 연결에 사용 중인 인증서가 유효한지를 검사하기 위해 App Service에 의해 수행 중인 검사를 반영합니다. VNet 통합을 사용하도록 설정한 후에, ASP 내의 앱에서 VNet에 대해 첫 번째 통합을 수행하는 경우에는 연결에 대한 보안을 보장하기 위해 인증서 교환이 필요합니다. 인증서와 더불어 DNS 구성, 경로 및 네트워크를 설명하는 기타 유사한 데이터를 가져옵니다.
인증서나 네트워크 정보가 변경되면 “네트워크 동기화”를 클릭해야 합니다. **참고**: “네트워크 동기화”를 클릭하면 앱과 VNet 간의 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다. 

## <a name="accessing-on-premises-resources"></a>온-프레미스 리소스 액세스
VNet 통합 기능의 장점 중 하나는 VNet이 사이트 간 VPN으로 온-프레미스 네트워크에 연결되는 경우 앱에서 온-프레미스 리소스에 액세스할 수 있다는 점입니다. 하지만 이 작업을 수행하려면 온-프레미스 VPN 게이트웨이를 지점과 사이트 간 IP 범위의 경로로 업데이트하는 것이 필요할 수 있습니다. 사이트 간 VPN이 처음으로 설정되면 설정을 구성하는 데 사용된 스크립트는 지점과 사이트 간 VPN을 포함하는 경로를 설정합니다. 사이트 간 VPN을 만든 후에 지점과 사이트 간 VPN을 추가하는 경우에는 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법에 대한 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. 

> [!NOTE]
> VNet 통합 기능은 ExpressRoute 게이트웨이가 있는 VNet에 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 [동시 사용 모드][VPNERCoex]로 구성되어 있더라도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 VNet에서 실행되는 [App Service Environment][ASE]를 사용할 수 있습니다.
> 
> 

## <a name="pricing-details"></a>가격 정보
VNet 통합 기능을 사용하는 경우 가격의 미묘한 차이에 대해 몇 가지 알아 둘 필요가 있습니다. 기능 사용과 관련된 요금은 3가지입니다.

* ASP 가격 책정 계층 요구 사항
* 데이터 전송 비용
* VPN Gateway 비용

앱이 이 기능을 사용할 수 있으려면 표준 또는 프리미엄 App Service 계획에 속해야 합니다. 해당 비용에 대한 자세한 정보는 [App Service 가격][ASPricing]을 참조하세요. 

지점과 사이트 간 VPN이 처리되는 방식 때문에, VNet이 동일한 데이터 센터에 있더라도 VNet 통합 연결을 통과하는 아웃바운드 데이터에 대해 요금이 부과됩니다. 부과되는 요금이 무엇인지 보려면 [데이터 전송 가격 정보][DataPricing]를 참조하세요. 

마지막 항목은 VNet 게이트웨이 비용입니다. 사이트 간 VPN과 같은 다른 이유로 게이트웨이가 필요하지 않다면 VNet 통합 기능을 지원하기 위한 게이트웨이 비용을 지불하는 것입니다. 해당 비용에 대한 자세한 정보는 [VPN Gateway 가격][VNETPricing]을 참조하세요. 

## <a name="troubleshooting"></a>문제 해결
기능을 설정하기 쉽다고 해서 환경에 문제가 없는 것은 아닙니다. 원하는 끝점에 액세스하다가 문제가 발생하는 경우, 앱 콘솔에서 연결을 테스트하는 데 사용할 수 있는 유틸리티가 있습니다. 두 가지 콘솔 환경을 사용할 수 있습니다. 하나는 Kudu 콘솔이고 다른 하나는 Azure Portal에서 사용할 수 있는 콘솔입니다. 앱에서 Kudu 콘솔로 이동하려면 도구-> Kudu로 이동합니다. 이것은 [sitename].scm.azurewebsites.net으로 이동하는 것과 마찬가지입니다. 해당 페이지가 열리면 Debug 콘솔 탭으로 이동합니다. Azure 포털에 호스트되는 콘솔로 이동하려면 앱에서 도구 -> 콘솔로 이동합니다. 

#### <a name="tools"></a>도구
ping, nslookup tracert 도구는 보안 제약 조건 때문에 콘솔을 통해 작동되지 않습니다. 그 자리를 채우기 위해 별도의 도구가 두 개 추가되었습니다. DNS 기능을 테스트하기 위해 nameresolver.exe라는 이름의 도구를 추가했습니다. 구문은 다음과 같습니다:

    nameresolver.exe hostname [optional: DNS Server]

nameresolver를 사용하여 앱이 의존하는 호스트 이름을 확인합니다. 이렇게 하면 DNS에 잘못 구성된 항목이 있는지 또는 DNS 서버에 대한 액세스가 없는지를 테스트할 수 있습니다.

다음 도구는 호스트와 포트에 대한 TCP 연결을 테스트하는 데 사용할 수 있습니다. 이 도구의 이름은 tcpping.exe이고 구문은 다음과 같습니다.

    tcpping.exe hostname [optional: port]

특정 호스트 및 포트를 연결할 수 있는 경우 tcpping 유틸리티에서 알림이 제공됩니다. 호스트 및 포트 조합에서 수신 대기하는 응용 프로그램이 있고 네트워크를 통해 앱에서 지정된 호스트 및 포트에 액세스할 수 있는 경우에만 성공으로 표시될 수 있습니다.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet에서 호스트되는 리소스에 대한 액세스 디버깅
특정 호스트와 포트에 앱이 도달하지 못하도록 방해할만한 요소는 많이 있습니다. 대부분의 경우 다음 세 가지 중 하나입니다.

* **중간에 방화벽이 있음** 중간에 방화벽이 있는 경우 TCP 시간 초과가 발생합니다. 즉, 이 경우 21초입니다. 연결을 테스트하려면 tcpping 도구를 사용하세요. TCP 시간 초과는 방화벽 뒤에 많은 항목이 있으나 방화벽 뒤에서 시작하기 때문일 수 있습니다. 
* **DNS에 액세스할 수 없음** DNS 시간 초과는 DNS 서버당 3초입니다. DNS 서버가 2개 있으면 제한 시간은 6초입니다. DNS가 작동하는지 확인하려면 nameresolver를 사용합니다. VNet이 구성된 DNS를 사용하지 않으므로 nslookup을 사용할 수 없음에 주의하세요.
* **잘못된 P2S IP 범위** 지점과 사이트 간 IP 범위는 RFC 1918 개인 IP 범위에 있어야 합니다(10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). 이 범위를 벗어나는 IP가 사용되면 제대로 작동하지 않습니다. 

해당 항목이 문제에 응답하지 않는 경우 먼저 다음과 같이 간단한 사항을 확인합니다. 

* 게이트웨이가 포털에서 실행되고 있나요?
* 인증서가 동기화된 상태로 표시되나요?
* 누군가가 영향을 받는 ASP에서 Sync Network(네트워크 동기화)를 수행하지 않고 네트워크 구성을 변경했나요? 

게이트웨이가 다운되어 있으면 다시 가동시킵니다. 인증서가 동기화되지 않은 상태이면 VNet 통합의 ASP 보기로 이동하여 “네트워크 동기화”를 클릭합니다. VNet 구성의 내용이 변경되었고 ASP와 동기화되지 않은 것으로 의심되면 VNet 통합의 ASP 보기로 이동하여 “네트워크 동기화”를 클릭합니다. 다시 말하지만, 이렇게 하면 VNet 연결 및 앱이 잠시 중단됩니다. 

이 모든 사항에 문제가 없으면 좀 더 깊이 살펴봐야 합니다.

* 동일한 VNet에서 리소스에 도달하는 데 VNet 통합을 사용하는 다른 앱이 있나요? 
* 그 앱의 콘솔로 이동하여 tcpping을 사용해서 VNet에 속하는 다른 리소스에 도달할 수 있나요? 

위 질문에 대한 답이 참이라면 VNet 통합에는 문제가 없고 어딘가 다른 부분에 문제가 있는 것입니다. 이런 경우에는 호스트:포트에 도달할 수 없는 이유를 알 수 있는 간단한 방법이 없기 때문에 해결하기가 더 어려워집니다. 여기에 해당하는 경우는 다음과 같습니다.

* 호스트에 방화벽이 실행 중이며 지점과 사이트 간 IP 범위에서 응용 프로그램 포트에 대한 액세스를 방해합니다. 보통 교차 서브넷에는 공용 액세스가 필요합니다.
* 대상 호스트가 다운되었습니다.
* 응용 프로그램이 다운되었습니다.
* IP 또는 호스트 이름이 잘못되었습니다.
* 응용 프로그램이 예상치 않은 다른 포트를 수신 대기 중입니다. 해당 호스트로 이동하여 cmd 프롬프트에서 "netstat -aon"을 사용하면 이것을 확인할 수 있습니다. 이렇게 하면 어떤 포트에 어떤 프로세스 ID가 수신 대기 중인지를 보여줍니다. 
* 네트워크 보안 그룹이 지점과 사이트 간 IP 범위에서 응용 프로그램 호스트 및 포트에 대한 액세스를 금지하도록 구성되어 있습니다.

지점과 사이트 간 IP 범위 내에서 어떤 IP를 앱이 사용할지 모르기 때문에 전체 범위에서 액세스할 수 있도록 허용해야 합니다. 

추가적인 디버그 단계에는 다음 내용이 포함됩니다.

* VNet의 다른 VM에 로그인하여 그 위치에서 리소스 호스트:포트에 대한 액세스를 시도합니다. 이러한 용도로 사용할 수 있는 TCP ping 유틸리티가 있고 필요한 경우에는 telnet을 사용할 수도 있습니다. 여기서는 다른 VM으로부터 연결이 존재하는 지만 판단하면 됩니다. 
* 다른 VM에서 응용 프로그램을 실행하고 앱과 콘솔에서 해당 호스트와 포트에 대한 액세스를 테스트합니다.

#### <a name="on-premises-resources"></a>온-프레미스 리소스 ####
앱이 온-프레미스 리소스에 도달할 수 없는 경우 먼저 확인할 사항은 VNet에 속하는 리소스에 도달할 수 있는지 여부입니다. 도달할 수 있으면 VNet의 VM에서 온-프레미스 응용 프로그램에 액세스를 시도합니다. telnet 또는 TCP ping 유틸리티를 사용할 수 있습니다. VM이 온-프레미스 리소스에 도달할 수 없으면 사이트 간 VPN 연결이 작동하는지 확인합니다. 제대로 작동하는 경우에는, 앞서 언급한 사항을 확인하고 온-프레미스 게이트웨이 구성 및 상태를 확인합니다. 

이제 VNet 호스트된 VM이 온-프레미스 시스템에 연결할 수 있지만 앱이 연결할 수 없다면 그 이유는 다음 중 하나일 가능성이 있습니다.

* 경로를 온-프레미스 게이트웨이에서 지점과 사이트 간 IP 범위로 구성하지 않음
* 네트워크 보안 그룹에서 지점과 사이트 간 IP 범위에 대한 액세스를 차단하고 있음
* 온-프레미스 방화벽이 지점과 사이트 간 IP 범위의 트래픽을 차단하고 있음
* VNet에 지점과 사이트 간에 따른 트래픽이 온-프레미스 네트워크에 연결되지 못하도록 차단하는 UDR(사용자 정의 경로)이 있음

## <a name="powershell-automation"></a>PowerShell 자동화

PowerShell을 사용하여 App Service를 Azure Virtual Network와 통합할 수 있습니다. 실행 준비 스크립트의 경우 [Azure App Service에서 Azure Virtual Network에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조하세요.

## <a name="hybrid-connections-and-app-service-environments"></a>하이브리드 연결 및 App Service Environment
VNet에 호스트되는 리소스에 액세스할 수 있도록 하는 기능은 3가지 입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* VNet 통합
* 하이브리드 연결
* App Service Environment

하이브리드 연결을 사용하려면 네트워크에 하이브리드 연결 관리자(HCM)라고 하는 릴레이 에이전트를 설치해야 합니다. HCM이 Azure와 응용 프로그램에 연결할 수 있어야 합니다. 이 솔루션은 인터넷 액세스가 가능한 끝점을 필요로 하지 않기 때문에, 온-프레미스 네트워크 또는 다른 클라우드 호스팅 네트워크 같은 원격 네트워크에 특히 유용합니다. HCM은 Windows에서만 실행되며 고가용성을 제공하기 위해 인스턴스를 최대 5개까지 실행하도록 할 수 있습니다. 하이브리드 연결은 TCP만 지원하며 각 HC 끝점은 특정 호스트:포트 조합과 일치해야 합니다. 

App Service 환경 기능을 사용하면 VNet에서 Azure App Service 인스턴스를 실행할 수 있습니다. 이를 통해 앱은 추가적인 단계 없이 VNet의 리소스에 액세스할 수 있습니다. App Service 환경의 다른 장점은 14GB의 RAM과 함께 Dv2 기반 작업자를 사용할 수 있다는 점입니다. 또 다른 장점은 필요에 맞게 시스템의 규모를 조정할 수 있다는 점입니다. ASP가 20개 인스턴스로 제한되는 다중 테넌트 환경과 달리, ASE에서는 최대 100개 ASP 인스턴스로 확장할 수 있습니다. VNet 통합에서 얻을 수 없지만 ASE를 통해 얻을 수 있는 점은 App Service Environment가 ExpressRoute VPN에서 작동할 수 있다는 점입니다. 

일부 사용 사례가 겹치기는 하지만, 다른 환경의 기능을 대체할 수 있는 기능은 없습니다. 무엇이 필요한지에 따라 사용할 기능이 달라집니다. 예: 

* 개발자가 Azure에서 사이트를 운영하기를 원하고, 사이트에서 자신의 책상 아래 있는 워크스테이션의 데이터베이스에 액세스가 가능하도록 설정하려는 경우에 가장 간편하게 사용할 수 있는 환경은 하이브리드 연결입니다. 
* 다수의 웹 속성을 공용 클라우드에 보관해 놓고 자사의 네트워크에서 해당 속성을 관리하기를 원하는 규모가 큰 조직의 경우 App Service 환경이 적합합니다. 
* App Service에서 호스트되는 앱이 많고 VNet 내의 리소스에 액세스하기만을 원한다면 VNet 통합을 선택하는 것이 바람직합니다. 

사용 사례 외에 간결성과 관련된 측면이 있습니다. VNet이 온-프레미스 네트워크에 이미 연결되어 있는 경우 VNet 통합이나 App Service Environment를 사용하는 것이 온-프레미스 리소스를 사용하는 손쉬운 방법입니다. 반면에 VNet이 온-프레미스 네트워크에 연결되어 있지 않은 경우에는 VNet과 사이트 간 VPN을 설정하는 것이 HCM을 설치하는 것과 비교하면 비용이 훨씬 더 많이 듭니다. 

기능적인 차이 외에 가격의 차이도 있습니다. App Service Environment 기능은 프리미엄 서비스 제품이지만 대부분의 네트워크 구성 가능성을 제공하고 다른 좋은 기능도 추가적으로 제공합니다. VNet 통합은 표준 또는 프리미엄 ASP에서 사용할 수 있으며 다중 테넌트 App Service에서 VNet의 리소스를 안전하게 사용하기에 가장 적합한 환경입니다. 하이브리드 연결은 현재 BizTalk 계정에 의존하고 있으며, BizTalk의 가격 수준은 무료에서 시작해서 필요한 양에 따라 점차적으로 비용이 높아집니다. 하지만 수많은 네트워크에서 작업이 진행되는 경우, 100개가 훨씬 넘는 네트워크(분리된)의 리소스에 액세스하기에는 하이브리드 연결만한 기능이 없습니다. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
