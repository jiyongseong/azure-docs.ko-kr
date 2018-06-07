---
title: '자습서: Azure Active Directory와 LaunchDarkly 통합 | Microsoft Docs'
description: Azure Active Directory 및 LaunchDarkly 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: f6611009ae10bf7a0d5c7f20f3ad26e9d41e456e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340712"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>자습서: Azure Active Directory와 LaunchDarkly 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LaunchDarkly를 통합하는 방법에 대해 알아봅니다.

LaunchDarkly를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- LaunchDarkly에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 LaunchDarkly에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

LaunchDarkly와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- LaunchDarkly Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 LaunchDarkly 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-launchdarkly-from-the-gallery"></a>갤러리에서 LaunchDarkly 추가
LaunchDarkly와 Azure AD의 통합을 구성하려면 갤러리의 LaunchDarkly를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 LaunchDarkly를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **LaunchDarkly**를 입력하고 결과 패널에서 **LaunchDarkly**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 LaunchDarkly에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 LaunchDarkly 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 LaunchDarkly의 관련 사용자 간에 연결이 형성되어야 합니다.

LaunchDarkly에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[LaunchDarkly 테스트 사용자 만들기](#create-a-launchdarkly-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 LaunchDarkly에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 LaunchDarkly 응용 프로그램에서 Single Sign-On을 구성합니다.

**LaunchDarkly에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **LaunchDarkly** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. **LaunchDarkly 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![LaunchDarkly 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. **식별자(엔터티 ID)** 텍스트 상자에 URL을 입력합니다. `app.launchdarkly.com`

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 회신 URL로 값을 업데이트하게 됩니다.

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![LaunchDarkly 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    **로그온 URL** 텍스트 상자에서 URL `https://app.launchdarkly.com`을 입력합니다.

5. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_400.png)
    
7. **LaunchDarkly 구성** 섹션에서 **LaunchDarkly 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **Single Sign-On 서비스 URL**을 복사합니다.

    ![LaunchDarkly 구성](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. 다른 웹 브라우저 창에서 LaunchDarkly 회사 사이트에 관리자로 로그인합니다.

9. 왼쪽 탐색 패널에서 **계정 설정**을 선택합니다.

    ![LaunchDarkly 구성](./media/active-directory-saas-launchdarkly-tutorial/configure1.png)

10. **보안** 탭을 클릭합니다.

    ![LaunchDarkly 구성](./media/active-directory-saas-launchdarkly-tutorial/configure2.png)

11. **ENABLE SSO**(SSO 사용)를 클릭한 다음, **EDIT SAML CONFIGURATION**(SAML 구성 편집)을 클릭합니다.

    ![LaunchDarkly 구성](./media/active-directory-saas-launchdarkly-tutorial/configure3.png)

12. **Edit your SAML configuration**(SAML 구성 편집) 섹션에서 다음 단계를 수행합니다.

    ![LaunchDarkly 구성](./media/active-directory-saas-launchdarkly-tutorial/configure4.png)

    a. 인스턴스에 대한 **SAML 소비자 서비스 URL**을 복사하여 Azure Portal의 **LaunchDarkly 도메인 및 URL** 섹션에서 [회신 URL] 텍스트 상자에 붙여넣습니다.

    나. **로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서를 메모장으로 열어 내용을 복사한 다음, **X.509 인증서** 상자에 붙여넣거나 **upload one**(업로드)을 클릭하여 인증서를 직접 업로드합니다.

    d. 페이지 맨 아래에 있는 **저장**

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-a-launchdarkly-test-user"></a>LaunchDarkly 테스트 사용자 만들기

이 섹션에서는 LaunchDarkly에서 Britta Simon이라는 사용자를 만듭니다. LaunchDarkly는 Just-In-Time 프로비저닝을 지원하며 기본적으로 사용하도록 설정합니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 LaunchDarkly에 액세스를 시도할 때 만들어집니다.
>[!Note]
>사용자를 수동으로 생성해야 하는 경우 [LaunchDarkly 클라이언트 지원 팀](mailto:support@launchdarkly.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 LaunchDarkly에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 LaunchDarkly에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **LaunchDarkly**를 선택합니다.

    ![응용 프로그램 목록의 LaunchDarkly 링크](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 LaunchDarkly 타일을 클릭하면 LaunchDarkly 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_203.png

