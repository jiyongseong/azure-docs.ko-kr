---
title: Tableau Online와 Azure Active Directory 통합 | Microsoft Docs
description: Azure Active Directory 및 Tableau Online 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 4747c2146c6590e30da2ba8985ecdfbd6a75ae96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tableau Online와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Tableau Online을 통합하는 방법에 대해 알아봅니다.

Tableau Server를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Tableau Online에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Tableau Online에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Tableau Online과의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Tableau Online Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Tableau Online 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-tableau-online-from-the-gallery"></a>갤러리에서 Tableau Online 추가
Tableau Online의 Azure AD 통합을 구성하려면 갤러리의 Tableau Online을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Tableau Online을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Tableau Online**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. 결과 창에서 **Tableau Online**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Tableau Online에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Tableau Online 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Tableau Online의 관련 사용자 간에 연결이 형성되어야 합니다.

Tableau Online에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Tableau Online에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Tableau Online 테스트 사용자 만들기](#creating-a-tableau-online-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Tableau Online에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Tableau Online 응용 프로그램에서 Single Sign-On을 구성합니다.

**Tableau Online에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Tableau Online** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. **Tableau Online 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. **로그온 URL** 텍스트 상자에서 URL `https://sso.online.tableau.com`을 입력합니다.

    나. **식별자** 텍스트 상자에 URL `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `을 입력합니다.

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. 다른 브라우저 창에서 Tableau Online 응용 프로그램에 로그온합니다. **설정** 및 **인증**에 차례로 이동합니다.
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. SAML을 사용하도록 설정하려면 **인증 유형** 섹션 아래에서 **SAML로 Single Sign-On** 확인란을 선택합니다.
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. **Tableau Online으로 메타데이터 파일 가져오기** 섹션까지 아래로 스크롤합니다.  찾아보기를 클릭하여 Azure AD에서 다운로드한 메타데이터 파일을 가져오기합니다. 그런 후 **Apply**를 클릭합니다.
   
   ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. **어설션 일치** 섹션에서 **이메일 주소**, **이름** 및 **성**에 대한 해당 ID 공급자 어설션 이름을 삽입합니다. Azure Ad에서 이 정보 얻으려면 
  
    a. Azure Portal에서 **Tableau Online** 응용 프로그램 통합 페이지로 이동합니다.
    
    나. 특성 섹션에서 **"기타 모든 사용자 특성 보기 및 편집"** 확인란을 선택합니다. 
    
   ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    다. 다음 단계에 따라 givenname, email, surname 특성의 네임스페이스 값을 복사합니다.

   ![Azure AD Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. **user.givenname** 값을 클릭합니다. 
    
    e. **네임스페이스** 텍스트 상자의 값을 복사합니다.

   ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. email 및 surname에 대한 네임스페이스 값을 복사하려면 이전 단계를 따릅니다.

    g. Tableau Onlin 응용 프로그램으로 전환한 후 **Tableau Online 특성** 섹션을 다음과 같이 설정합니다.
     * 전자 메일: **메일** 또는 **userprincipalname**
     * 이름: **givenname**
     * 성: **surname**
   
   ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-tableau-online-test-user"></a>Tableau Online 테스트 사용자 만들기

이 섹션에서는 Tableau Online에서 Britta Simon이라는 사용자를 만듭니다.

1. **Tableau Online**에서 **설정**을 클릭한 후 **인증** 섹션을 클릭합니다. **사용자 선택** 섹션으로 스크롤합니다. **사용자 추가**를 클릭하고 **이메일 주소 입력**을 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. **SSO(Single Sign-On) 인증에 대한 사용자 추가**를 선택합니다. **전자 메일 주소 입력** 텍스트 상자에 britta.simon@contoso.com을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. **만들기**를 클릭합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Tableau Online에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Tableau Online에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Tableau Online**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.

액세스 패널에서 Tableau Online 타일을 클릭하면 Tableau Online 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
