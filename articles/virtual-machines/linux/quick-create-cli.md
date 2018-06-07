---
title: 빠른 시작 - Azure CLI 2.0을 사용하여 Linux VM 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure CLI 2.0을 사용하여 Linux 가상 머신을 만드는 방법을 배웁니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>빠른 시작: Azure CLI 2.0을 사용하여 Linux 가상 머신 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI 2.0이 사용됩니다. 이 빠른 시작에서는 Azure CLI 2.0을 사용하여 Ubuntu를 실행하는 Azure에서 Linux VM(가상 머신)을 배포하는 방법을 보여줍니다. 작업에서 VM을 보려면 VM에 SSH를 수행하고 NGINX 웹 서버를 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 VM을 만듭니다.

다음 예제에서는 *myVM*이라는 VM을 만들고, *azureuser*라는 사용자 계정을 추가하고, 아직 기본 키 위치(*~/.ssh*)에 SSH 키가 없으면 새로 생성합니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

VM의 출력에서 사용자 고유의 `publicIpAddress`를 적어 둡니다. 이 주소는 다음 단계에서 VM에 액세스하는 데 사용됩니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

기본적으로 Azure에서 Linux VM을 만들 때만 SSH 연결이 열립니다. [az vm open-port](/cli/azure/vm#az_vm_open_port)를 사용하여 NGINX 웹 서버와 사용하기 위해 TCP 포트 80을 엽니다.

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

정상적으로 VM에 SSH를 수행합니다. VM에서 이전 출력에 표시된 것과 같이 **publicIpAddress**를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>웹 서버 설치

작업에서 VM을 확인하려면 NGINX 웹 서버를 설치합니다. 패키지 원본을 업데이트하고 최신 NGINX 패키지를 설치하려면 SSH 세션에서 다음 명령을 실행합니다.

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

완료되면 SSH 세션을 `exit`합니다.

## <a name="view-the-web-server-in-action"></a>작업에서 웹 서버 보기

NGINX를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 NGINX 시작 페이지를 봅니다. 이전 단계에서 가져온 VM의 공용 IP 주소를 사용합니다. 다음 예제는 기본 NGINX 웹 사이트를 보여줍니다.

![NGINX 기본 사이트](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다. VM에 대한 SSH 세션을 종료했는지 확인한 다음, 다음과 같이 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 가상 머신을 배포하고, 웹 트래픽에 대한 네트워크 포트를 열고, 기본 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Linux VM의 자습서를 계속 진행합니다.


> [!div class="nextstepaction"]
> [Azure Linux 가상 머신 자습서](./tutorial-manage-vm.md)
