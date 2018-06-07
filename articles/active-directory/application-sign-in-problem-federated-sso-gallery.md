---
title: 페더레이션된 Single Sign-On에 대해 구성된 갤러리 응용 프로그램에 로그인하는 문제 | Microsoft Docs
description: Azure AD에서 SAML 기반 페더레이션된 Single Sign-On에 대해 구성된 응용 프로그램에 로그인하는 경우 특정 문제에 대한 지침
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
ms.openlocfilehash: fa1387c3eb11899bb74418b2ff1af4a5d22a73d9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>페더레이션된 Single Sign-On에 대해 구성된 갤러리 응용 프로그램에 로그인하는 문제

문제를 해결하려면 다음과 같이 Azure AD에서 응용 프로그램 구성을 확인해야 합니다.

-   Azure AD 갤러리 응용 프로그램의 모든 구성 단계를 수행했습니다.

-   Azure AD에 구성된 식별자 및 회신 URL은 응용 프로그램에 있는 예상 값과 일치합니다.

-   응용 프로그램에 사용자를 할당했습니다.

## <a name="application-not-found-in-directory"></a>응용 프로그램을 디렉터리에서 찾을 수 없습니다

*오류 AADSTS70001: ‘https://contoso.com’ 식별자를 가진 응용 프로그램이 디렉터리에 없습니다*.

**가능한 원인**

SAML 요청의 응용 프로그램에서 Azure AD로 보내진 발급자 특성은 Azure AD 응용 프로그램에서 구성된 식별자 값과 일치하지 않습니다.

**해결 방법**

SAML 요청의 발급자 특성이 Azure AD에서 구성된 식별자 값과 일치하는지 확인합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **도메인 및 URL** 섹션으로 이동합니다. 식별자 텍스트 상자의 값이 오류 메시지에 표시되는 식별자 값과 일치하는지 확인합니다.

Azure AD에서 식별자 값을 업데이트하고 SAML 요청에서 응용 프로그램에 의해 일치하는 값이 전송된 후에 응용 프로그램에 로그인해야 합니다.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>회신 주소가 응용 프로그램에 대해 구성된 회신 주소와 일치하지 않습니다.

*오류 AADSTS50011: 회신 주소 ‘https://contoso.com’이 응용 프로그램에 대해 구성된 회신 주소와 일치하지 않습니다.*

**가능한 원인**

SAML 요청에서 AssertionConsumerServiceURL 값은 회신 URL 값 또는 Azure AD에 구성된 패턴과 일치하지 않습니다. SAML 요청에서 AssertionConsumerServiceURL 값은 오류에서 확인할 수 있는 URL입니다.

**해결 방법**

SAML 요청에서 AssertionConsumerServiceURL 값이 Azure AD에 구성된 회신 URL 값과 일치하는지 확인합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **도메인 및 URL** 섹션으로 이동합니다. SAML 요청에 있는 AssertionConsumerServiceURL 값과 일치하도록 회신 URL 텍스트 상자의 값을 확인하고 업데이트합니다.  
    * 회신 URL 텍스트 상자에 표시되지 않으면 **고급 URL 설정 표시** 확인란을 선택합니다.

Azure AD에서 회신 URL 값을 업데이트하고 SAML 요청에서 응용 프로그램에 의해 일치하는 값이 전송된 후에 응용 프로그램에 로그인해야 합니다.

## <a name="user-not-assigned-a-role"></a>역할이 지정되지 않은 사용자

*오류 AADSTS50105: 로그인한 사용자 'brian@contoso.com'는 응용 프로그램의 역할에 할당되지 않았습니다*.

**가능한 원인**

사용자는 Azure AD에서 응용 프로그램에 대한 액세스 권한을 부여받지 않았습니다.

**해결 방법**

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

## <a name="not-a-valid-saml-request"></a>유효한 SAML 요청이 아님

*오류 AADSTS75005: 요청이 유효한 Saml2 프로토콜 메시지가 아닙니다.*

**가능한 원인**

Azure AD에서는 Single Sign-On의 응용 프로그램에서 보낸 SAML 요청을 지원하지 않습니다. 일반적인 문제는 다음과 같습니다.

-   SAML 요청에서 필수 필드 누락

-   SAML 요청 인코딩 방법

**해결 방법**

1.  SAML 요청을 캡처합니다. [Azure AD에서 SAML 기반 Single Sign-On 응용 프로그램을 디버깅하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) 자습서에 따라 SAML 요청을 캡처하는 방법을 알아봅니다.

2.  응용 프로그램 공급 업체와 공유에 문의하세요.

   -   SAML 요청

   -   [Azure AD Single Sign-On SAML 프로토콜 요구 사항](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Single Sign-On에 Azure AD SAML 구현을 지원하는지 유효성을 검사해야 합니다.

## <a name="no-resource-in-requiredresourceaccess-list"></a>requiredResourceAccess 목록에 리소스 없음

*오류 AADSTS65005: 클라이언트 응용 프로그램은 리소스 '00000002-0000-0000-c000-000000000000'에 대한 액세스 권한을 요청했습니다. 클라이언트가 해당 requiredResourceAccess 목록*에 이 리소스를 지정하지 않았기 때문에 이 요청이 실패했습니다.

**가능한 원인**

응용 프로그램 개체에 문제가 있습니다.

**해결 방법: 옵션 1**

이 문제를 해결하려면 Azure AD 구성에 고유 식별자 값을 추가합니다. 식별자 값을 추가하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성한 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **도메인 및 URL** 섹션 아래에서 **고급 URL 설정 표시**를 확인합니다.

9.  **식별자** 텍스트 상자에 응용 프로그램의 고유 식별자를 입력합니다.

10. 구성을 **저장**합니다.


**해결 방법: 옵션 2**

위의 옵션 1이 작동하지 않으면 디렉터리에서 응용 프로그램을 제거합니다. 그런 다음 응용 프로그램을 추가 및 다시 구성하고 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

  * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램 **개요** 창의 왼쪽 위에서 **삭제**를 클릭합니다.

8.  Azure AD를 새로 고치고 Azure AD 갤러리에서 응용 프로그램을 추가합니다. 그런 다음 응용 프로그램을 구성합니다.

<span id="_Hlk477190176" class="anchor"></span>응용 프로그램을 다시 구성한 후에 응용 프로그램에 로그인할 수 있습니다.

## <a name="certificate-or-key-not-configured"></a>인증서 또는 키가 구성되지 않음

*오류 AADSTS50003: 서명 키가 구성되지 않았습니다.*

**가능한 원인**

응용 프로그램 개체가 손상되어 Azure AD에서 응용 프로그램에 대해 구성된 인증서를 인식하지 못합니다.

**해결 방법**

기존 인증서를 삭제하고 새 인증서를 만들려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

 * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **SAML 서명 인증서** 섹션에서 **새 인증서 만들기**를 클릭합니다.

9.  [만료 날짜]를 선택합니다. 그런 다음 **저장**을 클릭합니다.

10. **새 인증서 활성화**를 선택하여 활성 인증서를 재정의합니다. 그런 다음, 창 맨 위에 있는 **저장**을 클릭하고 롤오버 인증서를 활성화하도록 수락합니다.

11. **SAML 서명 인증서** 섹션에서 **제거**를 클릭하여 **사용하지 않는** 인증서를 제거합니다.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>응용 프로그램에 전송된 SAML 클레임을 사용자 지정할 때 발생하는 문제

응용 프로그램에 전송된 SAML 특성 클레임을 사용자 지정하는 방법을 알아보려면 [Azure Active Directory의 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure AD에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
