---
title: Linux VM의 이미지 캡처 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 만든, Linux 기반 Azure VM(가상 머신)의 이미지를 캡처하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 6071e025352199c5ec559598a580a918c2e9c666
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>클래식 Linux 가상 머신을 이미지로 캡처하는 방법
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 모델을 사용하여 이러한 단계를 수행하는](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 방법을 알아봅니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 문서에서는 Linux를 실행하는 클래식 Azure VM(Virtual Machine)을 캡처하여 다른 Virtual Machines를 만들 때 이미지로 사용하는 방법을 소개합니다. 이 이미지에는 OS 디스크를 비롯해 VM에 연결된 데이터 디스크가 포함됩니다. 여기에 네트워킹 구성은 포함되지 않으므로 이미지에서 다른 VM을 만들 때 네트워킹을 구성해야 합니다.

Azure는 사용자가 업로드한 이미지와 함께 해당 이미지를 **Images**아래에 저장합니다. 이미지에 대한 자세한 내용은 [Azure의 Virtual Machine 이미지 정보][About Virtual Machine Images in Azure]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
이러한 단계는 이미 클래식 배포 모델을 사용하여 Azure VM을 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. VM을 만들어야 할 경우 [Linux Virtual Machine을 만드는 방법][How to Create a Linux Virtual Machine]을 참조하세요.

## <a name="capture-the-virtual-machine"></a>가상 머신 캡처
1. 선택한 SSH 클라이언트를 사용하여 [VM에 연결](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.
2. SSH 창에서 다음 명령을 입력합니다. `waagent` 의 출력은 이 유틸리티의 버전에 따라 약간 다를 수 있습니다.

    ```bash
    sudo waagent -deprovision+user
    ```

    이전 명령은 시스템을 정리하여 다시 프로비전하기에 적합하도록 만듭니다. 이 작업은 다음 작업을 수행합니다.

   * SSH 호스트 키를 제거합니다(구성 파일에서 Provisioning.RegenerateSshHostKeyPair가 'y'인 경우).
   * /etc/resolv.conf의 Nameserver 구성을 지웁니다.
   * /etc/shadow에서 `root` 사용자 암호를 제거합니다(구성 파일에서 Provisioning.DeleteRootPassword가 'y'인 경우).
   * 캐시된 DHCP 클라이언트 임대 제거
   * 호스트 이름을 localhost.localdomain으로 다시 설정
   * 마지막으로 프로비전된 사용자 계정 (/var/lib/waagent에서 얻은) **및 관련 데이터**를 삭제합니다.

     > [!NOTE]
     > 프로비전을 해제하면 파일 및 데이터가 삭제되어 이미지가 "일반화"됩니다. 새 이미지 템플릿 파일로 캡처할 VM에서만 이 명령을 실행합니다. 하지만 이미지에서 중요한 정보가 모두 지워지고 이미지가 제3자에게 다시 배포하기에 적합하다고 보증할 수는 없습니다.

3. 계속하려면 **y** 를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.
4. **Exit** 를 입력하여 SSH 클라이언트를 닫습니다.

   > [!NOTE]
   > 나머지 단계에서는 클라이언트 컴퓨터에 이미 [Azure CLI를 설치](../../../cli-install-nodejs.md) 했다고 가정합니다. 다음 모든 단계는 [Azure Portal](http://portal.azure.com)에서도 수행할 수 있습니다.

5. 클라이언트 컴퓨터에서 Azure CLI를 열고 Azure 구독에 로그인합니다. 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli)을 참조하세요.

   > [!NOTE]
   > Azure Portal에서 포털에 로그인합니다.

6. 서비스 관리 모드에 있는지 확인합니다.

    ```azurecli
    azure config mode asm
    ```

7. 이미 프로비전 해제된 VM을 종료합니다. 다음 예에서는 `myVM`이라는 VM을 종료합니다.

    ```azurecli
    azure vm shutdown myVM
    ```
   필요한 경우 `azure vm list`를 사용하여 구독에서 만든 모든 VM 목록을 볼 수 있습니다.

   > [!NOTE]
   > Azure Portal을 사용하는 경우 VM을 선택하고 **중지**를 클릭하여 VM을 종료합니다.

8. VM이 중지되면 이미지를 캡처합니다. 다음 예에서는 `myVM`이라는 VM을 캡처하고 `myNewVM`이라는 범용 이미지를 만듭니다.

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` 하위 명령은 원래 가상 머신을 삭제합니다.

    > [!NOTE]
    > Azure Portal의 허브 메뉴에서 **이미지**를 선택하여 이미지를 캡처할 수 있습니다. 이미지에 대한 다음 정보를 제공해야 합니다. 이름, 리소스 그룹, 위치, 운영 체제 유형 및 저장소 Blob 경로

9. 이제 새 VM을 구성하는 데 사용할 수 있는 이미지 목록에서 새 이미지를 사용할 수 있습니다. 다음 명령을 사용하여 볼 수 있습니다.

   ```azurecli
   azure vm image list
   ```

   [Azure Portal](http://portal.azure.com)에서 새 이미지는 **Compute** 서비스에 속하는 **VM 이미지(클래식)** 에 표시됩니다. Azure 서비스 목록의 맨 아래에서 **모든 서비스**를 클릭하고 **Compute** 서비스를 살펴보고 **VM 이미지(클래식)** 에 액세스할 수 있습니다.   

   ![이미지 캡처 성공](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>다음 단계
이제 이미지를 사용하여 VM을 만들 수 있습니다. Azure CLI 명령 `azure vm create` 을 사용하고 만든 이미지 이름을 제공할 수 있습니다. 자세한 내용은 [클래식 배포 모델에서 Azure CLI 사용](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)을 참조하세요.

또는 [Azure Portal](http://portal.azure.com)에서 **이미지** 방법을 사용하고 만든 이미지를 선택하여 사용자 지정 VM을 만듭니다. 자세한 내용은 [사용자 지정 VM을 만드는 방법][How to Create a Custom Virtual Machine]을 참조하세요

**참고 항목:** [Azure Linux 에이전트 사용자 가이드](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
