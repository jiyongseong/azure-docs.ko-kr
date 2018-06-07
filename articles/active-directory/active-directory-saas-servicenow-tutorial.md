---
title: '자습서: ServiceNow와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 ServiceNow 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: cf955c15b1dfe3ef0c46ab5023035986da5c5c99
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>자습서: ServiceNow와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ServiceNow를 통합하는 방법에 대해 알아봅니다.

ServiceNow를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- ServiceNow에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 ServiceNow에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ServiceNow와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ServiceNow의 경우 ServiceNow의 인스턴스 또는 테넌트, Calgary 버전 이상
- ServiceNow Express의 경우 ServiceNow Express의 인스턴스, Helsinki 버전 이상
- ServiceNow 테넌트에서 [여러 공급자 Single Sign-On 플러그 인](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)을 사용하도록 설정해야 합니다. [서비스 요청을 제출](https://hi.service-now.com)하면 이렇게 설정할 수 있습니다.
- 자동 구성은 ServiceNow에 대한 다중 공급자 플러그 인을 사용하도록 설정합니다.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ServiceNow 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-servicenow-from-the-gallery"></a>갤러리에서 ServiceNow 추가
ServiceNow의 Azure AD 통합을 구성하려면 갤러리의 ServiceNow를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ServiceNow를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **ServiceNow**를 입력하고 결과 패널에서 **ServiceNow**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 ServiceNow](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 ServiceNow에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ServiceNow 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ServiceNow의 관련 사용자 간에 연결이 형성되어야 합니다.

ServiceNow에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

ServiceNow에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[ServiceNow에 대해 Azure AD Single Sign-on 구성](#configure-azure-ad-single-sign-on-for-servicenow)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[ServiceNow Express에 대해 Azure AD Single Sign-on 구성](#configure-azure-ad-single-sign-on-for-servicenow-express)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[ServiceNow 테스트 사용자 만들기](#create-a-servicenow-test-user)** - Azure AD의 Britta Simon 사용자에 연결된 ServiceNow 사용자를 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow에 대해 Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 ServiceNow 응용 프로그램에서 Single Sign-On을 구성합니다.

**ServiceNow에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **ServiceNow** 응용 프로그램 통합 페이지에서 **Single sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. **ServiceNow 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![ServiceNow 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<instance-name>.service-now.com/navpage.do`

    나. **식별자** 텍스트 상자에서 `https://<instance-name>.service-now.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이 값은 자습서의 뒷부분에 설명된 실제 로그온 URL과 식별자로 업데이트해야 합니다.

4. **SAML 서명 인증서** 섹션에서 다음 단계를 수행합니다. 

    ![인증서 다운로드 링크](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. 복사 단추를 클릭하여 **앱 페더레이션 메타 데이터 URL**을 복사하여 메모장에 붙여넣습니다. 이 앱 페더레이션 메타데이터 URL은 자습서의 뒷부분에서 사용됩니다.

    나. **인증서(Base64)** 를 클릭한 다음, 컴퓨터에 인증서 파일을 저장합니다.

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. ServiceNow 응용 프로그램에 관리자 권한으로 로그온합니다.

7. 다음 단계를 따라 **통합 - 여러 공급자 Single Sign-On 설치 관리자** 플러그 인을 활성화합니다.

    a. 왼쪽 탐색 창의 검색 창에서 **시스템 정의** 섹션을 검색한 다음 **플러그 인**을 클릭합니다.

    ![플러그 인 활성화](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "플러그 인 활성화")

     나. **통합 - 여러 공급자 Single Sign-On 설치 관리자**를 검색합니다.

     ![플러그 인 활성화](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "플러그 인 활성화")

    다. 플러그 인을 선택합니다. 마우스 오른쪽 단추를 클릭하고 **활성화/업그레이드**를 선택합니다.

    d. **활성화** 단추를 클릭합니다.

8. **ServiceNow**를 자동 및 수동으로 구성할 수 있는 두 가지 방법이 있습니다.

9. **ServiceNow**를 자동으로 구성하려면 다음 단계를 따르세요.

    a. Azure Portal의 **ServiceNow** Signle-Sign On 페이지로 돌아갑니다.

    나. ServiceNow에 대해 한 번 클릭으로 구성할 수 있는 서비스가 제공됩니다. 즉, Azure AD가 SAML 기반 인증을 위해 ServiceNow를 자동으로 구성합니다. 이 서비스를 사용하도록 설정하려면 **ServiceNow 구성** 섹션으로 이동한 다음 **ServiceNow 구성**을 클릭하여 로그온 구성 창을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

    다. ServiceNow 인스턴스 이름, 관리자 이름 및 관리자 암호를 **로그온 구성** 양식에 입력하고 **지금 구성**을 클릭합니다. 입력하는 관리자 사용자 이름에 ServiceNow의 **security_admin** 역할이 할당되어 있어야 이 절차를 수행할 수 있습니다. 이 방법을 사용하지 않고 SAML ID 공급자로 Azure AD를 사용하도록 ServiceNow를 수동으로 구성하려는 경우에는 **Single Sign-On 수동 구성**을 클릭하고 빠른 참조 섹션에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/configure.png "앱 URL 구성")

    d. ServiceNow 응용 프로그램에 관리자 권한으로 로그온합니다.

    e. 자동 구성에서 필요한 모든 설정을 **ServiceNow** 쪽에 구성하지만 **X.509 인증서**를 기본적으로 사용하지 않습니다. ServiceNow의 ID 공급자에 직접 매핑해야 합니다. 동일한 경우 아래 단계를 수행하세요.
    
    * 왼쪽의 탐색 창의 **다중 공급자 SSO**에서 **ID 공급자**를 클릭합니다.

      ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Single Sign-On 구성")

    * 자동으로 생성되는 ID 공급자를 클릭합니다.

      ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_08.png "Single Sign-On 구성")

    * **X.509 인증서** 섹션으로 아래로 스크롤합니다. **편집**을 선택합니다.

      ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_09.png "Single Sign-On 구성")
    
    * 인증서를 선택하고 오른쪽 화살표 아이콘을 클릭하여 인증서를 추가합니다.

      ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_11.png "Single Sign-On 구성")

    * **저장**을 클릭합니다.

    * 페이지의 오른쪽 위 모퉁이에서 **활성화**를 클릭합니다.

10. **ServiceNow**를 수동으로 구성하려면 다음 단계를 따르세요.

11. ServiceNow 응용 프로그램에 관리자 권한으로 로그온합니다.

12. 왼쪽 탐색 창의 검색 창에서 **Multi-Provider SSO** 섹션을 검색한 다음 **속성**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "앱 URL 구성")

13. **여러 공급자 SSO 속성** 대화 상자에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "앱 URL 구성")

    a. **여러 공급자 SSO 사용**을 **예**로 선택합니다.

    나. **모든 ID 공급자에서 사용자 테이블로 사용자 자동 가져오기 사용**에서 **예**를 선택합니다.

    다. **Multi-Provider SSO 통합에 디버그 로깅 사용**에서 **예**를 선택합니다.

    d. **...하는 사용자 테이블의 필드** 텍스트 상자에서 **user_name**을 입력합니다.

    e. **저장**을 클릭합니다.

14. 왼쪽 탐색 창의 검색 창에서 **Multi-Provider SSO** 섹션을 검색한 다음 **x509 인증서**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Single Sign-On 구성")

15. **X.509 인증서** 대화 상자에서 **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Single Sign-On 구성")

16. **X.509 Certificates** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에 구성의 이름을 입력합니다(예: **TestSAML2.0**).

    나. **활성**을 선택합니다.

    다. **형식**으로 **PEM**을 선택합니다.

    d. **형식**으로 **저장소 인증서 신뢰**를 선택합니다.

    e. Azure에서 다운로드한 Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PEM 인증서** 텍스트 상자에 붙여넣습니다.

     f. **제출**을 클릭합니다.

17. 왼쪽의 탐색 창에서 **ID 공급자**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Single Sign-On 구성")

18. **ID 공급자** 대화 상자에서 **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Single Sign-On 구성")

19. **ID 공급자** 대화 상자에서 **SAML2 Update1?** 을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Single Sign-On 구성")

20. SAML2 Update1 속성 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/idp.png "Single Sign-On 구성")

    a. **ID 공급자 메타데이터 가져오기** 대화 상자에서 **URL** 옵션을 선택합니다.

    나. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 입력합니다.

    다. **가져오기**를 클릭합니다.

21. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에 구성의 이름을 입력합니다(예: **SAML 2.0**).
    
    나. **ServiceNow 홈 페이지** 값을 복사하여 Azure Portal의 **ServiceNow 도메인 및 URL** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    > [!NOTE]
    > ServiceNow 인스턴스 홈 페이지의 URL은 **ServieNow 테넌트 URL**과 **/navpage.do**가 연결된 형태입니다(예: `https://fabrikam.service-now.com/navpage.do`).

    다. **엔터티 ID/발급자** 값을 복사하여 Azure Portal의 **ServiceNow 도메인 및 URL** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    d. **고급**을 클릭합니다. ServiceNow 배포에서 사용자를 고유하게 식별하는 데 사용되는 필드에 따라 **사용자 필드** 텍스트 상자에 **email** 또는 **user_name**을 입력합니다.

    > [!NOTE]
    > Azure Portal의 **ServiceNow > 특성 > Single Sign-On** 섹션으로 이동한 다음 원하는 필드를 **nameidentifier** 특성에 매핑하면 Azure AD 사용자 ID(사용자 계정 이름) 또는 전자 메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.

     e. **x509 인증서** 아래에 이전 단계에서 만든 인증서가 나열됩니다.

     > [!NOTE]
     > ServiceNow를 사용하면 연결 테스트 단추를 클릭하지 않고 Idp를 활성화할 수 없습니다. 동일한 작업을 재정의하려면 아래 단계를 따르세요.

22. 구성의 일부분으로 만든 새 ID 공급자에서 메뉴 아이콘을 클릭하고 목록에서 **copy sys_id**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694992.png "Single Sign-On 구성")

23. 왼쪽 위의 검색 상자에서 **sys_properties.list**를 검색하고 Enter 키를 누릅니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694993.png "Single Sign-On 구성")

24. **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Single Sign-On 구성")

25. **시스템 속성** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694995.png "Single Sign-On 구성")

    a. 이름 텍스트 상자에 `glide.authenticate.sso.redirect.idp` 값을 입력합니다.

    나. **값** 텍스트 상자에 이전 단계에서 복사한 copy sys_id 값을 붙여넣습니다.

    다. **개인**을 선택합니다.

    d. **제출**을 클릭합니다.

26. **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694994.png "Single Sign-On 구성")

27. **시스템 속성** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694996.png "Single Sign-On 구성")

    a. 이름 텍스트 상자에 `glide.authenticate.multisso.test.connection.mandatory` 값을 입력합니다.

    나. **값** 텍스트에 **false**를 입력합니다.

    다. **제출**을 클릭합니다.

28. 위에 단계 이후 이제 새 ID 공급자를 활성화하고 SSO가 작동해야 합니다.

> [!NOTE]
> 또한 새로운 시크릿 창에서 새로운 Idp 구성을 테스트해야 합니다.

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express에 대해 Azure AD Single Sign-On 구성

1. Azure Portal의 **ServiceNow** 응용 프로그램 통합 페이지에서 **Single sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. **ServiceNow 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `https://<instance-name>.service-now.com/navpage.do` 

    나. **식별자** 텍스트 상자에서 `https://<instance-name>.service-now.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [ServiceNow 클라이언트 지원 팀](https://www.servicenow.com/support/contact-support.html)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificates.png)

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. ServiceNow에 대해 한 번 클릭으로 구성할 수 있는 서비스가 제공됩니다. 즉, Azure AD가 SAML 기반 인증을 위해 ServiceNow를 자동으로 구성합니다. 이 서비스를 사용하도록 설정하려면 **ServiceNow 구성** 섹션으로 이동한 다음 **ServiceNow 구성**을 클릭하여 로그온 구성 창을 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. ServiceNow 인스턴스 이름, 관리자 이름 및 관리자 암호를 **로그온 구성** 양식에 입력하고 **지금 구성**을 클릭합니다. 입력하는 관리자 사용자 이름에 ServiceNow의 **security_admin** 역할이 할당되어 있어야 이 절차를 수행할 수 있습니다. 이 방법을 사용하지 않고 SAML ID 공급자로 Azure AD를 사용하도록 ServiceNow를 수동으로 구성하려는 경우에는 **Single Sign-On 수동 구성**을 클릭하고 빠른 참조 섹션에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/configure.png "앱 URL 구성")

8. ServiceNow Express 응용 프로그램에 관리자 권한으로 로그온합니다.

9. 왼쪽 탐색 창에서 **Single Sign-On**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "앱 URL 구성")

10. **Single Sign-on** 대화 상자에서 오른쪽 위의 구성 아이콘을 클릭하고 다음 속성을 설정합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "앱 URL 구성")

    a. **여러 공급자 SSO 사용**을 오른쪽으로 설정/해제합니다.
    
    나. **여러 공급자 SSO 통합에 대한 디버그 로깅 활성화**를 오른쪽으로 설정/해제합니다.
    
    다. **...하는 사용자 테이블의 필드** 텍스트 상자에서 **user_name**을 입력합니다.

11. **Single Sign-On** 대화 상자에서 **새 인증서 추가**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Single Sign-On 구성")

12. **X.509 Certificates** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에 구성의 이름을 입력합니다(예: **TestSAML2.0**).

    나. **활성**을 선택합니다.

    다. **형식**으로 **PEM**을 선택합니다.

    d. **형식**으로 **저장소 인증서 신뢰**를 선택합니다.

    e. Azure Portal에서 다운로드한 Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PEM 인증서** 텍스트 상자에 붙여넣습니다.

    f. **업데이트**를 클릭합니다.

13. **Single Sign-On** 대화 상자에서 **새 IdP 추가**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Single Sign-On 구성")

14. **새 ID 공급자 추가** 대화 상자의 **ID 공급자 구성** 아래에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에 구성의 이름을 입력합니다(예: **SAML 2.0**).

    나. **ID 공급자 URL** 필드에 Azure Portal에서 복사한 **ID 공급자 ID** 값을 붙여넣습니다.
    
    다. **ID 공급자 AuthnRequest** 필드에 Azure Portal에서 복사한 **인증 요청 URL** 값을 붙여넣습니다.

    d. **ID 공급자 SingleLogoutRequest** 필드에 Azure Portal에서 복사한 **Single Sign-Out 서비스 URL** 값을 붙여넣습니다.

    e. **ID 공급자 인증서**로 이전 단계에서 만든 인증서를 선택합니다.

15. **고급 설정**을 클릭하고 **추가 ID 공급자 속성** 아래에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Single Sign-On 구성")

    a. **IDP의 SingleLogoutRequest에 대한 프로토콜 바인딩** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**를 입력합니다.

    나. **NameID 정책** 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**를 입력합니다.

    다. **AuthnContextClassRef 메서드**에 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`를 입력합니다.

    d. **AuthnContextClass 만들기**의 선택을 취소합니다.

16. **추가 서비스 공급자 속성** 아래에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Single Sign-On 구성")

    a. **ServiceNow 홈페이지** 텍스트 상자에 ServiceNow 인스턴스 홈페이지의 URL을 입력합니다.

    > [!NOTE]
    > ServiceNow 인스턴스 홈 페이지의 URL은 **ServieNow 테넌트 URL**과 **/navpage.do**가 연결된 형태입니다(예: `https://fabrikam.service-now.com/navpage.do`).

    나. **엔터티 ID/발급자** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.

    다. **대상 URI** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.

    d. **시계 기울이기** 텍스트 상자에 **60**을 입력합니다.

    e. ServiceNow 배포에서 사용자를 고유하게 식별하는 데 사용되는 필드에 따라 **사용자 필드** 텍스트 상자에 **email** 또는 **user_name**을 입력합니다.

    > [!NOTE]
    > Azure Portal의 **ServiceNow > 특성 > Single Sign-On** 섹션으로 이동한 다음 원하는 필드를 **nameidentifier** 특성에 매핑하면 Azure AD 사용자 ID(사용자 계정 이름) 또는 전자 메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.

    f. **저장**을 클릭합니다.
 
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-servicenow-test-user"></a>ServiceNow 테스트 사용자 만들기

이 섹션에서는 ServiceNow에서 Britta Simon이라는 사용자를 만듭니다. ServiceNow 또는 ServiceNow Express 계정에 사용자를 추가하는 방법을 모르는 경우 [ServiceNow 클라이언트 지원 팀](https://www.servicenow.com/support/contact-support.html)에 문의합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ServiceNow에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 ServiceNow에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **ServiceNow**를 선택합니다.

    ![응용 프로그램 목록의 ServiceNow 링크](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ServiceNow 타일을 클릭하면 ServiceNow 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

