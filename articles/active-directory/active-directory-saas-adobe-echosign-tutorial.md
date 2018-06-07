---
title: '자습서: Adobe Sign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adobe Sign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: c3b7e7178ef68475f331edf058ca0f23661af3ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338876"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>자습서: Adobe Sign과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Sign을 통합하는 방법에 대해 알아봅니다.

Adobe Sign과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- Adobe Sign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Adobe Sign에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Adobe Sign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Adobe Sign Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Adobe Sign 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-adobe-sign-from-the-gallery"></a>갤러리에서 Adobe Sign 추가
Adobe Sign의 Azure AD 통합을 구성하려면 갤러리의 Adobe Sign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adobe Sign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Adobe Sign**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. 결과 패널에서 **Adobe Sign**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Adobe Sign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Adobe Sign 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Adobe Sign의 관련 사용자 간에 연결이 형성되어야 합니다.

Adobe Sign에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Adobe Sign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Adobe Sign 테스트 사용자 만들기](#creating-an-adobe-sign-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Adobe Sign에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Adobe Sign 응용 프로그램에서 Single Sign-On을 구성합니다.

**Adobe Sign에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Adobe Sign** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. **Adobe Sign 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<companyname>.echosign.com/`

    나. **식별자** 텍스트 상자에서 `https://<companyname>.echosign.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [Adobe Sign 클라이언트 지원팀](https://helpx.adobe.com/in/contact/support.html)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. **Adobe Sign 구성** 섹션에서 **Adobe Sign 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. 구성을 시작하기 전에 [Adobe Sign Client 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 문의하여 Adobe Sign에서 사용자의 도메인을 허용 목록에 포함해야 합니다. 도메인을 추가하려면 아래 단계를 따르세요.

    a. [Adobe Sign Client 지원 팀](https://helpx.adobe.com/in/contact/support.html)이 임의로 생성된 토큰을 보냅니다. 사용자의 도메인에 대한 토큰은 다음과 같습니다. **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    나. DNS 텍스트 레코드에 확인 토큰을 게시하고 [Adobe Sign Client 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 알려야 합니다.
    
    > [!NOTE]
    > 며칠 이상 걸릴 수도 있습니다. DNS 전파 지연은 DNS에 게시된 값이 한 시간 이상 동안 보이지 않을 수도 있음을 의미합니다. IT 관리자는 이 토큰을 DNS 텍스트 레코드에 게시하는 방법을 알아야 합니다.
    
    다. 토큰이 게시된 후 지원 티켓을 통해 [Adobe Sign Client 지원 팀](https://helpx.adobe.com/in/contact/support.html)에 알리면 해당 팀이 도메인의 유효성을 검사한 후 사용자 계정에 추가합니다.
    
    d. DNS 레코드에 토큰을 게시하기 위해 수행할 수 있는 일반 단계는 다음과 같습니다.

    * 도메인 계정에 로그인합니다.
    * DNS 레코드를 업데이트할 페이지를 찾습니다. 이 페이지는 DNS 관리, 서버 관리자 이름 또는 고급 설정 페이지일 수 있습니다.
    * 도메인에 대한 TXT 레코드를 찾습니다.
    * Adobe에서 제공한 전체 토큰 값으로 TXT 레코드를 추가합니다.
    * 변경 내용을 저장합니다.

8. 다른 웹 브라우저 창에서 Adobe Sign 회사 사이트에 관리자로 로그인합니다.

9. SAML 메뉴에서 **계정 설정**을 클릭한 다음, **SAML 설정**을 클릭합니다.
   
    ![계정](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "계정")

10. **SAML 설정** 섹션에서 다음 단계를 수행합니다.
  
    ![SAML 설정](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "SAML 설정")
   
    a. **SAML 모드**로 **SAML 필수**를 선택합니다.
   
    나. **Adobe 서명 계정 관리자가 Adobe 서명 자격 증명을 사용하여 로그인할 수 있게 허용**을 선택합니다.
   
    다. **사용자 만들기**로 **SAML을 통해 인증된 사용자를 자동으로 추가**를 선택합니다.

    d. Azure Portal에서 복사한 **SAML 엔터티 ID**를 **엔터티 ID/발급자 URL** 텍스트 상자에 붙여넣습니다.
    
    e. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**을 **로그인 URL/SSO 엔드포인트** 텍스트 상자에 붙여넣습니다.
   
    f. Azure Portal에서 복사한 **로그아웃 URL**을 **로그아웃 URL/SLO 엔드포인트** 텍스트 상자에 붙여넣습니다.

    g. 다운로드된 **인증서(Base64)** 파일을 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **IdP 인증서** 텍스트 상자에 붙여 넣습니다.

    h. **변경 내용 저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-an-adobe-sign-test-user"></a>Adobe Sign 테스트 사용자 만들기

Azure AD 사용자가 Adobe Sign에 로그인할 수 있도록 하려면 Adobe Sign으로 프로비전되어야 합니다. Adobe Sign의 경우 프로비전은 수동 작업입니다.

>[!NOTE]
>다른 Adobe Sign 사용자 계정 생성 도구 또는 Adobe Sign이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다. 

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Adobe Sign** 회사 사이트에 관리자 권한으로 로그인합니다.

2. 위쪽의 메뉴에서 **계정**을 클릭한 다음 왼쪽의 탐색 창에서 **사용자 및 그룹**과 **새 사용자 만들기**를 차례로 클릭합니다.
   
    ![계정](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "계정")
   
3. **새 사용자 만들기** 섹션에서 다음 단계를 수행합니다.
   
    ![사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "사용자 만들기")
   
    a. 관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **전자 메일 주소**, **이름** 및 **성**을 입력합니다.
   
    나. **사용자 만들기**를 클릭합니다.

>[!NOTE]
>Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 이메일을 받습니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adobe Sign에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Adobe Sign에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Adobe Sign**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

액세스 패널에서 Adobe Sign 타일을 클릭하면 Adobe Sign 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png
