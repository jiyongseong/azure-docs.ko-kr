---
title: Azure Active Directory 시작 | Microsoft Docs
description: Azure Active Diretory에서 라이선스 받기, 도메인 이름 추가, 사용자 지정 로그인 페이지 만들기 및 셀프 서비스 암호 재설정 추가
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: fc6cc88cabab1bc56b0a04ce8778823647c5b2bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-azure-ad"></a>Azure AD 시작
인증된 사용자에게만 응용 프로그램 및 서비스의 고가용성을 보장하기 위해 최신 ID 관리에는 확장 가능하고 일관된 안정성이 필요합니다. 사용자의 ID 관리 요구를 적절하게 지원하기 위해 IT 부서에는 승인된 공용 SaaS(software as a service) 앱에 대한 액세스를 제공하는 방법, 내부 기간 업무 앱을 호스트하는 방법 및 온-프레미스 앱 개발 및 사용을 강화하는 방법이 필요합니다. 이러한 요구 사항은 모두 클라우드 기반 ID 관리 솔루션의 필요성을 의미합니다.      

Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD는 핵심 디렉터리 서비스, 고급 ID 통제 및 응용 프로그램 액세스 관리 기능을 모두 제공합니다. Azure AD의 다중 테넌트, 지리적으로 분산된 고가용성 디자인은, 중요한 비즈니스 요구사항이 있을 때 Azure AD를 신뢰할 수 있음을 의미합니다.

Azure AD에는 온-프레미스 리소스 정보, 사용자 지정 가능한 회사 브랜딩, 간단한 라이선스 관리 및 셀프서비스 암호 관리를 동기화할 수 있는 기능을 포함한 ID 관리 기능의 전체 도구 모음이 포함됩니다. 이러한 쉽게 배포할 수 있는 기능을 통해 Azure AD를 시작하여 클라우드 기반 응용 프로그램을 보호하고, IT 프로세스를 간소화하고, 비용을 절감하고, 회사의 규정 준수 목표를 충족할 수 있도록 합니다.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

이 문서의 나머지 부분에서는 Azure AD를 시작하는 방법을 알려줍니다. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Azure Active Directory Premium에 등록
[Azure AD(Azure Active Directory) Premium을 시작](active-directory-get-started-premium.md)하기 위해 라이선스를 구입하고 Azure 구독에 연결할 수 있습니다. 새로운 Azure 구독을 만드는 경우 라이선싱 계획 및 Azure AD 서비스 액세스도 활성화해야 합니다. 

Active Directory Premium에 등록하는 데는 몇 가지 옵션이 있습니다. 

- Azure 또는 Office 365 구독 사용
- Enterprise Mobility + Security 라이선싱 계획 사용
- Microsoft 볼륨 라이선싱 계획 사용

### <a name="verification-step"></a>확인 단계
구독을 활성화한 후에 서비스에 로그인할 수 있는지를 확인합니다.

## <a name="add-a-custom-domain-name"></a>사용자 지정 도메인 이름 추가
모든 Azure AD 디렉터리는 *domainname*.onmicrosoft.com 양식의 초기 도메인 이름으로 제공됩니다. 초기 도메인 이름을 변경하거나 삭제할 수 없지만 [Azure AD에 회사 도메인 이름을 추가](add-custom-domain.md)할 수 있습니다. 예를 들어, 조직에는 비즈니스를 하는 데 사용하는 다른 도메인 이름 및 회사 도메인 이름을 사용하여 로그인하는 사용자가 있을 수 있습니다. Azure AD에 사용자 지정 도메인 이름을 추가하면 사용자에게 ‘alice@contoso.com’ 같은 친숙한 사용자 이름을 디렉터리에 할당할 수 있습니다. 'alice@.onmicrosoft.com' 대신입니다. 프로세스는 간단합니다.

1. 디렉터리에 사용자 지정 도메인 이름 추가
2. 도메인 이름 등록 기관의 도메인 이름에 대한 DNS 항목 추가
3. Azure AD에서 사용자 지정 도메인 이름 확인

### <a name="verification-step"></a>확인 단계
사용자 지정 도메인을 추가한 다음 Azure AD 포털의 **사용자 지정 도메인 이름** 블레이드에 **확인됨** 상태가 표시되는지 확인합니다.

## <a name="add-company-branding-to-your-sign-in-page"></a>로그인 페이지에 회사 브랜딩 추가 
혼동을 피하기 위해 대부분의 회사는 관리하는 모든 웹 사이트 및 서비스에 일관된 모양과 느낌을 적용하고자 합니다. Azure AD(Active Directory)는 [회사 로고 및 사용자 지정 색 구성표를 포함하도록 로그인 페이지의 외관을 사용자 지정](customize-branding.md)하는 방식으로 이 기능을 제공합니다. 로그인 페이지는 Office 365 또는 Azure AD를 ID 공급자로 사용하는 기타 웹 기반 응용 프로그램에 로그인할 경우에 표시되는 페이지입니다. 자격 증명을 입력하려면 이 페이지와 상호 작용합니다.

### <a name="verification-step"></a>확인 단계
Azure Portal에 로그인하고 사용자 지정된 로그인 페이지 및 추가 언어 사용자 지정이 올바르게 구성되었는지 확인합니다. 

## <a name="add-new-users"></a>새 사용자 추가
Azure Portal을 사용하거나 온-프레미스 Windows Server AD 리소스 데이터를 동기화하여 한 번에 하나씩 [조직의 Azure AD에 새 사용자를 추가](add-users-azure-active-directory.md)할 수 있습니다. Azure AD 포털에서 직접 클라우드 기반 사용자를 추가하거나 온-프레미스 사용자 정보를 동기화할 수 있습니다.

Azure AD에 온-프레미스 ID 동기화를 사용하려면 조직의 서버에 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)를 설치하고 구성해야 합니다. 이 응용 프로그램은 사용자 및 그룹을 기존 ID 원본에서 Azure AD 테넌트로 동기화합니다.

### <a name="verification-step"></a>확인 단계
새 사용자를 만들거나 동기화한 후에 Azure AD에 표시되는지 확인합니다.

## <a name="assign-licenses"></a>라이선스 할당
유료 기능을 구성하기 위해서는 구독을 얻기만 하면 되지만, Azure AD Premium 유료 기능에 대한 [사용자 라이선스를 할당](license-users-groups.md)해야 합니다. 액세스 권한이 있어야 하거나 Azure AD 유료 기능을 통해 관리되는 모든 사용자에게 라이선스가 할당되어야 합니다. 라이선스 할당은 Azure AD Premium, Basic 또는 Enterprise Mobility + Security 등의 구매한 서비스와 사용자 간의 매핑입니다.

그룹 기반 라이선스 할당을 사용하여 다음 예제와 같은 규칙을 설정할 수 있습니다.

- 디렉터리의 모든 사용자가 자동으로 라이선스를 얻습니다.
- 해당하는 직위를 가진 모든 사용자가 라이선스를 얻습니다.
- 셀프 서비스 그룹을 사용하여 조직의 다른 관리자에게 의사 결정을 위임할 수 있습니다.

### <a name="verification-step"></a>확인 단계
**Azure Active Directory** > **라이선스** > **모든 제품** 아래에서 사용할 수 있도록 할당된 라이선스를 검토합니다.

## <a name="configure-self-service-password-reset"></a>셀프 서비스 암호 재설정 구성
IT 관리자는 [SSPR(셀프 서비스 암호 재설정)](authentication/quickstart-sspr.md)을 사용하여 사용자에게 해당 암호 또는 계정을 재설정하거나 잠금 해제할 수 있도록 할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템을 사용하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

### <a name="verification-step"></a>확인 단계
**Azure Active Directory** > **암호 재설정** 아래에서 사용된 SSPR 속성을 검토하여 적절한 사용자 및 그룹 할당이 이루어졌는지 확인합니다. 


## <a name="next-steps"></a>다음 단계
[Azure Active Directory 서비스 페이지](https://azure.microsoft.com/services/active-directory/).

[Azure Active Directory 가격 책정 정보 페이지](https://azure.microsoft.com/pricing/details/active-directory/)