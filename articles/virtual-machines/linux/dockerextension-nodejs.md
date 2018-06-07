---
title: Azure CLI 1.0에서 Azure Docker VM 확장 사용 | Microsoft Docs
description: Docker VM 확장을 사용하여 Azure에서 Resource Manager 템플릿을 사용하여 Docker 환경을 빠르고 안전하게 배포하는 방법에 대해 알아보세요.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 2f981a50887138660d26a9d011870a05f1270c94
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Azure CLI 1.0에서 Docker VM 확장을 사용하여 Azure에서 Docker 환경 만들기
Docker는 Linux(및 Windows)에서 컨테이너와 함께 빠르게 사용할 수 있는 인기 있는 컨테이너 관리 및 이미징 플랫폼입니다. Azure에는 필요에 맞게 Docker를 배포할 수 있는 다양한 방법이 있습니다. 이 문서는 Docker VM 확장 및 Azure Resource Manager 템플릿 사용에 중점을 두고 있습니다. 

Docker Machine 및 Azure Container Service 사용을 비롯한 다른 배포 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* 앱을 신속하게 프로토타이핑하려면 [Docker Machine](docker-machine.md)을 사용하여 단일 Docker 호스트를 만듭니다.
* 보다 크고 안정적인 환경을 위해 Azure Docker VM 확장을 사용할 수 있습니다. 일관된 컨테이너 배포를 생성하도록 [Docker Compose](https://docs.docker.com/compose/overview/)를 지원하기도 합니다. 이 문서는 Azure Docker VM 확장 사용을 자세히 설명합니다.
* 추가 예약 및 관리 도구를 제공하는 프로덕션이 준비된 확장성 있는 환경을 구축하려면 [Azure Container Services에 Docker Swarm 클러스터](../../container-service/dcos-swarm/container-service-deployment.md)를 배포합니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](dockerextension.md) - 리소스 관리 배포 모델용 차세대 CLI 

## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM 확장 개요
Azure Docker VM 확장은 Linux 가상 머신(VM)에 Docker 데몬, Docker 클라이언트 및 Docker Compose를 설치하고 구성합니다. Azure Docker VM 확장을 사용하면 직접 Docker 호스트를 만들거나 Docker Machine을 사용하는 것보다 더 많은 제어와 가능을 사용할 수 있습니다. [Docker Compose](https://docs.docker.com/compose/overview/)와 같은 이러한 추가 기능은 Azure Docker VM 확장을 보다 강력한 개발자 또는 프로덕션 환경에 적합하도록 만들어 줍니다.

Azure Resource Manager 템플릿은 환경의 전체 구조를 정의합니다. 템플릿을 사용하면 Docker 호스트 VM, 저장소, RBAC(역할 기반 Access Control) 및 진단과 같은 리소스를 만들고 구성할 수 있습니다. 이러한 템플릿을 다시 사용하여 일관된 방식으로 추가 배포를 만들 수 있습니다. Azure Resource Manager 및 템플릿에 대한 자세한 내용은 [Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요. 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Azure Docker VM 확장을 사용하여 템플릿 배포
기존의 빠른 시작 템플릿을 사용하여 Docker 호스트를 설치 및 구성하기 위해 Azure Docker VM 확장을 사용하는 Ubuntu VM을 만들겠습니다. 템플릿은 [Docker를 사용한 간단한 Ubuntu VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)에서 볼 수 있습니다. 

[최신 Azure CLI](../../cli-install-nodejs.md)가 설치되어 있고 다음과 같이 Resource Manager 모드를 사용하여 로그인되어 있어야 합니다.

```azurecli
azure config mode arm
```

Azure CLI를 사용하여 템플릿 URI를 지정하고 템플릿을 배포합니다. 다음 예제에서는 *westus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 다음과 같이 자신만의 리소스 그룹 이름과 위치를 사용합니다.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

저장소 계정 이름을 묻는 프롬프트에 답하고 사용자 이름과 암호를 제공하고 DNS 이름을 제공합니다. 다음 예제와 유사하게 출력됩니다.

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Azure CLI는 몇 초 후에 프롬프트를 다시 표시하지만, Azure Docker VM 확장에 의해 Docker 호스트가 아직 생성 및 구성되고 있습니다. 배포를 완료하려면 몇 분 정도 소요됩니다. `azure vm show` 명령을 사용하여 Docker 호스트 상태에 대한 자세한 정보를 볼 수 있습니다.

다음 예제는 *myResourceGroup*이라는 리소스 그룹에서 *myDockerVM*(템플릿의 기본 이름입니다. 이 이름을 변경하지 마세요.)이라는 VM의 상태를 확인합니다. 앞의 단계에서 만든 리소스 그룹의 이름을 입력합니다.

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

`azure vm show` 명령의 출력은 다음 예제와 비슷합니다.

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

출력의 위쪽에 VM의 **ProvisioningState**가 표시됩니다. 출력에 *Succeeded*가 표시되면 배포가 완료된 것이며 VM에 대해 SSH를 실행할 수 있습니다.

출력의 끝 쪽으로 *FQDN*이 Docker 호스트의 정규화된 도메인 이름을 표시합니다. 이 FQDN은 나머지 단계에서 Docker 호스트에 대한 SSH를 수행하는 데 사용됩니다.

## <a name="deploy-your-first-nginx-container"></a>첫 번째 nginx 컨테이너 배포
배포가 완료되면 로컬 컴퓨터에서 새 Docker 호스트에 SSH를 수행합니다. 다음과 같이 자신의 사용자 이름과 FQDN을 입력합니다.

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Docker 호스트에 로그인되면 nginx 컨테이너를 실행하겠습니다.

```bash
sudo docker run -d -p 80:80 nginx
```

출력 내용은 nginx 이미지가 다운로드되고 컨테이너가 시작되는 다음 예제와 유사합니다.

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

출력 내용은 nginx 컨테이너를 실행 중이고 TCP 포트 80 및 443이 전달되는 것을 보여주는 다음 예제와 유사합니다.

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

작동되는 컨테이너를 보려면 웹 브라우저를 열고 Docker 호스트의 FQDN 이름을 입력합니다.

![ngnix 컨테이너 실행](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM 확장 템플릿 참조
이전 예제는 기존의 빠른 시작 템플릿을 사용했습니다. 자신만의 Resource Manager 템플릿을 사용하여 Azure Docker VM 확장을 배포할 수도 있습니다. 이렇게 하려면 VM의 *vmName*을 적합하게 정의하는 다음 내용을 Resource Manager 템플릿에 추가합니다.

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
    "typeHandlerVersion": "1.1",
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

