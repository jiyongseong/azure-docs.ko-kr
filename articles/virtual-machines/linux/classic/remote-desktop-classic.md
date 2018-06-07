---
title: Linux VM에 대한 원격 데스크톱 | Microsoft Docs
description: 원격 데스크톱을 설치 및 구성하여 클래식 배포 모델을 위한 Microsoft Azure Linux VM에 연결하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 0e1bfe468e1572ca98be956d39d82df562dce0e6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>원격 데스크톱을 사용하여 Microsoft Azure Linux VM에 연결
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 이 문서의 업데이트된 리소스 관리자 버전은 [여기](../use-remote-desktop.md)를 참조하세요.

## <a name="overview"></a>개요
RDP(원격 데스크톱 프로토콜)는 Windows에 사용되는 독점 프로토콜입니다. 어떻게 RDP를 사용하여 Linux VM에 원격으로 연결할 수 있을까요?

이 설명서에서 답을 줍니다! Microsoft Azure Linux VM에 xrdp를 설치 및 구성하는 데 도움이 되며, Windows 컴퓨터에서 원격 데스크톱을 사용하여 연결할 수 있습니다. 이 지침에서는 Ubuntu 또는 OpenSUSE를 실행하는 Linux VM을 예로 사용합니다.

xrdp 도구는 Windows 컴퓨터에서 원격 데스크톱으로 Linux 서버에 연결할 수 있도록 해주는 오픈 소스 RDP 서버입니다. RDP는 VNC(Virtual Network Computing)보다 더 강력한 성능을 구현합니다. RDP는 빠르고 선명한 반면 VNC는 JPEG 수준의 그래픽을 사용하여 렌더링하며 속도가 느려질 수 있습니다.

> [!NOTE]
> Linux를 실행하는 Microsoft Azure VM이 있어야 합니다. Linux VM을 만들고 설정하려면 [Azure Linux VM 자습서](createportal-classic.md)를 참조하세요.
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>원격 데스크톱에 대한 끝점 만들기
이 문서에서는 원격 데스크톱에 대해 기본 끝점 3389를 사용합니다. 아래와 같은 Linux VM에 `Remote Desktop`으로 3389 끝점을 설정합니다.

![이미지](./media/remote-desktop/endpoint-for-linux-server.png)

VM에 대한 끝점을 설정하는 방법을 모르는 경우 [이 지침](setup-endpoints.md)을 참조하세요.

## <a name="install-gnome-desktop"></a>Gnome 데스크톱 설치
`putty`를 통해 Linux VM에 연결하고 `Gnome Desktop`을 설치합니다.

Ubuntu의 경우 다음을 사용합니다.

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

OpenSUSE의 경우 다음을 사용합니다.

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>xrdp 설치
Ubuntu의 경우 다음을 사용합니다.

```bash
sudo apt-get install xrdp
```

OpenSUSE의 경우 다음을 사용합니다.

> [!NOTE]
> 다음 명령에서 현재 사용하는 버전으로 OpenSUSE 버전을 업데이트합니다. 아래 예는 `OpenSUSE 13.2`의 경우입니다.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>xrdp를 시작하고 부팅에 xdrp 서비스를 설정합니다.
OpenSUSE의 경우 다음을 사용합니다.

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Ubuntu의 경우 설치 후 부팅 시 자동으로 xrdp가 시작되어 사용됩니다.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Ubuntu 12.04LTS보다 높은 Ubuntu 버전을 사용하는 경우 xfce를 사용
현재 xrdp 버전은 Ubuntu 12.04LTS보다 높은 Ubuntu 버전에 대해 Gnome 데스크톱을 지원하지 않으므로 대신 `xfce` 데스크톱을 사용합니다.

`xfce`를 설치하려면 이 명령을 사용합니다.

```bash
sudo apt-get install xubuntu-desktop
```

그런 다음 이 명령을 사용하여 `xfce`를 활성화합니다.

```bash
echo xfce4-session >~/.xsession
```

구성 파일 `/etc/xrdp/startwm.sh`를 편집합니다.

```bash
sudo vi /etc/xrdp/startwm.sh   
```

`/etc/X11/Xsession` 줄 앞에 `xfce4-session` 줄을 추가합니다.

xrdp 서비스를 다시 시작하려면 다음을 사용합니다.

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Windows 컴퓨터에서 Linux VM 연결
Windows 컴퓨터에서 원격 데스크톱 클라이언트를 시작하고 Linux VM DNS 이름을 입력합니다. 또는 Azure Portal에서 VM의 대시보드로 이동하고 `Connect`를 클릭하여 Linux VM을 연결합니다. 이 경우 로그인 창이 다음과 같이 표시됩니다.

![이미지](./media/remote-desktop/no2.png)

Linux VM의 사용자 이름 및 암호로 로그인합니다.

## <a name="next-steps"></a>다음 단계
xrdp 사용에 대한 자세한 내용은 [http://www.xrdp.org/](http://www.xrdp.org/)를 참조하세요.
