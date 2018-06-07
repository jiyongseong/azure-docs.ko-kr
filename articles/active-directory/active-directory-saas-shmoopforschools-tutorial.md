---
title: '자습서: Shmoop For Schools와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Shmoop For Schools 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 799a88344d6c348061af19bfbbd9022025d2d66b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350613"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>자습서: Shmoop For Schools와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Shmoop For Schools를 통합하는 방법에 대해 알아봅니다.

Shmoop For Schools를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Shmoop For Schools에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Shmoop For Schools에 자동으로 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Shmoop For Schools와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Shmoop For Schools Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음을 수행하는 것이 좋습니다.

- 필요한 경우에만 프로덕션 환경을 사용합니다.
- Azure AD 평가판 환경이 없는 경우 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 확보합니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Shmoop For Schools 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-shmoop-for-schools-from-the-gallery"></a>갤러리에서 Shmoop For Schools 추가
Shmoop For Schools의 Azure AD 통합을 구성하려면 갤러리의 Shmoop For Schools를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Shmoop For Schools를 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Shmoop For Schools**를 입력합니다. 결과 패널에서 **Shmoop For Schools**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록에서 Shmoop For Schools](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Shmoop For Schools에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Shmoop For Schools 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Shmoop For Schools의 관련 사용자 간에 연결이 형성되어야 합니다.

Shmoop For Schools에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [Shmoop For Schools 테스트 사용자 만들기](#create-a-shmoop-for-schools-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Shmoop For Schools에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Shmoop For Schools 응용 프로그램에서 Single Sign-On을 구성합니다.

**Shmoop For Schools에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Shmoop For Schools** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자의 **Single Sign-On 모드** 아래 드롭다운 메뉴에서 **SAML 기반 로그인**을 선택합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. **Shmoop For Schools 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. **로그온 URL** 텍스트 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    나. **식별자** 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [Shmoop For Schools 클라이언트 지원 팀](mailto:support@shmoop.com)에 문의하세요. 
 
4. Shmoop For Schools 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 어설션을 구성하는 방법을 보여줍니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools는 사용자에게 **교사** 및 **학생**이라는 두 가지 역할을 지원합니다. 사용자가 적절한 역할을 할당 받을 수 있도록 Azure AD에서 이러한 역할을 설정합니다. Azure AD에서 역할을 구성하는 방법을 이해하려면 [Azure AD를 사용하는 클라우드 응용 프로그램의 역할 기반 액세스 제어](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/)를 참조하세요.
    
5. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이전 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성합니다.  다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. **특성 추가** 대화 상자를 열려면 **특성 추가**를 선택합니다.
    
    ![Single Sign-On 구성 ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Single Sign-On 구성](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에 해당 행에 표시되는 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 표시되는 특성 값을 선택합니다.

    d. **네임스페이스** 상자를 비워 둡니다.
    
    e. **확인**을 선택합니다.

6. **저장** 단추를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. **Shmoop For Schools** 쪽에서 Single Sign-On을 구성하려면 **앱 페더레이션 메타데이터 URL**을 [Shmoop For Schools 지원 팀](mailto:support@shmoop.com)에 보내야 합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동합니다. 그런 다음 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Shmoop For Schools 테스트 사용자 만들기

이 섹션은 Shmoop For Schools에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Shmoop For Schools는 기본적으로 사용하도록 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 Shmoop For Schools에 액세스를 시도하는 동안 만들어집니다.

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [Shmoop For Schools 지원 팀](mailto:support@shmoop.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Shmoop For Schools에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Shmoop For Schools에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 엽니다. 그런 다음 디렉터리 창에서 **엔터프라이즈 응용 프로그램**으로 이동합니다.  다음으로 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Shmoop For Schools**를 선택합니다.

    ![응용 프로그램 목록의 Shmoop For Schools 링크](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다. 

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **Shmoop For Schools** 타일을 클릭하면 Shmoop For Schools 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

