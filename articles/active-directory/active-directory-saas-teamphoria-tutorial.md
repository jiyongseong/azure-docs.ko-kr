---
title: '자습서: Teamphoria와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Teamphoria 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: f2d7a871d6cfb5fd694671c21f2aeacba6abe48f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>자습서: Teamphoria와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Teamphoria를 통합하는 방법에 대해 알아봅니다.

Teamphoria를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Teamphoria에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Teamphoria에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Teamphoria와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Teamphoria Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Teamphoria 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-teamphoria-from-the-gallery"></a>갤러리에서 Teamphoria 추가
Teamphoria의 Azure AD 통합을 구성하려면 갤러리의 Teamphoria를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Teamphoria를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Teamphoria**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. 결과 창에서 **Teamphoria**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Teamphoria에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Teamphoria 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Teamphoria의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Teamphoria의 **Username** 값으로 할당하여 설정합니다.

Teamphoria에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Teamphoria 테스트 사용자 만들기](#creating-a-teamphoria-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Teamphoria에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Teamphoria 응용 프로그램에서 Single Sign-On을 구성합니다.

**Teamphoria에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **Teamphoria** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. **Teamphoria 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<sub-domain>.teamphoria.com/login` 패턴을 사용하여 URL을 입력합니다.    

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL로 업데이트해야 합니다. 로그온 URL을 가져오려면 [Teamphoria 클라이언트 지원 팀](https://www.teamphoria.com/)에 문의합니다. 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서를 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. **Teamphoria 구성** 섹션에서 **Teamphoria 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. **Teamphoria** 측에서 Single Sign-On을 구성하려면 관리자 권한으로 Teamphoria 응용 프로그램에 로그인합니다.

8. 왼쪽 도구 모음에서 **관리 설정** 옵션으로 이동하고 구성 탭 아래에서 **SINGLE SIGN-ON**을 클릭하여 SSO 구성 창을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. 오른쪽 위 모서리에서 **새 ID 공급자 추가** 옵션을 클릭하여 SSO에 대한 설정을 추가하기 위한 양식을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. 아래에서 설명한 대로 필드에 세부 정보를 입력합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **표시 이름**: 관리 페이지에서 플러그 인의 표시 이름을 입력합니다.

    나. **단추 이름**: SSO를 통한 로그인에 대해 로그인 페이지에 표시하는 탭의 이름입니다.

    다. **인증서**: 메모장에서 Azure Portal에서 이전에 다운로드한 인증서를 열고 동일한 내용을 복사하고 상자에 붙여 넣습니다.

    d. **진입점**: Azure Portal에서 이전에 복사한 **SAML Single Sign-On 서비스 URL**을 붙여 넣습니다.

    e. 옵션을 **ON**으로 전환하고 **저장**을 클릭합니다.   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-teamphoria-test-user"></a>Teamphoria 테스트 사용자 만들기

Azure AD 사용자가 Teamphoria에 로그인할 수 있도록 하려면 Teamphoria로 프로비전되어야 합니다. Teamphoria의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Teamphoria 회사 사이트에 관리자 권한으로 로그인합니다.

2. 왼쪽 도구 모음에서 **관리자** 설정을 클릭하고 **관리** 탭 아래에서 **사용자**를 클릭하여 사용자에 대한 관리 페이지를 엽니다.

    ![직원 추가](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. **수동 초대** 옵션을 클릭합니다.

    ![피플 초대](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. 이 페이지에서 다음 작업을 수행합니다. 
    
    ![피플 초대](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. **전자 메일 주소** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    나. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    다. **성** 텍스트 상자에 **Simon**을 입력합니다.

    d. **1 사용자 초대**를 클릭합니다. 사용자는 시스템에서 생성할 초대를 수락해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Teamphoria에 대한 액세스를 허용합니다.

![사용자 할당][200] 

**Britta Simon을 Teamphoria에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Teamphoria**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

