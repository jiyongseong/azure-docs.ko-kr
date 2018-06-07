---
title: "Azure DC/OS 클러스터에 ACR 사용"
description: "Azure Container Service에서 DC/OS 클러스터에 Azure Container Registry 사용"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 90d449de19022b3b427e3d89d5beb18bbd36c6b4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>DC/OS 클러스터에 ACR을 사용하여 응용 프로그램 배포

이 문서에서는 DC/OS 클러스터에 Azure Container Registry를 사용하는 방법을 살펴봅니다. ACR을 사용하여 컨테이너 이미지를 비공개로 저장하고 관리할 수 있습니다. 이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Container Registry 배포(필요한 경우)
> * DC/OS 클러스터에서 ACR 인증 구성
> * Azure Container Registry에 이미지 업로드
> * Azure Container Registry에서 컨테이너 이미지 실행

이 자습서의 단계를 완료하려면 ACS DC/OS 클러스터가 필요합니다. 필요한 경우 [이 스크립트 샘플](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)을 사용하여 클러스터를 만들 수 있습니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Azure Container Registry 배포

필요한 경우 [az acr create](/cli/azure/acr#az_acr_create) 명령으로 Azure Container Registry를 만듭니다. 

다음 예제에서는 임의로 생성된 이름으로 레지스트리를 만듭니다. 또한 레지스트리는 `--admin-enabled` 인수를 사용하여 관리자 계정으로 구성됩니다.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

레지스트리가 만들어지면 Azure CLI에서 다음과 유사한 데이터를 출력합니다. `name` 및 `loginServer`는 이후 단계에서 사용되므로 기록해 둡니다.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

[az acr credential show](/cli/azure/acr/credential) 명령을 사용하여 컨테이너 레지스트리 자격 증명을 가져옵니다. `--name`을 마지막 단계에서 기록한 이름으로 대체합니다. 이후 단계에서 필요하므로 하나의 암호를 기록해 둡니다.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Azure Container Registry에 대한 자세한 내용은 [개인 Docker 컨테이너 레지스트리 소개](../../container-registry/container-registry-intro.md)를 참조하세요. 

## <a name="manage-acr-authentication"></a>ACR 인증 관리

개인 레지스트리에서 이미지를 밀어넣고 끌어오는 일반적인 방법은 먼저 레지스트리를 인증하는 것입니다. 이렇게 하려면 개인 레지스트리에 액세스해야 하는 모든 클라이언트에서 `docker login` 명령을 사용합니다. DC/OS 클러스터는 모두 ACR에 인증되어야 하는 많은 노드를 포함할 수 있으므로 각 노드에서 이 프로세스를 자동화하면 유용합니다. 

### <a name="create-shared-storage"></a>공유 저장소 만들기

이 프로세스에서는 클러스터의 각 노드에 탑재된 Azure 파일 공유를 사용합니다. 아직 공유 저장소를 설정하지 않은 경우 [DC/OS 클러스터 내부에서 파일 공유 설정](container-service-dcos-fileshare.md)을 참조하세요.

### <a name="configure-acr-authentication"></a>ACR 인증 구성

먼저 DC/OS 마스터의 FQDN을 가져와서 변수에 저장합니다.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

DC/OS 기반 클러스터의 마스터(또는 첫 번째 마스터)와의 SSH 연결을 만듭니다. 클러스터를 만들 때 기본값이 아닌 값이 사용된 경우 사용자 이름을 업데이트합니다.

```azurecli-interactive
ssh azureuser@$FQDN
```

다음 명령을 실행하여 Azure Container Registry에 로그인합니다. `--username`을 컨테이너 레지스트리의 이름으로 바꾸고 `--password`를 제공된 암호 중 하나로 바꿉니다. 예제의 마지막 인수 *mycontainerregistry.azurecr.io*를 컨테이너 레지스트리의 loginServer 이름으로 바꿉니다. 

이 명령은 `~/.docker` 경로 아래에 로컬로 인증 값을 저장합니다.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

컨테이너 레지스트리 인증 값이 포함된 압축 파일을 만듭니다.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

클러스터 공유 저장소에 이 파일을 복사합니다. 이 단계를 수행하면 DC/OS 클러스터의 모든 노드에서 파일을 사용할 수 있습니다.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>ACR에 이미지 업로드

이제 개발 컴퓨터나 Docker가 설치된 다른 시스템에서 이미지를 만들어 Azure Container Registry에 업로드합니다.

Ubuntu 이미지에서 컨테이너를 만듭니다.

```azurecli-interactive
docker run ubuntu --name base-image
```

이제 컨테이너를 새 이미지에 캡처합니다. 이미지 이름은 `loginServer/imageName` 형식으로 컨테이너 레지스트리의 `loginServer` 이름을 포함해야 합니다.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Azure Container Registry에 로그인합니다. 이름을 loginServer 이름으로 바꾸고, --username을 컨테이너 레지스트리의 이름으로 바꾸고, --password를 제공된 암호 중 하나로 바꿉니다.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

마지막으로 ACR 레지스트리에 이미지를 업로드합니다. 이 예제에서는 dcos-demo라는 이미지를 업로드합니다.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>ACR에서 이미지 실행

ACR 레지스트리에서 이미지를 사용하려면 파일 이름 *acrDemo.json*을 만들고 다음 텍스트를 이 파일에 복사합니다. 이미지 이름을 컨테이너 레지스트리 loginServer 이름 및 이미지 이름(예: `loginServer/imageName`)으로 바꿉니다. `uris` 속성을 기록해 둡니다. 이 속성은 컨테이너 레지스트리 인증 파일의 위치를 저장하며, 이 경우에는 DC/OS 클러스터의 각 노드에 탑재된 Azure 파일 공유입니다.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

DC/OC CLI를 사용하여 응용 프로그램을 배포합니다.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 포함하여 Azure Container Registry를 사용하도록 DC/OS를 구성했습니다.

> [!div class="checklist"]
> * Azure Container Registry 배포(필요한 경우)
> * DC/OS 클러스터에서 ACR 인증 구성
> * Azure Container Registry에 이미지 업로드
> * Azure Container Registry에서 컨테이너 이미지 실행
