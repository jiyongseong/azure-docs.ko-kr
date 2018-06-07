---
title: '자습서: Workplace by Facebook과 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 897ee084e0b36f1729260fabb33114652b82a05d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>자습서: Workplace by Facebook과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workplace by Facebook을 통합하는 방법에 대해 알아봅니다.

Workplace by Facebook과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 Workplace by Facebook에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Workplace by Facebook에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Workplace by Facebook과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

> [!NOTE]
> Facebook에는 Workplace Standard(무료)와 Workplace Premium(유료)의 두 가지 제품이 있습니다. 모든 Workplace Premium 테넌트는 비용 또는 필요한 라이선스에 영향을 미치지 않고 SCIM 및 SSO 통합을 구성할 수 있습니다. SSO 및 SCIM은 Workplace Standard에서 사용할 수 없는 합니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Workplace by Facebook 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>갤러리에서 Workplace by Facebook 추가
Azure AD에 Workplace by Facebook을 통합하도록 구성하려면 갤러리의 Workplace by Facebook을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workplace by Facebook을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **Workplace by Facebook**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. 결과 패널에서 **Workplace by Facebook**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Workplace by Facebook에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD의 사용자에 해당하는 Workplace by Facebook의 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Workplace by Facebook의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

이 링크 관계는 Azure AD의 **사용자 이름** 값을 Workplace by Facebook의 **Username** 값으로 할당하여 설정합니다.

Workplace by Facebook에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[재인증 빈도 구성](#configuring-reauthentication-frequency)** - SAML 확인을 요청하도록 작업 공간을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
4. **[Workplace by Facebook 테스트 사용자 만들기](#creating-a-workplace-by-facebook-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 Workplace by Facebook에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
6. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Workplace by Facebook 응용 프로그램에서 Single Sign-On을 구성합니다.

**Workplace by Facebook에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Workplace by Facebook** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. **Workplace by Facebook 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<instancename>.facebook.com`

    나. **식별자** 텍스트 상자에서 `https://www.facebook.com/company/<instanceID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. Workplace 커뮤니티에 대한 올바른 값을 보려면 Workplace 회사 대시보드의 인증 페이지를 참조하세요. 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. **Workplace by Facebook 구성** 섹션에서 **Workplace by Facebook 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. 다른 웹 브라우저 창에서 Workplace by Facebook 회사 사이트에 관리자로 로그인합니다.
  
   > [!NOTE] 
   > SAML 인증 프로세스의 일환으로 Workplace는 Azure AD에 매개 변수를 전달하기 위해 최대 2.5KB 크기의 쿼리 문자열을 사용할 수 있습니다.

8. **회사 대시보드**에서 **인증** 탭으로 이동합니다.

9. **SAML 인증** 아래 드롭다운 목록에서 **SSO 전용**을 선택합니다.

10. Azure Portal의 **Workplace by Facebook 구성** 섹션에서 복사한 값을 해당 필드에 입력합니다.

    *   **SAML URL** 텍스트 상자에 Azure Portal에서 복사한 **Single Sign-On 서비스 URL** 값을 붙여 넣습니다.
    *   **SAML 발급자 URL 텍스트 상자**에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여 넣습니다.
    *   **SAML 로그아웃 리디렉션**(선택 사항)에 Azure Portal에서 복사한 **로그아웃 URL**을 붙여 넣습니다.
    *   Azure Portal에서 다운로드한 **base-64로 인코딩된 인증서**를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음 **SAML 인증서** 텍스트 상자에 붙여 넣습니다.

11. **SAML 구성** 섹션에 나열된 대상 그룹 URL, 받는 사람 URL 및 ACS(Assertion Consumer Service) URL을 입력해야 할 수 있습니다.

12. 섹션 맨 아래로 스크롤하여 **SSO 테스트** 단추를 클릭합니다. Azure AD 로그인 페이지가 있는 팝업 창이 나타납니다. 일반적인 인증처럼 자격 증명을 입력합니다. 

    **문제 해결:** Azure AD에서 반환되는 이메일 주소가 로그인한 Workplace 계정과 동일한지 확인합니다.

13. 테스트가 성공적으로 완료되면 페이지 하단으로 스크롤하여 **저장** 단추를 클릭합니다.

14. Workplace를 사용하는 모든 사용자에게 이제 인증을 위한 Azure AD 로그인 페이지가 제공됩니다.

15. **SAML 로그아웃 리디렉션(선택 사항)** - 

    Azure AD의 로그아웃 페이지를 가리키는 데 사용할 수 있는 SAML 로그아웃 URL을 선택적으로 구성하도록 선택할 수 있습니다. 이 설정을 사용하도록 설정하고 구성하면 더 이상 사용자가 Workplace 로그아웃 페이지로 이동하지 않습니다. 대신 SAML 로그아웃 리디렉션 설정에 추가된 URL로 사용자가 리디렉션됩니다.

### <a name="configuring-reauthentication-frequency"></a>재인증 빈도 구성

매일, 3일, 1주, 2주, 매달을 주기로 SAML 확인을 요청하거나 SAML 확인을 요청하지 않도록 Workplace를 구성할 수 있습니다.

> [!NOTE] 
>모바일 응용 프로그램에서 SAML 확인에 대한 최소값은 일주일로 설정되어 있습니다.

Require SAML authentication for all users now(모든 사용자에게 SAML 인증 요구) 단추를 사용하여 모든 사용자에 대해 SAML 다시 설정을 강제 적용할 수도 있습니다.


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Workplace by Facebook 테스트 사용자 만들기

이 섹션에서는 Workplace by Facebook에서 Britta Simon이라는 사용자를 만듭니다. Workplace by Facebook은 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다.

이 섹션에는 사용자의 작업이 없습니다. Workplace by Facebook에 사용자가 없는 경우 Workplace by Facebook에 액세스하려고 하면 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Workplace by Facebook 클라이언트 지원 팀](https://workplace.fb.com/faq/)에 문의합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Workplace by Facebook에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Workplace by Facebook에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Workplace by Facebook**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png
