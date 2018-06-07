---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>시나리오
NSG를 만드는 방법을 보다 잘 설명하기 위해 이 문서에서는 다음 시나리오를 사용합니다.

![VNet 시나리오](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

이 시나리오에서는 다음과 같이 **TestVNet** 가상 네트워크의 각 서브넷에 대한 NSG를 만듭니다. 

* **NSG-FrontEnd**. 프런트 엔드 NSG는 *FrontEnd* 서브넷에 적용되며 다음 두 개의 규칙을 포함합니다.    
  * **rdp-rule**. *FrontEnd* 서브넷에 대한 RDP 트래픽을 허용합니다.
  * **web-rule**. *FrontEnd* 서브넷에 대한 HTTP 트래픽을 허용합니다.
* **NSG-BackEnd**. 백 엔드 NSG는 *BackEnd* 서브넷에 적용되며 다음 두 개의 규칙을 포함합니다.    
  * **sql-rule**. *FrontEnd* 서브넷의 SQL 트래픽만 허용합니다.
  * **web-rule**. *BackEnd* 서브넷의 모든 인터넷 관련 트래픽을 거부합니다.

이러한 규칙의 조합은 DMZ와 비슷한 시나리오를 만듭니다. 이 경우 백 엔드 서브넷은 프런트 엔드 서브넷의 SQL에 대해 들어오는 트래픽만 수신할 수 있고 인터넷에 액세스할 수 없는 반면 프런트 엔드 서브넷은 인터넷과 통신하고 들어오는 HTTP 요청만 수신할 수 있습니다.

