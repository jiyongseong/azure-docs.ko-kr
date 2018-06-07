---
title: Azure Kubernetes 클러스터 모니터링 - Sysdig
description: Sysdig을 사용하여 Azure Container Service에서 Kubernetes 클러스터 모니터링
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 275e71ce054b83c16b9f9cbfe621c6a7e31f79c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Sysdig을 사용하여 Azure Container Service Kubernetes 클러스터 모니터링

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 Azure CLI 및 kubectl 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig은 Azure에서 실행 중인 Kubernetes 클러스터에 대한 컨테이너를 모니터링할 수 있는 외부 모니터링 서비스 회사입니다. Sysdig을 사용하려면 활성 Sysdig 계정이 필요합니다.
[사이트](https://app.sysdigcloud.com)에서 계정에 등록할 수 있습니다.

Sysdig 클라우드 웹 사이트에 로그인하여 사용자 이름을 클릭하면 페이지에 "선택키"가 표시됩니다. 

![Sysdig API 키](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Kubernetes에 Sysdig 에이전트 설치
컨테이너를 모니터링하기 위해 Sysdig은 Kubernetes `DaemonSet`을 사용하여 각 컴퓨터에서 프로세스를 실행합니다.
DaemonSets은 컴퓨터당 컨테이너의 단일 인스턴스를 실행하는 Kubernetes API 개체입니다.
Sysdig의 모니터링 에이전트와 같은 도구를 설치하는 데 적합합니다.

Sysdig daemonset을 설치하려면 먼저 sysdig에서 [템플릿](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml)을 다운로드해야 합니다. 해당 파일을 `sysdig-daemonset.yaml`로 저장합니다.

Linux 및 OS X에서 다음을 실행할 수 있습니다.

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

PowerShell에서:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

그런 후 해당 파일을 편집하여 다음 Sysdig 계정에서 얻은 액세스 키를 삽입합니다.

마지막으로 DaemonSet을 만듭니다.

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>모니터링 보기
일단 설치하여 실행 중이면 에이전트는 Sysdig로 데이터를 다시 공급합니다.  [sysdig 대시보드](https://app.sysdigcloud.com)로 돌아가 컨테이너에 대한 정보를 확인합니다.

[새 대시보드 마법사](https://app.sysdigcloud.com/#/dashboards/new)를 통해 Kubernetes 특정 대시보드를 설치할 수도 있습니다.
