---
title: Azure AD에서 관리 액세스를 보호하기 위한 모범 사례 | Microsoft Docs
description: 조직의 관리 액세스 및 관리자 계정이 안전한지 확인합니다. Azure AD, Azure 및 Microsoft Online Services를 구성하는 시스템 설계자 및 IT 전문가를 대상으로 합니다.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 45a0cf104df6d29515ed1a94c78c8cc1e42a2223
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안

현대 조직에서 대부분 또는 모든 비즈니스 자산의 보안은 IT 시스템을 운영하고 관리하는 권한 있는 계정의 무결성에 따라 달라집니다. 사이버 공격자를 포함한 악의적인 행위자는 종종 권한 있는 액세스의 관리자 계정 및 기타 요소를 대상으로 하여 자격 증명 도난 공격을 통해 중요한 데이터 및 시스템에 신속하게 액세스하려고 합니다. 클라우드 서비스의 경우 예방 및 대응은 클라우드 서비스 공급자와 고객의 공동 책임입니다. 엔드포인트와 클라우드에 대한 최신 위협에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/sir/default.aspx)를 참조하세요. 이 문서는 현재 계획과 여기서 설명하는 지침 사이의 차이를 제거하기 위한 로드맵을 개발하는 데 도움이 될 수 있습니다.

> [!NOTE] 
> Microsoft는 최고 수준의 신뢰, 투명도, 표준 적합성 및 규정 준수를 위해 노력하고 있습니다. Microsoft 글로벌 인시던트 대응 팀이 클라우드 서비스에 대한 공격의 영향을 완화하는 방법과 Microsoft 비즈니스 제품 및 클라우드 서비스에 보안을 기본 제공하는 방법은 [Microsoft 보안 센터 - 보안](https://www.microsoft.com/en-us/trustcenter/security) 및 [Microsoft 보안 센터 - 준수](https://www.microsoft.com/en-us/trustcenter/compliance)에서 자세히 알아보세요.

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
대부분의 조직에서 비즈니스 자산의 보안은 IT 시스템을 운영하고 관리하는 권한 있는 계정의 무결성에 따라 달라집니다. 사이버 공격자는 조직의 중요한 데이터에 액세스하기 위해 인프라 시스템(예: Active Directory 및 Azure Active Directory)에 대한 권한 있는 액세스에 집중하고 있습니다. 

인터넷에서 SaaS 앱 및 개인 장치의 사용이 증가함에 따라 기본 보안 경계로 네트워크의 입구 및 출구 지점을 보호하는 데 초점을 맞춘 전통적인 접근 방식은 그다지 효과적이지 않습니다. 복잡한 현대 엔터프라이즈에서 네트워크 보안 경계를 자연스럽게 대체하는 것은 조직의 ID 계층에 있는 인증 및 권한 부여 제어입니다. 

권한 있는 관리 계정은 새로운 이 "보안 경계"를 효과적으로 제어합니다. 환경이 온-프레미스, 클라우드 또는 하이브리드 온-프레미스 및 클라우드 호스팅 서비스인지 여부에 관계없이 권한 있는 액세스를 보호하는 것이 중요합니다. 결정된 악의적 사용자에 대한 관리 액세스를 보호하려면 조직의 시스템을 위험으로부터 격리하기 위한 완벽하고 신중한 방법을 사용해야 합니다. 

권한 있는 액세스를 보호하기 위해 변경해야 하는 요소는 다음과 같습니다.
* 프로세스, 관리 사례 및 기술 자료 관리
* 호스트 방어, 계정 보호 및 ID 관리와 같은 기술 구성 요소

이 문서에서는 주로 Azure AD, Microsoft Azure, Office 365 및 다른 클라우드 서비스에서 관리되거나 보고되는 ID 및 액세스 보안을 위한 로드맵을 만드는 데 중점을 둡니다. 온-프레미스 관리 계정이 있는 조직의 경우 [권한 있는 액세스 보안](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)에서 설명하는 Active Directory에서 관리되는 권한 있는 온-프레미스 및 하이브리드 액세스에 대한 지침을 참조하세요. 

> [!NOTE] 
> 이 문서의 지침은 주로 Azure Active Directory Premium 계획 P1 및 P2에 포함된 Azure Active Directory의 기능을 인용합니다. Azure Active Directory Premium P2는 EMS E5 제품군 및 Microsoft 365 E5 제품군에 포함되어 있습니다. 이 지침에서는 사용자를 위해 구입한 Azure AD Premium P2 라이선스가 조직에 이미 있다고 가정합니다. 이러한 라이선스가 없으면 일부 지침이 조직에 적용되지 않을 수 있습니다. 또한 이 문서 전체에서 전역 관리자는 "회사 관리자" 또는 "테넌트 관리자"와 동의어입니다.

## <a name="develop-a-roadmap"></a>로드맵 개발 

사이버 공격자로부터 권한 있는 액세스를 보호하기 위한 로드맵을 개발하고 따르는 것이 좋습니다. 언제든지 조직 내의 기존 기능과 특정 요구 사항에 맞게 로드맵을 조정할 수 있습니다. 로드맵의 각 단계에서는 악의적 사용자가 온-프레미스, 클라우드 및 하이브리드 자산에 대한 권한 있는 액세스를 공격하는 데 드는 비용과 어려움을 제시해야 합니다. Microsoft는 다음 네 개의 로드맵 단계를 권장합니다. 이 권장 로드맵은 사이버 공격 인시던트 및 대응 구현에 대한 Microsoft의 경험을 기반으로 하여 가장 효과적이고 빠른 구현을 먼저 계획합니다. 이 로드맵에 대한 타임라인은 근사값으로 표시됩니다.

![타임라인이 있는 로드맵 단계](./media/admin-roles-best-practices/roadmap-timeline.png)

* 1단계(24~48시간): 즉시 수행하도록 권장되는 중요한 항목

* 2단계(2~4주): 가장 자주 사용되는 공격 기법에 대한 완화

* 3단계(1~3개월): 가시성 빌드 및 관리자 활동에 대한 모든 권한 빌드

* 4단계(6개월 이상): 보안 플랫폼 강화를 위한 방어 체계 구축 계속

이 로드맵 프레임워크는 이미 배포한 Microsoft 기술을 최대한 사용하도록 설계되었습니다. 또한 주요 현재 및 향후 보안 기술을 활용하고, 이미 배포했거나 배포하려고 고려 중인 다른 공급업체의 보안 도구를 통합할 수 있습니다. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1단계: 즉시 수행하도록 권장되는 중요한 항목

![1단계](./media/admin-roles-best-practices/stage-one.png)

로드맵의 1단계는 빠르고 쉽게 구현할 수 있는 중요한 작업에 집중합니다. 기본 수준의 권한 있는 보안 액세스를 보장하기 위해 처음 24~48시간 내에 이러한 몇 가지 항목을 즉시 수행하는 것이 좋습니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 작업은 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 설정

Azure AD PIM(Privileged Identity Management)이 아직 설정되지 않은 경우 프로덕션 테넌트에서 이를 설정합니다. Privileged Identity Management가 설정되면 권한 있는 액세스 역할 변경에 대한 알림 이메일 메시지를 받게 됩니다. 이러한 알림에서는 디렉터리에서 권한이 높은 역할에 추가 사용자가 추가될 때 조기 경고를 제공합니다.

Azure AD Privileged Identity Management는 Azure AD Premium P2 또는 EMS E5에 포함되어 있습니다. 이러한 솔루션을 사용하면 온-프레미스 환경과 클라우드에 있는 응용 프로그램 및 리소스에 대한 액세스를 보호할 수 있습니다. Azure AD Premium P2 또는 EMS E5가 아직 없고 이 로드맵에서 언급하는 기능을 더 자세히 평가하려면, [Enterprise Mobility + Security 90일 평가판](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)에 등록합니다. Azure AD 고급 보안 보고, 감사 및 경고를 사용하여 활동을 모니터링하려면, 이러한 라이선스 평가판을 통해 Azure AD Privileged Identity Management 및 Azure AD Identity Protection을 사용해 봅니다.

Azure AD Privileged Identity Management가 설정되면 다음을 수행합니다.

1. 프로덕션 테넌트의 전역 관리자 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Privileged Identity Management를 사용하려는 테넌트를 선택하려면, Azure Portal의 오른쪽 위 모서리에서 사용자 이름을 선택합니다.

3. **모든 서비스**를 선택하고, **Azure AD Privileged Identity Management**에 대한 목록을 필터링합니다.

4. **모든 서비스** 목록에서 Privileged Identity Management를 열고 대시보드에 고정합니다.

테넌트에서 Azure AD Privileged Identity Management를 사용하는 첫 번째 사용자는 테넌트의 **보안 관리자** 및 **권한 있는 역할 관리자** 역할이 자동으로 할당됩니다. 권한 있는 역할 관리자만 사용자에 대한 Azure AD 디렉터리 역할 할당을 관리할 수 있습니다. 또한 Azure AD Privileged Identity Management를 추가하면 초기 검색 및 할당 환경을 안내하는 보안 마법사가 표시됩니다. 이 시점에서 추가로 변경하지 않고도 마법사를 종료할 수 있습니다. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>권한이 높은 역할이 있는 계정 식별 및 분류 

Azure AD Privileged Identity Management가 설정되면, 디렉터리 역할 전역 관리자, 권한 있는 역할 관리자, Exchange Online 관리자 및 SharePoint Online 관리자에 속한 사용자를 확인합니다. 테넌트에 Azure AD PIM이 없으면 [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)를 사용할 수 있습니다. 일반적인 역할인 전역 관리자 역할부터 시작합니다. Office 365 포털, Azure Portal에서, 아니면 Microsoft PowerShell용 Azure AD 모듈을 사용하여 이 관리자 역할이 할당되었는지 여부에 관계없이 이 역할이 할당된 사용자는 조직에서 구독한 모든 클라우드 서비스에서 동일한 권한을 갖습니다. 

이러한 역할에 더 이상 필요하지 않은 계정을 제거하고 관리자 역할에 할당된 나머지 계정은 다음과 같이 분류합니다.

* 관리 사용자에게 개별적으로 할당되고, 비관리 용도(예: 개인 이메일)로 사용될 수도 있습니다.
* 관리 사용자에게 개별적으로 할당되고, 관리 용도로만 지정됩니다.
* 여러 사용자 간에 공유됩니다.
* 비상(break-glass) 응급 액세스 시나리오용
* 자동화된 스크립트용
* 외부 사용자용

#### <a name="define-at-least-two-emergency-access-accounts"></a>둘 이상의 응급 액세스 계정 정의 

관리자 권한으로 기존 개인 사용자의 계정에 로그인하거나 활성화할 수 없으므로 Azure AD 테넌트의 관리에서 실수로 잠글 수 있는 상황이 발생하지 않도록 합니다. 예를 들어 조직이 온-프레미스 ID 공급자와 페더레이션되는 경우 해당 ID 공급자를 사용할 수 없으므로 사용자가 온-프레미스에 로그인할 수 없습니다. 테넌트에 둘 이상의 응급 액세스 계정을 저장하여 우발적인 관리 액세스 부족에 따른 영향을 완화할 수 있습니다.

응급 액세스 계정을 사용하면 조직이 기존 Azure Active Directory 환경 내에서 권한 있는 액세스를 제한할 수 있습니다. 이러한 계정은 높은 권한을 부여받으며 특정 개인에게 할당되지 않습니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다. 조직에서는 응급 계정의 사용을 통제하고 필요한 시간까지만 허용하는 목표를 보장해야 합니다. 

전역 관리자 역할에 할당되었거나 적합한 계정을 평가합니다. *.onmicrosoft.com 도메인을 사용하는 클라우드 전용 계정("비상" 응급 액세스용)이 표시되지 않으면 해당 계정을 만듭니다. 자세한 내용은 [Azure AD에서 응급 액세스 관리 계정의 관리](active-directory-admin-manage-emergency-access-accounts.md)를 참조하세요.

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>다단계 인증 설정 및 권한이 높고 페더레이션되지 않은 다른 모든 단일 사용자 관리자 계정 등록 

로그인 시 Azure AD 관리자 역할(전역 관리자, 권한 있는 역할 관리자, Exchange Online 관리자 및 SharePoint Online 관리자) 중 하나 이상에 영구적으로 할당된 모든 개별 사용자에 대해 Azure MFA(Multi-Factor Authentication)를 요구합니다. 이 가이드를 사용하여 [관리자 계정에 대해 MFA(Multi-Factor Authentication)](authentication/howto-mfa-userstates.md)를 사용하도록 설정하고, 해당 사용자가 모두 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에 등록되었는지 확인합니다. 자세한 내용은 [Office 365에서 데이터 및 서비스에 대한 액세스 보호](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) 가이드의 2단계 및 3단계에서 찾을 수 있습니다. 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2단계: 가장 자주 사용되는 공격 기법에 대한 완화

![2단계](./media/admin-roles-best-practices/stage-two.png)

로드맵의 2단계는 가장 자주 사용되는 자격 증명 도난 공격 기법과 악용을 완화하는 데 집중하며, 약 2-4주 내에 구현되도록 설계되었습니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 작업은 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>서비스, 소유자 및 관리자의 인벤토리 수행

BYOD(Bring-Your-Own-Device) 및 재택 근무 정책이 증가하고 기업의 무선 연결이 확장됨에 따라 네트워크에 연결된 사용자를 모니터링하는 것이 중요합니다. 효과적인 보안 감사의 경우 네트워크에서 실행되지만 IT에서 지원하지 않아 안전하지 않을 수 있는 장치, 응용 프로그램 및 프로그램이 표시되는 경우가 많습니다. 자세한 내용은 [Azure 보안 관리 및 모니터링 개요](../security/security-management-and-monitoring-overview.md)를 참조하세요. 인벤토리 프로세스에는 다음 작업이 모두 포함되어야 합니다. 

* 관리 역할과 관리할 수 있는 서비스가 있는 사용자를 식별합니다.
* Azure AD PIM을 사용하여 Azure AD에 대한 관리자 액세스 권한(1단계에 나열된 역할 외의 추가 역할 포함)이 있는 조직의 사용자를 확인합니다.
* Azure AD에 정의된 역할 외에도, Office 365에는 조직의 사용자에게 할당할 수 있는 일단의 관리자 역할이 제공됩니다. 각 관리자 역할은 일반적인 비즈니스 기능에 매핑되며, 조직의 사용자에게 Office 365 관리 센터에서 특정 작업을 수행할 수 있는 권한을 부여합니다. Office 관리 센터를 사용하여 Azure AD에서 관리되지 않는 역할을 포함하여 Office 365에 대한 관리자 액세스 권한이 있는 조직의 사용자를 확인합니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) 및 [Office 365에 대한 보안 모범 사례](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3)를 참조하세요.
* Azure, Intune 또는 Dynamics 365와 같이 조직에서 사용하는 다른 서비스에서 인벤토리를 수행합니다.
* 관리자 계정(사용자의 일상적인 계정뿐만 아니라 관리 용도로 사용되는 계정)에 연결된 작업 이메일 주소가 있고 Azure MFA에 등록되었거나 온-프레미스에서 MFA를 사용하는지 확인합니다.
* 사용자에게 관리 액세스 권한에 대한 비즈니스상의 사유를 요청합니다.
* 필요하지 않은 개인 및 서비스에 대한 관리자 액세스 권한을 제거합니다.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>직장 또는 학교 계정으로 전환해야 하는 관리 역할의 Microsoft 계정 식별 

조직의 초기 전역 관리자가 Azure AD를 사용하기 시작할 때 기존 Microsoft 계정 자격 증명을 다시 사용하는 경우가 있습니다. 이러한 Microsoft 계정은 개별 클라우드 기반 계정 또는 동기화된 계정으로 대체되어야 합니다. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>전역 관리자 계정에 대해 별도의 사용자 계정 및 메일 전달 보장 

사이버 공격자가 개인 이메일 계정을 정기적으로 피싱하므로 전역 관리자 계정에 개인 이메일 주소가 없어야 합니다. 관리자 권한과 인터넷 위험(피싱 공격, 의도하지 않은 웹 검색)을 분리하려면 관리자 권한이 있는 각 사용자에 대한 전용 계정을 만듭니다. 

아직 전용 계정을 만들지 않았으면 사용자가 전역 관리 작업을 수행할 수 있는 별도의 계정을 만들어 실수로 이메일을 열거나 해당 관리자 계정과 연결된 프로그램을 실행하지 않도록 합니다. 이러한 계정에 작업 사서함으로 전달된 해당 이메일이 있는지 확인합니다.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>관리 계정의 암호가 최근에 변경되었는지 확인

지난 90일 동안 모든 사용자가 자신의 관리 계정에 로그인하고 암호를 한 번 이상 변경했는지 확인합니다. 또한 여러 사용자가 암호를 알고 있는 공유 계정의 암호가 최근에 변경되었는지 확인합니다.

#### <a name="turn-on-password-hash-synchronization"></a>암호 해시 동기화 켜기

암호 해시 동기화는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화하는 데 사용되는 기능입니다. AD FS(Active Directory Federation Service) 또는 다른 ID 공급자에서 페더레이션을 사용하도록 결정한 경우에도, AD 또는 ADFS 서버와 같은 온-프레미스 인프라가 실패하거나 일시적으로 사용할 수 없게 되므로, 필요에 따라 암호 해시 동기화를 백업으로 설정할 수 있습니다. 이렇게 하면 사용자가 온-프레미스 AD 인스턴스에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 서비스에 로그인할 수 있습니다. 또한 사용자가 Azure AD에 연결되지 않은 다른 서비스에서 동일한 이메일 주소와 암호를 사용하면, 암호 해시와 손상된 것으로 알려진 암호를 비교하여 Identity Protection에서 손상된 자격 증명을 검색할 수 있습니다.  자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)을 참조하세요.

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>모든 권한이 있는 역할의 사용자 및 노출된 사용자에 대해 MFA(Multi-Factor Authentication) 요구

Azure AD에서는 계정이 손상된 경우 당한 영향을 미칠 수 있는 관리자 및 모든 다른 사용자(예: 재무 담당자)를 포함하여 모든 사용자에 대해 MFA(Multi-Factor Authentication)를 요구하는 것이 좋습니다. 이렇게 하면 손상된 암호로 인한 공격의 위험이 줄어듭니다.

설정되는 MFA는 다음과 같습니다.

* 조직의 임원에 대한 계정과 같이 [많이 노출되는 계정에 대한 MFA](authentication/multi-factor-authentication-security-best-practices.md) 
* 연결된 다른 SaaS 앱에 대한 [개별 사용자와 연결된 모든 관리자 계정에 대한 MFA](authentication/howto-mfa-userstates.md) 
* Exchange Online 및 Office 포털에서 관리되는 역할의 관리자를 포함하여 Microsoft SaaS 앱의 모든 관리자에 대한 MFA

비즈니스용 Windows Hello를 사용하는 경우 Windows Hello 로그인 환경을 사용하여 MFA 요구 사항을 충족할 수 있습니다. 자세한 내용은 [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)를 참조하세요. 

#### <a name="configure-identity-protection"></a>Identity Protection 구성 

Azure AD Identity Protection은 조직의 ID에 영향을 주는 잠재적 취약성을 검색하는 데 사용할 수 있는 알고리즘 기반 모니터링 및 보고 도구입니다. 검색된 의심스러운 활동에 대해 자동화된 대응을 구성하고 이를 해결하기 위한 적절한 조치를 수행할 수 있습니다. 자세한 내용은 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)을 참조하세요.

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Office 365 보안 점수 가져오기(Office 365를 사용하는 경우)

보안 점수는 사용하고 있는 Office 365 서비스(예: OneDrive, SharePoint 및 Exchange)를 파악한 다음, 설정 및 활동을 조사하고 Microsoft에서 설정한 기준과 비교합니다. 보안 모범 사례와 일치하는 정도에 따라 점수가 매겨집니다. Office 365 Business Premium 또는 Enterprise 구독에 대한 관리자 권한(전역 관리자 또는 사용자 지정 관리자 역할)이 있는 사용자는 [https://securescore.office.com](https://securescore.office.com/)의 보안 점수에 액세스할 수 있습니다.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Office 365 보안 및 규정 준수 지침 검토(Office 365를 사용하는 경우)

[보안 및 규정 준수에 대한 계획](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)은 Office 365 고객이 Office 365를 구성하고 다른 EMS 기능을 활용하는 방법에 대해 간략히 설명합니다. 그런 다음, [Office 365에서 데이터 및 서비스에 대한 액세스를 보호하는 방법](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)의 3~6단계 및 [Office 365에서 보안 및 규정 준수를 모니터링하는 방법](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6) 가이드를 검토합니다.


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365 활동 모니터링 구성(Office365를 사용하는 경우)

조직의 사용자가 Office 365 서비스를 사용하는 방법을 모니터링하여 관리 계정이 있는 사용자와 해당 포털에 로그인하지 않으므로 Office 365 액세스 권한이 필요하지 않은 사용자를 확인할 수 있습니다. 자세한 내용은 [Office 365 관리 센터의 작업 보고서](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)를 참조하세요.

#### <a name="establish-incidentemergency-response-plan-owners"></a>인시던트/비상 대응 계획 소유자 설정

인시던트 대응을 효과적으로 수행하는 것은 복잡한 작업입니다. 따라서 성공적인 인시던트 대응 기능을 설정하려면 상당한 계획과 리소스가 필요합니다. 이 경우 사이버 공격을 지속적으로 모니터링하고 인시던트 처리의 우선 순위를 지정하는 절차를 확립하는 것이 필수적입니다. 데이터 수집, 분석 및 보고의 효과적인 방법은 관계를 만들고 다른 내부 그룹 및 계획 소유자와 통신을 설정하는 데 있어 매우 중요합니다. 자세한 내용은 [Microsoft 보안 대응 센터](https://technet.microsoft.com/security/dn440717)를 참조하세요. 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>아직 보안이 설정되지 않은 권한 있는 온-프레미스 관리 계정에 대한 보안

Azure Active Directory 테넌트가 온-프레미스 Active Directory와 동기화되는 경우 [권한 있는 보안 액세스 로드맵](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 1단계의 지침을 따릅니다. 여기에는 온-프레미스 관리 작업을 수행해야 하는 사용자에 대한 별도의 관리자 계정 만들기, Active Directory 관리자에 대한 액세스 권한 있는 워크스테이션 배포 및 워크스테이션 및 서버에 대한 고유 로컬 관리자 암호 만들기가 포함됩니다.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="complete-an-inventory-of-subscriptions"></a>구독의 인벤토리 완성

엔터프라이즈 포털과 Azure Portal을 사용하여 프로덕션 응용 프로그램을 호스팅하는 조직의 구독을 식별합니다. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>관리자 역할에서 Microsoft 계정 제거

Xbox, Live 및 Outlook과 같은 다른 프로그램의 Microsoft 계정은 조직 구독에 대한 관리자 계정으로 사용하면 안됩니다. 모든 Microsoft 계정에서 관리자 상태를 제거하고 Active Directory 직장 또는 학교 계정(예: chris@contoso.com)으로 바꿉니다.

#### <a name="monitor-azure-activity"></a>Azure 활동 모니터링

Azure 활동 로그는 Azure에서 구독 수준 이벤트 기록을 제공합니다. 즉 누가 어떤 리소스를 생성, 업데이트, 삭제했는지와 언제 이러한 이벤트가 발생했는지에 대한 정보를 제공합니다. 자세한 내용은 [Azure 구독에서 중요한 작업에 대한 알림 감사 및 수신](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md)을 참조하세요.


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD를 통해 다른 클라우드 앱에 대한 액세스를 관리하는 조직을 위한 추가 단계 

#### <a name="configure-conditional-access-policies"></a>조건부 액세스 정책 구성

온-프레미스 및 클라우드 호스팅 응용 프로그램에 대한 조건부 액세스 정책을 준비합니다. 사용자 작업 공간 가입 장치가 있는 경우 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](active-directory-device-registration-on-premises-setup.md)에서 자세한 정보를 얻을 수 있습니다.


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3단계: 가시성 빌드 및 관리자 활동에 대한 완전한 제어

![3단계](./media/admin-roles-best-practices/stage-three.png)

3단계는 2단계의 완화를 기반으로 하며, 약 1~3개월 내에 구현되도록 설계되었습니다. 권한 있는 보안 액세스 로드맵의 이 단계에 포함되는 구성 요소는 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>관리자 역할의 사용자에 대한 액세스 검토 수행

더 많은 회사 사용자가 클라우드 서비스를 통해 권한 있는 액세스를 얻고 있으며, 이로 인해 관리되지 않는 플랫폼이 늘어날 수 있습니다. 여기에는 Office 365 전역 관리자인 사용자, Azure 구독 관리자 및 VM 또는 SaaS 앱에 대한 관리자 액세스 권한이 있는 사용자가 포함됩니다. 대신, 조직에서는 모든 직원, 특히 관리자가 권한이 없는 사용자로 일상적인 비즈니스 트랜잭션을 처리하고, 필요한 경우에만 관리자 권한을 부여받아야 합니다. 관리자 역할의 사용자 수는 처음에 채택한 이후 증가했을 수 있으므로 관리자 권한을 활성화할 수 있는 자격이 있는 모든 사용자를 식별하고 확인하기 위해 액세스 검토를 수행합니다. 

다음을 수행합니다.

* Azure AD 관리자인 사용자를 확인하고, 요청 시 JIT(Just-In-Time) 관리자 액세스 권한 및 역할 기반 보안 제어를 사용하도록 설정합니다.
* 관리자 권한 액세스에 대한 명확한 이유가 없는 사용자를 다른 역할로 변환합니다(적격 역할이 아닌 경우 제거함).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>더 강력한 모든 사용자 인증에 대한 지속적인 공개 

Azure MFA 또는 Windows Hello와 같이 고위 경영진, 고위 관리자, 중요 IT 및 보안 담당자 및 많이 노출되는 다른 사용자에게 최신의 강력한 인증을 요구합니다. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Azure AD에 대한 관리 전용 워크스테이션 사용

공격자는 프로그램 논리를 변경하거나 관리자가 자격 증명을 입력하는 것을 염탐하는 악성 코드를 통해 권한 있는 계정을 대상으로 지정하여 조직의 데이터와 시스템에 액세스함으로써 데이터의 무결성 및 신뢰성을 방해할 수 있습니다. PAW(권한 있는 액세스 워크스테이션)는 인터넷 공격 및 위협 벡터로부터 보호되는 전용 운영 체제를 중요한 작업을 위해 제공합니다. 이러한 중요한 작업 및 계정을 매일 사용하는 워크스테이션 및 장치에서 분리하면 피싱 공격, 응용 프로그램 및 OS 취약점, 다양한 가장 공격 및 자격 증명 도난 공격(예: 키 입력 로깅, Pass-the-Hash 및 Pass-The-Ticket)으로부터 매우 강력한 보호 효과를 얻을 수 있습니다. 액세스 권한 있는 워크스테이션을 배포하는 경우 강화된 데스크톱 환경을 제외하고는 관리자가 관리자 자격 증명을 입력하는 위험을 줄일 수 있습니다. 자세한 내용은 [액세스 권한 있는 워크스테이션](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)을 참조하세요.

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>인시던트 처리에 대한 NIST 권장 사항 검토 

NIST(미국 표준 기술 연구소)에서는 인시던트 처리 지침, 특히 인시던트 관련 데이터를 분석하고 각 인시던트에 대한 적절한 대응을 결정하기 위한 지침을 제공합니다. 자세한 내용은 [NIST 컴퓨터 보안 인시던트 처리 지침(SP 800-61, 수정 버전 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)을 참조하세요.

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>추가 관리 역할에 JIT에 대한 PIM(Privileged Identity Management) 구현

Azure Active Directory의 경우 [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) 기능을 사용합니다. 권한 있는 역할을 시간 제한으로 활성화하려면 다음 작업을 수행하도록 설정해야 합니다.

* 관리자 권한을 활성화하여 특정 작업 수행
* 활성화 프로세스 중에 MFA 적용
* 경고를 사용하여 관리자에게 대역 외 변경 내용을 알림
* 사용자가 미리 구성된 시간 동안 특정 권한을 보유할 수 있도록 설정
* 보안 관리자가 모든 권한 있는 ID를 검색하고, 감사 보고서를 보고, 액세스 검토를 만들어 관리자 권한을 활성화할 수 있는 자격이 있는 모든 사용자를 식별하도록 허용

Azure AD Privileged Identity Management를 이미 사용하고 있는 경우 필요에 따라 시간 제한 권한에 대한 시간 범위(예: 유지 관리 기간)를 조정합니다.

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>암호 기반 로그인 프로토콜에 대한 노출 확인(Exchange Online을 사용하는 경우)

과거에는 프로토콜에서 사용자 이름/암호 조합이 장치, 이메일 계정, 전화 등에 포함되어 있다고 가정했습니다. 그러나 이제는 클라우드에서 사이버 공격에 대한 위험이 있으므로, 자격 증명이 손상된 경우 조직에 치명적일 수 있는 모든 잠재적 사용자를 식별하고, 강력한 인증 요구 사항 및 조건부 액세스를 구현하여 사용자 이름/암호를 통해 이메일에 로그인할 수 없도록 차단하는 것이 좋습니다. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Office 365 역할에 대한 역할 검토 평가 수행(Office 365를 사용하는 경우)

모든 관리자 사용자가 올바른 역할에 있는지 여부를 평가합니다(이 평가에 따라 삭제 및 다시 할당).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Office 365에서 사용되는 보안 인시던트 관리 방법을 검토하고 자신의 조직과 비교합니다.

이 보고서는 [Microsoft Office 365의 보안 인시던트 관리](https://www.microsoft.com/download/details.aspx?id=54302)에서 다운로드할 수 있습니다.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>권한 있는 온-프레미스 관리 계정 보안 유지

Azure Active Directory 테넌트가 온-프레미스 Active Directory에 연결되는 경우 [권한 있는 보안 액세스 로드맵](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 2단계의 지침을 따릅니다. 여기에는 모든 관리자에 대한 액세스 권한 있는 워크스테이션 배포, MFA 요구, DC 유지 관리를 위한 Just Inough Admin(충분한 관리) 사용, 도메인 공격 영역 감소, 공격 탐지를 위한 ATA 배포가 포함됩니다.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="establish-integrated-monitoring"></a>통합 모니터링 설정

[Azure Security Center](../security-center/security-center-intro.md)는 Azure 구독에서 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 간과될 수 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>호스팅된 Virtual Machines 내에서 권한 있는 계정 인벤토리 구성

대부분의 경우 사용자에게 모든 Azure 구독 또는 리소스에 대한 무제한 권한을 부여할 필요가 없습니다. Azure AD 관리자 역할을 사용하여 조직 내의 직무를 분리하고 특정 작업을 수행해야 하는 사용자에게 액세스 권한만 부여할 수 있습니다. 예를 들어 Azure AD 관리자 역할을 사용하여 한 관리자가 구독의 VM만 관리하고, 다른 관리자는 동일한 구독 내에서 SQL 데이터베이스를 관리할 수 있습니다. 자세한 내용은 [Azure Portal에서 역할 기반 Access Control 시작](../role-based-access-control/overview.md)을 참조하세요.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Azure AD 관리자 역할에 대한 PIM 구현

Azure AD 관리자 역할이 있는 Privileged Identity Management를 사용하여 Azure 리소스에 대한 액세스를 관리, 제어 및 모니터링합니다. PIM을 사용하면 권한의 노출 시간을 줄이고 보고서 및 경고를 통해 사용에 대한 가시성을 높여 사이버 공격으로부터 권한 있는 계정을 보호합니다. 자세한 내용은 [Privileged Identity Management를 사용하여 Azure 리소스에 대한 RBAC 액세스 관리](../role-based-access-control/pim-azure-resource.md)를 참조하세요.

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure 로그 통합을 사용하여 SIEM 시스템에 관련 Azure 로그 보내기 

Azure 로그 통합을 사용하면 Azure 리소스의 원시 로그를 조직의 기존 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있습니다. [Azure 로그 통합](../security/security-azure-log-integration-overview.md)은 Windows 이벤트 뷰어 로그에서 Windows 이벤트를 수집하고, Azure 활동 로그, Azure Security Center 경고 및 Azure 진단 로그에서 Azure 리소스를 수집합니다. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Azure AD를 통해 다른 클라우드 앱에 대한 액세스를 관리하는 조직을 위한 추가 단계

#### <a name="implement-user-provisioning-for-connected-apps"></a>연결된 앱에 대한 사용자 프로비저닝 구현

Azure AD를 사용하면 Dropbox, Salesforce, ServiceNow 등과 같은 클라우드 응용 프로그램(SaaS)에서 사용자 ID 만들기, 유지 관리 및 제거를 자동화할 수 있습니다. 자세한 내용은 [Azure AD를 사용하여 SaaS 응용 프로그램의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)를 참조하세요.

#### <a name="integrate-information-protection"></a>정보 보호 통합

MCAS를 사용하면 Azure Information Protection 분류 레이블을 기반으로 하여 파일을 조사하고 정책을 설정하여 클라우드에서 데이터를 더 잘 표시하고 제어할 수 있습니다. 클라우드에서 파일을 검색 및 분류하고 Azure Information Protection 레이블을 적용합니다. 자세한 내용은 [Azure Information Protection 통합](https://docs.microsoft.com/cloud-app-security/azip-integration)을 참조하세요.

#### <a name="configure-conditional-access"></a>조건부 액세스 구성

[SaaS 앱](https://azure.microsoft.com/overview/what-is-saas/) 및 Azure AD 연결 앱에 대한 그룹, 위치 및 응용 프로그램 민감도에 따라 조건부 액세스를 구성합니다. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>연결된 클라우드 앱의 활동 모니터링

연결된 응용 프로그램에서도 사용자의 액세스가 보호되도록 하려면 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)를 활용하는 것이 좋습니다. 이를 통해 관리자 계정 보안 외에도 클라우드 앱에 대한 엔터프라이즈 액세스를 보호하려면 다음 작업을 수행하도록 설정해야 합니다.

* 클라우드 앱에 대한 가시성 및 제어 확장
* 액세스, 활동 및 데이터 공유에 대한 정책 만들기
* 위험한 활동, 비정상적인 동작 및 위협을 자동으로 식별
* 데이터 유출 방지
* 위험 방지, 자동화된 위협 방지 및 정책 적용 최소화

Cloud App Security SIEM 에이전트는 Cloud App Security와 SIEM 서버를 통합하여 Office 365 경고 및 활동을 중앙 집중식으로 모니터링할 수 있도록 합니다. 서버에서 실행되고 Cloud App Security에서 경고 및 활동을 가져와서 SIEM 서버로 스트리밍합니다. 자세한 내용은 [SIEM 통합](https://docs.microsoft.com/cloud-app-security/siem)을 참조하세요.

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4단계: 사전 예방적인 보안 상태에 대한 방어 체계 구축 계속


![4단계](./media/admin-roles-best-practices/stage-four.png)

로드맵의 4단계는 3단계의 가시성을 기반으로 하며, 6개월 정도에 걸쳐 구현되도록 설계되었습니다. 로드맵을 완성하면 현재 알려져 있고 사용 가능한 잠재적 공격으로부터 강력한 권한 있는 액세스 보호를 개발할 수 있습니다. 그러나 보안 위협은 지속적으로 진화하고 변화하므로 비용을 높이고 환경을 대상으로 하는 악의적 사용자의 성공률을 줄이는 데 초점을 맞춘 지속적인 프로세스로 보안을 확인하는 것이 좋습니다.

권한 있는 액세스 보호는 현대 조직에서 비즈니스 자산에 대한 보안 보장을 확립하는 중요한 첫 번째 단계이지만, 정책, 운영, 정보 보안, 서버, 응용 프로그램, PC, 장치, 클라우드 패브릭 및 기타 구성 요소와 같은 요소가 포함된 완전한 보안 프로그램의 유일한 부분이 아니며, 지속적인 보안 보장을 제공합니다. 

권한 있는 액세스 계정을 관리하는 것 외에도 다음 사항을 지속적으로 검토하는 것이 좋습니다.

* 관리자가 권한이 없는 사용자로 일상적인 업무를 수행하고 있는지 확인합니다.
* 필요한 경우에만 권한 있는 액세스 권한을 부여하고, 나중에 제거합니다(Just-In-Time).
* 권한 있는 계정과 관련된 감사 활동을 유지하고 검토합니다.

완벽한 보안 로드맵 작성에 대한 자세한 내용은 [Microsoft 클라우드 IT 아키텍처 리소스](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)를 참조하세요. 이러한 항목을 다루는 Microsoft 서비스에 대한 자세한 내용은 Microsoft 담당자에게 문의하거나 [회사를 보호하기 위한 중요한 사이버 방어 구축](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)을 참조하세요.

권한 있는 보안 액세스 로드맵의 마지막 단계에 포함되는 구성 요소는 다음과 같습니다.

### <a name="general-preparation"></a>일반적인 준비

#### <a name="review-admin-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 검토 

현재 기본 제공되는 Azure AD 관리자 역할이 최신 상태인지 확인하고, 사용자가 해당 권한에 필요한 역할 및 위임에만 있는지 확인합니다. Azure AD를 사용하면 별도의 관리자를 지정하여 다른 기능을 제공할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)을 참조하세요.

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Azure AD 가입 장치를 관리하는 사용자 검토

자세한 내용은 [하이브리드 Azure Active Directory 가입 장치를 구성하는 방법](device-management-hybrid-azuread-joined-devices-setup.md)을 참조하세요.

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>[기본 제공 Office 365 관리자 역할](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)의 멤버 검토
Office 365를 사용하는 경우
‎
#### <a name="validate-incident-response-plan"></a>인시던트 대응 계획의 유효성 검사

계획을 향상시키려면 다음과 같이 계획이 예상대로 작동하는지 정기적으로 확인하는 것이 좋습니다.

* 기존 로드맵을 살펴보고 누락된 항목 확인
* 사후 평가 분석에 따라 기존 모범 사례 수정 또는 새 모범 사례 정의
* 업데이트된 인시던트 대응 계획 및 모범 사례가 조직 전체에 배포되었는지 확인


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Azure에 대한 액세스를 관리하는 조직을 위한 추가 단계 

[Azure 구독의 소유권을 다른 계정으로 양도](../billing/billing-subscription-transfer.md)할 필요가 있는지 결정합니다.
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"비상": 긴급 상황에서 수행할 작업

![응급](./media/admin-roles-best-practices/emergency.jpeg)

1. 주요 관리자와 보안 책임자에게 인시던트와 관련된 정보를 알립니다.

2. 공격 플레이 북을 검토합니다. 

3. Azure AD에 로그인하려면 "비상" 계정 사용자 이름/암호 조합에 액세스합니다. 

4. [Azure 지원 요청을 열어](../azure-supportability/how-to-create-azure-support-request.md) Microsoft로부터 도움을 받습니다.

5. [Azure AD 로그인 보고서](active-directory-reporting-azure-portal.md)를 확인합니다. 이벤트가 발생하는 시점과 보고서에 포함되는 시점 사이에 지연이 있을 수 있습니다.

6. 하이브리드 환경의 경우 페더레이션되고 AD FS 서버를 사용할 수 없으면 일시적으로 페더레이션 인증에서 암호 해시 동기화를 사용하도록 전환해야 할 수 있습니다. 이렇게 하면 AD FS 서버를 사용할 수 있을 때까지 도메인 페더레이션을 관리 인증으로 되돌립니다.

7. 권한 있는 계정에 대한 이메일을 모니터링합니다.

8. 잠재적인 법의학 및 법적 조사를 위해 관련 로그의 백업을 저장해야 합니다.

Microsoft Office 365에서 보안 인시던트를 처리하는 방법에 대한 자세한 내용은 [Microsoft Office 365의 보안 인시던트 관리](http://aka.ms/Office365SIM)를 참조하세요.

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>FAQ: 권한 있는 액세스 보안과 관련하여 자주 받는 일반적인 질문  


**Q:** 보안 액세스 구성 요소를 아직 구현하지 않은 경우 어떻게 해야 할까요?

**답변:** 둘 이상의 비상 계정을 정의하고, 권한 있는 관리자 계정에 MFA를 할당하고, 사용자 계정과 전역 관리자 계정을 분리합니다.


**Q:** 위반이 발생되면 가장 먼저 해결해야 하는 문제는 무엇인가요?

**답변:** 많이 노출되는 개인에게 가장 강력한 인증을 요구해야 합니다.


**Q:** 권한 있는 관리자가 비활성화되면 어떻게 되나요?

**답변:** 항상 최신 상태로 유지되는 전역 관리자 계정을 만듭니다.


**Q:** 전역 관리자 하나만 남아 있지만 연결할 수 없는 경우 어떻게 되나요? 

**답변:** 비상 계정 중 하나를 사용하여 즉각적인 권한 있는 액세스를 얻습니다.


**Q:** 조직 내에서 관리자를 보호하려면 어떻게 해야 할까요?

**답변:** 관리자는 항상 "권한이 없는" 표준 사용자로 일상적인 업무를 수행해야 합니다.
 

**Q:** Azure AD 내에서 관리자 계정을 만드는 가장 좋은 방법은 무엇인가요?

**답변:** 특정 관리 작업에 대한 권한 있는 액세스를 예약합니다.


**Q:** 영구 관리자 액세스를 줄이기 위한 도구는 무엇인가요?

**답변:** PIM(Privileged Identity Management) 및 Azure AD 관리자 역할입니다.


**Q:** Azure AD에 관리자 계정을 동기화할 때 Microsoft의 입장은 무엇인가요?

**답변:** 계층 0 관리자 계정(AD 포리스트, 도메인 또는 도메인 컨트롤러 및 모든 자산에 대한 직접 또는 간접 관리 제어 권한이 있는 계정, 그룹 및 기타 자산 포함)은 온-프레미스 AD 계정에만 사용되며, 일반적으로 클라우드의 Azure AD에 대해 동기화됩니다. 


**Q:** 관리자가 포털에서 임의의 관리자 액세스 권한을 할당하지 못하게 하려면 어떻게 해야 할까요?

**답변:** 모든 사용자와 대부분의 관리자에게 권한이 없는 계정을 사용합니다. 먼저 조직의 공간을 개발하여 권한이 부여되어야 하는 몇 개의 관리자 계정을 결정합니다. 그리고 새로 만든 관리 사용자를 모니터링합니다.


## <a name="next-steps"></a>다음 단계

* [Microsoft 보안 센터 - 제품 보안](https://www.microsoft.com/en-us/trustcenter/security) – Microsoft 클라우드 제품 및 서비스의 보안 기능

* [Microsoft 보안 센터 - 준수](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) – 클라우드 서비스를 위한 포괄적인 Microsoft 규정 준수 제품 집합

* [위험 평가 수행 방법에 대한 지침](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) - Microsoft 클라우드 서비스에 대한 보안 및 규정 준수 요구 사항 관리

### <a name="other-ms-online-services"></a>기타 MS 온라인 서비스 

* [Microsoft Intune 보안](https://www.microsoft.com/en-us/trustcenter/security/intune-security) – Intune은 클라우드에서 모바일 장치 관리, 모바일 응용 프로그램 관리 및 PC 관리 기능을 제공합니다.

* [Microsoft Dynamics 365 보안](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) – Dynamics 365는 CRM(고객 관계 관리) 및 ERP(전사적 자원 관리) 기능을 통합하는 Microsoft 클라우드 기반 솔루션입니다.

 
