---
title: AKS 및 Azure Container Registry에서 Draft 사용
description: AKS 및 Azure Container Registry에서 Draft 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5dfecefb6ce1d74e02c64371a864a6d3b07a2e1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Draft 사용

Draft는 Kubernetes 클러스터에 이러한 컨테이너를 포함하고 배포하게 하면서 집중 개발의 "내부 루프" 곧 개발 주기에 자유롭게 집중하게 하는 오픈 소스 도구입니다. Draft는 코드가 개발되고 있을 때 그러나 버전 제어에 커밋하기 전에 작동합니다. Draft를 사용하면 코드가 변경될 때 Kubernetes에 응용 프로그램을 신속하게 재배포할 수 있습니다. Draft에 대한 자세한 내용은 [Github의 Draft 설명서][draft-documentation]를 참조하세요.

이 문서에서는 AKS에서 Kubernetes 클러스터와 함께 Draft를 사용하는 방법에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 AKS 클러스터를 만들고 클러스터와 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

ACR(Azure Container Registry)에서 개인 Docker 레지스트리도 필요합니다. ACR 인스턴스를 배포하는 지침은 [Azure Container Registry 빠른 시작][acr-quickstart]을 참조하세요.

또한 Helm을 AKS 클러스터에 설치해야 합니다. Helm을 설치하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)에서 Helm 사용][aks-helm]을 참조하세요.

마지막으로 [Docker](https://www.docker.com)를 설치해야 합니다.

## <a name="install-draft"></a>Draft 설치

Draft CLI는 개발 시스템에서 실행되는 클라이언트로, Kubernetes 클러스터에 코드를 신속하게 배포할 수 있습니다.

> [!NOTE]
> 버전 0.12 전에 Draft를 설치했다면 먼저 `helm delete --purge draft`을 사용하여 클러스터에서 Draft를 삭제한 다음, `rm -rf ~/.draft`를 실행하여 로컬 구성을 제거해야 합니다. MacOS에서 작업하는 경우 `brew upgrade draft`를 실행합니다.

Mac에서 Draft CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션에 대해서는 [Draft 설치 가이드][draft-documentation]를 참조하세요.

```console
brew tap azure/draft
brew install draft
```

이제 `draft init` 명령으로 Draft를 초기화합니다.

```console
draft init
```

## <a name="configure-draft"></a>Draft 구성

Draft는 컨테이너 이미지를 로컬에서 빌드한 다음, 로컬 레지스트리(Minikube의 경우)에서 이미지를 배포하거나 사용할 이미지 레지스트리를 지정해야 합니다. 이 예제에서는 ACR(Azure Container Registry)를 사용함으로써 AKS 클러스터와 ACR 레지스트리 간에 트러스트 관계를 설정하고 Draft를 구성하여 컨테이너를 ACR로 푸시합니다.

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS 클러스터와 ACR 사이의 트러스트 만들기

AKS 클러스터와 ACR 레지스트리 간에 트러스트를 설정하려면 ACR 리포지토리 범위를 통해 참가자 역할을 추가하여 AKS가 사용된 Azure Active Directory 서비스 주체를 수정합니다. 이렇게 하려면 다음 명령을 실행합니다. _&lt;aks-rg-name&gt;_ 및 _&lt;aks-cluster-name&gt;_ 을 AKS 클러스터의 이름 및 리소스 그룹으로 교체하고 _&lt;acr-rg-nam&gt;_ 및 _&lt;acr-repo-name&gt;_ 을 트러스트를 만들려는 ACR 리포지토리의 리소스 그룹 및 리포지토리 이름으로 바꾸십시오.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(ACR에 액세스하기 위한 이러한 단계 및 기타 인증 메커니즘은 [ACR을 사용하여 인증하기](../container-registry/container-registry-auth-aks.md)에 있습니다.)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>ACR에서 배포하고 푸시할 Draft 구성

AKS와 ACR 간에 트러스트 관계가 있으므로 다음 단계를 통해 AKS 클러스터에서 ACR을 사용할 수 있습니다.
1. _&lt;레지스트리 이름&lt;_ 이 ACR 레지스트리 이름인 경우 `draft config set registry <registry name>.azurecr.io`를 실행하여 Draft 구성 `registry` 값을 설정합니다.
2. `az acr login -n <registry name>`을 실행하여 ACR 레지스트리에 로그온합니다.

로컬로 ACR에 로그온하고 AKS와 ACR 간에 트러스트 관계를 만들었으므로 ACR에서 AKS로 푸시하거나 풀하기 위해 암호는 필요없습니다. Azure Active Directory를 사용하여 Azure Resource Manager 수준에서 인증이 발생합니다.

## <a name="run-an-application"></a>응용 프로그램 실행

Draft 리포지토리에는 Draft 데모를 보여 주는 데 사용할 수 있는 몇 가지 샘플 응용 프로그램이 포함되어 있습니다. 리포지토리의 복제된 복사본을 만듭니다.

```console
git clone https://github.com/Azure/draft
```

Java 예제 디렉터리로 변경합니다.

```console
cd draft/examples/example-java/
```

`draft create` 명령을 사용하여 프로세스를 시작합니다. 이 명령은 Kubernetes 클러스터에서 응용 프로그램을 실행하는 데 사용되는 아티팩트를 만듭니다. 이러한 항목에는 Dockerfile, Helm 차트 포함 및 Draft 구성 파일인 `draft.toml` 파일이 포함됩니다.

```console
draft create
```

출력

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Kubernetes 클러스터에서 응용 프로그램을 실행하려면 `draft up` 명령을 사용합니다. 이 명령은 Dockerfile을 빌드하여 컨테이너 이미지를 만들고 이미지를 ACR로 푸시하고 마지막으로 Helm 차트를 설치하여 AKS에서응용 프로그램을 시작합니다.

처음 실행될 때는 컨테이너 이미지를 푸시하고 풀하는 데 어느 정도 시간이 걸릴 수 있습니다. 기본 계층이 캐시되면 걸리는 시간이 크게 줄어듭니다.

```console
draft up
```

출력

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>응용 프로그램 테스트

응용 프로그램을 테스트하려면 `draft connect` 명령을 사용합니다. 이 명령은 안전한 로컬 연결을 허용하는 Kubernetes Pod로 연결을 위임합니다. 완료되면 제공된 URL에서 응용 프로그램에 액세스할 수 있습니다.

경우에 따라 컨테이너 이미지를 다운로드하고 응용 프로그램을 시작하는 데 몇 분이 걸릴 수 있습니다. 응용 프로그램에 액세스할 때 오류가 발생하면 연결을 다시 시도합니다.

```console
draft connect
```

출력

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

이제 http://localhost:46143로 이동하여 응용 프로그램을 테스트합니다(앞의 예제의 경우 포트가 다를 수 있음). 응용 프로그램 테스트가 끝나면 `Control+C`를 사용하여 프록시 연결을 중지합니다.

> [!NOTE]
> `draft up --auto-connect` 명령을 사용하여 응용 프로그램을 빌드 및 배포할 수 있으며 즉각 첫 번째 실행 중인 컨테이너에 연결하여 반복 주기를 더 빠르게 할 수 있습니다.

## <a name="expose-application"></a>응용 프로그램 공개

Kubernetes에서 응용 프로그램을 테스트할 때 인터넷에서 응용 프로그램을 사용할 수 있도록 하는 것이 좋습니다. 이 작업은 [수신 컨트롤러][kubernetes-ingress] 또는 [LoadBalancer][kubernetes-service-loadbalancer] 유형의 Kubernetes 서비스를 사용하여 수행할 수 있습니다. 이 문서는 Kubernetes 서비스를 사용하여 자세히 설명합니다.


먼저, `LoadBalancer` 유형의 서비스를 만들도록 지정하기 위해 Draft 팩을 업데이트해야 합니다. 이렇게 하려면 `values.yaml` 파일에서 서비스 유형을 업데이트합니다.

```console
vi charts/java/values.yaml
```

`service.type` 속성을 찾고 값을 `ClusterIP`에서 `LoadBalancer`로 업데이트합니다.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

`draft up`을 실행하여 응용 프로그램을 다시 실행합니다.

```console
draft up
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. 진행 상황을 모니터링하려면 조사식과 함께 `kubectl get service` 명령을 사용합니다.

```console
kubectl get service -w
```

처음에는 서비스에 대한 *EXTERNAL-IP*가 `pending`으로 표시됩니다.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

EXTERNAL-IP 주소가 `pending`에서 `IP address`로 변경되면 `Control+C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

응용 프로그램을 보려면 외부 IP 주소로 이동합니다.

```console
curl 52.175.224.118
```

출력

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>응용 프로그램 반복

이제 Draft가 구성되었고 응용 프로그램이 Kubernetes에서 실행 중이므로 코드 반복에 대해 설정되었습니다. 업데이트된 코드를 테스트할 때마다 `draft up` 명령을 실행하여 실행 중인 응용 프로그램을 업데이트합니다.

이 예제에서는 Java Hello World 응용 프로그램을 업데이트합니다.

```console
vi src/main/java/helloworld/Hello.java
```

Hello World 텍스트를 업데이트합니다.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

`draft up --auto-connect` 명령을 실행하여 Pod가 응답할 준비가 되자마자 응용 프로그램을 재배포합니다.

```console
draft up --auto-connect
```

출력

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

마지막으로 응용 프로그램을 보고 업데이트를 확인합니다.

```console
curl 52.175.224.118
```

출력

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>다음 단계

Draft 사용에 대한 자세한 내용은 Github의 Draft 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Draft 설명서][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
