---
title: Azure 운영 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure 운영 보안을 위한 모범 사례 모음을 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 75f0d7ffaf92558b16605c29cd99ee439ab2e3d8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security-best-practices"></a>Azure 운영 보안 모범 사례
Azure 운영 보안은 사용자가 Microsoft Azure에서 자신의 데이터, 응용 프로그램 및 기타 자산을 보호할 수 있는 서비스, 제어 및 기능을 나타냅니다. Azure 운영 보안은 Microsoft SDL(Security Development Lifecycle), Microsoft 보안 대응 센터 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

이 문서에서는 Azure 데이터베이스 보안 모범 사례 모음에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터베이스 보안 경험과 여러분과 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.
-   각 모범 사례
-   해당 모범 사례를 사용해야 하는 이유
-   해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
- 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 운영 보안 모범 사례 문서는 이 문서가 작성된 당시에 있었던 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 다루는 Azure 운영 보안 모범 사례는 다음과 같습니다.

-   클라우드 인프라 모니터링, 관리 및 보호
-   ID 관리 및 SSO(Single Sign-On) 구현
-   요청 추적, 사용 추세 분석 및 문제 진단
-   중앙 집중식모니터링 솔루션을 사용한 서비스 모니터링 
-   위협 방지, 검색 및 대응
-   종단 간 시나리오 기반 네트워크 모니터링
-   검증된 DevOps 도구를 사용한 배포 보안

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>클라우드 인프라 모니터링, 관리 및 보호
IT 운영 팀은 이러한 시스템의 안정성과 보안을 포함하여 데이터 센터 인프라, 응용 프로그램 및 데이터를 관리하는 역할을 담당합니다. 그러나 점점 더 복잡해지는 IT 환경에서 보안 정보를 얻으려면 조직에서 여러 보안 및 관리 시스템의 데이터를 급하게 맞추어야 합니다.

[Microsoft OMS(Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)는 온-프레미스 및 클라우드 인프라를 관리하고 보호하는 데 유용한 Microsoft의 클라우드 기반 IT 관리 솔루션입니다.

[OMS 보안 및 감사 솔루션](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)을 사용하면 IT가 모든 리소스를 능동적으로 모니터링하여 보안 문제의 영향을 최소화할 수 있습니다. OMS 보안 및 감사에는 리소스 모니터링에 사용할 수 있는 보안 도메인이 있습니다.

OMS에 대한 자세한 내용은 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)문서를 참조하세요.

위협을 방지, 감지하고 위협에 응답할 수 있도록 [OMS(Operations Management Suite) 보안 및 감사 솔루션](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)에서는 다음을 포함하여 리소스에 대한 데이터를 수집하고 처리합니다.

-   보안 이벤트 로그
-   Windows (ETW) 이벤트에 대한 이벤트 추적
-   AppLocker 감사 이벤트
-   Windows 방화벽 로그
-   고급 위협 분석 이벤트
-   기준 평가 결과
-   맬웨어 방지 평가 결과
-   업데이트/패치 평가 결과
-   에이전트에 명시적으로 활성화되어 있는 Syslog 스트림


## <a name="manage-identity-and-implement-single-sign-on"></a>ID 관리 및 SSO(Single Sign-On) 구현
[Azure AD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.

또한 [Azure AD](https://azure.microsoft.com/services/active-directory/)는 [다단계 인증](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), 장치 등록, 셀프 서비스 암호 관리, 셀프 서비스 그룹 관리, 권한 있는 계정 관리, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 광범위한 감사 및 보안 모니터링 및 경고를 포함하여 완벽한 [ID 관리 기능](https://docs.microsoft.com/azure/security/security-identity-management-overview) 제품군을 갖추고 있습니다.

다음 기능을 통해 클라우드 기반 응용 프로그램을 보호하고, IT 프로세스를 간소화하고, 비용을 절감하고, 회사의 규정 준수 목표를 충족할 수 있도록 합니다.

-   클라우드에 대한 ID 및 액세스 관리
-   클라우드 응용 프로그램에 대한 사용자 액세스 간소화
-   중요한 데이터 및 응용 프로그램 보호
-   직원을 위한 셀프 서비스 지원
-   Azure Active Directory와 통합

### <a name="identity-and-access-management-for-the-cloud"></a>클라우드에 대한 ID 및 액세스 관리
Azure Active Directory(Azure AD)는 포괄적인 [ID 및 액세스 관리 클라우드 솔루션](https://www.microsoft.com/cloud-platform/identity-management)으로 사용자 및 그룹 관리를 위한 강력한 기능 모음을 제공합니다. Office 365와 같은 Microsoft 웹 서비스와 다양한 타사 SaaS(Software as-a Service) 응용 프로그램을 포함한 온-프레미스 및 클라우드 응용 프로그램에 대한 안전한 액세스를 돕습니다.
Azure AD에서 ID 보호를 활성화하는 방법은 [Azure Active Directory ID 보호 활성화](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)를 참조하세요.

### <a name="simplify-user-access-to-any-cloud-app"></a>클라우드 응용 프로그램에 대한 사용자 액세스 간소화
[SSO(Single Sign-On)](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)를 통해 Windows, Mac, Android 및 iOS 장치에서 수천 개의 클라우드 응용 프로그램에 대한 사용자 액세스를 간소화합니다. 사용자는 자신의 회사 자격 증명을 사용하여 개인 설정된 웹 기반 액세스 패널 또는 모바일 앱에서 응용 프로그램을 실행할 수 있습니다. Azure AD 응용 프로그램 프록시 모듈을 사용하면 SaaS 응용 프로그램뿐만 아니라 온-프레미스 웹 응용 프로그램도 게시하여 보안 원격 액세스 및 Single Sign-On 기능을 제공할 수 있습니다.

### <a name="protect-sensitive-data-and-applications"></a>중요한 데이터 및 응용 프로그램 보호
[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)을 활성화하여 추가적인 인증 수준을 제공하므로 온-프레미스 및 클라우드 응용 프로그램에 대한 무단 액세스를 방지합니다. 일치하지 않는 액세스 패턴을 식별하는 보안 모니터링, 경고 및 기계 학습 기반 보고서로 비즈니스를 보호하고 발생 가능한 위협을 완화합니다.

### <a name="enable-self-service-for-your-employees"></a>직원을 위한 셀프 서비스 지원
암호 재설정 및 그룹 생성 및 관리와 같은 중요한 작업을 직원들에게 위임합니다. Azure AD를 통해 [셀프 서비스 암호 변경](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password) 및 재설정 및 셀프 서비스 그룹 관리를 활성화합니다.

### <a name="integrate-with-azure-active-directory"></a>Azure Active Directory와 통합
[Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) 및 기타 모든 온-프레미스 디렉터리를 Azure AD로 확장하여 모든 클라우드 기반 응용 프로그램에서 Single Sign-On 기능을 활성화합니다. 모든 종류의 온-프레미스 디렉터리에서 사용자의 클라우드 디렉터리로 사용자 특성을 자동으로 동기화할 수 있습니다.

Azure Active Directory 통합 및 그 활성화 방법에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 문서를 확인하세요.

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>요청 추적, 사용 추세 분석 및 문제 진단
[Azure Storage 분석](https://docs.microsoft.com/azure/storage/storage-analytics)은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다.

저장소 분석 메트릭은 새 Storage 계정에서 기본적으로 활성화됩니다. Azure Portal에서 로깅을 활성화하고 메트릭과 로깅을 모두 구성할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [서비스 속성 설정] 작업을 사용하여 각 서비스에 대해 개별적으로 저장소 분석을 사용하도록 설정합니다.

저장소 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)을 참조하세요.

Azure Active Directory 통합 및 활성화 방법에 대한 자세한 내용은 [Storage Analytics 활성화 및 구성](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN)을 참조하세요.

## <a name="monitoring-services"></a>서비스 모니터링
클라우드 응용 프로그램은 이동하는 부분이 많아 복잡합니다. 모니터링은 응용 프로그램을 유지하고 정상 상태에서 실행할 수 있는 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다. 또한 응용 프로그램에 대해 깊이 이해하는 데 모니터링 데이터를 사용할 수 있습니다. 이러한 정보를 통해 응용 프로그램 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다.

### <a name="monitor-azure-resources"></a>Azure 리소스 모니터링
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)는 Azure 리소스를 모니터링하는 단일 원본이 되는 플랫폼 서비스입니다. Azure에서 Azure Monitor를 통해 리소스의 메트릭과 로그에 대해 시각화, 쿼리, 라우팅, 보관 및 조치를 수행할 수 있습니다. 모니터 포털 블레이드, [Monitor PowerShell Cmdlet](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [플랫폼 간 CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) 또는 [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx)를 사용하는 데이터를 통해 작업을 수행할 수 있습니다.

### <a name="enable-autoscale-with-azure-monitor"></a>Azure Monitor에서 자동 크기 조정 사용
[Azure Monitor 자동 크기 조정](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) 활성화는 VMSS(가상 머신 확장 집합), Cloud Services 및 App Service 계획과 App Service 환경에만 적용됩니다.

### <a name="manage-roles-permissions-and-security"></a>역할 권한 및 보안 관리
많은 팀에서는 [모니터링 데이터 및 설정에 대한 액세스를 엄격히 규제](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)할 필요가 있습니다. 예를 들어 모니터링에 대해 단독으로 작업하는 팀원(지원 엔지니어, devops 엔지니어)이 있거나, 관리되는 서비스 공급자를 사용할 경우 이들에게 리소스 생성, 수정 또는 삭제 기능은 제한하면서 모니터링 데이터에 대해서만 액세스를 부여하고자 할 수 있씁니다.

여기에서는 Azure의 사용자에게 기본 제공 모니터링 RBAC 역할을 신속하게 적용하거나 제한된 모니터링 권한이 필요한 사용자에 대해 자체 사용자 지정 역할을 구성하는 방법을 보여 줍니다. 그런 다음 Azure Monitor 관련 리소스에 대한 보안 고려 사항과, 포함된 데이터에 대한 액세스를 제한하는 방법에 대해 논의합니다.

## <a name="prevent-detect-and-respond-to-threats"></a>위협 방지, 검색 및 대응
Security Center 위협 감지는 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 보안 정보를 자동으로 수집하여 작동합니다. 위협을 식별하도록 종종 여러 소스의 정보를 상호 연결하는 이 정보를 분석합니다. 보안 경고는 위협을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에서 우선 순위가 지정됩니다.

-   Azure 구독의 [보안 정책을 구성](https://docs.microsoft.com/azure/security-center/security-center-policies)합니다.
-   [Security Center에서 권장 사항](https://docs.microsoft.com/azure/security-center/security-center-recommendations)을 사용하여 Azure 리소스를 보호합니다.
-   현재 [보안 경고](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)를 검토하고 관리합니다.

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 사용하면 Azure 리소스의 보안에 대한 향상된 가시성과 제어를 통해 위협을 예방, 검색 및 대응할 수 있습니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

보안 센터는 Azure에 기본 제공되는 사용하기 쉽고 효과적인 위협 예방, 감지 및 대응 기능을 제공합니다. 주요 기능:

-   클라우드 보안 상태 파악
-   클라우드 보안 제어
-   통합 클라우드 보안 솔루션을 쉽게 배포
-   위협 감지 및 신속한 대응

### <a name="understand-cloud-security-state"></a>클라우드 보안 상태 파악
Azure Security Center를 사용하여 모든 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다. 적합한 보안 제어를 구현하고 올바르게 구성했는지 한눈에 확인하고 주의가 필요한 리소스를 빠르게 확인합니다.

### <a name="take-control-of-cloud-security"></a>클라우드 보안 제어
회사의 클라우드 보안 요구에 따라 Azure 구독에 대한 [보안 정책](https://docs.microsoft.com/azure/security-center/security-center-policies)을 정의하고 각 구독의 데이터 민감도 또는 응용 프로그램 형식에 맞게 조정합니다. 정책 기반 권장 사항을 사용하여 필수 제어를 구현하는 프로세스를 통해 리소스 소유자가 클라우드 보안에서 필요한 조치를 취하도록 안내합니다.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>통합 클라우드 보안 솔루션을 쉽게 배포
업계 최고 방화벽 및 맬웨어 방지 프로그램 등 Microsoft 및 관련 파트너의 [보안 솔루션을 활성화합니다](https://docs.microsoft.com/azure/security-center/security-center-partner-integration).  간소화된 프로비전을 사용하면 네트워킹 변경이 구성된 경우에도 보안 솔루션을 배포할 수 있습니다. 파트너 솔루션의 보안 이벤트는 분석 및 경고를 위해 자동으로 수집됩니다.

### <a name="detect-threats-and-respond-fast"></a>위협 감지 및 신속한 대응
분석 지향적이며 통합적인 접근 방식으로 현재와 미래의 클라우드 위협을 앞서 대비하세요. Security Center는 Microsoft의 글로벌 [위협 정보](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) 및 전문 지식을 Azure 배포의 클라우드 보안 관련 이벤트에 대한 통찰력과 결합하여 조기에 실제적인 위협을 감지하도록 지원하므로 가양성을 줄일 수 있습니다. 클라우드 보안 경고는 관련 이벤트와 영향받은 리소스를 비롯한 공격 캠페인에 대한 통찰력을 제공하며 문제를 수정하고 신속하게 복구하는 방법을 제안합니다.

## <a name="end-to-end-scenario-based-network-monitoring"></a>종단 간 시나리오 기반 네트워크 모니터링
고객은 VNet, ExpressRoute, Application Gateway, 부하 분산 장치 등과 같은 다양한 개별 네트워크 리소스를 오케스트레이션하고 구성하여 Azure에서 종단 간 네트워크를 구축합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>패킷 캡처를 사용하여 원격 네트워크 모니터링 자동화
가상 머신(VM)에 로그인하지 않고 Network Watcher를 사용하여 네트워킹 문제를 모니터링 및 진단합니다. 경고를 설정하여 [패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture)를 트리거하고 패킷 수준에서 실시간 성능 정보에 액세스합니다. 문제를 발견하면 자세히 조사하여 더 정확히 진단할 수 있습니다.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>흐름 로그를 사용하여 네트워크 트래픽 이해
[네트워크 보안 그룹 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)를 사용하여 네트워크 트래픽 패턴을 더 자세히 이해합니다. 흐름 로그에서 제공하는 정보를 사용하여 준수, 감사 및 네트워크 보안 프로필 모니터링에 필요한 데이터를 수집할 수 있습니다.

### <a name="diagnose-vpn-connectivity-issues"></a>VPN 연결 문제 진단
Network Watcher를 사용하면 [자주 발생하는 VPN Gateway 및 연결 문제를 진단](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity)할 수 있습니다. 문제를 식별할 수 있을 뿐 아니라 생성된 세부 로그를 이용해 추가 조사를 수행할 수도 있습니다.

Network Watcher 구성 방법과 그 활성화 방법에 대한 자세한 내용은 [Network Watcher 구성](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) 문서에서 확인하세요.

## <a name="secure-deployment-using-proven-devops-tools"></a>검증된 DevOps 도구를 사용한 배포 보안
기업 및 팀의 생산성과 효율성을 높일 수 있는 Microsoft Cloud에서의 Azure DevOps 일부 사례 목록입니다.

-   **IaC(Infrastructure as Code):** IaC는 일상의 모듈식 인프라 빌드 및 관리와 관련한 IT 전문가들의 업무 부담을 해소해주는 기술과 사례의 모음입니다. 이를 통해 IT 전문가들은 소프트웨어 개발자가 응용 프로그램 코드를 구축 및 유지 관리하는 것과 유사한 방식으로 최신 서버 환경을 빌드하여 유지 관리할 수 있습니다. Azure의 경우 [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)를 사용하면 선언적 템플릿을 통해 응용 프로그램을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 응용 프로그램 수명 주기의 각 단계 중에 응용 프로그램을 반복해서 배포합니다.
-   **지속적인 통합 및 배포:** Azure 웹앱 또는 클라우드 서비스에 [자동으로 빌드 및 배포](https://www.visualstudio.com/docs/build/overview)하도록 VSTS(Visual Studio Team Services) 팀 프로젝트를 구성할 수 있습니다. VSTS는 코드 체크인 후 매번 Azure에 빌드한 뒤 이진 파일을 자동으로 배포합니다. 여기서 설명하는 패키지 빌드 프로세스는 Visual Studio의 Package 명령과 동일하며 게시 단계는 Visual Studio의 Publish 명령과 동일합니다.
-   **릴리스 관리:** Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview)는 다단계 배포 자동화 및 릴리스 프로세스 관리를 위한 훌륭한 솔루션입니다. 관리되는 지속적인 배포 파이프라인을 만들어 빠르고 쉽게 자주 릴리스합니다. Release Management를 통해 릴리스 프로세스 자동화 수준을 높이고 사전 정의된 승인 워크플로를 갖출 수 있습니다. 필요에 맞게 온-프레미스 및 클라우드에 배포하고 확장하며 사용자 지정합니다.
-   **앱 성능 모니터링:** 문제를 감지하고, 문제를 해결하고, 지속적으로 응용 프로그램을 개선합니다. 라이브 응용 프로그램의 모든 문제를 신속하게 진단합니다. 사용자가 어떤 작업을 하는지 확인합니다. JS 코드와 webconfig 항목을 추가하기만 하면 쉽게 구성이 가능하고 몇 분 안에 포털에서 그 결과와 모든 상세 정보를 확인하게 됩니다. [앱 정보](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/)를 통해 기업에서 문제와 조치를 더 신속하게 파악할 수 있습니다.
-   **부하 테스트 및 자동 크기 조정:** 앱의 성능 문제를 찾아내어 배포 품질을 향상시키고 비즈니스 요구 사항에 맞춰 항상 앱을 가동하고 사용할 수 있는지 확인할 수 있습니다. 앱이 다음 번 출시나 마케팅 캠페인을 위한 트래픽을 처리할 수 있게 합니다. VSTS로 거의 순식간에 클라우드 기반 [부하 테스트](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)를 실행하기 시작합니다.

## <a name="next-steps"></a>다음 단계
- [Azure 운영 보안](https://docs.microsoft.com/azure/security/azure-operational-security)에 대한 자세한 정보
- [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance) 살펴보기
- [Operations Management Suite 보안 및 감사 솔루션 시작](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
