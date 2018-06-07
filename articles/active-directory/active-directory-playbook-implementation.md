---
title: Azure Active Directory PoC 플레이 북 구현 | Microsoft Docs
description: ID 및 액세스 관리 시나리오를 탐색하고 신속하게 구현
services: active-directory
keywords: Azure Active Directory, 플레이 북, 개념 증명, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 2f01bdee5ffcc9e2f48278c7209da40ef64cc030
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Azure Active Directory 개념 증명 플레이 북: 구현

## <a name="foundation---syncing-ad-to-azure-ad"></a>파운데이션 - AD에서 Azure AD로 동기화 

하이브리드 ID는 이미 온-프레미스 디렉터리가 있는 대부분의 기업 고객에서 기반으로 하고 있습니다. 여기에서의 목표는 실제 ID 및 액세스 시나리오의 가치를 보여주기 위해 의도적으로 최대한 적은 시간을 사용하는 것입니다. 

| 시나리오 | 구성 요소| 
| --- | --- |  
| [온-프레미스 ID를 클라우드로 확장](#extending-your-on-premises-identity-to-the-cloud) | [디렉터리 동기화 - 암호 해시 동기화](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**참고**: Azure AD Connect DirSync/ADSync 또는 이전 버전이 이미 있는 경우 이 단계는 선택 사항입니다. 이 가이드의 일부 시나리오는 최신 버전의 Azure AD Connect가 필요할 수 있습니다.  <br/>[브랜딩](active-directory-playbook-building-blocks.md#branding) | 
| [그룹을 사용하여 Azure AD 라이선스 할당](#assigning-azure-ad-licenses-using-groups) | [그룹 기반 라이선스](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>온-프레미스 ID를 클라우드로 확장 

1. Bob은 Contoso의 Active Directory 관리자입니다. 그는 사용자 집합이 ID를 서비스로 사용할 수 있도록 설정하기 위한 요구 사항을 준비합니다. Azure AD Connect 마법사를 실행한 후 대상 사용자의 ID를 클라우드에서 사용할 수 있습니다. 
2. Bob이 대상 사용자 중 한 명인 Susie에게 Azure Active Directory 액세스 패널에 액세스하여 인증을 할 수 있는지 확인해 달라고 요청합니다. Susie는 브랜드가 지정된 로그인 페이지와 향후 응용 프로그램 액세스를 허용할 준비가 되어 있는 빈 액세스 패널을 확인합니다.

### <a name="assigning-azure-ad-licenses-using-groups"></a>그룹을 사용하여 Azure AD 라이선스 할당 

1. Azure AD 전역 관리자인 Bob은 Azure AD 초기 롤아웃의 일환으로 Azure AD 라이선스를 특정 사용자 집단에 할당하려고 합니다.
2. Bob이 파일럿 사용자를 위한 그룹을 생성합니다. 
3. Bob이 그룹에 라이선스를 할당합니다.
4. 정보 근로자 중 한 명인 Susie는 직무의 일환으로 보안 그룹에 추가됩니다.
5. 일정 시간이 지난 후 Susie는 Azure AD 프리미엄 라이선스에 액세스를 갖습니다. 이로 인해 나중에 더 많은 POC 시나리오가 가능해집니다.

## <a name="theme---lots-of-apps-one-identity"></a>테마 - 다수의 앱, 하나의 ID

| 시나리오 | 구성 요소| 
| --- | --- |  
| [SaaS 응용 프로그램 통합 - 페더레이션된 SSO](#integrate-saas-applications---federated-sso) | [SaaS 페더레이션된 SSO 구성](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[그룹 - 위임된 소유권](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [SaaS 응용 프로그램 통합 - 암호 SSO](#integrate-saas-applications---password-sso) | [SaaS 암호 SSO 구성](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO 및 ID 수명 주기 이벤트](#sso-and-identity-lifecycle-events) | [SaaS 및 ID 수명 주기](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [공유 계정에 대한 액세스 보호](#secure-access-to-shared-accounts) | [SaaS 공유 계정 구성](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [온-프레미스 응용 프로그램에 대한 보안 원격 액세스](#secure-remote-access-to-on-premises-applications) | [앱 프록시 구성](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [LDAP ID를 Azure AD에 동기화](#synchronize-ldap-identities-to-azure-ad) |  [일반 LDAP 커넥터 구성](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>SaaS 응용 프로그램 통합 - 페더레이션된 SSO 

1. Azure AD 전역 관리자인 Bob은 마케팅 부서로부터 해당 부서의 ServiceNow 인스턴스에 대한 액세스를 허용해달라는 요청을 받습니다. Bob은 Azure AD 설명서에서 단계별 자습서를 찾아서 그대로 수행하고 마케팅 팀장인 Kevin에게 사용자를 앱에 할당하는 역할을 위임합니다. 
2. Kevin이 ServiceNow 소유자 자격으로 로그인하여 Susie를 앱에 할당합니다. Kevin은 Susie의 프로필이 ServiceNow에서 자동으로 생성된 것도 확인합니다.
3. Susie는 마케팅 부서의 정보 근로자입니다. Azure AD에 로그인하여 myapps 포털에서 자신이 할당되어 있는 모든 SaaS 응용 프로그램을 확인합니다. 여기서부터 ServiceNow에 원활하게 액세스할 수 있습니다.
4. 마케팅 부서에서 ServiceNow에 누가 액세스했는지 감사하려고 합니다. Bob은 활동 보고서를 다운로드하여 Kevin과 전자 메일을 통해 공유합니다.  

### <a name="sso-and-identity-lifecycle-events"></a>SSO 및 ID 수명 주기 이벤트

1. Susie가 휴가를 떠나면 회사 정책에 따라 온-프레미스 AD 계정이 임시로 비활성화됩니다. 이제 Susie는 Azure AD에 로그인할 수 없기 때문에 ServiceNow에 액세스할 수 없습니다. 
2. Susie가 마케팅 부서에서 영업 부서로 수평 이동합니다. Kevin은 그녀의 액세스 권한을 ServiceNow에서 이동합니다. Susie가 Azure ad myapps에 로그인하면 ServiceNow 타일을 더 이상 볼 수 없습니다. 10분 후 Kevin은 ServiceNow 관리 콘솔에서 계정이 비활성화되었다고 Susie에게 알려줍니다.

### <a name="integrate-saas-applications---password-sso"></a>SaaS 응용 프로그램 통합 - 암호 SSO

1. Bob은 Atlassian HipChat에 대한 액세스를 구성합니다. HipChat에는 암호 SSO 통합이 있고 Susie에게 액세스를 부여합니다.
2. Susie는 myapps 포털에 로그인하여 Azure AD IE 브라우저 확장을 다운로드하는 링크를 찾아서 다운로드합니다.
3. 클릭하면 HipChat 사용자 이름 및 암호 자격 증명을 묻는 메시지가 표시됩니다. 이것은 일회성 작업이며 완료하면 HipChat에 액세스 권한을 갖게 됩니다.
4. 몇 일이 지난 후 Susie가 myapps 포털을 열어서 HipChat을 다시 클릭합니다. 이번에는 원활하게 액세스합니다.
5. HipChat 앱 소유자인 Kevin이 응용 프로그램에 누가 액세스했는지 감사하려고 합니다. Bob이 감사 보고서를 다운로드하여 Kevin과 전자 메일을 통해 공유합니다. 

### <a name="secure-access-to-shared-accounts"></a>공유 계정에 대한 액세스 보호 

1. Bob은 영업팀원의 공유 Twitter 핸들에 대한 보안을 담당합니다. Twitter를 SSO 응용 프로그램으로 추가하여 영업팀의 보안 그룹에 할당합니다. 그가 제공 받은 공유 계정에 대한 자격 증명을 시스템에 제공합니다. 
2. Twitter 자격 증명 공유는 여러 사람이 알고 있기 때문에 더 이상 신뢰할 수 없습니다. Bob이 Twitter 암호에 대한 자동 롤오버를 사용하도록 설정합니다.
3. 영업팀의 일원인 Susie가 myapps 포털에 로그인하면 Azure AD IE 브라우저 확장을 다운로드하는 링크가 표시됩니다. 이것을 설치합니다.
4. 클릭하면 Twitter에 바로 액세스됩니다. 그녀는 암호를 모릅니다.
5. Arnold 역시 영업팀에 속합니다. 그 역시 3-4단계에서 Susie와 같은 경험을 합니다.
6. 영업 부서에서 Twitter에 누가 액세스했는지 감사하려고 합니다. Bob은 활동 보고서를 다운로드하여 Kevin과 전자 메일을 통해 공유합니다. 

### <a name="secure-remote-access-to-on-premises-applications"></a>온-프레미스 응용 프로그램에 대한 보안 원격 액세스

1. Azure AD 전역 관리자인 Bob은 원격으로 작업하는 동안 비용 응용 프로그램과 같은 유용한 몇 가지 온-프레미스 리소스에 직원이 액세스하도록 허용해달라는 요청을 많이 받았습니다. [응용 프로그램 프록시 설명서](manage-apps/application-proxy-enable.md)에 따라 커넥터를 설치하고 Expenses를 응용 프로그램 프록시 응용 프로그램으로 게시합니다. 
2. Bob이 외부 Expenses 응용 프로그램 URL을 원격 액세스가 필요한 직원 중 한 명인 Susie와 공유합니다. 그녀가 링크에 액세스하여 AAD 인증을 거치면 Expenses에 액세스할 수 있고 원격 작업을 하는 동안에도 생산성을 유지할 수 있습니다. 
3. Bob은 계속해서 동일한 절차를 사용하여 온-프레미스 응용 프로그램을 추가로 게시하고 필요한 경우 사용자에게 액세스를 부여합니다. 추가적인 보안을 보장하기 위해서 게시하는 응용 프로그램 중 보다 민감한 응용 프로그램에 대해 조건부 액세스 및 Multi-Factor Authentication을 추가합니다.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>LDAP ID를 Azure AD에 동기화

1. Bob의 회사는 ID 인프라가 복잡합니다. 대부분의 사용자가 Windows Server ADDS(Active Directory Domain Services) 내에서 유지 관리됩니다. 그 중 일부는 ADLDS(Active Directory Lightweight Directory Services) 내 HR 시스템에서 관리됩니다.
2. Bob은 모든 사용자(ADDS에 없는)에게 SaaS 앱에 대한 액세스를 허용하는 일을 담당합니다.
3. Bob은 Azure AD Connect의 ADLDS에서 데이터를 끌어오기 위해 일반 LDAP 커넥터를 구성합니다.
4. Bob은 LDAP 사용자가 메타버스 및 Azure AD에 채워지도록 동기화 규칙을 생성합니다.
5. LDAP 사용자인 Susie가 동기화된 ID를 사용하여 자신의 SaaS에 액세스합니다.



> [!IMPORTANT] 
> 이 작업은 FIM/MIM 사용 경험이 요구되는 고급 구성입니다. 프로덕션에 사용되는 경우 이 구성에 대한 질문이 있으면 [프리미어 지원](https://support.microsoft.com/premier)을 참고하는 것이 좋습니다.



## <a name="theme---increase-your-security"></a>테마 - 보안 향상 

| 시나리오 | 구성 요소| 
| --- | --- |  
| [관리자 계정 액세스 보안](#secure-administrator-account-access) | [전화를 통한 Azure MFA](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [응용 프로그램에 대한 보안 액세스](#secure-access-to-applications) | [SaaS 응용 프로그램에 대한 조건부 액세스](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [JIT(Just-In-Time) 관리를 사용하도록 설정](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [위험 기반 ID 보호](#protect-identities-based-on-risk) | [위험 이벤트 검색](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[로그인 위험 정책 배포](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [인증서 기반 인증을 사용하여 암호 없이 인증](#authenticate-without-passwords-using-certificate-based-authentication) | [인증 기반 인증서 구성](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>관리자 계정 액세스 보안

1. Bob은 Azure AD 전역 관리자입니다. 그가 Stuart를 서비스의 공동 관리자로 식별했습니다. 
2. Bob이 보안 태세를 향상시키기 위해 Stuart의 계정이 MFA를 항상 요구하도록 구성합니다.
3. Stuart는 Azure Portal에 로그인하여 로그인을 계속하려면 자신의 전화 번호를 등록해야 한다는 것을 알게 됩니다.
4. 이제 Stuart의 후속 로그인이 Multi-Factor Authentication으로 보호되면서 ID를 확인하기 위한 전화를 받게 됩니다.

### <a name="secure-access-to-applications"></a>응용 프로그램에 대한 보안 액세스

1. Kevin은 ServiceNow의 비즈니스 소유자입니다. 이제는 회사에서 사용자가 회사 네트워크 외부에서 액세스하는 경우 MFA를 통해 로그인하기를 원합니다.
2. Azure AD 전역 관리자 Bob이 ServiceNow 응용 프로그램에 조건부 액세스 정책을 추가하여 외부 액세스에서 MFA를 사용하도록 설정합니다.
3. 정보 근로자 Susie가 my apps 포털에 로그인하여 ServiceNow 타일을 클릭합니다. 그녀에게 이제 MFA가 요구됩니다.

### <a name="enable-just-in-time-jit-administration"></a>JIT(Just-In-Time) 관리를 사용하도록 설정

1. Bob과 Stuart는 Azure AD 전역 관리자입니다. 이들은 관리 역할에 JIT 액세스를 허용하고 권한 있는 역할을 사용한 기록도 유지하려고 합니다.
2. Bob은 Azure AD 테넌트에서 PIM을 사용하도록 설정하고 보안 관리자가 됩니다. 자신과 Stuart의 전역 관리자 역할 멤버 자격을 영구에서 적격으로 변경합니다.
3. 이제 Bob과 Stuart는 Azure AD 구성을 변경하기 전에 Azure Portal을 통해 자신의 역할을 활성화해야 합니다. 

### <a name="protect-identities-based-on-risk"></a>위험 기반 ID 보호 

1. 정보 근로자 Susie가 Tor 브라우저에서 로그인을 시도합니다. 
2. Bob이 Azure AD ID 보호 대시보드를 확인했더니 Susie가 익명 IP 주소에서 로그인하는 것이 보입니다. 보안 팀은 이렇게 액세스하는 사용자에게 MFA를 요구하려고 합니다.
3. Bob이 중간 또는 높은 위험 이벤트에 대해 MFA를 요구하기 위해 Azure AD ID 보호 정책을 사용하도록 설정합니다.
4. 시간이 지나고 Susie가 Tor 브라우저에서 다시 로그인합니다. 이번에는 그녀에게 MFA 요구가 표시됩니다.

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>인증서 기반 인증을 사용하여 암호 없이 인증

1. Bob은 응용 프로그램에 대한 인증 요소로 암호 사용을 금지하는 금융 기관의 전역 관리자입니다.
2. Bob이 ADFS 및 Azure AD에서 인증서 인증을 강제 적용합니다.
3. Susie가 응용 프로그램에 액세스하면 인증서를 사용하여 인증하라는 메시지가 표시됩니다.

## <a name="theme---scale-with-self-service"></a>테마 - 셀프 서비스에 맞게 크기 조정

| 시나리오 | 구성 요소| 
| --- | --- |  
| [셀프 서비스 암호 재설정](#self-service-password-reset) | [셀프 서비스 암호 재설정](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [응용 프로그램에 대한 셀프 서비스 액세스](#self-service-access-to-applications) | [응용 프로그램에 대한 셀프 서비스 액세스](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>셀프 서비스 암호 재설정 

1. Bob은 Azure AD 전역 관리자이며 Susie를 비롯한 일부 사용자에게 셀프 서비스 암호 관리를 사용하도록 설정합니다. 
2. Susie가 myapps 포털에 로그인하면 향후 암호 재설정 이벤트를 위해 보안 정보를 등록하라는 메시지가 표시됩니다.
3. 며칠이 지나서 Susie가 암호를 잊어버리고 Azure AD 포털을 통해 암호를 재설정합니다.

### <a name="self-service-access-to-applications"></a>응용 프로그램에 대한 셀프 서비스 액세스 

1. Kevin은 ServiceNow의 비즈니스 소유자입니다. 사용자들이 ServiceNow를 모두 한꺼번에 추가하지 않고 필요할 때 “등록”하기를 바랍니다.
2. Azure AD 전역 관리자 Bob이 셀프 서비스 요청을 사용하도록 ServiceNow 응용 프로그램을 수정합니다.
3. 정보 근로자 Susie가 my apps 포털에 로그인하여 "응용 프로그램 추가" 단추를 클릭하면 ServiceNow가 권장되는 응용 프로그램 중 하나로 표시됩니다. 이제 my apps 포털로 돌아가서 ServiceNow 응용 프로그램을 확인합니다.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]