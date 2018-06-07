---
title: '자습서: Netsuite와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Netsuite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: 2f2bd227a3d8c0b797f37026032938fbcfe3de9e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351340"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>자습서: Netsuite와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Netsuite를 통합하는 방법에 대해 알아봅니다.

Netsuite를Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Netsuite에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Netsuite에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Netsuite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Form.com Single Sign-On이 사용하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Netsuite 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-netsuite-from-the-gallery"></a>갤러리에서 Netsuite 추가
Netsuite가 Azure AD에 통합되도록 구성하려면 갤러리의 Netsuite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Netsuite를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Netsuite**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. 결과 패널에서 **Netsuite**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Netsuite에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Netsuite 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Netsuite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Netsuite의 **Username** 값으로 할당하여 설정합니다.

Netsuite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Netsuite 테스트 사용자 만들기](#creating-a-netsuite-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Netsuite에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Netsuite 응용 프로그램에서 Single Sign-On을 구성합니다.

**Netsuite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Netsuite** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. **Netsuite 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.  `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트하세요. 이러한 값을 얻으려면 [Netsuite 지원 팀](http://www.netsuite.com/portal/services/support.shtml)에 문의하세요.
 
4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. **Netsuite 구성** 섹션에서 **Netsuite 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. 브라우저에서 새 탭을 열고 관리자 권한으로 Netsuite 회사 사이트에 로그인합니다.

8. 페이지의 위쪽에 있는 도구 모음에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. **설치 작업** 목록에서 **통합**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. **인증 관리** 섹션에서 **SAML Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. **SAML 설정** 페이지에서 다음 단계를 수행합니다.
   
    a. **로그온 구성**의 **빠른 참조** 섹션에서 **SAML Single Sign-On 서비스 URL**을 복사하여 Netsuite의 **Identity Provider Login Page**(ID 공급자 로그인 페이지) 필드에 붙여넣습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    나. [Netsuite 설치]에서 **기본 인증 방법**을 선택합니다.

    다. **SAMLV2 ID 공급자 메타데이터** 필드에서 **IDP 메타 데이터 파일 업로드**를 선택합니다. 그런 다음 **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. **제출**을 클릭합니다.

12. Azure AD에서 **기타 모든 사용자 특성 보기 및 편집** 확인란을 클릭하고 특성을 추가합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. **특성 이름** 필드에 `account`을(를) 입력합니다. **특성 값** 필드에서 Netsuite 계정 ID를 입력합니다. 이 값은 상수이며 계정에 따라 변경됩니다. 사용자의 계정 ID를 찾는 방법에 대한 지침은 아래에 포함되어 있습니다.

      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. Netsuite의 위쪽 탐색 메뉴에서 **설치**를 클릭합니다.

    나. 그런 다음 왼쪽 탐색 메뉴의 **Setup Tasks**(설치 작업) 섹션 아래를 클릭하고 **통합** 섹션을 선택한 다음 **Web Services Preferences**(웹 서비스 기본 설정)를 클릭합니다.

    다. Netsuite 계정 ID를 복사하여 Azure AD의 **특성 값** 필드에 붙여넣습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. 사용자가 Netsuite에서 Single Sign-On을 수행하려면 먼저 Netsuite에 적절한 권한을 할당해야 합니다. 이러한 사용 권한을 할당하려면 아래 지침을 따릅니다.

    a. 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    나. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **역할 관리**를 클릭합니다.
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    다. **새 역할**을 클릭합니다.

    d. 새 역할에 **이름**을 입력합니다.
      
      ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. **저장**을 클릭합니다.

    f. 위쪽에 있는 메뉴에서 **사용 권한**을 클릭합니다. **설치**를 클릭합니다.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. **SAML Single Sign-On 설치**를 선택한 다음, **추가**를 클릭합니다.

    h. **저장**을 클릭합니다.

    i. 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **사용자 관리**를 클릭합니다.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. 테스트 사용자를 선택합니다. **편집**을 클릭합니다.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. 역할 대화 상자에서 만든 역할을 선택하거 **추가**를 클릭합니다.
      
       ![Configure Single Sign-On](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. **저장**을 클릭합니다.
    
### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 

### <a name="creating-a-netsuite-test-user"></a>Netsuite 테스트 사용자 만들기

이 섹션에서는 Netsuite에서 Britta Simon이라는 사용자를 만듭니다. Netsuite는 Just-In-Time 프로비전을 지원하며, 이 프로비전은 기본적으로 사용하도록 설정됩니다.
이 섹션에 작업 항목이 없습니다. Netsuite에 아직 사용자가 없으면 Netsuite에 액세스하려고 할 때 새 사용자가 만들어집니다.


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Netsuite에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Netsuite에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Netsuite**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On 설정을 테스트하려면 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 액세스 패널을 연 다음, 테스트 계정에 로그인하고 **Netsuite**를 클릭합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

