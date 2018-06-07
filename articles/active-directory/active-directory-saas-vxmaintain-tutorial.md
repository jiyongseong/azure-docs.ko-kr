---
title: '자습서: vxMaintain과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 vxMaintain 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c30d59a2bd903a5578caf376957a22bb270455f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>자습서: Azure Active Directory와 vxMaintain 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 vxMaintain을 통합하는 방법에 대해 알아봅니다.

이 통합은 몇 가지 중요한 이점을 제공합니다. 다음을 수행할 수 있습니다.

- vxMaintain에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정을 사용하여 SSO(Single Sign-On)로 vxMaintain에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

vxMaintain과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- vxMaintain SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에서 설명하는 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 vxMaintain 추가
* Azure AD Single Sign-on 구성 및 테스트

## <a name="add-vxmaintain-from-the-gallery"></a>갤러리에서 vxMaintain 추가
vxMaintain의 Azure AD 통합을 구성하려면 갤러리의 vxMaintain을 관리되는 SaaS 앱 목록에 추가해야 합니다.

갤러리에서 vxMaintain을 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 창][2]
    
3. 응용 프로그램을 추가하려면 **모든 응용 프로그램** 대화 상자에서 **새 응용 프로그램**을 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **vxMaintain**을 입력합니다.

    !["Single Sign-On 모드" 드롭다운 목록](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. 결과 목록에서 **vxMaintain**을 선택한 다음 **추가**를 선택합니다.

    ![vxMaintain 링크](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 vxMaintain을 사용하여 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동되려면 Azure AD는 Azure AD 사용자에 해당하는 vxMaintain의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 해당 vxMaintain 사용자 간의 연결을 형성해야 합니다.

연결을 형성하려면 vxMaintain **사용자 이름** 값을 Azure AD **Username** 값으로 할당합니다.

vxMaintain을 사용하여 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 다음을 수행하여 vxMaintain 응용 프로그램에서 SSO를 구성합니다.

1. Azure Portal의 **vxMaintain** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    !["Single Sign-On" 명령][4]

2. SSO를 사용하도록 설정하려면 **Single Sign-On 모드** 드롭다운 목록에서 **SAML 기반 로그온**을 선택합니다.
 
    !["SAML 기반 로그온" 명령](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. **vxMaintain 도메인 및 URL** 아래에서 다음을 수행합니다.

    ![vxMaintain 도메인 및 URL 섹션](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. **식별자** 상자에 다음 `https://<company name>.verisae.com` 구문이 있는 URL을 입력합니다.

    나. **회신 URL** 상자에 다음 `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true` 구문이 있는 URL을 입력합니다.

    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 실제 식별자 및 회신 URL로 해당 항목을 업데이트합니다. 값을 가져오려면 [vxMaintain 지원 팀](http://www.verisae.com/contact-us)에 문의하세요.
 
4. **SAML 서명 인증서** 아래에서 **메타데이터 XML**을 선택한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    !["SAML 서명 인증서" 섹션](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. **저장**을 선택합니다.

    ![저장 단추](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. **vxMaintain** SSO를 구성하려면 다운로드한 **메타데이터 XML** 파일을 [vxMaintain 지원 팀](http://www.verisae.com/contact-us)에 보냅니다.

> [!TIP]
> 앱을 설정할 때 [Azure Portal](https://portal.azure.com)에서 이전 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 앱을 추가한 후 **Single Sign-On** 탭을 선택한 다음 **구성** 섹션에서 포함된 설명서에 액세스합니다. 
>
>포함된 설명서 기능에 대한 자세한 내용은 [엔터프라이즈 앱에 대한 Single Sign-On 관리](https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![Azure AD 테스트 사용자][100]

1. **Azure Portal**의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    !["Azure Active Directory" 단추](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹** > **모든 사용자**로 이동합니다.
    
    !["모든 사용자" 링크](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    **모든 사용자** 대화 상자가 열립니다. 

3. **사용자** 대화 상자를 열려면 **추가**를 선택합니다.
 
    ![추가 단추](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자에서 다음을 수행합니다.
 
    ![사용자 대화 상자](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 테스트 사용자인 Britta Simon의 이메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에서 생성된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-vxmaintain-test-user"></a>vxMaintain 테스트 사용자 만들기

이 섹션에서는 vxMaintain에서 테스트 사용자인 Britta Simon을 만듭니다. vxMaintain 플랫폼에 사용자를 추가하려면 [vxMaintain 지원 팀](http://www.verisae.com/contact-us)에 문의하세요. SSO를 사용하기 전에 사용자를 만들고 활성화합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 테스트 사용자인 Britta Simon에게 vxMaintain에 대한 액세스 권한을 부여합니다. 이렇게 하려면 다음을 수행합니다.

![표시 이름 목록의 테스트 사용자][200] 

1. Azure Portal **응용 프로그램** 보기에서 **디렉터리** 보기 > **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**으로 이동합니다.

    !["모든 응용 프로그램" 링크][201] 

2. **응용 프로그램** 목록에서 **vxMaintain**을 선택합니다.

    ![vxMaintain 링크](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202] 

4. **추가**를 선택한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][203]

5. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **Britta Simon**을 선택한 다음 **선택** 단추를 선택합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 **vxMaintain** 타일을 선택하면 vxMaintain 응용 프로그램에 자동으로 로그인됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

