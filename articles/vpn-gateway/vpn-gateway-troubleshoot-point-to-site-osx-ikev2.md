---
title: Mac OS X 클라이언트에서 Azure 지점 및 사이트 간 VPN 연결 문제 해결 | Microsoft Docs
description: P2S Mac OS X VPN 클라이언트 연결 문제 해결 단계
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Mac OS X VPN 클라이언트에서 지점 및 사이트 간 VPN 연결 문제 해결

이 문서는 기본 VPN 클라이언트와 IKEv2를 사용하여 Mac OS X에서 지점 및 사이트 간 연결 문제를 해결하는 데 도움이 됩니다. IKEv2용 Mac의 VPN 클라이언트는 매우 기본적이며, 많은 사용자 지정을 허용하지 않습니다. 확인해야 하는 네 가지 설정만 있습니다.

* 서버 주소
* 원격 ID
* 로컬 ID
* 인증 설정
* OS 버전 10.11 이상


## <a name="VPNClient"></a> 인증서 기반 인증 문제 해결
1. VPN 클라이언트 설정을 확인합니다. Command+Shift를 눌러 **네트워크 설정**으로 이동한 다음, “VPN”을 입력하여 VPN 클라이언트 설정을 확인합니다. 목록에서 조사해야 하는 VPN 항목을 클릭합니다.

  ![IKEv2 인증서 기반 인증](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. **서버 주소**가 완전한 FQDN이고 cloudapp.net을 포함하는지 확인합니다.
3. **원격 ID**는 서버 주소(게이트웨이 FQDN)와 동일해야 합니다.
4. **로컬 ID**는 클라이언트 인증서의 **주체**와 동일해야 합니다.
5. **인증 설정**을 클릭하여 인증 설정 페이지를 엽니다.

  ![인증 설정](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. 드롭다운에서 **인증서**가 선택되었는지 확인합니다.
7. **선택** 단추를 클릭하고 올바른 인증서가 선택되었는지 확인합니다. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="ikev2"></a>사용자 이름 및 암호 인증 문제 해결

1. VPN 클라이언트 설정을 확인합니다. Command+Shift를 눌러 **네트워크 설정**으로 이동한 다음, “VPN”을 입력하여 VPN 클라이언트 설정을 확인합니다. 목록에서 조사해야 하는 VPN 항목을 클릭합니다.

  ![IKEv2 사용자 이름 암호](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. **서버 주소**가 완전한 FQDN이고 cloudapp.net을 포함하는지 확인합니다.
3. **원격 ID**는 서버 주소(게이트웨이 FQDN)와 동일해야 합니다.
4. **로컬 ID**는 공백일 수 있습니다.
5. **인증 설정** 단추를 클릭하고 드롭다운에서 “사용자 이름”이 선택되었는지 확인합니다.

  ![인증 설정](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. 올바른 자격 증명 정보가 입력되었는지 확인합니다.

## <a name="additional"></a>추가 단계

이전 단계를 시도했으며 모든 항목이 제대로 구성된 경우 [Wireshark](https://www.wireshark.org/#download)를 다운로드하고 패킷 캡처를 수행합니다.

1. *iskmp*를 필터링하고 **IKE_SA** 패킷을 확인합니다. **페이로드: 보안 연결** 아래에서 SA 제안 세부 정보를 확인할 수 있습니다. 
2. 클라이언트와 서버에 공통 집합이 있는지 확인합니다.

  ![패킷](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 참조하세요.
