---
title: AKS(Azure Kubernetes Service) 클러스터 만들기
description: CLI 또는 Azure Portal에서 AKS 클러스터를 만듭니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 만들기

AKS(Azure Kubernetes Service) 클러스터는 Azure CLI 또는 Azure Portal에서 만들 수 있습니다.

## <a name="azure-cli"></a>Azure CLI

AKS 클러스터를 만들려면 [az aks create][az-aks-create] 명령을 사용합니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

다음 옵션은 `az aks create` 명령을 통해 사용할 수 있습니다.

| 인수 | 설명 | 필수 |
|---|---|:---:|
| `--name` `-n` | 관리 클러스터에 대한 리소스 이름입니다. | 예 |
| `--resource-group` `-g` | Azure Kubernetes Service 리소스 그룹의 이름입니다. | 예 |
| `--admin-username` `-u` | Linux Virtual Machines의 사용자 이름입니다.  기본값: azureuser. | no |
| ` --client-secret` | 서비스 사용자와 연결된 비밀입니다. | no |
| `--dns-name-prefix` `-p` | 클러스터 공용 ip 주소의 DNS 접두사입니다. | no |
| `--generate-ssh-keys` | 없는 경우 SSH 공용 및 개인 키 파일을 생성합니다. | no |
| `--kubernetes-version` `-k` | '1.7.9' 또는 '1.8.2'처럼 클러스터를 만드는 데 사용할 Kubernetes 버전입니다.  기본값: 1.7.7. | no |
| `--no-wait` | 장기 실행 작업이 완료될 때까지 기다리지 마세요. | no |
| `--node-count` `-c` | 노드 풀의 기본 노드 수입니다.  기본값: 3. | no |
| `--node-osdisk-size` | 노드 풀 가상 머신의 OS 디스크 크기(GB)입니다. | no |
| `--node-vm-size` `-s` | 가상 머신의 크기입니다.  기본값: Standard_D1_v2. | no |
| `--service-principal` | 클러스터 인증에 사용되는 서비스 사용자입니다. | no |
| `--ssh-key-value` | SSH 키 파일 값 또는 키 파일 경로입니다.  기본값: ~/.ssh/id_rsa.pub. | no |
| `--tags` | 'key[=value]' 형식의 공백으로 구분된 태그입니다. 기존 태그를 지우려면 ''를 사용합니다. | no |

## <a name="azure-portal"></a>Azure portal

Azure Portal에서 AKS 클러스터를 배포하는 방법에 대한 지침은 AKS(Azure Kubernetes Service) [Azure Portal 빠른 시작][aks-portal-quickstart]을 참조하세요.

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
