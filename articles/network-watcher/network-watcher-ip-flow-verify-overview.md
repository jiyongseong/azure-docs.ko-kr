---
title: Azure Network Watcher에서 IP 흐름 확인 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher IP 흐름 확인 기능에 대한 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181602"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure Network Watcher에서 IP 흐름 확인 소개

IP 흐름 확인은 가상 머신 간 패킷이 허용 또는 거부되는지를 확인합니다. 해당 정보는 방향, 프로토콜, 로컬 IP, 원격 IP, 로컬 포트 및 원격 포트로 구성됩니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙의 이름이 반환됩니다. 모든 원본 또는 대상 IP를 선택할 수 있는 동안 IP 흐름 확인을 통해 관리자는 인터넷 간 및 온-프레미스 환경 간 연결 문제를 신속하게 진단할 수 있습니다.

IP 흐름 확인은 가상 머신의 네트워크 인터페이스를 대상으로 합니다. 그런 다음 해당 네트워크 인터페이스 간에서 구성된 설정에 따라 트래픽 흐름이 확인됩니다. IP 흐름 확인은 네트워크 보안 그룹에서 규칙이 가상 머신 간의 수신 또는 송신 트래픽을 차단하는 경우를 확인하는 데 유용합니다.

Network Watcher의 인스턴스는 IP 흐름을 실행하려는 모든 지역에서 만들어져야 합니다. Network Watcher는 지역 서비스이며 동일한 지역의 리소스에 대해서만 실행할 수 있습니다. NIC와 연결된 경로는 여전히 반환되므로 사용되는 인스턴스는 IP 흐름 확인의 결과에 영향을 주지 않습니다.

![1][1]

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 포털을 통해 특정 가상 머신에 대해 패킷이 허용되거나 거부되는 경우를 알아봅니다. [포털을 사용하여 IP 흐름 확인으로 VM에서 트래픽이 허용되는지 확인](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












