---
title: Azure Container Service 자습서 - 응용 프로그램 업데이트
description: Azure Container Service 자습서 - 응용 프로그램 업데이트
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f54179329b521cc861e90f023ff0b010b7ce1f75
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="update-an-application-in-kubernetes"></a>Kubernetes에서 응용 프로그램 업데이트

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Kubernetes에서 응용 프로그램을 배포한 후 새 컨테이너 이미지 또는 이미지 버전을 지정하여 해당 응용 프로그램을 업데이트할 수 있습니다. 응용 프로그램을 업데이트할 때는 배포의 일부분만 동시에 업데이트되도록 업데이트가 스테이징됩니다. 이처럼 스테이징 업데이트가 수행되므로 업데이트 중에도 응용 프로그램을 계속 실행할 수 있습니다. 또한 배포 오류가 발생하는 경우에는 롤백 메커니즘도 제공됩니다. 

이 자습서(전체 7부 중 6부)에서는 샘플 Azure 투표 앱을 업데이트합니다. 완료하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 밀어넣기
> * 업데이트된 컨테이너 이미지 배포

후속 자습서에서는 Kubernetes 클러스터를 모니터링하도록 Log Analytics를 구성합니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다. 

이 자습서에서 사용한 미리 작성된 Docker Compose 파일과 응용 프로그램 소스 코드를 포함하는 응용 프로그램 리포지토리도 복제했습니다. 리포지토리 복제본을 만들었으며 디렉터리를 복제된 디렉터리로 변경했는지 확인하세요. 이 디렉터리 안에는 `azure-vote` 디렉터리와 `docker-compose.yml` 파일이 있습니다.

이러한 단계를 완료하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

## <a name="update-application"></a>응용 프로그램 업데이트

이 자습서에서는 응용 프로그램을 변경했으며 업데이트된 응용 프로그램을 Kubernetes 클러스터로 배포했습니다. 

`azure-vote` 디렉터리 내에서 응용 프로그램 소스 코드를 찾을 수 있습니다. 아무 코드 또는 텍스트 편집기나 사용하여 `config_file.cfg` 파일을 엽니다. 이 예에서는 `vi` 가 사용됩니다.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` 및 `VOTE2VALUE`의 값을 변경한 다음 파일을 저장합니다.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

파일을 저장하고 닫습니다.

## <a name="update-container-image"></a>컨테이너 이미지 업데이트

[docker-compose](https://docs.docker.com/compose/)를 사용하여 프런트 엔드 이미지를 다시 만들고 업데이트된 응용 프로그램을 실행합니다. `--build` 인수를 사용하여 응용 프로그램 이미지를 다시 만들도록 Docker Compose에 명령합니다.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>로컬에서 응용 프로그램 테스트

http://localhost:8080으로 이동하여 업데이트된 응용 프로그램을 확인합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>이미지 태그 지정 및 밀어넣기

`azure-vote-front` 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 

[az acr list](/cli/azure/acr#az_acr_list) 명령을 사용하여 로그인 서버 이름을 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/)를 사용하여 이미지에 태그를 지정합니다. `<acrLoginServer>`를 해당 Azure Container Registry 로그인 서버 이름 또는 공개 레지스트리 호스트 이름으로 바꿉니다. 이미지 버전도 `redis-v2`로 업데이트합니다.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

[docker push](https://docs.docker.com/engine/reference/commandline/push/)를 사용하여 레지스트리에 이미지를 업로드합니다. `<acrLoginServer>`는 실제 Azure Container Registry 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>업데이트된 응용 프로그램 배포

최대 작동 시간을 보장하려면 응용 프로그램 Pod의 여러 인스턴스가 실행되고 있어야 합니다. [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용하여 이 구성을 확인합니다.

```bash
kubectl get pod
```

출력

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

여러 Pod에서 azure-vote-front 이미지를 실행 중인 경우가 아니면 `azure-vote-front` 배포를 확장합니다.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

응용 프로그램을 업데이트하려면 [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) 명령을 사용합니다. `<acrLoginServer>`를 컨테이너 레지스트리의 로그인 서버 또는 호스트 이름으로 업데이트합니다.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

배포를 모니터링하려면 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용합니다. 업데이트된 응용 프로그램이 배포되면 Pod가 종료되고 새 컨테이너 이미지로 다시 만들어집니다.

```azurecli-interactive
kubectl get pod
```

출력

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>업데이트된 응용 프로그램 테스트

`azure-vote-front` 서비스의 외부 IP 주소를 가져옵니다.

```azurecli-interactive
kubectl get service azure-vote-front
```

IP 주소로 이동하여 업데이트된 응용 프로그램을 확인합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램을 업데이트하고 이 업데이트를 Kubernetes 클러스터에 배포했습니다. 다음 작업을 완료했습니다.

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 밀어넣기
> * 업데이트된 응용 프로그램 배포

다음 자습서로 이동하여 Log Analytics로 Kubernetes를 모니터링하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Log Analytics를 사용하여 Kubernetes 모니터링](./container-service-tutorial-kubernetes-monitor.md)