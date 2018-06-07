---
title: '자습서: Velpic SAML과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Velpic SAML 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 03ef1ef04d80ac9bb83bcce2082b6cc3f617d812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>자습서: Velpic SAML과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Velpic SAML을 통합하는 방법에 대해 알아봅니다.

Azure AD에 Velpic SAML을 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- Velpic SAML에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Velpic SAML에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Velpic SAML과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Velpic SAML Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Velpic SAML 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-velpic-saml-from-the-gallery"></a>갤러리에서 Velpic SAML 추가
Velpic SAML의 Azure AD 통합을 구성하려면 갤러리의 Velpic SAML을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Velpic SAML을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Velpic SAML**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. 결과 창에서 **Velpic SAML**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Velpic SAML에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Velpic SAML 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Velpic SAML의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Velpic SAML의 **Username** 값으로 할당하여 설정합니다.

Velpic SAML에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Velpic SAML 테스트 사용자 만들기](#creating-a-velpic-saml-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Velpic SAML에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Velpic SAML 응용 프로그램에서 Single Sign-On을 구성합니다.

**Velpic SAML에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **Velpic SAML** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. **Velpic SAML 도메인 및 URL** 섹션에 세부 정보를 입력합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. **로그인 URL** 텍스트 상자에서 값으로 `https://<sub-domain>.velpicsaml.net`를 입력합니다.

    나. **식별자** 텍스트 상자에 **'Single Sign-On URL'** 값 `https://auth.velpic.com/saml/v2/<entity-id>/login`을 붙여 넣습니다.
    
    > [!NOTE]
    > Velpic SAML 쪽에서 SSO 플러그 인을 구성한 경우 로그온 URL은 Velpic SAML 팀에서 제공하고 식별자 값을 사용할 수 있습니다. Velpic SAML 응용 프로그램 페이지에서 해당 값을 복사하고 여기에 붙여 넣어야 합니다.

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. Velpic SAML 구성 섹션에서 Velpic SAML 구성을 클릭하여 로그온 구성 창을 엽니다. 빠른 참조 섹션에서 SAML 엔터티 ID를 복사합니다.

7. 다른 웹 브라우저 창에서 Velpic SAML 회사 사이트에 관리자 권한으로 로그인합니다.

8. **관리** 탭을 클릭하고 **플러그 인** 단추를 클릭해야 하는 **통합** 섹션으로 이동하여 로그인을 위한 새 플러그 인을 만듭니다.

    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. **‘플로그 인 추가’** 단추를 클릭합니다.
    
    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. 플러그 인 추가 페이지에서 **SAML** 타일을 클릭합니다.
    
    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. 새 SAML 플러그 인의 이름을 입력하고 **'추가'** 단추를 클릭합니다.

    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. 세부 정보를 다음과 같이 입력합니다.

    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. **이름** 텍스트 상자에 SAML 플러그 인의 이름을 입력합니다.

    나. **발급자 URL** 텍스트 상자에 Azure Portal의 **로그온 구성** 창에서 복사한 **SAML 엔터티 ID**를 붙여 넣습니다.

    다. **공급자 메타데이터 구성**에 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드합니다.

    d. **'새 사용자 자동 만들기'** 확인란을 활성화하여 SAML JIT(Just-In-Time) 프로비전을 활성화하도록 선택할 수도 있습니다. 사용자자 Velpic에 없는 경우 이 플래그는 활성화되지 않으며 Azure에서 로그인에 실패합니다. 플래그가 활성화된 경우 사용자는 로그인 시 자동으로 Velpic으로 프로비전됩니다. 

    e. 텍스트 상자에서 **Single Sign-On URL**을 복사하고 Azure Portal에 붙여 넣습니다.
    
    f. **저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-velpic-saml-test-user"></a>Velpic SAML 테스트 사용자 만들기

응용 프로그램은 JIT(Just-In-Time) 사용자 프로비전을 지원하므로 이 단계는 일반적으로 필요하지 않습니다. 자동 사용자 프로비전이 활성화되지 않은 경우 수동 사용자 만들기는 아래에서 설명한 대로 실행될 수 있습니다.

Velpic SAML 회사 사이트에 관리자 권한으로 로그인하고 다음 단계를 수행합니다.
    
1. 관리 탭을 클릭하고 사용자 섹션으로 이동한 다음 새로 만들기 단추를 클릭하여 사용자를 추가합니다.

    ![사용자 추가](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![사용자](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. **이름** 텍스트 상자에 Britta Simon의 이름을 입력합니다.

    나. **성** 텍스트 상자에 Britta Simon의 성을 입력합니다.

    다. **사용자 이름** 텍스트 상자에 Britta Simon의 사용자 이름을 입력합니다.

    d. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    e. 나머지 정보는 선택 사항이며 필요한 경우 채울 수 있습니다.
    
    f. **저장**을 클릭합니다.  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Velpic SAML에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Velpic SAML에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Velpic SAML**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

1. 액세스 패널에서 Velpic SAML 타일을 클릭할 때 Velpic SAML 응용 프로그램의 로그인 페이지를 가져와야 합니다. 로그인 페이지에 **'Azure AD를 사용하여 로그인'** 단추가 표시됩니다.

    ![플러그 인](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. **'Azure AD를 사용하여 로그인'** 단추를 클릭하여 Azure AD 계정을 사용하여 Velpic에 로그인합니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

