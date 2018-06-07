---
title: Azure Active Directory를 사용하기 위해 응용 프로그램 등록 | Microsoft Docs
description: IT 전문가를 위해 작성된 이 문서는 Active Directory와 Azure 응용 프로그램 통합에 대한 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: kgremban
ms.custom: seohack1
ms.openlocfilehash: 66102836b954bf4fafc4379cf573658d89e0d409
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Azure Active Directory용 기간 업무 앱 개발
이 가이드는 Azure Active Directory(AD)에 대한 LoB(기간 업무) 응용 프로그램 개발의 개요를 제공하며, Active Directory/Office 365 전역 관리자용으로 작성되었습니다.

## <a name="overview"></a>개요
Azure AD와 통합된 응용 프로그램을 구축하면 조직의 사용자에게 Office 365를 사용하여 Single Sign-On을 제공합니다. Azure AD에 응용 프로그램이 있다면 응용 프로그램에 대한 인증 정책을 통한 제어를 제공합니다. 조건부 액세스 및 Multi-Factor Authentication(MFA)을 사용하여 앱을 보호하는 방법에 대한 자세한 내용은 [액세스 규칙 구성](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

Azure Active Directory를 사용하기 위해 응용 프로그램을 등록해야 합니다. 응용 프로그램을 등록하면 개발자가 Azure AD를 사용하여 사용자를 인증하고 전자 메일, 일정, 문서 등과 같은 사용자 리소스에 대한 액세스를 요청할 수 있습니다.

디렉터리(게스트 아님)의 멤버는 응용 프로그램을 등록할 수 있습니다.( *응용 프로그램 개체 만들기*라고 함)

응용 프로그램을 등록하면 사용자가 다음을 수행할 수 있습니다.

* Azure AD가 인식하는 응용 프로그램에 ID 가져오기
* 응용 프로그램에서 사용할 수 있는 하나 이상의 암호/키를 가져와서 AD에 자신을 인증
* Azure 포털에서 사용자 지정 이름, 로고 등으로 응용 프로그램 브랜딩
* 다음을 포함하여 앱에 대한 Azure AD 권한 부여 기능 적용

  * 역할 기반 Access Control(RBAC)
  * OAuth 권한 부여 서버인 Azure Active Directory(응용 프로그램에서 노출된 API 보호)
* 응용 프로그램에 예상 대로 작동하는 데 필요한 다음을 포함하는 사용 권한을 선언합니다.

      - 앱 사용 권한(전역 관리자만 해당) 예: 다른 Azure AD 응용 프로그램에서 역할 멤버 자격 또는 Azure 리소스, 리소스 그룹 또는 구독에 상대적인 역할 멤버 자격
      - 위임된 권한(모든 사용자). 예: Azure AD, 로그인 및 프로필 읽기

> [!NOTE]
> 기본적으로 모든 멤버는 응용 프로그램을 등록할 수 있습니다. 특정 멤버에 응용 프로그램 등록에 대한 사용 권한을 제한하는 방법을 알아보려면 [Azure AD에 응용 프로그램을 추가하는 방법](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)을 참조하세요.
>
>

다음은 개발자가 자신의 응용 프로그램을 생산할 준비를 돕기 위해 전역 관리자가 수행해야 하는 사항입니다.

* 액세스 규칙 구성(액세스 정책/MFA)
* 앱을 구성하여 사용자 할당 요구 및 사용자 할당
* 기본 사용자 동의 환경 무시

## <a name="configure-access-rules"></a>액세스 규칙 구성
SaaS 앱에 응용 프로그램별 액세스 규칙을 구성합니다. 예를 들어 MFA를 요구하거나 신뢰할 수 있는 네트워크의 사용자에 대한 액세스만 허용할 수 있습니다. 이에 대한 세부 정보는 [액세스 규칙 구성](active-directory-conditional-access-azure-portal-get-started.md)문서에서 사용할 수 있습니다.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>앱을 구성하여 사용자 할당 요구 및 사용자 할당
기본적으로 사용자는 할당되지 않아도 응용 프로그램에 액세스할 수 있습니다. 그러나 응용 프로그램이 역할을 노출하거나 또는 응용 프로그램을 사용자의 액세스 패널에 표시하려는 경우 사용자 할당이 필요합니다.

[사용자 할당 요구](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Azure AD Premium 또는 Enterprise Mobility Suite(EMS) 구독자인 경우 그룹을 사용하는 것이 좋습니다. 응용 프로그램에 그룹을 할당하면 그룹의 소유자에게 지속적인 액세스 관리를 위임할 수 있습니다. 그룹을 만들거나 조직에서 책임 파티를 요청하여 그룹 관리 기능을 사용하여 그룹을 만들 수 있습니다.

[응용 프로그램에 사용자 지정](active-directory-applications-guiding-developers-assigning-users.md)  
[응용 프로그램에 그룹 지정](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>사용자 동의 무시
기본적으로 각 사용자는 로그인하기 위해 동의 환경을 거칩니다. 사용자에게 응용 프로그램에 대한 사용 권한을 부여하도록 요청하는 동의 환경은 그런 결정을 내리는 데 익숙하지 않은 사용자에게 혼란을 줄 수 있습니다.

신뢰할 수 있는 응용 프로그램의 경우 조직을 대신하여 응용 프로그램에 동의하여 사용자 환경을 간소화할 수 있습니다.

Azure에서 동의 및 동의 환경에 대한 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](active-directory-integrating-applications.md)을 참조하세요.

## <a name="related-articles"></a>관련 문서
* [Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 응용 프로그램에 대한 보안 원격 액세스 사용](manage-apps/application-proxy.md)
* [Azure Conditional Access Preview for SaaS Apps](active-directory-conditional-access-azure-portal-get-started.md)
* [Azure AD를 사용하는 앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
