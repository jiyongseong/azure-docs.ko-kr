---
title: Azure Kubernetes Service에 대한 질문과 대답
description: Azure Kubernetes Service에 대한 일반적인 질문에 대한 답변을 제공합니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/09/2018
ms.author: nepeters
ms.openlocfilehash: 3152dc69bc8fb9a94111f85976e5d999c4b18261
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

> [!IMPORTANT]
> AKS(Azure Kubernetes Service)는 현재 **미리 보기**로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>현재 AKS(Azure Kubernetes Service)는 어떤 Azure 지역에서 제공되나요?

- 캐나다 중부
- 캐나다 동부
- 미국 중부
- 미국 동부
- 서유럽

## <a name="when-will-additional-regions-be-added"></a>추가 지역은 언제 추가되나요?

추가 지역은 수요가 증가하면 추가됩니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 노드에 보안 패치를 적용합니다. 하지만 필요에 따라 노드가 다시 부팅되도록 해야 합니다. 노드 다시 부팅을 수행하기 위한 몇 가지 옵션이 있습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터는 자동으로 [cordon 및 드레이닝 노드](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)를 업그레이드한 다음, 최신 Ubuntu 이미지로 백업합니다. `az aks upgrade`에서 현재 클러스터 버전을 지정하여 Kubernetes 버전을 변경하지 않으면서 노드에 OS 이미지를 업데이트합니다.
- Kubernetes를 위한 오픈 소스 재부팅 디먼인 [Kured](https://github.com/weaveworks/kured)를 사용합니다. Kured는 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)으로 실행하며 다시 부팅해야 함을 표시하는 파일의 존재에 대한 각 노드를 모니터링합니다. 그런 다음, 앞서 설명한 동일한 cordon 및 드레이닝 프로세스에 따라 클러스터 전반의 다시 부팅을 오케스트레이트합니다.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>고객이 ACS 또는 AKS를 사용하도록 권장하나요?

AKS는 미리 보기로 두는 동안, ACS-Kubernetes 또는 [acs-engine](https://github.com/azure/acs-engine)을 사용하여 프로덕션 클러스터를 만들도록 권장합니다. 개념 증명 배포 및 개발/테스트 환경에 AKS를 사용합니다.

## <a name="when-will-acs-be-deprecated"></a>ACS는 언제 지원이 종료될 예정인가요?

ACS는 AKS가 GA가 될 때쯤에 지원이 종료될 예정입니다. 이 날로부터 12개월 후에는 클러스터를 AKS로 마이그레이션할 예정입니다. 이 12개월 기간 동안 모든 ACS 작업을 실행할 수 있습니다.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요?

노드 자동 크기 조정은 지원되지 않지만 계획 중입니다. 이 오픈 소스 [자동 크기 조정 구현][auto-scaler]을 살펴볼 수 있습니다.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS는 Kubernetes RBAC(역할 기반 액세스 제어)를 지원합니까?

아니요, RBAC는 현재 AKS에서 지원되지 않지만, 곧 제공될 예정입니다.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>AKS를 기존 가상 네트워크에 배포할 수 있습니까?

예, 이것은 [고급 네트워킹 기능](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md)을 통해 지원됩니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

아니요. 통합되지 않지만 이 통합은 계획 중입니다. 당분간은 [Hexadite][hexadite]에서 다음 솔루션을 수행해봅니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

Windows Server 컨테이너를 실행하려면 Windows Server 기반 노드를 실행해야 합니다. Windows Server 기반 노드는 현재 [비공개 미리보기](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/)에 있습니다. 미리 보기 밖의 Azure에서 Kubernetes에 Windows Server 컨테이너를 실행해야 하는 경우 [acs-engine에 대한 설명서](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)를 참조하세요.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

각 AKS 배포는 두 리소스 그룹에 걸쳐 있습니다. 첫 번째 리소스 그룹이 만들어지고 AKS 리소스만 포함됩니다. *MC_myResourceGRoup_myAKSCluster_eastus*와 같은 이름으로 배포되는 동안 AKS 리소스 공급자는 자동으로 두 번째 리소스 그룹을 만듭니다. 두 번째 리소스 그룹에는 VM, 네트워킹 및 저장소와 같이 클러스터와 연결된 인프라 리소스의 모든 항목이 포함됩니다. 이는 리소스 정리를 간소화하기 위해 생성됩니다.

저장소 계정 또는 예약된 공용 IP 주소와 같은 AKS 클러스터를 함께 사용될 리소스를 만드는 경우 자동으로 생성된 리소스 그룹에 배치해야 합니다.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
