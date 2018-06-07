---
title: '자습서: O.C. Tanner - AppreciateHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 O.C. Tanner - AppreciateHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 57cdd0d8a58fd88ab1dda63d32397e726c7a2eb2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350256"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>자습서: O.C. Tanner - AppreciateHub와 Azure Active Directory 통합

이 자습서에서는 O.C. Tanner - AppreciateHub와 Azure AD(Azure Active Directory)를 통합하는 방법을 알아봅니다.

O.C. 통합 Tanner - Azure AD가 포함된 AppreciateHub는 다음과 같은 이점을 제공합니다.

- O.C.에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. Tanner - AppreciateHub
- 사용자가 자동으로 O.C.에 로그온하도록 설정할 수 있습니다. Tanner - Azure AD 계정이 포함된 AppreciateHub(Single Sign-On)
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

O.C.와 Azure AD 통합을 구성하려면 Tanner - AppreciateHub, 다음 사항이 필요합니다.

- Azure AD 구독
- O.C. Tanner - AppreciateHub Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. O.C. 추가 Tanner - 갤러리에서 AppreciateHub
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. 추가 Tanner - 갤러리에서 AppreciateHub
O.C.의 통합을 구성하려면 Tanner - Azure AD에서 AppreciateHub, O.C.를 추가해야 합니다. Tanner - 갤러리에서 관리된 SaaS 앱 목록에 AppreciateHub

**O.C.를 추가하려면 Tanner - 갤러리에서 AppreciateHub, 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **O.C.를 입력합니다. Tanner - AppreciateHub**.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. 결과 패널에서 **O.C. Tanner - AppreciateHub**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 한 O.C. Tanner - AppreciateHub를 사용하여 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD는 O.C. Tanner - AppreciateHub에서 해당 사용자가 Azure AD에서의 사용자라는 것을 알아야 합니다. 즉, Azure AD 사용자와 O.C.의 관련 사용자 간에 연결이 형성되어야 합니다. Tanner - AppreciateHub를 설정해야 합니다.

O.C. Tanner - AppreciateHub에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

O.C.를 사용하여 Azure AD Single Sign-On을 구성하고 테스트하려면 Tanner - AppreciateHub, 다음과 같은 구성 블록을 완료하는데 필요합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[O.C. 만들기 Tanner - O.C.에서 Britta Simon에 해당하는 사용자가 있는 AppreciateHub 테스트 사용자](#creating-a-oc-tanner---appreciatehub-test-user)**. 해당 사용자의 Azure AD 표현에 연결된 Tanner - AppreciateHub.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 O.C. Tanner - AppreciateHub 응용 프로그램에서 Single Sign-On을 구성합니다.

**O.C.를 사용하여 Azure AD Single Sign-On을 구성하려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

1. Azure Portal의 **O.C. Tanner - AppreciateHub** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. **O.C. Tanner - AppreciateHub 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<companyname>.octanner.net/sp/ACS.saml2`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 실제 회신 URL로 이 값을 업데이트하세요. 이 값을 얻으려면 [O.C. Tanner - AppreciateHub 지원 팀](mailto:sso@octanner.com)에 문의하세요.

    나. 다음 링크를 사용하여 메타데이터 파일을 엽니다. [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)
   
    다. **md:AssertionConsumerService** 노드를 찾습니다. 
   
    d. **위치** 특성의 값을 복사합니다. 
   
    ![앱 설정 구성][12]
   
    e. **Sign-on URL** 텍스트 상자에서 이전 단계에 얻은 값보다 큽니다.

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_400.png)

6. **O.C. Tanner - AppreciateHub** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **메타데이터 XML**을 [O.C. Tanner - AppreciateHub 지원 팀](mailto:sso@octanner.com)에 보내야 합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>O.C. 만들기 Tanner - AppreciateHub 테스트 사용자.

이 섹션은 O.C.에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. 액세스 권한을 부여합니다.

**O.C.에서 Britta Simon이라는 사용자를 만들려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

[O.C. Tanner - AppreciateHub 지원 팀](mailto:sso@octanner.com)에 요청하여 Azure AD에서 Britta Simon이라는 사용자 이름이 동일한 값인 nameID 특성을 가진 사용자를 만듭니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 O.C. Tanner - AppreciateHub에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon를 O.C.에 할당하려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **O.C.를 선택합니다. Tanner - AppreciateHub**.

    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  
O.C.를 클릭할 때 Tanner - 액세스 패널에서 AppreciateHub 타일, O.C.에 자동으로 로그온되야 합니다. Single Sign-On을 구성합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png

