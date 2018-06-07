---
title: Azure Container Registry로 인증
description: Azure Active Directory 서비스 주체 직접 및 레지스트리 로그인을 비롯하여 Azure Container Registry에 대한 인증 옵션입니다.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8fcebae21d73db75e19bd1091faa8f389f0ba40
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>개인 Docker 컨테이너 레지스트리로 인증

Azure Container Registry로 인증하는 방법은 여러 가지가 있으며 각 방법을 하나 이상의 레지스트리 사용 시나리오에 적용할 수 있습니다.

[개별 로그인](#individual-login-with-azure-ad)을 통해 레지스트리에 직접 로그인할 수 있으며, 응용 프로그램 및 컨테이너 오케스트레이터는 Azure AD(Azure Active Directory) [서비스 주체](#service-principal)를 사용하여 무인 또는 "헤드리스" 인증을 수행할 수 있습니다.

Azure Container Registry는 인증되지 않은 Docker 작업 또는 익명 액세스를 지원하지 않습니다. 공용 이미지에는 [Docker 허브](https://docs.docker.com/docker-hub/)를 사용할 수 있습니다.

## <a name="individual-login-with-azure-ad"></a>Azure AD로 개별 로그인

개발용 워크스테이션에서 이미지 풀 및 푸시와 같이 직접 레지스트리를 사용하여 작업할 때 [Azure CLI](/cli/azure/install-azure-cli)에서 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) 명령을 사용하여 인증합니다.

```azurecli
az acr login --name <acrName>
```

`az acr login`을 사용하여 로그인하는 경우 CLI는 `az login`을 실행할 때 만든 토큰을 사용하여 원활하게 레지스트리로 세션을 인증합니다. 이러한 방식으로 로그인하고 나면 자격 증명이 캐시되고 후속 `docker` 명령에 사용자 이름 또는 암호가 필요하지 않습니다. 토큰이 만료될 경우 다시 `az acr login` 명령을 사용하여 토큰을 새로 고친 후 다시 인증합니다. Azure ID와 함께 `az acr login`을 사용하면 [역할 기반 액세스](../role-based-access-control/role-assignments-portal.md)를 제공합니다.

## <a name="service-principal"></a>서비스 주체

레지스트리에 [서비스 주체](../active-directory/develop/active-directory-application-objects.md)를 할당할 수 있으며 응용 프로그램 또는 서비스는 헤드리스 인증에 이를 사용할 수 있습니다. 서비스 주체는 레지스트리에 [역할 기반 액세스](../role-based-access-control/role-assignments-portal.md)를 허용하며 사용자는 레지스트리에 여러 서비스 주체를 할당할 수 있습니다. 여러 서비스 주체를 사용하면 서로 다른 응용 프로그램에 대한 다양한 액세스를 정의할 수 있습니다.

사용 가능한 역할은 다음과 같습니다.

  * **읽기 권한자**: 풀
  * **참가자**: 풀 및 푸시
  * **소유자**: 풀, 푸시, 다른 사용자에게 역할 할당

서비스 주체는 다음과 같은 푸시 및 풀 시나리오에서 레지스트리에 대한 헤드리스 연결을 사용하도록 설정합니다.

  * *읽기 권한자*: 레지스트리에서 Kubernetes, DC/OS 및 Docker Swarm을 포함한 오케스트레이션 시스템으로 컨테이너 배포 컨테이너 레지스트리에서 관련 Azure 서비스(예: [AKS](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) 등)로 가져올 수도 있습니다.

  * *참가자*: 컨테이너 이미지를 빌드하고 레지스트리로 푸시하는 연속 통합 및 배포 솔루션(예: VSTS(Visual Studio Team Services) 또는 Jenkins)

> [!TIP]
> [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials) 명령을 실행하여 서비스 주체의 암호를 다시 생성할 수 있습니다.
>

또한 서비스 주체로 직접 로그인할 수도 있습니다. 다음과 같이 서비스 주체의 앱 ID와 암호를 `docker login` 명령에 제공합니다.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

로그인하면 Docker에서 자격 증명을 캐시하므로 앱 ID를 기억할 필요가 없습니다.

설치한 Docker 버전에 따라 `--password-stdin` 매개 변수 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 문서의 범위 외부에서 사용하는 경우 이 모범 사례를 따르는 것이 좋습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하세요.

ACR에 헤드리스 인증을 위해 서비스 주체를 사용하는 방법에 대한 자세한 내용은 [서비스 주체로 Azure Container Registry 인증](container-registry-auth-service-principal.md)을 참조하세요.

## <a name="admin-account"></a>관리자 계정

각 컨테이너 레지스트리에는 관리 사용자 계정이 포함되어 있으며 기본적으로 사용하지 않도록 설정되어 있습니다. 관리 사용자를 사용하도록 설정하고 [Azure Portal](container-registry-get-started-portal.md#create-a-container-registry)에서 또는 Azure CLI를 사용하여 해당 자격 증명을 관리할 수 있습니다.

> [!IMPORTANT]
> 관리자 계정은 주로 테스트 용도로 단일 사용자가 레지스트리에 액세스하도록 설계되었습니다. 관리자 계정 자격 증명을 여러 사용자와 공유하지 않는 것이 좋습니다. 관리자 계정으로 인증하는 모든 사용자는 레지스트리에 대한 푸시 및 풀 액세스 권한이 있는 단일 사용자로 나타납니다. 이 계정을 변경하거나 사용하지 않도록 설정하면 해당 자격 증명을 사용하는 모든 사용자의 레지스트리 액세스는 허용되지 않습니다. 헤드리스 시나리오의 경우 사용자 및 서비스 주체는 개별 ID를 사용하는 것이 좋습니다.
>

관리자 계정은 두 개의 암호가 제공되며, 둘 다 다시 생성할 수 있습니다. 두 개의 암호를 사용하면 다른 암호를 다시 생성하는 동안에 하나의 암호를 사용하여 레지스트리에 대한 연결을 유지할 수 있습니다. 관리자 계정을 사용할 수 있으면 레지스트리에 대한 기본 인증을 위해 사용자 이름과 둘 중 한 가지 암호를 `docker login` 명령에 전달할 수 있습니다. 예: 

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

향상된 보안을 위해 명령줄에 암호를 제공하는 대신 `--password-stdin` 매개 변수를 사용하는 것이 좋습니다. `-p` 없이 사용자 이름만 지정하고 메시지가 표시되면 암호를 입력할 수 있습니다.

기존 레지스트리에 대한 관리 사용자를 사용하도록 설정하려면 Azure CLI에서 [az acr update](/cli/azure/acr?view=azure-cli-latest#az_acr_update) 명령의 `--admin-enabled` 매개 변수를 사용하면 됩니다.

```azurecli
az acr update -n <acrName> --admin-enabled true
```

레지스트리로 이동하여 **설정**에서 **액세스 키**를 선택한 다음 **관리 사용자**에서 **사용**을 선택하면 Azure Portal에서 관리 사용자를 사용하도록 설정할 수 있습니다.

![Azure Portal에서 관리 사용자 UI 사용][auth-portal-01]

## <a name="next-steps"></a>다음 단계

* [Azure CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
