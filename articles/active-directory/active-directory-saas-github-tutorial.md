---
title: '자습서: GitHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ef9d0bb112738e9abea5894fb0f321368b7fc26e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>자습서: GitHub와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GitHub를 통합하는 방법에 대해 알아봅니다.

GitHub를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- GitHub에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 GitHub에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

GitHub와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- GitHub Single Sign-On을 사용하도록 설정된 구독


> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 GitHub 추가
2. Azure AD Single Sign-on 구성 및 테스트


## <a name="adding-github-from-the-gallery"></a>갤러리에서 GitHub 추가
Azure AD에 GitHub 통합을 구성하려면 갤러리의 GitHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GitHub를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **GitHub.com**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. 결과 창에서 **GitHub**를 선택하고 **추가** 단추를 클릭하여 해당 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 GitHub에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 GitHub 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 GitHub의 관련 사용자 간에 연결이 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 GitHub의 **Username** 값으로 할당하여 설정합니다.

GitHub에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[GitHub 테스트 사용자 만들기](#creating-a-GitHub-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GitHub에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 GitHub 응용 프로그램에서 Single Sign-On을 구성합니다.

**GitHub에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **GitHub** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. **GitHub 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. **로그인 URL** 텍스트 상자에서 값으로 `https://github.com/orgs/<entity-id>/sso`를 입력합니다.

    나. **식별자** 텍스트 상자에서 `https://github.com/orgs/<entity-id>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그인 URL 및 식별자로 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. GitHub 관리자 섹션으로 이동하여 이러한 값을 검색합니다. 

4. **사용자 특성** 섹션에서 **사용자 식별자**를 user.mail로 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기**를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. **새 인증서 만들기** 대화 상자에서 달력 아이콘을 클릭하고 **만료 날짜**를 선택합니다. 그런 후 **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. **SAML 서명 인증서** 섹션에서 **새 인증서 활성화**를 선택한 후 **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. 팝업 **롤오버 인증서** 창에서 **확인**을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. **GitHub 구성** 섹션에서 **GitHub 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. 다른 웹 브라우저 창에서 GitHub 회사 사이트에 관리자로 로그인합니다.

12. **설정**으로 이동하고 **보안**을 클릭합니다.

    ![설정](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. **SAML 인증 사용** 상자를 확인하여 Single Sign-On 구성 필드를 표시합니다. 그런 다음 Single Sign-On URL 값을 사용하여 Azure AD 구성에서 Single Sign-On URL을 업데이트합니다.

    ![설정](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. 다음 필드를 구성합니다.

    a. **로그인 URL**: Azure AD의 **GitHub 구성** 섹션에서 **SAML Single Sign-On 서비스 URL**을 입력합니다.

    나. **발급자**: Azure AD의 **GitHub 구성** 섹션에서 **SAML 엔터티 ID**를 입력합니다.

    다. **공용 인증서**: Azure AD에서 다운로드한 인증서를 메모장에서 열고 "BEGIN CERTIFICATE"(인증서 시작) 및 "END CERTIFICATE"(인증서 끝)를 포함한 내용을 복사합니다.

    ![설정](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. **SAML 구성 테스트**를 클릭하여 SSO 동안 유효성 검사 실패 또는 오류가 없는지 확인합니다.

    ![설정](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. 페이지 맨 아래에 있는 **저장**

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 


### <a name="creating-a-github-test-user"></a>GitHub 테스트 사용자 만들기

Azure AD 사용자가 GitHub에 로그인하려면 GitHub에 프로비전되어야 합니다.  
GitHub의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. GitHub 회사 사이트에 관리자 권한으로 로그인합니다.

2. **피플**을 클릭합니다.

    ![사람](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "사람")

3. **멤버 초대**를 클릭합니다.

    ![사용자 초대](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "사용자 초대")

4. **멤버 초대** 대화 상자 페이지에서 다음 단계를 수행합니다.

    a. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![피플 초대](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "피플 초대")
    
    나. **초대 보내기**를 클릭합니다.

    ![피플 초대](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "피플 초대")

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 GitHub에 대한 액세스 권한을 부여하여 Britta Simon이 Azure Single Sign-On을 사용하도록 설정합니다.

![사용자 할당][200] 

**Britta Simon을 GitHub에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **GitHub.com**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    


### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GitHub 타일을 클릭하면 GitHub 응용 프로그램에 로그온됩니다. 조직 계정으로 로그인하지만 개인 계정으로 로그인해야 합니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
