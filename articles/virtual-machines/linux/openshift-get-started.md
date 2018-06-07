---
title: Azure의 OpenShift 개요 | Microsoft Docs
description: Azure의 OpenShift 개요입니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: c8e740a66271c88b3abb036867d1760cc9e77607
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944504"
---
# <a name="openshift-in-azure"></a>Azure의 OpenShift

OpenShift는 엔터프라이즈에 Docker 및 Kubernetes를 제공하는 확장 가능한 개방형 컨테이너 응용 프로그램 플랫폼입니다.  

OpenShift는 컨테이너 오케스트레이션 및 관리를 위한 Kubernetes를 포함합니다. 추가로 다음을 지원하는 개발자 및 작업 중심 도구를 제공합니다.

- RAD(신속한 응용 프로그램 개발)
- 간편한 배포 및 확장
- 팀 및 응용 프로그램에 대한 장기 수명 유지 관리

OpenShift의 여러 버전을 사용할 수 있습니다.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

이 아티클에 설명된 4가지 버전 중 2가지 즉, OpenShift Origin 및 OpenShift Container Platform은 고객이 Azure에서 배포할 수 있습니다.

## <a name="openshift-origin"></a>OpenShift Origin

Origin은 커뮤니티가 지원하는 OpenShift의 [오픈 소스](https://www.openshift.org/) 업스트림 프로젝트입니다. Origin은 CentOS 또는 RHEL(Red Hat Enterprise Linux)에 설치할 수 있습니다.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform은 Red Hat이 제공하고 지원하는 엔터프라이즈급 [상용](https://www.openshift.com) 버전입니다. 이 버전을 통해 고객은 OpenShift Container Platform에 필요한 자격을 구매하고 전체 인프라의 설치 및 관리를 담당합니다.

고객이 전체 플랫폼을 “소유”하기 때문에 온-프레미스 데이터 센터나 공용 클라우드(Azure, AWS, Google 등)에 설치할 수 있습니다.

## <a name="openshift-online"></a>OpenShift Online

Online은 Container Platform을 사용하는 Red Hat이 관리하는 *다중 테넌트* OpenShift입니다. Red Hat이 모든 기본 인프라(예: VM, OpenShift 클러스터, 네트워킹, 저장소 등)를 관리합니다. 

이 버전을 통해 고객은 컨테이너를 배포하지만 컨테이너가 실행되는 호스트를 제어할 수 없습니다. Online은 다중 테넌트이므로 컨테이너가 다른 고객의 컨테이너와 동일한 VM 호스트에 함께 배치될 수 있습니다. 비용은 컨테이너당 청구됩니다.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated는 Container Platform을 사용하는 Red Hat이 관리하는 *단일 테넌트* OpenShift입니다. Red Hat이 모든 기본 인프라(VM, OpenShift 클러스터, 네트워킹, 저장소 등)를 관리합니다. 클러스터는 한 고객 전용이며 공용 클라우드(예: AWS, Google, Azure는 2018년 초 출시 예정)에서 실행됩니다. 시작 클러스터에는 연간 $48,000에(선불) 4개의 응용 프로그램 노드가 포함됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 OpenShift에 대한 일반적인 필수 조건 구성](./openshift-prerequisites.md)
- [Azure에서 OpenShift Origin 배포](./openshift-origin.md)
- [Azure에서 OpenShift Container Platform 배포](./openshift-container-platform.md)
- [배포 후 작업](./openshift-post-deployment.md)
- [OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
