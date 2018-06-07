---
title: Azure 고급 위협 검색 | Microsoft Docs
description: ID 보호 및 해당 기능에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: eb1bf9f6465a8a94fd2de75f581817887c333289
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="azure-advanced-threat-detection"></a>Azure 고급 위협 검색
## <a name="introduction"></a>소개

### <a name="overview"></a>개요

Microsoft는 Azure 플랫폼에서 사용할 수 있는 다양한 보안 관련 기능과 관련하여 Azure 고객을 지원하기 위해 일련의 백서, 보안 개요, 모범 사례 및 검사 목록을 개발했습니다. 주제는 폭과 깊이에 따라 다양하게 다루어지며 정기적으로 업데이트됩니다. 이 문서는 다음의 요약 섹션에서 설명하는 시리즈의 일부입니다.

### <a name="azure-platform"></a>Azure 플랫폼

Azure는 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 장치를 지원하는 공용 클라우드 서비스 플랫폼입니다.
다음과 같은 프로그래밍 언어를 지원합니다.
-   Docker 통합으로 Linux 컨테이너를 실행합니다.
-   JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드합니다.
-   iOS, Android 및 Windows 장치용 백 엔드를 빌드합니다.

Azure 공용 클라우드 서비스는 수백만의 개발자 및 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다.

조직과 함께 공용 클라우드로 마이그레이션하는 경우 해당 조직에서는 데이터를 보호하고 시스템에 대해 보안과 관리를 제공해야 합니다.

Azure의 인프라는 수백만 명의 고객을 동시에 호스팅하기 위한 시설에서 응용 프로그램에 이르는 인프라를 설계하며 비즈니스의 보안 요구 사항을 충족하는 신뢰할 수 있는 기반을 제공합니다. Azure는 앱 배포의 요구 사항에 맞게 보안을 구성하고 사용자 지정할 수 있는 다양한 옵션을 제공합니다. 이 문서는 이러한 요구 사항을 충족하는 데 도움이 됩니다.

### <a name="abstract"></a>요약

Microsoft Azure는 Azure Active Directory, Azure OMS(Operations Management Suite) 및 Azure Security Center와 같은 서비스를 통해 고급 위협 검색 기능을 기본적으로 제공하고 있습니다. 이 보안 서비스 및 기능 모음을 사용하면 Azure 배포에서 발생하는 상황을 간단하고 빠르게 파악할 수 있습니다.

이 백서에서는 위협 취약성 진단에 대한 "Microsoft Azure 방법"을 안내하고, 서버 및 기타 Azure 리소스를 대상으로 한 악의적 활동과 관련된 위험을 분석합니다. 이렇게 하면 Azure 플랫폼 및 고객 관련 보안 서비스와 기술로 최적화된 솔루션으로 식별 및 취약성 관리 방법을 확인할 수 있습니다.

이 백서에서는 Azure 플랫폼 및 고객 관련 제어 기술에 중점을 두는 반면, SLA, 가격 책정 모델 및 DevOps 구현 방법에 대한 고려 사항은 다루지 않습니다.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호

![Azure Active Directory ID 보호](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)는 조직의 ID에 영향을 미치는 위험 이벤트와 잠재적 취약성에 대한 개요를 제공하는 [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) 버전의 기능입니다. Microsoft는 10년 넘게 클라우드 기반 ID를 보호했습니다. Microsoft는 Azure AD ID 보호를 사용하여 기업 고객이 이러한 동일한 보호 시스템을 사용할 수 있도록 노력하고 있습니다. ID 보호는 [Azure AD의 비정상적인 활동 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)를 통해 제공되는 기존 Azure AD의 변칙 검색 기능을 사용하고, 실시간으로 비정상을 검색할 수 있는 새로운 위험 이벤트 유형을 도입하고 있습니다.

ID 보호는 적응형 기계 학습 알고리즘 및 추론을 사용하여 ID가 손상되었음을 나타낼 수 있는 비정상 및 위험 이벤트를 검색합니다. ID 보호는 이 데이터를 사용하여 이러한 위험 이벤트를 조사하고 적절한 수정 또는 완화 작업을 수행할 수 있도록 하는 보고서와 경고를 생성합니다.

하지만 Azure Active Directory ID 보호는 모니터링 및 보고 도구 이상입니다. ID 보호는 위험 이벤트에 따라 각 사용자에 대한 위험 수준을 계산하며 이는 위험 기반 정책을 구성하여 조직의 ID를 자동으로 보호할 수 있도록 합니다.

이러한 위험 기반 정책은 Azure Active Directory 및 [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)에서 제공하는 다른 [조건부 액세스 제어](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) 외에도, 암호 재설정 및 다단계 인증 적용을 포함하는 적응형 수정 작업을 자동으로 차단하거나 제공할 수 있습니다.

### <a name="identity-protections-capabilities"></a>ID 보호 기능

Azure Active Directory ID 보호는 모니터링 및 보고 도구 이상입니다. 조직의 ID를 보호하려면 지정된 위험 수준에 도달했을 때 검색된 문제에 자동으로 대응하는 위험 기반 정책을 구성할 수 있습니다. Azure Active Directory 및 EMS에서 제공되는 다른 조건부 액세스 제어 외에도 이러한 정책은 암호 재설정 및 Multi-Factor Authentication 적용을 포함하는 적응형 수정 작업을 자동으로 차단하거나 시작할 수 있습니다.

Azure ID 보호를 사용하여 계정과 ID를 보호할 수 있는 몇 가지 방법의 예는 다음과 같습니다.

[위험 이벤트 및 위험한 계정 검색:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   기계 학습 및 추론 규칙을 사용하여 6가지 위험 이벤트 유형 검색
-   사용자 위험 수준 계산
-   취약점을 강조 표시하여 전반적인 보안 포스처를 개선하도록 사용자 지정 권장 사항 제공

[위험 이벤트 조사:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   위험 이벤트에 대한 알림 보내기
-   관련된 컨텍스트 정보를 사용하여 위험 이벤트 조사
-   기본 워크플로를 제공하여 조사 추적
-   암호 재설정 등 수정 작업에 쉬운 액세스 제공

[위험 기준 조건부 액세스 정책:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   로그인을 차단하거나 Multi-Factor Authentication 과제를 요구하여 위험한 로그인을 완화하는 정책입니다.
-   위험한 사용자 계정 차단 및 보호 정책
-   Multi-Factor Authentication에 사용자 등록 요구 정책

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD PIM(Privileged Identity Management)

[Azure AD(Active Directory) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)를 사용하는 경우

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

조직 내 액세스를 관리, 제어 및 모니터링할 수 있습니다. Azure AD의 리소스 및 Office 365 또는 Microsoft Intune과 같은 다른 Microsoft 온라인 서비스에 대한 액세스를 포함합니다.

Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

-   Azure AD 관리자에 대한 경고와 보고서 및 Microsoft Online Services(예: Office 365 및 Intune)에 대한 "Just-In-Time" 관리 액세스 권한을 받을 수 있습니다.

-   관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서 가져오기

-   권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft OMS(Operations Management Suite)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)는 온-프레미스 및 클라우드 인프라를 관리하고 보호하는 데 유용한 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. OMS는 클라우드 기반 서비스로 구현되므로 인프라 서비스에 대한 최소한의 투자로 빠르게 실행할 수 있습니다. 새로운 보안 기능을 자동으로 제공하므로 지속적인 유지 관리 및 업그레이드 비용이 절감됩니다.

OMS는 자체적으로 중요 서비스를 제공하는 것 외에도 [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)와 같은 System Center 구성 요소와 통합하여 기존 관리 투자를 클라우드로 확장할 수 있습니다. System Center 및 OMS는 함께 작동하여 완전한 하이브리드 관리 환경을 제공할 수 있습니다.

### <a name="holistic-security-and-compliance-posture"></a>전체적인 보안 및 규정 준수 상태

[OMS 보안 및 감사 대시보드](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)는 주의가 필요한 주요 문제에 대한 기본 제공 검색 쿼리를 통해 조직의 IT 보안 상태에 대한 포괄적인 보기를 제공합니다. [보안 및 감사] 대시보드는 OMS의 보안과 관련된 모든 정보를 볼 수 있는 홈 화면입니다. 이 대시보드에서 컴퓨터의 보안 상태를 대략적으로 확인할 수 있으며 지난 24시간, 7일, 기타 사용자 지정 기간 동안 발생한 모든 이벤트를 확인할 수도 있습니다.

OMS 대시보드를 사용하면 소프트웨어 업데이트 평가, 맬웨어 방지 평가 및 구성 기준을 포함하여 IT 운영 컨텍스트 내에서 모든 환경의 전반적인 보안 상태를 빠르고 쉽게 이해할 수 있습니다. 또한 보안 로그 데이터는 보안 및 규정 준수 감사 프로세스를 간소화하기 위해 쉽게 액세스할 수 있습니다.

OMS 보안 및 감사 대시보드는 네 가지 주요 범주로 구성되어 있습니다.

![OMS 보안 및 감사 대시보드](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **보안 도메인**: 이 영역에서는 시간이 지남에 따라 보안 기록을 탐색하고, 맬웨어 평가, 업데이트 평가, 네트워크 보안, ID 및 액세스 정보, 보안 이벤트가 있는 컴퓨터에 액세스하며, Azure Security Center 대시보드에 빠르게 액세스할 수 있습니다.

-   **주목할 만한 문제**: 이 옵션에서는 활성 문제의 수와 이러한 문제의 심각도를 빠르게 확인할 수 있습니다.

-   **검색(미리 보기)**: 보안 경고가 자신의 리소스에 대해 발생하는 것처럼 시각화하여 공격 패턴을 식별할 수 있게 합니다.

-   **위협 인텔리전스**: 악성 아웃바운드 IP 트래픽이 있는 서버의 총 수, 악의적 위협 유형 및 이러한 IP가 발생한 위치를 보여주는 맵을 시각화하여 공격 패턴을 식별할 수 있게 합니다.

-   **일반적인 보안 쿼리**: 환경을 모니터링하는 데 사용할 수 있는 가장 일반적인 보안 쿼리의 목록을 제공합니다. 이러한 쿼리 중 하나를 클릭하면 해당 쿼리의 결과가 포함된 [Search] 블레이드가 열립니다.

### <a name="insight-and-analytics"></a>Insight and Analytics
[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)의 중심에는 Azure 클라우드에서 호스팅되는 OMS 리포지토리가 있습니다.

![Insight and Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

데이터 원본을 구성하고 구독에 솔루션을 추가하면 연결된 원본에서 리포지토리로 데이터가 수집됩니다.

![subscription](./media/azure-threat-detection/azure-threat-detection-fig5.png)

데이터 원본 및 솔루션은 각각 고유한 속성 집합을 가진 서로 다른 레코드 유형을 만들지만 리포지토리에 대한 쿼리에서 여전히 함께 분석할 수 있습니다. 따라서 동일한 도구 및 메서드를 사용하여 다양한 원본에서 수집된 여러 종류의 데이터로 작업할 수 있습니다.


Log Analytics와의 상호 작용은 대부분 모든 브라우저에서 실행되고 수집된 데이터를 분석 및 조작하는 구성 설정 및 여러 도구에 대한 액세스를 제공하는 OMS 포털을 통해 이루어집니다. 포털에서 수집된 데이터를 분석할 쿼리를 작성하는 [로그 검색](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches), 가장 중요한 검색에 대한 그래픽 보기로 사용자 지정할 수 있는 [대시보드](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards) 및 추가 기능과 분석 도구를 제공하는 [솔루션](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)을 사용할 수 있습니다.

![분석 도구](./media/azure-threat-detection/azure-threat-detection-fig6.png)

솔루션은 Log Analytics에 기능을 추가합니다. 주로 클라우드에서 실행되며 OMS 리포지토리에 수집된 데이터의 분석을 제공합니다. 또한 로그 검색으로 분석할 수 있거나 OMS 대시보드의 솔루션에서 제공하는 추가 사용자 인터페이스에서 분석할 수 있는 수집할 새 레코드 유형을 정의할 수 있습니다.
[보안 및 감사]는 이러한 솔루션 유형의 한 예입니다.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automation 및 제어: 보안 구성 표류에 대한 경고

Azure Automation은 PowerShell을 기반으로 하며 Azure 클라우드에서 실행되는 Runbook을 사용하여 관리 프로세스를 자동화합니다. 또한 Runbook을 로컬 데이터 센터의 서버에서 실행하여 로컬 리소스를 관리할 수 있습니다. Azure Automation은 PowerShell DSC(필요한 상태 구성)를 사용하여 구성 관리를 제공합니다.

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Azure에서 호스팅되는 DSC 리소스를 만들고 관리하며, 클라우드 및 온-프레미스 시스템에 적용하여 구성을 정의하고 자동으로 적용하거나, 표류에 대한 보고서를 가져와서 보안 구성이 정책 내에 유지되도록 할 수 있습니다.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center는 Azure 리소스를 보호하는 데 도움이 됩니다. Azure 구독에서 통합된 보안 모니터링 및 정책 관리를 제공합니다. 서비스 내에서 Azure 구독에 대한 정책뿐만 아니라 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)에 대한 정책도 정의할 수 있으므로 더욱 세분화할 수 있습니다.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Microsoft 보안 연구원은 지속적으로 위협을 지켜보고 있습니다. 클라우드 및 온-프레미스에 존재하는 Microsoft의 글로벌 서비스에서 얻은 원격 분석의 포괄적인 집합에 액세스할 수 있습니다. 이 광범위하고 다양한 데이터 집합의 컬렉션을 통해 Microsoft는 온라인 서비스 뿐만 아니라 해당 온-프레미스 소비자 및 엔터프라이즈 제품에서도 새로운 공격 패턴 및 추세를 검색할 수 있습니다.

따라서 공격자가 새롭고 점차 정교해지는 악용을 방출함에 따라 Security Center에서 검색 알고리즘을 빠르게 업데이트할 수 있습니다. 이 방법을 사용하면 빠르게 변하는 위협 환경과 보조를 맞추며 대응할 수 있습니다.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

보안 센터 위협 감지는 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 보안 정보를 자동으로 수집하여 작동합니다.  이 정보를 분석하며 여러 원본의 정보를 상호 연결하여 위협을 식별합니다.
보안 경고는 위협을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에서 우선 순위가 지정됩니다.

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 수동 접근 방법을 사용하여 식별할 수 없는 위협을 검색하고 공격의 진화를 예측하는 빅 데이터 및 [기계 학습](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)의 혁신적인 기술을 사용하여 클라우드 패브릭 전체에 대한 이벤트를 평가합니다. 이러한 보안 분석에는 다음이 포함됩니다.

### <a name="threat-intelligence"></a>위협 인텔리전스

Microsoft는 방대한 글로벌 위협 인텔리전스가 있습니다.
원격 분석은 Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft DCU(Digital Crimes Unit) 및 MSRC(Microsoft 보안 대응 센터)와 같은 여러 출처에서 유입됩니다.

![위협 인텔리전스](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

또한 연구원은 제 3자의 위협 인텔리전스 피드에 대해 주요 클라우드 서비스 공급자와 구독자 사이에서 공유되는 위협 인텔리전스 정보를 받습니다. Azure Security Center는 이 정보를 사용하여 알려진 부정 행위자의 위협을 경고할 수 있습니다. 일부 사례:

-   **강력한 Machine Learning 기능 활용** - Azure Security Center는 Azure 배포를 대상으로 하는 위협을 검색하는 데 사용할 수 있는 방대한 양의 클라우드 네트워크 활동 데이터에 액세스할 수 있습니다. 예: 

-   **무차별 암호 대입 검색** - 기계 학습은 SSH, RDP 및 SQL 포트에 대한 무차별 암호 대입 공격을 검색할 수 있는 원격 액세스 시도의 기록 패턴을 만드는 데 사용됩니다.

-   **아웃바운드 DDoS 및 봇네트 검색** - 클라우드 리소스를 대상으로 하는 공격의 공통 목표는 이러한 리소스의 계산 성능을 사용하여 다른 공격을 실행하는 것입니다.

-   **새 동작 분석 서버 및 VM** - 서버 또는 가상 컴퓨터가 손상되면 공격자는 검색을 피하고, 지속성을 유지하며, 보안 제어를 방해하면서 다양한 기술을 사용하여 해당 시스템에서 악성 코드를 실행합니다.

-   **Azure SQL Database 위협 검색** - Azure SQL Database에 대한 위협 검색은 비정상적이며 잠재적으로 위험한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 데이터베이스 활동을 식별합니다.

### <a name="behavioral-analytics"></a>동작 분석

동작 분석은 알려진 패턴의 컬렉션에 대해 데이터를 분석하고 비교하는 기술입니다. 그러나 이러한 패턴은 단순한 서명이 아닙니다. 대량 데이터 집합에 적용되는 복잡한 기계 학습 알고리즘을 통해 결정됩니다.

![동작 분석](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


또한 전문 분석가가 악의적인 행동을 신중하게 분석하여 결정합니다. Azure Security Center는 동작 분석을 사용하여 가상 머신 로그, 가상 네트워크 장치 로그, 패브릭 로그, 크래시 덤프 및 기타 원본에 대한 분석에 따라 손상된 리소스를 식별할 수 있습니다.

또한 광범위한 캠페인의 증거 지원을 확인하는 다른 신호와의 상관 관계가 있습니다. 이 상관 관계를 통해 설정된 손상 표시기와 일치하는 이벤트를 식별할 수 있습니다.

일부 사례:
-   **의심스러운 프로세스 실행**: 공격자는 다양한 기술을 사용하여 검색 없이 악성 소프트웨어를 실행합니다. 예를 들어 공격자는 맬웨어에 합법적인 시스템 파일과 같은 이름을 제공할 수 있지만, 이러한 파일을 대체 위치에 배치하거나 무해한 파일과 매우 유사한 이름을 사용하거나 파일의 실제 확장명을 가립니다. 보안 센터는 프로세스 동작을 모델링하고 이와 같은 이상값을 검색하는 프로세스 실행을 모니터링합니다.

-   **숨겨진 맬웨어 및 악용 시도**: 정교한 맬웨어는 디스크에 절대로 쓰지 않거나 디스크에 저장된 소프트웨어 구성 요소를 암호화하여 기존 맬웨어 방지 제품을 피할 수 있습니다. 그러나 이러한 맬웨어는 작동하기 위해 메모리에 추적을 남겨야 하므로 메모리 분석을 사용하여 검색할 수 있습니다. 소프트웨어가 충돌할 때 크래시 덤프는 충돌 시 메모리의 일부를 캡처합니다. Azure Security Center에서는 크래시 덤프의 메모리를 분석하여 소프트웨어의 취약성을 악용하며, 기밀 데이터에 액세스하고, 컴퓨터의 성능에 영향을 주지 않고 손상된 컴퓨터에서 은밀하게 유지하는 데 사용되는 기술을 검색할 수 있습니다.

-   **수평 이동 및 내부 정찰**: 손상된 네트워크에서 유지하고 중요한 데이터를 찾거나 수집하기 위해 공격자는 종종 손상된 컴퓨터에서 동일한 네트워크 내의 다른 컴퓨터로 수평 이동하려고 합니다. Security Center는 프로세스 및 로그인 활동을 모니터링하여 원격 명령 실행, 네트워크 검색 및 계정 열거와 같이 네트워크 내에서 공격자의 발판을 확장하려는 시도를 검색합니다.

-   **악의적인 PowerShell 스크립트**: 공격자는 PowerShell을 사용하여 대상 가상 머신에서 다양한 목적으로 악성 코드를 실행할 수 있습니다. 보안 센터는 의심스러운 활동의 증거에 대해 PowerShell 작업을 검사합니다.

-   **발신 공격**: 공격자는 종종 클라우드 리소스를 사용하여 추가 공격을 탑재할 목적으로 해당 클라우드 리소스를 대상으로 지정합니다. 예를 들어 손상된 가상 머신을 사용하여 다른 가상 머신에 무차별 암호 대입 공격을 시작하거나, 스팸을 보내거나, 인터넷에서 열려 있는 포트와 다른 장치를 검색할 수 있습니다. 보안 센터는 네트워크 트래픽에 기계 학습을 적용하여 아웃바운드 네트워크 통신이 표준을 초과하는 경우를 감지할 수 있습니다. 또한 스팸의 경우 Security Center는 Office 365의 인텔리전스와 비정상적인 전자 메일 트래픽을 상호 연결하여 전자메일이 부정한 것인지 또는 합법적인 전자 메일 캠페인의 결과인지 여부를 결정합니다.

### <a name="anomaly-detection"></a>이상 감지

Azure Security Center는 이상 감지를 사용하여 위협을 식별합니다. 동작 분석(큰 데이터 집합에서 파생된 알려진 패턴에 따라 결정)과 달리 이상 감지는 더욱 "개인화"되고 배포에만 적용되는 기준에 중점을 둡니다. 배포에 대한 정상적인 작동을 확인하기 위해 기계 학습이 적용되고 보안 이벤트를 표시할 수 있는 이상값 조건을 정의하는 규칙이 생성됩니다. 예를 들면 다음과 같습니다.

-   **인바운드 RDP/SSH 무차별 암호 대입 공격**: 배포에는 매일 많은 로그인이 있는 바쁜 가상 머신과 로그인이 거의 없는 다른 가상 머신이 있을 수 있습니다. Azure Security Center는 이러한 가상 머신에 대한 기준 로그인 활동을 결정하고, 기계 학습을 사용하여 정상적인 로그인 활동을 정의할 수 있습니다. 로그인 관련 특성에 정의된 기준과의 불일치가 있으면 경고가 생성될 수 있습니다. 다시, 기계 학습은 무엇이 중요한지를 결정합니다.

### <a name="continuous-threat-intelligence-monitoring"></a>연속 위협 인텔리전스 모니터링

Azure Security Center는 전 세계의 보안 연구 및 데이터 과학 팀과 협력하여 위협 환경의 변화를 지속적으로 모니터링합니다. 다음 이니셔티브가 포함됩니다.

-   **위협 인텔리전스 모니터링**: 위협 인텔리전스에는 기존 또는 새로운 위협에 대한 메커니즘, 표시기, 영향 및 조치 가능한 조언이 포함됩니다. 이 정보는 보안 커뮤니티에서 공유되고 Microsoft는 내부 및 외부 소스에서 위협 인텔리전스 피드를 지속적으로 모니터링합니다.

-   **신호 공유**: Microsoft의 클라우드 및 온-프레미스 서비스, 서버 및 클라이언트 끝점 장치의 광범위한 포트폴리오에 대한 보안 팀의 정보를 공유하고 분석합니다.

-   **Microsoft 보안 전문가**: 법정 분석 및 웹 공격 검색과 같은 전문 보안 분야에서 Microsoft 팀과 지속적인 관계를 유지하며 활동합니다.

-   **검색 튜닝**: 알고리즘은 실제 고객 데이터 집합에 대해 실행되며, 보안 연구원은 고객과 협력하여 결과의 유효성을 검사합니다. 기계 학습 알고리즘을 구체화하기 위해 참 및 거짓 긍정이 사용됩니다.

이러한 결합된 노력은 즉시 활용할 수 있는 새롭고 향상된 감지에 누적됩니다. 수행해야 할 작업이 없습니다.

## <a name="advanced-threat-detection-features---other-azure-services"></a>고급 위협 검색 기능 - 다른 Azure 서비스

### <a name="virtual-machine-microsoft-antimalware"></a>Virtual Machine: Microsoft 맬웨어 방지 프로그램

Azure용 [Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware)은 응용 프로그램 및 테넌트 환경을 위한 단일 에이전트 솔루션이며, 사용자의 개입 없이 백그라운드에서 실행되도록 설계되었습니다. 맬웨어 방지 프로그램 모니터링을 포함하여 기본 보안 또는 고급 사용자 지정 구성을 사용하여 응용 프로그램 워크로드의 필요에 따라 보호를 배포할 수 있습니다. Azure 맬웨어 방지 프로그램은 Azure Virtual Machines의 보안 옵션이며, 모든 Azure PaaS 가상 머신에 자동으로 설치됩니다.

**응용 프로그램용 Microsoft 맬웨어 방지 프로그램을 배포 및 활성화하는 Azure 기능**

#### <a name="microsoft-antimalware-core-features"></a>Microsoft 맬웨어 방지 프로그램 핵심 기능

-   **실시간 보호** - Cloud Services 및 Virtual Machines의 활동을 모니터링하여 맬웨어 실행을 검색하고 차단합니다.

-   **예약된 검색** - 활발하게 실행 중인 프로그램을 포함하여 대상으로 지정된 검색을 주기적으로 수행하여 맬웨어를 검색합니다.

-   **맬웨어 치료** - 검색된 맬웨어에 대해 악성 파일을 삭제 또는 격리하고 악성 레지스트리 항목을 정리하는 것과 같은 작업을 자동으로 수행합니다.

-   **서명 업데이트** - 최신 보호 서명(바이러스 정의)을 자동으로 설치하여 보호가 미리 결정된 빈도에서 최신 상태인지 확인합니다.

-   **맬웨어 방지 프로그램 엔진 업데이트** – Microsoft 맬웨어 방지 프로그램 엔진을 자동으로 업데이트합니다.

-   **맬웨어 방지 프로그램 플랫폼 업데이트** – Microsoft 맬웨어 방지 프로그램 플랫폼을 자동으로 업데이트합니다.

-   **활성 보호** - 검색된 위협 및 의심스러운 리소스에 대한 원격 분석 메타데이터를 Microsoft Azure에 보고하여 진화하는 위협 환경에 신속하게 대응하고 MAPS(Microsoft 활성 보호 시스템)를 통해 실시간 동기 서명 전달을 활성화합니다.

-   **샘플 보고** - Microsoft 맬웨어 방지 프로그램 서비스에 샘플을 제공하고 보고하여 서비스를 개선하고 문제를 해결하는 데 사용합니다.

-   **제외** – 응용 프로그램 및 서비스 관리자가 성능 및/또는 기타 이유로 보호 및 검색에서 특정 파일, 프로세스 및 드라이브를 제외하도록 구성할 수 있습니다.

-   **맬웨어 방지 이벤트 수집** - 운영 체제 이벤트 로그에서 수행된 맬웨어 방지 서비스 상태, 의심스러운 활동 및 치료 작업을 기록하고 고객의 Azure Storage 계정으로 수집합니다.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database 위협 검색

[Azure SQL Database 위협 검색](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)은 Azure SQL Database 서비스에 기본 제공되는 새로운 보안 인텔리전스 기능입니다. Azure SQL Database 위협 검색은 24시간 내내 항시 작동하여 비정상적인 데이터베이스 활동을 학습하고, 프로파일링하며, 검색하면서 데이터베이스에 대한 잠재적 위협을 식별합니다.

보안 책임자 또는 지정된 다른 관리자는 의심스러운 데이터베이스 활동이 발생하는 즉시 알림을 받을 수 있습니다. 각 알림에서는 의심스러운 활동에 대한 세부 정보를 제공하고 해당 위협을 자세히 조사하고 완화하는 방법을 권장합니다.

현재 Azure SQL Database 위협 검색은 잠재적인 취약성과 SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 패턴을 검색합니다.

위협 검색 전자 메일 알림을 받으면 사용자는 감사 뷰어를 여는 메일의 딥 링크 및/또는 의심스러운 이벤트가 발생했을 때 관련된 감사 레코드를 보여 주는 미리 구성된 감사 Excel 템플릿을 사용하여 다음과 같이 관련 감사 레코드를 탐색하고 살펴볼 수 있습니다.
-   비정상적인 데이터베이스 활동이 있는 데이터베이스/서버에 대한 저장소 감사

-   감사 로그를 작성하는 이벤트 발생 시 사용된 관련 감사 저장소 테이블

-   이벤트 발생 이후 다음 시간의 감사 레코드

-   이벤트 발생 시 유사한 이벤트 ID가 있는 감사 레코드(일부 검색기의 경우 선택 사항)

SQL Database 위협 검색기는 다음 검색 방법 중 하나를 사용합니다.

-   **결정적 검색** - 알려진 공격과 일치하는 SQL 클라이언트 쿼리에서 의심스러운 패턴(규칙 기반)을 검색합니다. 이 방법론은 높은 검색율과 낮은 가양성을 가지고 있지만 "원자성 검색" 범주에 속하기 때문에 범위가 제한됩니다.

-   **동작 검색** - 지난 30일 동안 표시되지 않은 데이터베이스에 대한 비정상 동작인 비정상 활동을 검색합니다.  SQL 클라이언트 비정상 활동에 대한 예로 실패한 로그인/쿼리, 대량의 데이터 추출, 비정상적인 정식 쿼리 및 데이터베이스 액세스에 사용된 알 수 없는 IP 주소의 급격한 증가가 있습니다

### <a name="application-gateway-web-application-firewall"></a>Application Gateway 웹 응용 프로그램 방화벽

[웹 응용 프로그램 방화벽](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)은 표준 [응용 프로그램 배달 제어](https://kemptechnologies.com/in/application-delivery-controllers) 기능에 응용 프로그램 게이트웨이를 사용하는 웹 응용 프로그램에 보호를 제공하는 [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview)의 기능입니다. 웹 응용 프로그램 방화벽은 대부분의 [OWASP 상위 10개 일반 웹 취약성](https://www.owasp.org/index.php/Top_10_2010-Main)(영문)으로부터 보호함으로써 이를 수행합니다.

![Application Gateway 웹 응용 프로그램 방화벽](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   SQL 삽입 공격 보호

-   교차 사이트 스크립팅 공격 보호

-   명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호

-   HTTP 프로토콜 위반 보호

-   누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호

-   보트, 크롤러 및 스캐너 방지

-   일반적인 응용 프로그램 구성 오류(즉 Apache, IIS 등) 검색

Application Gateway에서 WAF를 구성하면 다음과 같은 이점이 있습니다.

-   백 엔드 코드를 수정하지 않고 웹 취약점 공격으로부터 웹 응용 프로그램을 보호합니다.

-   동시에 여러 웹 응용 프로그램을 Application Gateway 뒤에 두고 보호합니다. Application Gateway는 게이트웨이 하나 뒤에 최대 20개의 웹 사이트를 호스트하여 웹 공격으로부터 보호할 수 있습니다.

-   Application Gateway WAF 로그에서 생성하는 실시간 보고서를 사용하여 공격을 받는 웹 응용 프로그램을 모니터링할 수 있습니다.

-   특정 규정 준수 컨트롤은 모든 인터넷 연결 끝점을 WAF 솔루션으로 보호해야 합니다. WAF가 활성화된 Application Gateway를 사용하면 이러한 규정 준수 요구 사항을 충족할 수 있습니다.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>변칙 검색 – Azure Machine Learning으로 작성된 API

변칙 검색은 Azure Machine Learning으로 작성된 API이며, 시계열 데이터에서 다양한 유형의 비정상 패턴을 검색하는 데 유용합니다. API는 시계열의 각 데이터 요소에 변칙 점수를 지정하며, 이 점수는 경고 생성, 대시보드를 통한 모니터링 또는 티케팅 시스템 연결에 사용할 수 있습니다.

[변칙 검색 API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api)는 시계열 데이터에서 다음과 같은 유형의 변칙을 검색할 수 있습니다.

-   **급증 및 급락:** 예를 들어 서비스에 대한 로그인 실패 횟수 또는 전자 상거래 사이트의 체크 아웃 수를 모니터링할 때 비정상적인 급증 또는 급락은 보안 공격 또는 서비스 중단을 나타낼 수 있습니다.

-   **긍정 추세 및 부정 추세:** 예를 들어 컴퓨팅에서 메모리 사용량을 모니터링할 때 사용 가능한 메모리 크기가 줄어들면 잠재적인 메모리 누수가 있음을 나타냅니다. 서비스 큐 길이를 모니터링할 때 지속적으로 증가하는 추세는 기본 소프트웨어 문제를 나타낼 수도 있습니다.

-   **수준 변경 및 동적 범위 값 변경:** 예를 들어 서비스 업그레이드 후 서비스 대기 시간의 수준 변경 또는 업그레이드 후 낮은 수준의 예외를 모니터링하는 것이 흥미로울 수 있습니다.

기계 학습 기반 API는 다음을 가능하게 합니다.

-   **유연하고 강력한 검색:** 변칙 검색 모델을 사용하면 사용자가 민감도 설정을 구성하고 계절 및 비계절 데이터 집합 간의 비정상 상태를 검색할 수 있습니다. 사용자는 필요에 따라 변칙 검색 모델을 조정하여 검색 API의 민감도를 높이거나 낮출 수 있습니다. 이는 계절적 패턴이 있거나 없는 데이터에 대해 더 가시적이거나 덜 가시적인 변칙을 검색하는 것을 의미합니다.

-   **확장성 있는 시기 적절한 검색:** 전문가의 도메인 지식을 통해 설정된 현재 임계값으로 모니터링하는 기존의 방법은 비용이 많이 들고 수백만 개의 동적으로 변하는 데이터 집합으로 확장할 수 없습니다. 이 API의 변칙 검색 모델은 학습되었으며 모델은 기록 데이터와 실시간 데이터 모두에서 자동으로 튜닝됩니다.

-   **사전 대응 및 실행 가능 검색:** 느린 추세 및 수준 변경 검색은 초기 변칙 검색에 적용할 수 있습니다. 검색된 초기 변칙 신호를 사용하여 사람이 문제 영역을 조사하고 조치를 취하도록 유도할 수 있습니다.  또한 이 변칙 검색 API 서비스를 기반으로 하여 근본 원인 분석 모델 및 경고 도구를 개발할 수도 있습니다.

변칙 검색 API는 서비스 상태/KPI 모니터링, IoT, 성능 모니터링 및 네트워크 트래픽 모니터링과 같은 다양한 시나리오에 효과적이고 효율적인 솔루션입니다. 이 API가 유용할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.
- IT 부서에는 이벤트, 오류 코드, 사용 현황 로그 및 성능(CPU, 메모리 등)을 적시에 추적할 수 있는 도구가 필요합니다.

-   온라인 상거래 사이트는 고객 활동, 페이지 보기, 클릭 등을 추적하려고 합니다.

-   유틸리티 회사는 물, 가스, 전기 및 기타 자원의 사용을 추적하려고 합니다.

-   설비/건물 관리 서비스는 온도, 습기, 교통량 등을 모니터링하려고 합니다.

-   IoT/제조업체는 시계열 센서 데이터를 사용하여 작업 흐름, 품질 등을 모니터링하려고 합니다.

-   콜 센터와 같은 서비스 공급자는 서비스 요청 추세, 인시던트 볼륨, 큐 길이 등을 모니터링해야 합니다.

-   비즈니스 분석 그룹은 비즈니스 KPI(예: 판매량, 고객 정서, 가격)의 비정상적인 변동을 실시간으로 모니터링하려고 합니다.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)는 Microsoft Cloud Security 스택의 중요한 구성 요소입니다. 클라우드 응용 프로그램에 대한 약속을 완전히 활용하지만 활동에 대한 향상된 가시성을 통해 제어를 유지하면서 조직을 지원할 수 있는 포괄적인 솔루션입니다. 또한 클라우드 응용 프로그램에서 중요한 데이터의 보호를 강화하는 데 도움이 됩니다.

섀도 IT를 발견하고, 위험을 평가하며, 정책을 적용하고, 활동을 조사하며, 위협을 중지할 수 있는 도구를 사용하면 조직에서 중요한 데이터에 대한 제어를 유지하면서 보다 안전하게 클라우드로 이동할 수 있습니다.

<table style="width:100%">
 <tr>
   <td>검색</td>
   <td>Cloud App Security로 섀도 IT를 발견합니다. 클라우드 환경에서 앱, 활동, 사용자, 데이터 및 파일을 검색하여 가시성을 확보합니다. 클라우드에 연결된 타사 앱을 발견합니다.</td>
 </tr>
 <tr>
   <td>조사</td>
   <td>클라우드 법정 분석 도구로 클라우드 앱을 조사하여 네트워크에서 위험한 앱, 특정 사용자 및 파일을 심층 분석합니다. 클라우드에서 수집한 데이터의 패턴을 찾습니다. 클라우드를 모니터링하는 보고서를 생성합니다.</td>

 </tr>
 <tr>
   <td>제어</td>
   <td>네트워크 클라우드 트래픽을 최대한 제어할 수 있도록 정책 및 경고를 설정하여 위험을 완화합니다. Cloud App Security를 사용하여 사용자를 안전하고 승인된 클라우드 앱 대체 기능으로 마이그레이션합니다.</td>

 </tr>
 <tr>
   <td>보호</td>
   <td>Cloud App Security를 사용하여 응용 프로그램을 승인하거나 금지하고, 데이터 손실 방지를 강화하며, 권한 및 공유를 제어하고, 사용자 지정 보고서 및 알림을 생성합니다.</td>

 </tr>
 <tr>
   <td>제어</td>
   <td>네트워크 클라우드 트래픽을 최대한 제어할 수 있도록 정책 및 경고를 설정하여 위험을 완화합니다. Cloud App Security를 사용하여 사용자를 안전하고 승인된 클라우드 앱 대체 기능으로 마이그레이션합니다.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security는 다음과 같은 방법으로 클라우드와 가시성을 통합합니다.

-   Cloud Discovery를 사용하여 조직에서 사용 중인 클라우드 환경과 클라우드 앱을 매핑하고 식별합니다.


-   클라우드의 앱을 승인하거나 금지합니다.



-   연결하는 앱의 가시성과 관리를 위해 공급자 API를 활용하는 배포하기 쉬운 앱 커넥터를 사용합니다.

-   정책을 설정하고 지속적으로 자세히 튜닝하여 연속적으로 제어하도록 지원합니다.

이러한 원본에서 데이터를 수집할 때 Cloud App Security에서 데이터에 대한 정교한 분석을 실행합니다. 비정상적인 활동을 즉시 알려주고 클라우드 환경에 대한 심층적인 가시성을 제공합니다. Cloud App Security에서 정책을 구성하고 이를 사용하여 클라우드 환경의 모든 데이터를 보호할 수 있습니다.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Azure Marketplace를 통한 타사 ATD 기능

### <a name="web-application-firewall"></a>웹 응용 프로그램 방화벽

웹 응용 프로그램 방화벽은 인바운드 웹 트래픽을 검사하고 SQL 삽입 공격, 사이트 간 스크립팅, 맬웨어 업로드, 응용 프로그램 DDoS 및 웹 응용 프로그램을 대상으로 하는 기타 공격을 차단합니다. 데이터 손실 방지 DLP (Data Loss Prevention)에 대한 백엔드 웹 서버로부터의 응답도 검사합니다. 관리자는 통합된 액세스 제어 엔진을 통해 AAA(인증, 권한 부여 & 계정)에 대한 세분화된 액세스 제어 정책을 만들 수 있으므로 조직에 강력한 인증 및 사용자 제어 기능을 제공합니다.

**주요 사항:**
-   SQL 삽입 공격, 사이트 간 스크립팅, 맬웨어 업로드, 응용 프로그램 DDoS 또는 응용 프로그램에 대한 기타 모든 공격을 검색하고 차단합니다.

-   인증 및 액세스 제어

-   아웃바운드 트래픽을 검색하여 중요한 데이터를 검색하고, 정보를 감추거나 유출로부터 차단할 수 있습니다.

-   캐싱, 압축 및 기타 트래픽 최적화와 같은 기능을 사용하여 웹 응용 프로그램 콘텐츠의 전달을 향상시킵니다.

다음은 Azure Market Place에서 사용할 수 있는 웹 응용 프로그램 방화벽의 예입니다.

[Barracuda 웹 응용 프로그램 방화벽, Brocade vWAF(Brocade 가상 웹 응용 프로그램 방화벽), Imperva SecureSphere 및 ThreatSTOP IP 방화벽](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>다음 단계

- [Azure Security Center 감지 기능](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Azure Security Center의 고급 검색 기능은 Microsoft Azure 리소스를 대상으로 하는 활성 위협을 식별하고, 신속하게 대응하는 데 필요한 정보를 제공합니다.

- [Azure SQL Database 위협 검색](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Azure SQL Database 위협 검색은 데이터베이스에 대한 잠재적 위협에 대한 문제를 해결하는 데 도움이 되었습니다.
