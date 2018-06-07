---
title: Azure Container Registry SKU
description: Azure Container Registry에서 사용할 수 있는 서로 다른 서비스 계층을 비교합니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: a8dcc6fc60b80a19c4edebd57fdad5bb10cfdd0b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

ACR(Azure Container Registry)은 다중 서비스 계층(즉, SKU)에서 사용 가능합니다. 이러한 SKU는 예측 가능한 가격 책정과 Azure에서 개인 Docker 레지스트리의 용량 및 사용량 패턴에 맞추기 위한 여러 옵션을 제공합니다.

| SKU | 관리 | 설명 |
| --- | :-------: | ----------- |
| **Basic** | 예 | 개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. Basic 레지스트리는 Standard 및 Premium(Azure Active Directory 인증 통합, 이미지 삭제 및 웹 후크)과 동일한 프로그래밍 성능을 가지고 있지만 크기와 사용량 제약이 있습니다. |
| **Standard** | 예 | Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 저장소 제한과 이미지 처리량이 증가합니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다. |
| **Premium** | 예 | Premium 레지스트리는 저장소 및 동시 작업 수 등의 제약에 더 높은 한도를 제공하여 대규모 시나리오가 가능합니다. Premium은 많은 이미지 처리 수용작업량을 제공하는 것 이외에도, 여러 지역 전반에서 단일 레지스트리를 관리하기 위한 [지역에서 복제][container-registry-geo-replication]와 같은 기능을 추가하여 각 배포에서 네트워크에 가까운 레지스트리를 유지합니다. |
| 클래식 | 아니오 | Classic 레지스트리 SKU는 Azure에서 Azure Container Registry 서비스의 초기 릴리스를 지원했습니다. Classic 레지스트리는 사용자 구독에서 Azure가 만드는 저장소 계정에서 지원하며 ACR가 처리량 증가 및 지역에서 복제 등의 더 높은 수준의 기능을 제공할 수 있는 기능을 제한합니다. 제한된 기능으로 인해 앞으로 Classic SKU를 폐기할 계획입니다. |

상위 수준 SKU를 선택하면 더 많은 성능 및 확장성을 제공하지만 모든 관리되는 SKU는 동일한 프로그래밍 방식 기능을 제공합니다. 여러 서비스 계층을 사용하여 Basic로 시작한 다음 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 변환할 수 있습니다.

> [!NOTE]
> Classic 레지스트리 SKU 폐기가 계획되어 있으므로, 모든 새로운 레지스트리에는 Basic, Standard 또는 Premium을 사용하는 것이 좋습니다. 기존 Classic 레지스트리 전환 방법에 대한 자세한 내용은 [Classic 레지스트리 업그레이드][container-registry-upgrade]를 참조하세요.
>

## <a name="managed-vs-unmanaged"></a>관리되는 레지스트리와 관리되지 않는 레지스트리 비교

Basic, Standard 및 Premium SKU는 *관리되는* 레지스트리로 통칭하고 Classic 레지스트리는 *관리되지 않는* 레지스트리라고 합니다. 둘 사이의 주요 차이점은 컨테이너 이미지가 저장되는 방법입니다.

### <a name="managed-basic-standard-premium"></a>관리되는(Basic, Standard, Premium)

관리되는 레지스트리는 Azure에서 완전히 관리하는 이미지 저장소를 사용합니다. 즉, 이미지를 저장하는 저장소 계정은 Azure 구독 내에서 표시되지 않습니다. [Azure Container Registry의 컨테이너 이미지 저장소][container-registry-storage]에서 자세히 설명한 대로 관리되는 레지스트리 SKU 중 하나를 사용하여 얻는 여러 가지 이점이 있습니다. 이 문서는 관리되는 레지스트리 SKU와 해당 기능에 중점을 둡니다.

### <a name="unmanaged-classic"></a>관리되지 않는(Classic)

Classic 레지스트리는 Classic 레지스트리를 지원하는 저장소 계정이 *사용자의* Azure 구독에 상주한다는 점에서 "관리되지 않습니다". 따라서 컨테이너 이미지가 저장되는 저장소 계정의 관리를 담당해야 합니다. 관리되지 않는 레지스트리를 사용하면 필요에 따라 SKU 간에 전환할 수 없으며(관리되는 레지스트리로 [업그레이드][container-registry-upgrade] 대신) 관리되는 레지스트리의 몇 가지 기능을 사용할 수 없습니다(예: 컨테이너 이미지 삭제 [지역에서 복제][container-registry-geo-replication] 및 [웹후크][container-registry-webhook]).

관리되는 SKU 중 하나로 Classic 레지스트리 업그레이드에 대한 자세한 내용은 [Classic 레지스트리 업그레이드][container-registry-upgrade]를 참조하세요.

## <a name="sku-feature-matrix"></a>SKU 기능 행렬

다음 표에는 Basic, Standard 및 Premium 서비스 계층의 기능 및 한계에 대해 자세히 나와 있습니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU 변경

Azure CLI를 사용하거나 Azure Portal에서 레지스트리의 SKU를 변경할 수 있습니다. 전환하는 SKU에 필요한 최대 저장소 용량이 있는 한 관리되는 SKU 간에 자유롭게 이동할 수 있습니다. Classic에서 관리되는 SKU 중 하나로 전환하는 경우 Classic으로 다시 이동할 수 없습니다. 단방향 변환입니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 SKU 간에 이동하려면 [az acr update][az-acr-update] 명령을 사용합니다. 예를 들어 Premium으로 전환하려면:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure Portal의 컨테이너 레지스트리 **개요**에서 **업데이트**를 선택한 다음, SKU 드롭다운에서 새로운 **SKU**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트][update-registry-sku]

Classic 레지스트리가 있는 경우 Azure Portal 내에서 관리되는 SKU를 선택할 수 없습니다. 대신 먼저 관리되는 레지스트리로 [업그레이드][container-registry-upgrade]해야 합니다([Classic에서 변경](#changing-from-classic) 참조).

## <a name="changing-from-classic"></a>Classic에서 변경

관리되는 Basic, Standard 또는 Premium SKU 중 하나로 관리되지 않는 Classic 레지스트리를 마이그레이션할 때 고려해야 할 추가 고려 사항이 있습니다. Classic 레지스트리가 많은 수의 이미지를 포함하고 많은 기가바이트가 있는 경우 마이그레이션 프로세스는 다소 시간이 걸릴 수 있습니다. 또한 `docker push` 작업은 마이그레이션이 완료될 때까지 사용할 수 없습니다.

관리되는 SKU 중 하나로 Classic 레지스트리 업그레이드에 대한 자세한 내용은 [Classic 컨테이너 레지스트리 업그레이드][container-registry-upgrade]를 참조하세요.

## <a name="pricing"></a>가격

각 Azure Container Registry SKU에 대한 가격 책정 정보는 [컨테이너 레지스트리 가격 책정][container-registry-pricing]을 참조하세요.

## <a name="next-steps"></a>다음 단계

**Azure Container Registry 로드맵**

GitHub에서 [ACR 로드맵][acr-roadmap]에 방문하여 예정된 서비스 기능에 대한 정보를 알아 보세요.

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice]에서 새로운 기능 제안 사항을 제출하고 투표합니다.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
