---
title: 앱과 Azure AD 통합 시작 | Microsoft Docs
description: 이 문서는 온-프레미스 응용 프로그램 및 클라우드 응용 프로그램과 Azure Active Directory(AD)를 통합하는 시작 가이드입니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ac3438f0c706988323318146965a87461dcce1e8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>응용 프로그램과 Azure Active Directory 통합 시작 가이드
## <a name="overview"></a>개요
이 항목은 Azure AD(Active Directory)와 응용 프로그램을 통합하기 위한 로드맵을 제공하려는 용도로 제공됩니다. 아래의 각 섹션에는 더 자세한 항목의 요약이 포함되므로 이 시작 가이드의 어떤 부분이 사용자와 관련 있는지 식별할 수 있습니다.  각 주제에 대해 자세히 알아보려면 링크를 따라갑니다.

## <a name="before-you-begin-take-inventory"></a>시작 하기 전에 인벤토리를 수행합니다.
응용 프로그램을 Azure AD와 통합하는 것을 알아보기 전에 현재 위치와 목표를 아는 것이 중요합니다.  다음 질문은 Azure AD 응용 프로그램 통합 프로젝트에 대해 생각해볼 수 있도록 하기 위한 용도로 제공됩니다.

### <a name="application-inventory"></a>응용 프로그램 인벤토리
* 응용 프로그램은 모두 어디에 있습니까? 누가 소유합니까?
* 응용 프로그램에 어떤 종류의 인증이 필요합니까?
* 누가 어떤 응용 프로그램에 액세스하려 합니까?
* 새 응용 프로그램을 배포하시겠습니까?
  * 사내에 구축하고 Azure 계산 인스턴스에 배포하시겠습니까?
  * Azure 응용 프로그램 갤러리에서 사용할 수 있는 응용 프로그램을 사용하시겠습니까?

### <a name="user-and-group-inventory"></a>사용자 및 그룹 인벤토리
* 사용자 계정은 어디에 있습니까?
  * 온-프레미스 Active Directory
  * Azure AD
  * 사용자가 소유한 별도 응용 프로그램 데이터베이스 내에서
  * 허용되지 않은 응용 프로그램에서
  * 위의 모든 항목
* 개별 사용자는 현재 어떤 사용 권한 및 역할 할당을 가지고 있습니까? 액세스를 검토해야 하거나 사용자 액세스 및 역할 할당이 적절하다고 생각합니까?
* 그룹은 온-프레미스 Active Directory 내에 만들어 집니까?
  * 그룹을 어떻게 구성합니까?
  * 그룹 멤버는 누구입니까?
  * 그룹은 현재 어떤 사용 권한/역할 할당을 가지고 있습니까?
* 통합하기 전에 사용자/그룹 데이터베이스를 정리해야 합니까?  (매우 중요한 질문입니다. 쓰레기를 넣고 쓰레기를 얻는 현상.)

### <a name="access-management-inventory"></a>액세스 관리 인벤토리
* 응용 프로그램에 대한 사용자 액세스를 현재 어떻게 관리합니까? 변경해야 합니까?  예를 들어 [RBAC](../../role-based-access-control/role-assignments-portal.md) 와 같이 액세스를 관리하는 다른 방법을 고려한 적이 있습니까?
* 누구에게 어떤 액세스가 필요합니까?

아마 이러한 모든 질문에 대한 답변은 없겠지만 괜찮습니다.  이 가이드는 이러한 일부 질문에 대답하고 합리적인 결정을 내릴 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Azure 구독 및 Azure Active Directory입니다.  Azure 구독이 아직 없는 경우 Azure를 30일 동안 무료로 사용해 볼 수 있습니다. [사용해 보세요!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Azure AD와 응용 프로그램 통합
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>클라우드 앱 검색을 사용하여 허용되지 않은 클라우드 응용 프로그램 찾기
위에서 설명한 대로 지금까지 조직에서 관리하지 않은 응용 프로그램이 있을 수 있습니다.  인벤토리 프로세스의 일부로 허용되지 않은 클라우드 응용 프로그램을 찾을 수 있습니다. [클라우드 앱 검색을 사용하여 허용되지 않은 클라우드 응용 프로그램 찾기](cloud-app-discovery.md)를 참조하세요.

### <a name="authentication-types"></a>인증 형식
응용 프로그램에는 각자 다른 인증 요구 사항이 있을 수 있습니다. Azure AD과 함께 인증서 서명은 SAML 2.0, WS-페더레이션 또는 OpenID 연결 프로토콜 뿐만 아니라 암호 Single Sign-on을 사용하는 응용 프로그램을 사용하는 응용 프로그램과 사용될 수 있습니다. Azure AD와 함께 사용할 응용 프로그램 인증 형식에 대한 자세한 내용은 [Azure Active Directory에서 페더레이션된 Single Sign-on에 대한 인증서 관리](../active-directory-sso-certs.md) 및 [암호 기반 Single Sign On](what-is-single-sign-on.md)을 참조하세요.

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Azure AD 앱 프록시를 사용하는 SSO 사용
Microsoft Azure AD 응용 프로그램 프록시를 사용하여 어디서든 어떤 장치에서든 안전하게 개인 네트워크 내부에 위치한 응용 프로그램에 액세스를 제공할 수 있습니다. 환경 내에서 응용 프로그램 프록시 커넥터를 설치한 후에 Azure AD를 이용하여 쉽게 구성될 수 있습니다

### <a name="integrating-applications-with-azure-ad"></a>Azure AD와 응용 프로그램 통합
다음 문서에서는 응용 프로그램을 Azure AD와 통합하는 여러 가지 방법을 설명하고 일부 지침을 제공합니다.

* [사용할 Active Directory 결정](../active-directory-administer.md)
* [Azure 응용 프로그램 갤러리에서 응용 프로그램 사용](what-is-single-sign-on.md)
* [SaaS 응용 프로그램 통합 자습서 목록](../active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>응용 프로그램에 대한 액세스 관리
다음 문서는 응용 프로그램이 Azure AD와 통합되면 Azure AD 커넥터 및 Azure AD를 사용하여 응용 프로그램에 대한 액세스를 관리할 수 있는 방법을 설명합니다.

* [Azure AD를 사용하는 앱에 대한 액세스 관리](../active-directory-managing-access-to-apps.md)
* [Azure AD 커넥터를 사용하여 자동화](../active-directory-saas-app-provisioning.md)
* [응용 프로그램에 사용자 지정](../active-directory-applications-guiding-developers-assigning-users.md)
* [응용 프로그램에 그룹 지정](../active-directory-applications-guiding-developers-assigning-groups.md)
* [계정 공유](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>사용자 지정 응용 프로그램 통합
전원 Azure AD를 활용하여 새 응용 프로그램을 작성하고 개발자를 지원하려면 [개발자 가이드](../active-directory-applications-guiding-developers-for-lob-applications.md)를 참조하세요.

Azure 응용 프로그램 갤러리에 사용자 지정 응용 프로그램을 추가하려는 경우 [Azure AD 셀프 서비스 SAML 구성을 사용하여 "앱 가져오기"](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)를 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)

