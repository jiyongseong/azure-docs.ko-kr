---
title: Azure에서 Linux VM용 SSH 키 쌍 만들기 및 사용 | Microsoft Docs
description: Azure에서 인증 프로세스의 보안을 개선하기 위해 Linux VM용 SSH 공용-개인 키를 만들고 사용하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>빠른 단계: Azure에서 Linux VM용 SSH 공개-개인 키 쌍을 만들고 사용하는 방법
SSH(보안 셸) 키 쌍을 사용하면 인증을 위해 SSH 키를 사용하는 VM(가상 머신)을 Azure에서 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다. 이 문서에서는 Linux VM용 SSH 공개-개인 키 파일 쌍을 신속하게 생성하고 사용하는 방법을 보여줍니다. Linux에서 Azure Cloud Shell, macOS 또는 Linux 호스트, Windows 하위 시스템 및 OpenSSH를 지원하는 기타 도구를 사용하여 이러한 단계를 완료할 수 있습니다. 

자세한 배경 및 예제는 [SSH 키 쌍을 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조합니다.

Windows 컴퓨터에서 SSH 키를 생성하고 사용하는 추가적인 방법은 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조합니다.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기
`ssh-keygen` 명령을 사용하여 `~/.ssh` 디렉터리에 기본적으로 생성되는 SSH 공용 및 개인 키 파일을 생성합니다. 요청될 경우 다른 위치 및 추가 암호(개인 키 파일에 액세스하기 위한 암호)를 지정할 수 있습니다. SSH 키 쌍이 현재 위치에 있으면 이러한 파일은 덮어쓰여집니다.

```bash
ssh-keygen -t rsa -b 2048
```

VM을 만들기 위해 [Azure CLI 2.0](/cli/azure)을 사용하는 경우 `--generate-ssh-keys` 옵션으로 [az vm 만들기](/cli/azure/vm#az_vm_create) 명령을 실행하여 SSH 공용 및 개인 키 파일을 선택적으로 생성할 수 있습니다. 키는 ~/.ssh 디렉터리에 저장됩니다. 이미 해당 위치에 있는 경우 이 명령 옵션은 키를 덮어쓰지 않습니다.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM을 배포하는 경우 SSH 공개 키 제공
인증을 위해 SSH 키를 사용하는 Linux VM을 만들려면 Azure Portal, CLI, Resource Manager 템플릿 또는 기타 방법을 사용하여 VM을 만들 때 SSH 공개 키를 지정합니다.

* [Azure Portal을 사용하여 Linux 가상 머신 만들기](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI를 사용하여 Linux 가상 머신 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 템플릿을 사용하여 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

SSH 공개 키의 형식을 잘 모르는 경우 다음과 같이 `cat`을 실행하여 공개 키를 확인할 수 있습니다. 이때 `~/.ssh/id_rsa.pub`를 사용자 고유의 공개 키 파일 위치로 대체합니다.

```bash
cat ~/.ssh/id_rsa.pub
```

Azure Portal 또는 Resource Manager 템플릿에서 사용할 공개 키 파일의 내용을 복사하여 붙여넣으려는 경우 추가 공백은 복사하지 않아야 합니다. 예를 들어, macOS를 사용하는 경우 공개 키 파일(기본적으로 `~/.ssh/id_rsa.pub`)을 **pbcopy**로 파이프하여 내용을 복사할 수 있습니다(**xclip**처럼 동일한 작업을 수행하는 다른 Linux 프로그램이 있음).

Azure에서 Linux VM에 배치하는 공개 키는 만들 때 위치를 변경하지 않는다면, 기본적으로 `~/.ssh/id_rsa.pub`에 저장됩니다. 기존 공개 키를 사용하여 VM을 만들기 위해 [Azure CLI 2.0](/cli/azure)을 사용하는 경우 `--ssh-key-value` 옵션으로 [az vm 만들기](/cli/azure/vm#az_vm_create) 명령을 실행하여 이 공개 키의 위치나 값을 지정합니다. 

## <a name="ssh-to-your-vm"></a>VM에 SSH를 사용
Azure VM에서 배포된 공개 키 및 로컬 시스템에서 배포된 개인 키를 통해 IP 주소 또는 VM의 DNS 이름을 사용하여 VM에 SSH를 사용합니다. 다음 명령에서 *azureuser* 및 *myvm.westus.cloudapp.azure.com*을 관리자 사용자 이름 및 정규화된 도메인 이름(또는 IP 주소)으로 바꿉니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

키 쌍을 만들 때 암호를 제공한 경우 로그인 프로세스 중에 메시지가 표시되면 암호를 입력합니다. (서버가 `~/.ssh/known_hosts` 폴더에 추가되고 Azure VM에서 공개 키가 변경되거나 서버 이름이 `~/.ssh/known_hosts`에서 제거될 까지 다시 연결할지 묻는 메시지가 표시되지 않습니다.)

SSH 키를 사용하여 만든 VM은 기본적으로 비활성화된 암호를 사용하여 구성되고 강제 무차별 암호 추측 시도를 더욱 비용이 많이 들고 결과적으로 어렵게 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 빠른 사용을 위해 간단한 SSH 키 쌍을 만드는 방법에 대해 설명합니다. 

* SSH 키 쌍과 함께 작동하려면 더 많은 지원이 필요한 경우 [SSH 키 쌍을 만들고 관리하는 자세한 단계](create-ssh-keys-detailed.md)를 참조합니다.

* Azure VM에 SSH를 연결하는 데 문제가 있는 경우 [Azure Linux VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md)을 참조합니다.


