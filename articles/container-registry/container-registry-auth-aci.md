---
title: Azure Container Instances의 Azure Container Registry를 사용하여 인증
description: Azure Active Directory 서비스 주체를 사용하여 Azure Container Instances에서 개인 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공하는 방법에 대해 알아봅니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: daa9c098de0c410bd4033cc62ee911631eb3b634
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Azure Container Instances의 Azure Container Registry를 사용하여 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 Azure Container Registry에서 개인 컨테이너 레지스트리에 액세스 권한을 제공할 수 있습니다.

이 문서에서는 레지스트리에 대한 *풀* 권한으로 Azure AD 서비스 주체를 만들고 구성하는 방법을 알아봅니다. 그런 다음 인증을 위해 서비스 주체를 사용하여 개인 레지스트리에서 이미지를 풀하는 ACI(Azure Container Instances)에서 컨테이너를 시작합니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

자동 또는 무인 방식으로 컨테이너 인스턴스를 만드는 응용 프로그램이나 서비스에서처럼 **헤드리스 시나리오**에서는 ACI의 인증에 서비스 주체를 사용해야 합니다.

예를 들어 야간에 실행되는 자동화된 스크립트가 있고 데이터를 처리하기 위해 [작업 기반 컨테이너 인스턴스](../container-instances/container-instances-restart-policy.md)를 만드는 경우, 풀 전용(읽기 권한자) 권한이 있는 서비스 주체를 사용하여 레지스트리에 인증할 수 있습니다. 그런 다음 서비스 주체의 자격 증명을 순환시키거나 다른 서비스와 응용 프로그램에 영향을 주지 않으면서 액세스를 완전히 철회할 수 있습니다.

레지스트리 [관리 사용자](container-registry-authentication.md#admin-account)가 비활성화된 경우에도 서비스 주체를 사용해야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>서비스 주체를 사용하여 인증

서비스 주체를 사용하여 Azure Container Instances에서 컨테이너를 시작하려면 `--registry-username`에 대한 ID와 `--registry-password`에 대한 암호를 지정합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 이전 샘플 스크립트 및 Azure PowerShell에 대한 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>다음 단계

다음 문서에는 서비스 주체 및 ACR 작업에 대한 추가 세부 정보가 포함되어 있습니다.

* [서비스 주체로 Azure Container Registry 인증](container-registry-auth-service-principal.md)
* [AKS(Azure Kubernetes Service)의 Azure Container Registry를 사용하여 인증](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
