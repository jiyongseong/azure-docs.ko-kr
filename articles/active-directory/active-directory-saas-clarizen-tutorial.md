---
title: '자습서: Clarizen과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Clarizen 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 8af9e7023142077fdeed0262e5f39921b158a14f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>자습서: Clarizen과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Clarizen을 통합하는 방법에 대해 알아봅니다. 이 통합은 다음과 같은 이점을 제공합니다.

- Clarizen에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Clarizen에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

이 자습서의 시나리오는 다음 두 가지 주요 작업으로 이루어져 있습니다.

1. 갤러리에서 Clarizen을 추가합니다.
2. Azure AD Single Sign-On을 구성하고 테스트합니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Clarizen과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Single Sign-On이 활성화된 Clarizen 구독

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마십시오.
- Azure AD 테스트 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="add-clarizen-from-the-gallery"></a>갤러리에서 Clarizen 추가
Clarizen의 Azure AD 통합을 구성하려면 갤러리의 Clarizen을 관리되는 SaaS 앱 목록에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 아이콘][1]

2. **엔터프라이즈 응용 프로그램**을 클릭합니다. 그런 후 **모든 응용 프로그램**을 클릭합니다.

    ![“엔터프라이즈 응용 프로그램” 및 “모든 응용 프로그램” 클릭][2]

3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![“추가” 단추][3]

4. 검색 상자에 **Clarizen**을 입력합니다.

    ![검색 상자에 “Clarizen” 입력](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. 결과 창에서 **Clarizen**을 선택하고 **추가**를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 창에서 Clarizen 선택](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
다음 섹션에서는 테스트 사용자 Britta Simon을 기준으로 Clarizen에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Clarizen 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Clarizen의 관련 사용자 간에 연결이 형성되어야 합니다. 이 연결 관계는 Azure AD의 **사용자 이름** 값을 Clarizen의 **Username** 값으로 할당하여 설정합니다.

Clarizen에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Clarizen 테스트 사용자 만들기](#create-a-clarizen-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Clarizen에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Clarizen 응용 프로그램에서 Single Sign-On을 구성합니다.

1. Azure Portal의 **Clarizen** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    !["Single sign-on" 클릭][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.

    !["SAML 기반 로그온" 선택](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. **Clarizen 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![식별자 및 회신 URL 상자](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. **식별자** 상자에 해당 값으로 **Clarizen**을 입력합니다.

    나. **회신 URL** 상자에 **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx** 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL을 사용해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. 실제 값을 확인하려면 [Clarizen 지원 팀](https://success.clarizen.com/hc/en-us/requests/new)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기**를 클릭합니다.

    !["새 인증서 만들기" 클릭](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. **새 인증서 만들기** 대화 상자에서 달력 아이콘을 클릭하고 만료 날짜를 선택합니다. 그런 다음 **Save**를 클릭합니다.

    ![만료 날짜 선택 및 저장](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. **SAML 서명 인증서** 섹션에서 **새 인증서 활성화**를 선택한 후 **저장** 단추를 클릭합니다.

    ![새 인증서를 활성화하기 위한 확인란 선택](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. **롤오버 인증서** 대화 상자에서 **확인**을 클릭합니다.

    !["확인"을 클릭하여 인증서를 활성화할지 확인](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    !["인증서(Base64)"를 클릭하여 다운로드 시작](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. **Clarizen 구성** 섹션에서 **Clarizen 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    !["Clarizen 구성" 클릭](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![파일 및 URL을 포함하는 "로그온 구성" 창](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. 다른 웹 브라우저 창에서 Clarizen 회사 사이트에 관리자로 로그인합니다.

11. 사용자 이름을 클릭한 다음 **설정**을 클릭합니다.

    ![사용자 이름 아래에서 "설정" 클릭](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "설정")

12. **전역 설정** 탭을 클릭합니다. 그런 다음 **페더레이션 인증** 옆에 있는 **편집**을 클릭합니다.

    ![“전역 설정” 탭](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "전역 설정")

13. **페더레이션 인증** 대화 상자에서 다음 단계를 수행합니다.

    ![“페더레이션 인증” 대화 상자](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "페더레이션 인증")

    a. **페더레이션 인증 사용**을 선택합니다.

    나. 다운로드한 인증서를 업로드하려면 **업로드** 를 클릭합니다.

    다. **로그인 URL** 상자에 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-on 서비스 URL** 값을 입력합니다.

    d. **로그아웃 URL** 상자에 Azure AD 응용 프로그램 구성 창의 **로그아웃 URL** 값을 입력합니다.

    e. **POST 사용**을 선택합니다.

    f. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 테스트 사용자의 이름 및 전자 메일 주소][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 아이콘](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. **사용자 및 그룹**을 클릭하고 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 클릭](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.

    ![“추가” 단추](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![이름, 전자 메일 주소 및 암호가 입력된 "사용자" 대화 상자](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-a-clarizen-test-user"></a>Clarizen 테스트 사용자 만들기
Azure AD 사용자가 Clarizen에 로그인할 수 있도록 하려면 사용자 계정을 프로비전해야 합니다. Clarizen의 경우 프로비전은 수동 작업입니다.

1. Clarizen 회사 사이트에 관리자 권한으로 로그인합니다.

2. **피플**을 클릭합니다.

    !["피플" 클릭](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "피플")

3. **사용자 초대**를 클릭합니다.

    !["사용자 초대" 단추](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "사용자 초대")

4. **피플 초대** 대화 상자에서 다음 단계를 수행합니다.

    !["피플 초대" 대화 상자](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "피플 초대")

    a. **전자 메일** 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    나. **초대**를 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Clarizen에 대한 액세스 권한을 부여합니다.

![할당된 테스트 사용자][200]

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동한 후 **엔터프라이즈 응용 프로그램**을 클릭하고 **모든 응용 프로그램**을 클릭합니다.

    ![“엔터프라이즈 응용 프로그램” 및 “모든 응용 프로그램” 클릭][201]

2. 응용 프로그램 목록에서 **Clarizen**을 선택합니다.

    ![목록에서 Clarizen 선택](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. 왼쪽 창에서 **사용자 및 그룹**을 클릭합니다.

    ![“사용자 및 그룹” 클릭][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    !["추가" 단추 및 "할당 추가" 대화 상자][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Clarizen 타일을 클릭하면 Clarizen 응용 프로그램에 자동으로 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
