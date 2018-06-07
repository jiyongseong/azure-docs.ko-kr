---
title: Azure AD를 사용한 계정 공유 | Microsoft Docs
description: Azure Active Directory를 통해 조직이 온-프레미스 앱과 소비자 클라우드 서비스에 대해 안전하게 계정을 공유할 수 있는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: b97ec4ffacead7630c267284f79f954ef03eff61
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="sharing-accounts-with-azure-ad"></a>Azure AD와 계정 공유
## <a name="overview"></a>개요
경우에 따라 조직에서는 여러 사용자에 단일 사용자 이름 및 암호를 사용해야 합니다. 보통은 다음 두 경우입니다.

* 온-프레미스 앱이나 소비자 클라우드 서비스 등, 각 사용자에 대한 고유의 로그인과 암호가 필요한 응용 프로그램을 액세스할 때(예: 기업 소셜 미디어 계정)
* 다중 사용자 환경을 만들 때. 상승된 권한이 있고 핵심 설정, 관리 및 복구 작업에 사용되는 단일 로컬 계정을 가지고 있습니다. Salesforce의 루트 계정이나 Office 365에 대한 로컬 "전역 관리자" 계정을 예로 들 수 있습니다.

일반적으로 이러한 계정은 자격 증명(사용자 이름/암호)을 적합한 개인에게 배포하거나 신뢰할 수 있는 여러 에이전트가 액세스할 수 있는 공유 위치에 저장하여 공유합니다.

전통적인 공유 모델에는 몇 가지 단점이 있습니다.

* 새 응용 프로그램에 대한 액세스를 활성화하려면 액세스가 필요한 모든 사용자에게 자격 증명을 배포해야 합니다.
* 각 공유 응용 프로그램에는 공유 자격 증명의 고유한 자체 세트가 필요할 수 있으므로 사용자가 여러 자격 증명 세트를 기억해야 합니다. 사용자가 여러 자격 증명을 기억해야 한다면, 편의를 위해 위험한 행동을 할 가능성이 높아집니다 (예: 암호 기록).
* 응용 프로그램에 누가 액세스 권한이 있는지 알 수 없습니다.
* 응용 프로그램에 누가 *액세스했는지* 알 수 없습니다.
* 응용 프로그램에 대한 액세스를 제거하려 할 때 자격 증명을 업데이트하고 다시 해당 응용 프로그램에 액세스가 필요한 모든 사용자에게 배포해야 합니다.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory 계정 공유
Azure AD는 이러한 단점을 제거하는 새로운 방법의 공유 계정 사용을 제공합니다.

Azure AD 관리자는 해당 응용 프로그램에 대해 액세스 패널을 사용하고 가장 적합한 Single Sign On 형식을 선택하여 액세스할 수 있는 응용 프로그램을 구성합니다. 이러한 유형 중 하나인 *암호 기반 Single Sign-on*을 통해 Azure AD는 해당 앱의 로그온 프로세스 중에 일종의 "broker"로 작동합니다.

사용자가 조직 계정으로 한번에 로그인합니다. 정기적으로 데스크톱 또는 이메일에 액세스하는 데 사용하는 것과 동일한 계정입니다. 사용자는 자신에게 할당된 응용 프로그램만 확인하고 액세스할 수 있습니다. 공유 계정을 사용하면 이 응용 프로그램 목록이 수에 관계없이 공유 자격 증명을 포함할 수 있습니다. 최종 사용자는 자신이 사용하는 다양한 계정을 기억하거나 기록해 둘 필요가 없습니다.

공유 계정을 사용하면 관리뿐 아니라 활용성도 높아지며 보안도 향상됩니다. 자격 증명을 사용할 권한이 있는 사용자는 공유 암호를 보는 것이 아니라 조정된 인증 흐름의 일환으로 암호를 사용할 권한을 받는 것입니다. 또한 일부 암호 SSO 응용 프로그램에는 Azure AD를 사용하여 정기적으로 암호를 롤오버(업데이트)하는 옵션이 있습니다. 시스템은 계정 보안을 증대하는 길고 복잡한 암호를 사용합니다. 관리자는 응용 프로그램에 액세스 권한을 쉽게 부여 또는 취소할 수 있으며 누가 계정에 액세스 권한이 있고 누가 과거에 액세스했는지 파악할 수 있습니다.

Azure AD는 모든 유형의 암호 Single Sign-on 응용 프로그램 전체에서EMS(Enterprise Mobility Suite), 프리미엄 또는 기본 라이선스 사용자의 공유 계정을 지원합니다. 응용 프로그램 갤러리에서 수천 개의 사전 통합된 응용 프로그램에서 계정을 공유하고, [사용자 지정 SSO 앱](active-directory-enterprise-apps-manage-sso.md)으로 자체 암호 인증 응용 프로그램을 추가할 수 있습니다.

계정에 공유를 사용하는 Azure AD 기능은 다음과 같습니다.

* [암호 SSO(Single sign-on)](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* 암호 SSO(Single sign-on) 에이전트
* [그룹 할당](active-directory-accessmanagement-self-service-group-management.md)
* 사용자 지정 암호 앱
* [앱 사용 대시보드/보고서](active-directory-passwords-get-insights.md)
* 최종 사용자 액세스 포털
* [앱 프록시](manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>계정 공유
Azure AD를 사용하여 계정을 공유하려면 다음이 필요합니다.

* 응용 프로그램 [앱 갤러리](https://azure.microsoft.com/marketplace/active-directory/)나 [사용자 지정 응용 프로그램](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) 추가
* 암호 SSO(Single Sign-On)에 대한 응용 프로그램 구성
* [그룹 기반 할당](active-directory-accessmanagement-group-saasapps.md)을 사용하고 옵션을 선택하여 공유 자격 증명 입력
* 선택 사항: Facebook, Twitter 또는 LinkedIn 등의 일부 응용 프로그램에서는 [Azure AD 자동 암호 롤오버](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

또한 MFA(Multi-Factor Authentication)로 공유 계정의 보안을 강화하고([Azure AD를 통한 응용 프로그램 보호](authentication/concept-mfa-whichversion.md)에 대한 자세한 정보) [Azure AD 셀프 서비스](active-directory-accessmanagement-self-service-group-management.md) 그룹 관리를 사용하여 응용 프로그램에 대한 액세스 권한이 있는 관리자에게 기능을 위임할 수 있습니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [조건부 액세스를 사용한 앱 보호](active-directory-conditional-access-azure-portal.md)
* [셀프 서비스 그룹 관리/SSAA](active-directory-accessmanagement-self-service-group-management.md)

