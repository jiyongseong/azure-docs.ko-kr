---
title: AKS에서 Azure File 사용
description: AKS에서 Azure 디스크 사용
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 163db8fdaecefbf51174392ba37039115cdb91c8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="volumes-with-azure-files"></a>Azure 파일이 포함된 볼륨

컨테이너 기반 응용 프로그램은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Azure 파일은 외부 데이터 저장소로 사용할 수 있습니다. 이 문서에서는 Azure Kubernetes Service에서 Azure 파일을 Kubernetes 볼륨으로 사용하는 방법을 자세히 설명합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [Kubernetes 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure 파일 공유를 Kubernetes 볼륨을 사용하려면 먼저 Azure Storage 계정과 파일 공유를 만들어야 합니다. 이 작업에는 다음 스크립트를 사용할 수 있습니다. 매개 변수 값을 기록해 두세요. Kubernetes 볼륨을 만들 때 이 값이 필요합니다.

```azurecli-interactive
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Kubernetes 비밀 만들기

Kubernetes에는 파일 공유에 액세하기 위해 자격 증명이 필요합니다. 자격 증명은 [Kubernetes 비밀][kubernetes-secret]에 저장됩니다. Kubernetes 비밀은 Kubernetes Pod를 만들 때 참조됩니다.

다음 명령을 사용하여 비밀을 만듭니다. `STORAGE_ACCOUNT_NAME`를 저장소 계정의 이름으로 바꾸고 `STORAGE_ACCOUNT_KEY`를 저장소 키로 바꿉니다.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>파일 공유를 볼륨으로 탑재

해당 사양에서 볼륨을 구성하여 Azure 파일 공유를 Pod에 탑재합니다. 다음 내용이 포함된 새 파일 `azure-files-pod.yaml`을 만듭니다. `aksshare`을 Azure 파일 공유에 지정된 이름으로 업데이트합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

kubectl을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

이제 Azure 파일 공유가 `/mnt/azure` 디렉터리에 탑재된 컨테이너가 실행되고 있습니다.  `kubectl describe pod azure-files-pod`를 통해 Pod를 검사하여 볼륨 탑재를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Files를 사용하는 Kubernetes 볼륨에 대해 자세히 알아보기

> [!div class="nextstepaction"]
> [Azure Files용 Kubernetes 플러그 인][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
