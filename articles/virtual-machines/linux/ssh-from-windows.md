---
title: Linux VM용 Windows를 통한 SSH 키 사용 | Microsoft Docs
description: Windows 컴퓨터에서 SSH 키를 생성하고 사용하여 Azure에서 Linux 가상 머신에 연결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure에서 Windows를 통해 SSH 키를 사용하는 방법

이 문서에서는 Azure에서 Linux 가상 머신(VM)에 연결하고 만들려는 Windows 컴퓨터에서 보안 셸(SSH) 키를 생성하고 사용하는 방법을 소개합니다. Linux 또는 macOS 클라이언트에서 SSH 키를 사용하려면 [빠른](mac-create-ssh-keys.md) 또는 [자세한](create-ssh-keys-detailed.md) 지침을 참조합니다.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows 패키지 및 SSH 클라이언트
Azure에서 *SSH 클라이언트*를 통해 Linux VM에 연결하고 관리합니다. Linux 또는 macOS를 실행하는 컴퓨터에는 일반적으로 SSH 연결을 만들고 SSH 키를 생성 및 관리하는 SSH 명령 모음이 있습니다. 

Windows 컴퓨터는 항상 비교 가능한 SSH 명령을 설치하지는 않습니다. [Linux용 Windows 하위 시스템](https://docs.microsoft.com/windows/wsl/about)을 포함하는 Windows 10 버전에서는 Bash 셸 내에 기본적으로 포함된 SSH 클라이언트와 같은 유틸리티를 실행하고 액세스할 수 있습니다. 

Windows용 Bash 이외의 것을 사용하려는 경우 로컬로 설치할 수 있는 공용 Windows SSH 클라이언트는 다음과 같은 패키지에 포함되어 있습니다.

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Windows 용 Git](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

다른 옵션은 [Azure Cloud Shell](../../cloud-shell/overview.md)의 Bash에서 사용할 수 있는 SSH 유틸리티를 사용하는 것입니다. 

* [https://shell.azure.com](https://shell.azure.com)의 웹 브라우저 또는 [Azure Portal](https://portal.azure.com)에서 Cloud Shell에 액세스합니다. 
* 이제 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)을 설치하여 Visual Studio Code 내에서 터미널로 Cloud Shell에 액세스합니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기
이 섹션에서는 Windows에서 SSH 키 쌍을 만드는 두 가지 옵션을 표시합니다.

### <a name="create-ssh-keys-with-ssh-keygen"></a>ssh-keygen을 사용하여 SSH 키 만들기

Windows 또는 GitBash용 Bash(또는 Azure Cloud Shell에서 Bash) 같은 명령 셸을 실행하는 경우 `ssh-keygen` 명령을 사용하여 SSH 키 쌍을 만듭니다. 다음 명령을 입력하고 표시되는 메시지에 응답합니다. SSH 키 쌍이 현재 위치에 있으면 이러한 파일은 덮어쓰여집니다. 

```bash
ssh-keygen -t rsa -b 2048
```

자세한 배경 및 정보는 `ssh-keygen`를 사용하여 키를 만드는 [빠른](mac-create-ssh-keys.md) 또는 [자세한](create-ssh-keys-detailed.md) 단계를 참조합니다.

### <a name="create-ssh-keys-with-puttygen"></a>PuTTYgen을 사용하여 SSH 키 만들기

SSH 키를 만드는 GUI 기반 도구 사용을 선호하는 경우 [PuTTY 다운로드 패키지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에 포함된 PuTTYgen 키 생성기를 사용할 수 있습니다. 

PuTTYgen을 사용하여 SSH RSA 키 쌍을 만들려면:

1. PuTTYgen을 시작합니다.

2. **생성**을 클릭합니다. 기본적으로 PuTTYgen은 2048비트 SSH-2 RSA 키를 생성합니다.

4. 키에 대한 몇 가지 임의성을 생성하려면 마우스를 빈 영역에 위치시킵니다.

5. 공개 키를 생성한 후 필요에 따라 전달 구를 입력하고 확인합니다. SSH 키를 사용하여 VM을 인증할 경우 전달 구를 입력하라는 메시지가 표시됩니다. 전달 구를 사용하지 않을 때 개인 키를 가져오면 해당 키를 사용하는 모든 VM 또는 서비스에 로그인 할 수 있습니다. 따라서 전달 구를 만드는 것이 좋습니다. 하지만 전달 구를 잊어버린 경우에는 복구할 수 있는 방법이 없으므로 주의해야 합니다.

6. 공개 키는 창의 맨 위에 표시됩니다. Linux VM을 만들 때 이 한 줄 형식의 공개 키를 복사하여 Azure Portal 또는 Azure Resource Manager 템플릿에 붙여넣습니다. 다음과 같이 **공개 키 저장**을 클릭하면 컴퓨터에 복사본을 저장할 수도 있습니다.

    ![PuTTY 공개 키 파일 저장](./media/ssh-from-windows/save-public-key.png)

7. 필요에 따라 개인 키를 PuTTy 개인 키 형식(.ppk 파일)으로 저장하려면 **개인 키 저장**을 클릭합니다. VM에 SSH 연결을 만들기 위해 나중에 PuTTY를 사용하려면 .ppk 파일이 필요합니다.

    ![PuTTY 개인 키 파일 저장](./media/ssh-from-windows/save-ppk-file.png)

    많은 SSH 클라이언트에서 사용하는 개인 키 형식인 OpenSSH 형식으로 개인 키를 저장하려는 경우 **변환** > **OpenSSH 키 내보내기**를 차례로 클릭합니다.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM을 배포하는 경우 SSH 공개 키 제공

인증을 위해 SSH 키를 사용하는 Linux VM을 만들려면 Azure Portal 또는 기타 방법을 사용하여 VM을 만들 때 SSH 공개 키를 제공합니다.

다음 예제에서는 Linux VM을 만들 때 이 공개 키를 복사하여 Azure 포털에 붙여넣는 방법을 보여 줍니다. 그런 다음 공개 키는 대개 새 VM의 `~/.ssh/authorized_keys`에 저장됩니다.

   ![Azure 포털에서 VM을 만들 때의 공개 키 사용](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>VM에 연결

Windows에서 Linux VM에 SSH 연결을 만드는 한 가지 방법은 SSH 클라이언트를 사용하는 것입니다. 이 방법은 Azure Cloud Shell의 Bash에서 SSH 도구를 사용하거나 Windows 시스템에 SSH 클라이언트가 설치된 경우 선호하는 방법입니다. GUI 기반 도구를 선호하는 경우 PuTTY로 연결할 수 있습니다.  

### <a name="use-an-ssh-client"></a>SSH 클라이언트 사용
Azure VM에서 배포된 공개 키 및 로컬 시스템에서 배포된 개인 키를 통해 IP 주소 또는 VM의 DNS 이름을 사용하여 VM에 SSH를 사용합니다. 다음 명령에서 *azureuser* 및 *myvm.westus.cloudapp.azure.com*을 관리자 사용자 이름 및 정규화된 도메인 이름(또는 IP 주소)으로 바꿉니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

키 쌍을 만들 때 암호를 구성한 경우 로그인 프로세스 중에 메시지가 표시되면 암호를 입력합니다.

### <a name="connect-with-putty"></a>PuTTY로 연결

[PuTTY 다운로드 패키지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 설치하고 이전에 PuTTY 개인 키(.ppk 파일)를 생성한 경우 PuTTY로 Linux VM에 연결할 수 있습니다.

1. PuTTY를 시작합니다.

2. 다음과 같이 Azure 포털에서 VM의 호스트 이름 또는 IP 주소를 입력합니다.

    ![새 PuTTY 연결 열기](./media/ssh-from-windows/putty-new-connection.png)

3. **열기**를 선택하기 전에 **연결** > **SSH** > **인증** 탭을 클릭합니다. PuTTY 개인 키(.ppk file) 찾기 및 선택:

    ![인증용 PuTTY 개인 키 선택](./media/ssh-from-windows/putty-auth-dialog.png)

4. **열기**를 클릭하여 VM에 연결합니다.

## <a name="next-steps"></a>다음 단계

* 단계, 옵션 및 SSH 키로 작업하는 고급 예에 대한 자세한 사항은 [SSH 키 쌍을 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조합니다.

* Azure Cloud Shell에서 PowerShell을 사용하여 Linux VM에 SSH 연결을 만들고 SSH 키를 생성할 수 있습니다. [PowerShell 빠른 시작](../../cloud-shell/quickstart-powershell.md#ssh)을 참조합니다.

* SSH를 사용하여 Linux VM을 연결하는 데 문제가 있으면 [Azure Linux VM에 대한 SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
