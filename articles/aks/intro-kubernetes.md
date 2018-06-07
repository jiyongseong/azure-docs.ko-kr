---
title: Azure Kubernetes Service 소개
description: Azure Kubernetes Service를 통해 Azure에서 컨테이너 기반 응용 프로그램을 간단히 배포 및 관리할 수 있습니다.
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4a36809b0f6a041ac4d9250624495aeaf1e397e9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-azure-kubernetes-service-aks-preview"></a>AKS(Azure Kubernetes Service) 미리 보기 소개

AKS(Azure Kubernetes Service)를 사용하면 컨테이너화된 응용 프로그램을 실행하는 미리 구성된 가상 머신의 클러스터를 보다 간편하게 만들고 구성하고 관리할 수 있습니다. 이를 통해 기존 기술을 사용하거나 크고 확장된 커뮤니티 전문 지식의 본문을 이용하여 Microsoft Azure의 컨테이너 기반 응용 프로그램을 배포하고 관리할 수 있습니다.

AKS를 사용하면 Kubernetes 및 Docker 이미지 형식을 통해 응용 프로그램 이식성을 유지하면서 Azure의 엔터프라이즈급 기능을 활용할 수 있습니다.

> [!IMPORTANT]
> AKS(Azure Kubernetes Service)는 현재 **미리 보기**로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="managed-kubernetes-in-azure"></a>Azure의 Managed Kubernetes

AKS는 대부분의 부담을 Azure에 오프로딩하여 Kubernetes 클러스터 관리의 복잡성과 운영 과부하를 줄입니다. 호스팅되는 Kubernetes 서비스인 Azure는 상태 모니터링 및 유지 관리 같은 중요 작업을 처리합니다. 또한 마스터가 아니라 클러스터 내의 에이전트 노드에 대해서만 지불합니다. Managed Kubernetes 서비스 형태인 AKS는 다음을 제공합니다.

> [!div class="checklist"]
> * 자동화된 Kubernetes 버전 업그레이드 및 패치
> * 간편한 클러스터 크기 조정
> * 자체 치료 호스팅된 컨트롤 평면(마스터)
> * 비용 절감 - 실행 중인 에이전트 풀 노드에 대해서만 지불

AKS 클러스터의 노드 관리를 Azure가 처리하므로 클러스터 업그레이드 등의 여러 작업을 수행할 필요가 없어집니다. Azure가 이렇게 중요한 유지 관리를 대신 수행하므로 AKS는 클러스터에 대한 직접 액세스(예: SSH 사용)를 제공하지 않습니다.

## <a name="using-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service) 사용
AKS의 목표는 현재 우리 고객들 사이에서 인기 있는 오픈 소스 도구 및 기술을 사용하여 컨테이너 호스팅 환경을 제공하는 것입니다. 이를 위해 표준 Kubernetes API 끝점을 공개합니다. 이러한 표준 끝점을 사용하면 Kubernetes 클러스터와 통신할 수 있는 모든 소프트웨어를 활용할 수 있습니다. 예를 들어 [kubectl][kubectl-overview], [helm][helm] 또는 [draft][draft]를 선택할 수 있습니다.

## <a name="creating-a-kubernetes-cluster-using-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 사용하여 Kubernetes 클러스터 만들기
AKS를 사용하기 시작하려면 [Azure CLI][aks-quickstart] 또는 포털(**Azure Kubernetes Service**에 대한 Marketplace 검색)을 통해 AKS 클러스터를 배포합니다. Azure Resource Manager 템플릿을 통해 더 많은 제어를 필요로 하는 고급 사용자인 경우 오픈 소스 [acs-engine][acs-engine] 프로젝트를 사용하여 사용자 지정 Kubernetes 클러스터를 빌드하여 `az` CLI를 통해 배포합니다.

### <a name="using-kubernetes"></a>Kubernetes 사용
Kubernetes는 컨테이너화된 응용 프로그램의 배포, 크기 조정 및 관리를 자동화합니다. 여기에는 다음과 같이 풍부한 기능들이 포함되어 있습니다.
* 자동 저장소 적재
* 자동 복구
* 수평적 크기 조정
* 서비스 검색 및 부하 분산
* 자동화된 롤아웃 및 롤백
* 비밀 및 구성 관리
* 저장소 오케스트레이션
* 일괄 처리 실행

## <a name="videos"></a>동영상

AKS(Azure Kubernetes Service) - Azure 2017년 10월 금요일:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Kubernetes에서 응용 프로그램을 개발 및 배포하기 위한 도구 - Azure OpenDev, 2017년 6월:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

AKS 빠른 시작으로 AKS 배포 및 관리에 대해 자세히 알아 봅니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

