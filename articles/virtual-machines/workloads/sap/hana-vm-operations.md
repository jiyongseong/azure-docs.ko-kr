---
title: Azure에서 SAP HANA 운영 | Microsoft Docs
description: Azure Virtual Machines에 배포된 SAP HANA 시스템에 대한 운영 가이드입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 959a483d293caa45180c946e92ac824fc56db084
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195042"
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure의 SAP HANA 운영 가이드
이 문서에서는 Azure VM(Virtual Machines)에 배포된 SAP HANA 시스템 운영을 위한 지침을 제공합니다. 이 문서는 다음 내용을 포함하는 표준 SAP 설명서를 대체하기 위한 것이 아닙니다.

- [SAP administration guide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)(SAP 관리 가이드)
- [SAP installation guides](https://service.sap.com/instguides)(SAP 설치 가이드)
- [SAP notes](https://sservice.sap.com/notes)(SAP 참고)

## <a name="prerequisites"></a>필수 조건
이 가이드를 사용하려면 다음 Azure 구성 요소에 대한 기본 지식이 필요합니다.

- [Azure 가상 머신](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 네트워킹 및 가상 네트워크](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure의 SAP NetWeaver 및 기타 SAP 구성 요소에 대해 자세히 알아보려면 [Azure 설명서](https://docs.microsoft.com/azure/)의 [Azure의 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 섹션을 참조하세요.

## <a name="basic-setup-considerations"></a>기본 설치 고려 사항
다음 섹션에서는 Azure VM에 SAP HANA 시스템을 배포하기 위한 기본 설치 고려 사항을 설명합니다.

### <a name="connect-into-azure-virtual-machines"></a>Azure Virtual Machines에 연결
[Azure Virtual Machines 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에 설명된 대로 Azure VM에 연결하기 위한 두 가지 기본 방법이 있습니다.

- SAP HANA를 실행하는 VM 또는 Jump VM의 공개 끝점과 인터넷을 통해 연결
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 연결

VPN 또는 ExpressRoute를 통한 사이트 간 연결은 프로덕션 시나리오에 필요합니다. 이 유형의 연결은 SAP 소프트웨어가 사용되는 프로덕션 시나리오로 제공되는 비프로덕션 시나리오에도 필요합니다. 다음 그림은 사이트 간 연결의 예를 보여 줍니다.

![사이트 간 연결](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM 유형 선택
프로덕션 시나리오에 사용할 수 있는 Azure VM 유형은 [SAP documentation for IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)(IAAS에 대한 SAP 설명서)에 나와 있습니다. 비프로덕션 시나리오의 경우 더 다양한 네이티브 Azure VM 유형을 사용할 수 있습니다.

>[!NOTE]
> 비프로덕션 시나리오의 경우 [SAP note #1928533](https://launchpad.support.sap.com/#/notes/1928533)(SAP 참고 #1928533)에 나열된 VM 유형을 사용합니다. 프로덕션 시나리오에 대한 Azure VM의 사용을 위해 SAP 게시된 [인증된 IaaS 플랫폼 목록](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP HANA 인증된 VM을 확인합니다.

다음을 사용하여 Azure에 VM을 배포합니다.

- Azure Portal
- Azure PowerShell cmdlet
- Azure CLI

[SAP 클라우드 플랫폼](https://cal.sap.com/)을 통해 Azure VM 서비스에 전체 설치된 SAP HANA 플랫폼을 배포할 수도 있습니다. 설치 프로세스는 [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) 또는 [여기](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)에서 릴리스된 자동화로 설명되어 있습니다.

### <a name="choose-azure-storage-type"></a>Azure Storage 유형 선택
Azure에서는 SAP HANA를 실행하는 Azure VM에 적합한 두 가지 저장소 유형을 제공합니다.

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요.

IOPS 및 저장소 처리량에서 저장소 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Azure 가상 머신에 대한 저장소 구성

현재까지 온-프레미스에 대한 SAP HANA 어플라이언스를 유지했으므로 어플라이언스 공급 업체에서 SAP HANA에 대한 최소 저장소 요구 사항이 충족되는지 확인해야 하기 때문에 I/O 하위 시스템 및 해당 기능에 대해 염려할 필요가 없었습니다. Azure 인프라를 직접 작성함에 따라 다음 섹션에서 제안하는 구성 요구 사항을 이해하기 위해 해당 요구 사항 중 일부를 숙지해야 합니다. 또는 SAP HANA를 실행하려는 Virtual Machines를 구성하는 경우입니다. 요청되는 특성의 일부는 다음을 수행해야 합니다.

- 최소 1MB I/O 크기로 250MB/초의 /hana/log에서 볼륨 읽기/쓰기 활성화
- 16MB 및 64MB I/O 크기의 /hana/data에 대한 최소 400MB/초의 읽기 작업 활성화
- 16MB 및 64MB I/O 크기의 /hana/data에 대한 최소 250MB/초의 쓰기 작업 활성화

낮은 저장소 대기 시간을 지정하는 것은 메모리 내 데이터를 유지하는 SAP HANA와 같은 DBMS 시스템에 중요합니다. 저장소에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 /hana/data 및 /hana/log 볼륨에는 Azure Premium Disk를 활용하는 것이 필수입니다. SAP에서의 필요에 따라 /hana/log 및 /hana/data의 최소 처리량을 달성하기 위해 여러 Azure Premium Storage 디스크에 대한 MDADM 또는 LVM을 사용하여 RAID 0을 빌드하고 /hana/data 및 /hana/log 볼륨으로 RAID 볼륨을 사용해야 합니다. RAID 0에 대한 스트라이프 크기로 다음을 사용하는 것이 좋습니다.

- /hana/data에 대해 64K 또는 128K
- /hana/log에 대해 32K

> [!NOTE]
> Azure Premium과 Standard 저장소는 세 개의 VHD 이미지를 유지하므로 RAID 볼륨을 사용하여 모든 중복 수준을 구성할 필요가 없습니다. RAID 볼륨의 사용법은 순수하게 충분한 I/O 처리량을 제공하는 볼륨을 구성하는 것입니다.

RAID 아래의 Azure VHD 수 누적은 IOPS 및 저장소 처리량 측면의 누적입니다. 따라서 3 x P30 Azure Premium Storage 디스크에 대해 RAID 0을 설정하는 경우 IOPS의 세 배 및 단일 Azure Premium Storage P30 디스크의 저장소 처리량의 세 배를 제공해야 합니다.

hana/data 및 /hana/log에 사용되는 디스크에 대한 Premium Storage 캐싱을 구성하지 마십시오. 이러한 볼륨을 빌드하는 모든 디스크는 해당 디스크의 캐싱을 '없음'으로 설정해야 합니다.

VM의 크기를 조정하거나 결정할 때도 전체 VM I/O 처리량을 고려하세요. 전체 VM 저장소 처리량은 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 문서에 설명되어 있습니다.

#### <a name="cost-conscious-azure-storage-configuration"></a>비용 인식 Azure Storage 구성
다음 표에서는 고객이 Azure VM에서 SAP HANA를 호스트하는 데 일반적으로 사용하는 VM 유형의 구성을 보여줍니다. SAP HANA에 대한 모든 최소 조건을 충족하지 못할 수 있는 일부 VM 유형이 있을 수 있습니다. 하지만 지금까지 해당 VM은 비-프로덕션 시나리오에 대해 제대로 수행되는 것처럼 보였습니다. 

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.



| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128GiB | 768MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128GiB | 384MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256GiB | 768MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GiB | 1200MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448GiB | 2000MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000GiB | 1000MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 저장소 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 /hana/backup 볼륨을 변경해야 하는 경우 자유롭게 조정할 수 있습니다.   
제안된 다른 볼륨에 대한 저장소 처리량이 실행하려는 작업을 충족하는지 여부를 확인합니다. 작업에 /hana/data 및 /hana/log에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다. 

> [!NOTE]
> 위의 구성은 Azure Premium Storage와 Azure Standard Storage의 혼합을 사용하지 않기 때문에 [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)의 이점을 얻지 못합니다. 그러나 비용을 최적화하기 위해 선택 영역이 선택되었습니다.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>단일 VM SLA을 충족하는 이점을 활용하도록 Azure Storage 구성
[Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)의 이점을 활용하려는 경우 Azure Premium Storage VHD를 단독으로 사용해야 합니다.

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128GiB | 768MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128GiB | 384MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256GiB | 768MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443GiB | 1200MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448GiB | 2000MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000GiB | 1000MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 저장소 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 /hana/backup 볼륨을 변경해야 하는 경우 자유롭게 조정할 수 있습니다.  
제안된 다른 볼륨에 대한 저장소 처리량이 실행하려는 작업을 충족하는지 여부를 확인합니다. 작업에 /hana/data 및 /hana/log에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 가상 머신용 Azure Write Accelerator를 사용하는 저장소 솔루션
Azure Write Accelerator는 M 시리즈 VM 전용으로 출시되는 기능입니다. 이름에서 알 수 있듯이 이 기능은 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. SAP HANA의 경우 Write Accelerator는 /hana/log 볼륨에 대해서만 사용해야 합니다. 따라서 앞에서 설명한 구성을 변경해야 합니다. /hana/log 볼륨에 대해서만 Azure Write Accelerator를 사용하기 위한 주요 변경 내용은 /hana/data와 /hana/log 간의 구분입니다. 

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 /hana/log 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 /hana/log 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

권장 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | /hana/log | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000GiB | 1000MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750GiB | 1000MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000GiB | 2000MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800GiB | 2000MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

제안된 다른 볼륨에 대한 저장소 처리량이 실행하려는 작업을 충족하는지 여부를 확인합니다. 작업에 /hana/data 및 /hana/log에 대한 더 높은 볼륨이 필요한 경우 Azure Premium Storage VHD의 수를 증가시켜야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 따라서 /hana/log 볼륨을 형성하는 Azure Premium Storage 디스크는 관리되는 디스크로 배포되어야 합니다.

Azure Write Accelerator에서 지원할 수 있는 VM당 Azure Premium Storage VHD 수는 제한됩니다. 이 제한은 현재 다음과 같습니다.

- M128xx VM당 16개 VHD
- M64xx VM당 8개 VHD

Azure Write Accelerator를 활성화하는 방법에 대한 자세한 지침은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 문서에서 확인할 수 있습니다.

Azure Write Accelerator에 대한 세부 정보 및 제한 사항은 동일한 설명서에서 찾을 수 있습니다.


### <a name="set-up-azure-virtual-networks"></a>Azure 가상 네트워크 설정
VPN 또는 ExpressRoute를 통해 Azure로의 사이트 간 연결이 있다면 가상 게이트웨이를 통해 VPN 또는 ExpressRoute 회로에 연결된 [Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)가 하나 이상 있어야 합니다. 가상 게이트웨이는 Azure 가상 네트워크의 서브넷에서 실행됩니다. SAP HANA를 설치하려면 가상 네트워크 내에 두 개의 서브넷을 추가로 만듭니다. 한 서브넷은 SAP HANA 인스턴스를 실행하는 VM을 호스트하고, 다른 서브넷은 SAP HANA Studio 또는 기타 관리 소프트웨어를 호스트하는 Jumpbox 또는 관리 VM을 실행합니다.

SAP HANA를 실행할 VM을 설치할 때 VM에는 다음이 필요합니다.

- 두 가상 NIC 설치: 하나의 NIC는 관리 서브넷에 연결되고 다른 NIC는 온-프레미스 네트워크나 다른 네트워크에서 Azure VM의 SAP HANA 인스턴스에 연결됩니다.
- 두 가상 NIC에 대해 배포된 고정 개인 IP 주소

IP 주소를 할당하는 다른 방법에 대한 개요는 [IP 주소 유형 및 Azure에서 할당 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)을 참조하세요. 

SAP HANA를 실행하는 VM의 경우 할당된 고정 IP 주소를 사용해야 합니다. HANA에 대한 일부 구성 특성은 IP 주소를 참조하기 때문입니다.

[Azure NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)는 SAP HANA 인스턴스 또는 Jumpbox로 라우팅된 트래픽을 전달하는 데 사용됩니다. NSG는 SAP HANA 서브넷 및 관리 서브넷에 연결됩니다.

다음 그림은 SAP HANA의 대략적인 배포 스키마를 간단하게 보여 줍니다.

![SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking.PNG)


사이트 간 연결 없이 Azure에 SAP HANA를 배포하려면 공용 IP 주소를 통해 SAP HANA 인스턴스에 액세스합니다. Jumpbox VM을 실행하는 Azure VM에 이 IP 주소를 할당해야 합니다. 이러한 기본 시나리오의 배포에서는 Azure 기본 제공 DNS 서비스가 호스트 이름을 확인해야 합니다. 공용 IP 주소가 사용되는 더 복잡한 배포에서는 Azure 기본 제공 DNS 서비스가 특히 중요합니다. Azure NSG를 사용하여 공용 IP 주소를 사용하는 자산이 있는 Azure 서브넷에 연결할 수 있는 열린 포트 또는 IP 주소 범위를 제한합니다. 다음 그림은 사이트 간 연결 없이 SAP HANA를 배포하는 경우의 대략적인 스키마를 보여 줍니다.
  
![사이트 간 연결 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM에서 SAP HANA를 배포하기 위한 작업
다음 섹션에서는 Azure VM에서 SAP HANA 시스템을 배포하는 일과 관련된 몇 가지 작업을 설명합니다.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM의 백업 및 복원 작업
다음 문서에서는 SAP HANA 배포를 백업 및 복원하는 방법을 설명합니다.

- [SAP HANA 백업 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 파일 수준 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 저장소 스냅숏 벤치마크](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA를 포함하는 VM 시작 및 다시 시작
Azure 공용 클라우드의 두드러진 기능으로 사용한 컴퓨팅 시간(분)에 대해서만 요금이 부과되는 것을 들 수 있습니다. 예를 들어 SAP HANA를 실행 중인 VM을 종료하는 경우 해당 시간 동안의 저장소 비용만 청구됩니다. 초기 배포에서 VM에 대해 고정 IP 주소를 지정하는 경우 또 다른 기능을 사용할 수 있습니다. SAP HANA가 있는 VM을 다시 시작하면 VM이 이전 IP 주소로 다시 시작됩니다. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP 원격 지원에 SAProuter 사용
온-프레미스 위치와 Azure 사이에 사이트 간 연결이 있고 SAP 구성 요소를 실행하고 있는 경우 이미 SAProuter를 실행 중일 것입니다. 이 경우 원격 지원을 위해 다음 항목을 완료하세요.

- SAP HANA를 호스트하는 VM의 개인 및 고정 IP 주소를 SAProuter 구성에서 유지 관리합니다.
- HANA VM을 호스트하는 서브넷의 NSG에서 TCP/IP 포트 3299를 통해 트래픽을 허용하도록 구성합니다.

인터넷을 통해 Azure에 연결 중이고 SAP HANA를 사용하는 VM에 대한 SAP 라우터가 없는 경우 이 구성 요소를 설치해야 합니다. 관리 서브넷에서 별도의 VM에 SAProuter를 설치합니다. 다음 그림은 사이트 간 연결 없이 SAProuter를 사용하여 SAP HANA를 배포하는 경우의 대략적인 스키마를 보여 줍니다.

![사이트 간 연결 및 SAProuter 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter는 Jumpbox VM이 아니라 별도의 VM에 설치해야 합니다. 별도의 VM에는 고정 IP 주소가 있어야 합니다. SAProuter를 SAP에서 호스트하는 SAProuter에 연결하려면 SAP에 IP 주소를 문의하세요. (SAP에서 호스트하는 SAProuter는 VM에 설치하는 SAProuter 인스턴스에 해당합니다.) SAP의 IP 주소를 사용하여 SAProuter 인스턴스를 구성합니다. 구성 설정에서 필수 포트는 TCP 포트 3299뿐입니다.

SAProuter를 통해 원격 지원 연결을 설정하고 유지 관리하는 방법에 대한 자세한 내용은 [SAP documentation](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)(SAP 설명서)을 참조하세요.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 네이티브 VM의 SAP HANA 고가용성
SUSE Linux 12 SP1 이상을 실행 중인 경우 STONITH 장치를 포함하는 Pacemaker 클러스터를 설정할 수 있습니다. 이 장치를 사용하면 HANA 시스템 복제를 통한 동기 복제와 자동 장애 조치(failover)를 사용하는 SAP HANA 구성을 설정할 수 있습니다. 설정 절차에 대한 자세한 내용은 [Azure 가상 머신에 대한 SAP HANA 고가용성 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)를 참조하세요.
