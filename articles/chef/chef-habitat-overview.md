---
title: Habitat 사용하여 Azure에 응용 프로그램 배포
description: 응용 프로그램을 Azure 가상 머신 및 컨테이너에 일관되게 배포하는 방법을 알아봅니다
keywords: azure, chef, devops, 가상 머신, 개요, 자동화, habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267204"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Habitat 사용하여 Azure에 응용 프로그램 배포
[Habitat](https://www.habitat.sh/)은 응용 프로그램 관리에 전혀 새로운 접근 방식을 제공하는 이런 종류로 최초인 오픈 소스 프로젝트입니다. Habitat은 응용 프로그램을 만들고 배포 단위를 자동화합니다. 응용 프로그램이 경량 "habitat"으로 래핑된 경우 런타임 환경은 컨테이너, 완전 복구 또는 PaaS인지 여부에 따라 더 이상 핵심이 아니며 응용 프로그램을 제한하지 않습니다. 

이 문서에서는 Habitat을 사용하는 혜택을 설명합니다.

## <a name="support-for-the-modern-application"></a>최신 응용 프로그램에 대한 지원
Habitat 패키지에는 응용 프로그램이 수명 주기 전체에서 실행해야 하는 모든 것이 포함됩니다. Habitat의 핵심 구성 요소는 다음과 같습니다.
- 패키지 형식 - Habitat 패키지의 응용 프로그램은 원자성이고 변경 불가능하며 감사 가능합니다.
- Habitat 감독자는 패키지의 피어 관계, 업그레이드 전략 및 보안 정책을 인식하는 응용 프로그램 패키지를 실행합니다. Habitat 감독자는 존재하는 모든 환경에 대해 응용 프로그램을 구성하고 관리합니다.
- Habitat 생태계는 Habitat 빌드 계획을 마련하고 Habitat 패키지를 만들고 디포에 게시하는 빌드 서비스를 제공합니다.

## <a name="run-any-application-anywhere"></a>모든 위치에서 모든 응용 프로그램 실행
Habitat을 사용하여 응용 프로그램은 모든 런타임 환경에서 수정되지 않고 실행할 수 있습니다. 완전 복구 및 가상 머신부터 컨테이너(예: Docker), 클러스터 관리 시스템(예: Mesosphere 또는 Kubernetes) 및 PaaS 시스템(예: Pivotal Cloud Foundry)에 이르는 모든 것이 포함됩니다.

## <a name="easily-port-legacy-applications"></a>쉽게 레거시 응용 프로그램 복사
레거시 응용 프로그램이 Habitat 패키지로 래핑될 경우 응용 프로그램은 원래 설계된 목적의 환경에서 독립적입니다. 패키지는 클라우드 또는 컨테이너 같은 최신 환경으로 신속하게 이동할 수 있습니다. 또한 Habitat 패키지에는 외부 연결 표준 인터페이스가 있기 때문에 레거시 응용 프로그램은 관리가 훨씬 쉬어집니다.

## <a name="improve-the-container-experience"></a>컨테이너 환경 개선
Habitat은 프로덕션 환경에서 컨테이터 관리의 복잡성을 줄여줍니다. 컨테이너 내에서 응용 프로그램 구성을 자동화하여 Habitat은 컨테이너 기반 응용 프로그램을 개발 환경에서 프로덕션으로 이동할 때 개발자가 직면하는 어려움을 처리합니다.

## <a name="integrate-into-the-chef-devops-workflow"></a>Chef DevOps 워크플로로 통합
Habitat 프로젝트는 Chef에서 후원합니다. Habitat은 응용 프로그램에 뛰어난 자동화 기능을 갖추려면 인프라 자동화로 Chef의 심층 경험을 활용합니다. 개발에서 배포까지의 응용 프로그램 릴리스 주기를 완전히 자동화하려면 Chef는 Habitat에 상업적 지원을 제공하고 Habitat 및 Chef 배달 사이에 원활한 통합을 확인합니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 Chef를 사용하여 Windows 가상 머신 만들기](/azure/virtual-machines/windows/chef-automation)