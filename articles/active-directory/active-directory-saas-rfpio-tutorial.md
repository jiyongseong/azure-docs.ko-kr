---
title: '자습서: RFPIO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 RFPIO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 915b6f9c96bc8dbab54e770340acf795df92a3fb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>자습서: RFPIO와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 RFPIO를 통합하는 방법에 대해 알아봅니다.

RFPIO를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- RFPIO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 RFPIO를에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

RFPIO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- RFPIO Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 RFPIO 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-rfpio-from-the-gallery"></a>갤러리에서 RFPIO 추가
RFPIO의 Azure AD 통합을 구성하려면 갤러리의 RFPIO를 관리되는 SaaS 앱 목록에 추가해야 합니다.

### <a name="to-add-rfpio-from-the-gallery"></a>갤러리에서 RFPIO를 추가하려면

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **RFPIO**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. 결과 패널에서 **RFPIO**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 RFPIO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 RFPIO 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 RFPIO의 관련 사용자 간에 연결이 형성되어야 합니다.

RFPIO에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

RFPIO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트합니다.
3. **[RFPIO 테스트 사용자 만들기](#creating-a-rfpio-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 RFPIO에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 RFPIO 응용 프로그램에서 Single Sign-On을 구성합니다.

**RFPIO에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **RFPIO** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. **RFPIO 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. **식별자** 텍스트 상자에 URL `https://www.rfpio.com`을 입력합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    나. **고급 URL 설정 표시**를 선택합니다.

    다. **릴레이 상태** 텍스트 상자에 문자열 값을 입력합니다. 이 값을 얻으려면 [RFPIO 지원 팀](https://www.rfpio.com/contact/)에 문의하세요. 

4. **고급 URL 설정 표시**를 선택합니다. **SP** 시작 모드에서 응용 프로그램을 구성하려면: 

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    **로그온 URL** 텍스트 상자에 URL `https://www.app.rfpio.com`을 입력합니다.

5. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. 다른 웹 브라우저 창에서 **RFPIO** 웹 사이트에 관리자로 로그인합니다.

8. 왼쪽 아래 모서리 드롭다운을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. **조직 설정**을 클릭합니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. **기능 및 통합**을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. **SAML SSO 구성**에서 **편집**을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. 이 섹션에서 다음 작업을 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. **다운로드한 메타데이터 XML**의 내용을 복사하여 **ID 구성** 필드에 붙여넣습니다.

    > [!NOTE]
    >다운로드한 **메타데이터 XML**의 내용을 복사하려면 **메모장++** 또는 적절한 **XML 편집기**를 사용합니다. 

    나. **유효성 검사**를 클릭합니다.

    다. **유효성 검사**를 클릭한 후 **SAML(Enabled)**(SAML(사용))을 설정으로 전환합니다.

    d. **제출**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-rfpio-test-user"></a>RFPIO 테스트 사용자 만들기

Azure AD 사용자가 RFPIO에 로그인할 수 있도록 하려면 RFPIO로 프로비전되어야 합니다.  
RFPIO의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. RFPIO 회사 사이트에 관리자로 로그인합니다.

2. 왼쪽 아래 모서리 드롭다운을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. **조직 설정**을 클릭합니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. **TEAM MEMBERS**(팀 멤버)를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. **멤버 추가**를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. **새 멤버 추가** 섹션에서 다음 작업을 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. **Enter one email per line**(줄당 하나의 메일 입력) 필드에 **메일 주소**를 입력합니다.

    나. 요구 사항에 따라 **역할**을 선택합니다.

    다. **멤버 추가**를 클릭합니다.
        
    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 RFPIO에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 RFPIO에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **RFPIO**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 RFPIO 타일을 클릭하면 RFPIO 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

