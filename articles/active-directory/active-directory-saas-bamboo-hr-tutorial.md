---
title: '자습서: Azure Active Directory와 BambooHR 통합| Microsoft Docs'
description: Azure Active Directory 및 BambooHR 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: c63a625c1eca7008c751d6904f182f76ee5f343b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>자습서: BambooHR과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 BambooHR을 통합하는 방법에 대해 알아봅니다.

BambooHR을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- BambooHR에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정에서 SSO(Single Sign-On)를 사용하여 BambooHR에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

BambooHR과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- BambooHR SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하지 않는 것이 좋습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에서 설명하는 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 BambooHR 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-bamboohr-from-the-gallery"></a>갤러리에서 BambooHR 추가
Azure AD에 BambooHR을 통합하도록 구성하려면 다음을 수행하여 갤러리의 BambooHR을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    ![Enterprise 응용 프로그램 창][2]
    
3. 응용 프로그램을 추가하려면 **새 응용 프로그램**을 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **BambooHR**을 입력합니다. 결과 목록에서 **BambooHR**을 선택한 다음, **추가**를 선택합니다.

    ![결과 목록의 BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 BambooHR에서 Azure AD SSO를 구성하고 테스트합니다.

SSO를 작동시키려면 Azure AD는 BambooHR에서 해당 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 BambooHR의 관련 사용자 간에 연결을 형성해야 합니다.

BambooHR에서 연결을 설정하려면 Azure AD **사용자 이름** 값을 BambooHR **Username** 값으로 할당합니다.

BambooHR에서 Azure AD SSO를 구성하고 테스트하려면 다음 5개 섹션에서 구성 요소를 완료합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 다음을 수행하여 BambooHR 응용 프로그램에서 SSO를 구성합니다.

1. Azure Portal의 **BambooHR** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 창의 **모드** 드롭다운 목록에서 **SAML 기반 로그온**을 선택합니다.
 
    ![Single Sign-On 창](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. **BambooHR 도메인 및 URL** 아래에서 다음을 수행합니다.

    ![BambooHR 도메인 및 URL 섹션](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. **로그온 URL** 상자에 다음과 같은 형식의 URL을 입력합니다. `https://<company>.bamboohr.com`

    나. **식별자** 상자에 `BambooHR-SAML` 값을 입력합니다.

    > [!NOTE] 
    > **로그온 URL** 값은 실제 값이 아닙니다. 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [BambooHR 클라이언트 지원 팀](https://www.bamboohr.com/contact.php)에 문의하세요. 
 
4. **SAML 서명 인증서** 아래에서 **인증서(Base64)** 를 선택한 후 사용자의 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. **저장**을 선택합니다.

    ![저장 단추](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. **BambooHR 구성** 아래에서 **BambooHR 구성**을 선택하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 나중에 사용하기 위해 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![BambooHR 구성](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. 새 창에서 BambooHR 회사 사이트에 관리자 권한으로 로그인합니다.

8. 홈페이지에서 다음을 수행합니다.
   
    ![BambooHR Single Sign-On 페이지](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. **앱**을 선택합니다.
   
    나. **앱** 창에서 **Single Sign-On**을 선택합니다.
   
    다. **SAML Single Sign-On**을 선택합니다.

9. **SAML Single Sign-On** 창에서 다음을 수행합니다.
   
    ![SAML Single Sign-On 창](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")
   
    a. **SSO 로그인 URL** 상자에 6단계의 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**을 붙여넣습니다.
      
    나. 메모장에서 Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 열고, 콘텐츠를 복사한 다음, **X.509 인증서** 상자에 붙여넣습니다.
   
    다. **저장**을 선택합니다.

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 이 지침의 요약 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하기만 하면 됩니다. 자세한 내용은 [Azure AD 포함 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 Britta Simon 만들기][100]

Azure AD에서 테스트 사용자를 만들려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. **모든 사용자** 창 상단에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. **사용자** 창에서 다음을 수행합니다.

    ![사용자 창](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-bamboohr-test-user"></a>BambooHR 테스트 사용자 만들기

Azure AD 사용자가 BambooHR에 로그인할 수 있도록 다음을 수행하여 BambooHR에서 수동으로 설정합니다.

1. **BambooHR** 사이트에 관리자 권한으로 로그인합니다.

2. 위쪽 도구 모음에서 **설정**을 선택합니다.
   
    ![설정 단추](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "설정")

3. **개요**를 선택합니다.

4. 왼쪽 창에서 **보안** > **사용자**를 선택합니다.

5. 설정하려는 유효한 Azure AD 계정의 사용자 이름, 암호 및 이메일 주소를 입력합니다.

6. **저장**을 선택합니다.
        
>[!NOTE]
>Azure AD 사용자 계정을 설정하기 위해 BambooHR 사용자 계정 생성 도구 또는 API를 사용할 수도 있습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

Azure SSO를 사용할 수 있도록 사용자인 Britta Simon에게 BambooHR에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

Britta Simon을 BambooHR에 할당하려면 다음을 수행합니다.

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동한 후 **Enterprise 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. **엔터프라이즈 응용 프로그램** 목록에서 **BambooHR**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 목록의 BambooHR 링크](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 창의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **선택** 단추를 선택합니다.

7. **할당 추가** 창에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 **BambooHR** 타일을 선택하면 BambooHR 응용 프로그램에 자동으로 로그인됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

