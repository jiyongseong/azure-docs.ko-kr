---
title: '자습서: OpenAthens와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 OpenAthens 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 3b19f3c7ed84a63f48320a2c7af8d06a9cc5deb4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>자습서: OpenAthens와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 OpenAthens를 통합하는 방법에 대해 알아봅니다.

OpenAthens를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- OpenAthens에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 OpenAthens에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

OpenAthens와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- OpenAthens Single Sign-On이 설정된 구독

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 OpenAthens 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-openathens-from-the-gallery"></a>갤러리에서 OpenAthens 추가
OpenAthens의 Azure AD 통합을 구성하려면 갤러리의 OpenAthens를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 OpenAthens를 추가하려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 창][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **OpenAthens**를 입력하고 결과 패널에서 **OpenAthens**를 선택한 후 **추가** 단추를 선택합니다.

    ![결과 목록의 OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 OpenAthens에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 OpenAthens 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 OpenAthens의 관련 사용자 간에 연결이 형성되어야 합니다.

OpenAthens에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

OpenAthens에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on), 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user), Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [OpenAthens 테스트 사용자 만들기](#create-a-openathens-test-user), Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 OpenAthens에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user), Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-on 테스트](#test-single-sign-on), 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 OpenAthens 응용 프로그램에서 Single Sign-On을 구성합니다.

**OpenAthens를 사용하여 Azure AD Single Sign-On을 구성하려면**

1. Azure Portal의 **OpenAthens** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. Single Sign-On을 사용하도록 설정하려면 **Single Sign-On** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. **OpenAthens 도메인 및 URL** 섹션에서 **식별자** 텍스트 상자에 `https://login.openathens.net/saml/2/metadata-sp` 값을 입력합니다.

    ![OpenAthens 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 선택한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![AMSL 서명 인증서 다운로드 링크](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. **저장** 단추를 선택합니다.

    ![Single Sign-On 저장 단추](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. 다른 웹 브라우저 창에서 OpenAthens 회사 사이트에 관리자로 로그인합니다.

7. **관리** 탭 아래의 목록에서 **연결**을 선택합니다. 

    ![Single Sign-On 구성](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. **SAML 1.1/2.0**을 선택한 다음 **구성** 단추를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. 구성을 추가하려면 **찾아보기** 단추를 선택하여 Azure Portal에서 다운로드한 메타데이터 .xml 파일을 업로드한 다음 **추가**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. **세부 정보** 탭 아래에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. **표시 이름 매핑**에서 **특성 사용**을 선택합니다.

    나. **표시 이름 특성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 값을 입력합니다.
    
    다. **고유의 사용자 매핑**에서 **특성 사용**을 선택합니다.

    d. **고유의 사용자 특성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 값을 입력합니다.

    e. **상태**에서 확인란 세 개를 모두 선택합니다.

    f. **로컬 계정 만들기**에서 **자동**을 선택합니다.

    g. **변경 내용 저장**을 선택합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서](https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 "Britta Simon"이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 텍스트 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
  
### <a name="create-an-openathens-test-user"></a>OpenAthens 테스트 사용자 만들기

OpenAthens는 Just-In-Time 프로비전을 지원하며 인증에 성공하면 자동으로 사용자가 생성됩니다. 이 섹션에서는 아무 작업도 수행할 필요가 없습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 OpenAthens에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon를 OpenAthens에 할당하려면**

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동한 후 **엔터프라이즈 응용 프로그램**으로 이동하고 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. **응용 프로그램** 목록에서 **OpenAthens**를 선택합니다.

    ![응용 프로그램 목록의 OpenAthens 연결](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 선택합니다. 그런 후 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 목록에서 **선택** 단추를 선택합니다.

7. **할당 추가** 창에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **OpenAthens** 타일을 선택하면 OpenAthens 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록은 [Azure AD에서 사용할 SaaS 앱 통합 자습서](active-directory-saas-tutorial-list.md)를 참조하세요.
* Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

