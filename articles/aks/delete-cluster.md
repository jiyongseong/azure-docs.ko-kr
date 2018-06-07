---
title: AKS(Azure Kubernetes Service) 클러스터 삭제
description: CLI 또는 Azure Portal에서 AKS 클러스터를 삭제합니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6391e36eff60634e07a90c1e6b5f0f44ee60d46b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 삭제

Azure Kubernetes Service 클러스터를 삭제하면 클러스터가 배포된 리소스 그룹은 남아 있지만 모든 AKS 관련 리소스가 삭제됩니다. 이 문서에서는 Azure CLI 및 Azure Portal을 사용하여 AKS 클러스터를 삭제하는 방법을 보여줍니다.

클러스터 삭제 외에도, 클러스터가 배포된 리소스 그룹을 삭제할 수 있으며, 그러면 AKS 클러스터도 함께 삭제됩니다.

## <a name="azure-cli"></a>Azure CLI

AKS 클러스터를 삭제하려면 [az aks delete][az-aks-delete] 명령을 사용합니다.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

다음 옵션은 `az aks delete` 명령을 통해 사용할 수 있습니다.

| 인수 | 설명 | 필수 |
|---|---|:---:|
| `--name` `-n` | 관리 클러스터에 대한 리소스 이름입니다. | 예 |
| `--resource-group` `-g` | Azure Kubernetes Service 리소스 그룹의 이름입니다. | 예 |
| `--no-wait` | 장기 실행 작업이 완료될 때까지 기다리지 마세요. | no |
| `--yes` `-y` | 확인을 묻는 메시지를 표시하지 마세요. | no |

## <a name="azure-portal"></a>Azure portal

Azure Portal에서 AKS(Azure Kubernetes Service) 리소스가 포함된 리소스 그룹을 찾아서 리소스를 선택하고, **삭제**를 클릭합니다. 삭제 작업을 확인하라는 메시지가 표시됩니다.

![AKS 클러스터 포털 삭제](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete