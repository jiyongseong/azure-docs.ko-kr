---
title: '자습서: ADP와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 ADP 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 64ae39e630897c69155ee85347c90421aca394a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>자습서: ADP와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ADP를 통합하는 방법에 대해 알아봅니다.

ADP를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- ADP에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 ADP에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ADP와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ADP가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ADP 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-adp-from-the-gallery"></a>갤러리에서 ADP 추가
ADP의 Azure AD 통합을 구성하려면 갤러리의 ADP를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ADP를 추가하려면 다음 단계를 수행합니다.**

1.  관리자 권한으로 Microsoft Azure ID 공급자 환경에 로그온합니다.

2. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

3. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
4. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

5. 검색 상자에 **ADP**를 입력하고 결과 패널에서 **ADP**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 ADP](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ADP에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ADP 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 ADP의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

ADP에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

ADP에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[ADP 테스트 사용자 만들기](#create-an-adp-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ADP에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 ADP 응용 프로그램에서 Single Sign-On을 구성합니다.

**ADP에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **ADP** 응용 프로그램 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다. 

    ![Single Sign-On 속성](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    나. **사용자 액세스 URL**을 복사하고 자습서의 뒷부분에서 설명되는 **로그온 URL 구성 섹션**에 붙여넣어야 합니다.

    다. **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    d. **사용자가 볼 수 있습니까** 필드 값을 **아니요**로 설정합니다.

2. **ADP** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

3. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. **ADP 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![ADP 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_url.png)

    **식별자** 텍스트 상자에 URL `https://fed.adp.com`를 입력합니다. 
    
5. ADP 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. 클레임 이름은 항상 **"PersonImmutableID"** 및 **employeeid**에 매핑한 값입니다. 

    여기에서는 Azure AD에서 ADP로 매핑되는 사용자가 **employeeid**에서 수행되지만 응용 프로그램 설정에 따라 다른 값에 이를 매핑할 수 있습니다. 따라서 사용자의 올바른 식별자를 사용하고 해당 값을 **"PersonImmutableID"** 클레임으로 매핑하려면 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)과 먼저 작업해 보세요.

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.

    > [!NOTE] 
    > SAML 어설션을 구성하기 전에 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)에 문의하고 테넌트에 대한 고유 식별자 특성 값을 요청합니다. 응용 프로그램에 대한 사용자 지정 클레임을 구성하려면 이 값이 필요합니다. 

7. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. **ADP** 측에서 Single Sign-On을 구성하려면 [ADP 웹 사이트](https://adpfedsso.adp.com/public/login/index.fcc)에서 다운로드된 **메타데이터 XML**을 업로드해야 합니다.

> [!NOTE]  
> 이 프로세스는 며칠이 걸릴 수 있습니다. 

### <a name="configure-your-adp-services-for-federated-access"></a>페더레이션 액세스에 대한 ADP 서비스 구성

>[!Important]
> ADP 서비스에 대한 페더레이션 액세스를 필요로 하는 직원은 ADP 서비스 앱에 할당되어야 하며 이후에 사용자는 특정 ADP 서비스에 다시 할당되어야 합니다.
ADP 담당자로부터 확인을 받는 즉시 ADP 서비스를 구성하고 특정 ADP 서비스에 대한 사용자 액세스를 제어하도록 사용자를 할당/관리합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **ADP**를 입력하고 결과 패널에서 **ADP**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 ADP](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. Azure Portal의 **ADP** 응용 프로그램 통합 페이지에서 **속성 탭**을 클릭하고 다음 단계를 수행합니다.  

    ![Single Sign-On 연결된 속성](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  **사용자가 로그인할 수 있습니까** 필드 값을 **예**로 설정합니다.

    나.  **사용자 할당 필요** 필드 값을 **예**로 설정합니다.

    다.  **사용자가 볼 수 있습니까** 필드 값을 **예**로 설정합니다.

6. **ADP** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

7. **Single Sign-On** 대화 상자에서 **모드**로 **연결된 로그온**을 선택합니다. 응용 프로그램을 **ADP**에 연결하려면

    ![연결된 Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. **로그온 URL 구성** 섹션으로 이동하고 다음 단계를 수행합니다.

    ![Single Sign-On 속성](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. 위의 **속성 탭**(기본 ADP 앱)에서 복사한 **사용자 액세스 URL**을 붙여넣습니다.
                                                             
    나. 다음은 다른 **릴레이 상태 URL**을 지원하는 다섯 개의 앱입니다. 특정 응용 프로그램에 대한 적절한 **릴레이 상태 URL** 값을 **사용자 액세스 URL**에 수동으로 추가해야 합니다.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **저장** 합니다.

10. ADP 담당자로부터 확인을 받는 즉시 한 명 또는 두 명의 사용자로 테스트를 시작합니다.

    a. ADP 서비스 앱에 적은 수의 사용자를 할당하여 페더레이션 액세스를 테스트합니다.

    나. 사용자가 갤러리에서 ADP 서비스 앱에 액세스하고 해당 ADP 서비스에 액세스할 수 있는 경우 테스트는 성공합니다.
 
11. 성공적인 테스트의 확인 후 페더레이션된 ADP 서비스를 개별 사용자 또는 사용자 그룹에 할당하고(자습서의 뒷부분에서 설명됨) 직원에게 롤아웃합니다. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-adp-test-user"></a>ADP 테스트 사용자 만들기

이 섹션은 ADP에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. ADP 계정에 사용자를 추가하려면 [ADP 지원팀](https://www.adp.com/contact-us/overview.aspx)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ADP에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 ADP에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **ADP**를 선택합니다.

    ![응용 프로그램 목록의 ADP 링크](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ADP 타일을 클릭하면 ADP 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

