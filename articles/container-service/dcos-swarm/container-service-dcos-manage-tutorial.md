---
title: Azure Container Service 자습서 - DC/OS 관리
description: Azure Container Service 자습서 - DC/OS 관리
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1c06605db3044234f6171d8b784bafb7e7ce759e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Azure Container Service 자습서 - DC/OS 관리

DC/OS는 컨테이너화된 최신 응용 프로그램 실행을 위한 분산 플랫폼을 제공합니다. Azure Container Service를 통해 프로덕션이 준비된 DC/OS 클러스터를 프로비전하는 작업은 간단하고 빠릅니다. 이 빠른 시작에서는 DC/OS 클러스터를 배포하고 기본 워크로드를 실행하는 데 필요한 기본 단계를 자세히 설명합니다.

> [!div class="checklist"]
> * ACS DC/OS 클러스터 만들기
> * 클러스터에 연결
> * DC/OS CLI 설치
> * 클러스터에 응용 프로그램 배포
> * 클러스터에서 응용 프로그램 크기 조정
> * DC/OS 클러스터 노드 크기 조정
> * 기본 DC/OS 관리
> * DC/OS 클러스터 삭제

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-dcos-cluster"></a>DC/OS 클러스터 만들기

먼저 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *westeurope* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

다음으로 [az acs create](/cli/azure/acs#az_acs_create) 명령을 사용하여 DC/OS 클러스터를 만듭니다.

다음 예제에서는 *myDCOSCluster*라는 DC/OS 클러스터를 만들고 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

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

## <a name="install-dcos-cli"></a>DC/OS CLI 설치

[az acs dcos install-cli](/azure/acs/dcos#install-cli) 명령을 사용하여 DC/OS 클라이언트를 설치합니다. Azure Cloud Shell을 사용하는 경우 DC/OS CLI가 이미 설치되어 있습니다. macOS 또는 Linux에서 Azure CLI를 실행하는 경우 sudo를 사용하여 이 명령을 실행해야 합니다.

```azurecli
az acs dcos install-cli
```

CLI를 클러스터 함께 사용하기 전에 SSH 터널을 사용하도록 구성되어야 합니다. 이렇게 하려면 필요한 경우 포트를 조정하여 다음 명령을 실행합니다.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>응용 프로그램 실행

ACS DC/OS 클러스터의 기본 예약 메커니즘은 Marathon입니다. Marathon은 응용 프로그램을 시작하고 DC/OS 클러스터에서 응용 프로그램의 상태를 관리하는 데 사용됩니다. Marathon을 통해 응용 프로그램을 예약하려면 **marathon-app.json**이라는 파일을 만들고 여기에 다음과 같은 내용을 복사합니다. 

```json
{
  "id": "demo-app-private",
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
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
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

**태스크** 열 값이 *0/1*에서 *1/1*로 전환되는 경우 응용 프로그램 배포가 완료됩니다.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Marathon 응용 프로그램 크기 조정

이전 예제에서는 단일 인스턴스 응용 프로그램을 만들었습니다. 응용 프로그램의 세 가지 인스턴스를 사용할 수 있도록 이 배포를 업데이트하려면 **marathon-app.json** 파일을 열고 인스턴스 속성을 3으로 업데이트합니다.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

`dcos marathon app update` 명령을 사용하여 응용 프로그램을 업데이트합니다.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

앱의 배포 상태를 보려면 다음 명령을 실행합니다.

```azurecli
dcos marathon app list
```

**태스크** 열 값이 *1/3*에서 *3/1*로 전환되는 경우 응용 프로그램 배포가 완료됩니다.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>인터넷에 액세스 가능한 앱 실행

ACS DC/OS 클러스터는 인터넷에 액세스할 수 있는 공용 노드 집합과 인터넷에 액세스할 수 없는 개인 노드 집합이라는 두 개의 노드 집합으로 구성됩니다. 기본 집합은 개인 노드이며 마지막 예제에서 사용했습니다.

응용 프로그램이 인터넷에 액세스할 수 있도록 만들려면 공개 노드 집합에 배포합니다. 그러려면 `acceptedResourceRoles` 개체에 `slave_public` 값을 입력합니다.

**nginx public.json**이라는 파일을 만들어서 여기에 다음과 같은 내용을 복사합니다.

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
dcos marathon app add nginx-public.json
```

DC/OS 공용 클러스터 에이전트의 공용 IP 주소를 가져옵니다.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

이 주소를 찾아 NGINX 기본 사이트를 반환합니다.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>DC/OS 클러스터 크기 조정

이전의 예에서 응용 프로그램은 여러 인스턴스로 확장되었습니다. 더 많거나 적은 계산 수용작업량을 제공하도록 DC/OS 인프라를 확장할 수도 있습니다. 그러려면 [az acs scale]() 명령을 사용합니다. 

DC/OS 에이전트의 현재 수를 보려면 [az acs show](/cli/azure/acs#az_acs_show) 명령을 사용합니다.

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

개수를 5로 늘리려면 [az acs scale](/cli/azure/acs#az_acs_scale) 명령을 사용합니다. 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>DC/OS 클러스터 삭제

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, DC/OS 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 포함하는 기본 DC/OS 관리 작업에 대해 배웠습니다. 

> [!div class="checklist"]
> * ACS DC/OS 클러스터 만들기
> * 클러스터에 연결
> * DC/OS CLI 설치
> * 클러스터에 응용 프로그램 배포
> * 클러스터에서 응용 프로그램 크기 조정
> * DC/OS 클러스터 노드 크기 조정
> * DC/OS 클러스터 삭제

다음 자습서로 이동하여 Azure의 DC/OS에서 응용 프로그램의 부하를 분산하는 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [부하 분산 응용 프로그램](container-service-load-balancing.md)