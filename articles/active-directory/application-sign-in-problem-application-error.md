---
title: "로그인한 후 응용 프로그램 페이지의 오류 | Microsoft Docs"
description: "응용 프로그램 자체에서 오류를 내보내는 경우 Azure AD 로그인에서 발생한 문제를 해결하는 방법"
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
ms.openlocfilehash: 02236e7f7ec6be0df5082d2cde3d616e628c3927
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="error-on-an-applications-page-after-signing-in"></a>로그인한 후 응용 프로그램 페이지의 오류

이 시나리오에서는 Azure AD에 로그인한 사용자가 있지만 응용 프로그램에서 사용자가 로그인 흐름을 성공적으로 완료할 수 없는 오류를 표시합니다. 이 시나리오에서 응용 프로그램은 Azure AD에서 발생한 응답을 수락하지 않습니다.

응용 프로그램이 Azure AD의 응답을 수락하지 않은 이유에는 몇 가지가 있습니다. 응용 프로그램의 오류가 응답에서 누락된 내용을 명확하게 파악하지 못한 경우, 다음을 수행합니다.

-   응용 프로그램이 Azure AD 갤러리인 경우 [Azure Active Directory의 응용 프로그램에 SAML 기반 Single Sign-On을 디버깅하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging) 문서에 있는 모든 단계를 수행했는지 확인합니다.

-   [Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 SAML 요청, SAML 응답 및 SAML 토큰을 캡처합니다.

-   응용 프로그램 공급 업체와 함께 SAML 응답을 공유하여 누락된 내용을 파악합니다.

## <a name="missing-attributes-in-the-saml-response"></a>SAML 응답에서 누락된 특성

Azure AD 응답으로 보낼 Azure AD 구성의 특성을 추가하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **사용자 특성** 섹션에서 **다른 모든 사용자 특성 보기 및 편집**을 클릭하여 사용자가 로그인할 때 SAML 토큰을 통해 응용 프로그램으로 보낼 특성을 편집합니다.

   특성을 추가하려면:

   * **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   * **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

9.  구성을 저장합니다.

다음 번에 사용자가 응용 프로그램에 로그인하면 Azure AD는 SAML 응답으로 새 특성을 보냅니다.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>응용 프로그램에 필요한 다른 사용자 식별자 값 또는 형식

SAML 응답이 역할과 같은 특성을 누락하거나 응용 프로그램에 EntityID 특성에 대한 다른 형식이 필요하기 때문에 응용 프로그램에 대한 로그인이 실패합니다.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Azure AD 응용 프로그램 구성에 특성을 추가합니다.

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **사용자 특성**의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

## <a name="change-entityid-user-identifier-format"></a>EntityID(사용자 식별자) 형식 변경

응용 프로그램이 EntityID 특성에 대해 다른 형식을 필요로 하는 경우입니다. 사용자 인증 후에 Azure AD에서 응답을 통해 응용 프로그램으로 보내는 EntityID(사용자 식별자) 형식은 선택할 수 없습니다.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 응용 프로그램이 요청한 형식을 기반으로 NameID 특성(사용자 식별자)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>응용 프로그램은 SAML 응답에 대해 다른 시그니처 메서드를 필요로 합니다.

Azure Active Directory에서 디지털 방식으로 로그인한 SAML 토큰을 변경하려고 합니다. 다음 단계를 수행하세요.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션에서 **고급 인증서 서명 설정 표시**를 클릭합니다.

9.  응용 프로그램에 필요한 적절한 **서명 옵션**을 선택합니다.

  * SAML 응답 서명

  * SAML 응답 및 어설션 서명

  * SAML 어설션 서명

다음 번에 사용자가 응용 프로그램에 로그인하면 Azure AD는 선택한 SAML 응답의 일부를 서명합니다.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>응용 프로그램에 필요한 SHA-1이라는 서명 알고리즘

기본적으로 Azure AD는 대부분의 보안 알고리즘을 사용하여 SAML 토큰을 서명합니다. 응용 프로그램에서 필요한 경우가 아니면 SHA-1로 서명 알고리즘을 변경하는 것을 권장하지 않습니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션에서 **고급 인증서 서명 설정 표시**를 클릭합니다.

9.  **서명 알고리즘**에서 SHA-1을 선택합니다.

다음 번에 사용자가 응용 프로그램에 로그인하면 Azure AD는 SHA-1 알고리즘을 사용하여 SAML 토큰을 서명합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
