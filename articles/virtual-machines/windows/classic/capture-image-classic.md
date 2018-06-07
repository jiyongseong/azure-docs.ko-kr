---
title: Azure Windows VM의 이미지 캡처 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 만든 Azure Windows 가상 머신의 이미지를 캡처합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 24366d3adf63fb6fafb8afa9f1a9fb359e88d8ed
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 Azure Windows 가상 머신의 이미지 캡처
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델 정보는 [Azure에서 일반화된 VM의 관리되는 이미지 캡처](../capture-image-resource.md)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 문서에서는 Windows가 실행되는 Azure 가상 머신을 캡처하여 다른 가상 머신을 만들 때 이미지로 사용하는 방법을 소개합니다. 이 이미지에는 OS 디스크를 비롯해 가상 머신에 연결되는 모든 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로, 이미지를 사용하는 다른 가상 머신을 만들 때 네트워크 구성을 설정해야 합니다.

Azure에서는 모든 Azure 서비스를 볼 때 나열된 **Compute** 서비스인 **VM 이미지(클래식)** 아래에 이미지를 저장합니다. 사용자가 업로드한 모든 이미지도 이 위치에 저장됩니다. 이미지에 대한 자세한 내용은 [가상 머신 이미지 정보](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
이러한 단계는 이미 Azure 가상 머신을 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 수행하지 않은 경우 가상 머신을 만들고 준비하는 정보는 다음 문서를 참조하세요.

* [이미지에서 가상 머신 만들기](createportal.md)
* [가상 머신에 데이터 디스크를 연결하는 방법](attach-disk.md)
* 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

> [!WARNING]
> 이 프로세스는 캡처된 후 원래의 가상 머신을 삭제합니다.
>
>

Azure Virtual Machine의 이미지를 캡처하기 전에 대상 가상 머신을 백업하는 것이 좋습니다. Azure Backup을 사용하여 Azure 가상 머신을 백업할 수 있습니다. 자세한 내용은 [Azure 가상 머신 백업](../../../backup/backup-azure-arm-vms.md)을 참조하세요. 다른 솔루션은 인증된 파트너에서 사용할 수 있습니다. 무엇이 현재 사용 가능한지 알아보려면, Azure Marketplace를 검색합니다.

## <a name="capture-the-virtual-machine"></a>가상 머신 캡처
1. [Azure Portal](http://portal.azure.com)에서 가상 컴퓨터에 **연결**합니다. 지침은 [Windows Server를 실행하여 가상 머신에 로그인하는 방법][How to sign in to a virtual machine running Windows Server]을 참조하세요.
2. 관리자로 명령 프롬프트 창을 엽니다.
3. 디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.
4. **시스템 준비 도구** 대화 상자가 나타납니다. 다음을 수행합니다.

   * **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개][How to Use Sysprep: An Introduction]를 참조하세요.
   * **종료 옵션**에서 **종료**를 선택합니다.
   * **확인**을 클릭합니다.

   ![Sysprep 실행](./media/capture-image/SysprepGeneral.png)
5. Sysprep을 실행하면 가상 머신이 종료되고 Azure Portal의 가상 머신 상태가 **중지됨**으로 변경됩니다.
6. Azure Portal에서 **Virtual Machines(클래식)**를 클릭한 후 캡처하려는 Virtual Machines를 선택합니다. **VM 이미지(클래식)** 그룹은 **모든 서비스**를 볼 때 **Compute** 아래에 나열됩니다.

7. 명령 모음에서 **캡처**를 클릭합니다.

   ![가상 머신 캡처](./media/capture-image/CaptureVM.png)

   **Virtual Machine 캡처** 대화 상자가 나타납니다.

8. **이미지 이름**에 새 이미지의 이름을 입력합니다. **이미지 레이블**에 새 이미지의 레이블을 입력합니다.

9. **가상 컴퓨터에서 Sysprep를 실행했습니다**를 클릭하세요. 이 확인란은 3~5단계에서 Sysprep을 사용하는 작업을 가리킵니다. 사용자 지정 이미지 집합에 Windows Server 이미지를 추가하기 전에 Sysprep을 실행하여 이미지를 일반화_해야_ 합니다.

10. 캡처가 완료되면 새 이미지를 **Marketplace** 및 **Compute**, **VM 이미지(클래식)** 컨테이너에서 사용할 수 있습니다.

    ![이미지 캡처 성공](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>다음 단계
이제 이미지를 사용하여 가상 머신을 만들 수 있습니다. 이를 위해 서비스 메뉴의 아래쪽에 있는 **모든 서비스** 메뉴 항목 및 **Compute** 그룹에서 **VM 이미지(클래식)**를 선택하여 가상 머신을 만들 수 있습니다. 지침에 대해서는 [이미지에서 가상 머신 만들기](createportal.md)를 참조하세요.

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
