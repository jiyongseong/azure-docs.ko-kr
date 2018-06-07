---
title: Azure에서 Linux VM에 대한 원격 데스크톱 사용 | Microsoft Docs
description: Azure에서 그래픽 도구를 사용하여 Linux VM에 연결하도록 원격 데스크톱(xrdp)을 설치 및 구성하는 방법 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: c47822bebdc8b3cc8896fe56b8f9a4ce317495c3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364304"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결
Azure의 Linux VM(가상 머신)은 SSH(보안 셸) 연결을 사용하여 명령줄에서 일반적으로 관리됩니다. Linux를 처음 사용하거나 빠른 문제 해결 시나리오의 경우 원격 데스크톱을 사용하는 편이 더 쉬울 수 있습니다. 이 문서에서는 Resource Manager 배포 모델을 사용하여 Linux VM에 대해 데스크톱 환경([xfce](https://www.xfce.org)) 및 원격 데스크톱([xrdp](http://www.xrdp.org))을 설치하고 구성하는 방법에 대해 자세히 설명합니다.


## <a name="prerequisites"></a>필수 조건
이 문서에는 Azure에 있는 기존 Ubuntu 16.04 LTS VM이 필요합니다. VM을 만들어야 하는 경우 다음 방법 중 하나를 사용합니다.

- [Azure CLI 2.0](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux VM에서 데스크톱 환경 설치
Azure의 Linux VM은 대부분 데스크톱 환경을 기본적으로 설치할 필요가 없습니다. Linux VM은 일반적으로 데스크톱 환경이 아닌 SSH 연결을 사용하여 관리됩니다. Linux에서 다양한 데스크톱 환경을 선택할 수 있습니다. 데스크톱 환경 선택에 따라 1~2GB의 디스크 공간을 사용하고 모든 필수 패키지를 설치 및 구성하는 데 5~10분이 걸릴 수 있습니다.

다음 예제에서는 Ubuntu 16.04 LTS VM에 경량 [xfce4](https://www.xfce.org/) 데스크톱 환경을 설치합니다. 다른 배포에 대한 명령은 약간씩 다릅니다. 예를 들어 `yum`을 사용하여 Red Hat Enterprise Linux에 설치하고 적절한 `selinux` 규칙을 구성하거나 `zypper`를 사용하여 SUSE에 설치합니다.

먼저 VM에 SSH를 사용합니다. 다음 예제에서는 사용자 이름 *azureuser*를 사용하여 *myvm.westus.cloudapp.azure.com*이라는 VM에 연결합니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Windows를 사용하고 SSH를 사용하는 방법에 대해 자세히 알아보려면 [Windows에서 SSH 키 사용 방법](ssh-from-windows.md)을 참조하세요.

다음으로 다음과 같이 `apt`를 사용하여 xfce를 설치합니다.

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>원격 데스크톱 서버 설치 및 구성
데스크톱 환경을 설치했으므로 들어오는 연결을 수신하도록 원격 데스크톱 서비스를 구성합니다. [xrdp](http://xrdp.org)는 대부분의 Linux 배포판에서 사용할 수 있는 오픈 소스 RDP(원격 데스크톱 프로토콜) 서버로 xfce와 함께 잘 작동합니다. 다음과 같이 Ubuntu VM에 xrdp를 설치합니다.

```bash
sudo apt-get install xrdp
```

사용자 세션을 시작할 때 사용할 데스크톱 환경을 xrdp에 알립니다. xfce를 구성하여 다음과 같이 xfce를 데스크톱 환경으로 사용합니다.

```bash
echo xfce4-session >~/.xsession
```

다음과 같이 적용되려면 변경을 위해 xrdp 서비스를 다시 시작합니다.

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>로컬 사용자 계정 암호 설정
VM을 만들 때 사용자 계정의 암호를 만든 경우 이 단계를 건너뜁니다. SSH 키 인증을 사용하고 로컬 계정 암호를 설정하지 않은 경우 xrdp를 사용하여 VM에 로그인하기 전에 암호를 지정합니다. xrdp는 인증을 위해 SSH 키를 수락할 수 없습니다. 다음 예제에서는 사용자 계정 *azureuser*의 암호를 지정합니다.

```bash
sudo passwd azureuser
```

> [!NOTE]
> 암호를 지정하더라도 현재 암호 로그인을 허용하지 않으면 암호 로그인을 허용하도록 SSHD 구성이 업데이트되지 않습니다. 보안의 관점에서 키 기반 인증을 사용하여 SSH 터널을 통해 VM에 연결한 다음 xrdp에 연결하려고 할 수 있습니다. 그렇다면, 원격 데스크톱 트래픽을 허용하도록 네트워크 보안 그룹 규칙을 만드는 방법에 대한 다음 단계를 건너뜁니다.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>원격 데스크톱 트래픽의 네트워크 보안 그룹 규칙 만들기
Linux VM에 도달하는 원격 데스크톱 트래픽을 허용하려면 포트 3389에 대한 TCP가 VM에 도달하도록 네트워크 보안 그룹 규칙을 만들어야 합니다. 네트워크 보안 그룹 규칙에 대한 자세한 내용은 [네트워크 보안 그룹이란?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. [Azure Portal을 사용하여 네트워크 보안 그룹 규칙을 만들](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 수도 있습니다.

다음 예제에서는 *3389* 포트에서 [az vm open-port](/cli/azure/vm#az_vm_open_port)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>원격 데스크톱 클라이언트와 Linux VM 연결
로컬 원격 데스크톱 클라이언트를 열고 IP 주소 또는 Linux VM의 DNS 이름에 연결합니다. 사용자 계정의 사용자 이름 및 암호를 VM에 다음과 같이 입력합니다.

![원격 데스크톱 클라이언트를 사용하여 xrdp에 연결](./media/use-remote-desktop/remote-desktop-client.png)

인증한 후에 xfce 데스크톱 환경이 로드되면 다음 예제와 유사합니다.

![xrdp를 통한 xfce 데스크톱 환경](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>문제 해결
원격 데스크톱 클라이언트를 사용하여 Linux VM에 연결할 수 없으면 Linux VM에서 `netstat`을 사용하여 다음과 같이 VM이 RDP 연결을 수신 대기하는지 확인합니다.

```bash
sudo netstat -plnt | grep rdp
```

다음 예제에서는 TCP 포트 3389를 예상대로 수신 대기하는 VM을 보여 줍니다.

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

*xrdp-sesman* 서비스가 수신 대기하지 않는 경우 Ubuntu VM에서 다음과 같이 서비스를 다시 시작합니다.

```bash
sudo service xrdp restart
```

서비스가 응답하지 않는 이유는 Ubuntu VM의 */var/log*에 있는 로그를 검토합니다. 또한 원격 데스크톱 연결을 시도하는 동안 syslog를 모니터링하여 모든 오류를 볼 수 있습니다.

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux 및 SUSE와 같은 기타 Linux 배포에는 검토할 서비스 및 다른 로그 파일 위치를 다시 시작하는 다른 방법이 있을 수 있습니다.

원격 데스크톱 클라이언트에서 응답을 수신하지 않고 시스템 로그에 이벤트가 표시되지 않는 경우 이 동작은 원격 데스크톱 트래픽이 VM에 도달할 수 없다는 것을 나타냅니다. 네트워크 보안 그룹 규칙을 검토하여 포트 3389에서 TCP를 허용하는 규칙이 있는지 확인합니다. 자세한 내용은 [응용 프로그램 연결 문제 해결](../windows/troubleshoot-app-connection.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Linux VM에서 SSH 키를 만들고 사용하는 방법에 대한 자세한 내용은 [Azure에서 Linux VM의 SSH 키 만들기](mac-create-ssh-keys.md)를 참조하세요.

Windows에서 SSH를 사용하는 방법에 대한 자세한 내용은 [Windows에서 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조하세요.

