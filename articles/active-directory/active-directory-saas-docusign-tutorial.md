---
title: '자습서: DocuSign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 DocuSign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 28c46e7071841552c5bcbf3df38d9d7771b0cfe6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345751"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>자습서: DocuSign와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 DocuSign을 통합하는 방법에 대해 알아봅니다.

DocuSign을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- DocuSign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 DocuSign에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

DocuSign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- DocuSign Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 DocuSign 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-docusign-from-the-gallery"></a>갤러리에서 DocuSign 추가
DocuSign의 Azure AD 통합을 구성하려면 갤러리의 DocuSign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 DocuSign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **DocuSign**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. 결과 패널에서 **DocuSign**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 DocuSign 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 DocuSign의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 DocuSign의 **Username** 값으로 할당하여 설정합니다.

DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[DocuSign 테스트 사용자 만들기](#creating-a-docusign-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 DocuSign에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 DocuSign 응용 프로그램에서 Single Sign-On을 구성합니다.

**DocuSign에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **DocuSign** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. **SAML 서명 인증서** 섹션에서 **인증서(Base 64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Azure Portal의 **DocuSign 구성** 섹션에서 **DocuSign 구성**을 클릭하여 로그온 구성 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. 다른 웹 브라우저 창에서 **DocuSign 관리 포털**에 관리자로 로그인합니다.

6. 왼쪽 탐색 메뉴에서 **도메인**을 클릭합니다.
   
    ![Single Sign-On 구성][51]

7. 오른쪽 창에서 **도메인 클레임**을 클릭합니다.
   
    ![Single Sign-On 구성][52]

8. **도메인 클레임** 대화 상자의 **도메인 이름** 텍스트 상자에 회사 도메인을 입력한 다음 **클레임**을 클릭합니다. 도메인을 확인하고 상태가 활성인지 확인합니다.
   
    ![Single Sign-On 구성][53]

9. 왼쪽 메뉴에서 **ID 공급자**  
   
    ![Single Sign-On 구성][54]
10. 오른쪽 창에서 **ID 공급자 추가**를 클릭합니다. 
   
    ![Single Sign-On 구성][55]

11. **ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][56]

    a. **이름** 텍스트 상자에 구성할 고유한 이름을 입력합니다. 공백을 사용하지 마세요.

    나. **SAML 엔터티 ID**를 **ID 공급자 발급자** 텍스트 상자에 붙여넣습니다.

    다. **SAML Single Sign-On 서비스 URL**을 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다.

    d. **로그아웃 URL**을 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. **Sign AuthN 요청**을 선택합니다.

    f. **AuthN 요청 보내기**로 **POST**를 선택합니다.

    g. **로그아웃 요청 보내기**로 **GET**을 선택합니다.

12. **사용자 지정 특성 매핑** 섹션에서 Azure AD 클레임을 사용하여 매핑하려는 필드를 선택합니다. 이 예제의 **emailaddress** 클레임은 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 값에 매핑됩니다. 이는 Azure AD의 메일 클레임에 대한 기본 클레임 이름입니다. 
   
    > [!NOTE]
    > 적절한 **사용자 ID**를 사용하여 Azure AD에서 DocuSign 사용자 매핑으로 사용자를 매핑합니다. 적절한 필드를 선택하고 조직 설정에 따라 적절한 값을 입력합니다.
          
    ![Single Sign-On 구성][57]

13. **ID 공급자 인증서** 섹션에서 **인증서 추가**를 클릭하고 Azure AD 포털에서 다운로드한 인증서를 업로드합니다.   
   
    ![Single Sign-On 구성][58]

14. **저장**을 클릭합니다.

15. **ID 공급자** 섹션에서 **작업**을 클릭한 다음 **끝점**을 클릭합니다.   
   
    ![Single Sign-On 구성][59]
 
16. **DocuSign 관리자 포털**의 **SAML 2.0 끝점 보기** 섹션에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][60]
   
    a. **서비스 공급자 발급자 URL**을 복사하여 Azure Portal **DocuSign 도메인 및 URL** 섹션의 **식별자** 텍스트 상자에 `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>` 패턴에 따라 붙여넣습니다.
   
    나. **서비스 공급자 로그인 URL**을 복사하여 Azure Portal **DocuSign 도메인 및 URL** 섹션의 **로그온 URL** 텍스트 상자에 `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`패턴에 따라 붙여넣습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    다.  페이지 맨 아래에 있는 **닫기**
    
17. Azure Portal에서 **저장**을 클릭합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-docusign-test-user"></a>DocuSign 테스트 사용자 만들기

응용 프로그램이 **Just-In-Time 사용자 프로비전**을 지원하며 인증 후에는 응용 프로그램에서 자동으로 사용자가 생성됩니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 DocuSign에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 DocuSign에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **DocuSign**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 DocuSign 타일을 클릭하면 DocuSign 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

