---
title: Azure Security 및 Compliance Blueprint - FFIEC 금융 서비스 규제 작업
description: Azure Security 및 Compliance Blueprint - FFIEC 금융 서비스 규제 작업
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: f1339af22132d19f14ea8ebb72fe0e6bd45b7fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895557"
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Azure Security 및 Compliance Blueprint - FFIEC 금융 서비스 규제 작업

## <a name="overview"></a>개요

Azure Security and Compliance Blueprint - FFIEC 금융 서비스 규제 작업은 클라우드의 중요한 데이터를 처리하도록 디자인된 안전하고 호환되는 PaaS(Platform as a Service) 웹 응용 프로그램을 배포하는 데 도움이 됩니다. 이 청사진은 자동화된 스크립트와 간단한 참조 아키텍처를 표시하는 지침, Microsoft Azure 솔루션을 간편하게 도입하는 데 도움이 되는 디자인으로 구성되어 있습니다. 이 청사진은 클라우드의 배포 부담과 비용을 절감하는 방식을 모색 중인 조직의 요구에 부응하는 종단 간 솔루션을 제시합니다.

이 청사진은 American Institute of Certified Public Accountants에서 설정한 엄격한 규정 준수 표준(예: SOC 1, SOC 2), 중요한 재무 데이터의 수집, 저장 및 검색에 대한 Payment Card Industry Data Security Standards Council의 DSS 3.2, FFIEC 요건을 충족하도록 디자인되었습니다. 또한 보안, 규격, 다중 계층 환경에서 재무 데이터를 관리하는 솔루션을 배포하여 이러한 데이터의 적절한 처리를 입증합니다. 이 솔루션은 종단 간 Azure 기반 PaaS 솔루션으로 배포됩니다. 

이 청사진은 고객이 클라우드의 보안 데이터 관리 요구 사항을 구축하고 이해하는 토대로 작성되었습니다. 이 솔루션은 프로덕션 배포에서 있는 그대로 사용하면 안 되며, Azure 서비스를 이해하고 디자인하고 배포하기 위해 사용해야 합니다. 이 솔루션은 고객이 Microsoft Azure를 안전한 규격 방식으로 사용하는 데 도움을 주기 위한 기준으로 디자인되었습니다.

공인된 감사자가 이 청사진을 기준으로 하는 모든 프로덕션 고객 솔루션을 인증해야 합니다. 솔루션은 각 고객의 구현 사양 및 지리적 위치에 따라 달라질 수 있습니다.

이 솔루션의 작동 방식을 간략히 살펴보려면 배포에 대해 설명하고 보여 주는 이 [비디오](https://aka.ms/fsiblueprintvideo)를 확인하세요.

## <a name="solution-components"></a>솔루션 구성 요소

이 아키텍처는 다음과 같은 구성 요소로 이루어지며, Azure PCI DSS 규정 준수 솔루션의 배포 기능을 사용합니다.

- **아키텍처 다이어그램**. 이 다이어그램은 Contoso Webstore 솔루션에 사용되는 참조 아키텍처를 보여 줍니다.
- **배포 템플릿**. 이 배포에서는 설정 중 구성 매개 변수를 지정하여 아키텍처 구성 요소를 Microsoft Azure에 자동으로 설치하는 데 [Azure Resource Manager 템플릿](/azure/azure-resource-manager/resource-group-overview#template-deployment)을 사용합니다.
- **자동화된 배포 스크립트**. 이 스크립트는 종단 간 솔루션 배포를 지원합니다. 스크립트는 다음으로 구성됩니다.
    - 모듈 설치 및 [전역 관리자](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 설치 스크립트는 필요한 PowerShell 모듈을 설치하고 전역 관리자 역할이 올바르게 구성되어 있는지 확인하는 데 사용됩니다. 
    - 설치 PowerShell 스크립트는 종단 간 솔루션을 배포하는 데 사용되며, 미리 작성된 데모 웹 응용 프로그램과 [SQL Database 샘플](https://github.com/Microsoft/azure-sql-security-sample) 콘텐츠가 담긴 .zip 파일 및 .bacpac 파일을 통해 제공됩니다. 이 솔루션의 소스 코드는 [지불 처리 Blueprint 코드 리포지토리][code-repo] 검토에 사용할 수 있습니다. 

## <a name="architectural-diagram"></a>아키텍처 다이어그램

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>사용자 시나리오

청사진은 아래의 사용 사례를 다룹니다.

> 이 시나리오에서는 가상의 웹 스토어가 중요한 데이터를 PaaS 클라우드 Azure 기반 솔루션으로 이동하는 방법을 보여 줍니다. 이 예제 솔루션에서는 기본 사용자 정보와 선택한 중요 데이터의 처리 및 수집을 보여 줍니다. 이 작업은 Azure Security 및 Compliance Blueprint - PCI DSS 규격 지불 처리 환경에서 차용합니다. 이 작업을 확장하려는 경우 ["구현에 대한 검토 및 지침"](https://aka.ms/pciblueprintprocessingoverview) 문서에서 PCI DSS 규격 환경을 검토하는 방법을 제공합니다.

### <a name="use-case"></a>사용 사례
*Contoso Webstore*라는 소규모 웹 스토어는 고객 결제 정보를 포함하는 재무 데이터를 클라우드로 이동할 준비가 되었습니다. 

Contoso Webstore의 관리자는 이 목표를 달성하기 위해 신속하게 배포할 수 있는 솔루션을 찾고 있습니다. 관리자는 이 POC(개념 증명)을 통해 관계자들에게 엄격한 규정 준수 요구에 따라 재무 데이터를 수집, 저장 및 검색하는 데 Azure를 어떻게 사용할 것인지에 대해 논의할 것입니다.

> 요구 사항은 각 고객의 구현 및 지역에서의 특정 상황에 따라 달라질 수 있으므로 이 POC를 사용한 아키텍처를 통해 구축된 솔루션의 적합한 보안 및 준수 검토 수행은 고객의 책임입니다.  

### <a name="elements-of-the-foundational-architecture"></a>기본 아키텍처 요소

기본 아키텍처는 다음과 같은 가상의 요소로 설계되었습니다.

도메인 사이트 `contosowebstore.com`

사용자 역할은 사용 사례를 설명하고 사용자 인터페이스에 대한 정보를 제공하는 데 사용됩니다.

#### <a name="role-site-and-subscription-admin"></a>역할: 사이트 및 구독 관리자 

|항목      |예|
|----------|------|
|사용자 이름: |`adminXX@contosowebstore.com`|
| 이름: |`Global Admin Azure PCI Samples`|
|사용자 유형:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 관리자 계정은 마스크 해제된 재무 정보를 읽을 수 없습니다. 모든 작업이 기록됩니다.
- 관리자 계정은 SQL Database 관리나 로그인이 불가능합니다.
- 관리자 계정은 Active Directory 및 구독을 관리할 수 있습니다.

#### <a name="role-sql-administrator"></a>역할: SQL 관리자

|항목      |예|
|----------|------|
|사용자 이름: |`sqlAdmin@contosowebstore.com`|
| 이름: |`SQLADAdministrator PCI Samples`|
| 이름: |`SQL AD Administrator`|
|성: |`PCI Samples`|
|사용자 유형:| `Administrator`|

- sqladmin 계정은 필터링되지 않은 재무 정보를 볼 수 없습니다. 모든 작업이 기록됩니다.
- sqladmin 계정은 SQL Database를 관리할 수 있습니다.

#### <a name="role-clerk"></a>역할: 직원

|항목      |예|
|----------|------|
|사용자 이름:| `receptionist_EdnaB@contosowebstore.com`|
| 이름: |`Edna Benson`|
| 이름:| `Edna`|
|성:| `Benson`|
| 사용자 유형: |`Member`|

Edna Benson은 리셉셔니스트이자 비즈니스 관리자입니다. 고객 정보가 정확하고 결제가 완료되도록 하는 업무를 담당합니다. Edna는 Contoso Webstore 데모 웹 사이트와의 모든 상호 작용을 위해 로그인한 사용자입니다. Edna는 다음 권한을 가집니다. 

- Edna는 고객 정보를 만들고 읽을 수 있습니다.
- Edna는 고객 정보를 수정할 수 있습니다.
- Edna는 재무 정보를 덮어쓸 수 있습니다.
- Edna 계정은 필터링되지 않은 재무 정보를 볼 수 없습니다.



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 예상 가격

이 기본 아키텍처 및 예제 웹 응용 프로그램에는 솔루션 규모를 정할 때 고려해야 하는 월별 요금 구조 및 시간당 사용 비용이 있습니다. 이러한 비용은 [Azure 비용 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 예상할 수 있습니다. 2017년 9월 현재 이 솔루션의 월별 예상 비용은 최대 2500달러입니다. 여기에는 1000달러/월의 ASE v2 사용료가 포함됩니다. 이러한 비용은 사용 규모에 따라 다르며 변경될 수 있습니다. 더 정확한 예상을 위해 배포 시점에 보다 월별 예상 비용을 계산하는 것은 고객의 책임입니다. 

이 솔루션에서는 다음과 같은 Azure 서비스를 사용했습니다. 배포 아키텍처의 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

>- Application Gateway
>- Azure Active Directory
>- App Service Environment v2
>- Log Analytics
>- Azure Key Vault
>- 네트워크 보안 그룹
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Azure 웹앱
>- Azure Automation
>- Azure Automation Runbook
>- Azure DNS
>- Azure Virtual Network
>- Azure Virtual Machine
>- Azure Resource Group 및 Policies
>- Azure Blob Storage
>- Azure Active Directory RBAC(역할 기반 액세스 제어)

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소를 자세히 설명합니다.

### <a name="network-segmentation-and-security"></a>네트워크 분할 및 보안

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

기본 아키텍처는 WAF(웹 응용 프로그램 방화벽)과 OWASP 규칙 집합을 사용하는 Application Gateway를 통해 보안상 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [종단 간 SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [WAF(웹 응용 프로그램 방화벽)](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) 모드
- [방지 모드](/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용
- [진단 로깅](/azure/application-gateway/application-gateway-diagnostics) 사용
- [사용자 지정 상태 프로브](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) 및 [Azure Advisor](/azure/advisor/advisor-security-recommendations): 추가적인 보호 및 알림을 제공합니다. Azure Security Center는 평판 시스템도 제공합니다.

#### <a name="virtual-network"></a>가상 네트워크

기본 아키텍처는 10.0.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

각 네트워크 계층에는 전용 NSG(네트워크 보안 그룹)가 있습니다.

- 방화벽 및 Application Gateway WAF에 대한 DMZ 네트워크 보안 그룹
- 관리 점프 박스에 대한 NSG(요새 호스트)
- 앱 서비스 환경에 대한 NSG:

각 NSG에는 안전하고 정확한 솔루션 운영을 위해 열린 특정 포트와 프로토콜이 있습니다. 

또한 각 NSG에 대해 다음과 같은 구성을 사용합니다.

- 활성화된 [진단 로그 및 이벤트](/azure/virtual-network/virtual-network-nsg-manage-log)는 저장소 계정에 저장 
- [NSG의 진단](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)에 연결된 Log Analytics

 
#### <a name="subnets"></a>서브넷
 각 서브넷이 해당 NSG에 연결되어야 합니다.

#### <a name="custom-domain-ssl-certificates"></a>사용자 지정 도메인 SSL 인증서
 사용자 지정 도메인 SSL 인증서를 사용하여 HTTPS 트래픽을 활성화합니다.

### <a name="data-at-rest"></a>미사용 데이터

이 아키텍처는 암호화, 데이터베이스 감사 및 기타 방법을 통해 미사용 데이터를 보호합니다.

#### <a name="azure-storage"></a>Azure Storage

암호화된 미사용 데이터 요구 사항에 부합하기 위해 모든 [Azure Storage](https://azure.microsoft.com/services/storage/)는 [Storage Service Encryption](/azure/storage/storage-service-encryption)을 사용합니다.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.

- [AD 인증 및 권한 부여](/azure/sql-database/sql-database-aad-authentication)
- [SQL Database 감사](/azure/sql-database/sql-database-auditing-get-started)
- [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- ASE 작업자 풀 및 클라이언트 IP 관리를 위한 [방화벽 규칙](/azure/sql-database/sql-database-firewall-configure) 
- [SQL 위협 감지](/azure/sql-database/sql-database-threat-detection-get-started)
- [Always Encrypted 열](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- 배포 후 PowerShell 스크립트를 사용한 [SQL Database 동적 데이터 마스킹](/azure/sql-database/sql-database-dynamic-data-masking-get-started)

### <a name="logging-and-auditing"></a>로깅 및 감사

[Log Analytics](https://azure.microsoft.com/services/log-analytics)는 Contoso Webstore에 재무 데이터 로깅을 포함하여 모든 시스템 및 사용자 활동에 대한 광범위한 로깅을 제공할 수 있습니다. 변경을 검토하고 정확도를 확인할 수 있습니다. 

- **활동 로그.**  [활동 로그](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다.
- **진단 로그.**  [진단 로그](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그입니다. 이러한 로그는 Windows 이벤트 시스템 로그, Azure Blob 저장소 로그, 테이블 및 큐 로그를 포함합니다.
- **방화벽 로그.**  Application Gateway는 전체 진단 및 액세스 로그를 제공합니다. 방화벽 로그는 WAF가 활성화된 Application Gateway 리소스에 사용할 수 있습니다.
- **로그 보관.**  모든 진단 로그는 보관을 위해 중앙 및 암호화된 Azure Storage 계정에 기록하도록 구성되며 정의된 보존 기간(2일)이 있습니다. 그런 다음 로그는 처리, 저장, 대시보드 작업을 위해 Azure Log Analytics에 연결됩니다. [Log Analytics](https://azure.microsoft.com/services/log-analytics)는 클라우드 및 온-프레미스 환경의 리소스로 생성된 데이터를 수집 및 분석할 수 있게 하는 Log Analytics 서비스입니다.

### <a name="encryption-and-secrets-management"></a>암호화 및 비밀 관리

Contoso Webstore 모든 중요 데이터를 암호화하며 Azure Key Vault를 사용하여 키를 관리하여 CHD 검색을 방지합니다.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption)는 모든 고객의 재무 데이터를 암호화하는 데 사용됩니다.
- 데이터는 [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) 및 BitLocker를 통해 디스크에 저장됩니다.

### <a name="identity-management"></a>ID 관리

다음 기술은 Azure 환경에서 ID 관리 기능을 제공합니다.

- [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. SQL Database에 액세스하는 사용자를 포함하여 모든 솔루션 사용자가 Azure Active Directory에서 만들어졌습니다.
- 응용 프로그램에 대한 인증은 Azure AD를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화도 Azure AD를 사용하여 Azure SQL Database에 대해 응용 프로그램을 인증합니다. 자세한 내용은 [Always Encrypted: SQL Database에서 중요 데이터 보호](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요. 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미칠 수 있는 잠재적인 취약점을 검색하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대처를 구성하며, 의심스러운 사건을 조사하고, 그 해결을 위해 적합한 조치를 취합니다.
- [Azure RBAC(Role-based Access Control)](/azure/role-based-access-control/role-assignments-portal)은 Azure에 대해 세밀하게 초점을 맞춘 액세스 관리를 구현합니다. 구독 액세스는 구독 관리자로 제한되며 Azure Key Vault 액세스는 모든 사용자로 제한됩니다.

Azure SQL Database의 보안 기능 사용에 대한 자세한 내용은 [Contoso Clinic 데모 응용 프로그램](https://github.com/Microsoft/azure-sql-security-sample) 샘플을 참조하세요.
   
### <a name="web-and-compute-resources"></a>웹 및 계산 리소스 

#### <a name="app-service-environment"></a>App Service 환경

[Azure App Service](/azure/app-service/)는 웹 앱 배포를 위한 관리되는 서비스입니다. Contoso Webstore 응용 프로그램은 [App Service 웹앱](/azure/app-service-web/app-service-web-overview) 형태로 배포됩니다.

[Azure App Service Environment(ASE v2)](/azure/app-service/app-service-environment/intro)는 Azure App Service 앱을 매우 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 Azure App Service 기능입니다. PCI DSS 규정 준수를 활성화하기 위해 이 기본 아키텍처에서 사용하는 프리미엄 서비스 계획입니다.

ASE는 단일 고객의 응용 프로그램만을 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다. 고객은 인바운드 및 아웃바운드 응용 프로그램 네트워크 트래픽 둘 다에 대해 세밀하게 제어할 수 있고 응용 프로그램은 가상 네트워크를 통해 온-프레미스 회사 리소스에 고속 보안 연결을 설정할 수 있습니다.

이 아키텍처에 대한 ASE 사용은 다음 제어/구성에 대해 허용됩니다.

- 보안 가상 네트워크 및 네트워크 보안 규칙 안의 호스트
- HTTPS 통신을 위해 자체 서명된 ILB 인증서가 있게 구성된 ASE
- [내부 부하 분산 모드](/azure/app-service-web/app-service-environment-with-internal-load-balancer)(모드 3)
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) 사용 안 함
- [TLS 암호화](/azure/app-service-web/app-service-app-service-environment-custom-settings) 변경
- [인바운드 트래픽 N/W 포트](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 제어 
- [WAF – 데이터 제한](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [SQL Database 트래픽](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) 허용


#### <a name="jumpbox-bastion-host"></a>점프박스(요새 호스트)

App Service 환경은 보호되고 잠겨 있으므로 Kudu를 사용한 웹 앱 모니터링 기능처럼 필요한 DevOps 릴리스나 변경 내용을 허용하는 메커니즘이 필요합니다. 가상 머신은 TCP 3389 이외의 포트에서 VM에 연결할 수 있게 하는 NAT Load Balancer 뒤에서 보호됩니다. 

다음 구성을 통해 가상 머신을 점프박스(요새 호스트)로 만듭니다.

-   [맬웨어 방지 확장](/azure/security/azure-security-antimalware)
-   [Log Analytics 확장](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 진단 확장](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault를 사용하는 [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) 
-   사용하지 않을 때 가상 머신 사용을 줄이는 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)

### <a name="security-and-malware-protection"></a>보안 및 맬웨어 방지

[Azure Security Center](https://azure.microsoft.com/services/security-center/)는 모든 Azure 리소스의 보안 상태에 대한 중앙 집중식 보기를 제공합니다. 적절한 보안 제어가 준비되고 올바르게 구성되었는지와 주의가 필요한 리소스를 한눈에 빠르게 확인할 수 있습니다.  

[Azure Advisor](/azure/advisor/advisor-overview)는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용량 원격 분석을 수행하고 Azure 리소스의 경제성, 성능, 고가용성 및 보안을 개선하는 데 도움이 되는 해결 방법을 권장합니다.

Azure Cloud Services 및 Virtual Machines용 [Microsoft Antimalware](/azure/security/azure-security-antimalware)는 악성 또는 원치 않는 소프트웨어가 사용자의 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능입니다.

### <a name="operations-management"></a>운영 관리

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/)를 사용하여 응용 프로그램 성능 관리와 즉각적인 분석으로 실행 가능하고 통찰력 있는 정보를 확보합니다.

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/)는 클라우드 및 온-프레미스 환경의 리소스에서 생성된 데이터를 수집 및 분석하도록 도와주는 Azure의 서비스입니다.

#### <a name="managment-solutions"></a>관리 솔루션

이러한 추가 관리 솔루션을 고려하고 구성해야 합니다. 
- [활동 로그 분석](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure Networking Analytics](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [변경 내용 추적](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Key Vault 분석](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [서비스 맵](/azure/operations-management-suite/operations-management-suite-service-map)
- [보안 및 감사](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [맬웨어 방지](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [업데이트 관리](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Security Center 통합

기본 배포는 정상 상태이며 안전한 구성 상태를 나타내는 보안 센터 권장 사항의 기준을 제공하기 위한 것입니다. Azure Security Center에서 데이터 수집을 사용할 수 있습니다. 자세한 내용은 [Azure Security Center - 시작](/azure/security-center/security-center-get-started)을 참조하세요.

## <a name="deploy-the-solution"></a>솔루션 배포

이 솔루션의 배포를 위한 구성 요소는 [Blueprint 코드 리포지토리][code-repo]에 제공됩니다. 기본 아키텍처의 배포에는 Microsoft PowerShell v5를 통해 실행되는 여러 단계가 필요합니다. 웹 사이트에 연결하려면 사용자 지정 도메인 이름(예: contoso.com)을 제공해야 합니다. 이 이름은 2단계에서 `-customHostName` 스위치로 지정됩니다. 자세한 내용은 [Azure Web Apps에 대한 사용자 지정 도메인 이름 구매](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)를 참조하세요. 사용자 지정 도메인 이름은 솔루션의 성공적인 배포 및 실행을 위해 필수는 아니지만 없으면 데모용 웹 사이트에 연결할 수 없습니다.

이 스크립트는 사용자가 지정한 Azure AD 테넌트에 도메인 사용자를 추가합니다. 테스트로 새 Azure AD 테넌트를 만들어 사용하는 것이 좋습니다.

배포 중에 문제가 발생하면 [FAQ 및 문제 해결](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)을 참조하세요.

솔루션 배포에 사용할 PowerShell을 새로 설치하는 것이 좋습니다. 또는 설치 스크립트의 적절한 실행에 필요한 최신 모듈을 사용하고 있는지 확인합니다. 이 예제에서는 점프박스(요새 호스트)에 로그인하여 다음 명령을 실행합니다. 그러면 사용자 지정 도메인 명령이 활성화됩니다.


1. 필요한 모듈을 설치하고 관리자 역할을 정확하게 설정합니다.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    자세한 사용 지침은 [스크립트 지침 - 관리자 계정 및 권한 설정](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md)을 참조하세요.
    
2. solution-update-management를 설치합니다. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    자세한 사용 지침은 [스크립트 지침 - Azure Resources 배포 및 구성](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)을 참조하세요.
    
3. Log Analytics 로깅 및 모니터링. 솔루션을 배포한 후에는 Log Analytics 작업 영역을 열 수 있고, 솔루션 저장소에서 제공한 샘플 템플릿을 사용하여 모니터링 대시보드의 구성 방법을 설명할 수 있습니다. 샘플 템플릿은 [omsDashboards 폴더](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)를 참조하세요. 템플릿을 올바르게 배포하려면 Log Analytics에 데이터를 수집해야 합니다. 사이트 작업에 따라 최대 1시간 이상 걸릴 수 있습니다.
 
    Log Analytics 로깅을 설정할 때 이러한 리소스를 포함하는 것이 좋습니다.
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>위협 모델

Contoso Webstore [Blueprint 위협 모델](https://aka.ms/pciblueprintthreatmodel)의 데이터 흐름 다이어그램(DFD) 및 샘플 위협 모델입니다.

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>고객 책임 매트릭스

고객은 고객이 책임을 지는 FFIEC 요구 사항과 Microsoft Azure가 책임을 지는 요구 사항을 간략하게 설명한 [책임 요약 매트릭스](https://aka.ms/fsiblueprintcrm)의 복사본을 유지할 책임이 있습니다.



## <a name="disclaimer-and-acknowledgments"></a>고지 사항 및 승인

*2017년 9월*

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft 및 AVYAN은 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.  
- 이 문서는 Microsoft 제품 또는 Avyan 제품이나 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.  
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.  

  > [!NOTE]
  > 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.  

- 이 문서의 솔루션은 기본 아키텍처용이며 있는 그대로 프러덕션 용도로 사용되어서는 안 됩니다. 규정 준수를 실현하려면 고객은 감사자에게 최종 고객 솔루션이 유효한지 문의해야 합니다.  
- 이 페이지에 등장하는 모든 고객 이름, 트랜잭션 레코드 및 관련 데이터는 허구이며 이 기본 아키텍처용으로 만들어져 설명 목적으로만 제공됩니다. 어떠한 실제 사례와도 연관시킬 의도가 없으며 그렇게 유추해서도 안 됩니다.  
- 이 솔루션에서 Microsoft와 Avyan Consulting이 공동으로 개발하였으며 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 제공됩니다.

