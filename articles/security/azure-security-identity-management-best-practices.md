---
title: Azure ID 및 액세스 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 ID 관리 및 액세스 제어에 대한 몇 가지 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 6632ab962f3df0cfee8d28d7dad40bad8baf3f50
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management 및 액세스 제어 보안 모범 사례

많은 사람이 ID를 기존 네트워크 중심 관점에서 역할을 대신하는 보안에 대한 새로운 경계 계층으로 생각하고 있습니다. 보안 주의 및 투자를 중심으로 하는 이러한 진화는 네트워크 경계에 점점 더 많은 구멍이 생기고 경계 방어가 [BYOD](http://aka.ms/byodcg) 장치 및 클라우드 응용 프로그램이 급증하기 전만큼 효과적일 수 없다는 사실에 기인합니다.

이 문서에서는 Azure ID 관리 및 액세스 제어 보안 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 [Azure AD](../active-directory/active-directory-whatis.md)에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure ID 관리 및 액세스 제어 보안 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 설명하는 Azure Identity Management 및 액세스 제어 보안 모범 사례는 다음과 같습니다.

* ID 관리를 중앙 집중화
* SSO(Single Sign-On) 사용
* 암호 관리 배포
* 사용자에 대한 MFA(Multi-Factor Authentication) 적용
* RBAC(역할 기반 액세스 제어) 사용
* Resource Manager를 사용하여 리소스가 만들어지는 위치 제어
* SaaS 앱에 ID 기능을 활용하도록 개발자 안내
* 의심스러운 활동을 적극적으로 모니터링

## <a name="centralize-your-identity-management"></a>ID 관리를 중앙 집중화

ID 보호에서 중요한 한 단계는 IT가 계정이 만들어진 위치에 관하여 하나의 단일 위치에서 계정을 관리할 수 있도록 하는 것입니다. 대부분의 기업 IT 조직은 기본 계정 디렉터리를 온-프레미스로 가지고 있지만 하이브리드 클라우드 배포가 증가하는 추세에 있으므로 온-프레미스와 클라우드 디렉터리를 통합하는 방법을 이해하고 최종 사용자에게 원활한 환경을 제공해야 합니다.

이 [하이브리드 ID](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) 시나리오를 수행하기 위해 다음 두 가지 옵션을 권장합니다.

* Azure AD Connect를 사용하여 온-프레미스 디렉터리를 클라우드 디렉터리와 동기화
* [암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) 및 [통과 인증](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)을 사용하여 Single sign-on을 사용하도록 설정 또는 [Active Directory 페더레이션 서비스](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)(AD FS)를 사용하여 클라우드 디렉터리가 있는 온-프레미스 ID 페더레이션

온-프레미스 ID를 클라우드 ID와 통합하는 데 실패한 조직은 계정 관리의 관리 오버헤드 증가에 따른 실수 및 보안 위반의 가능성이 증가를 경험하게 될 것입니다.

Azure AD 동기화 옵션에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/active-directory-aadconnect.md) 문서를 참조하세요.

## <a name="enable-single-sign-on-sso"></a>SSO(Single Sign-On) 사용

관리할 디렉터리가 여러 개인 경우, 이는 IT뿐만 아니라 여러 암호를 기억해야 하는 최종 사용자에게도 관리 문제가 됩니다. [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)를 사용하면 사용자에게 온-프레미스 또는 클라우드에서 리소스가 있는 위치와 상관없이 같은 자격 증명 집합을 사용하여 로그인하고 필요한 리소스에 액세스하는 기능을 제공할 수 있습니다.

SSO를 사용하여 사용자가 Azure AD에서 조직 계정을 기반으로 해당 [SaaS 응용 프로그램](../active-directory/manage-apps/what-is-single-sign-on.md)에 액세스할 수 있습니다. 이 방법은 Microsoft SaaS 앱뿐만 아니라 [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) 및 [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md) 등 다른 앱에도 적용할 수 있습니다. Azure AD를 [SAML 기반 ID](../active-directory/fundamentals-identity.md) 공급자로 사용하도록 응용 프로그램을 구성할 수 있습니다. 보안 컨트롤인 Azure AD는 Azure AD를 사용하여 액세스를 허용한 경우가 아니면 응용 프로그램에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 직접적으로 또는 멤버인 그룹을 통해 액세스를 부여할 수 있습니다.

> [!NOTE]
> SSO를 사용한다는 결정은 온-프레미스 디렉터리를 클라우드 디렉터리와 통합하는 방법에 영향을 줍니다. 디렉터리 동기화는 [동일한 로그온 환경](../active-directory/active-directory-aadconnect.md)만 제공하므로 SSO를 원하는 경우 페더레이션을 사용해야 합니다.
>
>

사용자 및 응용 프로그램에 대해 SSO를 적용하지 않는 조직은 사용자가 여러 개의 암호를 갖게 되어 암호를 다시 사용하거나 취약한 암호를 사용할 가능성이 급증하는 상황에 더 많이 노출됩니다.

Azure AD SSO에 대한 자세한 내용은 [Azure AD Connect를 통한 AD FS 관리 및 사용자 지정](../active-directory/active-directory-aadconnect-federation-management.md) 문서를 참조하세요.

## <a name="deploy-password-management"></a>암호 관리 배포

테넌트가 여럿이거나 사용자가 [자신의 암호를 재설정](../active-directory/active-directory-passwords-update-your-own-password.md)할 수 있도록 하려는 시나리오에서는 남용을 방지하기 위해 적절한 보안을 사용해야 합니다. Azure의 셀프 서비스 암호 재설정 기능을 활용하여 비즈니스 요구 사항을 충족하도록 보안 옵션을 사용자 지정할 수 있습니다.

사용자가 이러한 단계를 수행하려고 할 때 이러한 사용자의 의견을 듣고 이들의 경험에서 배우는 것이 중요합니다. 이러한 경험을 바탕으로 더 큰 그룹에 대해 배포하는 동안 발생할 수 있는 잠재적 문제를 완화하도록 계획을 구체화합니다. 또한 [암호 재설정 등록 활동 보고서](../active-directory/active-directory-passwords-get-insights.md)를 사용하여 등록하는 사용자를 모니터링하는 것이 좋습니다.

암호 변경 지원 호출을 방지하되 사용자가 자신의 암호를 재설정할 수 있도록 하고자 하는 조직은 암호 문제로 인해 더 많은 기술 지원 팀 호출을 겪게 될 가능성이 더 높습니다. 여러 테넌트가 있는 조직의 경우 이 유형의 기능을 구현하고 사용자가 보안 정책에 설정된 보안 경계 내에서 암호 재설정을 수행할 수 있도록 하는 것은 필수입니다.

암호 재설정에 대한 자세한 내용은 [암호 관리 배포 및 암호를 사용하도록 사용자 교육](../active-directory/authentication/howto-sspr-deployment.md) 문서를 참조하세요.

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>사용자에 대한 MFA(Multi-Factor Authentication) 적용

[PCI DSS 버전 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32)와 같은 업계 표준을 준수해야 하는 조직의 경우 사용자를 인증하기 위해 Multi-Factor Authentication을 반드시 시행해야 합니다. 사용자 인증에 MFA를 적용하면 업계 표준 준수뿐만 아니라 조직이 [PtH(Pass-the-Hash)](http://aka.ms/PtHPaper)와 같은 기밀 도난 유형의 공격을 완화하는 데에도 도움이 될 수 있습니다.

사용자에 대한 Azure MFA를 활성화하면 사용자 로그인 및 트랜잭션에 두 번째 보안 계층이 추가됩니다. 이 경우 트랜잭션이 파일 서버 또는 SharePoint Online에 있는 문서에 액세스할 수 있습니다. 또한 Azure MFA를 사용하면 IT는 손상된 자격 증명이 조직의 데이터에 액세스하는 가능성을 줄일 수 있습니다.

예: 사용자에 대해 Azure MFA를 적용하고 전화 통화 또는 문자 메시지를 검증 수단으로 사용하도록 구성합니다. 사용자의 자격 증명이 손상된 경우 공격자는 사용자의 전화에 대한 액세스 권한이 없으므로 리소스에 액세스할 수 없습니다. 추가적인 ID 보호 계층을 추가하지 않는 조직은 자격 증명 도난 공격에 취약하며, 이로 인해 데이터가 손상될 수 있습니다.

전체 인증 제어를 온-프레미스에 유지하려는 조직이 선택할 수 있는 대안 중 하나는 MFA 온-프레미스라고도 하는 [Azure Multi-factor Authentication 서버](../active-directory/authentication/howto-mfaserver-deploy.md)를 사용하는 것입니다. 이 방법을 사용하면 여전히 Multi-factor Authentication을 적용하면서도 MFA 서버를 온-프레미스에 유지할 수 있습니다.

Azure MFA에 대한 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](../active-directory/authentication/howto-mfa-getstarted.md)을 참조하세요.

## <a name="use-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어) 사용

[알아야 할 사항](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙을 기반으로 액세스를 제한하는 것은 데이터 액세스에 보안 정책을 적용하고자 하는 조직의 경우 필수입니다. Azure 역할 기반 Access Control(RBAC)을 사용하여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 권한을 할당할 수 있습니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다.

Azure의 [기본 제공 RBAC 역할](../role-based-access-control/built-in-roles.md)을 활용하여 사용자에게 권한을 할당할 수 있습니다. Storage 계정을 관리해야 하는 클라우드 운영자를 위한 *Storage 계정 참여자* 및 *클래식 Storage 계정 참여자* 역할을 사용하여 클래식 Storage 계정을 관리하는 방법을 고려해 볼 수 있습니다. VM 및 저장소 계정을 관리해야 하는 클라우드 운영자의 경우 *Virtual Machine 참여자* 역할에 계정을 추가하는 방법을 고려해 보세요.

RBAC와 같은 기능을 활용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 이로 인해 사용자가 처음에는 없어야 했던 특정 유형의 데이터(예: 높은 비즈니스 영향)에 액세스할 수 있도록 함으로써 데이터 손상을 초래할 수 있습니다.

Azure RBAC에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md) 문서를 참조하세요.

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Resource Manager를 사용하여 리소스가 만들어지는 위치 제어

클라우드 운영자가 조직의 리소스를 관리하는 데 필요한 규칙을 위반하지 않으면서도 작업을 수행할 수 있도록 하는 것이 중요합니다. 리소스가 만들어지는 위치를 제어하고자 하는 조직은 이러한 위치를 하드 코딩해야 합니다.

이 목적을 달성하기 위해 조직이 작업을 설명하는 정의 또는 거부된 리소스가 포함된 보안 정책을 만들 수 있습니다. 구독, 리소스 그룹 또는 개별 리소스와 같이 원하는 범위에서 해당 정책 정의를 할당합니다.

> [!NOTE]
> 이는 RBAC와 다르며 실제로 RBAC를 활용하여 해당 리소스를 만들 권한을 가지고 있는 사용자를 인증합니다.
>
>

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 활용하여 조직이 해당 비용 센터가 연결될 때에만 작업을 허용하고 그렇지 않으면 요청을 거부하도록 할 시나리오에 대한 사용자 지정 정책을 만들 수도 있습니다.

리소스를 만드는 방법을제어하지 않는 조직은 사용자가 필요 이상으로 많은 리소스를 만들어 서비스를 남용할 수 있는 상황에 직면할 가능성이 더 높습니다. 리소스 만들기 프로세스의 강화는 멀티 테넌트 시나리오를 보호하기 위한 중요한 단계입니다.

Azure Resource Manager를 사용하여 정책 만들기에 대한 자세한 내용은 [Azure Policy란?](../azure-policy/azure-policy-introduction.md) 문서를 참조하세요.

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>SaaS 앱에 ID 기능을 활용하도록 개발자 안내

사용자 ID는 사용자가 온-프레미스 또는 클라우드 디렉터리와 통합할 수 있는 [SaaS 앱](https://azure.microsoft.com/marketplace/active-directory/all/)에 액세스하는 많은 시나리오에 활용됩니다. 무엇보다도 개발자가 [SDL(Microsoft Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)과 같은 안전한 방법을 사용하여 이러한 앱을 개발하는 것이 좋습니다. Azure AD는 Identity-as-a-service를 제공하며 [OAuth 2.0](http://oauth.net/2/) 및 [OpenID Connect](http://openid.net/connect/) 등의 업계 표준 프로토콜뿐만 아니라 신속하게 여러 플랫폼용 오픈 소스 라이브러리를 지원하여 개발자의 인증 작업을 간소화합니다.

Azure AD에 대한 인증을 아웃소싱하는 응용 프로그램을 등록하도록 하려면 이는 필수 절차입니다. 이 절차를 수행하는 이유는 Azure AD가 SSO(Single Sine On)을 처리하거나 토큰을 교환할 때 응용 프로그램과의 통신을 조정해야 하기 때문입니다. Azure AD에서 발급된 토큰의 수명이 만료되면 사용자의 세션이 만료됩니다. 언제나 응용 프로그램이 이 시간을 사용해야 하는지 또는 이 시간을 줄일 수 있는지 여부를 평가합니다. 수명 주기 감소는 비활성 기간을 기준으로 사용자를 로그아웃시키는 보안 대책 역할을 할 수 있습니다.

앱 액세스에 ID 제어를 적용하지 않고 앱을 ID 관리 시스템과 안전하게 통합하는 방법을 개발자에게 안내하지 않는 조직은 [OWASP(Open Web Application Security Project) Top 10에 기술된 약한 인증 및 세션 관리](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet)와 같은 기밀 도난 유형의 공격에 더 취약할 수 있습니다.

SaaS 앱의 인증 시나리오에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](../active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

## <a name="actively-monitor-for-suspicious-activities"></a>의심스러운 활동을 적극적으로 모니터링

[Verizon 2016 데이터 위반 보고서](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/)에 따르면, 자격 증명 손상은 여전히 증가 추세에 있으며 수익성이 매우 좋은 사이버 범죄 비즈니스 중 하나가 되고 있습니다. 이 때문에 의심스러운 행동을 빨리 탐지하고 추가 조사 경고를 트리거할 수 있는 활성 ID 모니터링 시스템을 갖추고 있는 것이 중요합니다. Azure AD에는 조직에서 자신의 ID를 모니터링할 수 있도록 하는 두 가지 주요 기능: Azure AD Premium [비정상 보고서](../active-directory/active-directory-view-access-usage-reports.md) 및 Azure AD [ID 보호](../active-directory/active-directory-identityprotection.md) 기능이 있습니다.

비정상 보고서를 사용하여 [흔적 없는](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md) 로그인 시도, 특정 계정에 대한 [무차별 암호 대입](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) 공격, 여러 위치에서 로그인 시도, 감염된 장치에서 로그인 및 의심스러운 IP 주소를 확실히 식별합니다. 이는 보고서임을 염두에 두십시오. 즉, IT 관리자가 매일 또는 필요 시(대개 사고 대응 시나리오에서) 이러한 보고서를 실행하도록 하는 프로세스 및 절차를 시행해야 합니다.

반면에 Azure AD ID 보호는 실제로 사용하는 모니터링 시스템이며 현재 위험을 자체의 고유 대시보드에 표시합니다. 뿐만 아니라 전자 메일을 통해 일일 요약 알림을 받습니다. 비즈니스 요구 사항에 따라 위험 수준을 조정하는 것이 좋습니다. 위험 이벤트에 대한 위험 수준은 위험 이벤트의 심각도를 표시(높음, 보통 또는 낮음)합니다. 위험 수준은 ID 보호 사용자가 해당 조직에 대한 위험을 줄이기 위해 취해야 하는 작업의 우선 순위를 지정하도록 합니다.

자신의 ID 시스템을 적극적으로 모니터링하지 않는 조직은 사용자 자격 증명이 손상될 위험에 직면합니다. 이러한 자격 증명을 사용하여 의심스러운 활동이 일어나고 있다는 것을 알아야 이 유형의 위협을 완화시킬 수 있습니다.
Azure ID 보호에 대한 자세한 내용은 [Azure Active Directory ID 보호](../active-directory/active-directory-identityprotection.md)를 참조하세요.
