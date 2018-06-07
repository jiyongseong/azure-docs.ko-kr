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
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31805107"
---
## <a name="scenario"></a>시나리오
이 문서에서는 특정 시나리오에서 VM에 다중 NIC를 사용하는 배포를 살펴봅니다. 이 시나리오에는 Azure에 호스트되는 2계층 IaaS 워크로드가 있습니다. 각 계층은 가상 네트워크(VNet)의 자체 서브넷에 배포됩니다. 프런트 엔드 계층은 고가용성을 위해 부하 분산 장치 집합으로 그룹화되어 있는 몇 개의 웹 서버로 구성됩니다. 백 엔드 계층은 몇 개의 데이터베이스 서버로 구성됩니다. 데이터베이스 서버는 각각 2개의 NIC(데이터베이스 액세스를 위한 NIC와 관리를 위한 NIC)와 배포됩니다. 시나리오에는 배포에서 각 서브넷 및 NIC에 허용되는 트래픽을 제어하기 위한 NSG(네트워크 보안 그룹)도 포함됩니다. 다음 그림은 이 시나리오의 기본 아키텍처를 보여줍니다.

![MultiNIC 시나리오](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

