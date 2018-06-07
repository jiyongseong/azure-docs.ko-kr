---
title: CoScale을 사용하여 Azure Kubernetes 클러스터 모니터링
description: CoScale을 사용하여 Azure Container Service에서 Kubernetes 클러스터 모니터링
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 16580307193bbb7eb9b401eb1b14356e8589d6e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>CoScale을 사용하여 Azure Container Service Kubernetes 클러스터 모니터링

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

이 문서에서는 [CoScale](https://www.coscale.com/) 에이전트를 배포하여 Azure Container Service의 Kubernetes 클러스터에 있는 모든 노드 및 컨테이너를 모니터링하는 방법을 보여 줍니다. 이러한 구성을 위해서는 CoScale 계정이 필요합니다. 


## <a name="about-coscale"></a>CoScale 정보 

CoScale은 여러 오케스트레이션 플랫폼의 모든 컨테이너에서 메트릭 및 이벤트를 수집하는 모니터링 플랫폼입니다. CoScale은 Kubernetes 환경에 대한 전체 스택 모니터링을 제공합니다. 여기에는 스택의 모든 계층, 즉 OS, Kubernetes, Docker 및 컨테이너 내에서 실행되는 응용 프로그램에 대한 시각화 및 분석을 제공합니다. CoScale은 몇 가지 기본 제공 모니터링 대시보드를 제공하며 운영자 및 개발자가 인프라 및 응용 프로그램 문제를 빠르게 찾을 수 있도록 하는 기본 제공 비정상 감지 기능을 제공합니다.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

이 문서에 나와 있는 것처럼 Kubernetes 클러스터에 에이전트를 설치하여 CoScale을 SaaS 솔루션으로 실행할 수 있습니다. 데이터를 온사이트에 유지하려는 경우 온-프레미스 설치에도 CoScale을 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

먼저 [CoScale 계정을 만들어야 합니다](https://www.coscale.com/free-trial).

이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 `az` Azure CLI 및 `kubectl` 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```azurecli
az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](/cli/azure/install-azure-cli)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```bash
kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>DaemonSet에 CoScale 에이전트 설치
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)은 Kubernetes가 클러스터의 각 호스트에서 컨테이너의 단일 인스턴스를 실행하기 위해 사용합니다.
CoScale 에이전트와 같은 모니터링 에이전트를 실행하는 데 완벽합니다.

CoScale에 로그인한 후 [에이전트 페이지](https://app.coscale.com/)로 이동한 다음 DaemonSet을 사용하여 클러스터에 CoScale 에이전트를 설치합니다. CoScale UI는 에이전트를 만들고 전체 Kubernetes 클러스터 모니터링을 시작하기 위한 단계별 구성 과정을 제공합니다.

![CoScale 에이전트 구성](./media/container-service-kubernetes-coscale/installation.png)

클러스터에서 에이전트를 시작하려면 제공된 명령을 입력합니다.

![CoScale 에이전트 시작](./media/container-service-kubernetes-coscale/agent_script.png)

이것으로 끝입니다. 에이전트가 작동 및 실행되면 몇 분 내에 콘솔에 데이터가 표시됩니다. [에이전트 페이지](https://app.coscale.com/)를 방문하여 클러스터에 대한 요약을 확인하고, 추가 구성 단계를 수행하고, **Kubernetes 클러스터 개요**와 같은 대시보드를 참조합니다.

![Kubernetes 클러스터 개요](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale 에이전트는 클러스터의 새 컴퓨터에 자동으로 배포됩니다. 새 버전이 릴리스되면 에이전트가 자동으로 업데이트됩니다.


## <a name="next-steps"></a>다음 단계

CoScale 모니터링 솔루션에 대한 자세한 내용은 [CoScale 설명서](http://docs.coscale.com/) 및 [블로그](https://www.coscale.com/blog)를 참조하세요. 

