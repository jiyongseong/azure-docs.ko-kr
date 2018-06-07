---
title: Azure의 개인 Docker 컨테이너 레지스트리
description: 클라우드 기반의 관리되는 개인 Docker 레지스트리를 제공하는 Azure Container Registry 서비스에 대한 소개입니다.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 883da7d1487922f5cd986a67d7a7bded78ea3759
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure의 개인 Docker 컨테이너 레지스트리 소개

Azure 컨테이너 레지스트리는 오픈 소스 Docker 레지스트리 2.0에 기반한 관리되는 [Docker 레지스트리](https://docs.docker.com/registry/) 서비스입니다. 개인 [Docker 컨테이너](https://www.docker.com/what-docker) 이미지를 저장하고 관리하기 위해 Azure 컨테이너 레지스트리를 만들고 유지 관리합니다.

기존 컨테이너 배포 및 배포 파이프라인을 통해 Azure에서 컨테이너 레지스트리를 사용합니다. ACR Build(Azure Container Registry Build)를 사용하여 Azure에서 컨테이너 이미지를 빌드합니다. 요청에 따라 빌드하거나 소스 코드 커밋 및 기본 이미지 업데이트 빌드 트리거를 사용하여 빌드를 완전히 자동화합니다.

Docker 및 컨테이너에 대한 배경 지식은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

## <a name="use-cases"></a>사용 사례

Azure Container Registry에서 다양한 배포 대상으로 이미지 끌어오기:

* **확장성 있는 오케스트레이션 시스템**: [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) 및 [Kubernetes](http://kubernetes.io/docs/)를 포함하는 호스트 클러스터에서 컨테이너화된 응용 프로그램을 관리합니다.
* [AKS(Azure Kubernetes Service)](../aks/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) 및 기타 서비스를 포함한 응용 프로그램을 대규모로 빌드하고 실행할 수 있도록 지원하는 **Azure 서비스**.

개발자는 컨테이너 개발 워크플로의 일환으로 컨테이너 레지스트리에 밀어넣을 수도 있습니다. 예를 들어 [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) 또는 [Jenkins](https://jenkins.io/)와 같은 배포 도구 및 지속적인 통합의 컨테이너 레지스트리를 대상으로 합니다.

기본 이미지가 업데이트되면 자동으로 응용 프로그램 이미지를 다시 빌드하도록 [ACR 빌드](#azure-container-registry-build) 작업을 구성합니다. 팀에서 Git 리포지토리에 코드를 커밋할 때 ACR Build를 사용하여 이미지를 자동으로 빌드합니다. *ACR Build는 현재 미리 보기로 제공됩니다.*

## <a name="key-concepts"></a>주요 개념

* **레지스트리** - Azure 구독 내에서 하나 이상의 컨테이너 레지스트리를 만듭니다. 레지스트리는 세 개의 SKU, [기본, 표준 및 프리미엄](container-registry-skus.md)에서 사용할 수 있으며 각각은 webhook 통합, Azure Active Directory와 리포지토리 인증 및 삭제 기능을 지원합니다. 컨테이너 이미지의 네트워크에 가까운 로컬 저장소의 장점을 활용하려면 배포와 동일한 Azure 위치에 레지스트리를 만듭니다. 고급 복제 및 컨테이너 이미지 배포 시나리오에 대해 프리미엄 등록의 [지역에서 복제](container-registry-geo-replication.md) 기능을 사용합니다. 정규화된 레지스트리 이름의 형식은 `myregistry.azurecr.io`입니다.

  컨테이너 레지스트리에 대한 [액세스 제어](container-registry-authentication.md)는 Azure Active Directory에서 지원하는 [서비스주체](../active-directory/active-directory-application-objects.md) 또는 제공된 관리자 계정을 사용하여 수행합니다. 레지스트리로 인증하려면 표준 `docker login` 명령을 실행합니다.

* **리포지토리** - 레지스트리는 컨테이너 이미지 그룹인 하나 이상의 리포지토리를 포함합니다. Azure Container Registry는 다단계 리포지토리 네임스페이스를 지원합니다. 다단계 네임스페이스를 통해 특정 앱과 관련된 이미지 컬렉션 또는 특정 배포 또는 작업 팀에 대한 앱 컬렉션을 그룹화할 수 있습니다. 예: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1`은 회사 차원의 이미지를 나타냅니다.
  * `myregistry.azurecr.io/warrantydept/dotnet-build`는 보증 부서 차원에서 공유되는 .NET 앱을 빌드하는 데 사용되는 이미지를 나타냅니다.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web`은 보증 부서에서 소유한 고객 제출 앱에 그룹화된 웹 이미지를 나타냅니다.

* **이미지** - 리포지토리에 저장되며 각 이미지는 Docker 컨테이너의 읽기 전용 스냅숏입니다. Azure 컨테이너 레지스트리는 Windows 및 Linux 이미지 모두를 포함할 수 있습니다. 모든 컨테이너 배포에 대한 이미지 이름을 제어합니다. 표준 [Docker 명령](https://docs.docker.com/engine/reference/commandline/)을 사용하여 이미지를 리포지토리로 밀어넣거나 이미지를 리포지토리에서 끌어옵니다.

* **컨테이너** - 컨테이너는 소프트웨어 응용 프로그램을 정의하며 코드, 런타임, 시스템 도구 및 라이브러리를 포함하는 완전한 파일 시스템으로 래핑된 종속성을 정의합니다. 컨테이너 레지스트리에서 끌어온 Windows 또는 Linux 이미지를 기반으로 Docker 컨테이너를 실행합니다. 단일 컴퓨터에서 실행되는 컨테이너는 운영 체제 커널을 공유합니다. Docker 컨테이너는 모든 주요 Linux 배포판, macOS 및 Windows로 완전히 이식이 가능합니다.

## <a name="azure-container-registry-build-preview"></a>Azure Container Registry Build(미리 보기)

[ACR Build(Azure Container Registry Build)](container-registry-build-overview.md)는 Azure에서 간편하고 효율적인 Docker 컨테이너 이미지 빌드를 제공하는 Azure Container Registry 내의 기능 모음입니다. ACR Build를 사용하여 `docker build` 작업을 Azure로 오프로딩하면 개발 내부 루프를 클라우드로 확장할 수 있습니다. 컨테이너 OS 및 프레임워크 패치 파이프라인을 자동화하고, 팀에서 코드를 소스 제어로 커밋하면 자동으로 이미지를 빌드하도록 빌드 작업을 구성하세요.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-portal.md)
* [Azure CLI를 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)
* [ACR Build(미리 보기)를 사용하여 OS 및 프레임워크 패치 자동화](container-registry-build-overview.md)
