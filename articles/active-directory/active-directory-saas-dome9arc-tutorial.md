---
title: '자습서: Dome9 Arc와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Dome9 Arc 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: de164f6a439fd68b4746dc96c43ff5cb0740ffe8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>자습서: Dome9 Arc와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Dome9 Arc를 통합하는 방법에 대해 알아봅니다.

Dome9 Arc를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Dome9 Arc에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Dome9 Arc에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Dome9 Arc와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Dome9 Arc Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Dome9 Arc 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-dome9-arc-from-the-gallery"></a>갤러리에서 Dome9 Arc 추가
Azure AD에 Dome9 Arc를 통합하도록 구성하려면 갤러리의 Dome9 Arc를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Dome9 Arc를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **Dome9 Arc**를 입력하고, 결과 패널에서 **Dome9 Arc**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 Dome9 Arc에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Dome9 Arc 사용자를 인식하고 있어야 합니다. 즉 Azure AD 사용자와 Dome9 Arc의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Dome9 Arc에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Dome9 Arc에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Dome9 Arc 테스트 사용자 만들기](#create-a-dome9-arc-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Dome9 Arc에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Dome9 Arc 응용 프로그램에서 Single Sign-On을 구성합니다.

**Dome9 Arc에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Dome9 Arc** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **Dome9 Arc 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Dome9 Arc 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. **식별자** 텍스트 상자에 URL `https://secure.dome9.com/`을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > 이 자습서의 뒷부분에서 설명하는 dome9 관리 포털에서 회사 이름 값을 선택합니다.

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Dome9 Arc 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Dome9 Arc 클라이언트 지원 팀](https://dome9.com/about/contact-us/)에 문의하세요. 

5. Dome9 Arc 소프트웨어 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

    ![Single Sign-On 구성 attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름  | 특성 값 | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Single Sign-On 특성 추가 구성](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Single Sign-On 특성 편집 구성](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.

7. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. **Dome9 Arc 구성** 섹션에서 **Dome9 Arc 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Dome9 Arc 구성](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. 다른 웹 브라우저 창에서 Dome9 Arc 회사 사이트에 관리자 권한으로 로그인합니다.

11. 오른쪽 위 모서리에서 **프로필 설정**을 클릭한 다음, **계정 설정**을 클릭합니다. 

    ![Dome9 Arc 구성](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. **SSO**로 이동한 다음 **사용**을 클릭합니다.

    ![Dome9 Arc 구성](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. [SSO 구성] 섹션에서 다음 단계를 수행합니다.

    ![Dome9 Arc 구성](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. **계정 ID** 텍스트 상자에서 회사 이름을 입력합니다. 이 값은 Azure Portal URL 섹션에서 언급한 회신 URL에 사용됩니다.

    나. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **발급자** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **IDP 엔드포인트 URL** 텍스트 상자에 붙여넣습니다.

    d. 다운로드한 Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음, **X.509 인증서** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-dome9-arc-test-user"></a>Dome9 Arc 테스트 사용자 만들기

Azure AD 사용자가 Dome9 Arc에 로그인할 수 있게 하려면 해당 사용자를 응용 프로그램에 프로비전해야 합니다. Dome9 Arc는 JIT(Just-In-Time) 프로비전을 지원하지만 이 기능이 제대로 작동하려면 사용자가 특정 **역할**을 선택하여 사용자에게 할당해야 합니다.

   >[!Note] 
   >**역할**을 만드는 방법 및 다른 자세한 내용은 [Dome9 Arc 클라이언트 지원 팀](https://dome9.com/about/contact-us/)에 문의하세요.

**사용자 계정을 수동으로 프로비전하려면 다음 단계를 수행합니다.**

1. Dome9 Arc 회사 사이트에 관리자 권한으로 로그인합니다.

2. **사용자 및 역할**을 클릭한 다음, **사용자**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. **사용자 추가**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. **사용자 만들기** 섹션에서 다음 단계를 수행합니다.
    
    ![직원 추가](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. **이메일** 텍스트 상자에서 사용자의 이메일(예: Brittasimon@contoso.com)을 입력합니다.

    나. **이름** 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. **SSO 사용자**를 **설정**으로 지정합니다.

    e. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Dome9 Arc에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Dome9 Arc에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Dome9 Arc**를 선택합니다.

    ![응용 프로그램 목록의 Dome9 Arc 링크](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 [Dome9 Arc] 타일을 클릭하면 Dome9 Arc 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

