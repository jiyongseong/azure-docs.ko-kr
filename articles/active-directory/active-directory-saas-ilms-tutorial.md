---
title: '자습서: Azure Active Directory와 iLMS 통합 | Microsoft Docs'
description: Azure Active Directory와 iLMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 0281b0fca107908654dfac31014c79c187aa9b5d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>자습서: iLMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 iLMS를 통합하는 방법에 대해 알아봅니다.

iLMS를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- iLMS에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자의 Azure AD 계정으로 iLMS에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

iLMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- iLMS Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 iLMS 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-ilms-from-the-gallery"></a>갤러리에서 iLMS 추가
iLMS가 Azure AD에 통합되도록 구성하려면 갤러리에서 iLMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 iLMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **iLMS**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. 결과 창에서 **iLMS**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 iLMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 iLMS 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 iLMS의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 iLMS의 **Username** 값으로 할당하여 설정됩니다.

iLMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[iLMS 테스트 사용자 만들기](#creating-an-ilms-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 iLMS에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 iLMS 응용 프로그램에서 Single Sign-On을 구성합니다.

**iLMS에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **iLMS** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. **iLMS 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. **식별자** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 **식별자** 값을 붙여 넣습니다.

    나. **회신 URL** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 다음과 같은 패턴의 **끝점(URL)** 값을 붙여 넣습니다. `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >여기서 '123456'은 식별자 값의 예입니다.

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 선택하세요.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    **로그온 URL** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 **끝점(URL)** 값을 다음과 같은 패턴으로 붙여 넣습니다. `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. JIT 프로비전을 사용하도록 설정하려면 iLMS 응용 프로그램에 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/4.png)
    
    iLMS에서 **부서, 지역** 및 **국** 특성을 만들고 이러한 특성의 이름을 추가합니다. 위에 표시된 모든 특성이 필요합니다.  

    > [!NOTE] 
    > 이러한 특성을 매핑하려면 iLMS에서 **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 를 사용하도록 설정해야 합니다. 특성 구성을 이해하려면 [여기](http://support.inspiredelearning.com/customer/portal/articles/2204526)의 지침을 참조하세요.

6. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **확인**을 클릭합니다.

7. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. 다른 웹 브라우저 창에서 **iLMS 관리 포털**에 관리자로 로그인합니다.

10. **Settings(설정)** 탭에서 **SSO:SAML**을 클릭하여 SAML 설정을 열고 다음 단계를 수행합니다.
    
    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. **Service Provider(서비스 공급자)** 섹션을 확장하고 **Identifier(식별자)** 및 **Endpoint (URL)(끝점 URL)** 값을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/2.png) 

    나. **Identity Provider(ID 공급자)** 섹션에서 **Import Metadata(메타데이터 가져오기)** 를 클릭합니다.
    
    다. Azure Portal에서 다운로드한 **메타데이터** 파일을 **SAML 서명 인증서** 섹션에서 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. 인식할 수 없는 사용자의 iLMS 계정을 만들기 위해 JIT 프로비전을 사용하도록 설정하려면 아래 단계를 수행합니다.
        
       - **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 를 선택합니다.
       
       ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Azure AD의 특성을 iLMS의 특성과 매핑합니다. 특성 열에 특성 이름 또는 기본값을 지정합니다.

    e. **Business Rules(비즈니스 규칙)** 탭으로 이동하여 다음 단계를 수행합니다. 
        
       ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/5.png)

       - Single Sign-on 시 존재하지 않는 지역, 국 및 부서를 만들려면 **Create Un-recognized Regions, Divisions and Departments(인식할 수 없는 지역, 국 및 부서 만들기)** 를 선택합니다.
        
       - 사용자의 프로필을 Single Sign-on마다 업데이트할지 여부를 지정하려면 **Update User Profile During Sign-in(로그인하는 동안 사용자 프로필 업데이트)** 를 선택합니다. 
        
       - **“Update Blank Values for Non Mandatory Fields in User Profile”(사용자 프로필의 필수가 아닌 필드에 대한 빈 값 업데이트)** 옵션이 선택되어 있으면 로그인 시 비어 있는 선택적 프로필 필드로 인해 사용자의 iLMS 프로필에 해당 필드에 대한 빈 값을 포함하게 됩니다.
        
       - **Send Error Notification Email(오류 알림 전자 메일 보내기)** 를 선택하고 오류 알림 전자 메일을 수신할 사용자의 전자 메일을 입력합니다.

11. **저장** 단추를 클릭하여 설정을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
    
### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-an-ilms-test-user"></a>iLMS 테스트 사용자 만들기

응용 프로그램이 Just-In-Time 사용자 프로비전을 지원하며 인증 후에는 응용 프로그램에서 자동으로 사용자가 생성됩니다. JIT는 iLMS 관리 포털에서 SAML 구성을 설정하는 동안 **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 확인란을 클릭한 경우에 작동합니다.

사용자를 수동으로 만들어야 하는 경우에는 아래 단계를 수행합니다.

1. iLMS 회사 사이트에 관리자 권한으로 로그인합니다.

2. **Users(사용자)** 탭에서 **“Register User”(사용자 등록)** 를 클릭하여 **Register User(사용자 등록)** 페이지를 엽니다. 
   
   ![직원 추가](./media/active-directory-saas-ilms-tutorial/3.png)

3. **“Register User”(사용자 등록)** 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. **First Name(이름)** 텍스트 상자에 이름 Britta를 입력합니다.
   
    나. **Last Name(성)** 텍스트 상자에 성 Simon을 입력합니다.

    다. **Email ID(전자 메일 ID)** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    d. **Region(지역)** 드롭다운에서 지역에 대한 값을 선택합니다.

    e. **Division(국)** 드롭다운에서 국에 대한 값을 선택합니다.

    f. **Department(부서)** 드롭다운에서 부서에 대한 값을 선택합니다.

    g. **저장**을 클릭합니다.

    > [!NOTE] 
    > **Send Registration Mail(등록 메일 보내기)** 확인란을 선택하여 사용자에게 등록 메일을 보낼 수 있습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 iLMS에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 iLMS에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **iLMS**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 iLMS 타일을 클릭하면 iLMS 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

