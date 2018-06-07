---
title: 단일 Azure 지역 내 SAP HANA 가용성 | Microsoft Docs
description: 한 Azure 지역에 있는 Azure 네이티브 VM의 SAP HANA 작업에 대해 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>단일 Azure 지역 내 SAP HANA 가용성
이 문서에서는 한 Azure 지역 내의 여러 가용성 시나리오에 대해 설명합니다. Azure에는 전 세계에 걸쳐 많은 지역이 있습니다. Azure 지역 목록은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. Azure 지역 내의 VM에 SAP HANA를 배포하는 경우 Microsoft는 HANA 인스턴스가 있는 단일 VM의 배포를 제공합니다. 가용성을 높이기 위해 HANA 시스템 복제를 가용성 용도로 사용하는 [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 내에 두 개의 HANA 인스턴스가 있는 두 개의 VM을 배포할 수 있습니다. 

현재 Azure는 [Azure 가용성 영역(미리 보기)](https://docs.microsoft.com/azure/availability-zones/az-overview)에 대한 공개 미리 보기를 제공하고 있습니다. 이 문서에서는 가용성 영역에 대해 자세히 설명하지 않습니다. 그러나 가용성 집합과 가용성 영역을 사용하는 방법에 대한 일반적인 토론은 다음과 같습니다.

Azure 가용성 집합 및 가용성 영역 간의 차이점은 무엇일까요? 가용성 영역이 제공되는 Azure 지역에는 여러 데이터 센터가 있습니다. 데이터 센터는 전원, 냉각 및 네트워크의 공급 장치와 독립적입니다. 단일 Azure 지역 내에 서로 다른 영역을 제공하는 이유는 제공되는 2-3개 가용성 영역에 응용 프로그램을 배포할 수 있기 때문입니다. 전원 또는 네트워크 문제가 하나의 가용성 영역 인프라에만 영향을 준다고 가정하면, 가용성 영역을 사용하는 경우에도 여전히 Azure 지역 내의 응용 프로그램 배포가 완벽하게 작동합니다. 일부 용량이 줄어들 수도 있습니다. 예를 들어 한 영역의 VM이 손실될 수 있지만 다른 두 영역의 VM은 계속 가동되어 실행됩니다. 
 
Azure 가용성 집합은 가용성 집합 내에 배치한 VM 리소스를 Azure 데이터 센터에 배포할 때 서로 간에 오류가 격리되도록 하는 데 도움이 되는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 VM을 여러 물리적 서버, 계산 랙, 저장 장치 및 네트워크 스위치에서 실행되도록 합니다. 일부 Azure 문서에서 이 구성은 다른 [업데이트 및 장애 도메인](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)의 배치라고 합니다. 이러한 배치는 일반적으로 Azure 데이터 센터 내에 있습니다. 전원 및 네트워크 문제가 배포하는 데이터 센터에 영향을 준다고 가정하면, 한 Azure 지역의 모든 용량에 영향을 줍니다.

Azure 가용성 영역을 나타내는 데이터 센터의 배치는 대부분의 응용 프로그램에 허용되는 여러 영역에 배포된 서비스 간 배달 네트워크 대기 시간과 데이터 센터 간 특정 거리 사이의 절충안입니다. 이상적으로 자연 재해는 이 지역의 모든 가용성 영역에 대한 전원, 네트워크 공급 및 인프라에 영향을 주지 않습니다. 그러나 엄청난 자연 재해가 발생하면 가용성 영역이 한 지역 내에서 필요한 가용성을 제공하지 못할 수도 있습니다. 2017년 9월 20일 푸에르토리코 섬에 발생한 Maria 허리케인에 대해 생각해 보세요. 기본적으로 이 허리케인으로 인해 90마일 너비의 섬에서 거의 100%에 이르는 정전이 발생했습니다.

## <a name="single-vm-scenario"></a>단일 VM 시나리오

단일 VM 시나리오에서는 SAP HANA 인스턴스에 대한 Azure VM을 만듭니다. Azure Premium Storage를 사용하여 운영 체제 및 모든 데이터 디스크를 호스팅합니다. 99.9%의 Azure 가동 시간 SLA와 다른 Azure 구성 요소의 SLA는 고객을 위한 가용성 SLA를 충족시키는 데 충분합니다. 이 시나리오에서는 DBMS 계층을 실행하는 VM에 대해 Azure 가용성 집합을 활용할 필요가 없습니다. 여기서는 서로 다른 두 가지 기능을 사용합니다.

- Azure VM 자동 다시 시작(Azure 서비스 복구라고도 함)
- SAP HANA 자동 다시 시작

Azure VM 자동 다시 시작 또는 서비스 복구는 다음 두 수준에서 작동하는 Azure 기능입니다.

- Azure 서버 호스트에서 호스팅되는 VM의 상태를 확인합니다.
- Azure 패브릭 컨트롤러에서 서버 호스트의 상태 및 가용성을 모니터링합니다.

상태 검사 기능은 Azure 서버 호스트에서 호스팅되는 모든 VM의 상태를 모니터링합니다. VM이 비정상 상태에 놓이게 되면 VM 상태를 확인하는 Azure 호스트 에이전트에서 VM 다시 부팅을 시작할 수 있습니다. 패브릭 컨트롤러는 호스트 하드웨어와 관련된 문제를 나타낼 수 있는 여러 다른 매개 변수를 검사하여 호스트의 상태를 확인합니다. 또한 네트워크를 통해 호스트의 액세스 가능성도 확인합니다. 호스트 관련 문제가 표시되면 다음과 같은 이벤트가 발생할 수 있습니다.

- 호스트에서 잘못된 상태에 대한 신호를 보내는 경우 호스트를 다시 부팅하고 호스트에서 실행 중이었던 VM을 다시 시작합니다.
- 호스트가 다시 부팅한 후에도 정상 상태가 아닌 경우 원래 해당 호스트에서 호스팅된 VM을 정상 호스트에서 다시 시작합니다. 이 경우 해당 호스트는 비정상으로 표시됩니다. 이 호스트는 삭제하거나 대체할 때까지 추가 배포에 사용되지 않습니다.
- 다시 부팅 프로세스 중에 비정상 호스트에 문제가 발생하는 경우 정상 호스트에서 VM을 즉시 다시 시작합니다. 

Azure에서 제공하는 호스트 및 VM 모니터링을 사용하면 정상 Azure 호스트에서 호스트 문제가 발생한 Azure VM을 자동으로 다시 시작합니다. 

이 시나리오에서 사용하는 두 번째 기능은 VM을 다시 부팅한 후 다시 시작한 VM에서 실행되는 HANA 서비스가 자동으로 시작된다는 것입니다. [HANA 서비스 자동 다시 시작](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)은 다양한 HANA 서비스의 Watchdog 서비스를 통해 설정할 수 있습니다.

이 단일 VM 시나리오는 SAP HANA 구성에 콜드 장애 조치 노드를 추가하여 향상시킬 수 있습니다. SAP HANA 설명서에서 이 설정은 [호스트 자동 장애 조치](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)라고 합니다. 이 구성은 서버 하드웨어가 제한되어 있고 프로덕션 호스트 집합에 대한 단일 서버 노드를 호스트 자동 장애 조치 노드로 전용하는 온-프레미스 배포 상황에서 의미가 있습니다. 그러나 Azure의 기본 인프라에서 성공적인 VM 다시 시작을 위해 정상 대상 서버를 제공하는 Azure에서는 SAP HANA 호스트 자동 장애 조치를 배포할 필요가 없습니다. 이에 따라 HANA 호스트 자동 장애 조치를 위한 대기 노드를 예상하는 참조 아키텍처가 없습니다. 이는 SAP HANA 확장 집합 구성에도 적용됩니다.

## <a name="availability-scenarios-for-two-different-vms"></a>두 개의 서로 다른 VM에 대한 가용성 시나리오

한 Azure 가용성 집합 내에 두 개의 Azure VM을 사용하는 경우, 이러한 두 VM이 한 Azure 지역 내의 한 Azure 가용성 집합에 배치되면 해당 두 VM 간의 가동 시간을 늘릴 수 있습니다. Azure의 기본 설정은 다음과 같습니다.

![모든 계층이 포함된 두 VM의 다이어그램](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

다양한 가용성 시나리오를 설명하기 위해 다이어그램의 일부 계층이 생략되었습니다. 다이어그램에는 VM, 호스트, 가용성 집합 및 Azure 지역을 나타내는 계층만 표시됩니다. Azure Virtual Network 인스턴스, 리소스 그룹 및 구독은 이 섹션에서 설명하는 시나리오에서 역할을 수행하지 않습니다.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>두 번째 가상 머신에 백업 복제

가장 기본적인 설정 중 하나는 백업을 사용하는 것입니다. 특히 한 VM에서 다른 Azure VM으로 트랜잭션 로그 백업을 제공해야 할 수도 있습니다. Azure Storage 유형을 선택할 수 있습니다. 이 설정에서는 첫 번째 VM에서 수행된 예약된 백업 복사본을 두 번째 VM에 스크립팅해야 합니다. 두 번째 VM의 인스턴스를 사용해야 하는 경우 전체, 증분/차등 및 트랜잭션 로그 백업을 필요한 시점으로 복원해야 합니다. 

아키텍처는 다음과 같습니다.

![저장소 복제가 있는 두 VM의 다이어그램](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

이 설정은 중요한 RPO(복구 지점 목표) 및 RTO(복구 시간목표) 시간을 달성하는 데 적합하지 않습니다. 특히 RTO 시간은 복사된 백업을 사용하여 전체 데이터베이스를 완전히 복원해야 하므로 어려움을 겪습니다. 그러나 이 설정은 주 인스턴스에서 의도하지 않은 데이터 삭제를 복구하는 데 유용합니다. 이 설정을 사용하면 언제든지 특정 시점으로 복원하고, 데이터를 추출하며, 삭제된 데이터를 주 인스턴스로 가져올 수 있습니다. 따라서 다른 고가용성 기능과 함께 백업 복사본 방법을 사용해야 합니다. 

백업이 복사되는 동안 SAP HANA 인스턴스가 실행되는 주 VM보다 더 작은 VM을 사용할 수 있습니다. 적은 수의 VHD를 더 작은 VM에 연결할 수 있다는 점에 유의해야 합니다. 개별 VM 유형의 제한에 대한 자세한 내용은 [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)를 참조하세요.

### <a name="sap-hana-system-replication-without-automatic-failover"></a>자동 장애 조치가 없는 SAP HANA 시스템 복제

이 섹션에서 설명하는 시나리오에서는 SAP HANA 시스템 복제를 사용합니다. SAP 설명서는 [시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)를 참조하세요. 한 Azure 지역에 있는 두 개의 Azure VM은 서로 다르게 구성되므로 RTO에는 약간의 차이가 있습니다. 일반적으로 자동 장애 조치가 없는 시나리오는 한 Azure 지역의 VM에 특별히 적용되지 않을 수 있습니다. 이는 Azure 인프라에서 대부분의 오류가 발생하면 Azure 서비스 복구에서 다른 호스트의 주 VM을 다시 시작하기 때문입니다. 이 구성이 오류 시나리오와 관련하여 도움이 될 수 있는 몇 가지 중요한 경우가 있습니다. 또는 경우에 따라 고객이 더 많은 효율성을 실현할 수도 있습니다.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>자동 장애 조치 및 데이터 미리 로드가 없는 SAP HANA 시스템 복제

이 시나리오에서는 SAP HANA 시스템 복제를 사용하여 RPO가 0이 되도록 데이터를 동기 방식으로 이동합니다. 반면에, HANA 인스턴스 캐시에 장애 조치 또는 데이터 미리 로드가 필요하지 않을 정도로 충분히 긴 RTO가 있습니다. 이 경우 다음 작업을 수행하여 구성에서 경제적 효과를 추가로 달성할 수 있습니다.

- 두 번째 VM에서 다른 SAP HANA 인스턴스를 실행합니다. 두 번째 VM의 SAP HANA 인스턴스에서 가상 머신 메모리의 대부분을 사용합니다. 일반적으로 이는 두 번째 VM에 대한 장애 조치가 발생하는 경우입니다. 두 번째 VM을 종료하여 두 번째 VM에서 대상으로 지정된 HANA 인스턴스의 캐시에 복제된 데이터를 로드할 수 있습니다.
- 두 번째 VM에서 더 작은 VM 크기를 사용합니다. 장애 조치가 발생하면 수동 장애 조치 전에 추가 단계를 수행해야 합니다. 이 단계에서는 VM을 원본 VM의 크기로 조정합니다. 시나리오는 다음과 같습니다.

![저장소 복제가 있는 두 VM의 다이어그램](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> HANA 시스템 복제 대상에서 데이터 미리 로드를 사용하지 않는 경우에도 64GB 이상의 메모리가 필요합니다. 또한 64GB 외에도, rowstore 데이터를 대상 인스턴스의 메모리에 유지하기 위해 충분한 메모리가 필요합니다.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>자동 장애 조치는 없지만 데이터 미리 로드가 있는 SAP HANA 시스템 복제

이 시나리오에서는 두 번째 VM의 HANA 인스턴스에 복제된 데이터가 미리 로드됩니다. 이렇게 하면 데이터를 미리 로드하지 않는 두 가지 장점이 없어집니다. 이 경우 두 번째 VM에서 다른 SAP HANA 시스템을 실행할 수 없습니다. 또한 더 작은 VM 크기를 사용할 수 없습니다. 따라서 고객은 이 시나리오를 거의 구현하지 않습니다.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>자동 장애 조치가 있는 SAP HANA 시스템 복제

한 Azure 지역 내의 표준 및 가장 일반적인 가용성 구성에서는 SLES Linux를 실행하는 두 개의 Azure VM에 장애 조치 클러스터가 정의되어 있습니다. SLES Linux 클러스터는 [STONITH](http://linux-ha.org/wiki/STONITH) 장치와 함께 [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) 프레임워크를 기반으로 합니다. 

SAP HANA 측면에서 볼 때, 사용되는 복제 모드가 동기화되고 자동 장애 조치가 구성됩니다. 두 번째 VM에서 SAP HANA 인스턴스는 핫 대기 노드로 작동합니다. 대기 노드는 주 SAP HANA 인스턴스에서 변경 레코드의 동기 스트림을 받습니다. 트랜잭션이 HANA 주 노드에서 응용 프로그램을 통해 커밋되면, 보조 SAP HANA 노드에서 커밋된 레코드를 받았음을 확인할 때까지 주 HANA 노드는 응용 프로그램에 대한 커밋 확인을 기다립니다. SAP HANA는 두 개의 동기 복제 모드를 제공합니다. 이러한 두 동기 복제 모드에 대한 자세한 내용 및 차이점에 대한 설명은 [SAP HANA 시스템 복제에 대한 복제 모드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) 문서를 읽어보세요.

전체 구성은 다음과 같습니다.

![저장소 복제 및 장애 조치가 있는 두 VM의 다이어그램](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

RPO=0 및 매우 낮은 RTO 시간을 달성할 수 있기 때문에 이 솔루션은 선택할 수 있습니다. SAP HANA 클라이언트에서 가상 IP 주소를 사용하여 HANA 시스템 복제 구성에 연결하도록 SAP HANA 클라이언트 연결을 구성합니다. 이렇게 하면 보조 노드에 장애 조치하는 경우 응용 프로그램을 다시 구성할 필요가 없습니다. 이 시나리오에서는 주 VM 또는 보조 VM에 대한 Azure VM SKU가 동일해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure에서 이러한 설정을 구성하는 단계별 지침은 다음을 참조하세요.

- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [시스템 복제를 사용한 SAP HANA 고가용성](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Azure 지역 간 SAP HANA 가용성에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

