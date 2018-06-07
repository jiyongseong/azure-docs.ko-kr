---
title: '자습서: LinkedIn Learning과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 LinkedIn Learning 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: jeedes
ms.openlocfilehash: fabb79a1449529cde97db7054eb954564b4dfa68
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>자습서: LinkedIn Learning과 Azure Active Directory 통합

이 자습서에서는 LinkedIn Learning을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

LinkedIn Learning을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- LinkedIn Learning에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자의 Azure AD 계정으로 LinkedIn Learning에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

LinkedIn Learning과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- LinkedIn Learning Single Sign-On을 사용하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 LinkedIn Learning 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-linkedin-learning-from-the-gallery"></a>갤러리에서 LinkedIn Learning 추가
LinkedIn Learning이 Azure AD에 통합되도록 구성하려면 LinkedIn Learning을 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 LinkedIn Learning을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에서 **LinkedIn Learning**을 입력합니다. 결과 패널에서 **LinkedIn Learning**을 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 LinkedIn Learning에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 LinkedIn Learning 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 LinkedIn Learning의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 LinkedIn Learning의 **Username** 값으로 할당하여 설정합니다.

LinkedIn Learning에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[LinkedIn Learning 테스트 사용자 만들기](#creating-a-linkedin-learning-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 LinkedIn Learning 응용 프로그램에서 Single Sign-On을 구성합니다.

**LinkedIn Learning에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **LinkedIn Learning** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. 다른 웹 브라우저 창에서 LinkedIn Learning 테넌트에 관리자로 로그인합니다.

4. **계정 센터**의 **설정** 아래에서 **전역 설정**을 클릭합니다. 또한 드롭다운 목록에서 **학습 - 기본**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. **OR Click Here to load and copy individual fields from the form(또는 폼에서 개별 필드를 로드하여 복사하려면 여기를 클릭)** 을 클릭하고 **엔터티 ID** 및 **ACS(Assertion Consumer Access) URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. **IdP 시작** 모드에서 SSO를 구성하려면 Azure Portal의 **LinkedIn Learning 도메인 및 URL**에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. **식별자** 텍스트 상자에 LinkedIn 포털에서 복사한 **엔티티 ID**를 입력합니다. 

    나. **회신 URL** 텍스트 상자에 LinkedIn 포털에서 복사한 **ACS(Assertion Consumer Access) URL**을 입력합니다.

7. **SP 시작**에서 SSO를 구성하려면 구성 섹션에서 고급 URL 설정 표시 옵션을 클릭하고 다음 패턴으로 로그온 URL을 구성합니다.

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. LinkedIn Learning 응용 프로그램은 특정 형식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **사용자 ID**의 기본값은 **user.userprincipalname**이지만 LinkedIn Learning에는 이것이 사용자의 전자 메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있기 위해서입니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집**을 클릭하고 특성을 설정합니다. 사용자는 **전자 메일**, **부서**, **이름** 및 **성**이라는 이름의 4개 클레임을 추가해야 하며, 값은 **user.mail**, **user.department**, **user.givenname** 및 **user.surname**으로 각각 매핑됩니다.

    | 특성 이름 | 특성 값 |
    | --- | --- |
    | email| user.mail |    
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)
    
    a. **특성 추가**를 클릭하여 특성 대화 상자를 엽니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_04.png)

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **확인**을 클릭합니다.

10. **이름** 특성에서 다음 단계를 수행합니다.

    a. 특성을 클릭하여 **특성 편집** 창을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinLearning-tutorial/url_update.png)

    나. **네임스페이스**에서 URL 값을 삭제합니다.
    
    다. **확인**을 클릭하여 설정을 저장합니다.

11. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png) 

12. **저장**을 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

13. **LinkedIn 관리 설정** 섹션으로 이동합니다. XML 파일 업로드 옵션을 클릭하여 Azure Portal에서 다운로드한 XML 파일을 업로드합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. **설정**을 클릭하여 SSO를 사용하도록 설정합니다. SSO 상태가 **연결 안 됨**에서 **연결됨**으로 변경됩니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 

### <a name="creating-a-linkedin-learning-test-user"></a>LinkedIn Learning 테스트 사용자 만들기

LinkedIn Learning 응용 프로그램은 Just-In-Time 사용자 프로비전을 지원하며 인증 후에는 응용 프로그램에서 자동으로 사용자가 생성됩니다. LinkedIn Learning 포털의 관리 설정 페이지에서 **Automatically Assign licenses**(라이선스 자동 할당) 스위치를 전환하여 JIT(Just-in-time) 프로비저닝을 사용하도록 활성화하면 사용자에게 라이선스가 할당됩니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 LinkedIn Learning에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 LinkedIn Learning에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **LinkedIn Learning**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 LinkedIn Learning 타일을 클릭하면 Azure 로그온 페이지가 표시되고 로그온이 완료되면 LinkedIn Learning 응용 프로그램에 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png