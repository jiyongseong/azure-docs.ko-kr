---
title: '자습서: OpsGenie와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 OpsGenie 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: b42320884f5478d05c305185334da42097f603ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>자습서: OpsGenie와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 OpsGenie를 통합하는 방법에 대해 알아봅니다.

OpsGenie를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- OpsGenie 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 OpsGenie(Single Sign-on)에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

OpsGenie와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- OpsGenie Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 OpsGenie 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-opsgenie-from-the-gallery"></a>갤러리에서 OpsGenie 추가
OpsGenie의 Azure AD 통합을 구성하려면 갤러리의 OpsGenie를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 OpsGenie를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **OpsGenie**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_search.png)

5. 결과 패널에서 **OpsGenie**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 OpsGenie에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 OpsGenie 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 OpsGenie의 관련 사용자 간에 연결이 형성되어야 합니다.

OpsGenie에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

OpsGenie에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[OpsGenie 테스트 사용자 만들기](#creating-a-opsgenie-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 OpsGenie에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 OpsGenie 응용 프로그램에서 Single Sign-On을 구성합니다.

**OpsGenie에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **OpsGenie** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

3. **OpsGenie 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_url.png)

    **로그온 URL** 텍스트 상자에서 URL `https://app.opsgenie.com/auth/login`을 입력합니다.

4. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_certificate.png)

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_general_400.png)

6. **OpsGenie 구성** 섹션에서 **OpsGenie 구성**을 클릭하여 **로그온 구성** 창을 엽니다. 빠른 참조 섹션에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_configure.png)

7. 다른 브라우저 인스턴스를 열고 관리자 권한으로 OpsGenie에 로그인합니다.

8. **설정**을 클릭하고 **Single Sign On** 탭을 클릭합니다.
   
    ![OpsGenie Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

9. SSO를 사용하려면 **사용**을 선택합니다.
   
    ![OpsGenie 설정](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 

10. **공급자** 섹션에서 **Azure Active Directory** 탭을 클릭합니다.
   
    ![OpsGenie 설정](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 

11. Azure Active Directory 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![OpsGenie 설정](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Azure Portal에서 복사한 **Single Sign-On 서비스 URL** 값을 **SAML 2.0 끝점** 텍스트 상자에 붙여넣습니다.
    
    나. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL:** 텍스트 상자에 붙여넣습니다.
    
    다. **변경 내용 저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-opsgenie-test-user"></a>OpsGenie 테스트 사용자 만들기

이 섹션은 OpsGenie에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. 

1. 웹 브라우저 창에서 OpsGenie의 테넌트에 관리자로 로그인합니다.

2. 왼쪽 패널에서 **사용자** 를 클릭하여 사용자 목록으로 이동합니다.
   
   ![OpsGenie 설정](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. **사용자 추가**를 클릭합니다.

4. **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.
   
   ![OpsGenie 설정](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. **메일** 텍스트 상자에 Azure Active Directory에 나열된 Britta Simon의 메일 주소를 입력합니다.
   
   나. **전체 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
   다. **저장**을 클릭합니다. 

>[!NOTE]
>Britta는 자신의 프로필 설정에 대한 지침이 포함된 메일을 받게 됩니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 OpsGenie에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 OpsGenie에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **OpsGenie**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.

액세스 패널에서 OpsGenie 타일을 클릭하면 OpsGenie 응용 프로그램에 자동으로 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png

