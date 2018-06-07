---
title: Azure Container Service 빠른 시작 - DC/OS 클러스터 배포
description: Azure Container Service 빠른 시작 - DC/OS 클러스터 배포
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9893606943324f5a651cc800feeb86b8966dc15e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-dcos-cluster"></a>DC/OS 클러스터 배포

DC/OS는 컨테이너화된 최신 응용 프로그램 실행을 위한 분산 플랫폼을 제공합니다. Azure Container Service를 통해 프로덕션이 준비된 DC/OS 클러스터를 프로비전하는 작업은 간단하고 빠릅니다. 이 빠른 시작에서는 DC/OS 클러스터를 배포하고 기본 워크로드를 실행하는 데 필요한 기본 단계를 자세히 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/reference-index#az_login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>DC/OS 클러스터 만들기

[az acs create](/cli/azure/acs#az_acs_create) 명령을 사용하여 DC/OS 클러스터를 만듭니다.

다음 예제에서는 *myDCOSCluster*라는 DC/OS 클러스터를 만들고 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

제한 평가판과 같이 Azure 구독의 Azure 리소스 액세스 권한이 제한되는 경우도 있습니다. 사용 가능한 코어 제한으로 인해 배포가 실패하는 경우 [az acs create](/cli/azure/acs#az_acs_create) 명령에 `--agent-count 1`을 추가하여 기본 에이전트 수를 줄이세요. 

몇 분 후 명령이 완료되고 배포에 대한 정보가 반환됩니다.

## <a name="connect-to-dcos-cluster"></a>DC/OS 클러스터에 연결

DC/OS 클러스터를 만들면 SSH 터널을 통해 액세스할 수 있습니다. 다음 명령을 실행하여 DC/OS 마스터의 공용 IP 주소를 반환합니다. 이 IP 주소는 변수에 저장되어 다음 단계에서 사용됩니다.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

SSH 터널을 만들려면 다음 명령을 실행하고 화면에 나타나는 지침을 따르세요. 포트 80이 이미 사용 중인 경우 명령이 실패합니다. 터널링된 포트를 `85:localhost:80`과 같이 사용하지 않는 포트로 업데이트합니다. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

`http://localhost`로 이동하여 SSH 터널을 테스트할 수 있습니다. 80이 아닌 포트를 사용한 경우 위치를 일치하도록 조정합니다. 

SSH 터널이 성공적으로 만들어진 경우 DC/OS 포털이 반환됩니다.

![DCOS UI](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>DC/OS CLI 설치

명령줄에서 DC/OS 클러스터를 관리하는 데 DC/OS 명령줄 인터페이스를 사용합니다. [az acs dcos install-cli](/azure/acs/dcos#install-cli) 명령을 사용하여 DC/OS 클라이언트를 설치합니다. Azure Cloud Shell을 사용하는 경우 DC/OS CLI가 이미 설치되어 있습니다. 

macOS 또는 Linux에서 Azure CLI를 실행하는 경우 sudo를 사용하여 이 명령을 실행해야 합니다.

```azurecli
az acs dcos install-cli
```

CLI를 클러스터 함께 사용하기 전에 SSH 터널을 사용하도록 구성되어야 합니다. 이렇게 하려면 필요한 경우 포트를 조정하여 다음 명령을 실행합니다.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>응용 프로그램 실행

ACS DC/OS 클러스터의 기본 예약 메커니즘은 Marathon입니다. Marathon은 응용 프로그램을 시작하고 DC/OS 클러스터에서 응용 프로그램의 상태를 관리하는 데 사용됩니다. Marathon을 통해 응용 프로그램을 예약하려면 *marathon-app.json*이라는 파일을 만들고 여기에 다음과 같은 내용을 복사합니다. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

DC/OS 클러스터에서 실행되도록 응용 프로그램을 예약하는 다음 명령을 실행합니다.

```azurecli
dcos marathon app add marathon-app.json
```

앱의 배포 상태를 보려면 다음 명령을 실행합니다.

```azurecli
dcos marathon app list
```

**대기** 열 값이 *True*에서 *False*로 전환되는 경우 응용 프로그램 배포가 완료됩니다.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

DC/OS 클러스터 에이전트의 공용 IP 주소를 가져옵니다.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

이 주소를 찾아 NGINX 기본 사이트를 반환합니다.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>DC/OS 클러스터 삭제

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, DC/OS 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 DC/OS 클러스터를 배포하고 클러스터에서 간단한 Docker 컨테이너를 실행했습니다. Azure Container Service에 대해 자세히 알아보려면 ACS 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [ACS DC/OS 클러스터 관리](container-service-dcos-manage-tutorial.md)