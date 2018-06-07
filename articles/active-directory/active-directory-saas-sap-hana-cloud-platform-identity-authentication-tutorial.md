---
title: '자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Cloud Platform Identity Authentication 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: e7cc1df5e550dec62869c2a6f68cdc2a84167142
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352415"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Cloud Platform Identity Authentication을 통합하는 방법에 대해 알아봅니다. SAP Cloud Platform Identity Authentication은 Azure AD를 기본 IdP로 사용하는 SAP 응용 프로그램에 액세스하기 위한 프록시 IdP로 사용됩니다.

SAP Cloud Platform Identity Authentication을 Azure AD에 통합하면 다음과 같은 이점이 제공됩니다.

- SAP 응용 프로그램에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자들이 Azure AD 계정으로 SAP 응용 프로그램에 자동 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

SaaS 앱과 Azure AD의 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP Cloud Platform Identity Authentication과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Single Sign-On이 활성화된 SAP Cloud Platform Identity Authentication 구독.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 받으세요](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP Cloud Platform Identity Authentication 추가
2. Azure AD Single Sign-on 구성 및 테스트

본격적으로 세부적인 기술 정보를 살펴보기에 앞서 먼저 여기서 다루는 개념을 이해해야 합니다. SAP Cloud Platform Identity Authentication 및 Active Directory Federation Services를 이용하면 SAP Cloud Platform Identity Authentication으로 보호되는 SAP 응용 프로그램과 서비스를 이용하여 (IdP로 사용되는) Azure AD로 보호되는 응용 프로그램과 서비스에서 SSO를 구현할 수 있습니다.

현재는 SAP Cloud Platform Identity Authentication이 SAP 응용 프로그램에 대한 프록시 ID 공급자로 기능합니다. 그리고 Azure Active Directory는 이 설정에서 주요 ID 공급자로 작동합니다. 

아래 다이어그램에서 이 관계를 확인할 수 있습니다.

![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

이 설정을 적용하면 SAP Cloud Platform Identity Authentication 테넌트가 Azure Active Directory에서 신뢰할 수 있는 응용 프로그램으로 구성됩니다. 

이 방법으로 보호하려는 모든 SAP 응용 프로그램 및 서비스는 이후 SAP Cloud Platform Identity Authentication 관리 콘솔에서 구성됩니다.

따라서 (Azure Active Directory가 아닌) SAP Cloud Platform Identity Authentication에서 SAP 응용 프로그램 및 서비스에 대한 액세스 권한 부여를 수행해야 합니다.

Azure Active Directory Marketplace를 통해 SAP Cloud Platform Identity Authentication을 응용 프로그램으로서 구성하면 개별 클레임 또는 SAML 어설션을 구성할 필요가 없습니다.

>[!NOTE] 
>현재로서는 웹 SSO만 양측에 의해 테스트되었습니다. 앱-API 또는 API-API 통신에 필요한 흐름은 작동하긴 하나 테스트는 거치지 않았습니다. 이는 후속 작업의 일부로 테스트될 예정입니다.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>갤러리에서 SAP Cloud Platform Identity Authentication 추가
SAP Cloud Platform Identity Authentication을 Azure AD에 통합하려면 갤러리의 SAP Cloud Platform Identity Authentication을 관리되는 SaaS 앱 목록으로 추가해야 합니다.

**갤러리에서 SAP Cloud Platform Identity Authentication을 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 새로 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SAP Cloud Platform Identity Authentication**을 입력합니다. 

5. 결과 패널에서 **SAP Cloud Platform Identity Authentication**을 선택하고 **추가** 단추를 선택합니다.

    ![결과 목록에서 SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 SAP Cloud Platform Identity Authentication에서 Azure AD Single Sign-On을 구성하고 테스트합니다. 구성과 테스트는 “Britta Simon”이라는 테스트 사용자를 이용하여 수행합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP Cloud Platform Identity Authentication 사용자가 누구인지 알아야 합니다. 즉, Azure AD 사용자와 SAP Cloud Platform Identity Authentication의 해당 사용자 사이에 연결 관계가 형성되어야 합니다.

SAP Cloud Platform Identity Authentication의 **Username** 값에 Azure AD의 **user name**과 같은 값을 입력합니다. 이렇게 해서 두 사용자 간의 연결 관계를 형성했습니다.

SAP Cloud Platform Identity Authentication에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 사항을 완료합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [SAP Cloud Platform Identity Authentication 테스트 사용자 만들기](#create-an-sap-cloud-platform-identity-authentication-test-user) - Azure AD의 Britta Simon 사용자와 연결된 SAP Cloud Platform Identity Authentication 사용자를 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP Cloud Platform Identity Authentication 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAP Cloud Platform Identity Authentication에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SAP Cloud Platform Identity Authentication** 응용 프로그램 통합 페이지에서 **Single sign-on**을 선택합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자의 **SAML 기반 로그온**에서 **모드**를 선택하여 Single Sign-On을 활성화합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. **IDP** 시작 모드로 응용 프로그램을 구성하려면 **SAP Cloud Platform Identity Authentication 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.  

    ![SAP Cloud Platform Identity Authentication 도메인 및 URL Single Sign-On이 설정된 구독](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    a. **식별자** 상자에 다음과 같은 패턴을 사용하여 URL을 입력합니다. `<IAS-tenant-id>.accounts.ondemand.com`

    나. **회신 URL** 상자에 다음 패턴으로 URL을 입력합니다. `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이 값을 얻으려면 [SAP Cloud Platform Identity Authentication 클라이언트 지원 팀](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)에 문의하세요. 식별자 값을 모르는 경우 SAP Cloud Platform Identity Authentication 설명서의 [Tenant SAML 2.0 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) 관련 내용을 참조하세요.

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 선택합니다.

    ![SAP Cloud Platform Identity Authentication 도메인 및 URL Single Sign-On이 설정된 구독](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    **로그온 URL** 상자에 다음과 같은 패턴을 갖는 URL을 입력합니다. `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트하세요. 해당 비즈니스 응용 프로그램 로그온 URL을 사용하세요. 궁금한 점은 [SAP Cloud Platform Identity Authentication 클라이언트 지원 팀](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)에 문의하세요.

5. **SAML 서명 인증서** 섹션 아래에서 **메타데이터 XML**을 선택합니다. 그런 다음 메타데이터 파일을 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform Identity Authentication 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 특성 값을 관리할 수 있습니다. 다음 스크린샷은 이 형식의 예를 보여줍니다. 

    ![Single Sign-On 구성](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. SAP 응용 프로그램에서 **firstName**과 같은 특성이 필요한 경우 **사용자 특성** 섹션에 **firstName** 특성을 추가합니다. 이 옵션은 **SAML 토큰 특성** 대화 상자의 **Single Sign-On** 대화 상자에서 확인할 수 있습니다.

    a. **특성 추가** 대화 상자를 열려면 **특성 추가**를 선택합니다. 
    
    ![Single Sign-On 구성](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Single Sign-On 구성](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에 특성 이름 **firstName**을 입력합니다.
    
    다. **값** 목록에서 특성 값 **user.givenname**을 선택합니다.
    
    d. **확인**을 선택합니다.

8. **저장** 단추를 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. **SAP Cloud Platform Identity Authentication 구성** 섹션에서 **SAP Cloud Platform Identity Authentication 구성**을 선택하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![SAP Cloud Platform Identity Authentication 구성](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. 응용 프로그램에 대해 SSO를 구성하려면 SAP Cloud Platform Identity Authentication 관리 콘솔로 이동합니다. URL 패턴은 다음과 같습니다. `https://<tenant-id>.accounts.ondemand.com/admin` [Microsoft Azure AD와 통합](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)에서 SAP Cloud Platform Identity Authentication 설명서를 참조하세요. 

11. Azure Portal에서 **저장** 단추를 선택합니다.

12. 다른 SAP 응용 프로그램에 SSO를 추가하고 활성화하려는 경우에만 다음 단계를 계속합니다. **갤러리에서 SAP Cloud Platform Identity Authentication 추가** 섹션의 단계를 반복합니다.

13. Azure Portal의 **SAP Cloud Platform Identity Authentication** 응용 프로그램 통합 페이지에서 **연결된 로그온**을 선택합니다.

    ![연결된 로그온 구성](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. 구성을 저장합니다.

>[!NOTE] 
>새 응용 프로그램은 이전 SAP 응용 프로그램의 Single Sign-On 구성을 사용합니다. SAP Cloud Platform Identity Authentication 관리 콘솔에서 동일한 회사 ID 공급자를 사용했는지 확인합니다.

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 간결한 지침을 읽어 보세요.  **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 다음에 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션에서 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication 테스트 사용자 만들기

SAP Cloud Platform Identity Authentication에서 사용자를 만들 필요가 없습니다. Azure AD 사용자 저장소에 있는 사용자는 SSO 기능을 사용할 수 있습니다.

SAP Cloud Platform Identity Authentication에서는 ID 페더레이션 옵션을 지원합니다. 이 옵션을 통해 응용 프로그램에서 기업 ID 공급자가 인증한 사용자가 SAP Cloud Platform Identity Authentication의 사용자 저장소에 있는지 확인할 수 있습니다. 

ID 페더레이션 옵션은 기본적으로 사용 안 함으로 설정되어 있습니다. ID 페더레이션을 사용하도록 설정하면 SAP Cloud Platform Identity Authentication으로 가져온 사용자만 응용 프로그램에 액세스할 수 있습니다. 

SAP Cloud Platform Identity Authentication에서 ID 페더레이션을 사용 또는 사용하지 않도록 설정하는 방법은 [SAP Cloud Platform Identity Authentication의 사용자 저장소에서 ID 페더레이션 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)의 “SAP Cloud Platform Identity Authentication으로 ID 페더레이션 설정”을 참조하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Cloud Platform Identity Authentication에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**SAP Cloud Platform Identity Authentication에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램**으로 이동한 다음 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP Cloud Platform Identity Authentication**을 선택합니다.

    ![응용 프로그램 목록에서 SAP Cloud Platform Identity Authentication 링크](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Cloud Platform Identity Authentication 타일을 선택하면 SAP Cloud Platform Identity Authentication 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png
