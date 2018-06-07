---
title: Azure 결제 처리 청사진 - 방화벽 요구 사항
description: PCI DSS 요구 사항 1
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6f82c828b7cd878cc9ed9f42a4bf63d0d7aedb16
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 규격 환경에 대한 방화벽 요구 사항 
## <a name="pci-dss-requirement-1"></a>PCI DSS 요구 사항 1

**카드 소유자 데이터를 보호하기 위한 방화벽 구성 설치 및 유지 관리**

> [!NOTE]
> 이러한 요구 사항은 [PCI DSS(데이터 보안 표준) 버전 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)의 일부로 [PCI(결제 카드 산업) 보안 표준 위원회](https://www.pcisecuritystandards.org/pci_security/)에 의해 정의됩니다. 각 요구 사항에 대한 테스트 절차 및 지침에 대한 정보는 PCI DSS를 참조하세요.

방화벽은 회사의 네트워크(내부)와 신뢰할 수 없는 네트워크(외부) 간에 허용되는 컴퓨터 트래픽을 제어하는 장치 및 회사의 신뢰할 수 있는 내부 네트워크 내의 중요한 영역으로 들어오고 나가는 트래픽입니다. 카드 소유자 데이터 환경은 회사의 신뢰할 수 있는 네트워크 내의 중요한 영역에 대한 한 예입니다.
방화벽은 모든 네트워크 트래픽을 검사하고 지정된 보안 기준을 충족하지 않는 전송을 차단합니다.
모든 시스템은 전자 상거래로 인터넷을 통하거나, 데스크톱 브라우저, 직원 전자 메일 액세스, 전용 연결(예: B2B(기업 간 전자 상거래) 연결)에 의한 직원 인터넷 액세스로 인터넷을 통하거나, 무선 네트워크를 통하거나, 다른 원본을 통해 시스템에 진입하든 간에 신뢰할 수 없는 네트워크의 무단 액세스로부터 보호되어야 합니다. 종종 신뢰할 수 없는 네트워크에 대해 외견상 의미 없는 경로는 핵심 시스템에 보호되지 않은 경로를 제공할 수 있습니다. 방화벽은 모든 컴퓨터 네트워크에 대한 주요 보호 메커니즘입니다.
다른 시스템 구성 요소는 요구 사항 1에 정의된 방화벽에 대한 최소 요구 사항을 충족하면 방화벽 기능을 제공할 수 있습니다. 다른 시스템 구성 요소가 카드 소유자 데이터 환경 내에서 방화벽 기능을 제공하는 데 사용되는 경우, 이러한 장치는 요구 사항 1의 범위와 평가에 포함되어야 합니다.

## <a name="pci-dss-requirement-11"></a>PCI DSS 요구 사항 1.1

**1.1** 다음 항목을 포함하는 방화벽 및 라우터 구성 표준을 설정하고 구현합니다(1.1.1-1.1.7 참조).


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 격리를 사용하여 CDE의 방화벽을 제공하고, App Service Environment 구현을 통해 데이터의 CDE 수신 및 발신을 보호합니다.<br /><br />[ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)는 준수를 위해 사용되는 프리미엄 서비스 계획입니다. ASE 제어 및 구성에 대한 자세한 내용은 [PCI 지침 - App Service Environment 환경](payment-processing-blueprint.md#app-service-environment)을 참조하세요.|



### <a name="pci-dss-requirement-111"></a>PCI DSS 요구 사항 1.1.1

**1.1.1** 모든 네트워크 연결과 방화벽 및 라우터 구성 변경을 승인 및 테스트하는 공식 프로세스


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 인스턴스는 모든 변경이 올바르게 관리되도록 하기 위해 CI/CD DevOps 모델을 설정합니다. Log Analytics는 광범위한 변경 내용 로깅을 제공합니다. 변경을 검토하고 정확도를 확인할 수 있습니다. 자세한 지침은 [PCI 지침 - OMS(Operations Management Suite)](payment-processing-blueprint.md#logging-and-auditing)를 참조하세요.<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/)는 모든 Azure 리소스의 보안 상태에 대한 중앙 집중식 보기를 제공합니다. 적절한 보안 제어가 준비되고 올바르게 구성되었는지와 주의가 필요한 리소스를 한눈에 빠르게 확인할 수 있습니다.|



### <a name="pci-dss-requirement-112"></a>PCI DSS 요구 사항 1.1.2

**1.1.2** 카드 소유자 데이터 환경과 다른 네트워크(모든 무선 네트워크 포함) 간의 모든 연결을 식별하는 현재 네트워크 다이어그램

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 솔루션의 설치 패턴의 일부로 제공되는 Contoso Webstore 참조 아키텍처 및 디자인 설명서를 참조하세요.|



### <a name="pci-dss-requirement-113"></a>PCI DSS 요구 사항 1.1.3

**1.1.3** 시스템과 네트워크에서 모든 카드 소유자 데이터 흐름을 보여 주는 현재 다이어그램

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore DFD 및 위협 모델을 참조하세요.|



### <a name="pci-dss-requirement-114"></a>PCI DSS 요구 사항 1.1.4

**1.1.4** 각 인터넷 연결 및 DMZ(완충 영역)와 내부 네트워크 영역 간의 방화벽 요구 사항

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 게이트웨이, 네트워크 ACL 및 응용 프로그램 방화벽과 같은 경계 보호 장치를 사용하여 플랫폼 수준에서 내/외부 경계의 통신을 제어합니다. 그런 다음 고객이 자신의 사양 및 요구 사항에 맞게 이러한 장치를 구성합니다. Microsoft Azure는 플랫폼에 들어오는 통신을 필터링합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 PaaS 격리를 사용하여 DMZ를 제공하고, App Service Environment 구현을 통해 데이터의 CDE 수신 및 발신을 보호합니다.<br /><br />[ASE(App Service Environment)](/azure/app-service-web/app-service-app-service-environment-intro)는 준수를 위해 사용되는 프리미엄 서비스 계획입니다. ASE 제어 및 구성에 대한 자세한 내용은 [PCI 지침 - App Service Environment 환경](payment-processing-blueprint.md#app-service-environment)을 참조하세요.|



### <a name="pci-dss-requirement-115"></a>PCI DSS 요구 사항 1.1.5

**1.1.5** 네트워크 구성 요소 관리에 대한 그룹, 역할 및 책임에 대한 설명

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 [Azure RBAC(역할 기반 액세스 제어)](/azure/role-based-access-control/role-assignments-portal)를 사용하여 사용자 역할을 격리합니다. RBAC를 사용하면 Azure에 대해 정확하게 초점을 맞춘 액세스 관리를 사용할 수 있습니다. 구독 액세스 및 Azure Key Vault 액세스에 대한 특정 구성이 있습니다.|



### <a name="pci-dss-requirement-116"></a>PCI DSS 요구 사항 1.1.6

**1.1.6** 허용된 모든 서비스, 프로토콜 및 포트 사용에 대한 비즈니스 타당성 및 승인 설명서(안전하지 않은 것으로 간주되는 프로토콜에 대해 구현된 보안 기능 설명서 포함)

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 RA 디자인 전체에서 필요한 포트 및 프로토콜만 엽니다. 데이터 흐름에 대한 세부 정보는 DFD 및 위협 모델에서 확인할 수 있습니다.|



### <a name="pci-dss-requirement-117"></a>PCI DSS 요구 사항 1.1.7

**1.1.7** 적어도 6개월마다 방화벽 및 라우터 규칙 집합 검토에 대한 요구 사항

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에서는 불필요하거나 사용하지 않는 규칙이 포함되지 않도록 방화벽 규칙 집합을 검토합니다. 데모는 의도적으로 최소 권한과 최소 경로 공간으로 배포됩니다.|



## <a name="pci-dss-requirement-12"></a>PCI DSS 요구 사항 1.2

**1.2** 신뢰할 수 없는 네트워크와 카드 소유자 데이터 환경의 모든 시스템 구성 요소 간의 연결을 제한하는 방화벽 및 라우터 구성을 구축합니다. 

> [!NOTE]
> "신뢰할 수 없는 네트워크"는 검토 대상 회사에 속한 네트워크 외부의 네트워크 및/또는 회사에서 제어하거나 관리할 수 없는 네트워크입니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore의 CDE는 RA 및 배포 설명서에 정의되어 있습니다. 신뢰할 수 없는 네트워크는 의도적으로 거부됩니다.|



### <a name="pci-dss-requirement-121"></a>PCI DSS 요구 사항 1.2.1

**1.2.1** 인바운드 및 아웃바운드 트래픽을 카드 소유자 데이터 환경에 필요한 트래픽으로 제한하고, 특히 다른 모든 트래픽은 거부됩니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore의 CDE는 RA 및 배포 설명서에 정의되어 있습니다. 신뢰할 수 없는 네트워크는 의도적으로 거부됩니다. Contoso Webstore 데모는 Microsoft Azure 응용 프로그램 방화벽을 구성하여 지정된 범위의 IP 주소만 Microsoft Azure 서비스에 액세스하도록 허용합니다. Contoso Webstore는 모든 CDE 경계에서 deny-all(모두 거부) 방화벽을 제공합니다. 모든 구성은 배포 초기 설치 중에 수행됩니다.

> [!NOTE]
> 이 솔루션에는 CDE를 분리하기 위해 ASE(App Service Environment)가 사용됩니다. 그러나 ASE가 DMZ 격리를 구현하여 ASE에서 만든 아웃바운드 연결을 허용하므로 QSA(공인 보안 평가자)가 이 솔루션을 평가하는 것이 필수적입니다. PCI-DSS는 필요하지 않은 모든 인바운드 및 아웃바운드 연결을 차단해야 합니다. ASE가 올바르게 작동하려면 ASE에서 ["App Service Environment에 대한 네트워킹 고려 사항"](/azure/app-service/app-service-environment/network-info)에 정의한 대로 필요에 따라 아웃바운드 연결을 설정합니다. 고객은 솔루션을 프로덕션 환경에 배포하기 전에 QSA와 함께 아웃바운드 연결을 평가하여 요구 사항을 충족하는지 확인해야 합니다. |



### <a name="pci-dss-requirement-122"></a>PCI DSS 요구 사항 1.2.2

**1.2.2** 라우터 구성 파일을 보호하고 동기화합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 Microsoft Azure 기본 네트워크 제어와 동기화된 구성을 제공합니다.|



### <a name="pci-dss-requirement-123"></a>PCI DSS 요구 사항 1.2.3

**1.2.3** 모든 무선 네트워크와 카드 소유자 데이터 환경 간에 경계 방화벽을 설치하고 이러한 방화벽을 거부하도록 구성하거나, 트래픽이 업무용으로 필요한 경우 무선 환경과 카드 소유자 데이터 환경 간에 승인된 트래픽만 허용합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 무선 솔루션이나 기능을 사용하도록 설정되어 있지 않습니다.|



## <a name="pci-dss-requirement-13"></a>PCI DSS 요구 사항 1.3

**1.3** 카드 소유자 데이터 환경에서 인터넷과 모든 시스템 구성 요소 간의 직접적인 공용 액세스를 금지합니다.

**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 방화벽, 부하 분산 장치 및 ACL과 같은 네트워크 기반 및 호스트 기반 경계 보호 장치를 사용합니다. 이러한 장치는 VLAN 격리, NAT 및 패킷 필터링과 같은 메커니즘을 사용하여 고객 트래픽을 인터넷 및 관리 트래픽과 구분합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 시 Azure 응용 프로그램 방화벽의 구성을 제공하여 지정된 IP 주소 범위만 사이트에 액세스할 수 있도록 허용하고 Azure VM을 해당 CDE에 포함합니다.|



### <a name="pci-dss-requirement-131"></a>PCI DSS 요구 사항 1.3.1

**1.3.1** 공용으로 액세스할 수 있는 권한이 있는 서비스, 프로토콜 및 포트를 제공하는 시스템 구성 요소만으로 인바운드 트래픽을 제한하는 DMZ를 구현합니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에서 DMZ를 구현하면 권한이 있는 서비스만 CDE에 연결할 수 있습니다.|



### <a name="pci-dss-requirement-132"></a>PCI DSS 요구 사항 1.3.2

**1.3.2** 인바운드 인터넷 트래픽을 DMZ 내의 IP 주소로 제한합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore에서 DMZ를 구현하면 권한이 있는 서비스만 CDE에 연결할 수 있습니다.|



### <a name="pci-dss-requirement-133"></a>PCI DSS 요구 사항 1.3.3

**1.3.3** 위조된 원본 IP 주소가 네트워크로부터 유입되는 것을 탐지하고 차단하는 스푸핑 방지 조치를 구현합니다. 예를 들어 내부 원본 주소를 사용하여 인터넷에서 시작되는 트래픽을 차단합니다.

**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 스푸핑된 트래픽을 방지하고 들어오고 나가는 트래픽을 신뢰할 수 있는 플랫폼 구성 요소로 제한하기 위한 네트워크 필터링을 구현합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-134"></a>PCI DSS 요구 사항 1.3.4

**1.3.4** 카드 소유자 데이터 환경에서 인터넷으로의 무단 아웃바운드 트래픽을 허용하지 않습니다.


**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 아키텍처는 범위 내 환경에서 인터넷으로의 무단 아웃바운드 트래픽을 방지합니다. 이렇게 하려면 Microsoft Azure에서 승인된 포트 및 프로토콜에 대한 아웃바운드 트래픽 ACL을 구성합니다. 이러한 제어에는 SQL Server 데이터베이스의 CDE에 대한 액세스가 포함됩니다. <br /><br />PaaS SQL Database 인스턴스는 데이터베이스 보안 조치를 보여 주는 데 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-135"></a>PCI DSS 요구 사항 1.3.5

**1.3.5** "설정된" 연결만 네트워크에 허용합니다.


**책임:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 스푸핑된 트래픽을 방지하고 들어오고 나가는 트래픽을 신뢰할 수 있는 플랫폼 구성 요소로 제한하기 위한 네트워크 필터링을 구현합니다. Microsoft Azure 네트워크는 고객 트래픽과 관리 트래픽을 구분하기 위해 분리되어 있습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | 사용할 수 없습니다.|



### <a name="pci-dss-requirement-136"></a>PCI DSS 요구 사항 1.3.6

**1.3.6** DMZ 및 신뢰할 수 없는 다른 네트워크와 분리된 내부 네트워크 영역에 카드 소유자 데이터(예: 데이터베이스)를 저장하는 시스템 구성 요소를 배치합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 네트워크 분리 및 NAT를 사용하여 고객 트래픽과 관리 트래픽을 구분합니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore 아키텍처는 범위 내 환경에서 인터넷으로의 무단 아웃바운드 트래픽을 방지합니다. 이렇게 하려면 Microsoft Azure에서 승인된 포트 및 프로토콜에 대한 아웃바운드 트래픽 ACL을 구성합니다. 이러한 제어에는 SQL Server 데이터베이스의 CDE에 대한 액세스가 포함됩니다. <br /><br />PaaS SQL Database 인스턴스는 데이터베이스 보안 조치를 보여 주는 데 사용됩니다. 자세한 내용은 [PCI 지침 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)를 참조하세요.|



### <a name="pci-dss-requirement-137"></a>PCI DSS 요구 사항 1.3.7

**1.3.7** 사설 IP 주소와 경로 정보를 권한이 없는 당사자에게 공개하지 않습니다. 

> [!NOTE]
> IP 주소 지정을 모호하게 만드는 방법에는 다음 항목이 포함될 수 있지만 제한되지는 않습니다.
> - NAT(Network Address Translation)
> - 카드 소유자 데이터가 포함된 서버를 프록시 서버/방화벽 뒤에 배치합니다.
> - 등록된 주소 지정을 사용하는 사설망에 대한 경로 보급 알림을 제거하거나 필터링합니다.
> - 등록된 주소 대신 RFC1918 주소 공간을 내부적으로 사용합니다.


**책임:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | Microsoft Azure는 NAT(Network Address Translation) 및 네트워크 분리를 사용하여 고객 트래픽과 관리 트래픽을 구분합니다. Azure 장치는 UUID로 고유하게 식별되며 Kerberos를 사용하여 인증됩니다. Azure 관리되는 네트워크 장치는 RFC 1918 IP 주소로 식별됩니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 모든 카드 소유자 데이터를 프록시 서버/방화벽 뒤에 배치하고 RFC1918 주소 공간을 내부적으로 사용합니다.|



## <a name="pci-dss-requirement-14"></a>PCI DSS 요구 사항 1.4

**1.4** 네트워크 외부(예: 직원이 사용하는 랩톱)에서 인터넷에 연결되고 CDE에 액세스하는 데 사용되는 모든 휴대용 컴퓨팅 장치(회사 및/또는 직원 소유 포함)에 개인 방화벽 소프트웨어 또는 이와 동등한 기능을 설치합니다. 방화벽(또는 이와 동등한 기능) 구성에는 특정 구성 설정이 정의된 다음 항목이 포함됩니다.
- 개인 방화벽(또는 이와 동등한 기능)이 실행 중입니다.
- 개인 방화벽(또는 이와 동등한 기능)은 휴대용 컴퓨팅 장치 사용자에 의해 변경될 수 없습니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 최종 사용자 장치를 보호하지 않습니다. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune)을 사용하여 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 장치를 관리할 수 있습니다.|



## <a name="pci-dss-requirement-15"></a>PCI DSS 요구 사항 1.5

**1.5** 방화벽 관리에 대한 보안 정책 및 운영 절차가 문서화되고, 사용되고, 영향을 받는 모든 당사자에게 알려지도록 합니다.

**책임:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **공급자<br />(Microsoft&nbsp;Azure)** | 사용할 수 없습니다. |
| **고객<br />(PCI&#8209;DSS&nbsp;청사진)** | Contoso Webstore는 배포 시 Azure 응용 프로그램 방화벽의 구성을 제공하여 지정된 IP 주소 범위만 사이트에 액세스할 수 있도록 허용하고 Azure VM을 해당 CDE에 포함합니다.|




