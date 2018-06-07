---
title: Azure DC/OS 클러스터의 컨테이너 부하 분산
description: Azure Container Service DC/OS 클러스터에 있는 여러 컨테이너에 대해 부하를 분산합니다.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 62967636a4d80f72f731a666947d5d4d5e47f7e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Azure Container Service DC/OS 클러스터에서 컨테이너 부하 분산

이 문서에서는 Marathon-LB를 사용하여 DC/OS로 관리되는 Azure Container Service에 내부 부하 분산 장치를 만드는 방법을 살펴봅니다. 이 구성은 응용 프로그램을 수평으로 확장할 수 있습니다. 또한 부하 분산 장치를 공용 클러스터에 배치하고 응용 프로그램 컨테이너를 개인 클러스터에 배치하여 공용 및 개인 에이전트 클러스터를 활용할 수 있습니다. 이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Marathon Load Balancer 구성
> * 부하 분산 장치를 사용하여 응용 프로그램 배포
> * Azure Load Balancer 구성

이 자습서의 단계를 완료하려면 ACS DC/OS 클러스터가 필요합니다. 필요한 경우 [이 스크립트 샘플](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)을 사용하여 클러스터를 만들 수 있습니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>부하 분산 개요

Azure Container Service DC/OS 클러스터에는 두 개의 부하 분산 계층이 있습니다. 

**Azure Load Balancer**는 공용 진입점(최종 사용자가 액세스하는)을 제공합니다. Azure LB는 Azure Container Service에서 자동으로 제공하며 기본적으로 포트 80, 443 및 8080을 노출하도록 구성됩니다.

**Marathon Load Balancer(marathon-lb)** 는 요청을 서비스하는 컨테이너 인스턴스로 인바운드 요청을 라우팅합니다. 웹 서비스를 제공하는 컨테이너를 확장하면 marathon-lb는 동적으로 조정됩니다. 이 부하 분산 장치는 Container Service에 기본적으로 제공되지 않지만 쉽게 설치할 수 있습니다.

## <a name="configure-marathon-load-balancer"></a>Marathon Load Balancer 구성

Marathon Load Balancer는 배포한 컨테이너를 기준으로 동적으로 자체 재구성합니다. 컨테이너 또는 에이전트 손실에 대한 복원력이 있으며 손실이 발생하는 경우 Apache Mesos가 다른 곳에서 컨테이너를 다시 시작하고 marathon-lb가 상황에 맞게 조정됩니다.

공용 에이전트의 클러스터에 Marathon Load Balancer를 설치하려면 다음 명령을 실행합니다.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>부하 분산된 응용 프로그램 배포

이제 marathon-lb 패키지가 있으므로 부하를 분산하려는 응용 프로그램 컨테이너를 배포할 수 있습니다. 

먼저 공개적으로 노출된 에이전트의 FQDN을 가져옵니다.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

다음으로 *hello-web.json*이라는 파일을 만들고 다음 내용을 복사합니다. `HAPROXY_0_VHOST` 레이블을 DC/OS 에이전트의 FQDN으로 업데이트해야 합니다. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
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
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

DC/OS CLI를 사용하여 응용 프로그램을 실행합니다. 기본적으로 Marathon은 개인 클러스터에 응용 프로그램을 배포합니다. 다시 말해서 위의 배포는 부하 분산 장치를 통해서만 액세스할 수 있으며, 이는 일반적으로 고객이 원하는 동작입니다.

```azurecli-interactive
dcos marathon app add hello-web.json
```

응용 프로그램이 배포되면 에이전트 클러스터의 FQDN으로 이동하여 부하 분산된 응용 프로그램을 살펴봅니다.

![부하 분산된 응용 프로그램의 이미지](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Azure Load Balancer 구성

Azure Load Balancer는 기본적으로 포트 80, 8080 및 443을 노출합니다. 이러한 세 가지 포트 중 하나를 사용하는 경우(위의 예에서 실행하는 것처럼) 수행할 작업이 없습니다. 에이전트 부하 분산 장치의 FQDN에 도달할 수 있으며 새로 고칠 때마다 라운드 로빈 방식으로 세 개의 웹 서버 중 하나에 도달할 것입니다. 

다른 포트를 사용하는 경우 부하 분산 장치에서 사용한 포트에 대해 라운드 로빈 규칙 및 프로브를 추가해야 합니다. 이는 [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md)에서 `azure network lb rule create` 및 `azure network lb probe create` 명령으로 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 포함하여 Marathon 및 Azure 부하 분산 장치를 사용하여 ACS에서 부하를 분산하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Marathon Load Balancer 구성
> * 부하 분산 장치를 사용하여 응용 프로그램 배포
> * Azure Load Balancer 구성

다음 자습서로 넘어가서 Azure에서 Azure 저장소를 DC/OS와 통합하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [DC/OS 클러스터에 Azure File Share 탑재](container-service-dcos-fileshare.md)