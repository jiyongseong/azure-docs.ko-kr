---
title: AKS에서 Azure 디스크 사용
description: AKS에서 Azure 디스크 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b790213e19b9f2aaef74a3f670c89246f54fd6d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-disks"></a>Azure 디스크가 포함된 볼륨

컨테이너 기반 응용 프로그램은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Azure 디스크를 외부 데이터 저장소로 사용할 수 있습니다. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크를 Kubernetes 볼륨으로 사용하는 방법을 자세히 설명합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [Kubernetes 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="create-an-azure-disk"></a>Azure 디스크 만들기

Azure 관리되는 디스크를 Kubernetes 볼륨으로 탑재하려면 먼저 디스크가 AKS 클러스터 리소스와 동일한 리소스 그룹에 있어야 합니다. 이 리소스 그룹을 찾으려면 [az group list][az-group-list] 명령을 사용합니다.

```azurecli-interactive
az group list --output table
```

`MC_clustername_clustername_locaton`과 비슷한 이름의 리소스 그룹을 찾습니다. 여기서 clustername은 AKS 클러스터의 이름이고, location은 클러스터가 배포된 Azure 지역입니다.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

[az disk create][az-disk-create] 명령을 사용하여 Azure 디스크를 만듭니다.

이 예제를 사용하고 `--resource-group`을 리소스 그룹의 이름으로, `--name`을 사용자가 선택한 이름으로 업데이트합니다.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

디스크를 만든 후 다음과 비슷한 출력이 표시됩니다. 이 값은 Kubernetes pod에 디스크를 탑재할 때 사용되는 디스크 ID입니다.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>볼륨으로 디스크 탑재

컨테이너 사양에서 볼륨을 구성하여 Azure 디스크를 pod에 탑재합니다.

다음 내용이 포함된 새 파일 `azure-disk-pod.yaml`을 만듭니다. `diskName`을 새로 만든된 디스크의 이름으로, `diskURI`를 디스크 ID로 업데이트합니다. 또한 Azure 디스크를 pod에 탑재하는 경로인 `mountPath`를 적어둡니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

kubectl을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

이제 Azure 디스크가 `/mnt/azure`에 탑재된 Pod가 실행되고 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 디스크를 사용하는 Kubernetes 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
