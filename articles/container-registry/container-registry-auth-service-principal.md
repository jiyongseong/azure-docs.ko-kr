---
title: 서비스 주체로 Azure Container Registry 인증
description: Azure Active Directory 서비스 주체를 사용하여 개인 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공하는 방법에 대해 알아봅니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: 16af83522dd55744c485f6dd3696481e16da1b22
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>서비스 주체로 Azure Container Registry 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 컨테이너 레지스트리에 대해 컨테이너 이미지 `docker push` 및 `pull` 액세스 권한을 제공할 수 있습니다. 서비스 주체를 사용하면 "헤드리스" 서비스 및 응용 프로그램에 대한 액세스를 제공할 수 있습니다.

## <a name="what-is-a-service-principal"></a>서비스 주체란?

Azure AD *서비스 주체*는 구독 내 Azure 리소스에 대한 액세스를 제공합니다. 서비스 주체는 서비스에 대한 사용자 ID라고 생각할 수 있습니다. 여기서 "서비스"는 리소스에 액세스 권한이 필요한 응용 프로그램, 서비스 또는 플랫폼입니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 응용 프로그램이나 서비스를 구성할 수 있습니다.

Azure Container Registry 컨텍스트에서, Azure의 개인 Docker 레지스트리에 대한 풀, 푸시 및 풀 또는 소유자 권한이 있는 Azure AD 서비스 주체를 만들 수 있습니다.

## <a name="why-use-a-service-principal"></a>서비스 주체를 사용하는 이유

Azure AD 서비스 주체를 사용하면 개인 컨테이너 레지스트리에 대해 범위가 지정된 액세스를 제공할 수 있습니다. 응용 프로그램이나 서비스마다 레지스트리에 대한 맞춤 액세스 권한이 있는 여러 서비스 주체를 만들 수 있습니다. 또한 서비스와 응용 프로그램 사이에 자격 증명을 공유하지 않아도 되기 때문에, 선택한 서비스 주체(및 응용 프로그램)에 대해서만 자격 증명을 순환하거나 액세스 권한을 철회할 수 있습니다.

예를 들어, 웹 응용 프로그램은 이미지 `pull` 액세스만 제공하는 서비스 주체를 사용할 수 있는 반면에 빌드 시스템은 `push` 및 `pull` 액세스를 모두 제공하는 서비스 주체를 사용할 수 있습니다. 응용 프로그램 개발 팀 인력에 변화가 있으면, 빌드 시스템에 영향을 주지 않으면서 서비스 주체 자격 증명을 순환할 수 있습니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

**헤드리스 시나리오**에서 레지스트리 액세스를 제공하려면 서비스 주체를 사용해야 합니다. 즉, 컨테이너 이미지를 자동 또는 무인 방식으로 컨테이너 이미지를 푸시하거나 풀해야 하는 모든 응용 프로그램, 서비스 또는 스크립트입니다.

레지스트리에 개별 액세스하는 경우(예: 컨테이너 이미지를 개발 워크스테이션에 수동으로 풀하는 경우)에는, 레지스트리 액세스를 위해 자신의 [Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad)(예: [az acr login][az-acr-login])를 사용해야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 이전 샘플 스크립트 및 Azure PowerShell에 대한 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>다음 단계

컨테이너 레지스트리에 대한 액세스 권한을 부여한 서비스 주체가 있는 경우, 레지스트리 상호 작용을 위해 응용 프로그램 및 서비스에서 해당 자격 증명을 사용할 수 있습니다.

서비스 주체 자격 증명을 사용하도록 개별 응용 프로그램을 구성하는 내용은 이 문서의 범위 밖이지만, 다음 항목에서 특정 서비스 및 플랫폼에 대한 지침을 찾을 수 있습니다.

* [AKS(Azure Kubernetes Service)의 Azure Container Registry를 사용하여 인증](container-registry-auth-aks.md)
* [Azure Container Instances의 Azure Container Registry를 사용하여 인증(ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login