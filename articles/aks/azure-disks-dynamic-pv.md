---
title: AKS에서 Azure 디스크 사용
description: AKS에서 Azure 디스크 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 858961db439b28a71d3475d2608073287e02f2fd
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Azure 디스크가 포함된 영구적 볼륨

영구적 볼륨은 Kubernetes Pod와 함께 사용하기 위해 프로비전된 저장소 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. Kubernetes 영구적 볼륨에 대한 자세한 내용은 [Kubernetes 영구적 볼륨][kubernetes-volumes]을 참조하세요.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크와 함께 영구 볼륨을 사용하는 방법을 자세히 설명합니다.

> [!NOTE]
> Azure 디스크는 액세스 모드 형식 ReadWriteOnce만 사용하여 탑재할 수 있으며, 이 모드는 단일 AKS 노드에서만 사용할 수 있습니다. 여러 노드에서 영구 볼륨을 공유해야 하는 경우, [Azure 파일][azure-files-pvc]을 사용하는 것이 좋습니다.

## <a name="built-in-storage-classes"></a>기본 제공 저장소 클래스

저장소 클래스를 사용하여 영구적 볼륨에서 저장소 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조하세요.

모든 AKS 클러스터에는 Azure 디스크에 작동하도록 구성된 2개의 미리 만들어진 저장소 클래스가 포함되어 있습니다. `default` 저장소 클래스는 표준 Azure 디스크를 프로비전합니다. `managed-premium` 저장소 클래스는 프리미엄 Azure 디스크를 프로비전합니다. 클러스터의 AKS 노드가 프리미엄 저장소를 사용하는 경우 `managed-premium` 클래스를 선택합니다.

[kubectl get sc][kubectl-get] 명령을 사용하여 미리 생성된 저장소 클래스를 확인합니다.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)을 사용하여 저장소 클래스를 기반으로 하는 저장소를 자동으로 프로비전합니다. 이 경우에 PVC는 미리 생성된 저장소 클래스 중 하나를 사용하여 표준 또는 프리미엄 Azure 관리 디스크를 만들 수 있습니다.

파일 `azure-premimum.yaml`을 만들고 다음 매니페스트에 복사합니다.

`managed-premium` 저장소 클래스를 주석에서 지정하고 클레임이 `ReadWriteOnce` 액세스 권한으로 크기가 `5GB`인 디스크를 요청합니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 클레임을 만듭니다.

```azurecli-interactive
kubectl apply -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 영구적 볼륨 클레임 `azure-managed-disk`을 사용하여 `/mnt/azure` 경로에 Azure 디스크를 탑재하는 Pod를 만듭니다.

파일 `azure-pvc-disk.yaml`을 만들고 다음 매니페스트에 복사합니다.

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
        claimName: azure-managed-disk
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 이 구성은 `kubectl describe pod mypod`를 통해 Pod를 검사할 때 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 디스크를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md