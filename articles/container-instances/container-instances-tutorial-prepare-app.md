---
title: 'Azure Container Instances 자습서 - 앱 준비 '
description: Azure Container Instances 자습서 1/3부 - Azure Container Instances에 배포할 앱 준비
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 79041123196559c5759789638228ea0dd21f2762
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-container-for-deployment-to-azure-container-instances"></a>자습서: Azure Container Instances에 배포를 위한 컨테이너 만들기

Azure Container Instances를 통해 어떠한 가상 머신을 프로비전하지 않고 또 더 높은 수준의 서비스를 채택하지 않고도 Azure로 Docker 컨테이너를 배포할 수 있습니다. 이 자습서에서는 Azure Container Instances를 사용하여 실행할 수 있는 컨테이너 이미지에 작은 Node.js 웹 응용 프로그램을 패키지합니다.

시리즈의 1부에 해당하는 본 문서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 원본 코드 복제
> * 응용 프로그램 원본에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 이미지 테스트

자습서 2부 및 3부에서는 이미지를 Azure Container Registry에 업로드한 후 Azure Container Instances에 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>응용 프로그램 코드 가져오기

이 자습서의 응용 프로그램 예제는 [Node.js][nodejs]에서 빌드된 간단한 웹앱입니다. 이 응용 프로그램은 정적 HTML 페이지를 제공하며, 다음 스크린샷과 비슷한 모습입니다.

![브라우저에 표시된 자습서 앱][aci-tutorial-app]

Git을 사용하여 응용 프로그램 예제의 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

GitHub에서 직접 [ZIP 아카이브를 다운로드][aci-helloworld-zip]할 수도 있습니다.

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

응용 프로그램 예제의 Dockerfile은 컨테이너가 어떻게 빌드되었는지 보여줍니다. 컨테이너에 사용하기에 적합한 소규모 배포인 [Alpine Linux][alpine-linux] 기반의 [공식 Node.js 이미지][docker-hub-nodeimage]로 시작합니다. 그런 다음, 응용 프로그램 파일을 컨테이너에 복사하고 노드 패키지 관리자를 사용하여 종속성을 설치한 후 마지막으로 응용 프로그램을 시작합니다.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

[docker build][docker-build] 명령을 사용하여 컨테이너 이미지를 만들고 *aci-tutorial-app*으로 태깅합니다.

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

[docker build][docker-build] 명령의 출력은 다음과 비슷합니다(가독성을 위해 잘림).

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

빌드된 이미지를 보려면 [docker images][docker-images] 명령을 사용합니다.

```bash
docker images
```

새로 빌드된 이미지가 목록에 표시됩니다.

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

컨테이너를 Azure Container Instances에 배포하기 전에 [docker run][docker-run] 명령을 사용하여 로컬로 실행하여 정상적으로 작동하는지 확인합니다. `-d` 스위치를 통해 컨테이너를 백그라운드로 실행할 수 있으며 `-p`를 통해 컴퓨터의 임의 포트를 컨테이너의 포트 80에 매핑할 수 있습니다.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

`docker run` 명령의 출력은 명령이 성공한 경우 실행 중인 컨테이너의 ID를 표시합니다.

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

이제 브라우저에서 http://localhost:8080으로 이동하여 컨테이너가 실행 중인지 확인합니다. 다음과 비슷한 웹 페이지가 표시됩니다.

![브라우저에서 앱을 로컬로 실행][aci-tutorial-app-local]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances에 배포할 수 있는 컨테이너 이미지를 만들고, 로컬에서 실행되는 것을 확인했습니다. 지금까지 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제
> * 응용 프로그램 원본에서 컨테이너 이미지 만들기
> * 컨테이너를 로컬로 테스트

이 시리즈의 다음 자습서로 이동하여 Azure Container Registry에 컨테이너 이미지를 저장하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry에 이미지 푸시](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
