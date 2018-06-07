---
title: 클래식 Azure VM에 로그온 | Microsoft Docs
description: Azure Portal을 사용하여 클래식 배포 모델로 만든 Windows 가상 머신에 로그온할 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012362"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Azure 포털을 사용하여 Windows 가상 머신에 로그온
Azure 포털에서 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작하고 Windows VM에 로그온합니다.

Linux VM에 연결하고 싶으세요? [Linux를 실행하는 가상 머신에 로그온하는 방법](../../linux/mac-create-ssh-keys.md)을 참조하세요.

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델을 사용하여 VM에 로그온하는 방법에 대한 정보는 [여기](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결
1. Azure 포털에 로그인합니다.
2. 액세스할 가상 머신을 클릭합니다. 이름은 **모든 리소스** 창에 나열됩니다.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. 가상 머신 속성 페이지에서 **연결** 단추를 클릭합니다. 
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

## <a name="next-steps"></a>다음 단계
* **연결** 단추가 비활성 상태이거나 원격 데스크톱 연결에 다른 문제가 있는 경우 구성을 다시 설정해 봅니다. 가상 컴퓨터 대시보드에서 **원격 액세스 재설정**을 클릭합니다.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* 암호에 문제가 있는 경우 암호를 다시 설정합니다. **지원 + 문제 해결** 아래에서 가상 머신 대시보드의 왼쪽 가장자리를 따라 **암호 재설정**을 클릭합니다.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

이러한 팁이 작동하지 않거나 필요한 정보가 아닌 경우 [Windows 기반 Azure Virtual Machine에 대한 원격 데스크톱 연결 문제 해결](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.
