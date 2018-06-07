---
title: Jenkins 및 Azure 개요 | Microsoft Docs
description: Azure에서 Jenkins 빌드 및 배포 자동화 서버를 호스트하고 Azure 계산 및 저장소 리소스를 사용하여 CI/CD(지속적인 통합 및 배포) 파이프라인을 확장합니다.
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: ca4a6cb886b0453848dc7b29e15de2063878a65d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-and-jenkins"></a>Azure 및 Jenkins

[Jenkins](https://jenkins.io/)는 소프트웨어 프로젝트에 대해 CI/CD(지속적인 통합 및 배포)를 설정하는 데 사용되는 인기 있는 오픈 소스 자동화 서버입니다. Azure에서 Jenkins 배포를 호스트하거나 Azure 리소스를 사용하여 기존 Jenkins 구성을 확장할 수 있습니다. 응용 프로그램의 CI/CD를 Azure로 간소화하기 위해 Jenkins 플러그 인도 사용할 수 있습니다.

이 문서는 Azure와 Jenkins를 함께 사용하는 것을 소개하며 Jenkins 사용자에게 제공되는 주요 Azure 기능에 대해 자세히 설명합니다. Azure에서 사용자 고유의 Jenkins 서버를 시작하려면 [빠른 시작](install-jenkins-solution-template.md)을 참조하세요.

## <a name="host-your-jenkins-servers-in-azure"></a>Azure에서 Jenkins 서버 호스트

Azure에서 Jenkins를 호스트하여 빌드 자동화를 중앙 집중화하고 소프트웨어 프로젝트 규모가 확장됨에 따라 필요에 따라 배포 규모를 조정합니다. 다음을 사용하여 Azure에서 Jenkins를 배포할 수 있습니다.
 
- Azure Marketplace에서 [Jenkins 솔루션 템플릿](install-jenkins-solution-template.md).
- [Azure 가상 머신](/azure/virtual-machines/linux/overview). VM에서 Jenkins 인스턴스를 만들려면 [자습서](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd)를 참조하세요.
- [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)에서 실행 중인 Kubernetes 클러스터에서 [방법](/azure/container-service/kubernetes/container-service-kubernetes-jenkins)을 참조하세요.

[Log Analytics](/azure/log-analytics/log-analytics-overview) 및 [Azure CLI](/cli/azure)를 사용하여 Azure Jenkins 배포를 모니터링 및 관리합니다.

## <a name="scale-your-build-automation-on-demand"></a>요청 시 빌드 자동화 규모 조정

기존 Jenkins 배포에 빌드 에이전트를 추가하여, 빌드 수, 작업 및 파이프라인의 복잡성이 증가함에 따라 Jenkins 빌드 용량을 조정합니다. [Azure VM 에이전트 플러그 인](jenkins-azure-vm-agents.md)을 사용하여 Azure 가상 머신에서 이러한 빌드 에이전트를 실행할 수 있습니다. 자세한 내용은 [자습서](/azure/jenkins/jenkins-azure-vm-agents)를 참조하세요.

[Azure 서비스 주체](/azure/azure-resource-manager/resource-group-overview)로 구성되었으면 다음을 위해 Jenkins 작업 및 파이프라인에서 이 자격 증명을 사용할 수 있습니다.

- [Azure Storage 플러그 인](https://plugins.jenkins.io/windows-azure-storage)을 사용하여 [Azure Storage](/azure/storage/common/storage-introduction)에 빌드 아티팩트를 안전하게 저장하고 보관합니다. 자세히 알아보려면 [Jenkins 저장소 방법](/azure/storage/common/storage-java-jenkins-continuous-integration-solution)을 검토하세요.
- [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline)로 Azure 리소스를 관리하고 구성합니다.

## <a name="deploy-your-code-into-azure-services"></a>Azure 서비스에 코드 배포

Jenkins CI/CD 파이프라인의 일부로 Azure에 응용 프로그램을 배포하려면 Jenkins 플러그 인을 사용합니다. [Azure App Service](/azure/app-service/) 및 [Azure Container Service](/azure/container-service/kubernetes/)에 배포하면 기본 인프라를 관리하지 않고도 응용 프로그램에 업데이트를 준비, 테스트 및 릴리스할 수 있습니다.

 플러그 인은 다음과 같은 서비스 및 환경에 배포하는 데 사용할 수 있습니다.

- [Linux에서 Azure Web App](/azure/app-service/containers/app-service-linux-intro). 시작하려면 [자습서](java-deploy-webapp-tutorial.md)를 참조하세요.
- [Azure Web App](/azure/app-service/app-service-web-overview). 시작하려면 [방법](deploy-Jenkins-app-service-plugin.md)을 참조하세요.

