---
title: Windows Server VM에 연결 | Microsoft Docs
description: Azure 포털 및 리소스 관리자 배포 모델을 사용하여 Windows VM에 연결 및 로그온하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012644"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법
Azure Portal의 **연결** 단추를 사용하여 Windows 데스크톱에서 RDP(원격 데스크톱) 세션을 시작합니다. 먼저 가상 머신에 연결한 다음 로그온합니다.

Mac에서 Windows VM에 연결하려는 경우 [Microsoft 원격 데스크톱](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417)과 같이 Mac용 RDP 클라이언트를 설치해야 합니다.

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결
1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines**를 클릭합니다.
3. 목록에서 가상 머신을 선택합니다.
4. 가상 머신에 대한 페이지 위쪽에서 ![연결 단추 이미지](./media/connect-logon/connect.png) 단추를 선택합니다.
2. **가상 머신에 연결** 페이지에서 적절한 옵션을 선택하고 **RDP 파일 다운로드**를 클릭합니다.
2. 다운로드한 RDP 파일을 열고 메시지가 표시되면 **연결**을 클릭합니다. 
2. `.rdp` 파일이 알 수 없는 게시자에게서 비롯되었다는 경고를 받게 됩니다. 이것은 정상입니다. 원격 데스크톱 창에서 **연결** 을 클릭하여 계속합니다.
   
    ![알 수 없는 게시자에 대한 경고의 스크린샷입니다.](./media/connect-logon/rdp-warn.png)
3. **Windows 보안** 창에서 **추가 선택 사항** 및 **다른 계정 사용**을 차례로 선택합니다. 가상 머신의 계정에 대한 자격 증명을 입력하고 **확인**을 클릭합니다.
   
     **로컬 계정** - 일반적으로 가상 머신을 만들 때 지정한 로컬 계정 사용자 이름 및 암호입니다. 이 경우 도메인은 가상 머신의 이름이므로 *vmname*&amp;#92;*username*으로 입력합니다.  
   
    **도메인 가입 VM** - VM이 도메인에 속하는 경우, *도메인*&#92;*사용자 이름* 형식으로 사용자 이름을 입력합니다. 또한 계정은 관리자 그룹에 있거나 VM에 대한 원격 액세스 권한을 부여 받아야 합니다.
   
    **도메인 컨트롤러** - VM이 도메인 컨트롤러인 경우 해당 도메인의 도메인 관리자의 사용자 이름과 암호를 입력합니다.
4. **예** 를 클릭하여 가상 머신의 ID를 확인하고 로그온을 완료합니다.
   
   ![VM의 ID 확인에 대한 메시지를 보여 주는 스크린샷입니다.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > 포털에서 **연결** 단추가 회색으로 표시되고 [Express 경로](../../expressroute/expressroute-introduction.md) 또는 [사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 연결을 통해 Azure에 연결되지 않는 경우 RDP를 사용하려면 먼저 공용 IP 주소를 만들고 VM에 할당해야 합니다. 자세한 내용은 [Azure의 공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)에서 확인할 수 있습니다.
   > 
   > 


## <a name="next-steps"></a>다음 단계
연결하려고 할 때 문제가 발생할 경우 [원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.

