---
title: '자습서: SuccessFactors와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 SuccessFactors 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 63ee7f7a08d16048eb3f0672cac4c2f41a3ed0f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>자습서: SuccessFactors와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SuccessFactors를 통합하는 방법에 대해 알아봅니다.

SuccessFactors를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SuccessFactors에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SuccessFactors에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SuccessFactors와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SuccessFactors Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SuccessFactors 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-successfactors-from-the-gallery"></a>갤러리에서 SuccessFactors 추가
SuccessFactors의 Azure AD 통합을 구성하려면 갤러리의 SuccessFactors를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SuccessFactors를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SuccessFactors**를 입력하고 결과 패널에서 **SuccessFactors**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 SuccessFactors](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SuccessFactors 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 SuccessFactors의 관련 사용자 간에 연결이 형성되어야 합니다.

SuccessFactors에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SuccessFactors 테스트 사용자 만들기](#create-a-successfactors-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SuccessFactors에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SuccessFactors 응용 프로그램에서 Single Sign-On을 구성합니다.

**SuccessFactors에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SuccessFactors** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. **SuccessFactors 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![SuccessFactors 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다.
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    나. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [SuccessFactors 클라이언트 지원 팀](https://www.successfactors.com/en_us/support.html)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. **SuccessFactors 구성** 섹션에서 **SuccessFactors 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. 다른 웹 브라우저 창에서 **SuccessFactors 관리 포털**에 관리자로 로그인합니다.
    
8. **응용 프로그램 보안**을 방문하고 **Single Sign On 기능**으로 이동합니다. 

9. **토큰 재설정**에 값을 입력하고 **토큰 저장**을 클릭하여 SAML SSO를 사용하도록 설정합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][11]

    > [!NOTE] 
    > 이 값은 설정/해제 스위치로 사용됩니다. 값이 저장된 경우 SAML SSO는 ON입니다. 빈 값이 저장된 경우 SAML SSO는 OFF입니다.

10. 아래 스크린샷으로 이동하고 다음 작업을 수행합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][12]
   
    a. **SAML v2 SSO** 라디오 단추를 선택합니다.
   
    나. **SAML 어설션 파티 이름**(예: SAML 발급자 + 회사 이름)을 설정합니다.
   
    다. **발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.
   
    d. **필수 서명 요구**로 **응답(고객 생성/IdP/AP)** 을 선택합니다.
   
    e. **SAML 플래그 사용**으로 **사용**을 선택합니다.
   
    f. **로그인 요청 서명(SF 생성/SP/RP)** 으로 **아니요**를 선택합니다.
   
    g. **SAML 프로필**로 **브라우저/게시 프로필**을 선택합니다.
   
    h. **인증서 유효 기간 적용**으로 **아니요**를 선택합니다.
   
    i. Azure Portal에서 다운로드한 인증서 파일의 내용을 복사한 다음 **SAML 확인 인증서** 텍스트 상자에 붙여넣습니다.

    > [!NOTE] 
    > 인증서 내용에는 시작 인증서 및 끝 인증서 태그가 있어야 합니다.

11. SAML V2로 이동한 후 다음 단계를 수행합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][13]
   
    a. **SP 시작 전역 로그아웃 지원**으로 **예**를 선택합니다.
   
    나. **전역 로그아웃 서비스 URL(LogoutRequest 대상)** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.
   
    다. **요청 SP는 모든 NameID 요소를 암호화해야 합니다.** 로 **아니요**를 선택합니다.
   
    d. **NameID 형식**으로 **지정되지 않음**을 선택합니다.
   
    e. **SP 시작 로그인 사용(AuthnRequest)** 으로 **예**를 선택합니다.
   
    f. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **전사적 발급자로 보내기 요청** 텍스트 상자에 붙여넣습니다.

12. 로그인 사용자 이름의 대/소문자를 구분하지 않으려면 이 단계를 수행합니다.
   
    ![Configure Single Sign-On][29]
    
    a. a. **회사 설정**(아래쪽)을 방문합니다.
   
    나. **사용자 이름 대/소문자 구분하지 않음 사용**근처의 확인란을 선택합니다.
   
    c. **저장**을 클릭합니다.
   
    > [!NOTE] 
    > 이 기능을 사용하려고 하면 시스템에서 중복되는 SAML 로그인 이름이 만들어지는지 확인합니다. 예를 들어 고객의 사용자 이름에 User1 및 user1이 있는 경우입니다. 대/소문자를 구분하지 않으면 이러한 중복 항목을 만듭니다. 시스템에서 오류 메시지를 제공하고 기능이 비활성화됩니다. 다른 철자가 되도록 고객은 사용자 이름 중 하나를 변경해야 합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-successfactors-test-user"></a>SuccessFactors 테스트 사용자 만들기

Azure AD 사용자가 SuccessFactors에 로그인할 수 있도록 하려면 SuccessFactors로 프로비전되어야 합니다.  
SuccessFactors의 경우 프로비전은 수동 작업입니다.

SuccessFactors에서 사용자를 생성하려면 [SuccessFactors 지원 팀](https://www.successfactors.com/en_us/support.html)에 문의해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SuccessFactors에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 SuccessFactors에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SuccessFactors**를 선택합니다.

    ![응용 프로그램 목록의 SuccessFactors 링크](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SuccessFactors 타일을 클릭하면 SuccessFactors 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

