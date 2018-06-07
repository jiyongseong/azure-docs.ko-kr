---
title: '자습서: Azure Active Directory와 QPrism 통합 | Microsoft Docs'
description: Azure Active Directory와 QPrism 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: ffd7c828087162f83812da445b0eeb71545b59db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348987"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>자습서: QPrism과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 QPrism을 통합하는 방법에 대해 알아봅니다.

QPrism을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- QPrism에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 QPrism에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

QPrism와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- QPrism Single Sign-On이 설정된 구독

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 QPrism 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-qprism-from-the-gallery"></a>갤러리에서 QPrism 추가
QPrism의 Azure AD 통합을 구성하려면 갤러리의 QPrism을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 QPrism을 추가하려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램**을 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **QPrism**을 입력하고 결과 패널에서 **QPrism**을 선택합니다. 그런 후 **추가**를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 QPrism에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 QPrism 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 QPrism의 관련 사용자 간에 연결 관계가 있어야 합니다.

이러한 관계를 설정하려면 QPrism에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당합니다.

QPrism에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [QPrism 테스트 사용자 만들기](#create-a-qprism-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 QPrism에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-on 테스트](#test-single-sign-on) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 QPrism 응용 프로그램에서 Single Sign-On을 구성합니다.

1. Azure Portal의 **QPrism** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. **QPrism 도메인 및 URL** 섹션에서 다음을 수행합니다.

    ![QPrism 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<customer domain>.qmyzone.com/login`

    나. **식별자** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 로그온 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [QPrism 클라이언트 지원 팀](mailto:qsupport-ce@quatrro.com)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

     ![인증서 다운로드 링크](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_certificate.png)

5. **저장**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. **QPrism** 쪽에서 Single Sign-On을 구성하려면 **앱 페더레이션 메타데이터 URL**을 [QPrism 지원 팀](mailto:qsupport-ce@quatrro.com)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음을 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-qprism-test-user"></a>QPrism 테스트 사용자 만들기

이 섹션에서는 QPrism에서 Britta Simon이라는 사용자를 만듭니다. QPrism 플랫폼에서 사용자를 추가하려면 [QPrism 지원 팀](mailto:qsupport-ce@quatrro.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 QPrism에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 QPrism에 할당하려면**

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램**으로 이동한 다음 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **QPrism**을 선택합니다.

    ![응용 프로그램 목록의 QPrism 링크](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가**를 선택합니다. 그런 후 **할당 추가**에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택**을 선택합니다.

7. **할당 추가**에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 QPrism 타일을 선택하면 QPrism 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

