---
title: Azure Portal의 Azure Container Registry 리포지토리
description: Azure Portal에서 Azure Container Registry 리포지토리를 보는 방법
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 171593483fc94c1c67013ab520b0085ca98f3a82
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Azure Portal의 컨테이너 레지스트리 리포지토리 보기

Azure Container Registry를 통해 리포지토리에 Docker 컨테이너 이미지를 저장할 수 있습니다. 리포지토리에 이미지를 저장하면 격리된 환경에 이미지 그룹(또는 이미지 버전)을 저장할 수 있습니다. 이미지를 레지스트리에 푸시할 때 이러한 리포지토리를 지정하고, Azure Portal에서 해당 콘텐츠를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* **컨테이너 레지스트리**: Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* **Docker CLI**: 로컬 컴퓨터에 Docker 명령줄 인터페이스를 제공하는 [Docker][docker-install]를 설치합니다.
* **컨테이너 이미지**: 이미지를 컨테이너 레지스트리를 밀어 넣습니다. 이미지 밀어넣기 및 끌어오기 방법에 대한 지침은 [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)를 참조하세요.

## <a name="view-repositories-in-azure-portal"></a>Azure Portal에서 리포지토리 보기

Azure Portal에서 이미지 태그뿐만 아니라 이미지를 호스트하는 리포지토리 목록도 볼 수 있습니다.

[이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)의 단계를 따른 경우(나중에 이미지를 삭제하지 않음) 컨테이너 레지스트리에 Nginx 이미지가 있을 것입니다. 해당 문서의 지침에서는 `/samples/nginx`에서 네임스페이스 “samples”를 태그로 이미지에 지정하도록 지정했습니다. 해당 문서에 지정된 [docker push][docker-push] 명령이 아래에 다시 제공되니 참조하기 바랍니다.

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Azure Container Registry는 이러한 다단계 리포지토리 네임스페이스를 지원하므로 특정 앱 또는 앱 컬렉션과 관련된 이미지 모음을 다른 개발 또는 운영 팀으로 확장할 수 있습니다. 컨테이너 레지스트리의 리포지토리에 대한 자세한 내용은 [Azure의 개인 Docker 컨테이너 레지스트리](container-registry-intro.md)를 참조하세요.

리포지토리를 보려면

1. [Azure Portal][portal]에 로그인합니다.
1. Nginx 이미지를 밀어 넣은 **Azure Container Registry**를 선택합니다.
1. **리포지토리**를 선택하여 레지스트리의 이미지를 포함하는 리포지토리 목록을 확인합니다.
1. 리포지토리를 선택하여 해당 리포지토리 내의 이미지 태그를 확인합니다.

예를 들어, [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)에 지시된 대로 Nginx 이미지를 밀어 넣은 경우 다음과 유사한 결과가 표시됩니다.

![포털에서 리포지토리](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>다음 단계

이제 포털에서 리포지토리를 보고 사용하는 기본 방법을 확인했으므로 [ACS(Azure Kubernetes Service)](../aks/tutorial-kubernetes-prepare-app.md) 클러스터에서 Azure Container Registry를 사용해보세요.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
