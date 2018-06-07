---
title: '자습서: Adobe Creative Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Adobe Creative Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 808debcee11eb83f7e6afa1f3443a35c9f6767c4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>자습서: Adobe Creative Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Creative Cloud를 통합하는 방법에 대해 알아봅니다.

Adobe Creative Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 Adobe Creative Cloud에 액세스할 수 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Adobe Creative Cloud에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Adobe Creative Cloud와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Adobe Creative Cloud Single Sign-On을 사용하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Adobe Creative Cloud 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>갤러리에서 Adobe Creative Cloud 추가
Adobe Creative Cloud가 Azure AD에 통합되도록 구성하려면 Adobe Creative Cloud를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adobe Creative Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **Adobe Creative Cloud**를 입력하고, 결과 패널에서 **Adobe Creative Cloud**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Adobe Creative Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Adobe Creative Cloud 사용자가 누구인지 알아야 합니다. 즉, Azure AD 사용자와 Adobe Creative Cloud의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Adobe Creative Cloud에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Adobe Creative Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Adobe Creative Cloud 테스트 사용자 만들기](#create-an-adobe-creative-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 해당 사용자를 Adobe Creative Cloud에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Adobe Creative Cloud 응용 프로그램에서 Single Sign-On을 구성합니다.

**Adobe Creative Cloud에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Adobe Creative Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. IDP 시작 모드에서 응용 프로그램을 구성하려면 **Adobe Creative Cloud 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Adobe Creative Cloud 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. **식별자** 텍스트 상자에서 `https://www.okta.com/saml2/service-provider/<token>` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Adobe Creative Cloud 클라이언트 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 문의하세요. 

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Adobe Creative Cloud 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    **로그인 URL** 텍스트 상자에서 값으로 `https://adobe.com`를 입력합니다.

5. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램의 **사용자 특성** 탭에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | LastName |user.surname |
    | Email |user.mail |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.
    
8. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. **Adobe Creative Cloud 구성** 섹션에서 **Adobe Creative Cloud 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Adobe Creative Cloud 구성](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. 다른 웹 브라우저 창에서 [Adobe 관리 콘솔](https://adminconsole.adobe.com)에 관리자 권한으로 로그인합니다.

11. 위쪽 탐색 모음에서 **설정**으로 이동하고 **ID**를 선택합니다. 도메인 목록을 엽니다. 도메인에 대한 **구성** 링크를 클릭합니다. 그런 다음 **Single Sign On 구성 필요** 섹션에서 다음 단계를 수행합니다. 자세한 내용은 [도메인 설정](https://helpx.adobe.com/enterprise/using/set-up-domain.html)을 참조하세요.

    ![설정](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "설정")
    
    a. **찾아보기**를 클릭하여 Azure AD에서 다운로드한 인증서를 **IDP 인증서**에 업로드합니다.
    
    나. **IDP 발급자** 텍스트 상자에 Azure Portal의 **로그온 구성** 섹션에서 복사한 **SAML 엔터티 ID**의 값을 입력합니다.
    
    다. **IDP 로그인 URL** 텍스트 상자에 Azure Portal의 **로그온 구성** 섹션에서 복사한 **SAML SSO 서비스 URL**의 값을 입력합니다.
    
    d. **HTTP - 리디렉션**을 **IDP 바인딩**으로 선택합니다.
    
    e. **전자 메일 주소**를 **사용자 로그인 설정**으로 선택합니다.
    
    f. **저장** 단추를 클릭합니다.

12. 이제 대시보드에 XML **"메타데이터 다운로드"** 파일이 표시됩니다. 여기에는 Adobe의 EntityDescriptor URL과 AssertionConsumerService URL이 포함되어 있습니다. Azure AD 응용 프로그램에서 파일을 열어서 구성하십시오.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **앱 설정 구성** 대화 상자에서 **식별자**에 대해 Adobe가 제공한 EntityDescriptor 값을 사용합니다.

    나. **앱 설정 구성** 대화 상자에서 **회신 URL**에 대해 Adobe가 제공한 AssertionConsumerService 값을 사용합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud 테스트 사용자 만들기

Azure AD 사용자가 Adobe Creative Cloud에 로그인할 수 있도록 하려면 Adobe Creative Cloud에 프로비전되어야 합니다. Adobe Creative Cloud의 경우 프로비전이 수동 작업입니다.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. [Adobe 관리 콘솔](https://adminconsole.adobe.com) 사이트에 관리자 권한으로 로그인합니다.

2. Adobe의 콘솔에서 사용자를 페더레이션 ID로 추가하고 제품 프로필에 할당합니다. 사용자를 추가하는 방법에 대한 자세한 내용은 [Adobe 관리 콘솔에서 사용자 추가](https://helpx.adobe.com/enterprise/using/users.html#Addusers)를 참조하세요. 

3. 이 시점에서 이메일 주소/UPN을 Adobe 로그인 양식에 입력하고 Tab 키를 눌러 다음과 같이 Azure AD에 다시 페더레이션되어야 합니다.
    * 웹 액세스: www.adobe.com > 로그인
    * 바탕 화면 앱 유틸리티 내에서 > 로그인
    * 응용 프로그램 내에서 > 도움말 > 로그인

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adobe Creative Cloud에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Adobe Creative Cloud에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Adobe Creative Cloud**를 선택합니다.

    ![응용 프로그램 목록의 Adobe Creative Cloud 링크](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adobe Creative Cloud 타일을 클릭하면 Adobe Creative Cloud 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [도메인 설정(adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Adobe SSO에서 사용할 Azure 구성(adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

