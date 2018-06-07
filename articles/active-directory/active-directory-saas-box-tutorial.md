---
title: '자습서: Box와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Box 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Box와 Azure Active Directory 통합

이 자습서에서는 Box와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다.

Box와 Azure AD를 통합하면 다음과 같은 혜택이 있습니다.

- Box에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Box에 자동으로 로그인(Single Sign-On 또는 SSO)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Box와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Box SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하는 것은 바람직하지 *않습니다*.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Box 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-box-from-the-gallery"></a>갤러리에서 Box 추가
Box와 Azure AD를 통합하도록 구성하려면 다음을 수행하여 갤러리에서 Box를 관리되는 SaaS 앱 목록에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 창][2]
    
3. 새 응용 프로그램을 추가하려면 이 창의 맨 위쪽에 있는 **새 응용 프로그램** 단추를 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **Box**를 입력하고, 결과 목록에서 **Box**를 선택한 다음, **추가**를 선택합니다.

    ![결과 목록의 Box](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Box에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Box 사용자와 이에 해당하는 Azure AD의 사용자에 대해 알고 있어야 합니다. 즉, Azure AD 사용자와 Box의 동일한 사용자 간에 연결 관계가 형성되어야 합니다.

연결 관계를 설정하려면 Azure AD의 *사용자 이름* 값을 Box *Username*으로 할당합니다.

Box에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 5개 섹션에서 구성 요소를 완료해야 합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

다음을 수행하여 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Box 응용 프로그램에서 Single Sign-On을 구성합니다.

1. Azure Portal의 **Box** 응용 프로그램 통합 창에서 **Single Sign-On**을 선택합니다.

    ![“Single Sign-On” 링크][4]

2. **Single Sign-On** 창의 **Single Sign-On 모드** 상자에서 **SAML 기반 로그온**을 선택합니다.
 
    ![“Single Sign-On” 창](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. **Box 도메인 및 URL** 아래에서 다음을 수행합니다.

    !["Box 도메인 및 URL" Single Sign-On 정보](./media/active-directory-saas-box-tutorial/url3.png)

    a. **로그온 URL** 상자에 *https://\<subdomain>.box.com*과 같은 형식의 URL을 입력합니다.

    나. **식별자** 텍스트 상자에 **box.net**을 입력합니다.
     
    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트합니다. 값을 가져오려면 [Box 클라이언트 지원 팀](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)에 문의하세요. 

4. **SAML 서명 인증서** 아래에서 **메타데이터 XML**을 선택한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. **저장**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. 응용 프로그램에 대한 SSO를 구성하려면 [사용자 고유의 SSO 설정](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown)의 절차에 따릅니다.

> [!NOTE] 
> Box 계정에 SSO를 사용하도록 설정할 수 없는 경우 [Box 클라이언트 지원 팀](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)에 연락해서 다운로드한 XML 파일을 제공해야 합니다.

> [!TIP]
> 앱을 설정할 때 [Azure Portal](https://portal.azure.com)에서 이전 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에 **Single Sign-On** 탭을 선택하고 창 맨 아래에 있는 **구성** 섹션에서 포함된 설명서에 액세스합니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
>

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![Azure AD 테스트 사용자 만들기][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 링크](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. 현재 사용자 목록을 표시하려면 **사용자 및 그룹** > **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. **모든 사용자** 창 상단에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    **사용자** 창이 열립니다.

4. **사용자** 창에서 다음을 수행합니다.

    ![사용자 창](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-box-test-user"></a>Box 테스트 사용자 만들기

이 섹션에서는 Box에서 테스트 사용자인 Britta Simon을 만듭니다. Box는 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다. 사용자가 존재하지 않는 경우 Box에 액세스하려고 할 때 새 사용자를 만듭니다. 사용자를 만들기 위해 별다른 조치를 취할 필요가 없습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 사용자인 Britta Simon에게 Box에 대한 액세스 권한을 부여합니다. 이렇게 하려면 다음을 수행합니다.

![사용자 역할 할당][200]

1. Azure Portal에서 **응용 프로그램** 보기를 열고 **디렉터리** 보기로 이동한 후 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 및 "모든 응용 프로그램" 링크][201] 

2. **응용 프로그램** 목록에서 **Box**를 선택합니다.

    ![Box 링크](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가**를 선택한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 창의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **선택** 단추를 선택합니다.

7. **할당 추가** 창에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **Box** 타일을 선택하는 경우 Box 응용 프로그램에 로그인하는 로그인 페이지를 엽니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
* [사용자 프로비전 구성](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

