---
title: Azure Container Instances에서 컨테이너 작업 실행
description: Azure Container Instances를 사용하여 빌드, 테스트 또는 이미지 렌더링 작업에서처럼 완료될 때까지 실행되는 작업을 실행하는 방법에 대해 알아봅니다.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3bbe3e891423b6ad62a1d1093daef304206f3d76
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 작업 실행

Azure Container Instances에서는 컨테이너를 배포하는 것이 쉽고 빠르기 때문에 컨테이너 인스턴스에서 빌드, 테스트 및 이미지 렌더링과 같은 일회성 작업을 실행하기 위한 강력한 플랫폼을 제공합니다.

구성 가능한 다시 시작 정책을 사용하면 프로세스가 완료될 때 컨테이너가 중지되도록 지정할 수 있습니다. 컨테이너 인스턴스는 초 단위로 비용이 청구되기 때문에 작업을 실행하는 컨테이너가 실행되는 동안 사용된 계산 리소스에 대해서만 요금이 부과됩니다.

이 문서에서 제시된 예제는 Azure CLI를 사용합니다. Azure CLI 버전 2.0.21 이상이 [로컬로 설치되어 있거나][azure-cli-install] [Azure Cloud Shell](../cloud-shell/overview.md)에서 CLI를 사용해야 합니다.

## <a name="container-restart-policy"></a>컨테이너 다시 시작 정책

Azure Container Instances에서 컨테이너를 만들 때 세 가지 다시 시작 정책 설정 중 하나를 지정할 수 있습니다.

| 다시 시작 정책   | 설명 |
| ---------------- | :---------- |
| `Always` | 컨테이너 그룹의 컨테이너가 항상 다시 시작됩니다. 컨테이너를 만들 때 다시 시작 정책이 지정되지 않은 경우 적용되는 **기본** 설정입니다. |
| `Never` | 컨테이너 그룹의 컨테이너가 절대로 다시 시작되지 않습니다. 컨테이너가 한 번만 실행됩니다. |
| `OnFailure` | 컨테이너 그룹의 컨테이너가 컨테이너에서 실행된 프로세스가 실패할 때만(0이 아닌 종료 코드로 종료될 때) 다시 시작됩니다. 컨테이너가 한 번 이상 실행됩니다. |

## <a name="specify-a-restart-policy"></a>다시 시작 정책 지정

다시 시작 정책을 지정하는 방법은 Azure CLI, Azure PowerShell cmdlet 또는 Azure Portal에서와 같이 컨테이너 인스턴스를 만드는 방법에 따라 다릅니다. Azure CLI에서 [create a container][az-container-create]를 호출할 때 `--restart-policy` 매개 변수를 지정합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>완료될 때까지 실행 예제

다시 시작 정책의 작동 방식을 보려면 [microsoft/aci-wordcount][aci-wordcount-image] 이미지에서 컨테이너 인스턴스를 만들고 `OnFailure` 다시 시작 정책을 지정합니다. 이 예제 컨테이너는 기본적으로 셰익스피어의 [Hamlet](http://shakespeare.mit.edu/hamlet/full.html) 텍스트를 분석하고, 가장 많이 쓰이는 10개의 단어를 STDOUT에 쓰고 종료하는 Python 스크립트를 실행합니다.

다음 [az container create][az-container-create] 명령으로 예제 컨테이너를 실행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances는 컨테이너를 시작한 다음 응용 프로그램(또는 이 경우 스크립트)이 종료될 때 컨테이너를 중지합니다. Azure Container Instances가 다시 시작 정책이 `Never` 또는 `OnFailure`인 컨테이너를 중지하면 컨테이너의 상태가 **Terminated**으로 설정됩니다. [az container show][az-container-show] 명령을 사용하여 컨테이너의 상태를 확인할 수 있습니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

예제 출력:

```bash
"Terminated"
```

예제 컨테이너의 상태가 *Terminated*로 표시되면 컨테이너 로그를 확인하여 작업 출력을 볼 수 있습니다. 스크립트의 출력을 보려면 [az container logs][az-container-logs] 명령을 실행합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

출력

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

이 예제는 스크립트가 STDOUT으로 보낸 출력을 보여 줍니다. 그러나 컨테이너 작업은 나중에 검색하기 위해 출력을 영구적 저장소에 쓸 수도 있습니다. 예를 들어 [Azure 파일 공유](container-instances-mounting-azure-files-volume.md)에 쓸 수 있습니다.

## <a name="configure-containers-at-runtime"></a>런타임 시 컨테이너 구성

컨테이너 인스턴스를 만드는 경우 컨테이너가 시작될 때 실행할 사용자 지정 **명령줄**을 지정하고 **환경 변수**를 설정할 수 있습니다. 배치 작업에서 이 설정을 사용하여 작업별 구성으로 각 컨테이너를 준비할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

컨테이너에서 실행하는 응용 프로그램 또는 스크립트의 동적 구성을 제공하려면 컨테이너에 환경 변수를 설정합니다. 이는 `docker run`에 대한 `--env` 명령줄 인수와 유사합니다.

예를 들어 컨테이너 인스턴스를 만들 때 다음 환경 변수를 지정하여 예제 컨테이너에서 스크립트의 동작을 수정할 수 있습니다.

*NumWords*: STDOUT으로 전송된 단어 수입니다.

*MinLength*: 계산되는 단어의 최소 문자 수입니다. 숫자가 높을수록 "of" 및 "the"와 같은 일반적인 단어를 무시합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

컨테이너의 환경 변수에 `NumWords=5` 및 `MinLength=8`을 지정하면 컨테이너 로그에 다른 출력이 표시됩니다. 컨테이너 상태가 *Terminated*(상태를 확인하기 위해 `az container show` 사용)로 표시되면 로그를 표시하여 새 출력을 확인합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

출력

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>명령줄 재정의

컨테이너 인스턴스를 만들 때 명령줄을 지정하여 컨테이너 이미지로 굽는 명령줄을 재정의합니다. 이는 `docker run`에 대한 `--entrypoint` 명령줄 인수와 유사합니다.

예를 들어 다른 명령줄을 지정하여 예제 컨테이너가 *Hamlet* 이외의 텍스트를 분석하도록 할 수 있습니다. *wordcount.py* 컨테이너에서 실행하는 Python 스크립트는 URL을 인수로 받아들이고 기본값 대신 해당 페이지의 콘텐츠를 처리합니다.

예를 들어 *Romeo and Juliet*에서 상위 3개의 5글자 단어를 결정하려면 다음을 수행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

다시 한번 컨테이너가 *Terminated*가 되면 컨테이너의 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

출력

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

### <a name="persist-task-output"></a>작업 출력 유지

완료될 때까지 실행되는 컨테이너 출력을 유지하는 방법에 대한 자세한 내용은 [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](container-instances-mounting-azure-files-volume.md)를 참조하세요.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
