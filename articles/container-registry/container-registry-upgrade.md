---
title: Classic Azure Container Registry 업그레이드
description: 관리되지 않는 Classic 컨테이너 레지스트리를 업그레이드하여 Basic, Standard 및 Premium의 관리되는 컨테이너 레지스트리의 확장된 기능 집합을 활용할 수 있습니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 084dfc8f87aaea4b5bbad7cb5fdb9d445d566206
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-a-classic-container-registry"></a>Classic 컨테이너 레지스트리 업그레이드

ACR(Azure Container Registry)은 여러 서비스 계층(즉, [SKU](container-registry-skus.md))에서 사용 가능합니다. ACR의 초기 릴리스는 Basic, Standard 및 Premium SKU(통틀어 *관리되는* 레지스트리)에 내재된 몇 가지 기능이 없는 Classic 단일 SKU를 제공했습니다. 이 문서에서는 향상된 기능 집합의 사용할 수 있도록, 관리되지 않는 Classic 레지스트리를 관리되는 SKU 중 하나로 마이그레이션하는 방법을 자세히 설명합니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유

Classic의 관리되지 않는 레지스트리는 기능이 제한되어 있으므로 모든 Classic 레지스트리는 Basic, Standard 또는 Premium 관리되는 레지스트리로 업그레이드하는 것이 좋습니다. 이러한 더 높은 수준의 SKU는 레지스트리를 Azure 기능에 더 깊이 통합합니다.

관리되는 레지스트리는 다음 기능을 제공합니다.

* [개별 로그인](container-registry-authentication.md#individual-login-with-azure-ad)에 대한 Azure Active Directory 통합
* 이미지 및 태그 삭제 지원
* [지역에서 복제](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

무엇보다도 클래식 레지스트리는 해당 레지스트리를 만들 때 Azure 구독에서 Azure가 자동으로 프로비전한 저장소 계정에 따라 다릅니다. 반면, Basic, Standard 및 Premium SKU는 사용자의 이미지 저장소를 투명하게 처리하여 Azure의 [고급 저장소 기능](container-registry-storage.md)을 활용합니다. 별도 저장소 계정이 고유한 구독에서 생성되지 않습니다.

관리되는 레지스트리 저장소는 다음과 같은 이점을 제공합니다.

* 컨테이너 이미지는 [암호화된 상태로 유지](container-registry-storage.md#encryption-at-rest)됩니다.
* 이미지는 [지역 중복 저장소](container-registry-storage.md#geo-redundant-storage)를 사용하여 저장되므로 여러 지역 복제 시 이미지를 확실하게 백업할 수 있습니다.
* 자유로운 [SKU 간 이동](container-registry-skus.md#changing-skus) 기능으로 더 높은 수준의 SKU를 선택하면 더 높은 처리량을 지원할 수 있습니다. 각 SKU를 사용하여 ACR은 요구 사항 증가에 따라 처리량 요구 사항을 충족할 수 있습니다.
* 레지스트리 및 해당 저장소에 대한 통합 보안 모델은 간소화된 권한 관리를 제공합니다. 또한 별도 저장소 계정에 대한 사용 권한을 관리할 필요 없이, 컨테이너 레지스트리의 사용 권한만 관리하면 됩니다.

ACR의 이미지 저장소에 대한 자세한 내용은 [Azure Container Registry의 컨테이너 이미지 저장소](container-registry-storage.md)를 참조하세요.

## <a name="migration-considerations"></a>마이그레이션 고려 사항

Classic 레지스트리를 관리되는 레지스트리로 변경할 경우 Azure는 기존 컨테이너 이미지를 구독의 ACR 생성 저장소 계정에서 Azure에서 관리하는 저장소 계정으로 복사합니다. 이 프로세스는 레지스트리 크기에 따라 몇 분에서 몇 시간까지 걸릴 수 있습니다.

변환 프로세스 동안 모든 `docker push` 작업은 차단되지만 `docker pull`은 정상적으로 작동합니다.

변환 프로세스 동안 Classic 레지스트리를 지원하는 저장소 계정의 콘텐츠는 삭제하거나 수정하지 마세요. 이렇게 하면 컨테이너 이미지가 손상될 수 있습니다.

마이그레이션이 완료되면 원래 Classic 레지스트리를 지원하던 구독의 저장소 계정이 ACR에서 더 이상 사용되지 않습니다. 마이그레이션에 성공했는지 확인한 후에는 비용을 최소화하기 위해 저장소 계정을 삭제하는 것이 좋습니다.

>[!IMPORTANT]
> Classic에서 관리되는 SKU 중 하나로 업그레이드하는 과정은 **단방향 프로세스**입니다. 일단 Classic 레지스트리를 Basic, Standard 또는 Premium으로 변환하면 Classic으로 되돌릴 수 없습니다. 그러나 레지스트리에 대해 충분한 용량이 있는 관리되는 SKU 간에는 자유롭게 이동할 수 있습니다.

## <a name="how-to-upgrade"></a>업그레이드하는 방법

관리되지 않는 Classic 레지스트리를 관리되는 SKU로 업그레이드하는 방법에는 여러 가지가 있습니다. 다음 섹션에서는 [Azure CLI][azure-cli] 및 [Azure Portal][azure-portal]을 사용하는 프로세스를 설명합니다.

## <a name="upgrade-in-azure-cli"></a>Azure CLI에서 업그레이드

Azure CLI에서 Classic 레지스트리를 업그레이드하려면 [az acr update][az-acr-update] 명령을 실행하고 레지스트리에 대한 새로운 SKU를 지정합니다. 다음 예제에서는 *myclassicregistry*라는 Classic 레지스트리가 Premium SKU로 업그레이드됩니다.

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

마이그레이션이 완료되면 다음과 비슷한 출력이 표시됩니다. `sku`는 "Premium"이고 `storageAccount`는 "null"로, Azure가 이제 이 레지스트리에 대한 이미지 저장소를 관리함을 나타냅니다.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

최대 용량이 Classic 레지스트리 크기보다 작은 관리되는 레지스트리 SKU를 지정하면 다음과 유사한 오류 메시지가 수신됩니다.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

유사한 오류가 발생하면 [az acr update][az-acr-update] 명령을 실행하고, 이미지를 수용할 수 있는 다음 최상위 수준 SKU에 해당하는 제안된 SKU를 지정합니다.

## <a name="upgrade-in-azure-portal"></a>Azure Portal에서 업그레이드

Azure Portal을 사용하여 Classic 레지스트리를 업그레이드하는 경우, Azure는 이미지를 수용할 수 있는 가장 낮은 수준의 SKU를 자동으로 선택합니다. 예를 들어, 레지스트리에 12GiB 이미지가 들어 있는 경우 Azure에서는 Classic 레지스트리를 자동으로 선택하고 Standard(최대 100GiB)로 변환합니다.

Azure Portal을 사용하여 Classic 레지스트리를 업그레이드하려면 컨테이너 레지스트리의 **개요**로 이동한 후 **관리되는 레지스트리로 업그레이드**를 선택합니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 단추][update-classic-01-upgrade]

**확인**을 선택하여 관리되는 레지스트리로 업그레이드할 것임을 확인합니다.

![Azure Portal UI에서 Classic 레지스트리 업그레이드 확인][update-classic-02-confirm]

마이그레이션하는 동안 포털에서는 레지스트리의 **프로비전 상태**가 *업데이트 중*임을 나타냅니다. 앞에서 설명한 것처럼 `docker push` 작업은 마이그레이션하는 동안 사용되지 않도록 설정되므로, 마이그레이션이 진행되는 동안에는 Classic 레지스트리에서 사용되는 저장소 계정을 삭제하거나 업데이트하지 않아야 합니다. 이렇게 하면 이미지가 손상될 수 있습니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 진행 상태][update-classic-03-updating]

마이그레이션이 완료되면 **프로비전 상태**가 *성공*으로 표시되므로, 레지스트리에 대해 `docker push`를 한 번 더 실행할 수 있습니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 완료 상태][update-classic-04-updated]

## <a name="next-steps"></a>다음 단계

Classic 레지스트리를 Basic, Standard 또는 Premium으로 업그레이드하면 Azure에서는 Classic 레지스트리를 지원하던 저장소 계정을 더 이상 사용하지 않습니다. 비용을 줄이기 위해 이전 컨테이너 이미지를 포함하는 저장소 계정 또는 계정 내의 Blob 컨테이너를 삭제하는 것이 좋습니다.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com