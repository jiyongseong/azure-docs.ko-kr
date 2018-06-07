---
title: Docker 이미지를 개인 Azure 레지스트리에 푸시
description: Docker CLI를 사용하여 Azure의 개인 컨테이너 레지스트리로 Docker 이미지 밀어넣기 및 끌어오기
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d729a45b28ad02a652c265974d46fe1aaf752198
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Docker CLI를 사용하여 개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기 및 끌어오기

Azure Container Registry는 [Docker Hub](https://hub.docker.com/)에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 [Docker](http://hub.docker.com) 컨테이너 이미지를 저장하고 관리합니다. [Docker 명령줄 인터페이스](https://docs.docker.com/engine/reference/commandline/cli/)(Docker CLI)를 사용하여 컨테이너 레지스트리에서 [로그인](https://docs.docker.com/engine/reference/commandline/login/), [푸시](https://docs.docker.com/engine/reference/commandline/push/), [풀](https://docs.docker.com/engine/reference/commandline/pull/) 및 기타 작업을 수행합니다.

다음 단계에서는 공개 Docker Hub 레지스트리에서 공식 [Nginx 이미지](https://store.docker.com/images/nginx)를 다운로드하고 개인 Azure 컨테이너 레지스트리용으로 태그를 지정하고, 레지스트리에 푸시한 다음 레지스트리에서 풀합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)을 사용합니다.
* **Docker CLI** - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker](https://docs.docker.com/engine/installation/)를 설치합니다.

## <a name="log-in-to-a-registry"></a>레지스트리에 로그인

개인 컨테이너 레지스트리에 대해 [몇 가지 방법으로 인증](container-registry-authentication.md)할 수 있습니다. 명령줄에서 작업할 때 권장되는 방법은 Azure CLI 명령 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login)입니다. 예를 들어 이름이 *myregistry*인 레지스트리에 로그인하려면

```azurecli
az acr login --name myregistry
```

[docker login](https://docs.docker.com/engine/reference/commandline/login/)을 사용하여 로그인할 수도 있습니다. 다음 예제는 Azure Active Directory [서비스 주체](../active-directory/active-directory-application-objects.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 [서비스 주체를 할당](container-registry-authentication.md#service-principal)할 수 있습니다.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

두 명령 모두 완료되면 `Login Succeeded`를 반환합니다. `docker login`을 사용할 경우 `--password-stdin` 매개 변수 사용을 권고하는 보안 경고도 표시될 수 있습니다. 이 문서의 범위 외부에서 사용하는 경우 이 모범 사례를 따르는 것이 좋습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하세요.

> [!TIP]
> `docker login`을 사용하고 레지스트리에 푸시하기 위해 이미지에 태그할 때는 항상 정규화된 레지스트리 이름(전체 소문자)를 지정합니다.  이 문서의 예제에서 정규화된 이름은 *myregistry.azurecr.io*입니다.

## <a name="pull-the-official-nginx-image"></a>공식 Nginx 이미지 풀하기

먼저 공개 Nginx 이미지를 로컬 컴퓨터로 풀합니다.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

다음 [docker run](https://docs.docker.com/engine/reference/run/) 명령을 실행하여 포트 8080에서 Nginx 컨테이너의 로컬 인스턴스를 대화형으로 시작합니다(`-it`). `--rm` 인수는 사용자가 해당 컨테이너를 중지하면 제거되어야 함을 지정합니다.

```Bash
docker run -it --rm -p 8080:80 nginx
```

[http://localhost:8080](http://localhost:8080)으로 이동하여 실행 중인 컨테이너에서 Nginx가 서비스하는 기본 웹 페이지를 봅니다. 다음과 유사한 결과가 표시됩니다.

![로컬 컴퓨터의 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

`-it`를 통해 대화형으로 컨테이너를 시작했으므로 브라우저로 이동한 후 명령줄에서 Nginx 서버의 출력을 볼 수 있습니다.

컨테이너를 중지하고 제거하려면 `Control`+`C`를 누릅니다.

## <a name="create-an-alias-of-the-image"></a>이미지의 별칭 만들기 

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/)를 사용하여 레지스트리에 대한 정규화된 경로를 통한 이미지의 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 `samples` 네임스페이스를 지정합니다.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

네임스페이스를 통한 태그 지정에 대한 자세한 내용은 [Azure Container Registry 모범 사례](container-registry-best-practices.md#repository-namespaces)의 [리포지토리 네임스페이스](container-registry-best-practices.md) 섹션을 참조하세요.

## <a name="push-the-image-to-your-registry"></a>레지스트리에 이미지 푸시하기

이제 개인 레지스트리에 대한 정규화된 경로로 이미지에 태그를 지정했으므로 [docker push](https://docs.docker.com/engine/reference/commandline/push/)로 레지스트리에 푸시할 수 있습니다.

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>레지스트리에서 이미지 풀하기

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 레지스트리에서 이미지를 풀합니다.

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Nginx 컨테이너 시작

[docker run](https://docs.docker.com/engine/reference/run/) 명령을 사용하여 레지스트리에서 풀한 이미지를 실행합니다.

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

[http://localhost:8080](http://localhost:8080)으로 이동하여 실행 중인 컨테이너를 봅니다.

컨테이너를 중지하고 제거하려면 `Control`+`C`를 누릅니다.

## <a name="remove-the-image-optional"></a>이미지 제거(선택 사항)

Nginx 이미지가 더 이상 필요하지 않으면 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) 명령을 사용하여 로컬로 삭제할 수 있습니다.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Azure 컨테이너 레지스트리에서 이미지를 제거하려면 Azure CLI 명령 [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete)를 사용합니다. 예를 들어, 다음 명령은 태그에서 참조하는 매니페스트, 모든 연결된 계층 데이터, 기타 해당 매니페스트를 참조하는 모든 태그를 삭제합니다.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>다음 단계

이제 기본 사항을 배웠으니 레지스트리 사용을 시작할 준비가 되었습니다. 레지스트리에서 다음에 컨테이너 이미지를 배포합니다.

* [AKS(Azure Kubernetes Service)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
