---
title: Azure Kubernetes에서 Helm을 사용하여 컨테이너 배포
description: Helm 패키징 도구를 사용하여 AKS Kubernetes 클러스터에 컨테이너 배포
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 70e13fb377be3ec501cce5170ed391aac8cb6e5d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Helm 사용

[Helm][helm]은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT*, *Yum* 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 문서에서는 AKS Kubernetes 클러스터에서 Helm을 구성하고 사용하는 과정을 단계별로 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 AKS 클러스터를 만들고 클러스터와 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

## <a name="install-helm-cli"></a>Helm CLI 설치

Helm CLI는 개발 시스템에서 실행되는 클라이언트로, Helm 차트가 있는 응용 프로그램을 시작, 중지 및 관리할 수 있습니다.

Azure Cloud Shell을 사용하는 경우 Helm CLI가 이미 설치되어 있습니다. Mac에서 Helm CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션은 [Helm 설치][helm-install-options]를 참조하세요.

```console
brew install kubernetes-helm
```

출력

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Helm 구성

[helm init][helm-init] 명령은 Kubernetes 클러스터에 Helm 구성 요소를 설치하고 클라이언트 쪽 구성을 만드는 데 사용됩니다. 다음 명령을 실행하여 AKS 클러스터에 Helm을 설치하고 Helm 클라이언트를 구성하세요.

```azurecli-interactive
helm init --upgrade --service-account default
```

출력

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
```

## <a name="find-helm-charts"></a>Helm 차트 찾기

Helm 차트는 Kubernetes 클러스터에 응용 프로그램을 배포하는 데 사용됩니다. 미리 만들어진 Helm 차트를 검색하려면 [helm search][helm-search] 명령을 사용합니다.

```azurecli-interactive
helm search
```

다음과 유사한 출력이 표시되지만, 차트가 훨씬 더 많습니다.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

차트 목록을 업데이트하려면 [helm repo update][helm-repo-update] 명령을 사용합니다.

```azurecli-interactive
helm repo update
```

출력

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm 차트 실행

NGINX 수신 컨트롤러를 배포하려면 [helm install][helm-install] 명령을 사용합니다.

```azurecli-interactive
helm install stable/nginx-ingress --set rbac.create=false --set rbac.createRole=false --set rbac.createClusterRole=false
```

다음과 유사한 출력이 표시되지만, Kubernetes 배포 사용 방법에 대한 지침 등의 추가 정보가 포함되어 있습니다.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Kubernetes에서 NGINX 수신 컨트롤러를 사용하는 방법에 대한 자세한 내용은 [NGINX Ingress Controller][nginx-ingress](NGINX 수신 컨트롤러)를 참조하세요.

## <a name="list-helm-charts"></a>Helm 차트 목록 표시

클러스터에 설치된 차트 목록을 보려면 [helm list][helm-list] 명령을 사용합니다.

```azurecli-interactive
helm list
```

출력

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>다음 단계

Kubernetes 차트 관리 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
