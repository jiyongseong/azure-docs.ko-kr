---
title: Azure 보안 및 규정 준수 청사진 - GDPR 준수 IaaS 웹 응용 프로그램
description: Azure 보안 및 규정 준수 청사진 - GDPR 준수 IaaS 웹 응용 프로그램
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161856"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure 보안 및 규정 준수 청사진 - GDPR 준수 IaaS 웹 응용 프로그램

## <a name="overview"></a>개요
GDPR(일반 데이터 보호 규정)에는 조직에서 개인 정보를 식별하고 보호하는 방법, 투명성 요구 사항을 조정하는 방법, 개인 데이터 침해를 탐지하고 보고하는 방법, 개인 정보 담당 직원과 다른 직원을 교육하는 방법을 포함하여 개인 정보를 수집, 저장 및 사용하는 방법에 대한 다양한 요구 사항이 포함되어 있습니다. GDPR은 개인에게 개인 데이터에 대한 더 강화된 제어를 제공하고, 개인 데이터를 수집, 처리 또는 분석하는 조직에 대해 많은 새로운 의무를 부과합니다. GDPR은 EU(유럽 연합)의 사용자에게 상품과 서비스를 제공하거나 EU 거주자와 관련된 데이터를 수집하고 분석하는 조직에 새로운 규칙을 적용합니다. GDPR은 조직의 위치와 관계없이 적용됩니다.

Microsoft는 GDPR에서 식별되는 개인 데이터의 범주를 포함하여 클라우드의 데이터를 보호하기 위해 업계를 선도하는 보안 조치와 및 개인 정보 보호 정책을 갖춘 Azure를 설계했습니다. Microsoft의 [계약 조건](http://aka.ms/Online-Services-Terms)은 Microsoft의 프로세서 요구 사항을 따릅니다.

이 Azure 보안 및 규정 준수 청사진은 간단한 인터넷 연결 웹 응용 프로그램에 적합한 IaaS(Infrastructure as a Service) 환경을 배포하기 위한 지침을 제공합니다. 이 솔루션은 고객이 GDPR의 특정 보안 및 규정 준수 요구 사항을 충족할 수 있는 방법을 보여 주고, 고객이 Azure에서 자신의 IaaS 웹 응용 프로그램 솔루션을 구축하고 구성할 수 있는 기반을 제공합니다. 고객은 이 참조 아키텍처를 활용하고 GDPR 준수 과정에서 Microsoft의 다음 [4단계 프로세스](https://aka.ms/gdprebook)를 따를 수 있습니다.
1. 검색: 존재하는 개인 데이터와 해당 위치를 식별합니다.
2. 관리: 개인 데이터를 사용하고 액세스하는 방법을 관리합니다.
3. 보호: 취약성 및 데이터 침해를 방지, 탐지 및 대응하기 위한 보안 제어를 설정합니다.
4. 보고: 필요한 문서를 보관하고, 데이터 요청과 위반 알림을 관리합니다.

이 참조 아키텍처, 관련 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이 되며, 프로덕션 환경에서 있는 그대로 사용하면 안됩니다. 다음 사항에 유의하세요.
- 아키텍처는 고객이 GDPR 규격 방식으로 워크로드를 Azure에 배포할 수 있도록 지원하기 위한 기준을 제공합니다.
- 요구 사항이 각 고객의 특정 구현에 따라 달라질 수 있으므로 고객은 이 아키텍처를 사용하여 구축된 솔루션에 대한 적절한 보안 및 규정 준수 평가를 수행해야 합니다. 

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
이 솔루션은 SQL Server 백 엔드가 있는 IaaS 웹 응용 프로그램에 대한 참조 아키텍처를 배포합니다. 이 아키텍처에는 웹 계층, 데이터 계층, Active Directory 인프라, Application Gateway 및 Load Balancer가 포함됩니다. 웹 및 데이터 계층에 배포된 Virtual Machines는 가용성 집합에서 구성되며 SQL Server 인스턴스는 고가용성을 위해 AlwaysOn 가용성 그룹에 구성됩니다. Virtual Machines는 도메인에 조인되며 Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.  관리 요새 호스트는 관리자가 배포된 리소스에 액세스할 수 있는 보안 연결을 제공합니다. **Azure에서는 관리 및 참조 아키텍처 서브넷에 데이터 가져오기를 위해 VPN 또는 ExpressRoute 연결을 구성하는 것이 좋습니다**.

![GDPR 준수 IaaS 웹 응용 프로그램에 대한 참조 아키텍처 다이어그램](images/gdpr-iaaswa-architecture.png?raw=true "GDPR 준수 IaaS 웹 응용 프로그램에 대한 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처의 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

- Azure Virtual Machines
    - (1) 관리/요새(Windows Server 2016 Datacenter)
    - (2) Active Directory 도메인 컨트롤러(Windows Server 2016 Datacenter)
    - (2) SQL Server 클러스터 노드(Windows Server 2016의 SQL Server 2017)
    - (2) Web/IIS(Windows Server 2016 Datacenter)
- 가용성 집합
    - (1) Active Directory 도메인 컨트롤러
    - (1) SQL 클러스터 노드
    - (1) 웹/IIS
- Azure Virtual Network
    - (1) /16 가상 네트워크
    - (5) /24 서브넷
    - DNS 설정이 모든 도메인 컨트롤러에 설정됨
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF 응용 프로그램 게이트웨이 사용
        - 방화벽 모드: 방지
        - 규칙 집합: OWASP 3.0
        - 수신기: 443 포트
- Azure Storage
    - (7) 지역 중복 저장소 계정
- 클라우드 감시
- Recovery Services 자격 증명 모음
- Azure Key Vault
- AAD(Azure Active Directory)
- Azure 리소스 관리자
- OMS(Operations Management Suite)
- Azure Security Center

## <a name="deployment-architecture"></a>배포 아키텍처
다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

**요새 호스트**: 요새 호스트는 사용자가 이 환경에서 배포된 리소스에 액세스할 수 있도록 하는 단일 진입점입니다. 요새 호스트는 안전한 목록에 있는 IP 주소의 원격 트래픽만 허용하여 배포된 리소스에 대한 보안 연결을 제공합니다. RDP(원격 데스크톱 프로토콜) 트래픽을 허용하려면 트래픽의 원본을 NSG(네트워크 보안 그룹)에 정의해야 합니다.

이 솔루션은 다음 구성을 사용하여 가상 머신을 도메인에 조인된 요새 호스트로 만듭니다.
-   [맬웨어 방지 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 진단 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault를 사용하는 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   사용하지 않을 때 가상 머신 사용을 줄이는 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)를 사용하도록 설정하여 자격 증명과 다른 비밀이 실행 중인 운영 체제와 격리된 보호된 환경에서 실행됩니다.

### <a name="virtual-network"></a>가상 네트워크
아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: 이 솔루션은 가상 네트워크 내에서 별도의 웹 서브넷, 데이터베이스 서브넷, Active Directory 서브넷 및 관리 서브넷이 있는 아키텍처에 리소스를 배포합니다. 서브넷은 서브넷 간 트래픽을 시스템 및 관리 기능에 필요한 경우로만 제한하기 위해 개별 서브넷에 적용되는 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다.

이 솔루션으로 배포된 [네트워크 보안 그룹](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)에 대한 구성을 참조하세요. 조직에서는 [이 설명서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)를 참조하여 위의 파일을 편집하여 네트워크 보안 그룹을 구성할 수 있습니다.

각 서브넷에는 전용 NSG(네트워크 보안 그룹)가 있습니다.
- 1 NSG - Application Gateway용(LBNSG)
- 1 NSG - 요새 호스트용(MGTNSG)
- 1 NSG - 기본 및 백업 도메인 컨트롤러용(ADNSG)
- 1 NSG - SQL Server 및 클라우드 감시용(SQLNSG)
- 1 NSG - 웹 계층용(WEBNSG)

### <a name="data-in-transit"></a>전송 중 데이터
Azure는 기본적으로 Azure 데이터 센터와의 모든 통신을 암호화합니다. 또한 Azure Portal을 통한 Azure Storage에 대한 모든 트랜잭션은 HTTPS를 통해 발생합니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 암호화 및 데이터베이스 감사를 포함하여 다른 방법을 통해 미사용 데이터를 보호합니다.

**Azure Storage**: 암호화된 미사용 데이터 요구 사항을 충족하기 위해 모든 [Azure Storage](https://azure.microsoft.com/services/storage/)에서 [저장소 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다. 이렇게 하면 GDPR에서 정의한 조직의 보안 약정 및 준수 요구 사항을 지원하기 위해 개인 데이터를 보호할 수 있습니다.

**Azure Disk Encryption**: Azure Disk Encryption은 암호화된 Windows IaaS 가상 머신 디스크에 사용됩니다. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다.

**SQL Server**: SQL Server 인스턴스에서 사용하는 데이터베이스 보안 조치는 다음과 같습니다.
-   [AD 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 한 곳에서 집중적으로 관리할 수 있습니다.
-   [SQL Database 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
-   SQL Database는 [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하여 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 해독을 수행하고 미사용 정보를 보호하도록 구성됩니다. TDE는 저장된 개인 데이터가 무단 액세스의 영향을 받지 않도록 보장합니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 주입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다.
-   [Always Encrypted 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 개인 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않도록 보장합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 응용 프로그램 또는 응용 프로그램 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [확장 속성](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) 기능을 사용하면 관련된 개인 데이터를 처리하지 못하도록 하는 응용 프로그램 논리를 지원하기 위해 사용자가 데이터베이스 개체에 사용자 지정 속성을 추가하고 데이터를 "Discontinued" 태그로 지정할 수 있으므로 데이터 주체 처리를 중단할 수 있습니다.
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)을 사용하면 사용자가 데이터 액세스를 제한하여 처리를 중단하는 정책을 정의할 수 있습니다.
- [SQL Database DDM(동적 데이터 마스킹)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 권한이 없는 사용자 또는 응용 프로그램에 데이터를 마스킹하여 중요한 개인 데이터의 노출을 제한합니다. DDM은 잠재적으로 중요한 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 이렇게 하면 GDPR 보호에 적합한 개인 데이터를 식별하고 무단 액세스를 통해 데이터베이스를 종료하지 못하도록 액세스를 줄일 수 있습니다. **참고: 고객은 데이터베이스 스키마를 준수하도록 DDM 설정을 조정해야 합니다.**

### <a name="identity-management"></a>ID 관리
Azure 환경에서 개인 데이터에 대한 액세스를 관리하는 기능을 제공하는 기술은 다음과 같습니다.
-   [AAD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 이 솔루션의 모든 사용자는 SQL Server에 액세스하는 사용자를 포함하여 AAD에 만들어집니다.
-   응용 프로그램에 대한 인증은 AAD를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화도 AAD를 사용하여 SQL Server에 대해 응용 프로그램을 인증합니다.  자세한 내용은 [SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
-   [Azure RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 개인 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자에게만 허용됩니다.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하면 고객이 특정 리소스에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다.  관리자는 AAD PIM을 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- [AAD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)는 조직의 ID에 영향을 미치는 잠재적 취약성을 탐지하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대응을 구성하며, 의심스러운 인시던트를 조사하여 이를 해결하기 위한 적절한 조치를 수행합니다.
- 배포된 IaaS Active Directory 인스턴스는 배포된 IaaS 가상 머신에 대한 OS 수준의 ID 관리를 제공합니다.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 고객이 개인 데이터를 보호하고 이러한 데이터에 액세스할 수 있도록 하는 Azure Key Vault 기능은 다음과 같습니다.
- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 HSM(하드웨어 보안 모듈)을 통해 보호됩니다. 키 유형은 HSM 보호 2048비트 RSA 키입니다.
- RBAC를 사용하여 모든 사용자와 ID에 최소 필수 권한이 부여됩니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다
- 이 솔루션은 Azure Key Vault와 통합되어 IaaS 가상 머신 디스크 암호화 키와 비밀을 관리할 수 있도록 합니다.

**패치 관리**: 이 참조 아키텍처의 일부로 배포된 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 또한 이 솔루션에는 필요한 경우 가상 머신을 패치하기 위해 업데이트된 배포를 만들 수 있는 OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) 서비스가 포함되어 있습니다.

**맬웨어 방지**: Virtual Machines용 [Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware)은 알려진 악성 또는 원치 않는 소프트웨어에서 보호된 가상 머신에 설치하거나 실행하려고 시도할 때 구성 가능한 경고를 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 실시간 보호 기능을 제공합니다.

**보안 경고**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)를 사용하면 트래픽을 모니터링하고, 로그를 수집하고, 위협에 대한 데이터 원본을 분석할 수 있습니다. 또한 Azure Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 테세를 개선하고 개인 데이터를 보호하는 데 유용한 구성과 서비스 권장 사항을 제공합니다. Azure Security Center에는 인시던트 대응 팀이 위협을 조사하고 수정하도록 지원하기 위해 탐지된 각 위협에 대한 [위협 인텔리전스 보고서](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report)가 포함되어 있습니다.

**Application Gateway**: 이 아키텍처는 WAF(웹 응용 프로그램 방화벽)와 OWASP 규칙 집합을 사용하도록 설정된 Application Gateway를 통해 보안 취약성의 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [WAF(웹 응용 프로그램 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 모드
- [방지 모드](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용
- [진단 로깅](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics) 사용
- [사용자 지정 상태 프로브](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) 및 [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations)는 추가적인 보호 및 알림을 제공합니다. Azure Security Center는 평판 시스템도 제공합니다.

### <a name="business-continuity"></a>비즈니스 연속성

**고가용성**: 이 솔루션은 모든 가상 머신을 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)에 배포합니다. 가용성 집합을 사용하면 가상 머신을 격리된 여러 하드웨어 클러스터에 분산하여 가용성을 높일 수 있습니다. 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 가상 머신을 하나 이상 사용할 수 있습니다.

**Recovery Services 자격 증명 모음**: [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)은 백업 데이터를 저장하며, 이 아키텍처에 있는 Azure Virtual Machines의 모든 구성을 보호합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있으므로 복원 시간이 단축됩니다.

**클라우드 감시**: [클라우드 감시](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)는 Azure를 중재 지점으로 활용하는 Windows Server 2016의 장애 조치 클러스터 쿼럼 감시의 한 유형입니다. 다른 모든 쿼럼 감시와 마찬가지로 클라우드 감시는 투표를 받고 쿼럼 계산에 참여할 수 있지만 공개적으로 사용 가능한 표준 Azure Blob Storage를 사용합니다. 따라서 공용 클라우드에서 호스팅되는 VM에 대한 추가적인 유지 관리 오버헤드가 필요하지 않습니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

OMS는 시스템 및 사용자 활동, 시스템 상태에 대한 광범위 로깅을 제공합니다. OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 솔루션은 Azure 및 온-프레미스 환경의 리소스에서 생성된 데이터를 수집하고 분석합니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 통찰력을 제공합니다. 활동 로그는 작업의 초기자, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)에는 모든 리소스에서 내보낸 모든 로그가 포함됩니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Storage 로그, Key Vault 감사 로그 및 Application Gateway 액세스 및 방화벽 로그가 포함됩니다.
- **로그 보관**: 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure 저장소 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다. 이러한 로그는 처리, 저장, 대시보드 보고를 위해 Azure Log Analytics에 연결됩니다.

또한 이 아키텍처의 일부로 포함된 OMS 솔루션은 다음과 같습니다.
-   [AD 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 확인 솔루션은 표준 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
-   [맬웨어 방지 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): 맬웨어 방지 솔루션은 맬웨어, 위협 및 보호 상태를 보고합니다.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation 솔루션은 Runbook을 저장, 실행 및 관리합니다.
-   [보안 및 감사](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): 보안 및 감사 대시보드는 보안 도메인, 주목할 만한 문제, 탐지, 위협 인텔리전스 및 일반적인 보안 쿼리에 대한 메트릭을 제공하여 리소스의 보안 상태에 대한 높은 수준의 통찰력을 제공합니다.
-   [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 준 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
-   [업데이트 관리](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): 업데이트 관리 솔루션을 사용하면 고객이 사용 가능한 업데이트 상태 및 필수 업데이트 설치 프로세스를 포함하여 운영 체제 보안 업데이트를 관리할 수 있습니다.
-   [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포되는 에이전트 수와 해당 지리적 분포, 응답이 없는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
-   [Azure 활동 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) 활동 로그 분석 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.
-   [변경 내용 추적](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 변경 내용 추적 솔루션을 사용하면 고객이 환경의 변경 내용을 쉽게 식별할 수 있습니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 DFD(데이터 흐름 다이어그램)는 [다운로드](https://aka.ms/gdprIaaSdfd)에서 사용할 수 있거나 아래에 나와 있습니다. 이 모델을 사용하면 고객이 수정할 때 발생할 수 있는 시스템 인프라의 잠재적 위험 요소를 파악할 수 있습니다.

![GDPR 준수 IaaS 웹 응용 프로그램 위협 모델](images/gdpr-iaaswa-threat-model.png?raw=true "GDPR 준수 IaaS 웹 응용 프로그램 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서

[Azure 보안 및 규정 준수 청사진 – GDPR 고객 책임 매트릭스](https://aka.ms/gdprCRM)는 모든 GDPR 문서에 대한 컨트롤러 및 프로세서 책임을 나열합니다. Azure 서비스의 경우 일반적으로 고객이 컨트롤러이며 Microsoft는 프로세서 역할을 합니다.

[Azure 보안 및 규정 준수 청사진 - GDPR IaaS 웹 응용 프로그램 구현 매트릭스](https://aka.ms/gdprIaaSweb)는 구현이 각 대상 문서의 요구 사항을 충족시키는 방법에 대한 자세한 설명을 포함하여 GDPR 문서가 IaaS 웹 응용 프로그램 아키텍처에서 처리되는 정보를 제공합니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항
### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
이 IaaS 웹 응용 프로그램 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객이 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포한 안전하고 완성도가 높은 기술입니다. [IPsec 터널 모드](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 이에 따라 노출되지 않습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.  
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.  
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.  
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.  
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
