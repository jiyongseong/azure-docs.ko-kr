---
title: AKS에서 Azure File 사용
description: AKS에서 Azure 디스크 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 991db1fc32ae89ab04ca040cfb6e8d59ffe5262f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356446"
---
# <a name="persistent-volumes-with-azure-files"></a>Azure Files가 포함된 영구적 볼륨

영구적 볼륨은 Kubernetes 클러스터에서 사용하기 위해 만든 저장소의 일부입니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적으로 또는 정적으로 생성할 수 있습니다. 이 문서에서는 영구적 볼륨으로 Azure 파일의 **동적 생성**에 대해 자세히 설명합니다.

정적 생성을 포함하여 Kubernetes 영구적 볼륨에 대한 자세한 내용은 [Kubernetes 영구적 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="create-storage-account"></a>저장소 계정 만들기

Azure 파일 공유를 Kubernetes 볼륨으로 동적으로 생성하는 경우 AKS 클러스터와 동일한 리소스 그룹에 들어 있는 모든 저장소 계정을 사용할 수 있습니다. 필요한 경우 AKS 클러스터와 동일한 리소스 그룹에 저장소 계정을 만듭니다.

적절한 리소스 그룹을 식별하려면 [az group list][az-group-list] 명령을 사용합니다.

```azurecli-interactive
az group list --output table
```

`MC_clustername_clustername_locaton`과 비슷한 이름의 리소스 그룹을 찾습니다.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

[az storage account create][az-storage-account-create] 명령을 사용하여 저장소 계정을 만듭니다.

이 예제를 사용하고 `--resource-group`을 리소스 그룹의 이름으로, `--name`을 사용자가 선택한 이름으로 업데이트합니다.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>저장소 클래스 만들기

저장소 클래스는 Azure 파일 공유를 만드는 방법을 정의하는 데 사용됩니다. 클래스에 특정 저장소 계정을 지정할 수 있습니다. 저장소 계정을 지정하지 않으면 `skuName` 및 `location`을 지정해야 하고, 연결된 리소스 그룹의 모든 저장소 계정이 일치하는지 평가됩니다.

Azure 파일의 Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조하세요.

파일 `azure-file-sc.yaml`을 만들고 다음 매니페스트에 복사합니다. `storageAccount`를 대상 저장소 계정의 이름으로 업데이트합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

[kubectl apply][kubectl-apply] 명령을 사용하여 저장소 클래스를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)는 저장소 클래스 개체를 사용하여 Azure 파일 공유를 동적으로 프로비전합니다.

다음 YAML을 사용하여 크기 `5GB`에 `ReadWriteOnce` 액세스 권한을 가진 영구적 볼륨 클레임을 만들 수 있습니다. 액세스 모드에 대한 자세한 내용은 [Kubernetes 영구 볼륨][access-modes] 설명서를 참조하세요.

파일 `azure-file-pvc.yaml`을 만들고 다음 YAML에 복사합니다. `storageClassName`이 마지막 단계에서 만든 저장소 클래스와 일치하는지 확인합니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 클레임을 만듭니다.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

완료되면 파일 공유가 생성됩니다. 연결 정보 및 자격 증명을 포함하고 있는 Kubernetes 비밀도 생성됩니다.

## <a name="using-the-persistent-volume"></a>영구적 볼륨 사용

다음 YAML은 영구적 볼륨 클레임 `azurefile`을 사용하여 `/mnt/azure` 경로에 Azure 파일 공유를 탑재하는 Pod를 만듭니다.

`azure-pvc-files.yaml` 파일을 만들고 다음 YAML에 복사합니다. `claimName`이 마지막 단계에서 만든 PVC와 일치하는지 확인합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 이 구성은 `kubectl describe pod mypod`를 통해 Pod를 검사할 때 볼 수 있습니다.

## <a name="mount-options"></a>탑재 옵션

다음 표에 설명된 대로 Kubernetes 버전마다 기본 fileMode 및 dirMode 값이 다릅니다.

| 버전 | 값 |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 이상 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 이상 | 0755 |

클러스터 버전 1.8.5 이상을 사용하고 저장소 클래스를 사용하여 동적으로 영구적 볼륨을 만드는 경우 저장소 클래스 개체에서 마운트 옵션을 지정할 수 있습니다. 다음 예제에서는 `0777`을 설정합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

클러스터 버전 1.8.5 이상을 사용하고 정적으로 영구적 볼륨 개체를 만드는 경우 `PersistentVolume` 개체에서 마운트 옵션을 지정해야 합니다. 정적으로 영구 볼륨을 만드는 방법에 대한 자세한 내용은 [정적 영구적 볼륨][pv-static]을 참조하세요.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

버전 1.8.0 - 1.8.4의 클러스터를 사용하는 경우 `runAsUser` 값을 `0`으로 설정하여 보안 컨텍스트를 지정할 수 있습니다. Pod 보안 컨텍스트에 대한 자세한 내용은 [보안 컨텍스트 구성][kubernetes-security-context]을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Files를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Files용 Kubernetes 플러그 인][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
