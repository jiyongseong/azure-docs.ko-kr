---
title: Azure DevOps Project 개요 | Microsoft Docs
description: Azure DevOps Project의 가치 이해
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: b87cb14fc707d14638c2d6a52af6f295276a2279
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-azure-devops-project"></a>Azure DevOps Project 개요

Azure DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. DevOps 프로젝트 리소스를 사용하면 Azure Portal에서 단 몇 단계를 거쳐 빠르게 원하는 Azure 서비스에 좋아하는 앱 유형을 시작할 수 있습니다. DevOps Project는 응용 프로그램 개발, 배포 및 모니터링에 필요한 모든 것을 설정합니다.
DevOps Project 대시 보드를 사용하면 Azure Portal에서 한 번에 코드 커밋, 빌드 및 배포를 모니터링 할 수 있습니다.

## <a name="why-should-i-use-the-azure-devops-project"></a>Azure DevOps Project를 사용해야 하는 이유는 무엇인가요?

Azure DevOps Project는 CI(지속적인 통합) 및 CD(지속적인 업데이트) 파이프라인을 Azure로 자동 설정합니다.  기존 코드로 시작하거나 제공된 샘플 응용 프로그램 중 하나를 사용하여 해당 응용 프로그램을 Virtual Machines, App Service, Azure Container Service, Azure SQL Database, Azure Service Fabric과 같은 다양한 Azure 서비스에 신속하게 배포할 수 있습니다.  

Azure DevOps 프로젝트는 Azure Portal에 Azure DevOps Project 대시보드 만들어 초기 Git 리포지토리 설정, CI/CD 파이프라인 구성, 모니터링을 위해 Application Insights 리소스 만들기 및 전체 솔루션의 단일 보기 제공 등 DevOps 파이프라인의 초기 구성을 위해 모든 작업을 수행합니다.

Azure DevOps Project를 사용하여 다음을 수행할 수 있습니다.

* Azure에 응용 프로그램 신속 배포
* VSTS CI/CD 파이프라인의 설정 자동화
* DevOps 프로젝트를 템플릿으로 사용하여 VSTS로 Azure에 CI/CD를 올바르게 설정하는 방법을 보고 이해합니다.
* Azure에 CI/CD 파이프라인 시작한 다음 특정 시나리오에 따라 릴리스 파이프라인을 추가로 사용자 지정

## <a name="how-do-i-use-the-azure-devops-project"></a>Azure DevOps Project를 사용하려면 어떻게 해야 합니까?

Azure DevOps Project는 Azure Portal에서 구할 수 있습니다.  포털에서 다른 Azure 리소스와 마찬가지로 Azure DevOps 프로젝트 리소스를 만듭니다.  DevOps 프로젝트는 다양한 구성 옵션에 대해 마법사와 같은 단계별 환경을 제공합니다.  

초기 설정의 일부로 여러 구성 옵션을 선택합니다.  이러한 옵션에는 다음이 포함됩니다.

* 제공된 샘플 앱을 사용하거나 자신의 코드를 가져옵니다.
* 앱 언어 선택
* 언어를 기반으로 App 프레임워크 선택
* Azure 서비스 선택 (배포 대상)
* VSTS 계정 (신규 또는 기존)
* Azure 구독 선택
* Azure 서비스 위치 선택
* Azure 서비스에 대한 다양한 가격 책정 계층에서 선택

Azure DevOps Project를 사용하여 Azure Portal의 Azure DevOps Project 대시보드에서 모든 리소스를 한 곳에서 삭제할 수 있습니다.

## <a name="azure-devops-project-and-vsts-integration"></a>Azure DevOps 프로젝트 및 VSTS 통합

DevOps 프로젝트는 VSTS를 기반으로 합니다.  DevOps 프로젝트는 VSTS에서 CI/CD를 Azure에 설정하는 데 필요한 모든 작업을 자동화합니다.  Git 리포지토리는 새 VSTS 계정이나 기존 VSTS 계정에 만들어집니다.  DevOps 프로젝트는 샘플 응용 프로그램이나 기존 코드를 새로운 Git 리포지토리에 커밋합니다.  자동화는 또한 모든 새로운 코드 커밋이 빌드를 시작할 수 있도록 빌드를 위한 CI 트리거를 설정합니다.  또한 DevOps 프로젝트는 CD 트리거를 만들고 원하는 Azure 서비스에 모든 새로운 성공적인 빌드를 배포합니다.  빌드 및 릴리스 정의는 추가 시나리오에 맞게 사용자 지정할 수 있습니다.  또한 다른 프로젝트에서 사용할 수 있도록 빌드 및 릴리스 정의를 복제할 수도 있습니다.

DevOps Project를 만든 후 다음을 수행할 수 있습니다.

* 빌드 및 릴리스 파이프라인 사용자 지정
* 끌어오기 요청을 사용하여 코드 흐름을 관리하고 높은 품질을 유지함
* 코드를 병합하여 품질 표시줄을 높이기 전에 각 커밋 테스트 및 빌드
* 응용 프로그램과 함께 프로젝트 백로그 및 문제점 추적

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Azure DevOps Project를 사용하기 시작하려면 어떻게 해야 합니까?

* [Azure DevOps Project 시작하기](https://docs.microsoft.com/vsts/build-release/actions/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps Project 비디오

* [Azure DevOps Project로 CI/CD 만들기](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
