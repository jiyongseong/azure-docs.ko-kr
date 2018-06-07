---
title: '자습서: T&E Express와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 T&E Express 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 8d3980b6169c8a9fc60c04e527814159082a46e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>자습서: T&E Express와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 T&E Express를 통합하는 방법에 대해 알아봅니다.

T&E Express를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- T&E Express에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 T&E Express에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

T&E Express와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- T&E Express Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 T&E Express 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-te-express-from-the-gallery"></a>갤러리에서 T&E Express 추가
T&E Express의 Azure AD 통합을 구성하려면 갤러리의 T&E Express를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 T&E Express를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **T&E Express**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. 결과 창에서 **T&E Express**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 T&E Express에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 T&E Express 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 T&E Express의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 T&E Express의 **Username** 값으로 할당하여 설정합니다.

T&E Express에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[T&E Express 테스트 사용자 만들기](#creating-a-te-express-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 T&E Express에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 T&E Express 응용 프로그램에서 Single Sign-On을 구성합니다.

**T&E Express에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **T&E Express** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. **T&E Express 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. **식별자** 텍스트 상자에 해당 값으로 `https://<domain>.tyeexpress.com`을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. 이러한 값을 얻으려면 [T&E Express 지원 팀](http://www.tyeexpress.com/contacto.aspx)에 문의하세요.

5. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_400.png)

8. **T&E Express** 측에 Single Sign-On을 구성하려면 관리자 자격 증명을 사용하여 SAML Single Sign-On 없이 T&E express 응용 프로그램에 로그인합니다.

9. **관리** 탭 아래에서 **SAML 도메인**을 클릭하여 SAML 설정 페이지를 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tye-SAML.png)

10. **Activar(활성화)** 옵션을 **아니요**에서 **SI(예)** 로 선택합니다. **ID 공급자 메타데이터** 텍스트 상자에 Azure Portal에서 다운로드한 메타데이터 XML을 붙여 넣습니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tyeAdmin.png)

11. **Guardar(저장)** 단추를 클릭하여 설정을 저장합니다. 


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-te-express-test-user"></a>T&E Express 테스트 사용자 만들기

Azure AD 사용자가 T&E Express에 로그인할 수 있도록 하려면 T&E Express로 프로비전되어야 합니다.  
T&E Express의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. T&E Express 회사 사이트에 관리자 권한으로 로그인합니다.

2. 관리 탭 아래에서 사용자를 클릭하여 사용자 마스터 페이지를 엽니다.

    ![직원 추가](./media/active-directory-saas-tyeexpress-tutorial/tye-adminusers.png)

3. 홈 페이지에서 **+** 를 클릭하여 사용자를 추가합니다.

    ![직원 추가](./media/active-directory-saas-tyeexpress-tutorial/tye-usershome.png)

4. 양식에서 요청한 대로 모든 필수 세부 정보를 입력하고 저장 단추를 클릭하여 세부 정보를 저장합니다.

    ![직원 추가](./media/active-directory-saas-tyeexpress-tutorial/tye-usersadd.png)

    ![직원 추가](./media/active-directory-saas-tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 T&E Express에 대한 액세스를 허용합니다.

![사용자 할당][200] 

**Britta Simon을 T&E Express에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **T&E Express**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 T&E Express 타일을 클릭하면 T&E Express 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_203.png

