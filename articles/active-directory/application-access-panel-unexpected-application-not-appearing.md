---
title: "할당한 응용 프로그램이 액세스 패널에 표시되지 않음 | Microsoft Docs"
description: "응용 프로그램이 액세스 패널에 표시되지 않는 문제 해결"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 5cb8b600f77c5b7dae91204126e64ec7b9a861ae
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>할당한 응용 프로그램이 액세스 패널에 표시되지 않음

액세스 패널은 웹 기반 포털로 Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자를 통해 액세스 권한을 부여 받은 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 합니다. 이러한 응용 프로그램은 Azure AD 포털에서 사용자를 대신하여 구성됩니다. 응용 프로그램을 액세스 패널에 표시하려면 응용 프로그램이 올바르게 구성되어 있고, 사용자나 그 사용자가 속한 그룹에 할당되어 있어야 합니다.

사용자가 볼 수 있는 앱의 종류는 다음과 같은 범주로 분류됩니다.

-   Office 365 응용 프로그램

-   페더레이션 기반 SSO로 구성된 Microsoft 및 타사 응용 프로그램

-   암호 기반 SSO 응용 프로그램

-   기존 SSO 솔루션을 사용한 응용 프로그램

## <a name="general-issues-to-check-first"></a>먼저 확인해야 할 일반적인 문제

-   사용자에게 방금 응용 프로그램이 추가되었으면 몇 분 후에 사용자의 액세스 패널에 로그인했다가 다시 로그아웃하고 응용 프로그램이 추가되었는지 봅니다.

-   라이선스가 사용자 또는 사용자가 구성원인 그룹에서 제거된 경우 변경 사항이 만들어질 그룹의 크기 및 복잡성에 따라 시간이 오래 걸릴 수 있습니다. 액세스 패널에 로그인하기 전에 잠시 여유 시간을 둡니다.

## <a name="problems-related-to-application-configuration"></a>응용 프로그램 구성에 관련된 문제

응용 프로그램이 올바르게 구성되지 않아서 사용자의 액세스 패널에 표시되지 않을 수도 있습니다. 다음은 응용 프로그램 구성과 관련된 문제를 해결할 수 있는 몇 가지 방법입니다.

-   [Azure AD 갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [비갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Azure AD 갤러리 응용 프로그램에 대해 암호 Single Sign-On 응용 프로그램을 구성하는 방법](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [비갤러리 응용 프로그램에 대해 암호 Single Sign-On 응용 프로그램을 구성하는 방법](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD 갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법

Azure AD 갤러리에서 Enterprise Single Sign-On 기능을 사용하도록 설정된 모든 응용 프로그램은 단계별 자습서를 사용할 수 있습니다. [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)에 액세스하면 자세한 단계별 지침을 볼 수 있습니다.

Azure AD 갤러리에서 응용 프로그램을 구성하려면 다음을 수행해야 합니다.

-   [Azure AD 갤러리에서 응용 프로그램 추가](#add-an-application-from-the-azure-ad-gallery)

-   [Azure AD에서 응용 프로그램의 메타데이터 값 구성(로그온 URL, 식별자, 회신 URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD 메타데이터 및 인증서 검색](#download-the-azure-ad-metadata-or-certificate)

-   [응용 프로그램에서 Azure AD 메타데이터 값 구성(로그온 URL, 발급자, 로그아웃 URL 및 인증서)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 응용 프로그램 추가

Azure AD 갤러리에서 응용 프로그램을 추가하려면 아래 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **갤러리에서 추가** 섹션의 **이름 입력** 텍스트 상자에 응용 프로그램 이름을 입력합니다.

7.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

8.  응용 프로그램을 추가하기 전에 **이름** 텍스트 상자에서 이름을 변경할 수 있습니다.

9.  **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

짧은 시간 후에 응용 프로그램의 구성 창을 볼 수 있습니다.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 응용 프로그램의 Single Sign-On 구성

응용 프로그램에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **모드** 드롭다운에서 **SAML 기반 로그온**을 선택합니다.

9.  **도메인 및 URL**에 필요한 값을 입력합니다. 이러한 값은 응용 프로그램 공급업체에서 받아야 합니다.

   1. 응용 프로그램을 SP에서 시작한 SSO로 구성하려면 로그온 URL 값이 필요합니다. 일부 응용 프로그램의 경우는 식별자도 필수 값입니다.

   2. 응용 프로그램을 IdP에서 시작한 SSO로 구성하려면 회신 URL 값이 필요합니다. 일부 응용 프로그램의 경우는 식별자도 필수 값입니다.

10. **선택 사항:** 선택적인 값을 보려면 **고급 URL 설정 표시**를 클릭합니다.

11. **사용자 특성**의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

12. **선택 사항:** 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

13. 응용 프로그램의 Single Sign-On을 구성하는 방법에 관한 문서에 액세스하려면 **&lt;응용 프로그램 이름&gt; 구성**을 클릭합니다. 또한 응용 프로그램에 SSO를 설정하는 데 필요한 메타데이터 URL 및 인증서가 있습니다.

14. 구성을 저장하려면 **저장**을 클릭합니다.

15. 응용 프로그램에 사용자를 할당합니다.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가

사용자 식별자를 선택하거나 사용자 특성을 추가하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **사용자 특성** 섹션 아래의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다. 사용자를 인증하려면 선택한 옵션이 응용 프로그램의 예상 값과 일치해야 합니다.

   >[!NOTE] 
   >Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 응용 프로그램이 요청한 형식을 기반으로 NameID 특성(사용자 ID)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.
   >
   >

9.  사용자 특성을 추가하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭하여 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD 메타데이터 또는 인증서 다운로드

Azure AD에서 응용 프로그램 메타데이터 또는 인증서를 다운로드하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션으로 이동한 다음 **다운로드** 열 값을 클릭합니다. Single Sign-On을 구성해야 할 응용 프로그램에 따라 메타데이터 XML이나 인증서를 다운로드하는 옵션이 표시됩니다.

    Azure AD에서는 메타데이터를 가져오는 URL을 제공하지 않습니다. 메타데이터는 XML 파일로만 검색할 수 있습니다.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법

비갤러리 응용 프로그램을 구성하려면 Azure AD 프리미엄이 있어야 하며 응용 프로그램에서 SAML 2.0을 지원해야 합니다. Azure AD 버전에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

-   [Azure AD에서 응용 프로그램의 메타데이터 값 구성(로그온 URL, 식별자, 회신 URL)](#configuring-single-sign-on)

-   [사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD 메타데이터 및 인증서 검색](#download-the-azure-ad-metadata-or-certificate)

-   [응용 프로그램에서 Azure AD 메타데이터 값 구성(로그온 URL, 발급자, 로그아웃 URL 및 인증서)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Azure AD에서 응용 프로그램의 메타데이터 값 구성(로그온 URL, 식별자, 회신 URL)

Azure AD 갤러리에 없는 응용 프로그램에 대해 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **고유한 응용 프로그램 추가** 섹션에서 **비갤러리 응용 프로그램**을 클릭합니다.

7.  **이름** 텍스트 상자에 응용 프로그램 이름을 입력합니다.

8.  **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

9.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

10. **모드** 드롭다운에서 **SAML 기반 로그온**을 선택합니다.

11. **도메인 및 URL**에 필요한 값을 입력합니다. 이러한 값은 응용 프로그램 공급업체에서 받아야 합니다.

   1. 응용 프로그램을 IdP에서 시작한 SSO로 구성하려면 회신 URL과 식별자를 입력합니다.

   2.  **선택 사항:** 응용 프로그램을 SP에서 시작한 SSO로 구성하려면 로그온 URL 값이 필요합니다.

12. **사용자 특성**의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

13. **선택 사항:** 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

14. 응용 프로그램의 Single Sign-On을 구성하는 방법에 관한 문서에 액세스하려면 **&lt;응용 프로그램 이름&gt; 구성**을 클릭합니다. 또한 응용 프로그램에 필요한 Azure AD URL과 인증서가 있습니다.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가

사용자 식별자를 선택하거나 사용자 특성을 추가하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **사용자 특성** 섹션 아래의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다. 사용자를 인증하려면 선택한 옵션이 응용 프로그램의 예상 값과 일치해야 합니다.

   >[!NOTE] 
   >Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 응용 프로그램이 요청한 형식을 기반으로 NameID 특성(사용자 ID)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.
   >
   >

9.  사용자 특성을 추가하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭하여 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD 메타데이터 또는 인증서 다운로드

Azure AD에서 응용 프로그램 메타데이터 또는 인증서를 다운로드하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션으로 이동한 다음 **다운로드** 열 값을 클릭합니다. Single Sign-On을 구성해야 할 응용 프로그램에 따라 메타데이터 XML이나 인증서를 다운로드하는 옵션이 표시됩니다.

Azure AD에서는 메타데이터를 가져오는 URL을 제공하지 않습니다. 메타데이터는 XML 파일로만 검색할 수 있습니다.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD 갤러리 응용 프로그램에 대해 암호 Single Sign-On을 구성하는 방법

Azure AD 갤러리에서 응용 프로그램을 구성하려면 다음을 수행해야 합니다.

-   [Azure AD 갤러리에서 응용 프로그램 추가](#add-an-application-from-the-azure-ad-gallery)

-   [암호 Single Sign-On에 대한 응용 프로그램 구성](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 응용 프로그램 추가

Azure AD 갤러리에서 응용 프로그램을 추가하려면 아래 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **갤러리에서 추가** 섹션의 **이름 입력** 텍스트 상자에 응용 프로그램 이름을 입력합니다.

7.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

8.  응용 프로그램을 추가하기 전에 **이름** 텍스트 상자에서 이름을 변경할 수 있습니다.

9.  **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

잠시 후에 응용 프로그램의 구성 창을 볼 수 있습니다.

#### <a name="configure-the-application-for-password-single-sign-on"></a>암호 Single Sign-On에 대한 응용 프로그램 구성

응용 프로그램에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  [응용 프로그램에 사용자를 할당합니다](#how-to-assign-a-user-to-an-application-directly).

10. 또한 사용자의 행을 선택하고 **자격 증명 업데이트**를 클릭하고 사용자를 대신하여 사용자 이름 및 암호를 입력하여 사용자를 대신하여 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자는 시작할 때 자격 증명을 입력하라는 메시지를 받게 됩니다.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 암호 Single Sign-On을 구성하는 방법

Azure AD 갤러리에서 응용 프로그램을 구성하려면 다음을 수행해야 합니다.

-   [비갤러리 응용 프로그램 추가](#add-a-non-gallery-application)

-   [암호 Single Sign-On에 대한 응용 프로그램 구성](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>비갤러리 응용 프로그램 추가

Azure AD 갤러리에서 응용 프로그램을 추가하려면 아래 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **비갤러리 응용 프로그램**을 클릭합니다.

7.  **이름** 텍스트 상자에 응용 프로그램의 이름을 입력합니다. **추가**를 선택합니다.

짧은 시간 후에 응용 프로그램의 구성 창을 볼 수 있습니다.

#### <a name="configure-the-application-for-password-single-sign-on"></a>암호 Single Sign-On에 대한 응용 프로그램 구성

응용 프로그램에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

    1.  여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  **로그온 URL**을 입력합니다. 사용자가 로그인하기 위해 사용자 이름과 암호를 입력하는 URL입니다. URL에서 로그인 필드가 표시되는지 확인합니다.

10. [응용 프로그램에 사용자를 할당합니다](#how-to-assign-a-user-to-an-application-directly).

11. 또한 사용자의 행을 선택하고 **자격 증명 업데이트**를 클릭하고 사용자를 대신하여 사용자 이름 및 암호를 입력하여 사용자를 대신하여 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자는 시작할 때 자격 증명을 입력하라는 메시지를 받게 됩니다.

## <a name="problems-related-to-assigning-applications-to-users"></a>사용자의 응용 프로그램 할당과 관련된 문제

사용자에게 응용 프로그램이 할당되지 않아 액세스 패널에서 응용 프로그램이 표시되지 않을 수도 있습니다. 다음은 확인할 몇 가지 방법입니다.

-   [사용자가 응용 프로그램에 할당되었는지 확인](#check-if-a-user-is-assigned-to-the-application)

-   [응용 프로그램에 사용자를 직접 할당하는 방법](#how-to-assign-a-user-to-an-application-directly)

-   [사용자에게 응용 프로그램과 관련된 라이선스가 할당되었는지 확인](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [사용자에게 라이선스를 할당하는 방법](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>사용자가 응용 프로그램에 할당되었는지 확인

사용자가 응용 프로그램에 할당되었는지 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

6.  문제의 응용 프로그램 이름을 **검색**합니다.

7.  **사용자 및 그룹**을 클릭합니다.

8.  사용자가 응용 프로그램에 할당되었는지 확인합니다.

   * 할당되지 않았으면 “응용 프로그램에 사용자를 직접 할당하는 방법”에 있는 단계를 수행하여 할당합니다.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>응용 프로그램에 사용자를 직접 할당하는 방법

응용 프로그램에 하나 이상의 사용자를 직접 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  목록에서 사용자를 할당하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

8.  **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭하여 **할당 추가** 창을 엽니다.

9.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 클릭합니다.

10. **이름 또는 전자 메일 주소로 검색** 검색 상자에 할당하려는 사용자의 **전체 이름** 또는 **전자 메일 주소**를 입력합니다.

11. 목록의 **사용자** 위로 마우스를 이동하여 **확인란**을 표시합니다. 사용자의 프로필 사진이나 로고 옆의 확인란을 클릭하여 사용자를 **선택됨** 목록에 추가합니다.

12. **선택 사항:** **둘 이상의 사용자를 추가**하려는 경우 **이름 또는 전자 메일 주소로 검색** 검색 상자에 다른 **전체 이름** 또는 **전자 메일 주소**를 입력하고 확인란을 클릭하여 이 사용자를 **선택됨** 목록에 추가합니다.

13. 사용자 선택이 완료되면 **선택** 단추를 클릭하여 응용 프로그램에 할당되도록 사용자 및 그룹의 목록에 추가합니다.

14. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭하여 선택한 사용자에게 할당할 역할을 선택합니다.

15. **할당** 단추를 클릭하여 선택한 사용자에게 응용 프로그램을 할당합니다.

짧은 시간 후에 선택한 사용자는 액세스 패널에서 이러한 응용 프로그램을 시작할 수 있습니다.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>사용자에게 응용 프로그램과 관련된 라이센스가 있는지 확인

사용자의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 사용자가 현재 할당된 라이선스를 봅니다.

  * 사용자가 Office 라이선스에 할당된 경우 사용자의 액세스 패널에 나타나도록 자사 Office 응용 프로그램을 활성화합니다.

### <a name="how-to-assign-a-user-a-license"></a>사용자에게 라이선스를 할당하는 방법 

사용자에게 라이선스를 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 사용자가 현재 할당된 라이선스를 봅니다.

8.  **할당** 단추를 클릭합니다.

9.  사용 가능한 제품 목록에서 **하나 이상의 제품**을 선택합니다.

10. **선택 사항** 제품을 상세하게 할당하려면 **할당 옵션** 항목을 클릭합니다. 완료되면 **확인**을 클릭합니다.

11. **할당** 단추를 클릭하여 이러한 라이선스를 이 사용자에게 할당합니다.

## <a name="problems-related-to-assigning-applications-to-groups"></a>그룹의 응용 프로그램 할당과 관련된 문제

사용자는 응용 프로그램이 할당된 그룹에 속해 있으므로 액세스 패널에 응용 프로그램이 표시될 수 있습니다. 다음은 확인할 몇 가지 방법입니다.

-   [사용자의 그룹 멤버 자격 확인](#check-a-users-group-memberships)

-   [그룹에 응용 프로그램을 직접 할당하는 방법](#how-to-assign-an-application-to-a-group-directly)

-   [사용자가 라이선스에 할당된 그룹에 속해 있는지 확인](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [그룹에 라이선스를 할당하는 방법](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>사용자의 그룹 멤버 자격 확인

그룹의 멤버 자격을 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **그룹**을 클릭합니다.

8.  사용자가 응용 프로그램에 할당된 그룹에 속하는지 확인합니다.

  * 그룹에서 사용자를 제거하려는 경우 그룹의 **행을 클릭**하고 삭제를 선택합니다.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>그룹에 응용 프로그램을 직접 할당하는 방법

응용 프로그램에 하나 이상의 그룹을 직접 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  목록에서 사용자를 할당하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

8.  **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭하여 **할당 추가** 창을 엽니다.

9.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 클릭합니다.

10. **이름 또는 메일 주소로 검색** 검색 상자에 할당하려는 그룹의 **전체 그룹 이름**을 입력합니다.

11. 목록의 **그룹** 위로 마우스를 이동하여 **확인란**을 표시합니다. 그룹의 프로필 사진이나 로고 옆의 확인란을 클릭하여 사용자를 **선택됨** 목록에 추가합니다.

12. **선택 사항:** **둘 이상의 그룹을 추가**하려는 경우 **이름 또는 메일 주소로 검색** 검색 상자에 다른 **전체 그룹 이름**을 입력하고 확인란을 클릭하여 이 그룹을 **선택됨** 목록에 추가합니다.

13. 그룹 선택이 완료되면 **선택** 단추를 클릭하여 응용 프로그램에 할당되도록 사용자 및 그룹의 목록에 추가합니다.

14. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭하여 선택한 그룹에 할당할 역할을 선택합니다.

15. **할당** 단추를 클릭하여 선택한 그룹에 응용 프로그램을 할당합니다.

짧은 시간 후에 선택한 사용자는 액세스 패널에서 이러한 응용 프로그램을 시작할 수 있습니다.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>사용자가 라이선스에 할당된 그룹에 속해 있는지 확인

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **그룹**을 클릭합니다.

8.  특정 그룹의 행을 클릭합니다.

9.  **라이선스**를 클릭하여 그룹이 할당된 라이선스를 봅니다.

   * 그룹이 Office 라이선스에 할당된 경우 사용자의 액세스 패널에 나타나도록 특정 자사 Office 응용 프로그램을 활성화할 수 있습니다.

### <a name="how-to-assign-a-license-to-a-group"></a>그룹에 라이선스를 할당하는 방법

그룹에 라이선스를 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 그룹**을 클릭합니다.

6.  관심이 있는 그룹을 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 그룹이 현재 할당된 라이선스를 봅니다.

8.  **할당** 단추를 클릭합니다.

9.  사용 가능한 제품 목록에서 **하나 이상의 제품**을 선택합니다.

10. **선택 사항** 제품을 상세하게 할당하려면 **할당 옵션** 항목을 클릭합니다. 완료되면 **확인**을 클릭합니다.

11. **할당** 단추를 클릭하여 이러한 라이선스를 이 그룹에 할당합니다. 그룹의 크기와 복잡성에 따라 시간이 오래 걸릴 수 있습니다.

>[!NOTE]
>시간을 단축하려면 일시적으로 라이선스를 사용자에게 직접 할당할 수도 있습니다. 
>
>

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에 새 사용자 추가](active-directory-users-create-azure-portal.md)

