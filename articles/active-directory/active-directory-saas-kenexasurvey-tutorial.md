---
title: '자습서: IBM Kenexa Survey Enterprise와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 IBM Kenexa Survey Enterprise 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 88e0072adeeebaf6c9e964db28a45f6fe038fddf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>자습서: IBM Kenexa Survey Enterprise와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 IBM Kenexa Survey Enterprise을 통합하는 방법에 대해 알아봅니다.

IBM Kenexa Survey Enterprise를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 사용자의 IBM Kenexa Survey Enterprise에 대한 액세스 권한을 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정에서 SSO(Single Sign-on)를 사용하여 IBM Kenexa Survey Enterprise에 자동으로 로그인할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

IBM Kenexa Survey Enterprise와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- IBM Kenexa Survey Enterprise SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD SSO를 테스트합니다. 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 IBM Kenexa Survey Enterprise 추가
* Azure AD SSO 구성 및 테스트

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>갤러리에서 IBM Kenexa Survey Enterprise 추가
Azure AD에 IBM Kenexa Survey Enterprise의 통합을 구성하려면 갤러리의 IBM Kenexa Survey Enterprise를 관리되는 SaaS 앱 목록에 추가합니다.

갤러리의 IBM Kenexa Survey Enterprise를 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 응용 프로그램을 추가하려면 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **IBM Kenexa Survey Enterprise**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. 결과 목록에서 **IBM Kenexa Survey Enterprise**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 IBM Kenexa Survey Enterprise에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 IBM Kenexa Survey Enterprise 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD에서는 Azure AD 사용자와 IBM Kenexa Survey Enterprise의 관련 사용자 간에 연결을 설정해야 합니다.

링크 관계를 설정하려면 IBM Kenexa Survey Enterprise의 **사용자 이름** 값을 Azure AD의 **Username** 값으로 할당합니다.

IBM Kenexa Survey Enterprise에서 Azure AD SSO를 구성하고 테스트하려면 다음 두 개의 섹션에서 구성 요소를 완료합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 다음을 수행하여 IBM Kenexa Survey Enterprise 응용 프로그램에서 SSO를 구성합니다.

1. Azure Portal의 **IBM Kenexa Survey Enterprise** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![IBM Kenexa Survey Enterprise 구성 Single Sign-On 링크][4]

2. **Single Sign-On** 대화 상자의 **모드** 상자에서 **SAML 기반 로그온**을 선택하여 SSO를 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. **IBM Kenexa Survey Enterprise 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![IBM Kenexa Survey Enterprise 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. **식별자** 텍스트 상자에서 `https://surveys.kenexa.com/<companycode>` 패턴으로 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다. `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 실제 식별자 및 회신 URL로 해당 항목을 업데이트합니다. 실제 값을 가져오려면 [IBM Kenexa Survey Enterprise 지원팀](https://www.ibm.com/support/home/?lnk=fcw)에 문의하세요.

4. **SAML 서명 인증서** 아래에서 **인증서(Base64)** 를 클릭한 후 사용자의 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서(Base64) 다운로드 링크](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa Survey Enterprise 응용 프로그램은 특정 형식인 SAML(Security Assertions Markup Language) 어설션을 수신하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 응답에서 사용자 ID 클레임의 값은 Kenexa 시스템에서 구성된 SSO ID와 일치해야 합니다. 조직에서 적절한 사용자 ID를 SSO IDP(Internet Datagram Protocol)로 매핑하려면 [IBM Kenexa Survey Enterprise 지원팀](https://www.ibm.com/support/home/?lnk=fcw)과 함께 작업하세요. 

    기본적으로 Azure AD는 사용자 ID를 UPN(사용자 계정 이름) 값으로 설정합니다. 아래 스크린샷에 표시된 것처럼 **특성** 탭에서 이 값을 변경할 수 있습니다. 통합은 매핑을 완료한 후에만 정확하게 작동합니다.
    
    ![사용자 특성 대화 상자](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. **저장**을 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. **로그온 구성** 창을 열려면 **IBM Kenexa Survey Enterprise 구성** 섹션에서 **IBM Kenexa Survey Enterprise 구성**을 클릭합니다. 
 
    ![IBM Kenexa Survey Enterprise 구성 링크](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. **빠른 참조** 섹션에서 **로그아웃 URL**, **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL** 값을 복사합니다.

8. **로그온 구성** 창의 **빠른 참조**에서 **로그아웃 URL**, **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL**을 복사합니다.

9. **IBM Kenexa Survey Enterprise** 쪽에서 SSO를 구성하려면 다운로드한 **인증서(Base64)**, **로그아웃 URL**, **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL** 값을 [IBM Kenexa Survey Enterprise 지원팀](https://www.ibm.com/support/home/?lnk=fcw)에 보내야 합니다.

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 이러한 지침의 간결한 버전을 참조할 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서](https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![Azure AD 테스트 사용자 만들기][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![추가 단추](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise 테스트 사용자 만들기

이 섹션에서는 IBM Kenexa Survey Enterprise에서 Britta Simon이라는 사용자를 만듭니다. 

IBM Kenexa Survey Enterprise 시스템에서 사용자를 만들고 여기에 SSO ID를 매핑하려면 [IBM Kenexa Survey Enterprise 지원팀](https://www.ibm.com/support/home/?lnk=fcw)과 함께 작업하면 됩니다. 또한 이 SSO ID 값을 Azure AD의 사용자 ID 값에 매핑해야 합니다. **특성** 탭에서 이 기본 설정을 변경할 수 있습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO을 사용할 수 있도록 사용자인 Britta Simon에게 IBM Kenexa Survey Enterprise에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

IBM Kenexa Survey Enterprise에 사용자인 Britta Simon을 할당하려면 다음을 수행합니다.

1. Azure Portal에서 **응용 프로그램** 보기를 열고 **디렉터리** 보기로 이동한 후 **엔터프라이즈 응용 프로그램**을 선택하고 **모든 응용 프로그램**을 클릭합니다.

    !["엔터프라이즈 응용 프로그램" 및 "모든 응용 프로그램" 링크][201] 

2. **응용 프로그램** 목록에서 **IBM Kenexa Survey Enterprise**를 선택합니다.

    ![응용 프로그램 목록의 IBM Kenexa Survey Enterprise 링크](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. 왼쪽 창에서 **사용자 및 그룹**을 클릭합니다.

    ![“사용자 및 그룹” 링크][202] 

4. **추가** 단추를 클릭한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 **IBM Kenexa Survey Enterprise** 타일을 클릭하면 IBM Kenexa Survey Enterprise 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
