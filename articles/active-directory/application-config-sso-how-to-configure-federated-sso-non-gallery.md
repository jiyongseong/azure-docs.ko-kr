---
title: 비갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법 | Microsoft Docs
description: Azure AD와 통합할 사용자 지정 비갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법
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
ms.openlocfilehash: 65df07b6fca62476953ad98dc9e17d9a82c97a53
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 페더레이션된 Single Sign-On을 구성하는 방법

비갤러리 응용 프로그램에 대해 *코드를 작성하지 않고* Single Sign-On을 구성하려면 Azure AD Premium 구독이 있어야 하며 응용 프로그램이 SAML 2.0을 지원해야 합니다. Azure AD 버전에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="overview-of-steps-required"></a>필요한 단계 개요
다음은 비갤러리(예: 사용자 지정) 응용 프로그램에 대해 SAML 2.0을 사용하여 페더레이션된 Single Sign-On을 구성하는 데 필요한 단계를 요약한 개요입니다.

-   [Azure AD에서 응용 프로그램의 메타데이터 값 구성(로그온 URL, 식별자, 회신 URL)](#_Configuring_single_sign-on)

-   [사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD 메타데이터 및 인증서 검색](#download-the-azure-ad-metadata-or-certificate)

-   [응용 프로그램에서 Azure AD 메타데이터 값 구성(로그온 URL, 발급자, 로그아웃 URL 및 인증서)](#_Configuring_single_sign-on)

-   [응용 프로그램에 사용자 할당](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>비갤러리 응용 프로그램에 Single Sign-On 구성

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

   2. **선택 사항:** 응용 프로그램을 SP에서 시작한 SSO로 구성하려면 로그온 URL 값이 필요합니다.

12. **사용자 특성**의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

13. **선택 사항:** 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

14. 응용 프로그램의 Single Sign-On을 구성하는 방법에 관한 문서에 액세스하려면 **&lt;응용 프로그램 이름&gt; 구성**을 클릭합니다. 또한 응용 프로그램에 필요한 Azure AD URL과 인증서가 있습니다.

15. [응용 프로그램에 사용자를 할당합니다.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>사용자 식별자를 선택하고 응용 프로그램에 보낼 사용자 특성 추가

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

 >[!NOTE] Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 응용 프로그램이 요청한 형식을 기반으로 NameID 특성(사용자 식별자)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.
 >
 >

9.  사용자 특성을 추가하려면 **다른 모든 사용자 특성 보기 및 편집**을 클릭하여 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집합니다.

   특성을 추가하려면:

   1. **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   2. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD 메타데이터 또는 인증서 다운로드

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

## <a name="assign-users-to-the-application"></a>응용 프로그램에 사용자 할당

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

잠시 후 선택한 사용자는 솔루션 설명 섹션에 설명된 메서드를 사용하여 이러한 응용 프로그램을 시작할 수 있습니다.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>응용 프로그램에 보낸 SAML 클레임 사용자 지정

응용 프로그램에 전송된 SAML 특성 클레임을 사용자 지정하는 방법을 알아보려면 [Azure Active Directory의 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 사용하여 앱에 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
