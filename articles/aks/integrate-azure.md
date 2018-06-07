---
title: OSBA(Open Service Broker for Azure)를 사용하여 Azure에서 관리되는 서비스와 통합
description: OSBA(Open Service Broker for Azure)를 사용하여 Azure에서 관리되는 서비스와 통합
services: container-service
author: sozercan
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: a881b08874a157b0d6781ec3859b05eeaeba6676
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>OSBA(Open Service Broker for Azure)를 사용하여 Azure에서 관리되는 서비스와 통합

[Kubernetes 서비스 카탈로그][kubernetes-service-catalog]와 함께 OSBA(Open Service Broker for Azure)를 사용하면 개발자가 Kubernetes에서 Azure 관리 서비스를 활용할 수 있습니다. 이 가이드는 Kubernetes 서비스 카탈로그, OSBA(Open Service Broker for Azure) 및 Kubernetes를 사용하는 Azure에서 관리되는 서비스를 사용하는 응용 프로그램 배포에 중점을 두고 있습니다.

## <a name="prerequisites"></a>필수 조건
* Azure 구독

* Azure CLI 2.0: [로컬로 설치][azure-cli-install]하거나 [Azure Cloud Shell][azure-cloud-shell]에서 사용합니다.

* Helm CLI 2.7+: [로컬로 설치][helm-cli-install]하거나 [Azure Cloud Shell][azure-cloud-shell]에서 사용합니다.

* Azure 구독에서 Contributor 역할로 서비스 사용자를 만드는 권한

* 기존 AKS(Azure Kubernetes Service) 클러스터. AKS 클러스터가 필요한 경우 [AKS 클러스터 만들기][create-aks-cluster] 빠른 시작을 따릅니다.

## <a name="install-service-catalog"></a>서비스 카탈로그 설치

첫 번째 단계는 Helm 차트를 사용하여 Kubernetes 클러스터에 서비스 카탈로그를 설치하는 것입니다. 다음을 사용하여 클러스터에서 Tiller(Helm 서버) 설치를 업그레이드합니다.

```azurecli-interactive
helm init --upgrade
```

이제 서비스 카탈로그 차트를 Helm 리포지토리에 추가합니다.

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

마지막으로 Helm 차트와 함께 서비스 카탈로그를 설치합니다.

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false
```

Helm 차트를 실행한 후에는 다음 명령의 출력에 `servicecatalog`가 나타나는지 확인합니다.

```azurecli-interactive
kubectl get apiservice
```

예를 들어, 다음과 유사한 출력이 표시되어야 합니다(여기에는 잘려서 표시됨).

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Open Service Broker for Azure 설치

다음 단계는 Azure에서 관리하는 서비스에 대한 카탈로그를 포함하는 [Open Service Broker for Azure][open-service-broker-azure]에 설치하는 것입니다. 사용 가능한 Azure 서비스의 예로는 Azure Database for PostgreSQL, Azure Redis Cache, Azure Database for MySQL, Azure Cosmos DB, Azure SQL Database 등이 있습니다.

Open Service Broker for Azure Helm 리포지토리 추가부터 시작합니다.

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

다음 Azure CLI 명령을 사용하여 [서비스 사용자][create-service-principal]를 만듭니다.

```azurecli-interactive
az ad sp create-for-rbac
```

출력은 다음과 유사합니다. `appId`, `password`, `tenant` 값을 다음 단계에서 사용하기 위해 적어둡니다.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

위의 값으로 다음 환경 변수를 설정합니다.

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

이제 Azure 구독 ID를 가져옵니다.

```azurecli-interactive
az account show --query id --output tsv
```

다시 위의 값으로 다음 환경 변수를 설정합니다.

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

이러한 환경 변수를 채웠으니 다음 명령을 실행하여 Helm 차트를 사용하여 Open Service Broker for Azure를 설치합니다.

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

OSBA 배포가 완료되면 서비스 브로커, 서비스 클래스, 서비스 계획 등을 쿼리하기 위해 사용하기 쉬운 명령줄 인터페이스인 [서비스 카탈로그 CLI][service-catalog-cli]을 설치합니다.

다음 명령을 실행하여 서비스 카탈로그 CLI 이진을 설치합니다.

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

이제 설치된 서비스 broker를 나열합니다.

```azurecli-interactive
./svcat get brokers
```

다음과 비슷한 결과가 나타나야 합니다.

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

다음으로 사용 가능한 서비스 클래스를 나열합니다. 표시된 서비스 클래스는 Open Service Broker for Azure를 통해 프로비전할 수 있는 Azure에서 관리되는 서비스입니다.

```azurecli-interactive
./svcat get classes
```

마지막으로 사용 가능한 모든 서비스 계획을 나열합니다. 서비스 계획은 Azure에서 관리되는 서비스의 서비스 계층입니다. 예를 들어 Azure Database for MySQL의 경우 계획의 범위는 DTU(데이터베이스 트랜잭션 단위)가 50개인 기본 계층의 `basic50`부터 DTU가 800개인 표준 계층의 `standard800`까지 입니다.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Azure Database for MySQL을 사용하여 Helm 차트에서 WordPress 설치

이 단계에서는 Helm을 사용하여 WordPress용으로 업데이트된 Helm 차트를 설치합니다. 이 차트는 WordPress가 사용할 수 있는 외부 Azure Database for MySQL 인스턴스를 프로비전합니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

설치를 통해 올바른 리소스가 프로비전되었는지 확인하려면 설치된 서비스 인스턴스와 바인딩을 나열합니다.

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

설치된 비밀 나열:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>다음 단계

이 문서에 따라 서비스 카탈로그를 AKS(Azure Kubernetes Service)에 배포했습니다. Open Service Broker for Azure를 사용하여 Azure에서 관리되는 서비스(이 경우 Azure Database for MySQL)를 사용하는 WordPress 설치를 배포했습니다.

다른 업데이트된 OSBA 기반 Helm 차트에 액세스하려면 [Azure/helm-charts][helm-charts] 저장소를 참조하세요. OSBA와 함께 작동하는 자신만의 차트를 만들려는 경우 [새 차트 만들기][helm-create-new-chart]를 참조하세요.

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
