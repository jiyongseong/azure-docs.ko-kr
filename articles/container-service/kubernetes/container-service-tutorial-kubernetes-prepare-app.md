---
title: Azure Container Service 자습서 - 앱 준비
description: Azure Container Service 자습서 - 앱 준비
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2fe160652bf8df289d590722ef4024f0b3dd397c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Azure Container Service에 사용할 컨테이너 이미지 만들기

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

7개 중 1단계인 이 자습서에서는 Kubernetes에서 사용할 수 있도록 다중 컨테이너 응용 프로그램을 준비하는 과정입니다. 완료되는 단계는 다음과 같습니다.  

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제  
> * 응용 프로그램 원본에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 응용 프로그램 테스트

완료되면 다음 응용 프로그램을 로컬 개발 환경에서 액세스할 수 있습니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/azure-vote.png)

이후 자습서에서는 컨테이너 이미지를 Azure Container Registry에 업로드한 다음 Azure에서 호스트되는 Kubernetes 클러스터에서 실행합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서에서는 컨테이너, 컨테이너 이미지 및 기본 Docker 명령과 같은 핵심 Docker 개념에 대한 기본적인 지식이 있다고 가정합니다. 필요한 경우 컨테이너 기본 사항에 대한 입문서는 [Get started with Docker(Docker 시작)]( https://docs.docker.com/get-started/)를 참조하세요. 

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

Azure Cloud Shell에는 이 자습서의 모든 단계를 완료하는 데 필요한 Docker 구성 요소가 포함되어 있지 않습니다. 따라서 전체 Docker 개발 환경을 사용하는 것이 좋습니다.

## <a name="get-application-code"></a>응용 프로그램 코드 가져오기

이 자습서에서 사용되는 응용 프로그램 예제는 기본 투표 앱입니다. 응용 프로그램은 프런트 엔드 웹 구성 요소 및 백 엔드 Redis 인스턴스로 구성됩니다. 웹 구성 요소는 사용자 지정 컨테이너 이미지에 패키지됩니다. Redis 인스턴스는 Docker 허브에서 수정되지 않은 이미지를 사용합니다.  

Git을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

복제된 디렉터리에서 작업할 수 있도록 디렉터리를 변경합니다.

```
cd azure-voting-app-redis
```

해당 디렉터리에는 응용 프로그램 소스 코드, 미리 만든 Docker Compose 파일 및 Kubernetes 매니페스트 파일이 있습니다. 이러한 파일은 자습서 집합 전체에서 사용됩니다. 

## <a name="create-container-images"></a>컨테이너 이미지 만들기

[Docker Compose](https://docs.docker.com/compose/)는 컨테이너 이미지 빌드 및 다중 컨테이너 응용 프로그램 배포를 자동화하는 데 사용할 수 있습니다.

`docker-compose.yml` 파일을 실행하여 컨테이너 이미지를 만들고, Redis 이미지를 다운로드하고, 응용 프로그램을 시작합니다.

```bash
docker-compose up -d
```

완료되면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 만든 이미지를 확인합니다.

```bash
docker images
```

세 개의 이미지가 다운로드되거나 생성되었는지 확인합니다. `azure-vote-front` 이미지는 응용 프로그램을 포함하며 `nginx-flask` 이미지를 기준으로 사용합니다. `redis` 이미지는 Redis 인스턴스를 시작하는 데 사용됩니다.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[docker ps](https://docs.docker.com/engine/reference/commandline/ps/) 명령을 실행하여 실행 중인 컨테이너를 확인합니다.

```bash
docker ps
```

출력

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>로컬에서 응용 프로그램 테스트

http://localhost:8080으로 이동하여 실행 중인 응용 프로그램을 확인합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>리소스 정리

응용 프로그램 기능의 유효성을 검사했으므로 실행 중인 컨테이너를 중지하고 제거할 수 있습니다. 컨테이너 이미지를 삭제하지 마세요. 다음 자습서에서 `azure-vote-front` 이미지를 Azure Container Registry 인스턴스에 업로드합니다.

다음을 실행하여 실행 중인 컨테이너를 중지합니다.

```bash
docker-compose stop
```

다음 명령을 사용하여 중지된 컨테이너와 리소스를 삭제합니다.

```bash
docker-compose down
```

완료되면 Azure Vote 응용 프로그램을 구성하는 컨테이너 이미지가 있는 상태가 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램을 테스트하고 응용 프로그램에 대한 컨테이너 이미지를 만들었습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제  
> * 응용 프로그램 원본에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 응용 프로그램 테스트

다음 자습서로 이동하여 Azure Container Registry에 컨테이너 이미지 저장에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Container Registry에 이미지 밀어넣기](./container-service-tutorial-kubernetes-prepare-acr.md)
