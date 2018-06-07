---
title: '자습서: Lifesize Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Lifesize Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 84af3a9b726d990e585e2b12b1c0a6f4609fcb7e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344421"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>자습서: Lifesize Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Lifesize Cloud를 통합하는 방법에 대해 알아봅니다.

Lifesize Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Lifesize Cloud에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Lifesize Cloud에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Lifesize Cloud와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Lifesize Cloud Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Lifesize Cloud 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-lifesize-cloud-from-the-gallery"></a>갤러리에서 Lifesize Cloud 추가
Lifesize Cloud와 Azure AD의 통합을 구성하려면 갤러리의 Lifesize Cloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Lifesize Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Lifesize Cloud**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. 결과 패널에서 **Lifesize Cloud**를 선택하고 **추가** 단추를 클릭하여 해당 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Lifesize Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Lifesize Cloud 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Lifesize Cloud의 관련 사용자 간에 연결이 형성되어야 합니다.

Lifesize Cloud에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Lifesize Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Lifesize Cloud 테스트 사용자 만들기](#creating-a-lifesize-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Lifesize Cloud에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Lifesize Cloud 응용 프로그램에서 Single Sign-On을 구성합니다.

**Lifesize Cloud에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Lifesize Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. **Lifesize Cloud 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://login.lifesizecloud.com/ls/?acs`

    나. **식별자** 텍스트 상자에서 `https://login.lifesizecloud.com/<companyname>` 패턴을 사용하여 URL을 입력합니다.

     
4. **고급 URL 설정 표시**를 확인하고, 다음 단계를 수행합니다.    
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    **릴레이 상태** 텍스트 상자에서 `https://webapp.lifesizecloud.com/?ent=<identifier>` 패턴을 사용하여 URL을 입력합니다.
   
   > [!NOTE] 
   >이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 릴레이 상태 및 식별자로 값을 업데이트해야 합니다. 로그온 URL 및 식별자 값을 얻으려면 [Lifesize Cloud 클라이언트 지원 팀](https://www.lifesize.com/support)에 문의하세요. 자습서 뒷부분에서 설명하는 SSO 구성에서 릴레이 상태 값을 얻을 수 있습니다.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. **Lifesize Cloud 구성** 섹션에서 **Lifesize Cloud 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 **SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. 응용 프로그램에 대해 구성된 SSO를 가져오려면 관리자 권한으로 Lifesize Cloud 응용 프로그램에 로그인합니다.

8. 페이지의 오른쪽 위 모서리에 있는 사용자 이름을 클릭하고 **고급 설정**을 클릭합니다.
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. 고급 설정에서 이제 **SSO 구성** 링크를 클릭합니다. 현재 인스턴스에 대한 SSO 구성 페이지가 열립니다.
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. 이제 SSO 구성 UI에서 다음 값을 구성합니다.    
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **ID 공급자 발급자** 텍스트 상자에 붙여넣습니다.

    나.  Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음, **X.509 인증서** 텍스트 상자에 붙여넣습니다.
  
    d. 이름 텍스트 상자의 SAML 특성 매핑에 값을 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**으로 입력합니다.
    
    e. **성** 텍스트 상자의 SAML 특성 매핑에 값을 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**으로 입력합니다.
    
    f. **이메일** 텍스트 상자의 SAML 특성 매핑에 값을 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**로 입력합니다.

11. 구성을 확인하려면 **테스트** 단추를 클릭합니다.
   
    >[!NOTE]
    >테스트에 성공하려면 Azure AD에서 구성 마법사를 완료하고 테스트를 수행할 수 있는 사용자 또는 그룹에 대한 액세스를 제공해야 합니다.

12. **SSO 사용** 단추를 선택하여 SSO를 사용하도록 설정합니다.

13. 이제 모든 설정을 저장할 수 있도록 **업데이트** 단추를 클릭합니다. 그러면 RelayState 값이 생성됩니다. 텍스트 상자에 생성된 RelayState 값을 복사하여 **Lifesize Cloud 도메인 및 URL** 섹션의 **릴레이 상태** 텍스트 상자에 붙여넣습니다. 

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Lifesize Cloud 테스트 사용자 만들기

이 섹션에서는 Lifesize Cloud에서 Britta Simon이라는 사용자를 만듭니다. Lifesize Cloud는 자동 사용자 프로비전을 지원하지 않습니다. Azure AD에서 인증에 성공한 사용자는 응용 프로그램에서 자동으로 프로비전됩니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Lifesize Cloud에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Lifesize Cloud에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Lifesize Cloud**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Lifesize Cloud 타일을 클릭하면 Lifesize Cloud 응용 프로그램의 로그인 페이지가 표시됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

