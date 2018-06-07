---
title: '자습서: SAML SSO for Jira by resolution GmbH와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAML SSO for Jira by resolution GmbH 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: b0835d89786e2d389b845c8a4bdbbfb63c0bbcd3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>자습서: SAML SSO for Jira by resolution GmbH와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAML SSO for Jira by resolution GmbH를 통합하는 방법에 대해 알아봅니다.

SAML SSO for Jira by resolution GmbH와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- SAML SSO for Jira by resolution GmbH에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAML SSO for Jira by resolution GmbH에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAML SSO for Jira by resolution GmbH와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SAML SSO for Jira by resolution GmbH Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAML SSO for Jira by resolution GmbH 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>갤러리에서 SAML SSO for Jira by resolution GmbH 추가
Azure AD에 SAML SSO for Jira by resolution GmbH를 통합하도록 구성하려면 갤러리에서 관리되는 SaaS 앱 목록으로 SAML SSO for Jira by resolution GmbH를 추가해야 합니다.

**갤러리에서 SAML SSO for Jira by resolution GmbH를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **SAML SSO for Jira by resolution GmbH**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. 결과 창에서 **SAML SSO for Jira by resolution GmbH**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 SAML SSO for Jira by resolution GmbH에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAML SSO for Jira by resolution GmbH 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 SAML SSO for Jira by resolution GmbH의 관련 사용자 간의 링크 관계가 설정되어야 합니다.

SAML SSO for Jira by resolution GmbH에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

SAML SSO for Jira by resolution GmbH에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[SAML SSO for Jira by resolution GmbH 테스트 사용자 만들기](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAML SSO for Jira by resolution GmbH에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAML SSO for Jira by resolution GmbH 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAML SSO for Jira by resolution GmbH에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SAML SSO for Jira by resolution GmbH** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **SAML SSO for Jira by resolution GmbH 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<server-base-url>/plugins/servlet/samlsso`

4. **고급 URL 설정 표시**를 선택합니다. **SP** 시작 모드에서 응용 프로그램을 구성하려면:

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [SAML SSO for Jira by resolution GmbH 지원 팀](https://www.resolution.de/go/support)에 문의하세요. 

5. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. 다른 웹 브라우저 창에서 **SAML SSO for Jira by resolution GmbH 관리 포털**에 관리자로 로그인합니다.

8. 마우스로 선 위를 가리키고 **추가 기능**을 클릭합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. [관리자 액세스] 페이지로 리디렉션됩니다. **암호**를 입력하고 **확인** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. [추가 기능] 탭 섹션에서 **새 추가 기능 찾기**를 클릭합니다. **SAML Single Sign On(SSO) for JIRA**를 검색하고 **설치** 단추를 클릭하여 새 SAML 플러그 인을 설치합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. 플러그 인 설치가 시작됩니다. **닫기**를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. **관리**를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. **구성**을 클릭하여 새 플러그 인을 구성합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. **SAML SingleSignOn 플러그인 구성** 페이지에서 **새 IdP 추가** 단추를 클릭하여 ID 공급자의 설정을 구성합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. **SAML ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon5a.png)
 
    a. **Azure AD**를 IdP 유형으로 설정합니다.
    
    나. ID 공급자의 **이름**(예: Azure AD)을 추가합니다.
    
    다. ID 공급자의 **설명**(예: Azure AD)을 추가합니다.
    
    d. **다음**을 클릭합니다.
    
16. **ID 공급자 구성** 페이지에서 **다음** 단추를클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon5b.png)

17. **SAML IdP 메타데이터 가져오기** 페이지에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon5c.png)

    a. **파일 로드** 단추를 클릭하고 5단계에서 다운로드한 메타데이터 XML 파일을 선택합니다.

    나. **가져오기** 단추를 클릭합니다.
    
    다. 가져오기가 완료될 때까지 잠시 대기합니다.
    
    d. **다음** 단추를 클릭합니다.
    
18. **사용자 ID 특성 및 변환** 페이지에서 **다음** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon5d.png)
    
19. **사용자 만들기 및 업데이트** 페이지에서 **저장 및 다음**을 클릭하여 설정을 저장합니다.   
    
    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon6a.png)
    
20. **설정 테스트** 페이지에서 **테스트 건너뛰기 및 수동으로 구성**을 클릭하여 사용자 테스트를 한 번 건너뜁니다. 다음 섹션에서 수행되며 Azure Portal에서 몇 가지 설명이 필요합니다. 
    
    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon6b.png)
    
21. **테스트 건너뛰기의 의미...**  내용이 담긴 대화 상자가 표시되면 **확인**을 클릭합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/addon6c.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO for Jira by resolution GmbH 테스트 사용자 만들기

Azure AD 사용자가 SAML SSO for Jira by resolution GmbH에 로그인할 수 있게 하려면 해당 사용자를 SAML SSO for Jira by resolution GmbH에 프로비전해야 합니다.  
SAML SSO for Jira by resolution GmbH에서 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. SAML SSO for Jira by resolution GmbH 회사 사이트에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **사용자 관리**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. 리디렉션된 [관리자 액세스] 페이지에서 **암호**를 입력하고 **확인** 단추를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. **사용자 관리** 탭 섹션 아래에서  **사용자 만들기**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. **이메일 주소** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    나. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    다. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.

    e. **사용자 만들기**를 클릭합니다.   

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAML SSO for Jira by resolution GmbH에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 SAML SSO for Jira by resolution GmbH에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAML SSO for Jira by resolution GmbH**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 [SAML SSO for Jira by resolution GmbH] 타일을 클릭하면 SAML SSO for Jira by resolution GmbH 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [Azure 시작](active-directory-saas-access-panel-introduction.md)을 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png

