---
title: '자습서: ScaleX Enterprise와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 ScaleX Enterprise 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 94c30d39a5d72a022dd77d65141f1af679cf3dd3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>자습서: ScaleX Enterprise와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ScaleX Enterprise를 통합하는 방법에 대해 알아봅니다.

ScaleX Enterprise를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 사용자의 ScaleX Enterprise에 대한 액세스 권한을 제어할 수 있습니다.
- 사용자의 Azure AD 계정으로 ScaleX Enterprise에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ScaleX Enterprise와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ScaleX Enterprise Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마십시오.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ScaleX Enterprise 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-scalex-enterprise-from-the-gallery"></a>갤러리에서 ScaleX Enterprise 추가
ScaleX Enterprise가 Azure AD에 통합되도록 구성하려면 갤러리에서 ScaleX Enterprise를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ScaleX Enterprise를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **ScaleX Enterprise**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. 결과 창에서 **ScaleX Enterprise**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ScaleX Enterprise에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ScaleX Enterprise 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ScaleX Enterprise의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 ScaleX Enterprise의 **Username** 값으로 할당하여 설정합니다.

ScaleX Enterprise에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[ScaleX Enterprise 테스트 사용자 만들기](#creating-a-scalex-enterprise-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 ScaleX Enterprise에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 ScaleX Enterprise 응용 프로그램에서 Single Sign-On을 구성합니다.

**ScaleX Enterprise에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **ScaleX Enterprise** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**로 **SAML 기반 로그인**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. **ScaleX Enterprise 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. **식별자** 텍스트 상자에 `https://platform.rescale.com/saml2/<company id>/` 패턴으로 값을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://platform.rescale.com/saml2/<company id>/acs/`

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 선택하세요.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `https://platform.rescale.com/saml2/<company id>/sso/` 
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 또는 로그온 URL을 사용하여 값을 업데이트합니다. 이러한 값을 얻으려면 [ScaleX Enterprise 클라이언트 지원 팀](http://info.rescale.com/contact_sales)에 문의하세요. 

5. ScaleX 응용 프로그램에는 특정 형식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 수정해야 합니다. 사용자 지정 특성 설정을 열려면 **기타 모든 사용자 특성 보기 및 편집** 확인란을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. **name** 특성을 마우스 오른쪽 단추로 클릭하고 삭제를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    나. **emailaddress** 특성을 클릭하여 특성 편집 창을 엽니다. **user.mail**에서 **user.userprincipalname**으로 값을 변경하고 확인을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)   
    
5. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. **ScaleX Enterprise 구성** 섹션에서 **ScaleX Enterprise 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. **ScaleX Enterprise** 쪽에서 Single Sign-On을 구성하려면 관리자 권한으로 ScaleX Enterprise 회사 웹 사이트에 로그인합니다.

9. 오른쪽 위에 있는 메뉴를 클릭하고 **Contoso Administration(Contoso 관리)** 를 선택합니다.

    > [!NOTE] 
    > Contoso는 예일뿐입니다. 이 값은 실제 회사 이름이어야 합니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. 최상위 메뉴에서 **Integrations(통합)** 을 선택하고 **Single Sign-On**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. 다음과 같이 양식을 완료합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. **“Create any user who can authenticate with SSO.”(SSO로 인증할 수 있는 사용자 만들기)** 를 선택합니다.

    나. **Service Provider saml(서비스 공급자 saml)**: ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent*** 값을 붙여 넣습니다.

    다. **Name of Identity Provider email field in ACS response(ACS 응답의 ID 공급자 전자 메일 필드 이름)**: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 값을 붙여 넣습니다.

    d. **Identity Provider EntityDescriptor Entity ID(ID 공급자 EntityDescriptor 엔터티 ID):** Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여 넣습니다.

    e. **Identity Provider SingleSignOnService URL(ID 공급자 Single Sign-On URL):** Azure Portal의 **SAML Single Sign-On 서비스 URL**을 붙여 넣습니다.

    f. **Identity Provider public X509 certificate(ID 공급자 공용 X509 인증서):** Azure에서 다운로드 한 X509 인증서를 메모장에서 열어서 이 상자에 내용을 붙여 넣습니다. 인증서 내용 중간에 줄 바꿈이 없어야 합니다.
    
    g. 다음 확인란을 선택합니다: **Enabled(사용), Encrypt NameID(NameID 암호화) 및 Sign AuthnRequests(AuthnRequests 서명)**

    h. **Update SSO Settings(SSO 설정 업데이트)** 를 클릭하여 설정을 저장합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>ScaleX Enterprise 테스트 사용자 만들기

Azure AD 사용자가 ScaleX Enterprise에 로그인할 수 있도록 하려면 ScaleX Enterprise에 프로비전되어야 합니다. ScaleX Enterprise의 경우 프로비전이 자동 작업이며 수동 단계가 필요하지 않습니다. SSO 자격 증명으로 인증할 수 있는 사용자라면 누구든 ScaleX 쪽에서 자동으로 프로비전됩니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ScaleX Enterprise에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**ScaleX Enterprise에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **ScaleX Enterprise**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ScaleX Enterprise 타일을 클릭하면 ScaleX Enterprise 응용 프로그램에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png

