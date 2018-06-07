---
title: '자습서: Confluence SAML SSO by Microsoft와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Confluence SAML SSO by Microsoft 사이에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 856dca46c12862bf59fc7642f627b55733ff5931
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>자습서: Confluence SAML SSO by Microsoft와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Confluence SAML SSO by Microsoft를 통합하는 방법에 대해 알아봅니다.

Azure AD와 Confluence SAML SSO by Microsoft를 통합하면 다음과 같은 이점이 있습니다.

- Confluence SAML SSO by Microsoft에 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Confluence SAML SSO by Microsoft에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="description"></a>설명:

Atlassian Confluence 서버와 함께 Microsoft Azure Active Directory 계정을 사용하여 Single Sign-On을 사용하도록 설정합니다. 이러한 방식으로 모든 조직 사용자는 Azure AD 자격 증명을 사용하여 Confluence 응용 프로그램에 로그인할 수 있습니다. 이 플러그 인은 페더레이션에 대해 SAML 2.0을 사용합니다.

## <a name="prerequisites"></a>필수 조건

Confluence SAML SSO by Microsoft와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Confluence 서버 응용 프로그램이 Windows 64비트 서버(온-프레미스 또는 클라우드 IaaS 인프라)에 설치되어 있어야 합니다.
- Confluence 서버에서 HTTPS를 사용해야 합니다.
- 지원되는 Confluence 플러그 인 버전은 아래 섹션에 설명되어 있습니다.
- Confluence 서버가 인터넷에 연결되어 있고 인증을 위해 특히 Azure AD 로그인 페이지에 접속되고 Azure AD에서 토큰을 받을 수 있어야 합니다.
- Confluence에 관리자 자격 증명이 설정되어 있어야 합니다.
- Confluence에서 WebSudo를 사용하지 않아야 합니다.
- Confluence 서버 응용 프로그램에서 생성된 테스트 사용자

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 Confluence의 프로덕션 환경을 사용하는 것은 권장되지 않습니다. 응용 프로그램 개발 또는 스테이징 환경에서 통합을 먼저 테스트 한 다음 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="supported-versions-of-confluence"></a>지원되는 Confluence 버전 

현재 기준으로 다음 버전의 Confluence가 지원됩니다.

- Confluence: 5.0 ~ 5.10

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Confluence SAML SSO by Microsoft 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>갤러리에서 Confluence SAML SSO by Microsoft 추가
Confluence SAML SSO by Microsoft가 Azure AD에 통합되도록 구성하려면 갤러리에서 Confluence SAML SSO by Microsoft를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Confluence SAML SSO by Microsoft를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Confluence SAML SSO by Microsoft**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. 결과 패널에서 **Confluence SAML SSO by Microsoft**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Confluence SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD의 사용자에 해당하는 Confluence SAML SSO by Microsoft의 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Confluence SAML SSO by Microsoft의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Confluence SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Confluence SAML SSO by Microsoft 테스트 사용자 만들기](#creating-a-confluence-saml-sso-by-microsoft-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 Confluence SAML SSO by Microsoft에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Confluence SAML SSO by Microsoft 응용 프로그램에서 Single Sign-On을 구성합니다.

**Confluence SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Confluence SAML SSO by Microsoft** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. **Confluence SAML SSO by Microsoft 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<domain:port>/plugins/servlet/saml/auth`

    나. **식별자** 텍스트 상자에서 `https://<domain:port>/` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 명명된 URL인 경우 포트는 선택 사항입니다. 이러한 값은 Confluence 플러그 인 구성 중에 수신되며 자습서의 뒷부분에 설명되어 있습니다.

4. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. 다른 웹 브라우저 창에서 Confluence 인스턴스에 관리자로 로그인합니다.

7. 마우스로 선 위를 가리키고 **추가 기능**을 클릭합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=56503)에서 플러그 인을 다운로드합니다. **업로드 추가 기능** 메뉴를 사용하여 Microsoft에서 제공하는 플러그 인을 수동으로 업로드합니다. 플러그 인 다운로드에는 [Microsoft 서비스 계약](https://www.microsoft.com/en-us/servicesagreement/)이 적용됩니다. 
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. 플러그 인이 설치되면 **추가 기능 관리** 섹션의 **사용자가 설치한** 추가 기능 섹션에 표시됩니다. **구성**을 클릭하여 새 플러그 인을 구성합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. 구성 페이지에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > 메타데이터를 확인하는 데 오류가 없도록 앱에 매핑된 인증서가 하나만 있는지 확인합니다. 인증서가 여러 개 있으면 메타데이터를 확인할 때 관리자에게 오류가 표시됩니다.

    a. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣고 **해결** 단추를 클릭합니다. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

    나. **식별자, 회신 URL 및 로그인 URL** 값을 복사하여 Azure Portal에서 **Confluence SAML SSO by Microsoft 도메인 및 URL** 섹션의 **식별자, 회신 URL 및 로그인 URL** 텍스트 상자에 각각 붙여넣습니다.

    다. **Login Button Name**(로그인 단추 이름)에 조직이 사용자의 로그인 화면에 표시하려는 단추의 이름을 입력합니다.

    d. **SAML 사용자 ID 위치**에서 **사용자 ID는 Subject 문의 NameIdentifier 요소에 있습니다.** 또는 **사용자 ID는 Attribute 요소에 있습니다.** 를 선택합니다.  이 ID는 Confluence 사용자 ID여야 합니다. 사용자 ID가 일치하지 않으면 시스템에서 사용자 로그인을 허용하지 않습니다. 

    > [!Note]
    > 기본 SAML 사용자 ID 위치는 이름 식별자입니다. 이것을 특성 옵션으로 변경하고 적절한 특성 이름을 입력할 수 있습니다.
    
    e. **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.) 옵션을 선택하는 경우 **특성 이름** 텍스트 상자에 사용자 ID가 필요한 특성의 이름을 입력합니다. 

    f. Azure AD에서 페더레이션된 도메인(예: ADFS 등)을 사용하는 경우 **Enable Home Realm Discovery**(홈 영역 검색 사용) 옵션을 클릭하고 **도메인 이름**을 구성합니다.
    
    g. **도메인 이름**에 ADFS 기반 로그인인 경우 여기에 도메인 이름을 입력합니다.

    h. 사용자가 Confluence에서 로그아웃할 때 Azure AD에서 로그아웃하려면 **Enable Single Sign out**(Single Sign-Out 사용)을 선택합니다. 

    i. **저장** 단추를 클릭하여 설정을 저장합니다.

    > [!NOTE]
    > 설치 및 문제 해결에 대한 자세한 내용은 [MS Confluence SSO 커넥터 관리자 가이드](ms-confluence-jira-plugin-adminguide.md)를 참조하세요. [FAQ](ms-confluence-jira-plugin-faq.md)도 도움이 될 것입니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft 테스트 사용자 만들기

Azure AD 사용자가 Confluence 온-프레미스 서버에 로그인할 수 있게 하려면 사용자를 Confluence SAML SSO by Microsoft에 프로비전해야 합니다. Confluence SAML SSO by Microsoft의 경우 프로비전이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Confluence 온-프레미스 서버에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **사용자 관리**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. [사용자] 섹션에서 **사용자 추가** 탭을 클릭합니다. **사용자 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. **사용자 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 이메일 주소를 입력합니다.

    나. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    다. **전자 메일** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에서 Britta Simon에 대한 암호를 입력합니다.

    e. **암호 확인**을 클릭하여 암호를 다시 입력합니다.
    
    f. **추가** 단추를 클릭합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Confluence SAML SSO by Microsoft에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Confluence SAML SSO by Microsoft에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Confluence SAML SSO by Microsoft**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Confluence SAML SSO by Microsoft 타일을 클릭하면 Confluence SAML SSO by Microsoft 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png
