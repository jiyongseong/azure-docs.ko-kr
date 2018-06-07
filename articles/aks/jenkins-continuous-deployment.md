---
title: Azure Kubernetes Service에서 Kubernetes와 Jenkins 연속 배포
description: Jenkins로 연속 배포 프로세스를 자동화하여 Azure Kubernetes Service의 Kubernetes에서 컨테이너화된 앱을 배포 및 업그레이드하는 방법
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 376d3b916c4e01ea6111e6c1db63e976dd1ea320
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Jenkins 및 Azure Kubernetes Service를 사용한 연속 배포

이 문서에서는 Jenkins와 AKS(Azure Kubernetes Service) 클러스터 간의 기본 연속 배포 워크플로를 설정하는 방법을 보여줍니다.

예제 워크플로에는 다음 단계가 포함됩니다.

> [!div class="checklist"]
> * Kubernetes 클러스터에 Azure 투표 응용 프로그램을 배포합니다.
> * Azure 투표 응용 프로그램 코드를 업데이트하고 GitHub 리포지토리에 푸시하여 연속 배포 프로세스를 시작합니다.
> * Jenkins가 리포지토리를 복제하고 업데이트된 코드를 사용하여 새 컨테이너 이미지를 빌드합니다.
> * 이 이미지는 ACR(Azure Container Registry)에 푸시됩니다.
> * AKS 클러스터에서 실행 중인 응용 프로그램이 새 컨테이너 이미지를 사용하여 업데이트됩니다.

## <a name="prerequisites"></a>필수 조건

이 아티클의 단계를 완료하려면 다음 항목이 필요합니다.

- Kubernetes, Git, CI/CD 및 ACR(Azure Container Registry)에 대한 기본적인 이해.
- [AKS(Azure Kubernetes Service) 클러스터][aks-quickstart] 및 개발 시스템에 [구성된 AKS 자격 증명][aks-credentials]
- [ACR(Azure Container Registry) 레지스트리][acr-quickstart], ACR 로그인 서버 이름, 푸시 및 풀 액세스 권한이 있는 [ACR 자격 증명][acr-authentication].
- 개발 시스템에 설치된 Azure CLI.
- 개발 시스템에 설치된 Docker.
- GitHub 계정, [GitHub 개인용 액세스 토큰][git-access-token], 개발 시스템에 설치된 Git 클라이언트.

## <a name="prepare-application"></a>응용 프로그램 준비

이 문서에서 사용된 Azure 투표 응용 프로그램은 하나 이상의 Pod에서 호스트되는 웹 인터페이스와 임시 데이터 저장소를 위한 Redis를 호스트하는 두 번째 Pod를 포함합니다.

Jenkins/AKS 통합을 빌드하기 전에 Azure 투표 응용 프로그램을 준비하고 AKS 클러스터에 배포하세요. 이를 이 응용 프로그램의 한 버전으로 생각하세요.

다음 GitHub 리포지토리를 포크합니다.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

포크가 생성되면 개발 시스템에 복제합니다. 이 리포지토리를 복제할 때는 포크의 URL을 사용 중인지 확인합니다.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

복제된 디렉터리에서 작업할 수 있도록 디렉터리를 변경합니다.

```bash
cd azure-voting-app-redis
```

`docker-compose.yaml` 파일을 실행하여 `azure-vote-front` 컨테이너 이미지를 만들고 응용 프로그램을 시작합니다.

```bash
docker-compose up -d
```

완료되면 [docker images][docker-images] 명령을 사용하여 만든 이미지를 확인합니다.

세 개의 이미지가 다운로드되거나 생성되었는지 확인합니다. `azure-vote-front` 이미지는 응용 프로그램을 포함하며 `nginx-flask` 이미지를 기준으로 사용합니다. `redis` 이미지는 Redis 인스턴스를 시작하는 데 사용됩니다.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[az acr list][az-acr-list] 명령을 사용하여 ACR 로그인 서버를 가져옵니다. ACR 레지스트리를 호스팅하는 리소스 그룹으로 리소스 그룹 이름을 업데이트하도록 합니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] 명령을 사용하여 이미지에 로그인 서버 이름과 서버 버전 번호 `v1`을 태그로 지정합니다.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

ACR 로그인 서버 이름으로 ACR 로그인 서버 값을 업데이트하고 `azure-vote-front` 이미지를 레지스트리로 푸시합니다.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Kubernetes에 응용 프로그램 배포

Kubernetes 매니페스트 파일은 Azure 투표 리포지토리의 루트에 있으며, Kubernetes 클러스터에 응용 프로그램을 배포하는 데 사용할 수 있습니다.

먼저 ACR 레지스트리의 위치를 사용하여 **azure-vote-all-in-one-redis.yaml** 매니페스트 파일을 업데이트합니다. 텍스트 편집기로 파일을 열고 `microsoft`를 ACR 로그인 서버 이름으로 대체합니다. 이 값은 매니페스트 파일의 줄 **47**에 있습니다.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

다음으로 응용 프로그램을 실행하려면 [kubectl apply][kubectl-apply] 명령을 사용합니다. 이 명령은 매니페스트 파일을 구문 분석하고 정의된 Kubernetes 개체를 만듭니다.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

인터넷에 응용 프로그램을 노출하는 [Kubernetes 서비스][kubernetes-service]가 생성됩니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다.

진행 상황을 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```bash
kubectl get service azure-vote-front --watch
```

처음에는 *azure-vote-front* 서비스에 대한 *EXTERNAL-IP*가 *보류 중*으로 표시됩니다.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

*EXTERNAL-IP* 주소가 *보류 중*에서 *IP 주소*로 변경되면 `control+c`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

응용 프로그램을 보려면 외부 IP 주소로 이동합니다.

![Azure의 Kubernetes 클러스터 이미지](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>VM에 Jenkins 배포

가상 머신을 배포하고, 네트워크 액세스를 구성하고, Jenkins의 기본 설치를 완료하기 위한 스크립트가 미리 만들어져 있습니다. 또한 이 스크립트는 개발 시스템에서 Jenkins 시스템으로 Kubernetes 구성 파일을 복사합니다. 이 파일은 Jenkins와 AKS 클러스터 간 인증에 사용됩니다.

다음 명령을 실행하여 스크립트를 다운로드하고 실행합니다. 아래 URL을 사용하여 스크립트의 내용을 살펴볼 수도 있습니다.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

스크립트가 완료되면 Jenkins 서버의 주소와 Jenkins 잠금을 해제하는 키가 출력됩니다. URL로 이동하고, 키를 입력하고, 화면의 메시지를 따라 Jenkins 구성을 완료합니다.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins 환경 변수

Jenkins 환경 변수는 ACR(Azure Container Registry) 로그인 서버 이름을 저장하는 데 사용됩니다. 이 변수는 Jenkins 연속 배포 작업 중에 참조됩니다.

Jenkins 관리자 포털에서 **Jenkins 관리** > **시스템 구성**을 클릭합니다.

**전역 속성** 아래에서 **환경 변수**를 선택하고 이름이 `ACR_LOGINSERVER`이고 ACR 로그인 서버 값을 가진 변수를 추가합니다.

![Jenkins 환경 변수](media/aks-jenkins/env-variables.png)

완료되면 Jenkins 구성 페이지에서 **저장**을 클릭합니다.

## <a name="jenkins-credentials"></a>Jenkins 자격 증명

이제 Jenkins 자격 증명 개체에 ACR 자격 증명을 저장합니다. 이러한 자격 증명은 Jenkins 빌드 작업 중에 참조됩니다.

다시 Jenkins 관리 포털에서 **자격 증명** > **Jenkins** > **전역 자격 증명(무제한)** > **자격 증명 추가**를 클릭합니다.

자격 증명 종류가 **사용자 이름 및 암호**인지 확인하고 다음 항목을 입력합니다.

- **사용자 이름** - ACR 레지스트리를 통한 인증에 사용할 서비스 주체의 ID입니다.
- **암호** - ACR 레지스트리를 통한 인증에 사용할 서비스 주체의 클라이언트 암호입니다.
- **ID** - `acr-credentials`과 같은 자격 증명 식별자입니다.

완료되면 자격 증명 양식이 이 이미지와 비슷하게 표시되어야 합니다.

![ACR 자격 증명](media/aks-jenkins/acr-credentials.png)

**확인**을 클릭하고 Jenkins 관리 포털로 돌아갑니다.

## <a name="create-jenkins-project"></a>Jenkins 프로젝트 만들기

Jenkins 관리 포털에서 **새 항목**을 클릭합니다.

프로젝트에 이름(예: `azure-vote`)을 지정하고, **프리스타일 프로젝트**를 선택하고 **확인**을 클릭합니다.

![Jenkins 프로젝트](media/aks-jenkins/jenkins-project.png)

**일반** 아래에서 **GitHub 프로젝트**를 선택하고 Azure 투표 GitHub 프로젝트의 포크에 대한 URL을 입력합니다.

![GitHub 프로젝트](media/aks-jenkins/github-project.png)

**소스 코드 관리** 아래에서 **Git**을 선택하고 Azure 투표 GitHub 리포지토리의 포크에 대한 URL을 입력합니다.

자격 증명의 경우 **추가** > **Jenkins**를 클릭합니다. **종류** 아래에서 **비밀 텍스트**를 선택하고 [GitHub 개인용 액세스 토큰][git-access-token]을 비밀로 입력합니다.

완료되면 **추가**를 선택합니다.

![GitHub 자격 증명](media/aks-jenkins/github-creds.png)

**트리거 빌드** 아래에서 **GITScm 폴링에 대한 GitHub 후크 트리거**를 선택합니다.

![Jenkins 빌드 트리거](media/aks-jenkins/build-triggers.png)

**빌드 환경** 아래에서 **비밀 텍스트 또는 파일 사용**을 선택합니다.

![Jenkins 빌드 환경](media/aks-jenkins/build-environment.png)

**바인딩** 아래에서 **추가** > **사용자 이름 및 암호(구분)** 를 선택합니다.

**사용자 이름 변수**에 `ACR_ID`를 입력하고, **암호 변수**에 `ACR_PASSWORD`를 입력합니다.

![Jenkins 바인딩](media/aks-jenkins/bindings.png)

**셸 실행** 형식의 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다. 이 스크립트는 새 컨테이너 이미지를 빌드하고 ACR 레지스트리에 푸시합니다.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

**셸 실행** 형식의 다른 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다. 이 스크립트는 Kubernetes 배포를 업데이트합니다.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

완료되면 **저장**을 클릭합니다.

## <a name="test-the-jenkins-build"></a>Jenkins 빌드 테스트

계속하기 전에 Jenkins 빌드를 테스트합니다. 이는 빌드 작업이 올바르게 구성되었고, 올바른 Kubernetes 인증 파일이 준비되어 있으며, 올바른 ACR 자격 증명이 제공되었는지 검증합니다.

프로젝트의 왼쪽 메뉴에 있는 **지금 빌드**를 클릭합니다.

![Jenkins 테스트 빌드](media/aks-jenkins/test-build.png)

이 프로세스 중에는 GitHub 리포지토리가 Jenkins 빌드 서버에 복제됩니다. 새 컨테이너 이미지가 빌드되어 ACR 레지스트리로 푸시됩니다. 마지막으로, AKS 클러스터에서 실행 중인 Azure 투표 응용 프로그램이 새 이미지를 사용하도록 업데이트됩니다. 응용 프로그램 코드에는 변경된 내용이 없으므로 응용 프로그램은 변경되지 않습니다.

프로세스가 완료되면 빌드 기록 아래에서 **빌드 #1**을 클릭하고 **콘솔 출력**을 선택하여 빌드 프로세스의 모든 출력을 봅니다. 마지막 줄에 빌드가 성공했다는 내용이 표시되어야 합니다.

## <a name="create-github-webhook"></a>GitHub 웹후크 만들기

다음으로, 모든 커밋에서 새 빌드가 트리거되도록 응용 프로그램 리포지토리를 Jenkins 빌드 서버로 연결합니다.

1. 포크된 GitHub 리포지토리로 이동합니다.
2. **설정**을 선택한 다음 왼쪽에 있는 **통합 및 서비스**를 선택합니다.
3. **서비스 추가**를 선택하고 필터 상자에 `Jenkins (GitHub plugin)`를 입력하고 플러그인을 선택합니다.
4. Jenkins 연결 URL에 `http://<publicIp:8080>/github-webhook/`을 입력합니다. 여기서 `publicIp`는 Jenkins 서버의 IP 주소입니다. 후행 슬래시(/)를 포함해야 합니다.
5. 서비스 추가를 선택합니다.

![GitHub 웹후크](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>CI/CD 프로세스 종단 간 테스트

개발 컴퓨터에서 코드 편집기를 사용하여 복제된 응용 프로그램을 엽니다.

**/azure-vote/azure-vote** 디렉터리 아래에 **config_file.cfg**라는 파일이 있습니다. 이 파일의 vote 값을 cats 및 dogs 이외의 값으로 업데이트합니다.

다음 예제에서는 업데이트된 **config_file.cfg** 파일을 보여줍니다.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

완료되면 파일을 저장하고, 변경 내용을 커밋하고, 이를 GitHub 리포지토리의 포크에 푸시합니다. 커밋이 완료되면 GitHub 웹후크가 새로운 Jenkins 빌드를 트리거하고, 컨테이너 이미지 및 AKS 배포가 업데이트됩니다. Jenkins 관리 콘솔에서 빌드 프로세스를 모니터링합니다.

빌드가 완료되면 응용 프로그램 엔드포인트로 다시 이동하여 변경 내용을 확인합니다.

![Azure 투표 업데이트됨](media/aks-jenkins/azure-vote-updated-safari.png)

이제 간단한 연속 배포 프로세스가 완료되었습니다. 이 예제에 표시된 단계와 구성을 사용하여 더 강력하고 프로덕션 준비가 된 연속 빌드 자동화를 빌드할 수 있습니다.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli