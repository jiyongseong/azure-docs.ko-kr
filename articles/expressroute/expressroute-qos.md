---
title: "ExpressRoute에 대한 QoS 요구 사항 | Microsoft Docs"
description: "이 페이지는 Express 경로 회로에 QoS를 구성하고 관리하는 자세한 요구 사항을 제공합니다."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: db1c1447-0283-4a09-907b-ae481adc40c7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: cherylmc
ms.openlocfilehash: c097a9ccba91f59b323215d42d37e6d85e0981ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-qos-requirements"></a>Express 경로 QoS 요구 사항
비즈니스용 Skype에는 차별화된 QoS 처리를 필요로 하는 다양한 워크로드가 있습니다. Express 경로를 통해 음성 서비스를 사용하려면 아래에 설명한 요구 사항을 준수해야 합니다.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS 요구 사항은 Microsoft 피어링에만 적용합니다. Azure 공용 피어링 및 Azure 개인 피어링에서 수신하는 네트워크 트래픽의 DSCP 값은 0으로 다시 설정됩니다. 
> 
> 

다음 테이블에서는 비즈니스용 Skype에서 사용하는 DSCP 표시의 목록을 제공합니다. 자세한 내용은 [비즈니스용 Skype에 대한 QoS 관리](https://technet.microsoft.com/library/gg405409.aspx) 를 참조합니다.

| **트래픽 클래스** | **처리(DSCP 표시)** | **비즈니스 워크로드용 Skype** |
| --- | --- | --- |
| **음성** |EF (46) |Skype/Lync 음성 |
| **대화형** |AF41 (34) |비디오, VBSS |
| AF21 (18) |앱 공유 | |
| **기본값** |AF11 (10) |파일 전송 |
| CS0 (0) |다른 항목 | |

* 워크로드를 분류하고 올바른 DSCP 값을 표시해야 합니다. 네트워크에서 DSCP 표시를 설정하는 방법은 [여기](https://technet.microsoft.com/library/gg405409.aspx) 에 제공된 가이드를 따릅니다.
* 네트워크 내에서 여러 QoS 큐를 구성하고 지원해야 합니다. 음성은 독립 실행형 클래스이고 RFC 3246에 지정된 EF 처리를 받아야 합니다. 
* 큐 메커니즘, 정체 감지 정책 및 트래픽 클래스 당 대역폭 할당을 결정할 수 있습니다. 하지만 비즈니스 워크로드용 Skype에 대한 DSCP 표시를 유지해야 합니다. AF31 (26)와 같이 위에 나열되지 않은 DSCP 표시를 사용하면 패킷을 Microsoft에 보내기 전에 이 DSCP 값을 0으로 다시 작성해야 합니다. Microsoft는 위의 테이블에 나와 있는 DSCP 값으로 표시되는 패킷을 보냅니다. 

## <a name="next-steps"></a>다음 단계
* [라우팅](expressroute-routing.md) 및 [NAT](expressroute-nat.md)에 대한 요구 사항을 참조합니다.
* Express 경로 연결을 구성하려면 다음 링크를 참조하세요.
  
  * [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md)
  * [라우팅 구성](expressroute-howto-routing-classic.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-classic.md)

