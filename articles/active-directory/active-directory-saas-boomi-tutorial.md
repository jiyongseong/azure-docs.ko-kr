---
title: '자습서: Boomi와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Boomi 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: bd0b5cd66718ca0eeb9fa90366b90a899181e912
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>자습서: Boomi와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Boomi를 통합하는 방법에 대해 알아봅니다.

Boomi를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Boomi에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Boomi에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Boomi와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Boomi Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Boomi 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-boomi-from-the-gallery"></a>갤러리에서 Boomi 추가
Boomi의 Azure AD 통합을 구성하려면 갤러리의 Boomi를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Boomi를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Boomi**를 입력하고 결과 패널에서 **Boomi**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Boomi](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Boomi에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Boomi 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Boomi의 관련 사용자 간에 연결이 형성되어야 합니다.

Boomi에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Boomi에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Boomi 테스트 사용자 만들기](#create-a-boomi-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Boomi에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Boomi 응용 프로그램에서 Single Sign-On을 구성합니다.

**Boomi에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Boomi** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. **Boomi 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Boomi 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. **식별자** 텍스트 상자에 URL `https://platform.boomi.com/`를 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 이 값을 가져오려면 [Boomi 지원 팀](https://boomi.com/company/contact/)에 문의합니다.
 
4. Boomi 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대한 다음 클레임을 구성하세요. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. **Single sign on** 대화 상자의 **사용자 특성** 섹션에서 아래 표에 표시되는 각 행에 대해 다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_officespace_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.

6. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png) 

7. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

8. **Boomi 구성** 섹션에서 **Boomi 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Boomi 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

9. 다른 웹 브라우저 창에서 Boomi 회사 사이트에 관리자로 로그인합니다. 

10. **회사 이름**, **설정**으로 이동합니다.

11. **SSO 옵션** 탭을 클릭하고 아래 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. **SAML Single Sign-On 사용** 확인란을 선택합니다.

    나. **가져오기**를 클릭하여 Azure AD에서 다운로드한 인증서를 **ID 공급자 인증서**로 업로드합니다.
    
    다. **ID 공급자 로그인 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-On 서비스 URL** 값을 입력합니다.

    d. **페더레이션 ID 위치**로 **페더레이션 ID는 FEDERATION_ID 특성 요소입니다** 라디오 단추를 선택합니다. 

    e. **저장** 단추를 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
  
### <a name="create-a-boomi-test-user"></a>Boomi 테스트 사용자 만들기

Azure AD 사용자가 Boomi에 로그인할 수 있도록 하려면 Boomi로 프로비전되어야 합니다. Boomi의 경우 프로비전은 수동 작업입니다.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. Boomi 회사 사이트에 관리자로 로그인합니다.

2. 로그인 후 **사용자 관리**, **사용자**로 이동합니다.

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "사용자")

3. **+** 아이콘을 클릭하면 **사용자 역할 추가/유지 관리** 대화 상자가 열립니다.

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "사용자")

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "사용자")

    a. **사용자 메일 주소** 텍스트 상자에서 BrittaSimon@contoso.com과 같은 사용자의 메일 주소를 입력합니다.
    
    나. **이름** 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.
    
    d. 사용자의 **페더레이션 ID**를 입력합니다. 각 사용자는 계정 내에서 사용자를 고유하게 식별하는 페더레이션 ID가 있어야 합니다.
    
    e. **표준 사용자** 역할을 사용자에게 할당합니다. 관리자 역할은 일반적인AtomSphere 액세스 권한과 Single Sign-On 액세스 권한을 모두 제공하므로 이 역할을 할당하지 마세요.
    
    f. **확인**을 클릭합니다.
    
    > [!NOTE]
    > 사용자는 해당 암호가 ID 공급자를 통해 관리되므로 AtomSphere 계정에 로그인하는 데 사용할 수 있는 암호가 포함된 시작 알림 메일이 제공되지 않습니다. 다른 Boomi 사용자 계정 생성 도구 또는 Boomi가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Boomi에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Boomi에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Boomi**를 선택합니다.

    ![응용 프로그램 목록의 Boomi 링크](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Boomi 타일을 클릭하면 Boomi 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png

