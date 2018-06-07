---
title: '자습서: QuickHelp와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 QuickHelp 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: 9eaa9032a60b6b2906034df10d6eef8671255483
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349338"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>자습서: QuickHelp와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 QuickHelp를 통합하는 방법에 대해 알아봅니다.

QuickHelp를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- QuickHelp에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 QuickHelp에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

QuickHelp와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- QuickHelp Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 QuickHelp 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-quickhelp-from-the-gallery"></a>갤러리에서 QuickHelp 추가
QuickHelp의 Azure AD 통합을 구성하려면 갤러리의 QuickHelp를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 QuickHelp를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **QuickHelp**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_search.png)

5. 결과 패널에서 **QuickHelp**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 QuickHelp에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 QuickHelp 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 QuickHelp의 관련 사용자 간에 연결이 형성되어야 합니다.

QuickHelp에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

QuickHelp에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[QuickHelp 테스트 사용자 만들기](#creating-a-quickhelp-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 QuickHelp에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 QuickHelp 응용 프로그램에서 Single Sign-On을 구성합니다.

**QuickHelp에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **QuickHelp** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

3. **QuickHelp 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://quickhelp.com/<ROUTEURL>`

    나. **식별자** 텍스트 상자에 URL `https://auth.quickhelp.com`를 입력합니다.

    > [!NOTE] 
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 조직의 빠른 도움말 관리자 또는 브레인스토밍 클라이언트 성공 관리자에게 문의하여 값을 가져옵니다.
 
4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-quickhelp-tutorial/tutorial_general_400.png) 

6. QuickHelp 회사 사이트에 관리자로 로그인합니다.

7. 위쪽의 메뉴에서 **관리자**를 클릭합니다.
   
    ![Configure Single Sign-On][21]

8. **QuickHelp 관리자** 메뉴에서 **설정**을 클릭합니다.
   
    ![Configure Single Sign-On][22]

9. **인증 설정**을 클릭합니다.

10. **인증 설정** 페이지에서 다음 단계를 수행합니다.
   
    ![Configure Single Sign-On][23]
   
    a. **SSO 형식**으로 **WSFederation**을 선택합니다.
   
    나. 다운로드한 Azure 메타데이터 파일을 업로드하려면 **찾아보기**를 클릭하여 파일로 이동한 다음 **메타데이터 업로드**를 클릭합니다.
   
    다. **전자 메일 주소** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.
   
    d. **이름** 텍스트 상자에 `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.
   
    e. **성** 텍스트 상자에 `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`을 입력합니다.
   
    f. **작업 모음**에서 **저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-quickhelp-test-user"></a>QuickHelp 테스트 사용자 만들기

이 섹션은 QuickHelp에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.
Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 QuickHelp 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 QuickHelp의 관련 사용자 간에 연결이 형성되어야 합니다.

QuickHelp는 Just-In-Time 프로비전을 지원합니다. 즉, 필요한 경우 사용자 계정이 QuickHelp에 자동으로 만들어지고 계정이 Azure AD 계정에 연결됩니다.

이 섹션에 작업 항목이 없습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 QuickHelp에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 QuickHelp에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **QuickHelp**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  

액세스 패널에서 QuickHelp 타일을 클릭하면 QuickHelp 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
