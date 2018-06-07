---
title: 자습서 - Azure Container Registry Build를 사용하여 클라우드에서 컨테이너 이미지 빌드
description: 이 자습서에서는 ACR Build(Azure Container Registry Build)를 사용하여 Azure에서 Docker 컨테이너 이미지를 빌드하는 방법을 알아보고, Azure Container Instances에 배포합니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>자습서: Azure Container Registry Build를 사용하여 클라우드에서 컨테이너 이미지 빌드

**ACR Build**는 Azure에서 간편하고 효율적인 Docker 컨테이너 이미지 빌드를 제공하는 Azure Container Registry 내의 기능 모음입니다. 이 문서에서는 ACR Build의 *빠른 빌드* 기능을 사용하는 방법에 대해 알아봅니다. 빠른 빌드는 개발 "내부 루프"를 클라우드로 확장하여 빌드 성공 유효성 검사 및 성공적으로 빌드된 이미지를 컨테이너 레지스트리에 자동으로 푸시하는 기능을 제공합니다. 사용자의 이미지는 기본적으로 레지스트리에 가까운 클라우드에 빌드되므로 더 빠르게 배포할 수 있습니다.

모든 Dockerfile 전문 지식은 ACR Build로 직접 전송할 수 있습니다. Dockerfile을 변경하여 ACR Build를 통해 클라우드에 빌드할 필요 없이 명령만 실행하면 됩니다.

이 자습서는 시리즈의 1부입니다.

> [!div class="checklist"]
> * 샘플 응용 프로그램 소스 코드 가져오기
> * Azure에서 컨테이너 이미지 빌드
> * Azure Container Instances에 컨테이너 배포

후속 자습서에서 코드 커밋 및 기본 이미지 업데이트 시 자동화된 컨테이너 이미지 빌드에 ACR Build의 빌드 작업을 사용하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> ACR Build는 현재 미리 보기 상태이며, **미국 동부**(eastus) 및 **유럽 서부**(westeurope) 지역의 Azure 컨테이너 레지스트리에서만 지원됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 사용하려면 Azure CLI 버전 **2.0.32** 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 2.0 설치][azure-cli]를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="github-account"></a>GitHub 계정

아직 계정이 없는 경우 https://github.com에서 계정을 만듭니다. 이 자습서 시리즈에서는 GitHub 리포지토리를 사용하여 ACR Build에서 자동화된 이미지 빌드를 보여 줍니다.

### <a name="fork-sample-repository"></a>샘플 리포지토리 포크

다음으로, GitHub UI를 사용하여 샘플 리포지토리를 GitHub 계정에 포크합니다. 이 자습서에서는 리포지토리의 원본에서 컨테이너 이미지를 빌드하고, 다음 자습서에서는 커밋을 리포지토리의 포크에 푸시하여 자동화된 빌드를 시작합니다.

https://github.com/Azure-Samples/acr-build-helloworld-node 리포지토리를 포크합니다.

![GitHub에 있는 포크 단추(강조 표시됨)의 스크린샷][quick-build-01-fork]

### <a name="clone-your-fork"></a>포크 복제

리포지토리가 포크되면 포크를 복제하고 로컬 복제가 포함된 디렉터리를 입력합니다.

`git`을 사용하여 리포지토리를 복제하고, **\<your-github-username\>** 을 GitHub 사용자 이름으로 바꿉니다.

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

소스 코드가 포함된 디렉터리를 입력합니다.

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash 셸

이 자습서 시리즈의 명령은 Bash 셸 형식으로 지정되어 있습니다. PowerShell, 명령 프롬프트 또는 다른 셸을 사용하려면 줄 연속 및 환경 변수 형식을 적절하게 조정해야 할 수도 있습니다.

## <a name="build-in-azure-with-acr-build"></a>Azure에서 ACR Build를 사용하여 빌드

이제 소스 코드를 컴퓨터로 가져왔으므로 다음 단계에 따라 컨테이너 레지스트리를 만들고 ACR Build를 사용하여 컨테이너 이미지를 빌드합니다.

이 시리즈의 자습서에서는 샘플 명령을 더 쉽게 실행하기 위해 셸 환경 변수를 사용합니다. 다음 명령을 실행하여 `ACR_NAME` 변수를 설정합니다. **\<registry-name\>** 을 새 컨테이너 레지스트리에 대한 고유한 이름으로 바꿉니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 자습서에서 만드는 다른 리소스는 이 이름을 기반으로 하므로 이 첫 번째 변수만 수정해야 합니다.

```azurecli-interactive
ACR_NAME=<registry-name>
```

컨테이너 레지스트리 환경 변수가 채워지면 이제 값을 편집하지 않고도 자습서의 나머지 명령을 복사하여 붙여넣을 수 있습니다. 다음 명령을 실행하여 리소스 그룹 및 컨테이너 레지스트리를 만듭니다.

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

이제 레지스트리가 있으므로 ACR Build를 사용하여 코드 샘플에서 컨테이너 이미지를 빌드합니다. [az acr build][az-acr-build] 명령을 실행하여 *빠른 빌드*를 수행합니다.

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

[az acr build][az-acr-build] 명령의 출력은 다음과 비슷합니다. Azure에 대한 소스 코드("컨텍스트") 업로드 및 ACR Build가 클라우드에서 실행하는 `docker build` 작업의 세부 정보를 확인할 수 있습니다. ACR Build에서 `docker build`를 사용하여 이미지를 빌드하므로 ACR Build를 즉시 사용하기 위해 Dockerfile을 변경할 필요가 없습니다.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

출력이 거의 완료될 때 ACR Build에서 이미지에 대해 검색된 종속성을 표시합니다. 이렇게 하면 ACR Build에서 기본 이미지가 OS 또는 프레임워크 패치로 업데이트되는 경우와 같이 기본 이미지 업데이트 시 이미지 빌드를 자동화할 수 있습니다. 이 자습서 시리즈의 뒷부분에서 기본 이미지 업데이트에 대한 ACR Build 지원에 대해 알아봅니다.

## <a name="deploy-to-azure-container-instances"></a>Azure Container Instances에 배포

ACR Build는 기본적으로 성공적으로 빌드된 이미지를 레지스트리에 자동으로 푸시하여 레지스트리에서 즉시 배포할 수 있도록 합니다.

이 섹션에서는 Azure Key Vault와 서비스 주체를 만든 다음, 서비스 주체의 자격 증명을 사용하여 ACI(Azure Container Instance)에 컨테이너를 배포합니다.

### <a name="configure-registry-authentication"></a>레지스트리 인증 구성

모든 프로덕션 시나리오에서는 [서비스 주체][service-principal-auth]를 사용하여 Azure 컨테이너 레지스트리에 액세스해야 합니다. 서비스 주체를 통해 컨테이너 이미지에 대해 역할 기반 액세스 제어를 제공할 수 있습니다. 예를 들어, 레지스트리에 대해 끌어오기 전용 액세스 권한을 가지는 서비스 주체를 구성할 수 있습니다.

#### <a name="create-key-vault"></a>주요 자격 증명 모음 만들기

[Azure Key Vault](/azure/key-vault/)에 자격 증명 모음이 아직 없는 경우 Azure CLI에서 다음 명령을 사용하여 만듭니다.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>서비스 주체 만들기 및 자격 증명 저장

이제 서비스 주체를 만들고 해당 자격 증명을 주요 자격 증명 모음에 저장해야 합니다.

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령을 사용하여 서비스 주체를 만들고, [az keyvault secret set][az-keyvault-secret-set]를 사용하여 서비스 주체의 **암호**를 자격 증명 모음에 저장합니다.

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

이전 명령의 `--role` 인수는 *읽기 권한자* 역할을 사용해서 서비스 주체를 구성하고, 레지스트리에 대해 끌어오기 전용 액세스 권한을 부여합니다. 밀어넣기 및 끌어오기 액세스 권한을 부여하려면 `--role` 인수를 *contributor*로 변경합니다.

다음으로, 인증을 위해 Azure Container Registry에 전달하는 **username**인 서비스 주체의 *appId*를 자격 증명 모음에 저장합니다.

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure Key Vault을 만들고 다음 두 암호를 저장했습니다.

* `$ACR_NAME-pull-usr`: 서비스 주체 ID로, 컨테이너 레지스트리 **username**으로 사용됩니다.
* `$ACR_NAME-pull-pwd`: 서비스 주체 암호로, 컨테이너 레지스트리 **password**로 사용됩니다.

이제 사용자나 응용 프로그램 및 서비스가 레지스트리에서 이미지를 끌어올 때 이러한 암호를 이름으로 참조할 수 있습니다.

### <a name="deploy-container-with-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

서비스 주체 자격 증명이 Azure Key Vault 비밀로 저장되므로, 응용 프로그램 및 서비스에서 해당 자격 증명을 사용하여 개인 레지스트리에 액세스할 수 있습니다.

다음 [az container create][az-container-create] 명령을 실행하여 컨테이너 인스턴스를 배포합니다. 이 명령은 Azure Key Vault에 저장된 서비스 주체의 자격 증명을 사용하여 컨테이너 레지스트리를 인증합니다.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

`--dns-name-label` 값은 Azure 내에서 고유해야 하므로, 이전 명령은 컨테이너 레지스트리의 이름을 컨테이너의 DNS 이름 레이블에 추가합니다. 명령의 출력에는 컨테이너의 FQDN(정규화된 도메인 이름)이 표시됩니다. 예를 들면 다음과 같습니다.

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

다음 섹션에서 사용하기 위해 컨테이너의 FQDN을 적어 둡니다.

### <a name="verify-deployment"></a>배포 확인

컨테이너의 시작 프로세스를 보려면 [az container attach][az-container-attach] 명령을 사용합니다.

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

`az container attach` 출력은 먼저 이미지를 가져와서 시작할 때 컨테이너의 상태를 표시한 다음, 로컬 콘솔의 STDOUT 및 STDERR을 컨테이너의 STDOUT 및 STDERR에 바인딩합니다.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

`Server running at http://localhost:80`이 표시되면 브라우저에서 컨테이너의 FQDN으로 이동하여 실행 중인 응용 프로그램을 확인합니다.

![브라우저에서 렌더링된 샘플 응용 프로그램의 스크린샷][quick-build-02-browser]

콘솔에서 콘솔을 분리하려면 `Control+C`를 누릅니다.

## <a name="clean-up-resources"></a>리소스 정리

[az container delete][az-container-delete] 명령을 사용하여 컨테이너 인스턴스를 중지합니다.

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

컨테이너 레지스트리, 키 자격 증명 모음 및 서비스 주체를 포함하여 이 자습서에서 만든 *모든* 리소스를 제거하려면 다음 명령을 실행합니다. 그러나 이러한 리소스는 시리즈의 [다음 자습서](container-registry-tutorial-build-task.md)에서 사용되므로 다음 자습서로 바로 이동하는 경우 유지해야 할 수도 있습니다.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>다음 단계

이제 빠른 빌드를 통해 내부 루프를 테스트했으므로 소스 코드를 Git 리포지토리에 커밋할 때 컨테이너 이미지 빌드를 트리거하도록 **빌드 작업**을 구성합니다.

> [!div class="nextstepaction"]
> [빌드 작업을 사용하여 자동 빌드 트리거](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
