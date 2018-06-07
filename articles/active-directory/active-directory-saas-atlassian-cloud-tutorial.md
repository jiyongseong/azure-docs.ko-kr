---
title: '자습서: Atlassian Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Atlassian Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 261baed7b8e1e5480396c06a9f92ecfb48a8b7ac
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>자습서: Atlassian Cloud와 Azure Active Directory 통합

이 자습서에서는 Atlassian Cloud와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다.

Atlassian Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 Atlassian Cloud에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Atlassian Cloud에 자동으로 로그온(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Atlassian 클라우드 제품에 대한 SAML(Security Assertion Markup Language) Single Sign-On 사용을 설정하려면 Identity Manager를 설정해야 합니다. [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)에 대해 자세히 알아보세요.

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하지 않는 것이 좋습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 Atlassian Cloud 추가
* Azure AD Single Sign-on 구성 및 테스트

## <a name="add-atlassian-cloud-from-the-gallery"></a>갤러리에서 Atlassian Cloud 추가
Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 다음을 수행하여 갤러리의 Atlassian Cloud를 관리되는 SaaS 앱 목록에 추가합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    ![Enterprise 응용 프로그램 창][2]
    
3. 응용 프로그램을 추가하려면 **새 응용 프로그램**을 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **Atlassian Cloud**를 입력하고 결과 목록에서 **Atlassian Cloud**를 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 *Britta Simon*이라는 테스트 사용자를 기반으로 하여 Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Atlassian Cloud 사용자와 이 사용자에 대응하는 Azure AD의 사용자에 대해 알고 있어야 합니다. 즉, Azure AD 사용자와 Atlassian Cloud의 관련 사용자 간에 연결을 형성해야 합니다.

연결을 설정하려면 Azure AD *사용자 이름*에 할당한 값을 Atlassian Cloud *사용자 이름*으로 동일하게 할당합니다.

Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 섹션의 구성 요소를 완료해야 합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Atlassian Cloud 응용 프로그램에서 Single Sign-On을 구성합니다.

Atlassian Cloud에서 Azure AD Single Sign-on을 구성하려면 다음을 수행합니다.

1. Azure Portal의 **Atlassian Cloud** 응용 프로그램 통합 창에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 창의 **Single Sign-On 모드** 상자에서 **SAML 기반 로그온**을 선택합니다.

    ![Single Sign-On 창](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. **Atlassian Cloud 도메인 및 URL** 아래에서 IDP 시작 모드로 응용 프로그램을 구성하려는 경우 다음을 수행합니다.

    ![Atlassian Cloud 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. **식별자** 상자에 **`https://auth.atlassian.com/saml/<unique ID>`** 를 입력합니다.
    
    나. **회신 URL** 상자에 **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`** 를 입력합니다.

    다. **릴레이 상태** 상자에 **`https://<instancename>.atlassian.net`** 구문으로 URL을 입력합니다.

4. SP 시작 모드로 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 선택한 후 **로그온 URL** 상자에 **`https://<instancename>.atlassian.net`** 구문으로 URL을 입력합니다.

    ![Atlassian Cloud 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > 위의 값은 실제가 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL 값으로 업데이트합니다. Atlassian Cloud SAML 구성 화면에서 실제 값을 가져올 수 있습니다. 이러한 값은 자습서의 뒷부분에서 설명합니다.

5. **SAML 서명 인증서** 아래에서 **인증서(Base64)** 를 선택한 후 사용자의 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Atlassian Cloud 응용 프로그램은 특정 형식의 SAML 어설션을 찾아야 하며, 이를 위해 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 

    기본적으로 **사용자 ID** 값은 user.userprincipalname에 매핑됩니다. 이 값을 user.mail에 매핑되도록 변경합니다. 조직의 설정에 따라 적절한 다른 값을 선택할 수도 있지만, 대부분의 경우 메일을 사용하면 됩니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. **저장**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. **Atlassian Cloud 구성** 창을 열려면 **Atlassian Cloud 구성** 섹션에서 **Atlassian Cloud 구성**을 선택합니다.

9. **빠른 참조** 섹션에서 **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Atlassian Cloud 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. 응용 프로그램에 SSO를 구성하려면 관리자 자격 증명을 사용하여 Atlassian Portal에 로그인합니다.

11. Single Sign-On을 구성하기 전에 도메인을 확인해야 합니다. 자세한 내용은 [Atlassian 도메인 확인](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) 문서를 참조하세요.

12. 왼쪽 창에서 **SAML Single Sign-On**을 선택합니다. 아직 하지 않는 경우 Atlassian Identity Manager를 구독합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. **Add SAML configuration**(SAML 구성 추가) 창에서 다음을 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. **Identity provider Entity ID**(ID 공급자 엔터티 ID) 상자에 Azure Portal에서 복사한 SAML 엔터티 ID를 붙여넣습니다.

    나. **ID 공급자 SSO URL** 상자에 Azure Portal에서 복사한 SAML Single Sign-On 서비스 URL을 붙여넣습니다.

    다. Azure Portal에서 .txt 파일로 다운로드한 인증서를 열고 값(*시작 인증서* 및 *끝 인증서* 행 제외)을 복사하여 **공용 X509 인증서** 상자에 붙여넣습니다.
    
    d. **구성 저장**을 선택합니다.
     
14. 올바른 URL을 설정했는지 확인하려면 다음을 수행하여 Azure AD 설정을 업데이트합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. SAML 창에서 **SP Identity ID**(SP ID)를 복사한 후 Azure Portal의 Atlassian Cloud **도메인 및 URL** 아래에서 **식별자** 상자에 붙여넣습니다.
    
    나. SAML 창에서 **SP Assertion Consumer Service URL**을 복사한 후 Azure Portal의 Atlassian Cloud **도메인 및 URL** 아래에서 **회신 URL** 상자에 붙여넣습니다. [로그인 URL]은 Atlassian Cloud의 테넌트 URL입니다.

    > [!NOTE]
    > 기존 고객인 경우 Azure Portal에서 **SP Identity ID**(SP ID) 및 **SP Assertion Consumer Service URL** 값을 업데이트한 후 **Yes, update configuration**(예, 구성 업데이트)을 선택합니다. 새 고객인 경우 이 단계를 건너뛸 수 있습니다.
    
15. Azure Portal에서 **저장**을 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

   ![Azure AD 테스트 사용자 만들기][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹** > **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. **모든 사용자** 창에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. **사용자** 창에서 다음을 수행합니다.

    ![사용자 창](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.

### <a name="create-an-atlassian-cloud-test-user"></a>Atlassian Cloud 테스트 사용자 만들기

Azure AD 사용자가 Atlassian Cloud에 로그인하도록 하려면 Atlassian Cloud에서 다음을 수행하여 사용자 계정을 수동으로 프로비전합니다.

1. **관리** 창에서 **사용자**를 선택합니다.

    ![Atlassian Cloud 사용자 링크](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Atlassian Cloud에서 사용자를 만들려면 **사용자 초대**를 선택합니다.

    ![Atlassian Cloud 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. **메일 주소** 상자에 사용자의 메일 주소를 입력한 다음 응용 프로그램 액세스 권한을 할당합니다.

    ![Atlassian Cloud 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. 사용자에게 메일 초대를 보내려면 **사용자 초대**를 선택합니다. 사용자에게 메일 초대가 전송되고 사용자가 초대를 수락하면 시스템에서 활성화됩니다.

>[!NOTE]
>**사용자** 섹션에서 **대량 만들기** 단추를 선택하여 사용자를 대량으로 만들 수도 있습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 사용자 Britta Simon에게 Atlassian Cloud에 대한 액세스 권한을 부여합니다. 이렇게 하려면 다음을 수행합니다.

![사용자 역할 할당][200]

1. Azure Portal에서 **응용 프로그램** 보기를 열고 디렉터리 보기로 이동한 후 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. **응용 프로그램** 목록에서 **Atlassian Cloud**를 선택합니다.

    ![응용 프로그램 목록의 Atlassian Cloud 링크](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가**를 선택한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 창의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 창에서 **선택**을 선택합니다.

7. **할당 추가** 창에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 **Atlassian Cloud** 타일을 선택하면 Atlassian Cloud 응용 프로그램에 자동으로 로그인됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
