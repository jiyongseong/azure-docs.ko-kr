---
title: Azure 스택 차이점과 고려 사항을 네트워킹 | Microsoft Docs
description: Azure 스택에서 네트워킹을 작업할 때 차이점과 고려 사항에 알아봅니다.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: faff52ba5b5e2f0d573a67633d3a8411b2d7de74
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606429"
---
# <a name="considerations-for-azure-stack-networking"></a>Azure 스택 네트워킹에 대 한 고려 사항

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 네트워킹을 사용 하면 Azure 네트워킹에서 제공 하는 기능의 많습니다. 그러나 Azure 스택 네트워크를 배포 하기 전에 이해 해야 하는 몇 가지 주요 차이점이 있습니다.

이 문서에서는 Azure 스택 네트워킹 및 해당 기능에 대 한 고유 고려 사항에 대 한 개요를 제공 합니다. Azure 스택와 Azure 간의 대략적인 차이 대 한 자세한 내용은 [고려 사항 키](azure-stack-considerations.md) 항목입니다.

## <a name="cheat-sheet-networking-differences"></a>치트 시트: 네트워킹 차이점

|서비스 | 기능 | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | 다중 테 넌 트 DNS | 지원됨| 아직 지원 되지 않음|
| |DNS AAAA 레코드|지원됨|지원되지 않음|
| |구독 당 DNS 영역|100 (기본값)<br>요청에 증가할 수 있습니다.|100|
| |DNS 레코드는 영역 마다 설정|5000 (기본값)<br>요청에 증가할 수 있습니다.|5,000|
||영역 위임에 대 한 이름 서버|Azure는 만들어진 각 사용자 (테 넌 트) 영역에 대 한 네 가지 이름 서버를 제공 합니다.|Azure 스택 만들어진 각 사용자 (테 넌 트) 영역에 대 한 두 명의 이름 서버를 제공 합니다.|
| 가상 네트워크|가상 네트워크 피어링|Backbone Azure 네트워크를 통해 동일한 지역에 두 개의 가상 네트워크를 연결 합니다.|아직 지원 되지 않음|
| |IPv6 주소|일부분으로 IPv6 주소를 할당할 수는 [네트워크 인터페이스 구성](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)합니다.|IPv4만 지원됩니다.|
|VPN 게이트웨이|지점-사이트 VPN 게이트웨이|지원됨|아직 지원 되지 않음|
| |Vnet 대 Vnet 게이트웨이|지원됨|아직 지원 되지 않음|
| |VPN 게이트웨이 Sku|Basic, GW1, GW2, GW3, 표준 고성능, 매우 높은 성능에 대 한 지원. |Basic, Standard 및 고성능 Sku에 대 한 지원.|
|부하 분산 장치|IPv6 공용 IP 주소|부하 분산 장치에 IPv6 공용 IP 주소를 할당 하기 위한 지원 합니다.|IPv4만 지원됩니다.|
|Network Watcher|네트워크 감시자 테 넌 트 네트워크 모니터링 기능|지원됨|아직 지원 되지 않음|
|CDN|콘텐츠 배달 네트워크 프로필|지원됨|아직 지원 되지 않음|
|응용 프로그램 게이트웨이|-7 부하 분산|지원됨|아직 지원 되지 않음|
|Traffic Manager|최적의 응용 프로그램 성능과 안정성에 대 한 들어오는 트래픽을 라우팅하십시오.|지원됨|아직 지원 되지 않음|
|Express 경로|온-프레미스 인프라 또는 공동 배치 시설에서 Microsoft 클라우드 서비스에 신속 하 고 개인 연결을 설정 합니다.|지원됨|Azure 스택 Expressroute 회로에 연결 하기 위한 지원 합니다.|

## <a name="next-steps"></a>다음 단계

[Azure Stack의 DNS](azure-stack-dns.md)