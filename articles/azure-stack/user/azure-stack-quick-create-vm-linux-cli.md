---
title: Azure 스택의 Azure CLI를 사용 하 여 Linux 가상 컴퓨터 만들기 | Microsoft Docs
description: Azure 스택의 CLI와 Linux 가상 컴퓨터를 만듭니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>빠른 시작: Azure 스택에서 Azure CLI를 사용 하 여 Linux 서버 가상 컴퓨터 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure CLI를 사용 하 여 서버 16.04 Ubuntu LTS 가상 컴퓨터를 만들 수 있습니다. 이 문서를 만들고 가상 컴퓨터를 사용의 단계를 수행 합니다. 이 문서의 단계를 제공합니다.

* 가상 컴퓨터 원격 클라이언트와 연결 합니다.
* NGINX 웹 서버를 설치 하 고 기본 홈 페이지를 봅니다.
* 사용 되지 않는 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 스택 마켓플레이스에서 Linux 이미지**

   Azure 스택 마켓플레이스 기본적으로 Linux 이미지를 포함 하지 않습니다. 가져올 수 있도록 Azure 스택 연산자는 **Ubuntu Server 16.04 LTS** 필요한 이미지입니다. 연산자에 설명 된 단계를 사용할 수는 [스택에 Azure에서 Azure 마켓플레이스 항목을 다운로드](../azure-stack-download-azure-marketplace-item.md) 문서.

* Azure 스택 특정 버전의 Azure CLI를 만들고 리소스를 관리 해야 합니다. Azure 스택에 대해 구성 된 Azure CLI를 설정 하지 않은 경우에 로그인는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 단계에 따라 [ 설치 및 구성 Azure CLI](azure-stack-version-profiles-azurecli2.md)합니다.

* 이름 id_rsa.pub Windows 사용자 프로필의.ssh 디렉터리에 저장 된 공용 SSH 키입니다. SSH 키를 만드는 방법에 대 한 자세한 내용은 참조 [Windows에서 만드는 SSH 키](../../virtual-machines/linux/ssh-from-windows.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 배포 하 고 Azure 스택 리소스를 관리할 수 있는 논리적 컨테이너입니다. Azure 스택 또는 개발 키트에서 실행 하는 시스템을 통합는 [az 그룹 만들기](/cli/azure/group#az_group_create) 리소스 그룹을 만드는 명령입니다.

>[!NOTE]
 다음 코드 예제에서는 모든 변수에 값 할당 됩니다. 그러나 하려는 경우 새 값을 할당할 수 있습니다.

다음 예제에서는 로컬 위치에 myResourceGroup 명명 된 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

사용 하 여 가상 컴퓨터 만들기는 [az vm 만들기](/cli/azure/vm#az_vm_create) 명령입니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 로 사용자 암호입니다. 사용자 환경에 적합 한 값으로 이러한 값을 변경 합니다.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

공용 IP 주소에 반환 되는 **PublicIpAddress** 매개 변수입니다. 가상 컴퓨터에 액세스 하는 데 필요 하기 때문에이 주소를 적어 둡니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

을 때문에이 가상 컴퓨터는 IIS 웹 서버를 실행 하려면 인터넷 트래픽을 위해 포트 80을 열려면 해야 합니다. [az vm open-port](/cli/azure/vm#open-port) 명령을 사용하여 원하는 포트를 엽니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>SSH를 사용 하 여 가상 컴퓨터에 연결 하려면

SSH 설치 된 클라이언트 컴퓨터에서 가상 컴퓨터에 연결 합니다. 사용 하 여 Windows 클라이언트에서 작업할 경우 [Putty](http://www.putty.org/) 연결을 만듭니다. 가상 컴퓨터를 연결 하려면 다음 명령을 사용 합니다.

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>NGINX 웹 서버를 설치 합니다.

패키지 리소스를 업데이트 하 고 최신 NGINX 패키지 설치를 하려면 다음 스크립트를 실행 합니다.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

설치 NGINX 및 포트 80 가상 컴퓨터에서 열려 사용 하 여 가상 컴퓨터의 공용 IP 주소를 사용 하 여 웹 서버를 액세스할 수 있습니다. 웹 브라우저를 열고 ```http://<public IP address>```합니다.

![NGINX 웹 서버에 대 한 시작 페이지](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 사용할 수는 [az 그룹 삭제](/cli/azure/group#az_group_delete) 명령 이러한 리소스를 제거 합니다. 리소스 그룹 및 모든 리소스를 삭제 하려면 다음 명령을 실행 합니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 퀵 스타트의 웹 서버와 기본 Linux 서버 가상 컴퓨터를 배포 했습니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
