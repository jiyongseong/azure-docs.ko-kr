---
title: 내 응용 프로그램 목록에 예기치 않은 응용 프로그램 | Microsoft Docs
description: 테넌트에서 모든 응용 프로그램을 보는 방법 및 엔터프라이즈 응용 프로그램의 모든 응용 프로그램 목록에 응용 프로그램을 표시하는 방법 이해
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 75fb5fcb98605a080efa2c9232d186c9a81acf2b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="unexpected-application-in-my-applications-list"></a>내 응용 프로그램 목록에 예기치 않은 응용 프로그램

이 문서는 **엔터프라이즈 응용 프로그램**의 **모든 응용 프로그램** 목록에 응용 프로그램을 표시하는 방법을 이해할 수 있도록 돕습니다. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>테넌트에서 모든 응용 프로그램을 보는 방법

테넌트에서 모든 응용 프로그램을 보려면 **필터** 컨트롤을 사용하여 **모든 응용 프로그램** 목록에 **모든 응용 프로그램**을 표시해야 합니다. 다음 단계를 수행하세요.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

6.  **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용하도록 클릭합니다.

7.  **필터** 창에서 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>내 모든 응용 프로그램 목록에 특정 응용 프로그램이 나타나는 이유는 무엇입니까?

**모든 응용 프로그램**으로 필터링한 경우 **모든 응용 프로그램** **목록**은 테넌트의 모든 서비스 주체 개체를 보여 줍니다. 서비스 주체 개체는 다양한 방법으로 이 목록에 나타날 수 있습니다.

1.  다음을 포함하여 응용 프로그램 갤러리에서 모든 응용 프로그램을 추가하는 경우:

   1. **Azure AD 갤러리 응용 프로그램** – Single Sign-On에 대해 Azure AD와 사전 통합된 응용 프로그램입니다.

   2. **응용 프로그램 프록시 응용 프로그램** – 외부로 보안 Single Sign-On을 제공하려는 온-프레미스 환경에서 실행 중인 응용 프로그램입니다.

   3. **사용자 지정 개발된 응용 프로그램** – 조직에서 Azure AD 응용 프로그램 개발 플랫폼에서 개발하고자 하지만 아직 존재하지 않을 수 있는 응용 프로그램입니다.

   4. **비 갤러리 응용 프로그램** – 사용자의 응용 프로그램을 가져옵니다! 원하는 모든 웹 링크 또는 사용자 이름 및 암호 필드를 렌더링하는 모든 응용 프로그램은 SAML 또는 OpenID Connect 프로토콜을 지원하거나 Single Sign-On에 대해 Azure AD와 통합하려는 SCIM을 지원합니다.

2.  Azure Active Directory와 통합된 타<sup>사</sup> 응용 프로그램에 등록하거나 로그인하는 경우. 한 가지 예로 [Smartsheet](https://app.smartsheet.com/b/home) 또는 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)이 있습니다.

3.  [Microsoft Office 365](http://products.office.com/)와 같은 자사 응용 프로그램에 대한 라이선스를 사용자 또는 그룹에 등록 또는 추가하는 경우

4.  [응용 프로그램 레지스트리](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)를 사용하여 사용자 지정 개발된 응용 프로그램을 만들어 새 응용 프로그램 등록을 추가하는 경우

5.  [V2.0 응용 프로그램 레지스트리 포털](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)을 사용하여 사용자 지정 개발된 응용 프로그램을 만들어 새 응용 프로그램 등록을 추가하는 경우

6.  Visual Studio의 [ASP.net 인증 방법](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) 또는 [연결된 서비스](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)를 사용하여 개발 중인 응용 프로그램을 추가하는 경우

7.  [Azure AD PowerShell 모듈](/powershell/azure/install-adv2?view=azureadps-2.0)을 사용하여 서비스 주체 개체를 만드는 경우

8.  테넌트의 데이터를 사용하도록 관리자 권한으로 [응용 프로그램에 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)하는 경우

9.  테넌트의 데이터를 사용하도록 [사용자가 응용 프로그램에 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)하는 경우

10. 테넌트에 데이터를 저장하는 특정 서비스를 활성화하는 경우. 한 가지 예는 암호 재설정 정책을 안전하게 저장하도록 서비스 주체로 모델링되는 암호 재설정입니다.

앱을 디렉터리에 추가하는 방법에 대한 자세한 내용은 [응용 프로그램을 Azure AD에 추가하는 방법 및 이유](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)를 참조하세요.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>응용 프로그램에 대한 특정 사용자 또는 그룹의 할당을 제거하려는 경우

응용 프로그램에 대한 사용자 또는 그룹 할당을 제거하려면 [Azure Active Directory의 엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) 문서에 나열된 단계를 따릅니다.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>모든 사용자에 대해 응용 프로그램에 대한 모든 액세스를 비활성화하려는 경우

응용 프로그램에 대한 모든 사용자 로그인을 비활성화하려면 [Azure Active Directory의 엔터프라이즈 앱에 대한 사용자 로그인 비활성화](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) 문서에 나열된 단계를 따릅니다.

## <a name="i-want-to-delete-an-application-entirely"></a>응용 프로그램을 완전히 삭제하려는 경우

**응용 프로그램을 삭제**하려면 다음 단계를 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  삭제하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 맨 위의 응용 프로그램 **개요** 창에서 **삭제** 아이콘을 클릭합니다.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>모든 응용 프로그램에 대한 모든 이후 사용자 동의 작업을 비활성화하려는 경우

전체 디렉터리에 대한 사용자 동의를 비활성화하면 모든 응용 프로그램에 대한 최종 사용자 동의를 방지합니다. 관리자는 여전히 사용자의 동작에 동의할 수 있습니다. 응용 프로그램 동의 및 동의하거나 동의하지 않을 수 있는 이유에 대해 자세히 알아보려면 [사용자 및 관리자 동의 이해하기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)를 참조하세요.

**전체 디렉터리에서 모든 이후 사용자 동의 작업을 비활성화**하려면 다음 단계를 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **사용자 설정**을 클릭합니다.

6.  **사용자가 앱이 데이터에 액세스하도록 허용할 수 있음** 토글을 **아니요**로 설정하고 **저장** 단추를 클릭하여 모든 이후 사용자 동의 작업을 비활성화합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)
