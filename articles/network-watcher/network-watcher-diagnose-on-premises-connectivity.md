---
title: "Azure Network Watcher를 사용하여 VPN 게이트웨이를 통한 온-프레미스 연결 진단 | Microsoft Docs"
description: "이 문서에서는 Azure Network Watcher 리소스 문제 해결을 사용하여 VPN 게이트웨이를 통해 온-프레미스 연결을 진단하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e51d31035a8b05238ef0f8d13dd6b6c3f9ad02e8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>VPN Gateway를 통한 온-프레미스 연결 진단

Azure VPN Gateway를 사용하여 온-프레미스 네트워크와 Azure 가상 네트워크 간의 보안 연결에 대한 필요성을 해결하는 하이브리드 솔루션을 만들 수 있습니다. 요구 사항이 고유하므로 온-프레미스 VPN 장치의 선택도 고유합니다. Azure는 현재 장치 공급 업체와 협력하여 지속적으로 유효성이 검사되는 [여러 VPN 장치](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)를 지원합니다. 온-프레미스 VPN 장치를 구성하기 전에 장치 관련 구성 설정을 검토합니다. 마찬가지로 Azure VPN Gateway는 연결을 설정하는 데 사용되는 [지원되는 IPsec 매개 변수](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)의 집합으로 구성됩니다. 현재 Azure VPN Gateway에서 IPsec 매개 변수의 특정 조합을 지정하거나 선택하는 방법이 없습니다. 온-프레미스와 Azure 간의 성공적인 연결 설정을 위해 온-프레미스 VPN 장치 설정은 Azure VPN Gateway에서 지정한 IPsec 매개 변수를 따라야 합니다. 설정이 정확하지 않으면 연결이 손실되고 현재까지 해당 문제에 대한 해결은 간단하지 않았으며 보통 문제를 식별하고 해결하는 데 시간이 걸렸습니다.

Azure Network Watcher 문제 해결 기능을 사용하여 게이트웨이 및 연결로 모든 문제를 진단하고 몇 분 안에 문제를 해결하기 위해 정보를 바탕으로 결정을 내릴 수 있는 충분한 정보를 얻을 수 있습니다.

## <a name="scenario"></a>시나리오

온-프레미스 VPN Gateway로 FortiGate를 사용하여 Azure와 온-프레미스 간에 사이트 간 연결을 구성하려고 합니다. 이 시나리오를 달성하려면 다음 설치 프로그램이 필요합니다.

1. Virtual Network 게이트웨이 - Azure의 VPN Gateway
1. 로컬 네트워크 게이트웨이 - Azure 클라우드에서 [온-프레미스(FortiGate) VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) 표현
1. 사이트 간 연결(경로 기반) - [VPN Gateway와 온-프레미스 라우터 간의 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [FortiGate 구성](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

사이트 간 구성에 대한 자세한 단계별 지침은 [Azure Portal을 사용하여 사이트 간 연결로 VNet 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 방문하여 찾을 수 있습니다.

중요한 구성 단계 중 하나는 IPsec 통신 매개 변수를 구성하는 것이며 모든 잘못된 구성은 온-프레미스 네트워크와 Azure 간의 연결 손실로 이어집니다. 현재 Azure VPN Gateway는 1단계에 대해 다음 IPsec 매개 변수를 지원하도록 구성됩니다. 앞에서 언급했듯이 이러한 설정은 수정할 수 없습니다.  다음 표에서 볼 수 있듯이 Azure VPN Gateway에서 지원하는 암호화 알고리즘은 AES256, AES128 및 3DES입니다.

### <a name="ike-phase-1-setup"></a>IKE 1단계 설정

| **속성** | **정책 기반** | **경로 기반 및 표준 또는 고성능 VPN Gateway** |
| --- | --- | --- |
| IKE 버전 |IKEv1 |IKEv2 |
| Diffie-Hellman 그룹 |그룹 2(1024비트) |그룹 2(1024비트) |
| 인증 방법 |미리 공유한 키 |미리 공유한 키 |
| 암호화 알고리즘 |AES256 AES128 3DES |AES256 (3DES) |
| 해시 알고리즘 |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| 1단계 SA(보안 연결) 수명(시간) |28,800초 |10,800초 |

사용자로서 FortiGate를 구성해야 합니다. 샘플 구성은 [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt)에서 찾을 수 있습니다. 자신도 모르게 해시 알고리즘으로 SHA-512를 사용하도록 FortiGate를 구성했습니다. 이 알고리즘은 정책 기반 연결에 대해 지원되는 알고리즘이 아니므로 VPN 연결이 작동합니다.

이러한 문제는 해결하기 어려우며 근본 원인은 직관적이지 않은 경우가 많습니다. 이 경우 문제 해결에 대한 도움을 얻기 위해 지원 티켓을 열 수 있습니다. 그러나 Azure Network Watcher 문제 해결 API를 사용하면 이러한 문제를 직접 식별할 수 있습니다.

## <a name="troubleshooting-using-azure-network-watcher"></a>Azure Network Watcher를 사용하여 문제 해결

연결을 진단하려면 Azure PowerShell에 연결하고 `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet을 시작합니다. [Virtual Network 게이트웨이 및 연결 문제 해결 - PowerShell](network-watcher-troubleshoot-manage-powershell.md)에서 이 cmdlet 사용에 대한 세부 정보를 찾을 수 있습니다. 이 cmdlet을 완료하는 데는 몇 분 정도 걸릴 수 있습니다.

cmdlet이 완료되면 cmdlet에 지정된 저장소 위치로 이동하여 문제 및 로그에 대한 자세한 정보를 확인할 수 있습니다. Azure Network Watcher는 다음 로그 파일을 포함하는 zip 폴더를 만듭니다.

![1][1]

IKEErrors.txt라는 파일을 열면 온-프레미스 IKE 설정 잘못된 구성의 문제를 표시하는 다음 오류를 표시합니다.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

이 경우 연결이 제대로 작동하지 않게 되는 `ERROR_IPSEC_IKE_POLICY_MATCH`가 있었다고 언급하므로 Scrubbed-wfpdiag.txt에서 오류에 대한 자세한 정보를 얻을 수 있습니다.

또 다른 일반적인 잘못된 구성은 잘못된 공유 키 지정입니다. 앞의 예제에서 다른 공유 키를 지정한 경우 IKEErrors.txt는 다음과 같은 오류를 표시합니다. `Error: Authentication failed. Check shared key`

Azure Network Watcher 문제 해결 기능을 사용하면 간단한 PowerShell cmdlet의 편의성으로 VPN Gateway 및 연결을 진단하고 문제를 해결할 수 있습니다. 현재 다음 조건 진단을 지원하고 더 많은 조건을 추가하기 위해 노력하고 있습니다.

### <a name="gateway"></a>게이트웨이

| 오류 유형 | 이유 | 로그|
|---|---|---|
| NoFault | 오류가 발견되지 않은 경우를 나타냅니다. |예|
| GatewayNotFound | 게이트웨이를 찾을 수 없거나 게이트웨이가 프로비저닝되지 않았습니다. |아니요|
| PlannedMaintenance |  게이트웨이 인스턴스가 유지 관리되고 있습니다.  |아니요|
| UserDrivenUpdate | 사용자 업데이트를 진행 중인 경우를 나타냅니다. 크기 조정 작업일 수 있습니다. | 아니요 |
| VipUnResponsive | 게이트웨이의 기본 인스턴스에 연결할 수 없습니다. 상태 검색에 실패하면 발생합니다. | 아니요 |
| PlatformInActive | 플랫폼에 문제가 있습니다. | 아니요|
| ServiceNotRunning | 기본 서비스가 실행되고 있지 않습니다. | 아니요|
| NoConnectionsFoundForGateway | 게이트웨이에 연결이 존재하지 않습니다. 단지 경고일 뿐입니다.| 아니요|
| ConnectionsNotConnected | 연결이 이루어지지 않았습니다. 단지 경고일 뿐입니다.| 예|
| GatewayCPUUsageExceeded | 현재 게이트웨이 CPU 사용량이 95%를 초과했습니다. | 예 |

### <a name="connection"></a>연결

| 오류 유형 | 이유 | 로그|
|---|---|---|
| NoFault | 오류가 발견되지 않은 경우를 나타냅니다. |예|
| GatewayNotFound | 게이트웨이를 찾을 수 없거나 게이트웨이가 프로비저닝되지 않았습니다. |아니요|
| PlannedMaintenance | 게이트웨이 인스턴스가 유지 관리되고 있습니다.  |아니요|
| UserDrivenUpdate | 사용자 업데이트를 진행 중인 경우를 나타냅니다. 크기 조정 작업일 수 있습니다.  | 아니요 |
| VipUnResponsive | 게이트웨이의 기본 인스턴스에 연결할 수 없습니다. 상태 검색에 실패하면 발생합니다. | 아니요 |
| ConnectionEntityNotFound | 연결 구성이 없습니다. | 아니요 |
| ConnectionIsMarkedDisconnected | 연결이 "연결 끊김"으로 표시되었습니다. |아니요|
| ConnectionNotConfiguredOnGateway | 기본 서비스에 연결이 구성되어 있지 않습니다. | 예 |
| ConnectionMarkedStandy | 기본 서비스가 대기로 표시되었습니다.| 예|
| 인증 | 미리 공유한 키가 일치하지 않습니다. | 예|
| PeerReachability | 피어 게이트웨이에 연결할 수 없습니다. | 예|
| IkePolicyMismatch | 피어 게이트웨이에 Azure에서 지원되지 않는 IKE 정책이 있습니다. | 예|
| WfpParse 오류 | WFP 로그를 구문 분석하는 동안 오류가 발생했습니다. |예|

## <a name="next-steps"></a>다음 단계

[Azure Network Watcher 문제 해결을 사용하여 VPN 게이트웨이 모니터링](network-watcher-monitor-with-azure-automation.md)을 방문하여 PowerShell 및 Azure Automation으로 VPN Gateway 연결 확인에 대해 알아보기

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
