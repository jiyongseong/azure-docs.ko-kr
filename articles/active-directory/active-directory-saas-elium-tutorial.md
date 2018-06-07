---
title: '자습서: Elium과 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Elium 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: b64f4153908668117ff07b20701c834ce66d3c46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>자습서: Elium과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Elium을 통합하는 방법에 대해 알아봅니다.

Elium을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Elium에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Elium에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Elium과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Elium Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Elium 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-elium-from-the-gallery"></a>갤러리에서 Elium 추가
Elium과 Azure AD의 통합을 구성하려면 갤러리의 Elium을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Elium을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **Elium**을 입력하고, 결과 패널에서 **Elium**을 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Elium](./media/active-directory-saas-elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 Elium에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Elium 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Elium의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

Elium에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Elium 테스트 사용자 만들기](#create-an-elium-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Elium에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고, Elium 응용 프로그램에서 Single Sign-On을 구성합니다.

**Elium에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Elium** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-elium-tutorial/tutorial_elium_samlbase.png)

3. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **Elium 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Elium 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-elium-tutorial/tutorial_elium_url.png)

    a. **식별자** 텍스트 상자에서 `https://<platform-domain>.elium.com/login/saml2/metadata` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<platform-domain>.elium.com/login/saml2/acs`

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Elium 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-elium-tutorial/tutorial_elium_url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. `https://<platform-domain>.elium.com/login/saml2/metadata`에서 다운로드할 수 있는 **SP 메타데이터 파일**(이 자습서의 뒷부분에서 설명함)에서 이러한 값을 얻을 수 있습니다.

5. Elium 응용 프로그램에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute.png)

6. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이전의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
           
    | 특성 이름 | 특성 값 |   
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|
    
    > [!NOTE]
    > 이러한 특성은 기본 클레임입니다. 하지만 **이메일 클레임만 필요합니다**. 또한 JIT 프로비전의 경우 이메일 클레임만 필수입니다. 다른 사용자 지정 클레임은 고객 플랫폼마다 다를 수 있습니다.

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute_04.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/tutorial_attribute_05.png)

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. 네임스페이스를 공백으로 둡니다.
    
    e. **Ok**를 클릭합니다. 

5. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-elium-tutorial/tutorial_elium_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-elium-tutorial/tutorial_general_400.png)
    
7. 다른 웹 브라우저 창에서 관리자 권한으로 Elium 회사 사이트에 로그인합니다.

8. 오른쪽 위 모서리에서 **사용자 프로필**을 클릭한 다음, **관리**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user1.png)

9. **보안** 탭을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user2.png)

10. **SSO(Single Sign-On)** 섹션까지 아래로 스크롤하여 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user3.png)

    a. **계정에 대해 SAML2 인증이 작동하는지 확인** 값을 복사하여 Azure Portal의 **Elium 도메인 및 URL** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    > [!NOTE]
    > SSO가 구성되면 언제든지 `https://<platform_domain>/login/regular/login` URL에서 기본 원격 로그인 페이지에 액세스할 수 있습니다. 

    나. **SAML2 페더레이션 사용** 확인란을 선택합니다.

    다. **JIT 프로비전** 확인란을 선택합니다.

    d. **다운로드** 단추를 클릭하여 **SP 메타데이터**를 엽니다.

    e. **SP 메타데이터** 파일에서 **entityID**를 검색하고, **entityID** 값을 복사하여 Azure Portal의 **Elium 도메인 및 URL** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user4.png)

    f. **SP 메타데이터** 파일에서 **AssertionConsumerService**를 검색하고, **위치** 값을 복사하여 Azure Portal의 **Elium 도메인 및 URL** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-elium-tutorial/user5.png)

    g. Azure Portal에서 다운로드한 메타데이터 파일을 메모장에 열고, 내용을 복사한 다음, **IdP 메타데이터** 텍스트 상자에 붙여넣습니다.

    h. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-elium-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-elium-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-elium-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-elium-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-elium-test-user"></a>Elium 테스트 사용자 만들기

이 섹션은 Elium에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Elium은 JIT(Just-In-Time) 프로비전을 지원하며, 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 없는 경우 Elium에 액세스하려는 중에 만들어집니다.
>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Elium 지원 팀](mailto:support@elium.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Elium에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Elium에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Elium**을 선택합니다.

    ![응용 프로그램 목록의 Elium 링크](./media/active-directory-saas-elium-tutorial/tutorial_elium_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Elium 타일을 클릭하면 Elium 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-elium-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-elium-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-elium-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-elium-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-elium-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-elium-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-elium-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-elium-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-elium-tutorial/tutorial_general_203.png

