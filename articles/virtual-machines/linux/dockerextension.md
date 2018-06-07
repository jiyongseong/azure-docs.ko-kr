---
title: Azure Docker VM 확장 사용 | Microsoft Docs
description: Docker VM 확장을 사용하여 Azure에서 Resource Manager 템플릿 및 Azure CLI 2.0을 사용하여 Docker 환경을 빠르고 안전하게 배포하는 방법에 대해 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 1e5a4fcfd758c12213d6de7d0f5cfcc78531ee97
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Docker VM 확장을 사용하여 Azure에서 Docker 환경 만들기
Docker는 Linux에서 컨테이너와 함께 빠르게 사용할 수 있는 인기 있는 컨테이너 관리 및 이미징 플랫폼입니다. Azure에는 필요에 맞게 Docker를 배포할 수 있는 다양한 방법이 있습니다. 이 문서는 Azure CLI 2.0을 사용하여 Docker VM 확장 및 Azure Resource Manager 템플릿을 사용하는 방법에 중점을 두고 있습니다. [Azure CLI 1.0](dockerextension-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다.

> [!WARNING]
> Linux용 Azure Docker VM 확장은 사용되지 않으며 2018년 11월에 사용 중지됩니다.
> 확장은 단순히 Docker를 설치합니다.따라서 cloud-init 또는 사용자 지정 스크립트 확장 등 대안을 사용하여 선택한 Docker 버전을 설치하는 것이 좋습니다. cloud-init를 사용하는 방법에 대한 자세한 내용은 [cloud-init를 사용하여 Linux VM 사용자 지정](tutorial-automate-vm-deployment.md)을 참조하세요.

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 확장 개요
Azure Docker VM 확장은 Linux 가상 머신(VM)에 Docker 데몬, Docker 클라이언트 및 Docker Compose를 설치하고 구성합니다. Azure Docker VM 확장을 사용하면 직접 Docker 호스트를 만들거나 Docker Machine을 사용하는 것보다 더 많은 제어와 가능을 사용할 수 있습니다. [Docker Compose](https://docs.docker.com/compose/overview/)와 같은 이러한 추가 기능은 Azure Docker VM 확장을 보다 강력한 개발자 또는 프로덕션 환경에 적합하도록 만들어 줍니다.

Docker Machine 및 Azure Container Service 사용을 비롯한 다른 배포 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* 앱을 신속하게 프로토타이핑하려면 [Docker Machine](docker-machine.md)을 사용하여 단일 Docker 호스트를 만듭니다.
* 추가 예약 및 관리 도구를 제공하는 프로덕션이 준비된 확장성 있는 환경을 구축하려면 Azure Container Services에 [Kubernetes](../../container-service/kubernetes/index.yml) 또는 [Docker Swarm](../../container-service/dcos-swarm/index.yml) 클러스터를 배포합니다.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Azure Docker VM 확장을 사용하여 템플릿 배포
기존의 빠른 시작 템플릿을 사용하여 Docker 호스트를 설치 및 구성하기 위해 Azure Docker VM 확장을 사용하는 Ubuntu VM을 만들겠습니다. 템플릿은 [Docker를 사용한 간단한 Ubuntu VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)에서 볼 수 있습니다. 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다.

먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

다음으로 [GitHub의 이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)의 Azure Docker VM 확장을 포함하는 [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create)로 VM을 배포합니다. 메시지가 표시되면 *newStorageAccountName*, *adminUsername*, *adminPassword* 및 *dnsNameForPublicIP*에 대한 고유한 값을 제공합니다.

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

배포를 완료하려면 몇 분 정도 소요됩니다.


## <a name="deploy-your-first-nginx-container"></a>첫 번째 NGINX 컨테이너 배포
DNS 이름을 비롯한 VM의 세부 정보를 보려면 [az vm show](/cli/azure/vm#az_vm_show)를 사용합니다.

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

새 Docker 호스트로 SSH를 수행합니다. 이전 단계에서 자신의 사용자 이름 및 DNS 이름을 제공합니다.

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Docker 호스트에 로그인되면 NGINX 컨테이너를 실행하겠습니다.

```bash
sudo docker run -d -p 80:80 nginx
```

출력 내용은 NGINX 이미지가 다운로드되고 컨테이너가 시작되는 다음 예제와 유사합니다.

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

다음과 같이 Docker 호스트에서 실행 중인 컨테이너의 상태를 확인합니다.

```bash
sudo docker ps
```

출력 내용은 NGINX 컨테이너를 실행 중이고 TCP 포트 80 및 443이 전달되는 것을 보여주는 다음 예제와 유사합니다.

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

작동되는 컨테이너를 보려면 웹 브라우저를 열고 Docker 호스트의 DNS 이름을 입력합니다.

![ngnix 컨테이너 실행](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 확장 템플릿 참조
이전 예제는 기존의 빠른 시작 템플릿을 사용했습니다. 자신만의 Resource Manager 템플릿을 사용하여 Azure Docker VM 확장을 배포할 수도 있습니다. 이렇게 하려면 VM의 `vmName`을 적합하게 정의하는 다음 내용을 Resource Manager 템플릿에 추가합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Resource Manager 템플릿 사용에 대한 자세한 연습은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Docker Compose](https://docs.docker.com/compose/overview/)를 사용하여 [Docker 디먼 TCP 포트를 구성](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket)하고, [Docker 보안](https://docs.docker.com/engine/security/security/)을 이해하거나 컨테이너를 배포할 수도 있습니다. Azure Docker VM 확장 자체에 대한 자세한 내용은 [GitHub 프로젝트](https://github.com/Azure/azure-docker-extension/)를 참조하세요.

Azure의 추가적인 Docker 배포 옵션에 대한 자세한 내용을 읽어보세요.

* [Azure 드라이버로 Docker Machine 사용](docker-machine.md)  
* [Azure 가상 머신에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작](docker-compose-quickstart.md)
* [Azure 컨테이너 서비스 클러스터 배포](../../container-service/dcos-swarm/container-service-deployment.md)

