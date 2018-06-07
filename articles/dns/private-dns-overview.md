---
title: 개인 도메인에 Azure DNS 사용 | Microsoft Docs
description: Microsoft Azure의 개인 DNS 호스팅 서비스에 대한 개요입니다.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 677fc66b66d6c17806a313f2fac3a15e8e1775ba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261582"
---
# <a name="using-azure-dns-for-private-domains"></a>개인 도메인에 Azure DNS 사용
DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다.  인터넷 연결 DNS 도메인 외에도 Azure DNS는 이제 사설 DNS 도메인을 미리 보기 기능으로 지원합니다.  
 
Azure DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 사설 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다.  사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크 내에서 그리고 가상 네트워크 간에 이름을 확인할 수 있게 해줍니다. 또한 분할 수평 보기로 영역을 구성하여 사설 및 공용 DNS 영역에서 동일한 이름을 공유할 수 있습니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>이점

* **사용자 지정 DNS 솔루션이 필요 없습니다.** 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다.  이제는 Azure의 기본 인프라를 사용하여 DNS 영역 관리를 수행할 수 있으므로 사용자 지정 DNS 솔루션을 만들고 관리해야 하는 부담이 사라졌습니다.

* **모든 공용 DNS 레코드 형식을 사용합니다.**  Azure DNS는 A, AAAA, CNAME, MX, NS, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리.** Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다.  따라서 사용자 지정 DNS 솔루션을 만들거나 응용 프로그램을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인.** Azure에서 제공하는 호스트 이름과는 달리, 사설 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다.  이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경.** 이 새로운 기능은 학습 곡선을 단축할 수 있도록 기존의 친숙한 Azure DNS 도구(PowerShell, Resource Manager 템플릿, REST API)를 사용합니다.

* **수평 분할 DNS 지원.** Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다.  분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.

* **모든 Azure 지역에서 사용 가능** Azure DNS Private Zones는 Azure 공용 클라우드의 모든 Azure 지역에서 사용할 수 있습니다. 


## <a name="capabilities"></a>기능 
* 등록 가상 네트워크로 개인 영역에 연결된 단일 가상 네트워크에서 가상 머신 자동 등록. 가상 머신이 개인 IP를 가리키는 A 레코드로 개인 영역에 등록(추가)됩니다. 또한 등록 가상 네트워크의 가상 머신이 삭제되면 Azure가 연결된 개인 영역에서 해당 DNS 레코드도 자동으로 제거합니다. 또한 영역에 대한 DNS 확인이 가상 네트워크 내의 모든 가상 머신에서 작동한다는 점에서 등록 가상 네트워크는 기본적으로 확인 가상 네트워크 역할을 합니다. 
* 확인 가상 네트워크로 개인 영역에 연결된 가상 네트워크 전체에서 지원되는 정방향 DNS 확인. 가상 네트워크 간 DNS 확인의 경우 가상 네트워크가 서로 연결되는 명시적 종속성은 없습니다. 그러나 고객이 다른 시나리오(예: HTTP 트래픽)를 위해 가상 네트워크를 연결할 수도 있습니다.
* VNET 범위 내에서 지원되는 역방향 DNS 조회. 개인 영역에 할당된 가상 네트워크 내의 개인 IP에 대한 역방향 DNS 조회는 호스트/레코드 이름 및 접미사로 영역 이름을 포함하는 FQDN을 반환합니다. 


## <a name="limitations"></a>제한 사항
* 개인 영역당 1개의 등록 가상 네트워크
* 개인 영역당 최대 10개의 확인 가상 네트워크
* 지정된 가상 네트워크가 1개의 개인 영역에만 등록 가상 네트워크로 연결될 수 있습니다.
* 지정된 가상 네트워크가 최대 10개의 개인 영역에 확인 가상 네트워크로 연결될 수 있습니다.
* 등록 가상 네트워크가 지정된 경우, 개인 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Powershell/CLI/API에서 보거나 검색할 수 없지만 VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.
* 역방향 DNS는 등록 가상 네트워크의 개인 IP 공간에 대해서만 작동합니다.
* 개인 영역에 등록되지 않은 개인 IP(예: 확인 가상 네트워크로 개인 영역에 연결된 가상 네트워크에 있는 가상 머신의 개인 IP)에 대한 역방향 DNS는 DNS 접미사로 “internal.cloudapp.net”를 반환하지만 이 접미사는 확인할 수 없습니다.   
* 가상 네트워크가 처음에 등록 또는 확인 가상 네트워크로 개인 영역에 연결할 때는 비어 있어야 합니다(VM 레코드 없음). 그러나 가상 네트워크가 나중에 등록 또는 확인 가상 네트워크로 다른 개인 영역에 연결할 때는 비어 있지 않을 수 있습니다. 
* 이때 Azure와 온-프레미스 네트워크 간에 확인이 가능하도록 조건부 전달은 지원되지 않습니다. 고객이 다른 메커니즘을 통해 이 시나리오를 실현할 수 있는 방법에 대한 설명서는 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

또한 특정 종류의 작업에 필요한 특정 DNS 등록 및 확인 동작을 포함하여 Azure DNS의 개인 영역에 대한 몇 가지 일반적인 질문과 대답을 [FAQ](./dns-faq.md#private-dns)에서 읽어보는 것이 좋습니다. 


## <a name="pricing"></a>가격

공개 미리 보기 기간에는 사설 DNS 영역을 무료로 사용할 수 있습니다. 일반 공급 기간에는 이 기능은 기존 Azure DNS 제품과 비슷한 사용량 기반 가격 책정 모델을 사용합니다. 


## <a name="next-steps"></a>다음 단계

[PowerShell](./private-dns-getstarted-powershell.md) 또는 [CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 개인 영역을 만드는 방법을 알아봅니다.

Azure DNS에서 개인 영역으로 실현할 수 있는 몇 가지 일반적인 시나리오를 [개인 영역 시나리오](./private-dns-scenarios.md)에서 확인합니다.

특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 개인 영역에 대한 몇 가지 일반적인 질문과 대답을 [FAQ](./dns-faq.md#private-dns)에서 확인합니다. 

[DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

