---
title: '자습서: Palo Alto Networks - Admin UI와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks - Admin UI 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aa3366810a40b004fe510cb2909f8da0f3513ddb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Palo Alto Networks - Admin UI와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks - Admin UI를 통합하는 방법에 대해 알아봅니다.

Azure AD와 Palo Alto Networks - Admin UI를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- Palo Alto Networks - Admin UI에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Palo Alto Networks - Admin UI에 자동으로 로그온(SSO, Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Palo Alto Networks - Admin UI와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Palo Alto Networks 차세대 방화벽 또는 Panorama(방화벽을 위한 중앙 집중식 관리 시스템)

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경을 사용하는 것은 바람직하지 *않습니다*.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 Palo Alto Networks - Admin UI 추가
* Azure AD Single Sign-on 구성 및 테스트

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>갤러리에서 Palo Alto Networks - Admin UI 추가
Azure AD와 Palo Alto Networks - Admin UI의 통합을 구성하려면 다음과 같이 갤러리의 Palo Alto Networks - Admin UI를 관리되는 SaaS 앱 목록에 추가하세요.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 창][2]
    
3. 새 응용 프로그램을 추가하려면 이 창의 맨 위쪽에 있는 **새 응용 프로그램** 단추를 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **Palo Alto Networks - Admin UI**를 입력하고, 결과 목록에서 **Palo Alto Networks - Admin UI**를 선택한 다음, **추가**를 선택합니다.

    ![결과 목록의 Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Palo Alto Networks - Admin UI에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Palo Alto Networks - Admin UI 사용자와 이 사용자에 대응하는 Azure AD의 사용자에 대해 알고 있어야 합니다. 즉, Azure AD 사용자와 Palo Alto Networks - Admin UI의 동일한 사용자 간에 연결 관계가 형성되어야 합니다.

링크 관계를 설정하려면 Azure AD의 *사용자 이름* 값을 Palo Alto Networks - Admin UI *사용자 이름*으로 할당합니다.

Palo Alto Networks - Admin UI에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 5개 섹션에서 구성 요소를 완료해야 합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

다음과 같이 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Palo Alto Networks - Admin UI 응용 프로그램에서 Single Sign-On을 구성합니다.

1. Azure Portal의 **Palo Alto Networks - Admin UI** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![“Single Sign-On” 링크][4]

2. **Single Sign-On** 창의 **Single Sign-On 모드** 상자에서 **SAML 기반 로그온**을 선택합니다.
 
    ![“Single Sign-On” 창](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. **Palo Alto Networks - Admin UI 도메인 및 URL** 아래에서 다음을 수행합니다.

    !["Palo Alto Networks - Admin UI 도메인 및 URL" Single Sign-On 정보](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. **로그온 URL** 상자에 *https://\<고객 방화벽 FQDN>/php/login.php* 형식으로 URL을 입력합니다.

    나. **식별자** 상자에 *https://\<고객 방화벽 FQDN>:443/SAML20/SP* 형식으로 URL을 입력합니다.
    
    다. **회신 URL** 상자에 *https://\<고객 방화벽 FQDN>:443/SAML20/SP/ACS* 형식으로 ACS(Assertion Consumer Service) URL을 입력합니다.
    
    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트합니다. 이러한 값을 얻으려면 [Palo Alto Networks - Admin UI 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의합니다. 
 
4. Palo Alto Networks - Admin UI 응용 프로그램은 특정 형식의 SAML 어설션을 기대하므로 다음 이미지에 나와 있는 것처럼 클레임을 구성하세요. 다음과 같이 **응용 프로그램 통합** 페이지의 **사용자 특성** 섹션에서 특성 값을 관리할 수 있습니다.
    
    ![SAML 토큰 특성 목록](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > 특성 값은 예시일 뿐이므로 *username* 및 *adminrole*에 적절한 값을 매핑하세요. 방화벽에서 특정 가상 시스템에 대한 관리자 액세스를 제한하는 데 사용되는 다른 선택적 특성 *accessdomain*이 있습니다.
   >
        
    | 특성 이름 | 특성 값 |
    | --- | --- |    
    | 사용자 이름 | user.userprincipalname |
    | adminrole | customadmin |

    a. **특성 추가**를 선택합니다.  
    
    !["특성 추가" 단추](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    **특성 추가** 창이 열립니다.

    !["특성 추가" 창](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    나. **이름** 텍스트 상자에 해당 행에 표시되는 특성 이름을 입력합니다.
    
    다. **값** 상자에서 해당 행에 표시되는 특성 값을 입력합니다.
    
    d. **확인**을 선택합니다.

    > [!NOTE]
    > 특성에 대한 자세한 내용은 다음 아티클을 참조하세요.
    > * [Admin UI(adminrole)에 대한 관리 역할 프로필](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI(accessdomain)에 대한 장치 액세스 도메인](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. **SAML 서명 인증서** 아래에서 **메타데이터 XML**을 선택한 후 **저장**을 선택합니다.

    ![메타데이터 XML 다운로드 링크](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![저장 단추](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. 새 창에서 관리자 권한으로 Palo Alto Networks Firewall Admin UI를 엽니다.

7. **장치** 탭을 선택합니다.

    ![장치 탭](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. 왼쪽 창에서 **SAML ID 공급자**를 선택한 후 **가져오기**를 선택하여 메타데이터 파일을 가져옵니다.

    ![메타데이터 파일 가져오기 단추](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. **SAML ID 공급자 서버 프로필 가져오기** 창에서 다음을 수행합니다.

    !["SAML ID 공급자 서버 프로필 가져오기" 창](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. **프로필 이름** 상자에 이름(예: **AzureAD Admin UI**)을 제공합니다.
    
    나. **ID 공급자 메타데이터** 아래에서 **찾아보기**를 선택하고 앞서 Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.
    
    다. **ID 공급자 인증서의 유효성 검사** 확인란을 선택 취소합니다.
    
    d. **확인**을 선택합니다.
    
    e. 방화벽의 구성을 커밋하려면 **커밋**을 선택합니다.

10. 왼쪽 창에서 **SAML ID 공급자**를 선택한 후 이전 단계에서 만든 SAML ID 공급자 프로필(예: **AzureAD Admin UI**)을 선택합니다. 

    ![SAML ID 공급자 프로필](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. **SAML ID 공급자 서버 프로필** 창에서 다음을 수행합니다.

    !["SAML ID 공급자 서버 프로필" 창](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. **ID 공급자 SLO URL** 상자에서 이전에 가져온 SLO URL을 **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**으로 바꿉니다.
  
    나. **확인**을 선택합니다.

12. Palo Alto Networks Firewall Admin UI에서 **장치**를 선택한 후 **관리자 역할**을 선택합니다.

13. **추가** 단추를 선택합니다. 

14. **관리자 역할 프로필** 창의 **이름** 상자에 관리자 역할의 이름을 제공합니다(예: **fwadmin**).  
    이 관리자 역할 이름은 ID 공급자가 보낸 SAML 관리자 역할 특성 이름과 일치해야 합니다. 관리자 역할 이름 및 값은 4단계에서 생성되었습니다.

    ![Palo Alto Networks 관리자 역할 구성](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Firewall Admin UI에서 **장치**를 선택한 후 **인증 프로필**을 선택합니다.

16. **추가** 단추를 선택합니다. 

17. **인증 프로필** 창에서 다음을 수행합니다. 

    !["인증 프로필" 창](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. **이름** 상자에 이름(예: **AzureSAML_Admin_AuthProfile**)을 제공합니다.
    
    나. **형식** 드롭다운 목록에서 **SAML**을 선택합니다. 
   
    다. **IdP 서버 프로필** 드롭다운 목록에서 적절한 SAML ID 공급자 서버 프로필(예: **AzureAD Admin UI**)을 선택합니다.
   
    다. **단일 로그아웃 사용** 확인란을 선택합니다.
    
    d. **관리자 역할 특성** 상자에 특성 이름(예: **adminrole**)을 입력합니다. 
    
    e. **고급** 탭을 선택한 후 **허용 목록**에서 **추가**를 선택합니다. 
    
    ![고급 탭의 추가 단추](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. **모두** 확인란을 선택하거나 이 프로필로 인증될 수 있는 사용자 및 그룹을 선택합니다.  
    사용자를 인증할 때 방화벽은 연결된 사용자 이름 또는 그룹이 이 목록에 있는 항목과 일치하는지 비교합니다. 항목을 추가하지 않은 경우 사용자가 인증될 수 없습니다.

    g. **확인**을 선택합니다.

18. 관리자가 Azure를 통해 SAML SSO를 사용하도록 허용하려면 **장치** > **설정**을 선택합니다. **설정** 창에서 **관리** 탭을 선택한 후 **인증 설정**에서 **설정** ("기어") 단추를 선택합니다. 

 ![설정 단추](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. 17단계에서 만든 SAML 인증 프로필(예: **AzureSAML_Admin_AuthProfile**)을 선택합니다.

 ![인증 프로필 필드](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. **확인**을 선택합니다.

21. 구성을 커밋하려면 **커밋**을 선택합니다.


> [!TIP]
> 앱을 설정할 때 [Azure Portal](https://portal.azure.com)에서 이전 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에 **Single Sign-On** 탭을 선택하고 창 맨 아래에 있는 **구성** 섹션에서 포함된 설명서에 액세스합니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 다음을 수행하여 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![Azure AD 테스트 사용자 만들기][100]

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 링크](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. 현재 사용자 목록을 표시하려면 **사용자 및 그룹** > **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. **모든 사용자** 창 상단에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **사용자** 창이 열립니다.

4. **사용자** 창에서 다음을 수행합니다.

    ![사용자 창](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Palo Alto Networks - Admin UI 테스트 사용자 만들기

Palo Alto Networks - Admin UI는 Just-In-Time 사용자 프로비전을 지원합니다. 사용자 아직 없을 경우 인증 성공 후 시스템에 사용자가 자동으로 생성됩니다. 사용자를 만들기 위해 별다른 조치를 취할 필요가 없습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이라는 사용자가 Azure Single Sign-On을 사용할 수 있도록 Palo Alto Networks - Admin UI에 대한 액세스 권한을 부여합니다. 이렇게 하려면 다음을 수행합니다.

![사용자 역할 할당][200] 

1. Azure Portal에서 **응용 프로그램** 보기를 열고 **디렉터리** 보기로 이동한 후 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 및 "모든 응용 프로그램" 링크][201] 

2. **응용 프로그램** 목록에서 **Palo Alto Networks - Admin UI**를 선택합니다.

    ![Palo Alto Networks - Admin UI 링크](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가**를 선택한 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 창의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **선택** 단추를 선택합니다.

7. **할당 추가** 창에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Palo Alto Networks - Admin UI 타일을 선택하면 Palo Alto Networks - Admin UI 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

