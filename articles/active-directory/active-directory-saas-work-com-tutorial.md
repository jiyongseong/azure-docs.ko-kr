---
title: '자습서: Work.com와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Work.com 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: bfc76d05a52d0283e3367f9c98dc8ed427cbe592
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>자습서: Work.com과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Work.com을 통합하는 방법에 대해 알아봅니다.

Work.com을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Work.com에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Work.com에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Work.com과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Work.com Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Work.com 추가
2. Azure AD Single Sign-On 구성 및 테스트

## <a name="add-workcom-from-the-gallery"></a>갤러리에서 Work.com 추가
Work.com의 Azure AD 통합을 구성하려면 갤러리의 Work.com을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Work.com을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Work.com**을 입력하고 결과 패널에서 **Work.com**을 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![갤러리에서 추가](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Work.com에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Work.com 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Work.com의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Work.com에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Work.com에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Work.com 테스트 사용자 만들기](#create-a-workcom-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Work.com에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Work.com 응용 프로그램에서 Single Sign-On을 구성합니다.

>[!NOTE]
>Single Sign-On을 구성하려면 사용자 할당 도메인 이름을 Work.com로 설정해야 합니다. 최소한 한 개의 도메인 이름을 정의하고, 도메인 이름을 테스트 한 후 전체 조직에 배포해야 합니다.

**Work.com에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Work.com** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![SAML 기반 로그온](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. **Work.com 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Work.com 도메인 및 URL 섹션](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Work.com 클라이언트 지원 팀](https://help.salesforce.com/articleView?id=000159855&type=3)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![SAML 서명 인증서 섹션](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![저장 단추](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. **Work.com 구성** 섹션에서 **Work.com 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Work.com 구성 섹션](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. 관리자 권한으로 Work.com 테넌트에 로그인합니다.

8. **설정**으로 이동합니다.
   
    ![설치](./media/active-directory-saas-work-com-tutorial/ic794108.png "설치")

9. 왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
   
    ![내 도메인](./media/active-directory-saas-work-com-tutorial/ic767825.png "내 도메인")

10. 도메인이 올바르게 설정되었는지 확인하려면 “**Step 4 Deployed to Users**(4단계 사용자에게 배포)”에 있는지 확인하고 “**My Domain Settings**(내 도메인 설정)”을 검토합니다.
   
    ![사용자에게 배포된 도메인](./media/active-directory-saas-work-com-tutorial/ic784377.png "사용자에게 배포된 도메인")

11. Work.com 테넌트에 로그인합니다.

12. **설정**으로 이동합니다.
    
    ![설치](./media/active-directory-saas-work-com-tutorial/ic794108.png "설치")

13. **보안 제어** 메뉴를 확장한 다음 **Single Sign-On 설정**을 클릭합니다.
    
    ![Single Sign-On 설정](./media/active-directory-saas-work-com-tutorial/ic794113.png "Single Sign-On 설정")

14. **Single Sign-On 설정** 대화 상자 페이지에서 다음 단계를 수행합니다.
    
    ![SAML 사용](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML 사용")
    
    a. **SAML 사용**을 선택합니다.
    
    나. **새로 만들기**를 클릭합니다.

15. **SAML Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.
    
    ![SAML Single Sign-On 설정](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML Single Sign-On 설정")
    
    a. **이름** 텍스트 상자에 구성할 이름을 입력합니다.  
       
    > [!NOTE]
    > **이름**에 값을 제공하면 **API 이름** 텍스트 상자가 자동으로 채워집니다.
    
    나. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.
    
    다. Azure Portal에서 다운로드한 인증서를 업로드하려면 **찾아보기**를 클릭합니다.
    
    d. **엔터티 ID** 텍스트 상자에 `https://salesforce-work.com`을 입력합니다.
    
    e. **SAML ID 유형**으로 **사용자 개체에서 페더레이션 ID를 포함하는 어설션**을 선택합니다.
    
    f. **SAML ID 위치**에서 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
    
    g. **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    h. **ID 공급자 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.
    
    i. **서비스 공급자가 시작한 요청 바인딩**에서 **HTTP Post**를 선택합니다.
    
    j. **저장**을 클릭합니다.

16. Work.com 클래식 포털의 왼쪽 탐색창에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다. 
    
    ![내 도메인](./media/active-directory-saas-work-com-tutorial/ic794115.png "내 도메인")

17. **내 도메인** 페이지의 **로그인 페이지 브랜딩** 섹션에서 **편집**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/active-directory-saas-work-com-tutorial/ic767826.png "로그인 페이지 브랜딩")

14. **로그인 페이지 브랜딩** 페이지의 **인증 서비스** 섹션에 **SAML SSO 설정** 이름이 표시됩니다. 이름을 선택하고 **저장**을 클릭합니다.
    
    ![로그인 페이지 브랜딩](./media/active-directory-saas-work-com-tutorial/ic784366.png "로그인 페이지 브랜딩")

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![사용자 및 그룹 -> 모든 사용자](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![추가](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자 페이지](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-workcom-test-user"></a>Work.com 테스트 사용자 만들기
Azure Active Directory 사용자가 로그인하려면, Work.com에 프로비전되어야 합니다. Work.com의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면
1. Work.com 회사 사이트에 관리자 권한으로 로그인합니다.

2. **설정**으로 이동합니다.
   
    ![설치](./media/active-directory-saas-work-com-tutorial/IC794108.png "설치")
3. **사용자 관리 \> 사용자**로 이동합니다.
   
    ![사용자 관리](./media/active-directory-saas-work-com-tutorial/IC784369.png "사용자 관리")

4. **새 사용자**를 클릭합니다.
   
    ![모든 사용자](./media/active-directory-saas-work-com-tutorial/IC794117.png "모든 사용자")

5. 사용자 편집 섹션에서, 관련 텍스트 상자에 프로비전하려는 유효한 Azure AD 계정의 특성에 다음 단계를 수행합니다.
   
    ![사용자 편집](./media/active-directory-saas-work-com-tutorial/ic794118.png "사용자 편집")
   
    a. **이름** 텍스트 상자에 사용자의 **이름**을 **Britta**로 입력합니다.
    
    나. **성** 텍스트 상자에 사용자의 **성**을 **Simon**으로 입력합니다.
    
    다. **별칭** 텍스트 상자에 사용자의 **이름**을 **BrittaS**로 입력합니다.
    
    d. **전자 메일** 텍스트 상자에 사용자의 **이메일 주소** **Brittasimon@contoso.com**을 입력합니다.
    
    e. **사용자 이름** 텍스트 상자에 사용자의 사용자 이름(예: **Brittasimon@contoso.com**)을 입력합니다.
    
    f. **Nick Name**(애칭) 텍스트 상자에 사용자의 **애칭** **Simon**을 입력합니다.
    
    g. **역할**, **사용자 라이선스** 및 **프로필**을 차례로 선택합니다.
    
    h. **저장**을 클릭합니다.  
      
    > [!NOTE]
    > Azure AD 계정 보유자에게 계정 확인 링크가 포함한 전자 메일이 발송되며, 확인을 마치면 계정이 활성화됩니다.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Work.com에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Work.com에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Work.com**을 선택합니다.

    ![앱 목록의 Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Work.com 타일을 클릭하면 Work.com 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

