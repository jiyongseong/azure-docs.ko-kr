---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구 | Microsoft Docs
description: Azure(큰 인스턴스)에서 SAP HANA의 재해 복구 계획 및 고가용성 계획 수립
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c939e0fb59c7fce2c1c34aca1b77bd0b8cec0c5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure의 SAP HANA 큰 인스턴스 고가용성 및 재해 복구 

>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. reader가 특히 백업, 복원, 고가용성 및 재해 복구의 항목에서 SAP HANA 관리 및 작업을 이해하고 전문 지식을 가졌다고 가정하겠습니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

사용자 환경에서 수행되는 단계와 프로세스를 해당 HANA 버전 및 릴리스로 연습하는 것이 중요합니다. 이 문서에 설명된 일부 프로세스는 이해하기 쉽도록 간소화되었으며 최종 운영 핸드북의 자세한 단계로 사용하면 안 됩니다. 구성에 대한 작업 핸드북을 만들려는 경우 프로세스를 테스트 및 실행하고 특정 구성과 관련된 프로세스를 문서화해야 합니다. 


고가용성 및 DR(재해 복구)은 업무상 중요한 Azure의 SAP HANA(대규모 인스턴스) 서버를 실행하는 경우에 중요한 측면입니다. SAP, 시스템 통합업체 또는 Microsoft와 협의하여 적합한 고가용성 및 재해 복구 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경에 지정된 RPO(복구 지점 목표)와 복구 시간 목표를 고려하는 것도 중요합니다.

Microsoft에서는 HANA 큰 인스턴스를 통해 일부 SAP HANA 고가용성 기능을 지원합니다. 이러한 기능은 다음과 같습니다.

- **저장소 복제:** 다른 Azure 지역의 다른 HANA 큰 인스턴스 스탬프로 모든 데이터를 복제하는 저장소 시스템의 기능입니다. SAP HANA는 이 메서드와 독립적으로 작동합니다. 이 기능은 HANA 대규모 인스턴스에 대해 제공되는 기본 재해 복구 메커니즘입니다.
- **HANA 시스템 복제:** 별도 SAP HANA 시스템에 [SAP HANA에 있는 모든 데이터를 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)합니다. 복구 시간 목표는 정기적으로 데이터 복제를 통해 최소화됩니다. SAP HANA는 비동기 모드, 동기식 메모리 내 모드 및 동기 모드를 지원합니다. 동기 모드는 동일한 데이터 센터 내에 있거나 100km 미만의 거리에 있는 SAP HANA 시스템에만 사용됩니다. HANA 대규모 인스턴스 스탬프의 현재 디자인에서 HANA 시스템 복제는 한 지역 내의 고가용성을 위해서만 사용할 수 있습니다. HANA 시스템 복제에는 다른 Azure 지역으로 재해 복구를 구성하기 위해 타사 역방향 프록시 또는 라우팅 구성 요소가 필요합니다. 
- **자동 장애 조치 호스트:** HANA 시스템 복제의 대안인 SAP HANA의 로컬 오류 복구 솔루션입니다. 마스터 노드를 사용할 수 없게 되면 확장 모드에서 하나 이상의 대기 SAP HANA 노드를 구성하고 SAP HANA를 자동으로 대기 노드에 장애 조치합니다.

Azure의 SAP HANA(대규모 인스턴스)는 세 개의 지역(미국, 오스트레일리아 및 유럽)에 있는 두 개의 Azure 지역에서 제공됩니다. HANA 대규모 인스턴스 스탬프를 호스팅하는 지정학적 영역 내의 두 지역을 별도의 전용 네트워크 회로에 연결합니다. 재해 복구 메서드를 제공하기 위해 저장소 스냅숏을 복제하는 데 이 기능을 사용합니다. 복제는 기본적으로 설정되지 않지만 재해 복구 기능을 주문한 고객에 대해 설정됩니다. 저장소 복제는 HANA 큰 인스턴스에 대한 저장소 스냅숏 사용 여부에 따라 달라집니다. 또한 다른 지리적 영역에 있는 DR 영역으로 Azure 지역을 선택할 수 없습니다. 

다음 표에는 현재 지원되는 고가용성 및 재해 복구 방법과 조합이 제공되어 있습니다.

| HANA 큰 인스턴스에서 지원되는 시나리오 | 고가용성 옵션 | 재해 복구 옵션 | 설명 |
| --- | --- | --- | --- |
| 단일 노드 | 사용할 수 없음. | 전용 DR 설치.<br /> 다목적 DR 설치. | |
| 호스트 자동 장애 조치(failover): N+m<br /> 1+1 포함 | 활성 역할의 대기에서 가능.<br /> HANA에서 역할 전환 제어. | 전용 DR 설치.<br /> 다목적 DR 설치.<br /> 저장소 복제를 사용하여 DR 동기화. | HANA 볼륨 세트가 모든 노드에 연결됨(n+m).<br /> DR 사이트에는 동일한 수의 노드가 있어야 함. |
| HANA 시스템 복제 | 주 또는 보조 설치에서 가능.<br /> 장애 조치(failover)의 경우 보조가 주 역할로 전환.<br /> HANA 시스템 복제 및 OS 제어 장애 조치(Failover). | 전용 DR 설치.<br /> 다목적 DR 설치.<br /> 저장소 복제를 사용하여 DR 동기화.<br /> HANA 시스템 복제를 사용하는 DR은 아직 타사 구성 요소 없이 가능하지 않음. | 별도의 디스크 볼륨 세트가 각 노드에 연결됩니다.<br /> 프로덕션 사이트에 있는 보조 복제본의 디스크 볼륨만 DR 위치로 복제됩니다.<br /> DR 사이트에는 한 세트의 볼륨이 필요합니다. | 

전용 DR 설치는 DR 사이트의 HANA 큰 인스턴스 단위가 다른 워크로드 또는 비프로덕션 시스템을 실행하는 데 사용되지 않는 위치입니다. 이 단위는 수동이며 재해 장애 조치(failover)가 실행되는 경우에만 배포됩니다. 하지만 이 설치는 많은 고객이 선호하는 기본 선택 사항은 아닙니다.

> [!NOTE]
> 오버레이 시나리오로 구성된 [SAP HANA MCOD 배포](https://launchpad.support.sap.com/#/notes/1681092)(한 단위에 여러 개의 HANA 인스턴스가 있음)는 표에 나열된 HA 및 DR 방법으로 작동합니다. 단, Pacemaker 기반의 자동 장애 조치(failover) 클러스터에서 HANA 시스템 복제를 사용하는 경우는 예외입니다. 이 경우 단위당 하나의 HANA 인스턴스만 지원됩니다. [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) 배포에서 둘 이상의 테넌트가 배포된 경우 비저장소 기반 HA 및 DR 메서드만 작동합니다. 하나의 테넌트가 배포된 경우 나열된 모든 방법이 유효합니다.  

다목적 DR 설치는 DR 사이트의 HANA 큰 인스턴스 단위가 비프로덕션 워크로드를 실행하는 곳입니다. 재해가 발생하면 비프로덕션 시스템을 종료하고, 저장소 복제(추가) 볼륨 세트를 탑재한 다음, 프로덕션 HANA 인스턴스를 시작합니다. HANA 대규모 인스턴스 재해 복구 기능을 사용하는 대부분의 고객은 이 구성을 사용합니다. 


다음 SAP 문서에서 SAP HANA 고가용성에 대한 자세한 내용을 참조하세요. 

- [SAP HANA 고가용성 백서](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 관리 가이드](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA 시스템 복제에 대한 SAP HANA Academy 비디오](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 지원 참고 사항 #1999880 – SAP HANA 시스템 복제에 대한 FAQ](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 지원 참고 사항 #2165547 – SAP HANA 시스템 복제 환경 내의 SAP HANA 백업 및 복원](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 지원 참고 사항 #1984882 – 가동 중지 시간이 최소/없는 하드웨어 Exchange에 대한 SAP HANA 시스템 복제 사용](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA 큰 인스턴스를 사용한 재해 복구의 네트워크 고려 사항

HANA 대규모 인스턴스의 재해 복구 기능을 활용하려면 두 개의 Azure 지역에 네트워크 연결을 설계해야 합니다. 기본 Azure 지역의 온-프레미스에서 Azure ExpressRoute 회로 연결이 필요하고 온-프레미스에서 재해 복구 지역에 또 다른 회로 연결이 필요합니다. 이러한 방법은 MSEE(Microsoft Enterprise Edge 라우터) 위치를 포함하는 Azure 지역에 문제가 있는 상황을 다룹니다.

두 번째 방법으로 한 지역의 Azure(대규모 인스턴스)에서 SAP HANA에 연결된 모든 Azure 가상 네트워크를 다른 지역의 HANA 대규모 인스턴스를 연결하는 ExpressRoute 회로에 연결할 수 있습니다. 이러한 *교차 연결*을 사용하면 지역 1의 Azure 가상 네트워크에서 실행되는 서비스는 지역 2의 HANA 대규모 인스턴스 단위에 연결될 수 있으며 반대로도 가능합니다. 이 조치는 Azure와 온-프레미스 위치를 연결하는 MSEE 위치 중 하나가 오프라인 상태가 되는 경우에 문제를 해결합니다.

다음 그림은 재해 복구 사례에 대한 복원력 있는 구성을 보여줍니다.

![재해 복구에 대한 최적의 구성](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>재해 복구에 HANA 대규모 인스턴스 저장소 복제를 사용하는 경우 기타 요구 사항

HANA 대규모 인스턴스를 사용하는 재해 복구 설정에 대한 추가 요구 사항은 다음과 같습니다.

- 사용자의 프로덕션 SKU와 동일한 크기의 Azure의 SAP HANA(대규모 인스턴스) SKU를 주문하고 재해 복구 지역에 배포합니다. 현재 고객 배포에서는 비프로덕션 HANA 인스턴스를 실행하기 위해 이러한 인스턴스가 사용됩니다. 이러한 구성을 *다목적 DR 설치*라고 합니다.   
- 재해 복구 사이트에서 복구하려는 Azure의 SAP HANA(대규모 인스턴스) SKU 각각에 대해 DR 사이트의 추가 저장소를 주문합니다. 추가 저장소를 구입하면 저장소 볼륨을 할당할 수 있습니다. 프로덕션 Azure 지역의 저장소 복제 대상인 볼륨을 재해 복구 Azure 지역에 할당할 수 있습니다.

 

## <a name="backup-and-restore"></a>Backup 및 복원

운영 데이터베이스에서 가장 중요한 측면 중 하나는 치명적인 이벤트로부터 해당 데이터베이스를 보호하는 것입니다. 이러한 이벤트의 원인은 자연 재해에서 단순한 사용자 오류에 이르기까지 다양합니다.

특정 시점(예: 누군가가 중요한 데이터를 삭제하기 전에)으로 복원하는 기능을 사용하여 데이터베이스를 백업하면 중단이 발생하기 전의 상태에 가능한 한 가깝게 복원할 수 있습니다.

최상의 결과를 위해 두 가지 백업을 수행해야 합니다.

- 데이터베이스 백업: 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

응용 프로그램 수준에서 수행되는 전체 데이터베이스 백업 외에도 저장소 스냅숏으로 백업을 수행할 수 있습니다. 저장소 스냅숏은 트랜잭션 로그 백업을 대체하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하거나 이미 커밋된 트랜잭션에서 로그를 비우는 데 중요합니다. 하지만 저장소 스냅숏은 데이터베이스의 롤포워드 이미지를 신속하게 제공하여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 두 가지 백업 및 복원 옵션을 제공합니다.

- DIY(Do It Yourself) 디스크 공간이 충분한지 계산한 후에 다음과 같은 디스크 백업 메서드 중 하나를 사용하여 전체 데이터베이스 및 로그 백업을 수행합니다. HANA 대규모 인스턴스 단위에 연결된 볼륨이나 Azure VM(가상 머신)에 설정된 NFS(네트워크 파일 공유)에 직접 백업할 수 있습니다. 후자의 경우 고객은 Azure에서 Linux VM을 설정하고, VM에 Azure Storage를 연결하고, 해당 VM에 구성된 NFS 서버를 통해 저장소를 공유합니다. HANA 큰 인스턴스 단위에 직접 연결된 볼륨에 대해 백업을 수행하는 경우, (Azure Storage를 기반으로 하는 NFS 공유를 내보내는 Azure VM을 설정한 후에) 백업을 Azure 저장소 계정에 복사해야 합니다. Azure 백업 자격 증명 모음 또는 Azure 콜드 저장소를 사용할 수도 있습니다. 

   또 다른 옵션은 백업을 Azure Storage 계정에 복사한 후에 저장하기 위해 타사 데이터 보호 도구를 사용하는 것입니다. DIY 백업 옵션은 준수 및 감사 목적으로 인해 장기간 저장되어야 하는 데이터에 필요할 수 있습니다. 모든 경우에 백업은 VM 및 Azure Storage를 통해 표시되는 NFS 공유로 복사됩니다.

- 인프라 백업 및 복원 기능 Azure(대규모 인스턴스)의 SAP HANA 기본 인프라가 제공하는 백업 및 복원 기능을 사용할 수도 있습니다. 이 옵션은 백업 및 빠른 복원에 대한 필요를 충족합니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다. 이 섹션에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능에 대한 백업 및 복원의 관계에 대해서도 설명합니다.

>   [!NOTE]
>   HANA 큰 인스턴스의 기본 인프라에서 사용하는 스냅숏 기술은 SAP HANA 스냅숏에 대한 종속성을 갖습니다. 현재, SAP HANA 스냅숏은 SAP HANA 다중 테넌트 데이터베이스 컨테이너의 다중 테넌트와 함께 작동하지 않습니다. 테넌트가 하나만 배포되면 SAP HANA 스냅숏이 작동하여 이 메서드를 사용할 수 있습니다.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 저장소 스냅숏 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 저장소 인프라는 볼륨의 저장소 스냅숏을 지원합니다. 볼륨의 백업 및 복원은 다음 사항을 고려하여 지원됩니다.

- 전체 데이터베이스 백업 대신 저장소 볼륨 스냅숏을 자주 사용합니다.
- /hana/data 및 /hana/shared(/usr/sap 포함) 볼륨을 통해 스냅숏을 트리거하는 경우 SAP HANA 스냅숏이 저장소 스냅숏을 실행하기 전에 스냅숏 기술이 SAP HANA 스냅숏을 시작합니다. 이 SAP HANA 스냅숏은 저장소 스냅숏을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다.
- 저장소 스냅숏이 성공적으로 실행된 후에 SAP HANA 스냅숏이 삭제됩니다.
- 트랜잭션 로그 백업은 자주 수행되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨을 트리거하여 별도의 스냅숏을 생성할 수 있습니다. 이 경우에 HANA 스냅숏을 실행할 필요가 없습니다.
- 데이터베이스를 특정 시점으로 복원해야 하는 경우 Azure Service Management에서 Microsoft Azure 지원(프로덕션 중단의 경우) 또는 SAP HANA를 요청하여 특정 저장소 스냅숏으로 복원합니다. 예를 들어 샌드박스 시스템을 원래 상태로 복원하는 계획이 있습니다.
- 저장소 스냅숏에 포함된 SAP HANA 스냅숏은 저장소 스냅숏을 만든 후에 실행되고 저장된 트랜잭션 로그 백업에 적용하는 오프셋 지점입니다.
- 이러한 트랜잭션 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

다음과 같은 세 가지 볼륨 클래스를 대상으로 지정하여 저장소 스냅숏을 수행할 수 있습니다.

- /hana/data 및 /hana/shared(/usr/sap 포함)에 대해 결합된 스냅숏. 이 스냅숏을 만들려면 저장소 스냅숏에 대한 준비로 SAP HANA 스냅숏을 만들어야 합니다. SAP HANA 스냅숏은 데이터베이스가 저장소 관점에서 일관된 상태에 있도록 합니다. 설치 지점인 복원 프로세스를 위한 것입니다.
- /hana/logbackups에 대한 별도 스냅숏.
- 운영 체제 파티션


### <a name="storage-snapshot-considerations"></a>저장소 스냅숏 고려 사항

>[!NOTE]
>저장소 스냅숏은 HANA 큰 인스턴스 단위에 할당된 저장소 공간을 사용합니다. 저장소 스냅숏을 예약하는 경우 다음과 같은 측면과 유지할 저장소 스냅숏의 수를 고려해야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 저장소 스냅숏의 구체적인 역학은 다음과 같습니다.

- 만들어진 시점에서 특정 저장소 스냅숏은 작은 저장소를 사용합니다.
- 데이터 콘텐츠가 변경되고 저장소 볼륨에서 SAP HANA 데이터 파일의 콘텐츠가 변경되면 스냅숏은 데이터 변경 사항은 물론 원래 블록 콘텐츠도 저장해야 합니다.
- 결과적으로 저장소 스냅숏의 크기가 증가합니다. 스냅숏이 오래 존재할수록 저장소 스냅숏은 커집니다.
- 저장소 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 저장소 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대해 고정된 볼륨 크기로 제공됩니다. 이러한 볼륨의 스냅숏을 수행하면 볼륨 공간이 줄어듭니다. 저장소 스냅숏을 예약할 시기를 결정해야 합니다. 또한 저장소 볼륨의 공간 소비를 모니터링하고 저장하는 스냅숏의 수를 관리해야 합니다. 대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행할 때 저장소 스냅숏을 사용하지 않도록 설정할 수 있습니다. 


다음 섹션에서는 일반적인 권장 사항을 비롯한 이러한 스냅숏을 수행하는 방법에 대한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅숏을 유지할 수 있지만 이 숫자보다 훨씬 낮게 유지하는 것이 좋습니다.
- 스냅숏 저장소를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 저장소 스냅숏의 수를 줄입니다. 보관할 스냅숏의 수를 줄이거나 볼륨을 확장할 수 있습니다. 추가 저장소는 1테라바이트 단위로 주문할 수 있습니다.
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에는 /hana/data 볼륨에 대해 저장소 스냅숏을 사용하지 않도록 설정합니다. 
- SAP HANA 테이블을 크게 재구성하는 동안 저장소 스냅숏을 가능하면 사용하지 않아야 합니다.
- 저장소 스냅숏은 Azure(대규모 인스턴스)에서 SAP HANA의 재해 복구 기능을 활용하기 위한 필수 구성 요소입니다.

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>셀프 서비스 저장소 스냅숏을 사용하기 위한 필수 구성 요소

스냅숏 스크립트가 성공적으로 실행되도록 하려면 Perl이 HANA 대규모 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다. Perl은 HANA 큰 인스턴스 단위에 미리 설치되어 제공됩니다. Perl 버전을 확인하려면 다음 명령을 사용합니다.

`perl -v`

![이 명령을 실행하면 공개 키가 복사됩니다.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>저장소 스냅숏 설정

HANA 대규모 인스턴스를 사용하여 저장소 스냅숏을 설정하려면 다음과 같은 단계를 수행합니다.
1. Perl이 HANA 큰 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
2. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
3. 적용할 수 있는 경우 실행하는 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다.
4. 모든 SAP HANA 큰 인스턴스 서버에 SAP HANA HDB 클라이언트를 설치합니다.
5. 각 지역의 첫 번째 SAP HANA 큰 인스턴스 서버에서 스냅숏 생성을 제어하는 기본 저장소 인프라에 액세스하기 위한 공개 키를 만듭니다.
6. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts)의 스크립트 및 구성 파일을 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
7. 적절한 고객 사양에 필요한 대로 *HANABackupDetails.txt* 파일을 수정합니다.

### <a name="consideration-for-mcod-scenarios"></a>MCOD 시나리오에 대한 고려 사항
하나의 HANA 대규모 인스턴스 단위에 여러 SAP HANA 인스턴스가 있는 [MCOD 시나리오](https://launchpad.support.sap.com/#/notes/1681092)를 실행하는 경우, 각 SAP HANA 인스턴스에 별도의 저장소 볼륨을 프로비전했습니다. 현재 버전의 셀프 서비스 스냅숏 자동화에서는 각 HANA 인스턴스 SID(시스템 ID)에 대해 별도의 스냅숏을 시작할 수 없습니다. 제공되는 기능은 구성 파일에서 서버의 등록된 SAP HANA 인스턴스를 확인하고(이 아티클의 뒷부분 참조) 단위에 등록된 모든 인스턴스 볼륨의 동시 스냅숏을 실행합니다.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1단계: SAP HANA HDB 클라이언트 설치

Azure 대규모 인스턴스의 SAP HANA에 설치된 Linux 운영 체제는 백업 및 재해 복구를 위해 SAP HANA 저장소 스냅숏을 실행하는 데 필요한 폴더와 스크립트를 포함합니다. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts)에서 최신 릴리스를 확인하세요. 스크립트의 가장 최신 릴리스 버전은 3.x입니다. 각 스크립트마다 동일한 주 릴리스 내의 다른 부 릴리스를 가질 수 있습니다.

>[!IMPORTANT]
>스크립트 버전 2.1에서 버전 3.0으로 이동하면서 구성 파일과 일부 구문의 구조가 변경되었습니다. 특정 섹션에서 설명을 참조하세요. 

SAP HANA를 설치하는 동안 HANA 대규모 인스턴스 단위에 SAP HANA HDB 클라이언트를 설치하는 것은 사용자의 책임입니다.

### <a name="step-2-change-the-etcsshsshconfig"></a>2단계: /etc/ssh/ssh\_config 변경

아래와 같이 _MACs hmac-sha1_ 줄을 추가하여 `/etc/ssh/ssh_config`를 변경합니다.
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

HANA 대규모 인스턴스 테넌트의 저장소 스냅숏 인터페이스에 대한 액세스할 수 있도록 설정하려면 공개 키를 통한 로그인 프로시저를 설정해야 합니다. 테넌트에 있는 첫 번째 Azure의 SAP HANA(대규모 인스턴스) 서버에서 저장소 인프라에 액세스하는 데 사용할 공개 키를 만듭니다. 공개 키를 사용하면 저장소 스냅숏 인터페이스에 로그인하는 데 암호가 필요하지 않습니다. 공개 키를 만들면 암호 자격 증명을 유지할 필요 없습니다. SAP HANA 큰 인스턴스 서버의 Linux에서 공개 키를 생성하려면 다음 명령을 실행합니다.
```
  ssh-keygen –t dsa –b 1024
```
새 위치는 **_/root/.ssh/id\_dsa.pub**입니다. 실제 암호를 입력하지 마십시오. 그렇지 않으면 로그인할 때마다 암호를 입력해야 합니다. 대신, **Enter**를 두 번 선택하여 로그인에 대한 “암호 입력” 요구 사항을 제거합니다.

폴더를 **/root/.ssh/**로 변경한 다음, `ls` 명령을 실행하여 공개 키가 예상대로 수정되었는지 확인합니다. 키가 있는 경우 다음 명령을 실행하여 복사할 수 있습니다.

![이 명령을 실행하면 공개 키가 복사됩니다.](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

이 시점에서 Azure Service Management의 SAP HANA에 연락하여 공개 키를 제공하십시오. 서비스 담당자는 공개 키를 사용하여 HANA 큰 인스턴스 테넌트에 대해 획득되는 기본 저장소 인프라에 등록합니다.

### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

SAP HANA 스냅숏 만들기를 시작하려면 저장소 스냅숏 스크립트에서 사용할 수 있는 SAP HANA에 사용자 계정을 만들어야 합니다. 이를 위해 SAP HANA Studio 내에 SAP HANA 사용자 계정을 만듭니다. 사용자는 SID 데이터베이스가 아닌 SYSTEMDB 아래에 만들어야 합니다. 이 계정에는 **Backup 관리** 및 **카탈로그 읽기** 권한이 있어야 합니다. 이 예제에서 사용자 이름은 **SCADMIN**입니다. HANA Studio에서 만든 사용자 계정 이름은 대/소문자를 구분합니다. 사용자가 다음에 로그인할 때 암호를 변경하도록 요구하는 경우 **아니요**를 선택해야 합니다.

![HANA Studio에서 사용자 만들기](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

하나의 단위에 여러 SAP HANA 인스턴스가 있는 MCOD 배포를 사용하는 경우 각 SAP HANA 인스턴스에서 이 단계를 반복해야 합니다.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

이 단계에서는 스크립트가 런타임 시 암호를 제출하지 않아도 되도록 사용자가 만든 SAP HANA 사용자 계정에 권한을 부여합니다. SAP HANA 명령 `hdbuserstore`을 사용하면 하나 이상의 SAP HANA 노드에 저장되는 SAP HANA 사용자 키를 만들 수 있습니다. 사용자 키를 사용하면 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다. 스크립팅 프로세스는 이 아티클의 뒤에서 설명하겠습니다.

>[!IMPORTANT]
>다음 명령을 `root`로 실행합니다. 그렇지 않으면 스크립트가 제대로 작동할 수 없습니다.

`hdbuserstore` 명령을 다음과 같이 입력합니다.

**비 MDC HANA 설정의 경우**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**MDC HANA 설치**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

다음 예제에서 사용자는 **SCADMIN01**이고 호스트 이름은 **lhanad01**이고 인스턴스는 **01**입니다.
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
하나의 단위에 여러 SAP HANA 인스턴스가 있는 HANA MCOD 배포를 사용하는 경우 각 SAP HANA 인스턴스 및 해당 단위의 관련된 백업 사용자에 대해 단계를 반복해야 합니다.

SAP HANA 스케일 아웃 구성이 있는 경우 단일 서버에서 모든 스크립팅을 관리해야 합니다. 이 예제에서는 키와 관련된 호스트를 보여주는 방식으로 각 호스트에 대해 SAP HANA 키 **SCADMIN01**을 변경해야 합니다. HANA DB 인스턴스 번호로 SAP HANA 백업 계정을 수정하십시오. 키에는 할당된 호스트에 대한 관리자 권한이 있어야 하며, 스케일 아웃 구성에 대한 백업 사용자에게는 모든 SAP HANA 인스턴스에 대한 액세스 권한이 있어야 합니다. 이름이 **lhanad01**, **lhanad02** 및 **lhanad03**인 3개의 확장 노드가 있는 경우 명령 시퀀스는 다음과 같습니다.

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6단계: 스냅숏 스크립트 가져오기, 스냅숏 구성, 구성 및 연결 테스트

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts)에서 최신 버전의 스크립트를 다운로드하십시오. 다운로드한 스크립트 및 텍스트 파일을 **hdbsql**에 대한 작업 디렉터리에 복사합니다. 현재 HANA 설치의 경우 이 디렉터리 형식은 /hana/shared/D01/exe/linuxx86\_64/hdb와 같습니다. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Perl 스크립트를 처리하는 경우: 

- Microsoft Operations의 지시가 없는 한 스크립트를 수정하지 마세요.
- 스크립트 또는 매개 변수 파일을 수정하라는 메시지가 표시되는 경우 메모장 등의 Windows 편집기가 아니라 항상 “vi” 등의 Linux 텍스트 편집기를 사용합니다. Windows 편집기를 사용하면 파일 형식이 손상될 수 있습니다.
- 항상 최신 스크립트를 사용합니다. 최신 버전은 GitHub에서 다운로드할 수 있습니다.
- 전체적으로 동일한 버전의 스크립트를 사용합니다.
- 프로덕션 시스템에서 직접 사용하기 전에 스크립트를 테스트하고 필수 매개 변수 및 스크립트 출력에 익숙해져야 합니다.
- Microsoft Operations에서 프로비전된 서버의 탑재 지점 이름을 변경하지 마세요. 이 스크립트를 성공적으로 실행하려면 이러한 표준 탑재 지점을 사용할 수 있어야 합니다.


각 스크립트 및 파일의 용도는 다음과 같습니다.

- **azure\_hana\_backup.pl**: HANA 데이터와 공유 볼륨, /hana/logbackups 볼륨 또는 운영 체제에서 저장소 스냅숏을 실행하려면 Linux Cron 예약 유틸리티를 사용하여 이 스크립트를 예약합니다.
- **azure\_hana\_replication\_status.pl**: 이 스크립트는 프로덕션 사이트에서 재해 복구 사이트로의 복제 상태에 대한 기본 세부 정보를 제공합니다. 이 스크립트는 복제가 수행되고 있는지 확인하기 위해 모니터링하고 복제되는 항목의 크기를 표시합니다. 또한 복제 너무 오래 걸리거나 연결이 다운될 경우에 대한 지침을 제공합니다.
- **azure\_hana\_snapshot\_details.pl**: 이 스크립트는 사용자 환경에 존재하는 볼륨별 모든 스냅숏에 대한 기본 세부 정보 목록을 제공합니다. 이 스크립트는 주 서버 또는 재해 복구 위치의 서버 단위에서 실행할 수 있습니다. 이 스크립트는 스냅숏이 포함된 각 볼륨별로 다음과 같이 세분화된 정보를 제공합니다.
   * 볼륨의 총 스냅숏 크기
   * 해당 볼륨의 각 스냅숏에 다음 세부 정보가 포함됩니다. 
      - 스냅숏 이름 
      - 만든 시간 
      - 스냅숏 크기
      - 스냅숏 빈도
      - 스냅숏과 연결된 HANA 백업 ID(해당되는 경우)
- **azure\_hana\_snapshot\_delete.pl**: 이 스크립트는 저장소 스냅숏 또는 스냅숏 집합을 삭제합니다. HANA Studio에 있는 SAP HANA 백업 ID 또는 저장소 스냅숏 이름을 사용할 수 있습니다. 현재, 백업 ID는 HANA data/log/shared 볼륨에 대해 생성된 스냅숏에만 연결됩니다. 그렇지 않은 경우 스냅숏 ID를 입력하면 입력한 스냅숏 ID와 일치하는 모든 스냅숏을 찾습니다.  
- **testHANAConnection.pl**: 이 스크립트는 SAP HANA 인스턴스에 대한 연결을 테스트하며, 저장소 스냅숏을 설정하는 데 필요합니다.
- **testStorageSnapshotConnection.pl**: 이 스크립트는 두 가지 용도로 사용됩니다. 첫째, 스크립트를 실행하는 HANA 대규모 인스턴스 단위가 할당된 저장소 가상 머신 및 HANA 대규모 인스턴스의 저장소 스냅숏 인터페이스에 대해 액세스할 수 있도록 합니다. 두 번째 용도는 테스트 중인 HANA 인스턴스에 대한 임시 스냅숏을 만드는 것입니다. 이 스크립트는 백업 스크립트가 예상대로 작동되도록 하기 위해 서버의 모든 HANA 인스턴스에 대해 실행해야 합니다.
- **removeTestStorageSnapshot.pl**: 이 스크립트는 **testStorageSnapshotConnection.pl** 스크립트로 만든 테스트 스냅숏을 삭제합니다.
- **azure\_hana\_dr\_failover.pl**: 이 스크립트는 다른 지역으로 DR 장애 조치(failover)를 시작합니다. DR 지역의 HANA 대규모 인스턴스 단위 또는 장애 조치하려는 단위에서 스크립트를 실행해야 합니다. 이 스크립트는 기본 쪽에서 보조 쪽으로의 저장소 복제를 중지하고 DR 볼륨에서 최신 스냅숏을 복원하며 DR 볼륨에 대한 탑재 지점을 제공합니다.
- **azure\_hana\_test\_dr\_failover.pl**: 이 스크립트는 DR 사이트로 테스트 장애 조치(failover)를 수행합니다. azure_hana_dr_failover.pl 스크립트와 달리 이 실행은 기본에서 보조로의 저장소 복제를 중단하지 않습니다. 복제된 저장소 볼륨의 복제본이 DR 쪽에 생성되고 복제된 볼륨의 탑재 지점이 제공됩니다. 
- **HANABackupCustomerDetails.txt**: 이 파일은 SAP HANA 구성에 맞게 수정해야 하는 수정 가능한 구성 파일입니다. *HANABackupCustomerDetails.txt* 파일은 저장소 스냅숏을 실행하는 스크립트의 제어 및 구성 파일입니다. 사용자의 목적에 맞게 파일을 조정하고 설정하십시오. 인스턴스를 배포할 때 Azure Service Management의 SAP HANA에서 **Storage Backup 이름** 및 **Storage IP 주소**를 수신합니다. 이 파일에서는 변수의 시퀀스, 순서 또는 간격을 수정할 수 없습니다. 그러면 스크립트가 제대로 실행되지 않습니다. 또한 Azure Service Management의 SAP HANA에서 확장 노드 또는 마스터 노드의 IP 주소(스케일 아웃의 경우)를 수신했습니다. SAP HANA를 설치하는 동안 얻은 HANA 인스턴스 번호도 알고 있습니다. 이제 구성 파일에 백업 이름을 추가해야 합니다.

강화 또는 스케일 아웃 배포의 경우 HANA 대규모 인스턴스 단위와 서버 IP 주소의 서버 이름을 입력한 후에 구성 파일은 다음 예와 같습니다. SAP HANA 시스템 복제를 사용하는 경우 HANA 시스템 복제 구성의 가상 IP 주소를 사용합니다. 백업하거나 복구하려는 각 SAP HANA SID에 필요한 모든 필드에 정보를 입력합니다.

필수 필드 앞에 "#"을 추가하여 일정 기간 동안 백업하지 않으려는 인스턴스의 행을 주석으로 처리할 수도 있습니다. 해당 특정 인스턴스를 백업하거나 복구할 필요가 없는 경우 서버에 포함된 모든 SAP HANA 인스턴스를 입력할 필요도 없습니다. 모든 필드에 대해 형식을 유지해야 합니다. 그러지 않으면 모든 스크립트가 오류 메시지를 throw하고 스크립트가 종료됩니다. 사용 중인 마지막 SAP HANA 인스턴스 뒤에 있는 사용하지 않는 SID 정보의 추가 필수 행을 삭제할 수 있습니다. 모든 행에 정보를 입력하거나, 주석으로 처리하거나, 삭제해야 합니다.

>[!IMPORTANT]
>버전 2.1에서 버전 3.0으로 이동하면서 파일의 구조가 변경되었습니다. 3.0 버전 스크립트를 사용하려는 경우 구성 파일 구조를 조정해야 합니다. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

HANA 대규모 인스턴스 단위 또는 스케일 아웃 구성에 구성하는 각 인스턴스에 대해 다음과 같이 데이터를 정의해야 합니다.

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
스케일 아웃 및 HANA 시스템 복제 구성의 경우 각 노드에서 이 구성을 반복합니다. 이 조치가 실패할 경우 백업 및 최종 저장소 복제가 계속 작동하도록 해야 합니다.   

모든 구성 데이터를 *HANABackupCustomerDetails.txt* 파일에 추가한 후에 HANA 인스턴스 데이터에 대한 구성이 올바른지 확인합니다. `testHANAConnection.pl` 스크립트를 사용합니다. 이것은 SAP HANA 강화 또는 스케일 아웃 구성과 별개입니다.

```
testHANAConnection.pl
```

SAP HANA 규모 확장 구성이 있는 경우 마스터 HANA 인스턴스에서 필요한 모든 HANA 서버 및 인스턴스에 액세스할 수 있어야 합니다. 테스트 스크립트에 대한 매개 변수가 없지만 이 스크립트를 적절하게 실행하려면 *HANABackupCustomerDetails.txt* 구성 파일에 데이터 추가해야 합니다. 셸 명령 오류 코드만 반환되기 때문에 스크립트가 모든 인스턴스의 오류를 검사하는 것은 불가능합니다. 이런 경우에도 스크립트는 다시 확인하기 위한 몇 가지 유용한 설명을 제공하지 않습니다.

스크립트를 실행하려면 다음 명령을 입력하십시오.
```
 ./testHANAConnection.pl
```
스크립트가 HANA 인스턴스의 상태를 가져오는 데 성공한 경우 HANA 연결이 성공적이라는 메시지를 출력합니다.


다음 테스트 단계에서는 *HANABackupCustomerDetails.txt* 구성 파일에 추가한 데이터를 기준으로 저장소에 대한 연결을 확인한 다음, 테스트 스냅숏으로 실행합니다. `azure_hana_backup.pl` 스크립트를 실행하기 전에 다음 테스트를 실행해야 합니다. 볼륨에 스냅숏이 없는 경우 볼륨이 비어 있는지 아니면 스냅숏 세부 정보를 가져오지 못하는 SSH가 있는지를 확인하는 것이 불가능합니다. 이러한 이유로 스크립트는 다음 두 단계를 실행합니다.

- 스크립트에서 스냅숏을 실행할 수 있도록 테넌트의 저장소 가상 머신과 인터페이스가 액세스할 수 있는지 확인합니다.
- HANA 인스턴스에서 각 볼륨의 테스트, 더미 또는 스냅숏을 만듭니다.

이러한 이유로 HANA 인스턴스를 인수로 포함합니다. 실행이 실패하는 경우 저장소 연결에 대한 오류 검사를 제공할 수 없습니다. 오류 검사가 없더라도 스크립트는 유용한 힌트를 제공합니다.

1. 이 테스트를 수행할 명령 시퀀스를 실행합니다.

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   HANA 큰 인스턴스 단위를 핸드오버할 때 저장소 사용자 이름과 저장소 IP 주소가 둘 다 제공되었습니다.

2. 테스트 스크립트를 실행합니다.
   ```
    ./testStorageSnapshotConnection.pl <HANA SID>
   ```

이 스크립트는 이전 설치 단계에서 제공된 공개 키와 *HANABackupCustomerDetails.txt* 파일에 구성된 데이터를 사용하여 저장소에 로그인하려고 합니다. 로그인이 성공하면 다음 콘텐츠가 표시됩니다.

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

저장소 콘솔 연결에 문제가 발생하면 다음과 같은 출력이 표시됩니다.

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

저장소 가상 머신 인터페이스에 로그인이 성공하면 스크립트는 2단계로 진행되고 테스트 스냅숏을 만듭니다. SAP HANA의 3노드 스케일 아웃 구성에 대한 출력은 다음과 같습니다.

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

이 스크립트를 사용하여 테스트 스냅숏을 성공적으로 실행한 경우 실제 저장소 스냅숏 구성을 진행할 수 있습니다. 성공하지 못했다면 계속 진행하기 전에 문제를 조사합니다. 테스트 스냅숏은 첫 번째 실제 스냅숏이 완료될 때까지 유지되어야 합니다.


### <a name="step-7-perform-snapshots"></a>7단계: 스냅숏 수행

모든 준비 단계가 완료되면 실제 저장소 스냅숏을 구성하기 시작할 수 있습니다. 예약할 스크립트는 SAP HANA 강화 및 규모 확장 구성에 작동합니다. 백업 스크립트의 주기적인 일반 실행의 경우 cron 유틸리티를 사용하여 스크립트를 예약합니다. 

세 가지 유형의 스냅숏 백업을 만들 수 있습니다.
- **HANA**: hana/data 및 /hana/shared(/usr/sap도 포함)가 포함된 볼륨이 조합된 스냅숏으로 보호되는 결합된 스냅숏 백업입니다. 이 스냅숏에서 단일 파일을 복원할 수 있습니다.
- **Logs**: /hana/logbackups 볼륨의 스냅숏 백업입니다. 이 저장소 스냅숏을 실행하기 위해 트리거되는 HANA 스냅숏은 없습니다. 이 저장소 볼륨은 SAP HANA 트랜잭션 로그 백업을 포함하기 위한 것입니다. 이러한 기능은 로그 증가를 제한하고 잠재적 인 데이터 손실을 방지하기 위해 더 자주 수행됩니다. 이 스냅숏에서 단일 파일을 복원할 수 있습니다. 빈도를 3분 미만으로 낮추지 마세요.
- **Boot**: HANA 대규모 인스턴스의 부팅 LUN(논리 단위 번호)을 포함하는 볼륨의 스냅숏입니다. 이 스냅숏 백업은 HANA 큰 인스턴스의 유형 I SKU에서만 가능합니다. 부팅 LUN을 포함하는 볼륨의 스냅숏에서 단일 파일 복원을 수행할 수 없습니다.


>[!NOTE]
> MCOD 배포를 지원하는 버전 3.0 스크립트로 이동하면서 이러한 세 가지 유형의 스냅숏에 대한 호출 구문이 변경되었습니다. 인스턴스의 HANA SID를 더 이상 지정할 필요가 없습니다. 단위의 SAP HANA 인스턴스가 구성 파일 *HANABackupCustomerDetails.txt*에 구성되어 있는지 확인해야 합니다.

>[!NOTE]
> 스크립트를 처음으로 실행하는 경우 다중 SID 환경에서는 예기치 않은 오류가 발생할 수도 있습니다. 스크립트를 다시 실행하여 문제를 해결할 수 있습니다.



*azure_hana_backup.pl* 스크립트를 사용하여 저장소 스냅숏을 실행하기 위한 새 호출 구문은 다음과 같습니다.

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

매개 변수의 세부 정보는 다음과 같습니다. 

- 첫 번째 매개 변수는 스냅숏 백업의 유형을 지정합니다. 허용되는 값은 **hana**, **logs**, **boot**입니다. 
- **<HANA Large Instance Type>** 매개 변수는 부팅 볼륨 백업에만 필요합니다. HANA 큰 인스턴스 단위에 종속된 “TypeI” 또는 “TypeII”의 두 유효한 값이 있습니다. 사용자 단위의 형식을 알아보려면 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조하세요.  
- **<snapshot_prefix>** 매개 변수는 스냅숏 유형에 대한 백업 레이블 또는 스냅숏입니다. 두 가지 목적 중 한 가지는 이름을 지정하여 스냅숏의 의미를 파악하는 것입니다. 두 번째 목적은 *azure\_hana\_backup.pl* 스크립트가 특정 레이블 하에 보존되는 저장소 스냅숏의 수를 확인하는 것입니다. 두 개의 다른 레이블로 동일한 유형(예: **hana**)의 저장소 스냅숏 백업을 두 개 예약하고 각각에 대해 30개의 스냅숏을 보관하도록 정의하면 영향을 받는 볼륨의 저장소 스냅숏은 60개가 됩니다. 
- **<snapshot_frequency>** 매개 변수는 이후 개발을 위해 예약되었으며 영향을 주지 않습니다. **로그** 형식의 백업을 실행하는 경우 "3분"으로 설정하고 다른 백업 형식을 실행하는 경우 "15분"으로 설정합니다.
- **<number of snapshots retained>** 매개 변수는 동일한 스냅숏 접두사(레이블)가 포함된 스냅숏 수를 정의하여 간접적으로 스냅숏의 보존을 정의합니다. 이 매개 변수는 cron을 통해 예약된 실행에 중요합니다. 동일한 snapshot_prefix를 가진 스냅숏 수가 이 매개 변수로 지정된 개수를 초과할 경우 새 저장소 스냅숏을 실행하기 전에 가장 오래된 스냅숏이 삭제됩니다.

스케일 아웃의 경우 스크립트는 모든 HANA 서버에 액세스할 수 있는지 확인하기 위해 추가 검사를 수행합니다. 또한 스크립트는 SAP HANA 스냅숏을 만들기 전에 모든 HANA 인스턴스가 적절한 인스턴스 상태를 반환하는지 확인합니다. SAP HANA 스냅숏 다음에 저장소 스냅숏이 실행됩니다.

`azure_hana_backup.pl` 스크립트를 실행하면 다음 세 가지 단계에서 저장소 스냅숏이 만들어집니다.

1. SAP HANA 스냅숏을 실행
2. 저장소 스냅숏을 실행
3. 저장소 스냅숏을 실행하기 전에 만든 SAP HANA 스냅숏을 제거

스크립트를 실행하려면 복사된 HDB 실행 파일 폴더에서 호출합니다. 

보존 기간은 스크립트를 실행할 경우 매개 변수로 제출되는 스냅숏 개수와 함께 관리됩니다. 저장소 스냅숏이 적용되는 기간은 실행 기간 및 스크립트 실행 시 매개 변수로 제출된 스냅숏 개수입니다. 유지되는 스냅숏 개수가 스냅숏 스크립트의 호출에서 매개 변수로 이름이 지정된 수를 초과하면 새 스냅숏을 실행하기 전에 동일한 레이블의 가장 오래된 저장소가 삭제됩니다. 호출의 마지막 매개 변수로 지정한 숫자는 유지되는 스냅숏 개수를 제어하는 데 사용할 수 있는 숫자입니다. 이 숫자를 사용하여 스냅숏에 사용되는 디스크 공간을 간접적으로 제어할 수도 있습니다. 

> [!NOTE]
>레이블을 변경하면 즉시 계수가 다시 시작됩니다. 스냅숏이 실수로 삭제되지 않도록 레이블을 엄격하게 지정해야 합니다.

### <a name="snapshot-strategies"></a>스냅숏 전략
다른 유형에 대한 스냅숏 빈도는 HANA 대규모 인스턴스 재해 복구 기능을 사용하는지 여부에 따라 달라집니다. 이 기능은 저장소 스냅숏을 사용합니다. 그러려면 저장소 스냅숏의 빈도 및 실행 기간에 대한 특별한 권장 사항이 필요할 수 있습니다. 

다음 고려 사항 및 권장 사항에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능을 사용하지 *않는다*고 가정합니다. 대신, 저장소 스냅숏을 사용하여 백업을 보유하고 지난 30일 동안의 지정 시간 복구를 제공할 수 있습니다. 스냅숏 수와 공간이 제한된다고 가정할 경우 고객은 다음과 같은 요구 사항을 고려했습니다.

- 지정 시간 복구에 대한 복구 시간.
- 사용된 공간.
- 재해로부터 잠재적 복구를 위한 복구 지점 및 복구 시간 목표.
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행. 디스크 또는 **backint** 인터페이스에 대한 전체 데이터베이스 백업이 수행될 때마다 저장소 스냅숏의 실행에 실패합니다. 저장소 스냅숏을 기반으로 전체 데이터베이스 백업을 실행하려는 경우 이 시간 동안 저장소 스냅숏의 실행이 비활성화되어 있는지 확인하십시오.
- 볼륨당 스냅숏 수(255개로 제한).


HANA 큰 인스턴스의 재해 복구 기능을 사용하지 않는 고객의 경우 스냅숏 기간이 덜 빈번합니다. 이러한 경우에 고객은 12시간 또는 24시간 기간으로 /hana/data, /hana/shared(/usr/sap 포함)의 결합된 스냅숏을 수행하고 1달 동안 해당 스냅숏을 유지합니다. 로그 백업 볼륨의 스냅숏도 마찬가지입니다. 하지만 로그 백업 볼륨에 대한 SAP HANA 트랜잭션 로그 백업은 5-15분 기간으로 실행됩니다.

예약된 저장소 스냅숏은 cron를 사용하여 수행하는 것이 가장 좋습니다. 모든 백업 및 재해 복구 요구 사항에 대해 동일한 스크립트를 사용하고, 다양하게 요청된 백업 시간을 일치시키도록 스크립트 입력을 수정합니다. 이러한 스냅숏은 해당 실행 시간(매시간, 12시간, 매일 또는 매주)에 따라 cron에서 모두 다르게 예약됩니다. 

/etc/crontab의 cron 일정 예제는 다음과 같습니다.
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
이전 예제에는 /hana/data, /hana/shared(/usr/sap 포함) 위치가 포함된 볼륨을 처리하는 시간별로 결합 스냅숏이 있습니다. 지난 2일 내에 더 빠른 지정 시간 복구에 이 형식의 스냅숏을 사용합니다. 또한 해당 볼륨에 대한 일별 스냅숏이 있습니다. 따라서 2일 간의 시간별 스냅숏과 4주 간의 일별 스냅숏을 갖습니다. 또한 트랜잭션 로그 백업 볼륨은 매일 백업됩니다. 또한 이러한 백업은 4주 동안 유지됩니다. crontab의 세 번째 줄에 표시된 대로 HANA 트랜잭션 로그의 백업은 5분마다 실행되도록 예약됩니다. 저장소 스냅숏을 실행하는 여러 cron 작업의 시작 시간이 차이를 두기 때문에 해당 스냅숏이 특정 시점에 한꺼번에 실행되지 않습니다. 

다음 예제에서는 /hana/data, /hana/shared(/usr/sap 포함) 위치가 포함된 볼륨을 처리하는 결합된 스냅숏을 매시간 수행합니다. 이 스냅숏은 이틀 동안 보관합니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 5분 단위로 실행되고 4시간 동안 유지됩니다. 이전과 마찬가지로, HANA 트랜잭션 로그 파일의 백업은 5분 간격으로 실행되도록 예약됩니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 트랜잭션 로그 백업이 시작된 후 2분 지연되어 수행됩니다. 해당하는 2분 이내에 정상적인 상황에서 SAP HANA 트랜잭션 로그 백업이 완료되어야 합니다. 이전과 마찬가지로, 부팅 LUN이 포함된 볼륨은 저장소 스냅숏에 의해 하루 1번 백업되고 4주 동안 보관됩니다.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

다음 그래픽은 부팅 LUN을 제외하고 이전 예제의 순서를 보여줍니다.

![백업과 스냅숏 간의 관계](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA는 /hana/log 볼륨에 대해 정기적인 쓰기를 수행하여 데이터베이스에 대한 커밋된 변경 내용을 문서화합니다. 정기적으로 SAP HANA는 /hana/data 볼륨에 저장점을 씁니다. crontab에 지정된 대로 SAP HANA 트랜잭션 로그 백업은 5분마다 실행됩니다. /hana/data 및 /hana/shared 볼륨에 대해 결합된 저장소 스냅숏을 트리거한 결과로, SAP HANA 스냅숏이 매시간 실행된다는 것을 알 수 있습니다. HANA 스냅숏이 성공한 후 결합된 저장소 스냅숏이 실행됩니다. crontab에 설명된 대로 /hana/logbackup 볼륨의 저장소 스냅숏은 HANA 트랜잭션 로그 백업이 있고 약 2분 후에 5분마다 실행됩니다.

> [!NOTE]
>HANA 시스템 복제 설정의 두 노드에서 저장소 스냅숏 백업을 예약하는 경우 두 노드 간의 스냅숏 백업 실행이 겹치지 않도록 해야 합니다. SAP HANA에는 한 번에 하나의 HANA 스냅숏만 처리해야 하는 제한 사항이 있습니다. HANA 스냅숏이 성공적인 저장소 스냅숏 백업의 기본 구성 요소이기 때문에 기본 및 보조 노드와 최종 세 번째 노드의 저장소 스냅숏의 시간이 서로 겹치지 않는지 확인해야 합니다.


>[!IMPORTANT]
> SAP HANA 트랜잭션 백업에 대한 저장소 스냅숏 사용은 스냅숏이 SAP HANA 로그 백업과 함께 수행된 경우에만 유용합니다. 이러한 트랜잭션 로그 백업은 저장소 스냅숏 사이의 기간에 적용되어야 합니다. 

30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음을 수행해야 합니다.

- 극단적인 경우 30일이 지난 /hana/data 및 /hana/shared의 결합된 저장소 스냅숏에 액세스합니다.
- 결합된 저장소 스냅숏 사이의 시간에 해당하는 인접한 트랜잭션 로그 백업이 있어야 합니다. 따라서 트랜잭션 로그 백업 볼륨의 가장 오래된 스냅숏은 30일이 되어야 합니다. 트랜잭션 로그 백업을 Azure Storage에 있는 다른 NFS 공유에 복사하는 경우에는 그렇지 않습니다. 이러한 경우에 해당 NFS 공유에서 오래된 트랜잭션 로그 백업을 끌어올 수 있습니다.

저장소 스냅숏 및 트랜잭션 로그 백업의 최종 저장소 복제를 활용하려면 SAP HANA가 트랜잭션 로그 백업을 쓰는 위치를 변경해야 합니다. 이것은 HANA Studio에서 변경할 수 있습니다. SAP HANA가 전체 로그 세그먼트를 자동으로 백업하지만 확정된 로그 백업 간격을 지정해야 합니다. 재해 복구 옵션을 사용하는 경우 일반적으로 확정된 기간으로 로그 백업을 실행하기 때문에 특히 그렇습니다. 다음과 같은 경우 로그 백업 간격으로 15분이 설정되었습니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15분보다 더 자주 수행되는 백업을 선택할 수도 있습니다. 더 잦은 설정은 HANA 대규모 인스턴스의 재해 복구 기능과 결합하여 자주 사용됩니다. 일부 고객은 5분마다 트랜잭션 로그 백업을 수행합니다.  

이전에 데이터베이스를 백업한 적이 없는 경우 최종 단계는 파일 기반 데이터베이스 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA가 지정된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


첫 번째로 성공한 저장소 스냅숏을 실행한 후 6단계에서 실행된 테스트 스냅숏을 삭제할 수 있습니다. 이렇게 하려면 `removeTestStorageSnapshot.pl` 스크립트를 실행하십시오.
```
./removeTestStorageSnapshot.pl <hana instance>
```

스크립트 출력의 예제는 다음과 같습니다.
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅숏의 개수 및 크기 모니터링

특정 저장소 볼륨에서 스냅숏의 개수 및 스냅숏의 저장소 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅숏 디렉터리 또는 파일을 표시하지 않습니다. 그러나 Linux OS 명령 `du`는 저장소 스냅숏이 동일한 볼륨에 저장되므로 이러한 저장소 스냅숏에 대한 세부 정보를 보여 줍니다. 이 명령은 다음 옵션과 함께 사용할 수 있습니다.

- `du –sh .snapshot`: 이 옵션은 스냅숏 디렉터리 내에서 모든 스냅숏을 제공합니다.
- `du –sh --max-depth=1`: 이 옵션은 **.snapshot** 폴더에 저장된 모든 스냅숏 및 각 스냅숏의 크기를 나열합니다.
- `du –hc`: 이 옵션은 모든 스냅숏에 사용되는 전체 크기를 제공합니다.

이러한 명령을 사용하여 만들고 저장한 스냅숏이 볼륨에서 모든 저장소를 사용하지 않도록 합니다.

>[!NOTE]
>부팅 LUN의 스냅숏은 이전 명령으로 표시되지 않습니다.

### <a name="getting-details-of-snapshots"></a>스냅숏 세부 정보 가져오기
스냅숏에 대한 자세한 내용을 보려면 `azure_hana_snapshot_details.pl` 스크립트를 사용할 수도 있습니다. 이 스크립트는 재해 복구 위치에 활성 서버가 있을 때 어떤 위치에서도 실행될 수 있습니다. 이 스크립트는 스냅숏이 포함된 각 볼륨별로 다음과 같이 세분화된 출력을 제공합니다. 
   * 볼륨의 총 스냅숏 크기
   * 해당 볼륨의 각 스냅숏에 다음 세부 정보가 포함됩니다. 
      - 스냅숏 이름 
      - 만든 시간 
      - 스냅숏 크기
      - 스냅숏 빈도
      - 스냅숏과 연결된 HANA 백업 ID(해당되는 경우)

스크립트 실행 구문의 예제는 다음과 같습니다.

```
./azure_hana_snapshot_details.pl 
```

스크립트가 HANA 백업 ID를 검색하려고 하기 때문에 SAP HANA 인스턴스에 연결되어야 합니다. 이 연결에서는 *HANABackupCustomerDetails.txt* 구성 파일을 올바르게 설정해야 합니다. 볼륨에 있는 두 개 스냅숏의 출력 내용은 다음과 같습니다.

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>저장소 스냅숏에서 파일 수준 복원
스냅숏 형식 **hana** 및 **logs**의 경우 **.snapshot** 디렉터리의 볼륨에서 직접 스냅숏에 액세스할 수 있습니다. 각 스냅숏에 대해 하위 디렉터리가 있습니다. 해당 하위 디렉터리의 스냅숏 시점에 있는 상태인 각 파일을 실제 디렉터리 구조로 복사할 수 있습니다.

>[!NOTE]
>단일 파일 복원은 HANA 큰 인스턴스 단위의 유형에 독립적인 부팅 LUN의 스냅숏에 대해 작동하지 않습니다. **.snapshot** 디렉터리는 부팅 LUN에서 노출되지 않습니다. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>서버에서 스냅숏 개수 감소

앞에서 설명한 대로 사용자가 저장한 스냅숏의 특정 레이블 수를 줄일 수 있습니다. 스냅숏을 시작하는 명령의 마지막 두 매개 변수는 보존하려는 스냅숏의 수와 레이블입니다.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

앞의 예제에서 스냅숏 레이블은 **dailyhana**이고, 보존할 이 레이블의 스냅숏 개수는 **28**입니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅숏 수를 줄이려고 할 수 있습니다. 스냅숏 수를 (예를 들어 15개로) 줄이는 가장 좋은 방법은 마지막 매개 변수를 **15**로 설정하여 스크립트를 실행하는 것입니다.

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

이 설정으로 스크립트를 실행하면 새 저장소 스냅숏을 포함하여 스냅숏 개수가 15가 됩니다. 15개의 오래된 스냅숏은 삭제되고 15개의 가장 최근 스냅숏은 유지됩니다.

 >[!NOTE]
 > 이 스크립트는 1시간을 초과하는 스냅숏이 있는 경우에만 스냅숏 수를 줄입니다. 스크립트는 1시간 미만인 스냅숏을 삭제하지 않습니다. 이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

구문 예제에서 백업 레이블 **hanadaily**가 포함된 스냅숏 집합을 더 이상 유지하지 않으려면 **0**을 보존 숫자로 사용하는 스크립트를 실행합니다. 그러면 해당 레이블과 일치하는 모든 스냅숏이 제거됩니다. 그러나 모든 스냅숏을 제거하면 HANA 대규모 인스턴스 재해 복구 기능에 영향을 미칠 수 있습니다.

특정 스냅숏을 삭제하는 또 다른 옵션은 `azure_hana_snapshot_delete.pl` 스크립트를 사용하는 것입니다. 이 스크립트는 HANA Studio에서 확인된 HANA 백업 ID를 사용하거나 스냅숏 이름 자체를 사용하여 스냅숏 또는 스냅숏 집합을 삭제하도록 디자인되었습니다. 현재, 백업 ID는 **hana** 스냅숏 유형에 대해 생성된 스냅숏에만 연결됩니다. **로그** 및 **부팅** 형식의 스냅숏 백업은 SAP HANA 스냅숏을 수행하지 않습니다. 따라서 해당 스냅숏에는 백업 ID가 없습니다. 스냅숏 이름을 입력하는 경우 입력한 스냅숏 이름과 일치하는 다른 볼륨의 모든 스냅숏을 찾습니다. 

스크립트의 호출 구문을 사용하여 HANA 인스턴스의 SID를 지정해야 하는 스크립트를 호출합니다.

```
./azure_hana_snapshot_delete.pl <SID>

```

**root** 사용자로 스크립트를 실행합니다.

스냅숏을 선택하는 경우 각 스냅숏을 개별적으로 삭제할 수 있습니다. 먼저 스냅숏이 포함된 볼륨을 제공한 후 실제 스냅숏 이름을 제공합니다. 스냅숏이 해당 볼륨에 존재하고 1시간을 초과한 경우 삭제됩니다. `azure_hana_snapshot_details` 스크립트를 실행하면 볼륨 이름 및 스냅숏 이름을 확인할 수 있습니다. 

>[!IMPORTANT]
>데이터가 삭제하려는 스냅숏에서만 존재하는 경우 스냅숏을 삭제하면 해당 데이터가 영구적으로 손실됩니다.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅숏으로 복구

프로덕션 중단 시나리오에서 저장소 스냅숏에서 복구하는 프로세스는 Microsoft Azure 지원에서 고객 인시던트로 시작될 수 있습니다. 프로덕션 시스템에서 데이터가 삭제되고 데이터를 검색하는 유일한 방법이 프로덕션 데이터베이스를 복원하는 것인 경우 긴급한 문제입니다.

다른 경우, 지정 시간 복구는 긴급성이 낮고 사전에 계획될 수 있습니다. 우선 순위 플래그를 발생시키는 대신 Azure의 SAP HANA Service Management를 사용하여 이 복구를 계획할 수 있습니다. 예를 들어 새로운 개선 패키지를 적용하여 SAP 소프트웨어를 업그레이드하도록 계획하는 경우가 있을 수 있습니다. 이런 경우 개선 패키지를 업그레이드하기 전의 상태를 나타내는 스냅숏으로 되돌려야 합니다.

요청을 보내기 전에 준비해야 합니다. Azure Service Management 팀의 SAP HANA는 요청을 처리하고 복원된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅숏을 기반으로 HANA 데이터베이스를 복원할 수 있습니다. 

요청에 대해 준비하는 방법을 아래에 나와 있습니다.

>[!NOTE]
>사용자 인터페이스는 사용하는 SAP HANA 릴리스에 따라 다음 스크린샷과 다를 수 있습니다.

1. 복원할 스냅숏을 결정합니다. 달리 지시가 없는 경우 hana/data 볼륨만 복원됩니다. 

2. HANA 인스턴스를 종료합니다.

 ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 운영 체제에 계속 탑재되는 경우 스냅숏 복원이 실패합니다.
 ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Azure 지원 요청을 열어서 특정 스냅숏의 복원에 대한 지침을 포함합니다.

 - 복원 중: Azure Service Management의 SAP HANA에서 전화 회의에 참여하여 올바른 저장소 스냅숏이 복원될 수 있도록 조정, 검증 및 확인을 수행할 것을 요청할 수 있습니다. 

 - 복원한 후에 Azure의 SAP HANA Service Management에서는 저장소 스냅숏이 복원된 시기를 알립니다.

5. 복원 프로세스가 완료되면 모든 데이터 볼륨을 다시 탑재합니다.

 ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio를 통해 HANA DB에 다시 연결하는 경우 자동으로 표시되지 않으면 SAP HANA Studio 내에서 복구 옵션을 선택합니다. 다음 예제에서는 최신 HANA 스냅숏에 대한 복원을 보여 줍니다. 저장소 스냅숏에는 하나의 HANA 스냅숏이 포함됩니다. 가장 최근의 저장소 스냅숏으로 복원하는 경우 가장 최근의 HANA 스냅숏이어야 합니다. (이전 저장소 스냅숏으로 복원하는 경우 저장소 스냅숏이 만들어진 시간에 따라 HANA 스냅숏을 찾아야 합니다.)

 ![SAP HANA Studio 내에서 복구 옵션 선택](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. **특정 데이터 백업이나 저장소 스냅숏으로 데이터베이스 복구**를 선택합니다.

 ![복구 유형 지정 창](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. **카탈로그 없이 백업 지정**을 선택합니다.

 ![백업 위치 지정 창](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. **대상 형식** 목록에서 **스냅숏**을 선택합니다.

 ![복구할 백업 지정 창](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. **마침**을 선택하여 복구 프로세스를 시작합니다.

 ![“마침”을 선택하여 복구 프로세스를 시작](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 데이터베이스는 저장소 스냅숏이 포함된 HANA 스냅숏으로 복원되고 복구됩니다.

 ![HANA 데이터베이스는 HANA 스냅숏으로 복원되고 복구됩니다.](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>가장 최근 상태로 복구

다음 프로세스는 저장소 스냅숏에 포함되는 HANA 스냅숏을 복원합니다. 그런 다음 저장소 스냅숏을 복원하기 전에 트랜잭션 로그 백업을 데이터베이스의 가장 최근 상태로 복원합니다.

>[!IMPORTANT]
>계속 진행하기 전에 트랜잭션 로그 백업의 완전한 연속 체인이 있는지 확인합니다. 이러한 백업 없이 데이터베이스의 현재 상태를 복원할 수 없습니다.

1. [가장 최근 HANA 스냅숏으로 복구](#recovering-to-the-most-recent-hana-snapshot)에서 1-6단계를 완료합니다.

2. **가장 최근 상태로 데이터베이스 복구**를 선택합니다.

 !["가장 최근 상태로 데이터베이스 복구"를 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 가장 최근인 HANA 로그 백업의 위치를 지정합니다. 위치에 HANA 스냅숏에서 가장 최근 상태까지 모든 HANA 트랜잭션 로그 백업이 포함되어야 합니다.

 ![가장 최근인 HANA 로그 백업의 위치를 지정합니다.](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 데이터베이스를 복구하는 기본으로 백업을 선택합니다. 이 예제에서 스크린샷의 HANA 스냅숏은 저장소 스냅숏이 포함되었던 HANA 스냅숏입니다. 

 ![데이터베이스를 복구하는 기본으로 백업을 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 델타가 HANA 스냅숏 시간과 가장 최근의 상태 간에 존재하지 않는 경우 **델타 Backup 사용** 확인란의 선택을 취소합니다.

 ![델타가 존재하지 않는 경우 "델타 Backup 사용" 확인란의 선택을 취소합니다](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 요약 화면에서 **마침**을 선택하여 복원 절차를 시작합니다.

 ![요약 페이지에서 "마침"을 클릭합니다.](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>다른 지정 시점으로 복구
저장소 스냅숏에 포함된 HANA 스냅숏과 HANA 스냅숏 지정 시점 복구보다 이후인 스냅숏 간의 지정 시점으로 복구하려면 다음 단계를 수행합니다.

1. 복구하려는 시간에서 HANA 스냅숏의 모든 트랜잭션 로그 백업이 있는지 확인합니다.
2. [가장 최근 상태로 복구](#recovering-to-the-most-recent-state)에서 프로시저를 시작합니다.
3. 프로시저의 2단계에 있는 **복구 유형 지정** 창에서 **데이터베이스를 다음 지정 시점으로 복구**를 선택한 다음, 해당 시점을 지정합니다. 
4. 3-6단계를 완료합니다.

### <a name="monitor-the-execution-of-snapshots"></a>스냅숏의 실행 모니터링

HANA 대규모 인스턴스의 저장소 스냅숏을 사용하려면 해당 스냅숏의 실행도 모니터링해야 합니다. 저장소 스냅숏을 실행하는 스크립트는 파일에 출력을 작성한 다음, Perl 스크립트와 동일한 위치에 저장합니다. 각 저장소 스냅숏에 대해 별도 파일을 기록합니다. 각 파일의 출력은 스냅숏 스크립트가 실행되는 다양한 단계를 표시합니다.

1. 스냅숏을 만들어야 하는 볼륨을 찾습니다.
2. 이 볼륨에서 생성한 스냅숏을 찾습니다.
3. 기존의 최종 스냅숏을 삭제하여 지정한 스냅숏 개수와 일치시킵니다.
4. SAP HANA 스냅숏을 만듭니다.
5. 볼륨에 대한 저장소 스냅숏을 만듭니다.
6. SAP HANA 스냅숏을 삭제합니다.
7. 가장 최근의 스냅숏 이름을 **.0**으로 변경합니다.

스크립트 기능의 가장 중요한 부분은 다음 부분으로 식별됩니다.
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
스크립트가 HANA 스냅숏의 생성을 기록하는 방법을 이 샘플에서 볼 수 있습니다. 확장 사례의 경우 이 프로세스는 마스터 노드에서 시작됩니다. 마스터 노드는 각 작업자 노드에서 SAP HANA 스냅숏을 동기적으로 생성하기 시작합니다. 그러면 저장소 스냅숏이 만들어집니다. 저장소 스냅숏을 성공적으로 실행한 후에 HANA 스냅숏이 삭제됩니다. HANA 스냅숏 삭제는 마스터 노드에서 시작됩니다.


## <a name="disaster-recovery-principles"></a>재해 복구 원칙
HANA 대규모 인스턴스는 서로 다른 Azure 지역의 HANA 대규모 인스턴스 스탬프 간에 재해 복구 기능을 제공합니다. Azure의 미국 서부 지역에서 HANA 대규모 인스턴스 단위를 배포하는 경우 미국 동부 지역의 HANA 대규모 인스턴스 단위를 재해 복구 단위로 활용할 수 있습니다. 앞서 언급한 것처럼 재해 복구는 자동으로 구성되지 않습니다. DR 지역의 다른 HANA 큰 인스턴스 단위에 대한 비용이 부과되기 때문입니다. 재해 복구 설치의 경우 강화 및 규모 확장이 둘 다 가능합니다. 

지금까지 배포된 시나리오에서는 고객이 설치된 HANA 인스턴스를 사용하는 비프로덕션 시스템을 실행하기 위해 DR 지역의 단위를 사용했습니다. 이 HANA 큰 인스턴스 단위는 프로덕션 용도로 사용되는 SKU와 동일한 SKU여야 합니다. 다음 이미지에서는 Azure 프로덕션 지역의 서버 단위와 재해 복구 지역 간의 디스크 구성을 보여줍니다.

![디스크 관점의 DR 설치 구성](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

이 개요 그래픽에서 볼 수 있듯이 두 번째 디스크 볼륨 세트를 주문해야 합니다. 대상 디스크 볼륨은 재해 복구 단위의 프로덕션 인스턴스에 대한 프로덕션 볼륨과 크기가 같습니다. 이러한 디스크 볼륨은 재해 복구 사이트의 HANA 큰 인스턴스 서버 단위와 연결됩니다. 다음 볼륨은 프로덕션 지역에서 DR 사이트로 복제됩니다.

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap 포함)

/hana/log 볼륨은 복제되지 않습니다. 해당 볼륨에서 복원하는 방식으로 SAP HANA 트랜잭션 로그가 필요하지 않기 때문입니다. 

제공되는 재해 복구 기능 중 기본 기능은 HANA 대규모 인스턴스 인프라에서 제공하는 저장소 복제 기능입니다. 저장소 쪽에서 사용되는 기능은 저장소 볼륨에 변경 사항이 발생하면 비동기 방식으로 복제되는 지속적인 변경 흐름이 아닙니다. 대신, 이러한 볼륨의 스냅숏이 정기적으로 생성된다는 사실에 의존하는 메커니즘입니다. 이미 복제된 스냅숏과 아직 복제되지 않은 새 스냅숏 간의 델타는 재해 복구 사이트를 거쳐 대상 디스크 볼륨으로 전송됩니다.  이러한 스냅숏은 볼륨에 저장되며 재해 복구 장애 조치(failover)가 발생하면 해당 볼륨에서 복원되어야 합니다.  

볼륨의 전체 데이터를 처음 전송하는 것은 데이터 양이 스냅숏 간의 델타보다 작아지기 전이어야 합니다. 결과적으로 DR 사이트의 볼륨에는 프로덕션 사이트에서 수행되는 모든 볼륨 스냅숏이 포함됩니다. 결과적으로 프로덕션 시스템으로 롤백하지 않고도 손실된 데이터를 복구하기 위해 해당 DR 시스템을 사용하여 이전 상태로 돌아갈 수 있습니다.

하나의 HANA 대규모 인스턴스 단위에 여러 독립된 SAP HANA 인스턴스가 있는 MCOD 배포의 경우 모든 SAP HANA 인스턴스가 저장소를 DR 쪽으로 복제할 것으로 예상됩니다.

프로덕션 사이트에서 HANA 시스템 복제를 고가용성 기능으로 사용하는 경우에는 계층 2(또는 복제본) 인스턴스의 볼륨만 복제됩니다. 보조 복제본(계층 2) 서버 단위 또는 이 단위의 SAP HANA 인스턴스를 유지 관리하거나 중단할 경우 이러한 구성으로 인해 DR 사이트에 대한 저장소 복제가 지연될 수 있습니다. 


>[!IMPORTANT]
>다계층 HANA 시스템 복제와 마찬가지로 HANA 대규모 인스턴스 재해 복구 기능을 사용하는 경우 계층 2 HANA 인스턴스 또는 서버 단위를 종료하면 재해 복구 사이트로의 복제가 차단됩니다.


>[!NOTE]
>HANA 대규모 인스턴스 저장소 복제 기능은 저장소 스냅숏을 미러링하고 복제합니다. 이 아티클의 [백업 및 복구](#backup-and-restore) 섹션에서 소개한 대로 저장소 스냅숏을 수행하지 않는 경우 재해 복구 사이트로 복제할 수 없습니다. 재해 복구 사이트로 저장소를 복제하려면 저장소 스냅숏을 반드시 실행해야 합니다.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>재해 복구 시나리오 준비
이 시나리오에서 프로덕션 Azure 지역의 HANA 대규모 인스턴스에서 프로덕션 시스템을 실행하고 있습니다. 다음 단계의 경우 해당 HANA 시스템의 SID가 "PRD"이고, DR Azure 지역의 HANA 대규모 인스턴스에서 비프로덕션 시스템을 실행하고 있다고 가정하겠습니다. 후자의 경우 해당 SID가 "TST"라고 가정하겠습니다. 다음 이미지에서는 이 구성을 보여줍니다.

![DR 설치 시작](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

서버 인스턴스가 추가 저장소 볼륨 세트와 함께 아직 주문되지 않은 경우 Azure의 SAP HANA Service Management는 TST HANA 인스턴스를 실행하는 HANA 대규모 인스턴스 단위에 프로덕션 복제본의 대상으로 추가 볼륨 집합을 연결합니다. 이를 위해 프로덕션 HANA 인스턴스의 SID를 제공해야 합니다. Azure Service Management의 SAP HANA가 해당 볼륨의 연결을 확인하면 해당 볼륨을 HANA 큰 인스턴스 단위에 탑재해야 합니다.

![DR 설치 다음 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

다음 단계는 TST HANA 인스턴스를 실행하는 DR Azure 지역의 HANA 대규모 인스턴스 단위에 두 번째 SAP HANA 인스턴스를 설치하는 것입니다. 새로 설치된 SAP HANA 인스턴스는 SID가 동일해야 합니다. 생성된 사용자는 프로덕션 인스턴스와 UID 및 그룹 ID가 동일해야 합니다. 설치가 성공하면 다음을 수행해야 합니다.

- 이 아티클의 앞부분에서 설명한 저장소 스냅숏 준비의 2단계를 실행합니다.
- 이 단계를 실행하지 않은 경우 HANA 대규모 인스턴스 단위의 DR 단위에 대한 공개 키를 만듭니다. 이 아티클의 앞부분에서 설명한 저장소 스냅숏 준비의 3단계를 참조하세요.
- 새 HANA 인스턴스를 사용하여 *HANABackupCustomerDetails.txt*를 유지 관리하고 저장소에 대한 연결이 올바르게 작동하는지 테스트합니다.  
- DR Azure 지역의 HANA 대규모 인스턴스 단위에 새로 설치된 SAP HANA 인스턴스를 중지합니다.
- 이 PRD 볼륨을 분리하고 Azure Service Management의 SAP HANA에 문의하십시오. 저장소 복제 대상으로 작동하는 동안은 액세스할 수 없으므로 볼륨을 단위에 탑재된 상태로 유지할 수 없습니다.  

![복제를 설정하기 전 DR 설치 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

운영 팀에서는 프로덕션 Azure 지역의 PRD 볼륨과 DR Azure 지역의 PRD 볼륨 간에 복제 관계를 설정합니다.

>[!IMPORTANT]
>재해 복구 사이트에서 복제된 SAP HANA 데이터베이스를 일관된 상태로 복원할 필요가 없으므로 /hana/log 볼륨은 복제되지 않습니다.

다음으로 재해 발생 시 RTO 및 RPO를 가져오도록 저장소 스냅숏 백업 일정을 설정하거나 조정합니다. 복구 지점 목표를 최소화려면 HANA 큰 인스턴스 서비스에서 다음 복제 간격을 설정합니다.
- 결합된 스냅숏(스냅숏 형식 **hana**)을 적용하는 볼륨의 경우 15분마다 재해 복구 사이트의 동일한 저장소 볼륨 대상에 복제되도록 설정합니다.
- 트랜잭션 로그 백업 볼륨(스냅숏 형식 **logs**)의 경우 3분마다 재해 복구 사이트의 동일한 저장소 볼륨 대상에 복제되도록 설정합니다.

복구 지점 목표를 최소화하려면 다음을 설정합니다.
- **hana** 유형 저장소 스냅숏을 30분~1시간마다 수행합니다(7단계 - 스냅샷 수행 참조).
- SAP HANA 트랜잭션 로그 백업을 5분마다 수행합니다.
- **logs** 유형의 저장소 스냅숏은 5~15분마다 수행합니다. 이 간격 주기로 약 15-25분의 RPO를 설정합니다.

이 설정을 사용하면 트랜잭션 로그 백업의 시퀀스, 저장소 스냅숏 및 HANA 트랜잭션 로그 백업 볼륨 복제와 /hana/data, /hana/shared(/usr/sap 포함)가 다음 그래픽의 데이터와 같이 표시될 수 있습니다.

 ![시간 축에서 트랜잭션 로그 백업 스냅숏과 스냅 미러 간 관계](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

재해 복구 사례에서 RPO를 향상시키려면 HANA 트랜잭션 로그 백업을 Azure의 SAP HANA(대규모 인스턴스)에서 다른 Azure 지역에 복사할 수 있습니다. 이와 같이 RPO를 더 감소하기 위해서는 다음과 같은 단계를 수행합니다.

1. HANA 트랜잭션 로그를 가능한 한 자주 /hana/logbackups에 백업합니다.
2. rsync를 사용하여 트랜잭션 로그 백업을 NFS 공유 호스팅 Azure 가상 머신에 복사합니다. VM은 Azure 프로덕션 지역과 DR 지역의 Azure 가상 네트워크에 있습니다. 프로덕션 HANA 큰 인스턴스를 Azure에 연결하는 회로에 두 Azure 가상 네트워크를 연결해야 합니다. [HANA 큰 인스턴스를 사용한 재해 복구의 네트워크 고려 사항](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) 섹션의 그래픽을 참조하세요. 
3. 해당 지역의 트랜잭션 로그 백업을 VM이 연결된 NFS에서 내보낸 저장소에 유지합니다.
4. 재해 장애 조치(failover)의 경우 /hana/logbackups 볼륨에서 찾은 트랜잭션 로그 백업을 최근에 재해 복구 사이트의 NFS 공유에 생성한 트랜잭션 로그 백업으로 보완합니다. 
5. 트랜잭션 로그 백업을 시작하여 DR 영역에 저장할 수 있는 최신 백업으로 복원합니다.

HANA 큰 인스턴스 작업에서 복제 관계 설정을 확인하고 실행 스토리지 스냅숏 백업을 시작하면 데이터 복제가 시작됩니다.

![복제를 설정하기 전 DR 설치 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

복제가 진행되면서 DR Azure 지역의 PRD 볼륨에 있는 스냅숏이 복원되지 않습니다. 저장만 됩니다. 볼륨이 이러한 상태로 탑재되어 있다면 DR Azure 지역의 서버 단위에 PRD SAP HANA 인스턴스를 설치한 후에 해당 볼륨을 분리한 상태를 나타냅니다. 또한 아직 복원되지 않은 저장소 백업을 나타냅니다.

장애 조치(failover)가 발생하는 경우 최신 저장소 스냅숏 대신 이전 저장소 스냅숏으로 복원하도록 선택할 수도 있습니다.

## <a name="disaster-recovery-failover-procedure"></a>재해 복구 장애 조치(failover) 프로시저
DR 사이트로 장애 조치(failover)할 때 고려할 두 가지 경우가 있습니다.

- SAP HANA 데이터베이스를 최신 상태의 데이터로 되돌려야 합니다. 이 경우에 Microsoft에 문의할 필요 없이 장애 조치(failover)를 수행할 수 있는 셀프 서비스 스크립트가 있습니다. 하지만 장애 복구(failback)의 경우 Microsoft로 작업해야 합니다.
- 최신 복제된 스냅숏이 아닌 저장소 스냅숏으로 복원할 수도 있습니다. 이 경우에 Microsoft로 작업해야 합니다. 

>[!NOTE]
>다음 단계는 DR 단위를 나타내는 HANA 대규모 인스턴스 단위에서 실행되어야 합니다. 
 
최신 복제된 저장소 스냅숏을 복원하려면 다음 단계를 수행합니다. 

1. 실행 중인 HANA 대규모 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치되어 있기 때문입니다.
2. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이것을 확인하려면 다음 명령을 사용합니다. `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
3. DR 사이트 HANA 대규모 인스턴스 단위에서 *azure_hana_dr_failover.pl* 스크립트를 실행합니다. 스크립트에서 복원할 SAP HANA SID를 묻는 메시지가 표시됩니다. 요청되면 복제되었으며 DR 사이트의 HANA 대규모 인스턴스 단위에 있는 *HANABackupCustomerDetails.txt* 파일에서 유지 관리되는 하나 또는 유일한 SAP HANA SID를 입력합니다. 

      여러 SAP HANA 인스턴스를 장애 조치하려는 경우 스크립트를 여러 번 실행해야 합니다. 요청된 경우 장애 조치하고 복원하려는 SAP HANA SID를 입력합니다. 완료 시 스크립트에서 HANA 대규모 인스턴스 단위에 추가된 볼륨의 탑재 지점 목록을 표시합니다. 이 목록에는 복원된 DR 볼륨도 포함됩니다.

4. Linux 운영 체제 명령을 사용하여 복원된 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재합니다. 
6. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
7. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재된 DR/hana/logbackups 디렉터리에 병합해야 합니다. 기존 백업을 덮어쓰지 마십시오. 저장소 스냅숏의 최신 복제를 사용하여 복제되지 않은 최신 백업만 복사합니다.
8. DR Azure 지역의 /hana/shared/PRD 볼륨에 복제된 스냅숏에서 단일 파일을 복원할 수도 있습니다. 

실제 복제 관계에 영향을 주지 않고 DR 장애 조치(failover)를 테스트할 수 있습니다. 테스트 장애 조치를 수행하려면 위의 1, 2단계를 수행한 다음, 3단계를 계속 진행합니다.

>[!IMPORTANT]
>3단계에서 소개된 스크립트를 사용하여 **장애 조치(failover) 테스트** 과정을 통해 DR 사이트에서 만든 인스턴스에서 프로덕션 트랜잭션을 실행하지 *않습니다*. 해당 명령은 기본 사이트와 아무 관계도 없는 볼륨 세트를 만듭니다. 따라서 기본 사이트로 다시 동기화할 수 *없습니다*. 

테스트 장애 조치의 3단계:

DR 사이트 HANA 대규모 인스턴스 단위에서 **azure_hana_test_dr_failover.pl** 스크립트를 실행합니다. 이 스크립트는 기본 사이트와 DR 사이트 간의 복제 관계를 중지하지 *않습니다*. 대신, 이 스크립트는 DR 저장소 볼륨을 복제합니다. 복제 프로세스가 성공하면 복제된 볼륨이 최신 스냅숏 상태로 복원된 후 DR 단위에 탑재됩니다. 스크립트에서 복원할 SAP HANA SID를 묻는 메시지가 표시됩니다. 복제되었으며 DR 사이트의 HANA 대규모 인스턴스 단위에 있는 *HANABackupCustomerDetails.txt* 파일에서 유지 관리되는 하나 또는 유일한 SAP HANA SID를 입력합니다. 

여러 SAP HANA 인스턴스를 테스트하려는 경우 스크립트를 여러 번 실행해야 합니다. 요청된 경우 장애 조치를 위해 테스트하려는 인스턴스의 SAP HANA SID를 입력합니다. 완료 시 스크립트에서 HANA 대규모 인스턴스 단위에 추가된 볼륨의 탑재 지점 목록을 표시합니다. 이 목록에는 복제된 DR 볼륨도 포함됩니다.

4단계로 계속 진행합니다.

   >[!NOTE]
   >몇 시간 전에 삭제한 일부 데이터를 복구하기 위해 장애 조치(failover)하고 DR 볼륨을 이전 스냅숏으로 설정해야 하는 경우 이 프로시저가 적용됩니다. 

4. 실행 중인 HANA 대규모 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치되어 있기 때문입니다.
5. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이것을 확인하려면 다음 명령을 사용합니다. `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
6. 재해 복구 사이트를 복원하려는 스냅숏 이름 또는 SAP HANA 백업 ID를 결정합니다. 실제 재해 복구의 경우 이 스냅숏이 일반적으로 최신 스냅숏입니다. 손실된 데이터를 복구해야 하는 경우 이전 스냅숏을 선택하십시오.
7. 우선 순위가 높은 지원 요청을 통해 Azure 지원에 문의합니다. DR 사이트에서 해당 스냅숏(스냅숏의 이름 및 날짜를 포함) 또는 HANA 백업 ID의 복원을 요청합니다. 기본값은 작업 쪽에서 /hana/data 볼륨만 복원하는 것입니다. /hana/logbackups 볼륨도 필요하면 구체적으로 명시해야 합니다. */hana/shared 볼륨을 복원하지 마십시오.* 대신 PRD에서 /hana/shared 볼륨을 다시 탑재한 후에 **.snapshot** 디렉터리 및 해당 하위 디렉터리에서 특정 파일(예: global.ini)를 선택해야 합니다. 

   작업 쪽에서 다음 단계가 수행됩니다.

   a. 프로덕션 볼륨에서 재해 복구 볼륨으로의 스냅숏 복제는 중지됩니다. 프로덕션 사이트의 중단으로 인해 재해 복구 절차를 수행해야 하는 경우 이러한 중단이 이미 발생했을 수 있습니다.
   
   나. 선택한 백업 ID에 해당하는 스냅숏 또는 저장소 스냅숏 이름이 재해 복구 볼륨에 복원됩니다.
   
   다. 복원 후에는 재해 복구 볼륨을 재해 복구 지역의 HANA 대규모 인스턴스 단위에 탑재할 수 있습니다.
      
8. 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재하십시오. 
9. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
10. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재된 DR/hana/logbackups 디렉터리에 병합해야 합니다. 기존 백업을 덮어쓰지 마십시오. 저장소 스냅숏의 최신 복제를 사용하여 복제되지 않은 최신 백업만 복사합니다.
11. DR Azure 지역의 /hana/shared/PRD 볼륨에 복제된 스냅숏에서 단일 파일을 복원할 수도 있습니다.

단계의 다음 시퀀스에서는 복원된 저장소 스냅숏 및 사용 가능한 트랜잭션 로그 백업에 따라 SAP HANA 프로덕션 인스턴스가 복구됩니다.

1. SAP HANA Studio를 사용하여 백업 위치를 **/hana/logbackups**로 변경합니다.
   ![DR 복구를 위한 백업 위치 변경](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA는 백업 파일 위치를 검색하고, 가장 최근 트랜잭션 로그 백업을 복원할 것을 제안합니다. 아래와 같은 화면이 표시될 때까지 검사하는 데 몇 분 정도가 소요될 수 있습니다. ![DR 복구를 위한 트랜잭션 로그 백업 목록](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. 일부 기본 설정을 조정합니다.

      - **델타 백업 사용** 선택을 취소합니다.
      - **로그 영역 초기화**를 선택합니다.

   ![로그 영역 초기화 설정](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. **마침**을 선택합니다.

   ![DR 복원 완료](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

여기에 표시된 것처럼 진행률 창이 표시됩니다. 해당 예제는 3노드 스케일 아웃 SAP HANA 구성의 재해 복구 복원에 대한 것입니다.

![복원 진행률](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

복원이 **마침** 화면에서 중단된 것처럼 보이고 진행률 화면이 표시되지 않는 경우 작업자 노드의 모든 SAP HANA 인스턴스가 실행되고 있는지 확인합니다. 필요한 경우 SAP HANA 인스턴스를 수동으로 시작합니다.


### <a name="failback-from-a-dr-to-a-production-site"></a>DR에서 프로덕션 사이트로 장애 복구(Failback)
DR에서 프로덕션 사이트로 장애 복구(Failback)할 수 있습니다. 재해 복구 사이트로의 장애 조치가 손실된 데이터를 복구할 필요가 아닌 프로덕션 Azure 지역의 문제로 인해 발생한 시나리오를 살펴보겠습니다. 재해 복구 사이트에서 한동안 SAP 프로덕션 워크로드를 실행했습니다. 프로덕션 사이트의 문제가 해결되면 프로덕션 사이트로 장애 복구(Failback)하려고 할 수 있습니다. 데이터가 손실되도록 할 수는 없으므로 프로덕션 사이트로 돌아가는 단계는 Azure 운영 팀의 SAP HANA와의 몇 가지 단계 및 긴밀한 협조가 필요합니다. 문제가 해결되었을 때 운영 팀이 프로덕션 사이트로 다시 동기화를 시작하도록 트리거하는 것은 사용자의 몫입니다.

수행할 단계의 시퀀스입니다.

1. Azure 운영 팀의 SAP HANA는 프로덕션 상태를 나타내는 재해 복구 저장소 볼륨에서 프로덕션 저장소 볼륨을 동기화하는 트리거를 가져옵니다. 이 상태에서 프로덕션 사이트에 있는 HANA 큰 인스턴스 단위가 종료됩니다.
2. Azure 운영 팀의 SAP HANA는 복제를 모니터링하고 사용자에게 알리기 전에 인식해야 합니다.
3. 재해 복구 사이트에서 프로덕션 HANA 인스턴스를 사용하는 응용 프로그램을 종료합니다. 그런 다음, HANA 트랜잭션 로그 백업을 수행합니다. 다음으로 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행되는 HANA 인스턴스를 중지합니다.
4. 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행 중인 HANA 인스턴스가 종료된 후에 운영 팀은 수동으로 디스크 볼륨을 다시 동기화합니다.
5. Azure 운영 팀의 SAP HANA가 프로덕션 사이트에서 HANA 큰 인스턴스 단위를 다시 시작한 후 사용자에게 전달합니다. HANA 대규모 인스턴스 단위의 시작 시간에 SAP HANA 인스턴스가 종료된 상태인지 확인합니다.
6. 이전에 재해 복구 사이트로 장애 조치(failover)를 수행할 때와 동일한 데이터베이스 복원 단계를 수행합니다.

### <a name="monitor-disaster-recovery-replication"></a>재해 복구 복제 모니터링

`azure_hana_replication_status.pl` 스크립트를 실행하여 저장소 복제 진행 상태를 모니터링할 수 있습니다. 이 스크립트를 재해 복구 위치에서 실행되는 단위에서 실행하여 예상되는 대로 작동시켜야 합니다. 이 스크립트는 복제가 활성 상태인지 여부에 관계없이 작동합니다. 이 스크립트는 재해 복구 위치에서 테넌트의 모든 HANA 큰 인스턴스 단위에 대해 실행할 수 있습니다. 부팅 볼륨에 대한 세부 정보를 가져오는 데는 사용할 수 없습니다.

이 명령을 사용하여 스크립트를 호출합니다.
```
./replication_status.pl <HANA SID>
```

출력은 볼륨별로 다음 섹션으로 세분화됩니다.  

- 연결 상태
- 현재 복제 작업
- 마지막에 복제된 스냅숏 
- 최신 스냅숏의 크기
- 스냅숏 간 현재 지연 시간(마지막으로 완료된 스냅숏 복제와 현재 간)

위치 간의 연결이 끊어지지 않고 현재 장애 조치(failover) 이벤트가 진행 중인 경우 연결 상태는 **활성**으로 표시됩니다. 복제 작업은 현재 복제 중이거나 유휴 상태인 데이터가 있는지 여부 또는 현재 연결에서 수행되고 있는 다른 작업이 있는지를 확인합니다. 마지막에 복제된 스냅숏은 `snapmirror…`로만 표시됩니다. 그런 후 마지막 스냅숏의 크기가 표시됩니다. 마지막으로 지연 시간이 표시됩니다. 지연 시간은 예약된 복제부터 복제가 완료될 때까지의 시간을 나타냅니다. 복제가 시작된 경우에도 데이터 복제의 지연 시간은 1시간을 초과할 수 있습니다(특히 초기 복제에서). 지연 시간은 진행 중인 복제가 완료될 때까지 계속 증가합니다.

출력의 예제는 다음과 같습니다.

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












