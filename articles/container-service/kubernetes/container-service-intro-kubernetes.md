---
title: Kubernetes용 Azure Container Service 소개
description: Kubernetes용 Azure Container Service를 통해 Azure에서 컨테이너 기반 응용 프로그램을 간단히 배포 및 관리할 수 있습니다.
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: f12fc0baa055e62d4f15c0e42eb7add3661ea6fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-azure-container-service-for-kubernetes"></a>Kubernetes용 Azure Container Service 소개

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Kubernetes용 Azure Container Service를 사용하면 컨테이너화된 응용 프로그램을 실행하는 미리 구성된 가상 머신의 클러스터를 보다 간편하게 만들고 구성하고 관리할 수 있습니다. 이를 통해 기존 기술을 사용하거나 크고 확장된 커뮤니티 전문 지식의 본문을 이용하여 Microsoft Azure의 컨테이너 기반 응용 프로그램을 배포하고 관리할 수 있습니다.

Azure Container Service를 사용하면 Kubernetes 및 Docker 이미지 형식을 통해 응용 프로그램 이식성을 유지하면서 Azure의 엔터프라이즈급 기능을 활용할 수 있습니다.

## <a name="using-azure-container-service-for-kubernetes"></a>Kubernetes용 Azure Container Service 사용
Azure 컨테이너 서비스를 사용하는 우리의 목표는 현재 우리 고객들 사이에서 인기 있는 오픈 소스 도구 및 기술을 사용하여 컨테이너 호스팅 환경을 제공하는 것입니다. 이를 위해 표준 Kubernetes API 끝점을 공개합니다. 이러한 표준 끝점을 사용하면 Kubernetes 클러스터와 통신할 수 있는 모든 소프트웨어를 활용할 수 있습니다. 예를 들어 [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) 또는 [draft](https://github.com/Azure/draft)를 선택할 수 있습니다.

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Azure Container Service를 사용하여 Kubernetes 클러스터 만들기
Azure Container Service를 사용하기 시작하려면 [Azure CLI 2.0](container-service-kubernetes-walkthrough.md)을 사용하거나 포털(**Azure Container Service**에 대한 Marketplace 검색)을 통해 Azure Container Service 클러스터를 배포합니다. Azure Resource Manager 템플릿을 통해 더 많은 제어를 필요로 하는 고급 사용자인 경우 오픈 소스 [acs-engine](https://github.com/Azure/acs-engine) 프로젝트를 사용하여 사용자 지정 Kubernetes 클러스터를 빌드하여 `az` CLI를 통해 배포할 수 있습니다.

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

Azure Container Service를 통해 배포된 Kubernetes의 아키텍처 다이어그램:

![Kubernetes를 사용하도록 구성된 Azure Container Service.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>동영상

Azure Container Services의 Kubernetes 지원(Azure Friday, 2017년 1월):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Kubernetes에서 응용 프로그램을 개발 및 배포하기 위한 도구(Azure OpenDev, 2017년 6월):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>다음 단계

[Kubernetes 빠른 시작](container-service-kubernetes-walkthrough.md)을 살펴보고 오늘날 Azure Container Service에 대해 알아봅니다.