---
title: SAP HANA on Azure(대규모 인스턴스) 개요 및 아키텍처 | Microsoft Docs
description: SAP HANA on Azure(대규모 인스턴스)를 배포하는 방법에 대한 아키텍처 개요입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3342f3057917202d81359a27accf47ba288b128
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077626"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure(대규모 인스턴스)란?

SAP HANA on Azure(대규모 인스턴스)는 Azure의 고유한 솔루션입니다. Azure는 SAP HANA를 배포하고 실행하기 위한 가상 머신을 제공하는 것 외에도, 운영 체제 미설치 전용 서버에서 SAP HANA를 실행하고 배포할 수 있는 가능성을 제공합니다. SAP HANA on Azure(대규모 인스턴스) 솔루션은 사용자에게 할당된 비공유 호스트/서버 운영 체제 미설치 하드웨어를 기반으로 합니다. 서버 하드웨어는 계산/서버, 네트워킹 및 스토리지 인프라가 포함된 큰 스탬프 안에 내장됩니다. 이는 하나의 조합으로 HANA TDI(맞춤형 데이터 센터 통합) 인증을 받았습니다. SAP HANA on Azure(대규모 인스턴스)는 서로 다른 서버 SKU 또는 크기를 제공합니다. 장치는 72개 CPU와 768GB 메모리를 갖출 수 있으며, 최대 960개 CPU와 20TB 메모리를 갖춘 장치로 이동할 수 있습니다.

인프라 스탬프 내의 고객 격리는 테넌트에서 수행되며 다음과 같습니다.

- **네트워킹**: 고객에게 할당된 테넌트별 가상 네트워크를 통해 인프라 스택 내에서 고객이 격리됩니다. 테넌트는 단일 고객에게 할당됩니다. 고객은 여러 개의 테넌트를 둘 수 있습니다. 테넌트가 동일한 고객에게 속해 있더라도 테넌트의 네트워크 격리는 인프라 스탬프 수준에서 테넌트 간 네트워크 통신을 금지합니다.
- **저장소 구성 요소**: 저장소 볼륨이 할당된 저장소 가상 머신을 통해 격리됩니다. 저장소 볼륨은 하나의 스토리지 가상 머신에만 할당될 수 있습니다. 저장소 가상 머신은 SAP HANA TDI 인증 인프라 스택에서 단일 테넌트에만 독점적으로 할당됩니다. 따라서 저장소 가상 머신에 할당된 저장소 볼륨은 관련된 특정 단일 테넌트에서만 액세스할 수 있으며, 배포된 다른 테넌트에서는 표시되지 않습니다.
- **서버 또는 호스트**: 서버 또는 호스트 장치가 고객 또는 테넌트 간에 공유되지 않습니다. 고객에게 배포되는 서버 또는 호스트는 단일 테넌트에 할당되는 원자성 베어 메탈 계산 단위입니다. 다른 고객과 호스트 또는 서버를 다른 고객과 공유할 수 있는 하드웨어 분할 또는 소프트 분할은 *사용되지 않습니다*. 특정 테넌트의 저장소 가상 머신에 할당되는 저장소 볼륨은 그러한 서버에 탑재됩니다. 테넌트에는 다양한 SKU의 하나 또는 다수의 서버 단위가 할당될 수 있습니다.
- SAP HANA on Azure(대규모 인스턴스) 인프라 스탬프 내에서 네트워킹, 저장소 및 계산 수준에 대한 테넌트 개념을 통해 서로 다른 여러 테넌트가 배포되고 서로 간에 격리됩니다. 


이러한 베어 메탈 서버 단위는 SAP HANA 실행만 지원합니다. SAP 응용 프로그램 계층 또는 워크로드 미들웨어 계층은 가상 머신에서 실행됩니다. SAP HANA on Azure(대규모 인스턴스) 장치를 실행하는 인프라 스탬프는 Azure 네트워크 서비스 백본에 연결됩니다. 이러한 방식으로 SAP HANA on Azure(대규모 인스턴스) 장치와 가상 머신 간의 짧은 대기 시간 연결이 제공됩니다.

이 문서는 SAP HANA on Azure(대규모 인스턴스)를 다루는 여러 문서 중 하나입니다. 여기서는 솔루션에서 제공하는 기본 아키텍처, 역할 및 서비스를 소개합니다. 솔루션의 고급 기능에 대해서도 설명합니다. 네트워킹 및 연결과 같은 대부분의 다른 영역에 대해서는 네 가지 다른 문서에서 세부 정보와 드릴다운 정보를 다루고 있습니다. SAP HANA on Azure(대규모 인스턴스) 설명서에서는 VM에 SAP NetWeaver를 설치하거나 배포하는 상황에 대해 다루지 않습니다. Azure의 SAP NetWeaver는 동일한 Azure 문서 컨테이너에 있는 별도의 문서에서 다룹니다. 


HANA 대규모 인스턴스 지침의 다른 문서는 다음 영역을 다룹니다.

- [SAP HANA on Azure(대규모 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure(대규모 인스턴스) 설치 및 구성](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure(대규모 인스턴스) 문제 해결 및 모니터링](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [STONITH를 사용하여 SUSE에서 고가용성 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [형식 II SKU에 대한 OS 백업 및 복원](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>정의

아키텍처 및 기술적 배포 가이드에는 여러 가지 일반적인 정의가 광범위하게 사용됩니다. 다음 용어와 의미에 유의합니다.

- **IaaS**: Infrastructure as a Service
- **PaaS**: Platform as a Service
- **SaaS**: Software as a service
- **SAP 구성 요소**: ECC(ERP 중앙 구성 요소), BW(비즈니스 웨어하우스), 솔루션 관리자 또는 EP(엔터프라이즈 포털)과 같은 개별 SAP 응용 프로그램입니다. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 응용 프로그램을 기반으로 사용할 수 있습니다.
- **SAP 환경:** 개발, 품질 보증, 교육, 재해 복구 또는 프로덕션과 같은 비즈니스 기능을 수행하기 위해 논리적으로 그룹화된 하나 이상의 SAP 구성 요소입니다.
- **SAP 자산:** IT 환경의 SAP 자산 전체를 나타냅니다. SAP 지형에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
- **SAP 시스템**: DBMS 계층과 응용 프로그램 계층(예: SAP ERP 개발 시스템, SAP BW 테스트 시스템, SAP CRM 프로덕션 시스템)의 조합입니다. Azure 배포는 온-프레미스와 Azure 간에 이러한 두 계층의 분할을 지원하지 않습니다. SAP 시스템은 온-프레미스 또는 Azure에 배포됩니다. SAP 자산의 서로 다른 시스템은 Azure 또는 온-프레미스에 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포하고, 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다. SAP HANA on Azure(대규모 인스턴스)의 경우 VM에서 SAP 시스템의 SAP 응용 프로그램 계층을 호스팅하고, SAP HANA on Azure(대규모 인스턴스) 스탬프의 장치에서 관련된 SAP HANA 인스턴스를 호스팅하기 위한 것입니다.
- **대규모 인스턴스 스탬프:** SAP HANA TDI 인증을 받고 Azure 내에서 SAP HANA 인스턴스를 전용으로 실행하는 하드웨어 인프라 스택입니다.
- **SAP HANA on Azure(대규모 인스턴스):** 여러 Azure 지역의 대규모 인스턴스 스탬프에 배포된 SAP HANA TDI 인증 하드웨어에서 HANA 인스턴스를 실행하는 Azure 제품에 대한 공식 이름입니다. *HANA 대규모 인스턴스* 관련 용어는 *SAP HANA on Azure(대규모 인스턴스)* 의 약어이며 기술적 배포 가이드에서 널리 사용됩니다.
- **프레미스 간**: VM이 온-프레미스 데이터 센터와 Azure 간에 사이트 간, 다중 사이트 또는 ExpressRoute 방식으로 연결된 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 시나리오라고도 합니다. 이러한 연결은 온-프레미스 도메인, 온-프레미스 Azure Active Directory/OpenLDAP 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 Azure 구독의 Azure 자산으로 확장됩니다. 이 확장을 사용하면 VM이 온-프레미스 도메인에 속할 수 있습니다. 

   온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고, 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오는 대부분의 SAP 자산이 배포되는 전형적인 방식입니다. 자세한 내용은 [Azure VPN Gateway 계획 및 설계](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [Azure Portal을 사용하여 사이트 간 연결이 있는 가상 네트워크 만들기](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
- **테넌트:** HANA 대규모 인스턴스 스탬프에 배포된 고객은 *테넌트*로 격리됩니다. 테넌트는 네트워킹, 저장소 및 계산 계층에서 다른 테넌트로부터 격리됩니다. 다른 테넌트에 할당된 저장소 및 계산 장치는 HANA 대규모 인스턴스 스탬프 수준에서 서로 표시하거나 통신할 수 없습니다. 고객은 다양한 테넌트에 배포하도록 선택할 수 있습니다. 그러한 경우에도 HANA 대규모 인스턴스 스탬프 수준에서 테넌트 간 통신은 없습니다.
- **SKU 범주:** HANA 대규모 인스턴스의 경우 SKU는 다음 두 가지 범주로 제공됩니다.
    - **유형 I 클래스:** S72, S72m, S144, S144m, S192 및 S192m
    - **유형 II 클래스**: S384, S384m, S384xm, S576m, S768m 및 S960m


클라우드에서 SAP 워크로드를 배포하는 방법에 대해 다양한 추가 리소스를 사용할 수 있습니다. Azure에서 SAP HANA 배포를 실행하려는 경우, Azure IaaS의 원칙 및 Azure IaaS에 대한 SAP 워크로드 배포를 경험하고 숙지해야 합니다. 계속하기 전에 먼저 [Azure 가상 머신에서 SAP 솔루션 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

## <a name="certification"></a>인증

NetWeaver 인증 외에도, SAP에서는 Azure IaaS와 같은 특정 인프라에서 SAP HANA를 지원하기 위해 SAP HANA에 대한 특수한 인증을 요구합니다.

NetWeaver 및 약간의 SAP HANA 인증에 대한 핵심 SAP Note는 [SAP Note #1928533 – Azure의 SAP 응용 프로그램: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)입니다.

[SAP Note #2316233 - SAP HANA on Microsoft Azure(대규모 인스턴스)](https://launchpad.support.sap.com/#/notes/2316233/E)도 중요합니다. 이 가이드에 설명된 솔루션에 대해 다룹니다. 또한 Azure의 GS5 VM 유형에서 SAP HANA를 실행할 수 있습니다. 이 사례에 대한 정보는 [SAP 웹 사이트](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)에 게시됩니다.

SAP Note #2316233에서 언급한 SAP HANA on Azure(대규모 인스턴스) 솔루션은 Microsoft 및 SAP 고객에게 Azure에서 대규모 SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA 또는 기타 SAP HANA 워크로드를 배포할 수 있는 기능을 제공합니다. 이 솔루션은 SAP-HANA 인증 전용 하드웨어 스탬프([SAP HANA 맞춤형 TDI(데이터 센터 통합)](https://scn.sap.com/docs/DOC-63140))를 기반으로 합니다. SAP HANA TDI 구성 솔루션을 실행하면 모든 SAP HANA 기반 응용 프로그램(예: SAP Business Suite on SAP HANA, SAP BW on SAP HANA, S4/HANA 및 BW4/HANA)이 하드웨어 인프라에서 작동합니다.

VM에서 SAP HANA를 실행하는 것과 비교할 때 이 솔루션에 이점이 있습니다. 훨씬 더 큰 메모리 용량을 제공합니다. 이 솔루션을 사용하려면 다음 주요 사항을 이해해야 합니다.

- SAP 응용 프로그램 계층 및 비SAP 응용 프로그램은 일반적인 Azure 하드웨어 스탬프에서 호스팅되는 VM에서 실행됩니다.
- 고객 온-프레미스 인프라, 데이터 센터 및 응용 프로그램 배포는 ExpressRoute(권장) 또는 VPN(가상 사설망)을 통해 클라우드 플랫폼에 연결됩니다. Active Directory 및 DNS도 Azure로 확장됩니다.
- HANA 워크로드에 대한 SAP HANA 데이터베이스 인스턴스는 Azure(큰 인스턴스)의 SAP HANA에서 실행됩니다. 대규모 인스턴스 스탬프는 Azure 네트워킹에 연결되므로 VM에서 실행되는 소프트웨어는 HANA 대규모 인스턴스에서 실행되는 HANA 인스턴스와 상호 작용할 수 있습니다.
- SAP HANA on Azure(대규모 인스턴스) 하드웨어는 SUSE Linux Enterprise Server 또는 Red Hat Enterprise Linux가 미리 설치되어 있는 IaaS에서 제공되는 전용 하드웨어입니다. 가상 머신과 마찬가지로 운영 체제에 대한 추가 업데이트 및 유지 관리는 사용자의 책임입니다.
- HANA 대규모 인스턴스 장치에서 SAP HANA를 실행하는 데 필요한 HANA 또는 추가 구성 요소의 설치는 사용자의 책임입니다. 진행 중인 SAP HANA on Azure 개별 작업 및 관리도 모두 사용자의 책임입니다.
- 여기에 설명된 솔루션 외에도 Azure(큰 인스턴스)에서 SAP HANA에 연결되는 Azure 구독에 다른 구성 요소를 설치할 수 있습니다. 예를 들어 SAP HANA 데이터베이스와 통신하거나 직접 사용할 수 있게 하는 구성 요소(예: 점프 서버, RDP 서버, SAP HANA Studio, SAP BI용 SAP Data Services 시나리오, 네트워크 모니터링 솔루션)가 있습니다.
- Azure와 마찬가지로 HANA 대규모 인스턴스는 고가용성 및 재해 복구 기능을 지원합니다.

## <a name="architecture"></a>아키텍처

SAP HANA on Azure(대규모 인스턴스) 솔루션에는 높은 수준에서 VM에 상주하는 SAP 응용 프로그램 계층이 있습니다. 데이터베이스 계층은 Azure IaaS에 연결된 동일한 Azure 지역의 대규모 인스턴스 스탬프에 있는 SAP TDI 구성 하드웨어에 상주합니다.

> [!NOTE]
> SAP 응용 프로그램 계층은 SAP DBMS 계층과 동일한 Azure 지역에 배포합니다. 이 규칙은 Azure의 SAP 워크로드에 대해 게시된 정보에 잘 설명되어 있습니다. 

SAP HANA on Azure(대규모 인스턴스)의 전체 아키텍처는 SAP TDI 인증 하드웨어(가상화되지 않은 SAP HANA 데이터베이스용 운영 체제 미설치 고성능 서버) 구성을 제공합니다. 또한 사용자 요구 사항에 맞게 SAP 응용 프로그램 계층에 대한 리소스 크기를 조정할 수 있는 Azure 기능 및 유연성도 제공합니다.

![Azure(큰 인스턴스)에서 SAP HANA의 아키텍처 개요](./media/hana-overview-architecture/image1-architecture.png)

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.

- **오른쪽**: 최종 사용자가 SAP와 같은 LOB 응용 프로그램에 액세스할 수 있도록 데이터 센터에서 서로 다른 응용 프로그램을 실행하는 온-프레미스 인프라를 보여 줍니다. 이상적으로 이 온-프레미스 인프라는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 Azure에 연결됩니다.

- **가운데:** Azure IaaS를 보여 주고, 이 경우 VM을 사용하여 SAP HANA를 DBMS 시스템으로 사용하는 SAP 또는 다른 응용 프로그램을 호스팅합니다. VM에서 제공하는 메모리에서 작동하는 더 작은 HANA 인스턴스는 응용 프로그램 계층과 함께 VM에 배포됩니다. 가상 머신에 대한 자세한 내용은 [가상 머신](https://azure.microsoft.com/services/virtual-machines/)을 참조하세요.

   Azure 네트워크 서비스는 다른 응용 프로그램과 함께 SAP 시스템을 가상 네트워크로 그룹화하는 데 사용됩니다. 이러한 가상 네트워크는 온-프레미스 시스템 및 SAP HANA on Azure(대규모 인스턴스)에 연결됩니다.

   Azure에서 실행하도록 지원되는 SAP NetWeaver 응용 프로그램 및 데이터베이스의 경우 [SAP Support Note #1928533 – Azure의 SAP 응용 프로그램: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)을 참조하세요. Azure에 SAP 솔루션을 배포하는 방법에 대한 설명서는 다음을 참조하세요.

  -  [Windows 가상 머신에서 SAP 사용](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Azure 가상 머신에서 SAP 솔루션 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **왼쪽:** Azure 대규모 인스턴스 스탬프의 SAP HANA TDI 인증 하드웨어를 보여 줍니다. HANA 대규모 인스턴스 장치는 온-프레미스에서 Azure로 연결할 때와 동일한 기술을 사용하여 구독의 가상 네트워크에 연결됩니다.

Azure 큰 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공하고 SAP HANA 인증을 받은 Intel Xeon E7-8890v3 또는 Intel Xeon E7-8890v4 프로세서를 기반으로 하는 서버입니다.
- **네트워크:** 컴퓨팅, 저장소 및 LAN 구성 요소를 상호 연결하는 통합된 고속 네트워크 패브릭입니다.
- **저장소:** 통합된 네트워크 패브릭을 통해 액세스되는 저장소 인프라입니다. 제공되는 특정 저장소 용량은 배포되는 특정 SAP HANA on Azure(대규모 인스턴스) 구성에 따라 다릅니다. 추가 저장소 용량은 추가 월별 비용으로 사용할 수 있습니다.

큰 인스턴스 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트로 배포됩니다. 테넌트 배포 시 Azure 등록 내에서 Azure 구독 이름을 지정합니다. Azure 구독은 HANA 대규모 인스턴스가 청구되는 구독입니다. 이러한 테넌트는 Azure 구독과 1:1 관계를 포함합니다. 네트워크의 경우 서로 다른 Azure 구독에 속한 다른 가상 네트워크에서 한 Azure 지역의 한 테넌트에 배포된 HANA 대규모 인스턴스 장치에 액세스할 수 있습니다. 이러한 Azure 구독은 동일한 Azure 등록에 속해야 합니다. 

VM과 마찬가지로, SAP HANA on Azure(대규모 인스턴스)가 여러 Azure 지역에 제공됩니다. 재해 복구 기능을 제공하려면 옵트인하도록 선택할 수 있습니다. 하나의 지정학적 지역의 서로 다른 큰 인스턴스 스탬프가 서로 연결됩니다. 예를 들어 미국 서부 및 미국 동부에 있는 HANA 대규모 인스턴스 스탬프가 재해 복구 복제를 위한 전용 네트워크 링크를 통해 연결됩니다. 

Azure Virtual Machines를 사용하여 다양한 VM 유형 중에서 선택할 수 있는 것처럼 SAP HANA의 다양한 워크로드 유형에 맞게 조정된 HANA 대규모 인스턴스의 다양한 SKU 중에서 선택할 수 있습니다. SAP는 Intel 프로세서 세대에 따라 다양한 워크로드에 대한 메모리 대 프로세서 소켓 비율을 적용합니다. 다음 표에서는 제공된 SKU 형식을 보여줍니다.

2017년 7월 현재, SAP HANA on Azure(대규모 인스턴스)는 미국 서부, 미국 동부, 오스트레일리아 동부, 오스트레일리아 남동부, 유럽 서부 및 북유럽의 Azure 지역에서 여러 구성으로 사용할 수 있습니다.

| SAP 솔루션 | CPU | 메모리 | Storage | 가용성 |
| --- | --- | --- | --- | --- |
| OLAP에 대해 최적화됨: SAP BW, BW/4HANA<br /> 또는 SAP HANA(일반 OLAP 워크로드용) | Azure S72에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  768 GB |  3 TB | 사용 가능 |
| --- | Azure S144에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  1.5 TB |  6 TB | 더 이상 제공되지 않음 |
| --- | Azure S192에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드 |  2.0 TB |  8 TB | 사용 가능 |
| --- | Azure S384에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  4.0 TB |  16 TB | 사용 가능 |
| OLTP에 대해 최적화됨: SAP Business Suite<br /> SAP HANA 또는 S/4HANA(OLTP)에서,<br /> 일반 OLTP | Azure S72m에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  1.5 TB |  6 TB | 사용 가능 |
|---| Azure S144m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  3.0 TB |  12 TB | 더 이상 제공되지 않음 |
|---| Azure S192m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드  |  4.0 TB |  16 TB | 사용 가능 |
|---| Azure S384m에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  6.0 TB |  18 TB | 사용 가능 |
|---| Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  8.0 TB |  22 TB |  사용 가능 |
|---| Azure의 SAP HANA S576m<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  12.0 TB |  28 TB | 사용 가능 |
|---| Azure의 SAP HANA S768m<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  16.0 TB |  36 TB | 사용 가능 |
|---| Azure의 SAP HANA S960m<br /> – 20 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 480 CPU 코어 및 960 CPU 스레드 |  20.0 TB |  46 TB | 사용 가능 |

- CPU 코어 = 서버 단위 프로세서 합계의 비하이퍼 스레드 CPU 코어 합계
- CPU 스레드 = 서버 단위 프로세서 합계의 하이퍼 스레드된 CPU 코어에서 제공하는 계산 스레드 합계 모든 장치는 기본적으로 하이퍼스레드 기술을 사용하도록 구성됩니다.


선택한 특정 구성은 워크로드, CPU 리소스 및 원하는 메모리에 따라 달라집니다. OLTP 워크로드에서 OLAP 워크로드에 최적화된 SKU를 사용할 수 있습니다. 

모든 제안에 대한 하드웨어는 SAP HANA TDI 인증을 기반으로 합니다. SKU를 구분하는 두 가지 하드웨어 클래스는 다음과 같습니다.

- '유형 I 클래스' SKU: S72, S72m, S144, S144m, S192 및 S192m
- SKU의 "유형 II 클래스": S384, S384m, S384xm, S576m, S768m 및 S960m

HANA 대규모 인스턴스 스탬프 전체는 단일 고객용으로만 할당되지 않습니다. 이 사실은 Azure에 배포된 네트워크 패브릭을 통해 연결된 계산 및 저장소 리소스 랙에도 적용됩니다. HANA 대규모 인스턴스 인프라는 Azure와 마찬가지로 다음 세 가지 수준에서 서로 격리된 서로 다른 고객 &quot;테넌트&quot;를 배포합니다.

- **네트워크**: HANA 대규모 인스턴스 스탬프 내에서 가상 네트워크를 통해 격리됩니다.
- **저장소**: 저장소 볼륨이 할당되고 테넌트 간 저장소 볼륨을 격리하는 저장소 가상 머신을 통해 격리됩니다.
- **계산**: 서버 장치를 전용으로 단일 테넌트에 할당합니다. 서버 장치에 대한 하드 분할 또는 소프트 분할이 없습니다. 테넌트 간 단일 서버 또는 호스트 단위의 공유 없음. 

서로 다른 테넌트 간의 HANA 대규모 인스턴스 장치 배포는 상호 간에 표시되지 않습니다. 서로 다른 테넌트에 배포된 HANA 대규모 인스턴스 장치는 HANA 대규모 인스턴스 스탬프 수준에서 서로 직접 통신 할 수 없습니다. 한 테넌트 내의 HANA 대규모 인스턴스 장치만 HANA 대규모 인스턴스 스탬프 수준에서 서로 통신할 수 있습니다.

대규모 인스턴스 스탬프에 배포된 테넌트는 청구를 위해 하나의 Azure 구독에 할당됩니다. 네트워크의 경우 동일한 Azure 등록 내에 있는 다른 Azure 구독의 가상 네트워크에서 액세스할 수 있습니다. 또한 동일한 Azure 지역의 또 다른 Azure 구독을 사용하여 배포할 경우 별도의 HANA 대규모 인스턴스 테넌트를 요청하기로 선택할 수 있습니다.

HANA 대규모 인스턴스에서 SAP HANA를 실행하는 것과 Azure에 배포된 VM에서 SAP HANA를 실행하는 것 사이에는 중요한 차이가 있습니다.

- Azure(큰 인스턴스)의 SAP HANA의 경우 가상화 계층이 없습니다. 기본적인 완전 복구 하드웨어의 성능을 얻을 수 있습니다.
- Azure와 달리 Azure(큰 인스턴스) 서버에서 SAP HANA는 특정 고객이 전용으로 사용할 수 있습니다. 서버 장치 또는 호스트가 하드 또는 소프트 분할될 가능성이 없습니다. 결과적으로 HANA 대규모 인스턴스 장치는 테넌트에 전체적으로 할당된 대로 사용되며, 이에 따라 사용자에게 제공됩니다. 서버를 다시 부팅하거나 종료해도 운영 체제 및 SAP HANA가 다른 서버에 자동으로 배포되지 않습니다. (유형 I 클래스 SKU의 경우, 유일한 예외는 서버에서 문제가 발생하고 다른 서버에서 다시 배포해야 하는 경우입니다.)
- Azure와 달리 최고의 가성비를 위해 호스트 프로세서 유형을 선택한 경우 Azure(큰 인스턴스)에서 SAP HANA에 대해 선택한 프로세서 유형은 Intel E7v3 및 E7v4 프로세서 제품군에서 최고의 성능을 보입니다.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>하나의 HANA 대규모 인스턴스 장치에서 여러 SAP HANA 인스턴스 실행
HANA 대규모 인스턴스 장치에 둘 이상의 활성 SAP HANA 인스턴스를 호스팅할 수 있습니다. 저장소 스냅숏 및 재해 복구 기능을 제공하기 위해 이러한 구성에는 인스턴스당 볼륨 세트가 필요합니다. 현재 HANA 대규모 인스턴스 장치는 다음과 같이 세분화할 수 있습니다.

- **S72, S72m, S144, S192**: 장치의 최소 크기는 256GB이며, 256GB씩 증분할 수 있습니다. 다른 증분 단위(예: 256GB, 512GB)는 장치의 메모리 최댓값과 결합할 수 있습니다.
- **S144m 및 S192m**: 장치의 최소 크기는 512GB이며, 256GB씩 증분할 수 있습니다. 다른 증분 단위(예: 512GB, 768GB)는 장치의 메모리 최댓값과 결합할 수 있습니다.
- **유형 II 클래스**: 장치의 최소 크기는 2TB이며, 512GB씩 증분할 수 있습니다. 다른 증분 단위(예: 512GB, 1TB, 1.5TB)는 장치의 메모리 최댓값과 결합할 수 있습니다.

여러 SAP HANA 인스턴스를 실행하는 몇 가지 예는 다음과 같습니다.

| SKU | 메모리 크기 | 저장소 크기 | 여러 데이터베이스가 장착된 크기 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768GB HANA 인스턴스<br /> 또는 1 x 512GB 인스턴스 + 1 x 256GB 인스턴스<br /> 또는 3 x 256GB 인스턴스 | 
| S72m | 1.5 TB | 6 TB | 3 x 512GB HANA 인스턴스<br />또는 1 x 512GB 인스턴스 + 1 x 1TB 인스턴스<br />또는 6 x 256GB 인스턴스<br />또는 1x1.5 TB 인스턴스 | 
| S192m | 4 TB | 16 TB | 8 x 512GB 인스턴스<br />또는 4 x 1TB 인스턴스<br />또는 4 x 512GB 인스턴스 + 2 x 1TB 인스턴스<br />또는 4 x 768GB 인스턴스 + 2 x 512GB 인스턴스<br />또는 1 x 4TB 인스턴스 |
| S384xm | 8 TB | 22 TB | 4 x 2TB 인스턴스<br />또는 2 x 4TB 인스턴스<br />또는 2 x 3TB 인스턴스 + 1 x 2TB 인스턴스<br />또는 2 x 2.5TB 인스턴스 + 1 x 3TB 인스턴스<br />또는 1 x 8TB 인스턴스 |


다른 변형도 있습니다. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA 데이터 계층화 및 확장 노드 사용
SAP는 다양한 SAP NetWeaver 버전의 SAP BW 및 SAP BW/4HANA에 대한 데이터 계층화 모델을 지원합니다. 데이터 계층 모델에 대한 자세한 내용은 [SAP HANA 확장 노드가 있는 SAP BW/4HANA 및 SAP BW on HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) SAP 문서를 참조하세요.
HANA 대규모 인스턴스를 사용하면 FAQ 및 SAP 블로그 문서에서 설명한 대로 SAP HANA 확장 노드의 옵션 1 구성을 사용할 수 있습니다. 옵션 2 구성은 다음 HANA 대규모 인스턴스 SKU(S72m, S192, S192m, S384 및 S384m)와 함께 설정될 수 있습니다. 

설명서를 살펴볼 때 장점이 바로 표시되지 않을 수 있습니다. 하지만 SAP 크기 조정 지침을 살펴보면 옵션 1과 옵션 2 SAP HANA 확장 노드를 사용하여 장점을 확인할 수 있습니다. 예는 다음과 같습니다.

- SAP HANA 크기 조정 지침은 일반적으로 메모리로 데이터 볼륨 크기의 두 배를 필요로 합니다. 핫 데이터를 사용하여 SAP HANA 인스턴스를 실행하는 경우 50% 이하의 메모리만 데이터로 채워집니다. 메모리의 나머지 부분은 해당 작업을 수행하는 SAP HANA에 대해 이상적으로 유지됩니다.
- 즉, SAP BW 데이터베이스를 실행하는 2TB의 메모리가 있는 HANA 대규모 인스턴스 S192 단위에서 데이터 볼륨으로 1TB만을 갖습니다.
- 옵션 1의 추가 SAP HANA 확장 노드 및 S192 HANA 대규모 인스턴스 SKU를 사용하는 경우 데이터 볼륨에 2TB 용량을 추가로 제공합니다. 옵션 2 구성에서는 웜 데이터 볼륨에 4TB를 추가로 제공합니다. 핫 노드와 비교할 때 "웜" 확장 노드의 전체 메모리 용량은 옵션 1에 대한 데이터 저장에 사용할 수 있습니다. 옵션 2 SAP HANA 확장 노드 구성의 데이터 볼륨에 메모리를 두 배로 사용할 수 있습니다.
- 최종적으로 데이터 용량은 3TB이며, 옵션 1의 경우 핫 대 웜 비율은 1:2입니다. 옵션 2 확장 노드 구성에서는 데이터 용량이 5TB이고, 비율은 1:4입니다.

메모리에 비해 데이터 볼륨이 높을수록 요청하는 웜 데이터가 디스크 저장소에 저장될 확률이 높아집니다.


## <a name="operations-model-and-responsibilities"></a>작업 모델 및 책임

Azure(큰 인스턴스)에서 SAP HANA가 제공하는 서비스는 Azure IaaS 서비스에 맞춰 조정됩니다. SAP HANA에 최적화된 운영 체제가 설치된 HANA 대규모 인스턴스를 가져옵니다. Azure IaaS VM과 마찬가지로 OS를 강화하고, 필요한 추가 소프트웨어와 HANA를 설치하며, OS와 HANA를 운영하고 업데이트하는 대부분의 작업은 사용자의 책임입니다. Microsoft는 사용자에게 OS 업데이트 또는 HANA 업데이트를 강제로 요구하지 않습니다.

![Azure(큰 인스턴스)에서 SAP HANA의 책임](./media/hana-overview-architecture/image2-responsibilities.png)

다이어그램에서 보여 주듯이 SAP HANA on Azure(대규모 인스턴스)는 다중 테넌트 IaaS 제품입니다. 대부분의 경우 책임 분담은 OS 인프라 경계에 있습니다. Microsoft는 운영 체제 선 아래에서 서비스의 모든 측면을 담당합니다. 사용자는 선 위에 있는 서비스의 모든 측면을 담당합니다. OS는 사용자의 책임입니다. 규정 준수, 보안, 응용 프로그램 관리, 기본 요소 및 OS 관리를 위해 채택할 수 있는 최신 온-프레미스 방법을 계속 사용할 수 있습니다. 시스템은 모든 측면의 네트워크에 있는 것처럼 표시됩니다.

이 서비스는 SAP HANA에 최적화되어 있으므로 기본 인프라 기능을 사용하여 최상의 결과를 얻기 위해 Microsoft와 협력해야 하는 영역이 있습니다.

다음 목록에는 각 레이어와 책임에 대한 자세한 정보를 제공합니다.

**네트워킹**: SAP HANA를 실행하는 대규모 인스턴스 스탬프에 대한 모든 내부 네트워크입니다. 저장소에 대한 액세스, 인스턴스 간 연결(규모 확장 및 기타 기능), 환경에 대한 연결, VM에서 SAP 응용 프로그램 계층이 호스팅되는 Azure에 대한 연결은 사용자의 책임입니다. 또한 재해 복구용 복제를 위한 Azure 데이터 센서 간 WAN 연결도 포함됩니다. 모든 네트워크는 테넌트에서 분할되며 QoS(서비스 품질)를 적용합니다.

**저장소:** SAP HANA 서버에 필요한 모든 볼륨 및 스냅숏을 위한 가상화되고 분할된 저장소입니다. 

**서버:** 테넌트에 할당된 SAP HANA DB를 실행할 전용 물리적 서버입니다. SKU의 Type I 클래스 서버는 추상화된 하드웨어입니다. 이런 유형의 서버에서는 서버 구성이 프로필에 수집되고 유지 관리되며, 이런 프로필은 하나의 실제 하드웨어에서 다른 실제 하드웨어에 이동할 수 있습니다. 작업에 의한 이러한 프로필 이동(수동)은 Azure 서비스 복구와 약간 비교될 수 있습니다. 유형 II 클래스 SKU 서버는 이러한 기능을 제공하지 않습니다.

**SDDC:** 데이터 센터를 소프트웨어 정의 엔터티로 관리하는 데 사용되는 관리 소프트웨어입니다. Microsoft를 확장성, 가용성 및 성능상의 이유로 리소스를 풀(pool)할 수 있습니다.

**O/S:** 서버에서 실행되고 사용자가 선택한 OS(SUSE Linux 또는 Red Hat Linux)입니다. 함께 제공되는 OS 이미지는 SAP HANA를 실행하기 위해 개별 Linux 공급업체에서 Microsoft에 제공한 것입니다. SAP HANA에 최적화된 특정 이미지에 대해서는 Linux 공급업체에서 제공한 구독이 있어야 합니다. OS 공급업체에 이미지를 등록하는 것은 사용자의 책임입니다. 

Microsoft에서 인계하는 시점부터 Linux 운영 체제의 추가 패치를 수행해야 합니다. 이 패치에는 성공적인 SAP HANA 설치에 필요할 수 있고 특정 Linux 공급업체에서 해당 SAP HANA에 최적화된 OS 이미지에 포함하지 않은 추가 패키지가 포함되어 있습니다. 자세한 내용은 SAP의 HANA 설치 설명서 및 SAP Note를 참조하세요. 

특정 서버 하드웨어와 관련하여 OS 및 해당 드라이버의 오작동 또는 최적화로 인해 OS 패치를 수행해야 합니다. OS의 보안 또는 기능 패치도 수행해야 합니다. 

또한 다음과 같은 모니터링 및 용량 계획에도 책임이 있습니다.

- CPU 리소스 사용량
- 메모리 사용량
- 사용 가능한 공간, IOPS 및 대기 시간과 관련된 디스크 볼륨
- HANA 대규모 인스턴스 및 SAP 응용 프로그램 계층 간의 네트워크 볼륨 트래픽

HANA 대규모 인스턴스의 기본 인프라는 OS 볼륨의 백업 및 복원에 대한 기능을 제공합니다. 이 기능을 사용하는 것도 사용자의 책임입니다.

**미들웨어:** 주로 SAP HANA 인스턴스입니다. 관리, 작업 및 모니터링은 사용자의 책임입니다. 제공된 기능을 사용하여 백업/복원 및 재해 복구 용도로 저장소 스냅숏을 사용할 수 있습니다. 이러한 기능은 인프라에 의해 제공됩니다. 이러한 기능을 사용하여 고가용성 또는 재해 복구를 설계하고, 이를 활용하며, 모니터링하여 저장소 스냅숏이 성공적으로 실행되었는지 여부를 확인해야 합니다.

**데이터:** SAP HANA에서 관리하는 데이터와 볼륨 또는 파일 공유에 있는 백업 파일과 같은 기타 데이터입니다. 사용 가능한 디스크 공간 모니터링 및 볼륨의 콘텐츠 관리는 사용자의 책임입니다. 또한 디스크 볼륨 및 저장소 스냅숏의 성공적인 백업 실행도 모니터링해야 합니다. 재해 복구 사이트에 대한 데이터 복제를 성공적으로 실행하는 것은 Microsoft의 책임입니다.

**응용 프로그램:** SAP 응용 프로그램 인스턴스이거나, 비SAP 응용 프로그램의 경우 해당 응용 프로그램의 응용 프로그램 계층입니다. 해당 응용 프로그램의 배포, 관리, 운영 및 모니터링은 사용자의 책임입니다. 가상 네트워크 내에서 CPU 리소스 사용, 메모리 사용, Azure Storage 사용 및 네트워크 대역폭 사용에 대한 용량 계획을 담당합니다. 또한 가상 네트워크에서 SAP HANA on Azure(대규모 인스턴스)에 이르는 리소스 사용에 대한 용량 계획도 담당합니다.

**WAN:** 워크로드에 대해 온-프레미스에서 Azure 배포로 설정한 연결입니다. HANA 대규모 인스턴스를 사용하는 모든 고객은 Azure ExpressRoute를 사용하여 연결합니다. 이 연결은 SAP HANA on Azure(대규모 인스턴스) 솔루션에 포함되지 않습니다. 이 연결 설정은 사용자의 책임입니다.

**보관:** 저장소 계정에서 사용자 고유의 방법을 사용하여 데이터 복사본을 보관하는 것이 좋습니다. 보관하려면 관리, 규정 준수, 비용 및 작업이 필요합니다. Azure에서 보관 복사본과 백업을 생성하고 호환되는 방식으로 저장해야 합니다.

[Azure(큰 인스턴스)의 SAP HANA에 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)를 참조하세요.

## <a name="sizing"></a>크기 조정

HANA 대규모 인스턴스에 대한 크기 조정은 일반적으로 HANA에 대한 크기 조정과 다르지 않습니다. 다른 RDBMS에서 HANA로 이동하려는 기존 및 배포된 시스템의 경우 SAP는 기존 SAP 시스템에서 실행되는 다양한 보고서를 제공합니다. 데이터베이스가 HANA로 이동되면 이러한 보고서는 데이터를 확인하고 HANA 인스턴스에 대한 메모리 요구 사항을 계산합니다. 이러한 보고서를 실행하고 최신 패치 또는 버전을 가져오는 방법에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

- [SAP Note #1793345 - HANA에서 SAP Suite에 대한 크기 조정](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA 및 S/4 HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - BW on HANA에 대한 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - BW on HANA에 대한 새 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2296290)

녹색 필드 구현을 위해 SAP Quick Sizer를 사용하여 HANA 상단에서 SAP 소프트웨어 구현에 대한 메모리 요구 사항을 계산할 수 있습니다.

데이터 볼륨이 증가함에 따라 HANA에 대한 메모리 요구 사항도 증가합니다. 나중에 발생할 상황을 예측하는 데 도움이 되도록 현재 메모리 사용을 파악해야 합니다. 그러면 메모리 요구 사항에 따라 해당 요구 사항을 HANA 대규모 인스턴스 SKU 중 하나에 매핑할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 목록은 Azure(더 큰 인스턴스)에서 SAP HANA를 실행하기 위한 요구 사항을 모읍니다.

**Microsoft Azure**

- Azure(큰 인스턴스)에서 SAP HANA로 연결할 수 있는 Azure 구독입니다.
- Microsoft 프리미어 지원 계약입니다. Azure에서 SAP를 실행하는 것과 관련된 특정 정보는 [SAP Support Note #2015553 – SAP on Microsoft Azure: 필수 구성 요소 지원](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요. CPU가 384개 이상인 HANA 대규모 인스턴스 장치를 사용하는 경우 Azure Rapid Response를 포함하도록 프리미어 지원 계약을 확장해야 합니다.
- SAP를 통해 크기 조정 연습을 수행한 후 필요한 HANA 대규모 인스턴스 SKU를 파악해야 합니다.

**네트워크 연결**

- 온-프레미스와 Azure 간의 ExpressRoute: 온-프레미스 데이터 센터를 Azure에 연결하려면 ISP에서 1Gbps 이상의 연결을 요청해야 합니다. 

**운영 체제**

- SAP 응용 프로그램용 SUSE Linux Enterprise Server 12에 대한 라이선스

   > [!NOTE] 
   > Microsoft에서 제공하는 운영 체제가 SUSE에 등록되어 있지 않습니다. 구독 관리 도구 인스턴스에 연결되어 있지 않습니다.

- Azure에서 VM에 배포된 SUSE Linux 구독 관리 도구. 이 도구는 SUSE에서 SAP HANA on Azure(대규모 인스턴스)를 등록하고 하나씩 업데이트할 수 있는 기능을 제공합니다. (HANA 대규모 인스턴스 데이터 센터 내에서는 인터넷에 액세스할 수 없습니다.) 
- SAP HANA용 Red Hat Enterprise Linux 6.7 또는 7.2에 대한 라이선스.

   > [!NOTE]
   > Microsoft에서 제공하는 운영 체제가 Red Hat에 등록되어 있지 않습니다. Red Hat 구독 관리자 인스턴스에 연결되어 있지 않습니다.

- Azure에서 VM에 배포된 Red Hat 구독 관리자. Red Hat 구독 관리자는 Red Hat에서 SAP HANA on Azure(대규모 인스턴스)를 등록하고 하나씩 업데이트할 수 있는 기능을 제공합니다. (Azure 대규모 인스턴스 스탬프에 배포된 테넌트 내에서는 인터넷에 직접 액세스할 수 없습니다.)
- SAP를 사용하려면 Linux 공급자와도 지원 계약을 체결해야 합니다. 이 요구 사항은 HANA 대규모 인스턴스 솔루션 또는 Azure에서 Linux를 실행한다는 사실로 인해 제거되지 않습니다. 일부 Linux Azure 갤러리 이미지와 달리, HANA 대규모 인스턴스의 솔루션 제품에는 서비스 요금이 *포함되지 않습니다*. Linux 배포자와의 지원 계약과 관련하여 SAP의 요구 사항을 충족시키는 것은 사용자의 책임입니다. 
   - SUSE Linux의 경우 [SAP Note #1984787 - SUSE Linux Enterprise Server 12: 설치 정보](https://launchpad.support.sap.com/#/notes/1984787) 및 [SAP Note #1056161 - SAP 응용 프로그램에 대한 SUSE 우선 지원](https://launchpad.support.sap.com/#/notes/1056161)에서 지원 계약의 요구 사항을 살펴보세요.
   - Red Hat Linux의 경우 HANA 대규모 인스턴스의 운영 체제에 대한 지원 및 서비스 업데이트가 포함된 올바른 구독 수준을 있어야 합니다. Red Hat에서는 [SAP 솔루션](https://access.redhat.com/solutions/3082481)용 Red Hat Enterprise Linux 구독을 권장합니다. 

Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.

운영 체제 및 HLI 펌웨어/드라이버 버전의 호환성 매트릭스는 [HLI에 대한 OS 업그레이드](os-upgrade-hana-large-instance.md)를 참조하세요.


**데이터베이스**

- SAP HANA에 대한 라이선스 및 소프트웨어 설치 구성 요소(플랫폼 및 Enterprise 버전).

**응용 프로그램**

- SAP HANA에 연결되는 모든 SAP 응용 프로그램 및 관련 SAP 지원 계약에 대한 라이선스 및 소프트웨어 설치 구성 요소
- SAP HANA on Azure(대규모 인스턴스) 환경 및 관련 지원 계약과 관련하여 사용되는 모든 비SAP 응용 프로그램에 대한 라이선스 및 소프트웨어 설치 구성 요소

**기술**

- Azure IaaS 및 해당 구성 요소에 대한 경험 및 지식
- Azure에서 SAP 워크로드를 배포하는 방법에 대한 경험 및 지식
- SAP HANA 설치 공인 전문가
- SAP HANA에 대한 고가용성 및 재해 복구를 설계하는 SAP 설계 기술

**SAP**

- SAP 고객으로 SAP와 지원 계약을 맺기를 기대합니다.
- 특히 HANA 대규모 인스턴스 SKU의 유형 II 클래스를 구현하는 경우, SAP HANA 버전 및 최종적인 대규모 강화 하드웨어 구성에 대해 SAP에 문의하세요.


## <a name="storage"></a>Storage

SAP HANA on Azure(대규모 인스턴스)의 저장소 레이아웃은 SAP 권장 지침에 따라 클래식 배포 모델에서 SAP HANA를 통해 구성됩니다. 이 지침은 [SAP HANA 저장소 요구 사항](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서에 나와 있습니다.

유형 I 클래스의 HANA 대규모 인스턴스는 메모리 볼륨으로 저장소 볼륨의 4배를 제공합니다. 유형 II 클래스의 HANA 대규모 인스턴스 장치의 경우 저장소는 4배를 초과할 수 없습니다. 장치에는 HANA 트랜잭션 로그 백업을 저장하기 위한 볼륨이 제공됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 설치 및 구성](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

저장소 할당이라는 면에서 다음 표를 참조하세요. 다음 표에는 다른 HANA 대규모 인스턴스 장치와 함께 제공되는 여러 볼륨에 대한 대략적인 용량이 나와 있습니다.

| HANA 큰 인스턴스 SKU | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1,280GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328GB | 768 GB |1,280GB | 768 GB |
| S192 | 4,608GB | 1,024GB | 1,536GB | 1,024GB |
| S192m | 11,520 GB | 1,536GB | 1,792GB | 1,536GB |
| S384 | 11,520 GB | 1,536GB | 1,792GB | 1,536GB |
| S384m | 12,000 GB | 2,050GB | 2,050GB | 2,040GB |
| S384xm | 16,000 GB | 2,050GB | 2,050GB | 2,040GB |
| S576m | 20,000 GB | 3,100GB | 2,050GB | 3,100GB |
| S768m | 28,000 GB | 3,100GB | 2,050GB | 3,100GB |
| S960m | 36,000 GB | 4,100GB | 2,050GB | 4,100GB |


실제로 배포되는 볼륨은 볼륨 크기를 표시하는 데 사용된 배포와 도구에 따라 달라질 수 있습니다.

HANA 대규모 인스턴스 SKU를 세분화하는 경우 가능한 분할 조각의 몇 가지 예는 다음과 같을 수 있습니다.

| 메모리 파티션(GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792GB | 640 GB | 1,024GB | 640 GB |
| 1,536 | 3,328GB | 768 GB | 1,280GB | 768 GB |


이러한 크기는 볼륨을 표시하는 데 사용된 배포와 도구에 따라 약간 달라질 수 있는 대략적인 볼륨 크기입니다. 또한 2.5TB와 같은 다른 파티션 크기도 있습니다. 이러한 저장소 크기는 이전 파티션에 사용된 것과 비슷한 수식을 사용하여 계산되었습니다. '파티션'이라는 용어는 운영 체제, 메모리 또는 CPU 리소스가 분할된 방식을 나타내지 않습니다. 이는 단지 하나의 HANA 대규모 인스턴스 장치에 배포하려는 다른 HANA 인스턴스에 대한 저장소 파티션을 나타냅니다. 

더 많은 저장소가 필요할 수 있습니다. 저장소는 1TB 단위로 추가로 구입하여 추가할 수 있습니다. 이 추가 저장소는 추가 볼륨으로 추가될 수 있습니다. 또한 기존 볼륨 중 하나 이상을 확장하는 데도 사용할 수 있습니다. 원래 배포된 볼륨 크기는 줄일 수 없으며, 위의 표에서 대부분 설명되어 있습니다. 볼륨 이름을 변경하거나 탑재할 수도 없습니다. 앞에서 설명한 저장소 볼륨은 NFS4 볼륨으로 HANA 대규모 인스턴스 장치에 연결됩니다.

저장소 스냅숏은 백업/복원 및 재해 복구 용도로 사용할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

### <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화
HANA 대규모 인스턴스에 사용되는 저장소를 통해 디스크에 저장된 데이터를 투명하게 암호화할 수 있습니다. HANA 대규모 인스턴스 장치가 배포되면 이러한 종류의 암호화를 사용하도록 설정할 수 있습니다. 또한 배포가 완료되면 암호화된 볼륨으로 변경할 수도 있습니다. 암호화되지 않은 볼륨에서 암호화된 볼륨으로 이동하는 경우 투명하게 수행되며 가동 중지 시간이 필요하지 않습니다. 

유형 I 클래스 SKU를 사용하면 부팅 LUN이 저장된 볼륨이 암호화됩니다. HANA 대규모 인스턴스의 유형 II 클래스 SKU인 경우 OS 메서드를 통해 부팅 LUN을 암호화해야 합니다. 자세한 내용은 Microsoft 서비스 관리 팀에 문의하세요.


## <a name="networking"></a>네트워킹

Azure 네트워크 서비스의 아키텍처는 SAP 응용 프로그램을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. 이러한 모든 SAP 시스템이 SAP HANA를 기반으로 하는 것은 아닙니다. SAP 자산은 아마도 다음을 사용하는 하이브리드일 수 있습니다.

- 온-프레미스에 배포된 SAP 시스템 이러한 시스템은 해당 크기로 인해 현재 Azure에서 호스팅할 수 없습니다. 예를 들어 SQL Server에서 데이터베이스로 실행되고 VM이 제공할 수 있는 것보다 더 많은 CPU 또는 메모리 리소스가 필요한 프로덕션 SAP ERP 시스템이 있습니다.
- 온-프레미스에 배포된 SAP HANA 기반 SAP 시스템.
- VM에 배포된 SAP 시스템. 이러한 시스템은 리소스 사용량 및 메모리 요구량에 따라 Azure에서 VM에 성공적으로 배포할 수 있는 SAP NetWeaver 기반 응용 프로그램에 대한 배포, 테스트, 샌드박스 또는 프로덕션 인스턴스가 될 수 있습니다. 또한 SQL Server와 같은 데이터베이스를 기반으로 할 수도 있습니다. 자세한 내용은 [SAP Note 1928533 - Azure의 SAP 응용 프로그램: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533/E)을 참조하세요. 그리고 이러한 시스템은 SAP HANA와 같은 데이터베이스를 기반으로 할 수 있습니다. 자세한 내용은 [SAP HANA 인증 IaaS 플랫폼](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)을 참조하세요.
- Azure 대규모 인스턴스 스탬프에서 SAP HANA on Azure(대규모 인스턴스)를 활용하는 Azure에서 VM에 배포된 SAP 응용 프로그램 서버.

네 가지 이상의 배포 시나리오가 있는 하이브리드 SAP 자산이 일반적입니다. Azure에서 실행되는 완전한 SAP 자산에 대한 고객 사례도 많이 있습니다. VM이 더욱 강력해짐에 따라 Azure에서 자신의 모든 SAP 솔루션을 이동하는 고객의 수가 늘고 있습니다.

Azure에 배포된 SAP 시스템의 컨텍스트에서 Azure 네트워킹은 복잡하지 않습니다. 기준이 되는 원칙은 다음과 같습니다.

- Azure 가상 네트워크는 온-프레미스 네트워크에 연결되는 ExpressRoute 회로에 연결되어야 합니다.
- 온-프레미스를 Azure에 연결하는 ExpressRoute 회로에는 대개 1Gbps 이상의 대역폭이 있어야 합니다. 이 최소 대역폭을 통해 온-프레미스 시스템과 VM에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- Azure에서 모든 SAP 시스템이 서로 통신할 수 있도록 Azure 가상 네트워크에 설정되어야 합니다.
- 온-프레미스에서 호스팅되는 Active Directory 및 DNS는 ExpressRoute를 통해 온-프레미스에서 Azure로 확장됩니다.


> [!NOTE] 
> 요금 청구 관점에서 볼 때, 하나의 Azure 구독은 특정 Azure 지역의 대규모 인스턴스 스탬프에 있는 하나의 테넌트에만 연결할 수 있습니다. 반대로, 하나의 대규모 인스턴스 스탬프 테넌트는 하나의 Azure 구독에만 연결할 수 있습니다. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

서로 다른 여러 Azure 지역에 SAP HANA on Azure(대규모 인스턴스)를 배포하면 별도의 테넌트가 대규모 인스턴스 스탬프에 배포됩니다. 이러한 인스턴스가 동일한 SAP 자산에 포함되면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> SAP HANA on Azure(대규모 인스턴스)에서는 Azure Resource Manager 배포만 지원됩니다.

 

### <a name="additional-virtual-network-information"></a>추가 가상 네트워크 정보

가상 네트워크를 ExpressRoute에 연결하려면 Azure 게이트웨이를 만들어야 합니다. 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

Azure 게이트웨이는 Azure 외부의 인프라 또는 Azure 대규모 인스턴스 스탬프에 ExpressRoute와 함께 사용할 수 있습니다. 또한 Azure 게이트웨이를 사용하여 가상 네트워크 간에 연결할 수도 있습니다. 자세한 내용은 [PowerShell을 사용하여 Resource Manager에 대한 네트워크 간 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이러한 연결이 서로 다른 Microsoft 엔터프라이즈 에지 라우터에서 오는 경우 Azure 게이트웨이를 최대 4개의 서로 다른 ExpressRoute 연결에 연결할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> 두 사용 사례에 대해 Azure 게이트웨이에서 제공하는 처리량은 차이가 있습니다. 자세한 내용은 [VPN Gateway 정보](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 가상 네트워크 게이트웨이로 달성할 수 있는 최대 처리량은 ExpressRoute 연결을 사용할 경우 10Gbps입니다. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. 가상 네트워크 게이트웨이의 전체 대역폭을 활용하려면 다중 스트림을 사용합니다. 또는 단일 파일의 병렬 스트림에서 다른 파일을 복사해야 합니다.


### <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- Azure 네트워크 서비스(앞에서 설명한 대로 게이트웨이가 있는 가상 네트워크 포함). 이 부분은 응용 프로그램 요구 사항, 보안 및 규정 준수 요구 사항에 적합한 디자인을 찾아야 하는 영역입니다. HANA 대규모 인스턴스를 사용하는지 여부는 선택할 가상 네트워크와 Azure 게이트웨이 SKU의 수 측면에서 고려해야 할 또 다른 요소입니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute 기술을 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 
- 대부분 투명한 HANA 대규모 인스턴스의 네트워킹

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA 대규모 인스턴스를 사용하기 때문에 온-프레미스 자산에 있는 사용자가 ExpressRoute를 통해 Azure에 연결해야 한다는 요구 사항은 변경되지 않습니다. HANA 대규모 인스턴스 장치에서 호스팅되는 HANA 인스턴스에 연결하는 응용 프로그램 계층을 호스팅하는 VM을 실행하는 하나 이상의 가상 네트워크가 있어야 한다는 요구 사항도 변경되지 않습니다. 

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 고객 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로 통해 가상 네트워크에 연결됩니다. 로드 조건을 분리하기 위해 온-프레미스와 가상 네트워크 ExpressRoute 간의 링크 및 가상 네트워크와 HANA 대규모 인스턴스 간의 링크는 동일한 라우터를 공유하지 않습니다.
- SAP 응용 프로그램 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 SAP HANA에서 응용 프로그램 계층으로의 데이터 전송(결과 집합)과 같이 많은 작은 요청과 버스트와는 다른 특성을 가지고 있습니다.
- SAP 응용 프로그램 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- 가상 네트워크 게이트웨이에는 둘 이상의 ExpressRoute 연결이 있습니다. 두 연결은 모두 가상 네트워크 게이트웨이의 들어오는 데이터에 대한 최대 대역폭을 공유합니다.

VM과 HANA 대규모 인스턴스 장치 간에 발생하는 네트워크 대기 시간은 일반적인 VM 간 네트워크 왕복 대기 시간보다 길 수 있습니다. Azure 지역에 따라 측정값은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)에서 평균 미만으로 분류된 0.7ms 왕복 대기 시간을 초과할 수 있습니다. 그럼에도 불구하고 고객은 SAP HANA 기반 프로덕션 SAP 응용 프로그램을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. 배포한 고객은 HANA 대규모 인스턴스 장치를 사용하여 SAP HANA에서 SAP 응용 프로그램을 실행함으로써 크게 향상되었음을 보고합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다.
 
VM과 HANA 대규모 인스턴스 간에 결정적인 네트워크 대기 시간을 제공하려면 가상 네트워크 게이트웨이 SKU를 선택해야 합니다. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 전송하도록 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA 대규모 인스턴스 유형 II 클래스 SKU의 경우, 반드시 UltraPerformance 게이트웨이 SKU를 가상 네트워크 게이트웨이로 사용해야 합니다.

> [!IMPORTANT] 
> SAP 응용 프로그램과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA 대규모 인스턴스 유형 II 클래스 SKU의 경우, UltraPerformance 게이트웨이 SKU만 가상 네트워크 게이트웨이로 지원됩니다.



### <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. ExpressRoute 회로는 엔터프라이즈 에지 라우터에 연결됩니다. 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 경로가 설정되면 해당 경로가 Azure 백본에 연결되고 모든 Azure 지역에 액세스할 수 있습니다.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. Azure 대규모 인스턴스 스탬프는 Azure IaaS 및 대규모 인스턴스 스탬프에서 VM 간의 네트워크 대기 시간을 최소화하기 위해 전용 엔터프라이즈 에지 라우터 장치를 통해 연결됩니다.

SAP 응용 프로그램 인스턴스를 호스팅하는 VM에 대한 가상 네트워크 게이트웨이는 ExpressRoute 회로에 연결됩니다. 동일한 가상 네트워크는 대규모 인스턴스 스탬프에 전용으로 연결하기 위해 별도의 엔터프라이즈 에지 라우터에 연결됩니다.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 응용 프로그램 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. 2Gbps 또는 10Gbps 처리량의 가상 네트워크 게이트웨이 대역폭에서 병목 현상이 나타나지 않는다고 가정합니다.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

SAP HANA on Azure(대규모 인스턴스)에 연결하기 위해 여러 SAP 시스템 또는 대형 SAP 시스템을 배포하는 경우, 가상 네트워크 게이트웨이의 처리량으로 인해 병목 현상이 발생할 수 있습니다. 이 경우 응용 프로그램 계층을 여러 가상 네트워크로 분할합니다. 또한 다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수도 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

별도의 가상 네트워크를 사용하여 저장소를 관리하는 VM을 호스팅합니다. 이렇게 하면 SAP 응용 프로그램 계층을 실행하는 VM을 제공하는 가상 네트워크 게이트웨이에서 큰 파일이나 데이터를 HANA 대규모 인스턴스로부터 Azure에 전송하지 못하도록 방지할 수 있습니다. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 더 큰 단일 SAP 응용 프로그램 계층에 대해 여러 가상 네트워크를 활용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

 Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 가상 네트워크를 통해 SAP 응용 프로그램 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

이 그림에서는 여러 가상 네트워크를 통해 배포된 SAP 응용 프로그램 계층 또는 구성 요소를 보여 줍니다. 이 구성은 이러한 가상 네트워크에서 호스팅되는 응용 프로그램 간의 통신 중에 발생한 피할 수 없는 대기 시간 오버헤드를 유발했습니다. 기본적으로 서로 다른 가상 네트워크에 있는 VM 간의 네트워크 트래픽은 이 구성에서 엔터프라이즈 에지 라우터를 통해 라우팅됩니다. 이 라우팅은 2016년 9월부터 최적화할 수 있습니다. 

두 가상 네트워크 간의 통신 대기 시간을 최적화하고 줄이려면 동일한 지역 내에서 가상 네트워크를 피어링합니다. 이 방법은 해당 가상 네트워크가 다른 구독에 있는 경우에도 작동합니다. 가상 네트워크 피어링을 사용하면 서로 다른 두 가상 네트워크의 VM 간 통신에서 Azure 네트워크 백본을 통해 서로 직접 통신할 수 있습니다. 대기 시간은 VM이 동일한 가상 네트워크에 있는 것처럼 표시됩니다. Azure 가상 네트워크 게이트웨이를 통해 연결된 IP 주소 범위를 처리하는 트래픽은 가상 네트워크의 개별 가상 네트워크 게이트웨이를 통해 라우팅됩니다. 

가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.


### <a name="routing-in-azure"></a>Azure에서 라우팅

SAP HANA on Azure(대규모 인스턴스)의 네트워크 라우팅에 대해 고려해야 할 세 가지 중요 사항은 다음과 같습니다.

* SAP HANA on Azure(대규모 인스턴스)는 VM 및 전용 ExpressRoute 연결을 통해서만 액세스할 수 있으며, 온-프레미스에서 직접 액세스 할 수 없습니다. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 응용 프로그램 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다.

* 재해 복구를 위해 두 개의 다른 Azure 지역에 배포된 HANA 대규모 인스턴스 장치가 있는 경우 동일한 일시적 라우팅 제한이 적용됩니다. 즉, 한 지역(예: 미국 서부)에 있는 HANA 대규모 인스턴스 장치의 IP 주소는 다른 지역(예: 미국 동부)에 배포된 HANA 대규모 인스턴스 장치로 라우팅되지 않습니다. 이 제한은 지역 간에 또는 HANA 대규모 인스턴스 장치를 가상 네트워크에 연결하는 ExpressRoute 회로 연결 간의 Azure 네트워크 피어링을 사용하는 것과는 독립적입니다. 그래픽 표현은 "여러 지역에서 HANA 대규모 인스턴스 장치 사용" 섹션의 그림을 참조하세요. 배포된 아키텍처와 함께 제공되는 이 제한은 HANA 시스템 복제를 재해 복구 기능으로 즉시 사용할 수 없도록 금지합니다.

* SAP HANA on Azure(대규모 인스턴스) 장치에는 사용자가 제출한 서버 IP 풀 주소 범위에서 할당된 IP 주소가 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이 IP 주소는 가상 네트워크를 HANA on Azure(대규모 인스턴스)에 연결하는 Azure 구독 및 ExpressRoute를 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션을 처음 배포하는 경우와 같이 더 이상 NAT를 통해 *할당되지 않습니다*. 

> [!NOTE] 
> 처음 두 가지 목록 항목에서 설명한 대로 일시적인 라우팅에 대한 제한을 해결하려면 라우팅에 추가 구성 요소를 사용합니다. 제한을 해결하는 데 사용할 수 있는 구성 요소는 다음과 같습니다.

> * 데이터를 라우팅하는 역방향 프록시를 사용합니다. 예를 들어 F5 BIG-IP, NGINX(Traffic Manager 포함)가 가상 방화벽/트래픽 라우팅 솔루션으로 Azure에 배포되어 있습니다.
> * Linux VM에서 [IPTables 규칙](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다.

> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

### <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스는 인터넷에 직접 *연결되지 않습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 응용 프로그램 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [이 SAP 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

재해 복구 용도 외에도, 여러 Azure 지역에 SAP HANA on Azure(대규모 인스턴스)를 배포해야 하는 이유가 있을 수 있습니다. 지역의 다른 가상 네트워크에 배포된 각 VM에서 HANA 대규모 인스턴스에 액세스하려는 경우가 있습니다. 다른 HANA 대규모 인스턴스 장치에 할당된 IP 주소는 해당 게이트웨이를 통해 직접 인스턴스에 연결된 가상 네트워크를 넘어서 전파되지 않습니다. 따라서 가상 네트워크 디자인이 약간 변경되었습니다. 가상 네트워크 게이트웨이는 다른 엔터프라이즈 에지 라우터에서 서로 다른 4개의 ExpressRoute 회로를 처리할 수 있습니다. 대규모 인스턴스 스탬프 중 하나에 연결된 각 가상 네트워크는 다른 Azure 지역의 대규모 인스턴스 스탬프에 연결할 수 있습니다.

![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

이 그림에서는 두 지역에 있는 서로 다른 가상 네트워크가 두 Azure 지역의 SAP HANA on Azure(대규모 인스턴스)에 연결하는 데 사용되는 서로 다른 두 개의 ExpressRoute 회로에 연결된 방식을 보여 줍니다. 새로 소개된 연결은 빨간색 사각형 선입니다. 가상 네트워크 중에서 이러한 연결을 사용하면 해당 가상 네트워크 중 하나에서 실행되는 VM이 두 지역에 배포된 서로 다른 각각의 HANA 대규모 인스턴스 장치에 액세스할 수 있습니다. 그림에서 보여 주듯이 온-프레미스에서 두 개의 Azure 지역에 연결하는 두 개의 ExpressRoute 연결이 있다고 가정합니다. 이러한 정렬은 재해 복구 용도를 위해 권장됩니다.

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.


