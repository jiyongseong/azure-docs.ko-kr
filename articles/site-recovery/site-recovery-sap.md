---
title: Azure Site Recovery를 사용하여 다중 SAP NetWeaver 응용 프로그램 배포 보호 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 SAP NetWeaver 응용 프로그램 배포를 보호하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: e2107177663163259d1f731717c4910bc986fc1f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Site Recovery를 사용하여 다중 계층 SAP NetWeaver 응용 프로그램 배포 보호

대부분의 중대형 SAP 배포에는 어떤 형태로든 재해 복구 솔루션이 사용됩니다. 점점 더 많은 핵심 비즈니스 프로세스가 SAP과 같은 응용 프로그램으로 옮겨가면서 강력하고 테스트 가능한 재해 복구 솔루션의 중요성이 높아지고 있습니다. Azure Site Recovery는 수많은 테스트를 거쳐 SAP 응용 프로그램과 통합되었습니다. Site Recovery는 대부분의 온-프레미스 재해 복구 솔루션의 역량을 확장하며, 경쟁 솔루션 대비 총 소유 비용(TCO)이 낮습니다.

Site Recovery의 기능은 다음과 같습니다.
* 구성 요소를 Azure에 복제하여 **온-프레미스에서 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 응용 프로그램 보호를 활성화**합니다.
* 구성 요소를 다른 Azure 데이터 센터에 복사하여 **Azure에서 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 응용 프로그램 보호를 활성화**합니다.
* Site Recovery를 사용하여 **클라우드 마이그레이션을 간소화**하여 Azure에 SAP 배포를 마이그레이션합니다.
* SAP 응용 프로그램 테스트를 위해 온디맨드로 프로덕션 클론을 만들어 **SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화**합니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 SAP NetWeaver 응용 프로그램 배포를 보호하는 방법을 설명합니다. Site Recovery를 사용하여 다른 Azure 데이터 센터로 복제하여 3계층 SAP NetWeaver 배포를 보호하는 모범 사례도 보여줍니다. 또한, 지원되는 시나리오와 구성을 설명하고, 테스트 장애 조치(failover)(재해 복구 연습)와 실제 장애 조치(failover)를 수행하는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음 작업을 수행하는 방법을 알고 있어야 합니다.

* [Azure에 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)
* [복구 네트워크 디자인](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure로 테스트 장애 조치(failover) 수행](azure-to-azure-walkthrough-test-failover.md)
* [Azure로 장애 조치(failover) 수행](site-recovery-failover.md)
* [도메인 컨트롤러 복제](site-recovery-active-directory.md)
* [SQL Server 복제](site-recovery-sql.md)

## <a name="supported-scenarios"></a>지원되는 시나리오
다음과 같은 시나리오에서 Site Recovery를 사용하여 재해 복구 솔루션을 구현할 수 있습니다.
* 하나의 Azure 데이터 센터에서 실행되고 다른 Azure 데이터 센터로 복제되는 SAP 시스템(Azure-to-Azure 재해 복구). 자세한 내용은 [Azure-to-Azure 복제 아키텍처](https://aka.ms/asr-a2a-architecture)를 참조하세요.
* 온-프레미스 VMware(또는 물리적) 서버에서 실행되고 Azure 데이터 센터의 재해 복구 사이트로 복제되는 SAP 시스템(VMware-to-Azure 재해 복구). 이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [VMware-to-Azure 복제 아키텍처](https://aka.ms/asr-v2a-architecture)를 참조하세요.
* 온-프레미스 Hyper-V에서 실행되고 Azure 데이터 센터의 재해 복구 사이트로 복제되는 SAP 시스템(Hyper-V-to-Azure 재해 복구). 이 시나리오에는 일부 추가 구성 요소가 필요합니다. 자세한 내용은 [Hyper-V-to-Azure 복제 아키텍처](https://aka.ms/asr-h2a-architecture)를 참조하세요.

이 문서에서는 Azure-to-Azure 재해 복구 시나리오를 바탕으로 Site Recovery의 SAP 재해 복구 기능을 보여줍니다. Site Recovery 복제는 응용 프로그램마다 달라지지 않으므로 여기에서 설명하는 프로세스를 다른 시나리오에도 적용할 수 있습니다.

### <a name="required-foundation-services"></a>필요한 기반 서비스
이 문서에서 설명하는 시나리오에서는 다음과 같은 기반 서비스가 배포되어 있습니다.
* Azure ExpressRoute 또는 Azure VPN Gateway
* Azure에서 실행되는 하나 이상의 Active Directory 도메인 컨트롤러와 DNS 서버

Site Recovery를 배포하기 전에 위와 같은 인프라를 구현하는 것이 권장됩니다.

## <a name="typical-sap-application-deployment"></a>일반 SAP 응용 프로그램 배포
일반적으로 대형 SAP 고객은 6~20개의 개별 SAP 응용 프로그램을 배포합니다. 이러한 응용 프로그램 대부분은 SAP NetWeaver ABAP 또는 Java 엔진을 기반으로 합니다. 소규모의 특정 비NetWeaver SAP 독립 실행형 엔진과 일부 비SAP 응용 프로그램이 이러한 코어 NetWeaver 응용 프로그램을 지원합니다.  

반드시 환경에서 실행 중인 모든 SAP 응용 프로그램을 파악해야 합니다. 그런 다음, 배포 모드(2계층 또는 3계층), 버전, 패티, 크기, 변동 속도, 디스크 지속성 요구 사항을 확인합니다.

![일반적인 SAP 배포 패턴 다이어그램](./media/site-recovery-sap/sap-typical-deployment.png)

SQL Server AlwaysOn, Oracle Data Guard, SAP HANA 시스템 복제와 같은 기본 DBMS 도구를 사용하여 SAP 데이터베이스 지속성 계층을 보호합니다. 클라이언트 계층은 데이터베이스 계층과 마찬가지로 Site Recovery에 의해 보호되지 않습니다. 따라서 클라이언트 계층에 영향을 주는 요인을 고려해야 합니다. 이러한 요인에는 DNS 전파 지연, 보안, 재해 복구 데이터 센터로의 원격 액세스 등이 있습니다.

Site Recovery는 SAP SCS와 ASCS를 포함하여 응용 프로그램 계층에 권장되는 솔루션입니다. 비NetWeaver SAP 응용 프로그램 및 비SAP 응용 프로그램과 같은 여타 응용 프로그램이 전체 SAP 배포 환경의 일부를 구성합니다. Site Recovery를 사용하여 이러한 요소를 보호해야 합니다.

## <a name="replicate-virtual-machines"></a>가상 머신 복제
모든 SAP 응용 프로그램 가상 머신을 Azure 재해 복구 데이터 센터로 복제하는 작업을 시작하려면 [Azure로 가상 머신 복제](azure-to-azure-walkthrough-enable-replication.md)의 지침을 따릅니다.

고정 IP 주소를 사용하는 경우에는 가상 머신에 부여할 IP 주소를 지정할 수 있습니다. IP 주소를 설정하려면 **계산 및 네트워크 설정** > **네트워크 인터페이스 카드**로 이동합니다.

![Site Recovery 네트워크 인터페이스 카드 창에서 사설 IP 주소를 설정하는 방법을 보여주는 스크린샷](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>복구 계획 만들기
복구 계획은 장애 조치(failover) 시에 다중 계층 응용 프로그램에서 여러 계층의 시퀀싱을 지원합니다. 시퀀싱은 응용 프로그램의 일관성을 유지하는 데 도움이 됩니다. 다중 계층 웹 응용 프로그램에 대한 복구 계획을 생성할 때는 [Site Recovery를 사용하여 복구 계획 만들기](site-recovery-create-recovery-plans.md)에서 설명하는 단계를 수행합니다.

### <a name="add-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
응용 프로그램이 제대로 작동하려면 장애 조치(failover) 후에 또는 테스트 장애 조치(failover) 중에 Azure 가상 머신에서 일부 작업을 수행해야 할 수 있습니다. 일부 장애 조치(failover) 사후 작업은 자동화할 수 있습니다. 예를 들어, 복구 계획에 해당 스크립트를 추가하여 DNS 항목을 업데이트하고 바인딩과 연결을 변경할 수 있습니다.

### <a name="dns-update"></a>DNS 업데이트
DNS가 동적 DNS 업데이트로 구성된 경우 가상 머신은 일반적으로 시작할 때 DNS를 새 IP 주소로 업데이트합니다. DNS를 가상 머신의 새 IP 주소로 업데이트하는 명시적 단계를 추가하려면 복구 계획 그룹에 장애 조치(failover) 사후 작업으로 [DNS의 IP 주소를 업데이트하는 스크립트](https://aka.ms/asr-dns-update)를 추가합니다.  

## <a name="example-azure-to-azure-deployment"></a>Azure-Azure 배포 예제
다음은 Site Recovery Azure-to-Azure 재해 복구 시나리오를 보여주는 다이어그램입니다.

![Azure-to-Azure 복제 시나리오 다이어그램](./media/site-recovery-sap/sap-replication-scenario.png)

* 기본 데이터 센터는 싱가포르에 있으며(Azure 동남 아시아) 재해 복구 데이터 센터는 홍콩입니다(Azure 동아시아). 이 시나리오에서는 싱가포르에서 동기 모드로 SQL Server AlwaysOn을 실행하는 두 VM으로 현지 고가용성이 제공됩니다.
* 파일 공유 SAP ASCS는 SAP 단일 실패 지점에서 고가용성을 제공합니다. 파일 공유 ASCS에는 클러스터 공유 디스크가 필요하지 않습니다. SIOS와 같은 응용 프로그램도 필요하지 않습니다.
* DBMS 계층에 대한 재해 복구 보호는 비동기 복제를 통해 수행합니다.
* 이 시나리오는 “대칭 재해 복구”를 보여줍니다. 이 용어는 프로덕션과 완전히 똑같은 복제인 재해 복구 솔루션을 의미합니다. 재해 복구 SQL Server 솔루션은 뛰어난 가용성을 자랑합니다. 데이터베이스 계층에서 대칭 재해 복구가 반드시 요구되는 것은 아닙니다. 수많은 고객들이 유연한 클라우드 배포를 활용하여 재해 복구 이벤트 사후에 현지 고가용성 노드를 빠르게 빌드합니다.
* 이 다이어그램은 Site Recovery에 의해 복제된 응용 프로그램 서버 계층과 SAP NetWeaver ASCS를 보여줍니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1.  Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 응용 프로그램용으로 생성한 복구 계획을 선택합니다.
3.  **테스트 장애 조치**를 선택합니다.
4.  테스트 장애 조치(failover) 프로세스를 시작하려면 복구 지점과 Azure 가상 네트워크를 선택합니다.
5.  보조 환경이 가동 중인 경우 유효성 검사를 수행할 수 있습니다.
6.  유효성 검사가 완료되면 장애 조치(failover) 환경을 정리하기 위해 **테스트 장애 조치(failover) 정리**를 선택합니다.

자세한 내용은 [Site Recovery에서 Azure로 테스트 장애 조치(failover)](site-recovery-test-failover-to-azure.md)를 참조하세요.

## <a name="run-a-failover"></a>장애 조치(Failover) 실행

1.  Azure Portal에서 Recovery Services 자격 증명 모음을 선택합니다.
2.  SAP 응용 프로그램용으로 생성한 복구 계획을 선택합니다.
3.  **장애 조치(failover)** 를 선택합니다.
4.  복구 지점을 선택하여 장애 조치(failover) 프로세스를 시작합니다.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Site Recovery를 사용하여 SAP NetWeaver 배포를 위한 재해 복구 솔루션을 빌드하는 방법을 자세히 알아보려면 백서 [SAP NetWeaver: Azure Site Recovery를 사용하여 재해 복구 솔루션 빌드](http://aka.ms/asr-sap)를 다운로드하여 참조하세요. 이 백서에서는 다양한 SAP 아키텍처의 권장 사항에 대해 설명하고, Azure의 SAP에서 지원되는 응용 프로그램 및 VM 유형을 나열하며, 재해 복구 솔루션에서 사용할 수 있는 테스트 계획 옵션에 대해 설명합니다.
* Site Recovery를 사용하여 [다른 워크로드를 복제](site-recovery-workload.md)하는 방법에 대해 알아봅니다.
