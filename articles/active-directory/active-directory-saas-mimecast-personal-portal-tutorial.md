---
title: '자습서: Mimecast Personal Portal과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Mimecast Personal Portal 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 4b976481e60926e6ab95a679e6dcc8aa0818b48b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>자습서: Mimecast Personal Portal과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Mimecast Personal Portal을 통합하는 방법에 대해 알아봅니다.

Mimecast Personal Portal을 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Mimecast Personal Portal에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Mimecast Personal Portal에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Mimecast Personal Portal과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Mimecast Personal Portal에서 Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Mimecast Personal Portal 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>갤러리에서 Mimecast Personal Portal 추가
Mimecast Personal Portal이 Azure AD에 통합되도록 구성하려면 갤러리에서 Mimecast Personal Portal을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Mimecast Personal Portal을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **Mimecast Personal Portal**을 입력하고, 결과 패널에서 **Mimecast Personal Portal**을 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Mimecast Personal Portal에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 Mimecast Personal Portal 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Mimecast Personal Portal의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Mimecast Personal Portal에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Mimecast Personal Portal 테스트 사용자 만들기](#create-a-mimecast-personal-portal-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Mimecast Personal Portal에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Mimecast Personal Portal 응용 프로그램에서 Single Sign-On을 구성합니다.

**Mimecast Personal Portal에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Mimecast Personal Portal** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. **Mimecast Personal Portal 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Mimecast Personal Portal 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. **로그온 URL** 텍스트 상자에서 URL을 입력합니다. 

    | 지역  |  값 | 
    | --------------- | --------------- | 
    | 유럽          | `https://eu-api.mimecast.com/login/saml`|
    | 미국   | `https://us-api.mimecast.com/login/saml`|
    | 남아프리카    | `https://za-api.mimecast.com/login/saml`|
    | 오스트레일리아       | `https://au-api.mimecast.com/login/saml`|
    | 역외        | `https://jer-api.mimecast.com/login/saml`|

    나. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.

    | 지역  |  값 | 
    | --------------- | --------------- |
    | 유럽          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 미국   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | 남아프리카    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 오스트레일리아       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 역외        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    다. **회신 URL** 텍스트 상자에서 URL을 입력합니다. 

    | 지역  |  값 | 
    | --------------- | --------------- | 
    | 유럽          | `https://eu-api.mimecast.com/login/saml`|
    | 미국   | `https://us-api.mimecast.com/login/saml`|
    | 남아프리카    | `https://za-api.mimecast.com/login/saml`|
    | 오스트레일리아       | `https://au-api.mimecast.com/login/saml`|
    | 역외        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > 식별자 값은 실제 값이 아닙니다. 실제 식별자로 값을 업데이트하세요. 이러한 값을 얻으려면 [Mimecast Personal Portal 클라이언트 지원 팀](http://www.mimecast.com/customer-success/technical-support/)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. **Mimecast Personal Portal 구성** 섹션에서 **Mimecast Personal Portal 구성**을 클릭하고 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Mimecast Personal Portal 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. 다른 웹 브라우저 창에서 Mimecast Personal Portal 포털에 관리자로 로그인합니다.

8. **서비스 \> 응용 프로그램**으로 이동합니다.
   
    ![응용 프로그램](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "응용 프로그램")

9. **인증 프로필**을 클릭합니다.
   
    ![인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "인증 프로필")

10. **새 인증 프로필**을 클릭합니다.
   
    ![새 인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "새 인증 프로필")

11. **인증 프로필** 섹션에서 다음 단계를 수행합니다.
   
    ![인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "인증 프로필")
   
    a. **설명** 텍스트 상자에 구성 이름을 입력합니다.
   
    나. **Mimecast Personal Portal에 대한 SAML 인증 적용**을 선택합니다.
   
    다. **공급자**로 **Azure Active Directory**를 선택합니다.
   
    d. **발급자 URL** 텍스트 상자에서 Azure Portal로부터 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.
   
    e. **로그인 URL** 텍스트 상자에서 Azure Portal로부터 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.
   
    f. **로그아웃 URL** 텍스트 상자에서 Azure Portal로부터 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    g. Azure Portal에서 다운로드한 **base-64**로 인코딩된 인증서를 메모장에서 열고 콘텐츠를 클립보드에 복사한 다음 **Identity Provider Certificate (Metadata)**(ID 공급자 인증서)(Metadata) 텍스트 상자에 붙여넣습니다.

    h. **Single Sign-On 허용**을 선택합니다.
   
    i. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Mimecast Personal Portal 테스트 사용자 만들기

Azure AD 사용자가 Mimecast Personal Portal에 로그인하게 하려면 Mimecast Personal Portal에 프로비전해야 합니다. Mimecast Personal Portal의 경우 프로비전은 수동 작업입니다.

사용자를 만들려면 먼저 도메인을 등록해야 합니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. **Mimecast Personal Portal** 에 관리자 권한으로 로그인합니다.

2. **디렉터리 \> 내부**로 이동합니다.
   
    ![디렉터리](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "디렉터리")

3. **새 도메인에 등록**을 클릭합니다.
   
    ![새 도메인에 등록](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "새 도메인에 등록")

4. 새 도메인을 만든 후 **새 주소**를 클릭합니다.
   
    ![새 주소](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "새 주소")

5. 새 주소 대화 상자에서 프로비전할 유효한 Azure AD 계정에 대한 다음 단계를 수행합니다.
   
    ![저장](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "저장")
   
    a. **이메일 주소** 텍스트 상자에 사용자의 **이메일 주소**를 **BrittaSimon@contoso.com**으로 입력합니다.
    
    나. **Global Name**(전역 이름) 텍스트 상자에 **사용자 이름**을 **BrittaSimon**으로 입력합니다.

    다. **암호** 및 **암호 확인** 텍스트 상자에 사용자의 **암호**를 입력합니다.
   
    나. **저장**을 클릭합니다.

>[!NOTE]
>Mimecast Personal Portal 사용자 계정 생성 도구 또는 Mimecast Personal Portal에서 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Mimecast Personal Portal에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Mimecast Personal Portal에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Mimecast Personal Portal**을 선택합니다.

    ![응용 프로그램 목록의 Mimecast Personal Portal 링크](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Mimecast Personal Portal 타일을 클릭하면 Mimecast Personal Portal 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

