---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e49cfe786272b34675ca377808e2961e61a757e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
VM에 원격 데스크톱 연결을 만들어 VNet에 배포된 VM에 연결할 수 있습니다. 처음에 VM에 연결할 수 있는지 확인하는 가장 좋은 방법은 컴퓨터 이름이 아닌 개인 IP 주소를 사용하여 연결하는 것입니다. 이 방법을 사용하면 연결할 수 있는지, 이름 확인이 제대로 구성되었는지 테스트할 수 있습니다. 

1. VM의 개인 IP 주소를 찾습니다. Azure Portal에서 VM의 속성을 살펴보거나 PowerShell을 사용하여 VM의 개인 IP 주소를 찾을 수 있습니다.
2. 지점 및 사이트 간 VPN 연결을 사용하여 VNet에 연결되었는지 확인합니다. 
3. 작업 표시줄에서 검색 상자에 "RDP" 또는 "원격 데스크톱 연결"을 입력하여 원격 데스크톱 연결을 열고 원격 데스크톱 연결을 선택합니다. PowerShell에서 'mstsc' 명령을 사용하여 원격 데스크톱 연결을 열 수도 있습니다. 
3. 원격 데스크톱 연결에서 VM의 개인 IP 주소를 입력합니다. "옵션 표시"를 클릭하여 추가 설정을 조정한 다음 연결할 수 있습니다.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM에 대한 RDP 연결 문제를 해결하려면

VPN 연결을 통해 가상 머신에 연결하는 데 문제가 있는 경우 몇 가지 방법으로 확인할 수 있습니다. 자세한 문제 해결 정보는 [VM에 대한 원격 데스크톱 연결 문제 해결](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)을 참조하세요.

- VPN 연결이 성공했는지 확인합니다.
- VM의 개인 IP 주소에 연결하고 있는지 확인합니다.
- 'ipconfig'를 사용하여 연결하는 컴퓨터의 이더넷 어댑터에 할당된 IPv4 주소를 확인합니다. IP 주소가 연결하는 VNet의 주소 범위 또는 VPNClientAddressPool의 주소 범위 내에 있는 경우 이 주소를 겹치는 주소 공간이라고 합니다. 주소 공간이 이러한 방식으로 겹치면 네트워크 트래픽이 Azure에 도달하지 않고 로컬 네트워크에 남아 있습니다.
- 컴퓨터 이름이 아닌 개인 IP 주소를 사용하여 VM에 연결할 수 있는 경우 DNS를 올바르게 구성했는지 확인합니다. 이름 확인이 VM에서 작동하는 방법에 대한 자세한 내용은 [VM에서 이름 확인](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.
- DNS 서버 IP 주소가 VNet에 지정된 후에 VPN 클라이언트 구성 패키지가 생성되었는지 확인합니다. DNS 서버 IP 주소를 업데이트한 경우 새 VPN 클라이언트 구성 패키지를 생성하고 설치합니다.