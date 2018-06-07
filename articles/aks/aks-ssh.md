---
title: AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기
description: AKS(Azure Kubernetes Service) 클러스터 노드를 사용하는 SSH 연결을 만듭니다.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c2b77e558db0e323370c24b87a75357235677f7e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기

경우에 따라 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 AKS(Azure Kubernetes Service) 노드에 액세스해야 할 수도 있습니다. AKS(Azure Kubernetes Service) 노드는 인터넷에 노출되지 않습니다. 이 문서에서 설명하는 단계를 사용하여 AKS 노드를 사용하는 SSH 연결을 만듭니다.

## <a name="get-aks-node-address"></a>AKS 노드 주소 가져오기

`az vm list-ip-addresses` 명령을 사용하여 AKS 클러스터 노드의 IP 주소를 가져옵니다. 리소스 그룹 이름을 AKS 리소스 그룹의 이름으로 바꿉니다.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>SSH 연결 만들기

`debian` 컨테이너 이미지를 실행하고 여기에 터미널 세션을 연결합니다. 그러면 컨테이너를 사용하여 AKS 클러스터의 노드가 있는 SSH 세션을 만들 수 있습니다.

```console
kubectl run -it --rm aks-ssh --image=debian
```

컨테이너에 SSH 클라이언트를 설치합니다.

```console
apt-get update && apt-get install openssh-client -y
```

두 번째 터미널을 열고, 모든 Pod를 나열하여 새로 만든 Pod 이름을 가져옵니다.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

SSH 키를 Pod에 복사하고, Pod 이름을 적절한 값으로 바꿉니다.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

사용자 읽기 전용이 되도록 `id_rsa` 파일을 업데이트합니다.

```console
chmod 0600 id_rsa
```

이제 AKS 노드에 대한 SSH 연결을 만듭니다. AKS 클러스터에 대한 기본 사용자 이름은 `azureuser`입니다. 클러스터를 만들 때 이 계정을 변경한 경우 적절한 관리 사용자 이름으로 바꿉니다.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>SSH 액세스 제거

완료되면 SSH 세션을 종료한 다음, 대화형 컨테이너 세션을 종료합니다. 이 작업은 AKS 클러스터에서 SSH 액세스에 사용되는 Pod를 삭제합니다.