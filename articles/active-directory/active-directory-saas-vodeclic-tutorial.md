---
title: '자습서: Vodeclic과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Vodeclic 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: bc889919f2d869478843881cc8eae06fc9cb232c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>자습서: Vodeclic과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Vodeclic을 통합하는 방법에 대해 알아봅니다.

Vodeclic을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Vodeclic에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Vodeclic SSO(Single Sign-On)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Vodeclic과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Vodeclic SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 받으세요](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Vodeclic 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-vodeclic-from-the-gallery"></a>갤러리에서 Vodeclic 추가
Vodeclic의 Azure AD 통합을 구성하려면 갤러리의 Vodeclic을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리의 Vodeclic을 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Vodeclic**을 입력합니다. 결과 패널에서 **Vodeclic**을 선택한 다음, **추가** 단추를 선택하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Vodeclic에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Vodeclic 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Vodeclic의 관련 사용자 간에 연결이 형성되어야 합니다.

Vodeclic에서 Azure AD의 **사용자 이름**과 동일한 **Username** 값을 제공합니다. 이렇게 해서 두 사용자 간의 연결 관계를 형성했습니다.

Vodeclic에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [Vodeclic 테스트 사용자 만들기](#create-a-vodeclic-test-user) - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Vodeclic에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-on 테스트](#test-single-sign-on) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Vodeclic 응용 프로그램에서 Single Sign-On을 구성합니다.

**Vodeclic에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Vodeclic** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자의 **Single Sign-On 모드** 아래에서 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 **Vodeclic 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Vodeclic 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. **식별자** 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<companyname>.lms.vodeclic.net/auth/saml`

    나. **회신 URL** 상자에 다음 패턴으로 URL을 입력합니다. `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시** 확인란을 선택하고 다음 단계를 수행합니다.

    ![Vodeclic 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    **로그온 URL** 텍스트 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Vodeclic 클라이언트 지원 팀](mailto:hotline@vodeclic.com)에 문의하세요.

5. **SAML 서명 인증서** 섹션 아래에서 **메타데이터 XML**을 선택합니다. 그런 다음 메타데이터 파일을 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. **저장**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. **Vodeclic** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **메타데이터 XML**을 [Vodeclic 지원 팀](mailto:hotline@vodeclic.com)에 보내세요. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동합니다. 그런 다음 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-vodeclic-test-user"></a>Vodeclic 테스트 사용자 만들기

이 섹션에서는 Vodeclic에서 Britta Simon이라는 사용자를 만듭니다. [Vodeclic 지원 팀](mailto:hotline@vodeclic.com)에 문의하여 Vodeclic 플랫폼에 사용자를 추가하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

> [!NOTE]
> 응용 프로그램 요구 사항에 따라 사용 중인 컴퓨터를 허용 목록에 포함해야 할 수 있습니다. 이 경우 공용 IP 주소를 [Vodeclic 지원 팀](mailto:hotline@vodeclic.com)과 공유해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Vodeclic에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Vodeclic에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램**으로 이동한 다음 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Vodeclic**을 선택합니다.

    ![응용 프로그램 목록의 Vodeclic 링크](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 선택합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Vodeclic 타일을 선택하면 Vodeclic 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

