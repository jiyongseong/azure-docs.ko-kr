---
title: 자습서 - Azure Container Registry Build를 사용하여 컨테이너 이미지 빌드 자동화
description: 이 자습서에서는 소스 코드를 Git 리포지토리에 커밋할 때 클라우드에서 컨테이너 이미지 빌드를 자동으로 트리거하도록 빌드 작업을 구성하는 방법을 알아봅니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fba499441d092f4dce09d13d607dfc5de65d98b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>자습서: Azure Container Registry Build를 사용하여 컨테이너 이미지 빌드 자동화

ACR Build는 [빠른 빌드](container-registry-tutorial-quick-build.md) 외에도 *빌드 작업*을 사용하여 자동화된 Docker 컨테이너 이미지 빌드를 지원합니다. 이 자습서에서는 Azure CLI를 사용하여 소스 코드를 Git 리포지토리에 커밋할 때 클라우드에서 이미지 빌드를 자동으로 트리거하는 빌드 작업을 만듭니다.

이 자습서는 시리즈의 2부입니다.

> [!div class="checklist"]
> * 빌드 작업 만들기
> * 빌드 작업 테스트
> * 빌드 상태 보기
> * 코드 커밋을 사용하여 빌드 작업 트리거

이 자습서에서는 [이전 자습서](container-registry-tutorial-quick-build.md)의 단계를 이미 완료했다고 가정합니다. 아직 완료하지 않은 경우 계속 진행하기 전에 이전 자습서의 [필수 조건](container-registry-tutorial-quick-build.md#prerequisites) 섹션에 있는 단계를 완료하세요.

> [!IMPORTANT]
> ACR Build는 현재 미리 보기 상태이며, **미국 동부**(eastus) 및 **유럽 서부**(westeurope) 지역의 Azure 컨테이너 레지스트리에서만 지원됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 사용하려면 Azure CLI 버전 **2.0.32** 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 2.0 설치][azure-cli]를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="get-sample-code"></a>샘플 코드 가져오기

이 자습서에서는 [이전 자습서](container-registry-tutorial-quick-build.md)의 단계를 이미 완료했고, 샘플 리포지토리를 포크 및 복제했다고 가정합니다. 아직 완료하지 않은 경우 계속 진행하기 전에 이전 자습서의 [필수 조건](container-registry-tutorial-quick-build.md#prerequisites) 섹션에 있는 단계를 완료하세요.

### <a name="container-registry"></a>컨테이너 레지스트리

이 자습서를 완료하려면 Azure 구독에 Azure 컨테이너 레지스트리가 있어야 합니다. 레지스트리가 필요하면 [이전 자습서](container-registry-tutorial-quick-build.md) 또는 [빠른 시작: Azure CLI를 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요.

## <a name="build-task"></a>빌드 작업

빌드 작업은 컨테이너 이미지 소스 코드의 위치 및 빌드를 트리거하는 이벤트를 포함하여 자동화된 빌드의 속성을 정의합니다. 빌드 작업에 정의된 이벤트(예: Git 리포지토리에 커밋)가 발생하면 ACR Build는 클라우드에서 컨테이너 이미지 빌드를 시작합니다. 기본적으로 이 작업에 지정된 Azure 컨테이너 레지스트리에 성공적으로 빌드된 이미지를 푸시합니다.

현재 ACR Build에서 지원하는 빌드 작업 트리거는 다음과 같습니다.

* Git 리포지토리에 커밋
* 기본 이미지 업데이트

## <a name="create-a-build-task"></a>빌드 작업 만들기

이 섹션에서는 먼저 ACR Build에서 사용할 GitHub PAT(개인용 액세스 토큰)를 만듭니다. 그런 다음, 코드가 리포지토리의 포크에 커밋될 때 빌드를 트리거하는 빌드 작업을 만듭니다.

### <a name="create-a-github-personal-access-token"></a>GitHub 개인용 액세스 토큰 만들기

Git 리포지토리에 커밋할 때 빌드를 트리거하려면 ACR Build에서 리포지토리에 액세스하기 위해 PAT(개인용 액세스 토큰)가 필요합니다. 다음 단계에 따라 GitHub에 PAT를 생성합니다.

1. https://github.com/settings/tokens/new에 있는 GitHub의 PAT 만들기 페이지로 이동합니다.
1. 토큰에 대한 짧은 **설명**을 입력합니다(예: "ACR Build 작업 데모").
1. **repo**(리포지토리) 아래에서 **repo:status** 및 **public_repo**를 사용하도록 설정합니다.

   ![GitHub에 있는 개인용 액세스 토큰 생성 페이지의 스크린샷][build-task-01-new-token]

1. **토큰 생성** 단추를 선택합니다(암호를 확인하라는 메시지가 표시될 수 있음).
1. 생성된 토큰을 복사하여 **보안 위치**에 저장합니다. 이 토큰은 다음 섹션에서 빌드 작업을 정의할 때 사용합니다.

   ![GitHub에 생성된 개인용 액세스 토큰의 스크린샷][build-task-02-generated-token]

### <a name="create-the-build-task"></a>빌드 작업 만들기

이제 ACR Build에서 커밋 상태를 읽고 리포지토리에 웹후크를 만들 수 있도록 설정하는 데 필요한 단계를 완료했으므로 리포지토리에 커밋할 때 컨테이너 이미지 빌드를 트리거하는 빌드 작업을 만들 수 있습니다.

먼저 이러한 셸 환경 변수를 환경에 적합한 값으로 채웁니다. 이는 반드시 필요한 것은 아니지만 이 자습서에서 여러 줄로 된 Azure CLI 명령을 조금 더 쉽게 실행할 수 있도록 합니다. 이러한 값을 채우지 않으면 명령 예제에서 표시되는 모든 위치에서 각 값을 수동으로 바꾸어야 합니다.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

이제 [az acr build-task create][az-acr-build-task-create] 명령을 실행하여 빌드 작업을 만듭니다.

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

이 빌드 작업은 모든 시간 코드가 `--context`로 지정된 리포지토리의 *마스터* 분기에 커밋되도록 지정하고, ACR Build는 해당 분기의 코드에서 컨테이너 이미지를 빌드합니다. `--image` 인수는 이미지 태그의 버전 부분에 대해 `{{.Build.ID}}`의 매개 변수화된 값을 지정하여 빌드된 이미지가 특정 빌드와 상호 연결되고 태그가 고유하게 지정되도록 합니다.

성공적인 [az acr build-task create][az-acr-build-task-create] 명령의 출력은 다음과 비슷합니다.

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-04-18T23:14:45.905395+00:00",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "Github"
  },
  "status": "enabled",
  "tags": null,
  "timeout": null,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}

```

## <a name="test-the-build-task"></a>빌드 작업 테스트

이제 빌드를 정의하는 빌드 작업이 있습니다. 빌드 정의를 테스트하려면 [az acr build-task run][az-acr-build-task-run] 명령을 실행하여 빌드를 수동으로 트리거합니다.

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

기본적으로 `az acr build-task run` 명령이 실행되면 로그 출력이 콘솔에 스트림됩니다. 이 출력에서는 **eastus2** 빌드를 큐에 넣고 빌드했음을 보여 줍니다.

```console
$ az acr build-task run --registry mycontainerregistry --name buildhelloworld
Queued a build with build-id: eastus2.
Starting to stream the logs...
Cloning into '/root/acr-builder/src'...
time="2018-04-19T00:06:20Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
ffef1347389a008c9a8bfdf8c6a0ed78b0479894
time="2018-04-19T00:06:20Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-04-19T00:06:20Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:eastus2 ."
Sending build context to Docker daemon  182.8kB
Step 1/5 : FROM node:9-alpine
9: Pulling from library/node
Digest: sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90
Status: Image is up to date for node:9-alpine
 ---> aa3e171e4e95
Step 2/5 : COPY . /src
 ---> e1c04dc2993b

[...]

6e5e20cbf4a7: Layer already exists
b69680cb4898: Pushed
b54af9b858b7: Pushed
eastus2: digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6 size: 2423
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloworld:eastus2"
"["mycontainerregistry.azurecr.io/helloworld@sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6"]"
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloworld
    tag: eastus2
    digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 6944c6bd0602f96e5fecf56ff8d66e2d268223e3

Build complete
Build ID: eastus2 was successful after 39.789138274s
```

## <a name="view-build-status"></a>빌드 상태 보기

때로는 수동으로 트리거하지 않은 진행 중인 빌드의 상태를 확인하는 것이 유용할 수 있습니다. 예를 들어 소스 코드 커밋으로 트리거되는 빌드 문제를 해결하는 동안이 이에 해당합니다. 이 섹션에서는 수동 빌드를 트리거하지만, 빌드 로그를 콘솔로 스트림하는 기본 동작은 표시하지 않습니다. 그런 다음, `az acr build-task logs` 명령을 사용하여 진행 중인 빌드를 모니터링합니다.

먼저 앞에서 수행한 대로 빌드를 수동으로 트리거하지만, `--no-logs` 인수를 지정하여 콘솔에 로깅이 표시되지 않도록 합니다.

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

다음으로, `az build-task logs` 명령을 사용하여 현재 실행 중인 빌드의 로그를 봅니다.

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

현재 실행 중인 빌드에 대한 로그는 콘솔로 스트림되며, 다음 출력과 비슷하게 표시됩니다(여기서는 잘린 상태로 표시됨).

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus3

[...]

Build complete
Build ID: eastus3 was successful after 30.076988169s
```

## <a name="trigger-a-build-with-a-commit"></a>커밋을 사용하여 빌드 트리거

이제 빌드 작업을 수동으로 실행하여 테스트했으므로 소스 코드가 변경되면 해당 빌드 작업을 자동으로 트리거합니다.

먼저 [리포지토리][sample-repo]의 로컬 복제본이 포함된 디렉터리에 있는지 확인합니다.

```azurecli-interactive
cd acr-build-helloworld-node
```

다음으로, 다음 명령을 실행하여 새 파일을 만들고, 커밋하고, GitHub에 있는 리포지토리의 포크에 푸시합니다.

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

`git push` 명령을 실행할 때 GitHub 자격 증명을 제공하라는 메시지가 표시될 수 있습니다. GitHub 사용자 이름을 제공하고, 이전에 암호에 대해 만든 PAT(개인용 액세스 토큰)를 입력합니다.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

커밋이 리포지토리에 푸시되면 ACR Build에서 만든 웹후크가 실행되고 Azure Container Registry에서 빌드를 시작합니다. 현재 실행 중인 빌드에 대한 빌드 로그를 표시하여 빌드 진행 상황을 확인하고 모니터링합니다.

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

출력은 다음과 비슷하며, 현재 실행 중이거나 마지막으로 실행된 빌드를 보여 줍니다.

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus4

[...]

Build complete
Build ID: eastus4 was successful after 28.9587031s
```

## <a name="list-builds"></a>빌드 나열

ACR Build에서 레지스트리에 대해 완료한 빌드 목록을 보려면 [az acr build-task list-builds][az-acr-build-task-list-builds] 명령을 실행합니다.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

이 명령의 출력은 다음과 비슷하게 표시됩니다. ACR Build에서 실행한 빌드가 표시되고, 가장 최근 빌드에 대한 TRIGGER 열에 "Git 커밋"이 표시됩니다.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
eastus4     buildhelloworld  Linux       Succeeded  Git Commit  2018-04-20T22:50:27Z  00:00:35
eastus3     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:47:19Z  00:00:30
eastus2     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:46:14Z  00:00:55
eastus1                                  Succeeded  Manual      2018-04-20T22:38:22Z  00:00:55
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 소스 코드를 Git 리포지토리에 커밋할 때 빌드 작업을 사용하여 Azure에서 컨테이너 이미지 빌드를 자동으로 트리거하는 방법을 알아보았습니다. 컨테이너 이미지의 기본 이미지가 업데이트될 때 빌드를 트리거하는 빌드 작업을 만드는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [기본 이미지 업데이트 시 빌드 자동화](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
