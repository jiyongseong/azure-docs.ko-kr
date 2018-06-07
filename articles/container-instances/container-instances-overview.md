---
title: Azure Container Instances 개요
description: Azure Container Instances 이해
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2da61d52e43857237f6e231c03ea62ba5eefdadd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

컨테이너는 클라우드 응용 프로그램을 패키지, 배포 및 관리하기 위한 기본 방법으로 도입되고 있습니다. Azure Container Instances는 어떠한 가상 머신도 관리하지 않고 또 더 높은 수준의 서비스를 채택하지 않고도 Azure에서 컨테이너를 실행하는 가장 빠르고 간단한 방법을 제공합니다.

Azure Container Instances는 간단한 응용 프로그램, 작업 자동화 및 빌드 작업 등 격리된 컨테이너에서 작동할 수 있는 모든 시나리오에 적합한 솔루션입니다. 여러 컨테이너 간 서비스 검색, 자동 크기 조정 및 조정된 응용 프로그램 업그레이드를 포함하여 전체 컨테이너 오케스트레이션이 필요한 시나리오에는 [AKS(Azure Kubernetes Service)](../aks/index.yml)를 사용하는 것이 좋습니다.

## <a name="fast-startup-times"></a>빠른 시작 시간

컨테이너는 가상 머신을 통한 상당한 시작 이점을 제공합니다. Azure Container Instances는 VM을 프로비전 및 관리할 필요 없이 Azure에서 몇 초 안에 컨테이너를 시작할 수 있습니다.

## <a name="public-ip-connectivity-and-dns-name"></a>공용 IP 연결 및 DNS 이름

Azure Container Instances는 IP 주소 및 FQDN(정규화된 도메인 이름)을 사용하여 컨테이너를 인터넷에 직접 노출할 수 있습니다. 컨테이너 인스턴스를 만들 때 사용자 정의 DNS 이름 레이블을 지정하여 *customlabel*.*azureregion*.azurecontainer.io에서 응용 프로그램에 연결할 수 있습니다.

## <a name="hypervisor-level-security"></a>하이퍼바이저 수준 보안

지금까지는 컨테이너가 응용 프로그램 종속성 격리 및 리소스 관리를 제공했지만 적대적인 다중 테넌트 사용을 위해서 충분히 보강되지 않았습니다. Azure Container Instances는 응용 프로그램이 VM에서 격리되는 것처럼 컨테이너에서도 격리되도록 보장합니다.

## <a name="custom-sizes"></a>사용자 지정 크기

일반적으로 컨테이너는 단일 응용 프로그램만 실행하도록 최적화되었지만 이러한 응용 프로그램의 정확한 요구 사항은 크게 다를 수 있습니다. Azure Container Instances는 CPU 코어 및 메모리의 정확한 사양을 허용하여 최적의 활용도를 제공합니다. 필요한 만큼 비용을 초 단위로 지불하므로 실제로 필요한 양에 따라 지출을 미세하게 조정할 수 있습니다.

## <a name="persistent-storage"></a>영구 저장소

Azure Container Instances를 통해 상태를 검색하고 유지하려면 [Azure Files 공유 탑재](container-instances-mounting-azure-files-volume.md)를 직접 제공합니다.

## <a name="linux-and-windows-containers"></a>Linux 및 Windows 컨테이너

Azure Container Instances는 동일한 API로 Windows 및 Linux 컨테이너를 모두 예약할 수 있습니다. [컨테이너 그룹](container-instances-container-groups.md)을 만들 때는 OS 종류만 지정합니다.

일부 기능은 현재 Linux 컨테이너에 제한됩니다. 저희는 Windows 컨테이너에 모든 기능을 제공하기 위해 노력 중이며, [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 확인할 수 있습니다.

## <a name="co-scheduled-groups"></a>공동 예약된 그룹

Azure Container Instances는 호스트 컴퓨터, 로컬 네트워크, 저장소 및 수명 주기를 공유하는 [다중 컨테이너 그룹](container-instances-container-groups.md)의 예약을 지원합니다. 이렇게 하면 주 응용 프로그램 컨테이너를 로깅 사이드카 같은 다른 지원 역할 컨테이너와 결합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

빠른 시작 가이드를 사용하여 단일 명령으로 Azure에 컨테이너를 배포해 보세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 빠른 시작](container-instances-quickstart.md)
