---
title: '자습서: SignalFx와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SignalFx 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 67e9bb69e1e8741e4a4ff7e7b78cb06856d2450f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>자습서: SignalFx와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SignalFx를 통합하는 방법에 대해 알아봅니다.

SignalFx와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- SignalFx에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SignalFx에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SignalFx와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SignalFx Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SignalFx 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-signalfx-from-the-gallery"></a>갤러리에서 SignalFx 추가
SignalFx의 Azure AD의 통합을 구성하려면 갤러리의 SignalFx를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SignalFx를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **SignalFx**를 입력하고, 결과 패널에서 **SignalFx**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 SignalFx에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SignalFx 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SignalFx의 관련 사용자 간의 연결 관계가 설정되어야 합니다.

SignalFx에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SignalFx 테스트 사용자 만들기](#create-a-signalfx-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SignalFx에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고, SignalFx 응용 프로그램에서 Single Sign-On을 구성합니다.

**SignalFx에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SignalFx** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. **SignalFx 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![SignalFx 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. **식별자** 텍스트 상자에 URL `https://api.signalfx.com/v1/saml/metadata`를 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > 위의 값은 실제 값이 아닙니다. 값을 자습서 뒷부분에서 설명하는 실제 회신 URL로 업데이트합니다.

4. SignalFx 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.   

    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Single Sign-On 구성 추가](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Single Sign-On Addattb](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스**를 비워 둡니다.
    
    e. **Ok**를 클릭합니다.
 
6. **SAML 서명 인증서** 섹션에서 다음 단계를 수행합니다. 

    ![인증서 다운로드 링크](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. 복사 단추를 클릭하고 **앱 페더레이션 메타데이터 URL**을 복사하여 메모장에 붙여넣습니다.

    나. **인증서(Base64)** 를 클릭한 다음, 컴퓨터에 인증서 파일을 저장합니다.

7. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. **SignalFx 구성** 섹션에서 **SignalFx 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID**를 복사합니다.

    ![SignalFx 구성](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

9. SignalFx 회사 사이트에 관리자 권한으로 로그온합니다.

10. SignalFx의 위쪽에서 **통합**을 클릭하여 통합 페이지를 엽니다.

    ![SignalFx 통합](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

11. **로그인 서비스** 섹션 아래에서 **Azure Active Directory** 타일을 클릭합니다.
 
    ![SignalFx SAML](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

12. **새 통합**을 클릭하고 **설치** 탭 아래에서 다음 단계를 수행합니다.
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. **이름** 텍스트 상자에서 새 통합 이름(예: **OurOrgName SAML SSO**)을 입력합니다.

    나. **통합 ID** 값을 복사하고, Azure Portal의 **SignalFx 도메인 및 URL** 섹션에 있는 **회신 URL** 텍스트 상자에서 **회신 URL**(예: `https://api.signalfx.com/v1/saml/acs/<integration ID>`)을 추가합니다.

    다. Azure Portal에서 다운로드한 **Base64로 인코딩된 인증서**를 **인증서** 텍스트 상자에 업로드하려면 **파일 업로드**를 클릭합니다.

    d. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **발급자 URL** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣습니다.

    f. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
  
### <a name="create-a-signalfx-test-user"></a>SignalFx 테스트 사용자 만들기

이 섹션은 SignalFx에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. SignalFx는 JIT(Just-In-Time) 프로비전을 지원하며, 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. 아직 존재하지 않는 경우 SignalFx에 액세스하려고 하는 동안 새 사용자가 만들어집니다.

사용자가 SAML SSO에서 처음으로 SignalFx에 로그인하면, [SignalFx 지원 팀](mailto:kmazzola@signalfx.com)에서 인증을 위해 클릭해야 하는 링크가 포함된 이메일을 보냅니다. 이는 사용자가 처음 로그인할 때만 발생합니다. 이후의 로그인 시도에는 이메일 유효성 검사가 필요하지 않습니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [SignalFx 지원 팀](mailto:kmazzola@signalfx.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SignalFx에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 SignalFx에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SignalFx**를 선택합니다.

    ![응용 프로그램 목록의 SignalFx 링크](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SignalFx 타일을 클릭하면 SignalFx 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png

