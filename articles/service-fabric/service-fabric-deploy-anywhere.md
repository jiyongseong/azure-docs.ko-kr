---
title: Windows Server 및 Linux에서 Azure Service Fabric 클러스터 만들기 | Microsoft Docs
description: 서비스 패브릭 클러스터는 Windows Server 및 Linux에서 실행됩니다. 즉, Windows Server 또는 Linux를 실행할 수 있는 모든 위치에 서비스 패브릭 응용 프로그램을 배포 및 호스트할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Windows Server 또는 Linux에서 Service Fabric 클러스터 만들기
Azure Serivce Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 일부인 컴퓨터나 VM을 클러스터 노드라고 합니다. 클러스터의 규모를 수천 개의 노드로 확장할 수 있습니다. 새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 서비스 파티션 복제본 및 인스턴스의 균형을 조정합니다. 전반적인 응용 프로그램 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본 및 인스턴스의 균형을 다시 조정합니다.

Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다. 다시 말해 온-프레미스든 Microsoft Azure든 클라우드 공급자든 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 Service Fabric 응용 프로그램을 배포하고 실행할 수 있습니다.

## <a name="create-service-fabric-clusters-on-azure"></a>Azure에서 Service Fabric 클러스터 만들기
Azure에서 클러스터 만들기는 리소스 모델 템플릿 또는 [Azure Portal](https://portal.azure.com)을 통해 수행됩니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md) 또는 [Azure Portal에서 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)를 참조하세요.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Azure에서 클러스터에 지원되는 운영 체제
다음 운영 체제를 실행하는 가상 머신에서 클러스터를 만들 수 있습니다.

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Windows Server 1709에 Service Fabric을 배포하기로 결정한 경우 (1) 장기 서비스 분기가 아니므로 나중에 버전을 이동해야 할 수 있으며 (2) 컨테이너를 배포하는 경우 Windows Server 2016에 빌드한 컨테이너는 Windows Server 1709에서 작동하지 않으며 그 반대의 경우도 마찬가지라는(배포하려면 다시 빌드가 필요함) 점에 유의해야 합니다.
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>온-프레미스에서 또는 클라우드 공급자를 사용하여 Service Fabric 독립 실행형 클러스터 만들기
Service Fabric은 온-프레미스 또는 클라우드 공급자에서 독립 실행형 Service Fabric 클러스터를 만들 수 있는 설치 패키지를 제공합니다.

Windows Server에서 독립 실행형 Service Fabric 클러스터 설치에 자세한 내용은 [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)를 참조하세요.

  > [!NOTE]
  > Linux용 독립 실행형 클러스터는 현재 지원되지 않습니다. Linux는 개발 및 Azure Linux 다중 컴퓨터 클러스터에 대해 단일 상자에서 지원됩니다.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>클라우드 배포와 온-프레미스 배포 비교
서비스 패브릭 클러스터 온-프레미스를 만드는 프로세스는 VM 집합이 있는 클라우드를 선택하여 거기에 클러스터를 만드는 프로세스와 비슷합니다. VM을 프로비전하는 초기 단계는 사용하는 클라우드 공급자 또는 온-프레미스 환경에 의해 제어됩니다. VM을 준비하고 VM 간에 네트워크 연결을 설정한 후에는 동일한 방법으로 서비스 패브릭 패키지를 설정하고, 클러스터 설정을 편집하고, 클러스터 만들기 및 관리 스크립트를 실행합니다. 이렇게 하면 새 호스팅 환경을 대상으로 선택할 때 Service Fabric 클러스터 운영 및 관리에 대한 지식과 경험이 전달됩니다.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>독립 실행형 서비스 패브릭 클러스터를 만들 경우의 이점
* 클러스터를 호스트할 클라우드 공급자를 자유롭게 선택할 수 있습니다.
* 서비스 패브릭 응용 프로그램을 한 번 작성한 다음 변경 없이 또는 최소한의 변경으로 여러 호스팅 환경에서 실행할 수 있습니다.
* 호스팅 환경 간에 서비스 패브릭 응용 프로그램 빌드에 대한 지식이 전달됩니다.
* 환경 간에 서비스 패브릭 클러스터 실행 및 관리에 대한 운영 경험이 전달됩니다.
* 호스팅 환경 제약을 받지 않아 고객 도달 범위가 넓습니다.
* 데이터 센터 또는 클라우드에 블랙아웃이 발생하면 서비스를 다른 배포 환경으로 이동할 수 있으므로 안정성이 강화되고 대규모로 가동이 중단되는 일이 없습니다.

## <a name="supported-operating-systems-for-standalone-clusters"></a>독립 실행형 클러스터에 지원되는 운영 체제
다음 운영 체제를 실행하는 컴퓨터 또는 VM에서 클러스터를 만들 수 있습니다(Linux는 아직 지원되지 않음).

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>온-프레미스에서 만든 독립 실행형 서비스 패브릭 클러스터와 비교한 Azure 서비스 패브릭 클러스터의 장점
Azure에서 서비스 패브릭 클러스터를 실행하면 온-프레미스 옵션을 사용할 때보다 몇 가지 이점이 더 있습니다. 따라서 클러스터 실행 위치가 크게 중요하지 않은 경우 Azure에서 클러스터를 실행하는 것이 좋습니다. Azure에서는 클러스터의 작업 및 관리를 더 용이하고 안정적으로 해주는 다음과 같은 다른 Azure 기능 및 서비스와의 통합을 제공합니다.

* **Azure 포털:** Azure 포털을 사용하면 쉽게 클러스터를 만들고 관리할 수 있습니다.
* **Azure 리소스 관리자:** Azure 리소스 관리자를 사용하면 클러스터에서 사용하는 모든 리소스를 하나의 단위로 쉽게 관리할 수 있기 때문에 비용 추적 및 청구가 간소화됩니다.
* **Azure 리소스로서의 Service Fabric 클러스터** Service Fabric 클러스터는 Azure 리소스이므로 Azure의 다른 리소스와 같이 모델링할 수 있습니다.
* **Azure 인프라와의 통합** 서비스 패브릭은 OS, 네트워크 및 그 밖에 응용 프로그램의 가용성 및 안정성을 개선하기 위한 업그레이드에 맞게 기본 Azure 인프라를 조정합니다.  
* **진단:** Azure에서는 Azure 진단과 Log Analytics가 통합됩니다.
* **자동 크기 조정:** Azure의 클러스터는 Virtual Machine 크기 조정 집합으로 인해 자동 크기 조정 기능이 기본적으로 제공됩니다. 온-프레미스 및 다른 클라우드 환경에서는 사용자 고유 자동 크기 조정 기능을 빌드하거나 서비스 패브릭에서 클러스터 크기 조정을 위해 제공하는 API를 사용하여 수동으로 조정해야 합니다.

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

