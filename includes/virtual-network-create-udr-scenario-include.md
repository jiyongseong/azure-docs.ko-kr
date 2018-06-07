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
ms.openlocfilehash: b91ae155761f6357e286f4742d57b97cf96d909a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>시나리오
UDR을 만드는 방법을 보다 잘 설명하기 위해 이 문서에서는 다음 시나리오를 사용합니다.

![이미지 설명](./media/virtual-network-create-udr-scenario-include/figure1.png)

이 시나리오에서는 다음과 같이 *프런트 엔드 서브넷*에 대해 하나의 UDR을 만들고 *백 엔드 서브넷*에 대해 다른 UDR을 만듭니다. 

* **UDR-FrontEnd**. 프런트 엔드 UDR은 *FrontEnd* 서브넷에 적용되며 다음 한 개의 경로를 포함합니다.    
  * **RouteToBackend**. 이 경로는 모든 트래픽을 백 엔드 서브넷으로, 다시 **FW1** 가상 머신으로 보냅니다.
* **UDR-BackEnd**. 백 엔드 UDR은 *BackEnd* 서브넷에 적용되며 다음 한 개의 경로를 포함합니다.    
  * **RouteToFrontend**. 이 경로는 모든 트래픽을 프런트 엔드 서브넷으로, 다시 **FW1** 가상 머신으로 보냅니다.

이러한 경로의 조합은 한 서브넷에서 다른 서브넷으로 보내는 모든 트래픽이 가상 어플라이언스로 사용되는 **FW1** 가상 머신으로 경로 지정되도록 합니다. 또한 **FW1** VM에 대한 IP 전달을 설정하여 다른 VM으로 보내는 트래픽을 수신할 수 있도록 해야 합니다.

