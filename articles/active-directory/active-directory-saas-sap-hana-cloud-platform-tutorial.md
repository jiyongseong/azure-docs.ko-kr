---
title: '자습서: SAP Cloud Platform과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Cloud Platform 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 4d39c8437cbed497e2a2a7e64caa05f8e3d04869
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>자습서: SAP Cloud Platform과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Cloud Platform을 통합하는 방법에 대해 알아봅니다.

Azure AD와 SAP Cloud Platform을 통합하면 다음과 같은 이점이 제공됩니다.

- SAP Cloud Platform에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP Cloud Platform에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP Cloud Platform과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SAP Cloud Platform Single Sign-On이 설정된 구독

이 자습서를 완료한 후 SAP Cloud Platform에 할당한 Azure AD 사용자가 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

>[!IMPORTANT]
>사용자 고유의 응용 프로그램을 배포하거나 SAP Cloud Platform 계정에 대 한 single sign 테스트하도록 응용 프로그램을 구독해야 합니다. 이 자습서에서는 응용 프로그램이 계정에 배포됩니다.
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP Cloud Platform 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>갤러리에서 SAP Cloud Platform 추가
SAP Cloud Platform의 Azure AD 통합을 구성하려면 갤러리의 SAP Cloud Platform을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP Cloud Platform을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SAP Cloud Platform**을 입력하고 결과 패널에서 **SAP Cloud Platform**을 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록에서 SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP Cloud Platform에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP Cloud Platform의 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP Cloud Platform의 관련 사용자 간에 연결이 형성되어야 합니다.

SAP Cloud Platform에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

SAP Cloud Platform에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SAP Cloud Platform 테스트 사용자 만들기](#create-a-sap-cloud-platform-test-user)** - SAP Cloud Platform에 Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP Cloud Platform 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAP Cloud Platform에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SAP Cloud Platform** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. **SAP Cloud Platform 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![SAP Cloud Platform 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. **로그온 URL** 텍스트 상자에 **SAP Cloud Platform** 응용 프로그램에 사용자가 로그인하기 위해 사용한 URL을 입력합니다. SAP Cloud Platform 응용 프로그램에서 보호된 리소스의 계정 관련 URL입니다. URL은 다음 패턴을 기반으로 합니다. `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >사용자의 인증을 필요로 하는 SAP Cloud Platform 응용 프로그램의 URL입니다.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    나. **식별자** 텍스트 상자에 다음 패턴 중 하나를 사용하여 SAP Cloud Platform의 URL 형식을 입력합니다. 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 로그온 URL 및 식별자를 가져오려면 [SAP Cloud Platform 클라이언트 지원 팀](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)에 문의하세요. 이 자습서의 뒷부분에 설명되어 있는 트러스트 관리 섹션에서 회신 URL을 가져올 수 있습니다.
    > 
     
4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. 다른 웹 브라우저 창의 `https://account.<landscape host>.ondemand.com/cockpit`(예: https://account.hanatrial.ondemand.com/cockpit))에 있는 SAP Cloud Platform Cockpit에 로그인합니다.

7. **신뢰** 탭을 클릭합니다.
   
    ![신뢰](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "신뢰")

8. 트러스트 관리 섹션의 **로컬 서비스 공급자** 아래에서 다음 단계를 수행합니다.

    ![신뢰 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "신뢰 관리")
   
    a. **편집**을 클릭합니다.

    나. **구성 유형**으로 **사용자 지정**을 선택합니다.

    다. **로컬 공급자 이름**으로 기본값을 그대로 사용합니다. 이 값을 복사하고 SAP Cloud Platform의 Azure AD 구성에서 **식별자** 필드로 붙여넣습니다.

    d. **서명 키** 및 **서명 인증서** 키 쌍을 생성하려면 **키 쌍 생성**을 클릭합니다.

    e. **주 전파**로 **사용 안 함**을 선택합니다.

    f. **강제 인증**으로 **사용 안 함**을 선택합니다.

    g. **저장**을 클릭합니다.

9. **로컬 서비스 공급자** 설정을 저장한 후 다음을 수행하여 회신 URL을 가져옵니다.
   
    ![메타 데이터 가져오기](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "메타 데이터 가져오기")

    a. **메타데이터 가져오기**를 클릭하여 SAP Cloud Platform 메타데이터 파일을 다운로드합니다.

    나. 다운로드한 SAP Cloud Platform 메타데이터 XML 파일을 연 다음 **ns3:AssertionConsumerService** 태그를 찾습니다.
 
    다. **위치** 특성의 값을 복사한 다음 SAP Cloud Platform의 Azure AD 구성에서 **회신 URL** 필드로 붙여넣습니다.

10. **신뢰할 수 있는 ID 공급자** 탭을 클릭한 다음 **신뢰할 수 있는 ID 공급자 추가**를 클릭합니다.
   
    ![신뢰 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "신뢰 관리")
   
    >[!NOTE]
    >신뢰할 수 있는 ID 공급자 목록을 관리하려면, 로컬 서비스 공급자 섹션에서 사용자 할당 구성 형식을 선택해야 합니다. 기본 구성 유형으로 SAP ID 서비스에 대한 편집할 수 없는 암시적 트러스트가 있습니다. 없음의 경우 신뢰 설정이 필요 없습니다.
    > 
    > 

11. **일반** 탭을 클릭한 다음 **찾아보기**를 클릭하여 다운로드한 메타데이터 파일을 업로드합니다.
    
    ![신뢰 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "신뢰 관리")
    
    >[!NOTE]
    >메타데이터 파일을 업로드하면 **Single Sign-On URL**, **단일 로그아웃 URL** 및 **서명 인증서**의 값이 자동으로 채워집니다.
    > 
     
12. **특성** 탭을 클릭합니다.

13. **특성** 탭에서 다음 단계를 수행합니다.
    
    ![특성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "특성") 

    a. **어설션 기반 특성 추가**를 클릭한 다음 어설션 기반 특성을 추가합니다.
       
    | 어설션 특성 | 보안 주체 특성 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >특성의 구성은 SAML 응답에서 예상되는 특성 및 코드의 이 특성에 액세스하는 이름(보안 주체 특성)과 같은 SCP의 응용 프로그램이 개발된 방법에 따라 다릅니다.
     > 
    
    나. 스크린샷의 **기본 특성** 은 설명 목적입니다. 시나리오 작업을 작성할 필요가 없습니다.  
 
    다. 스크린샷에 표시되는 **보안 주체 특성** 의 이름 및 값은 응용 프로그램 개발 방법에 따라 달라집니다. 응용 프로그램에 다른 매핑이 필요할 수 있습니다.

### <a name="assertion-based-groups"></a>어설션 기반 그룹

선택적 단계로, Azure Active Directory ID 공급자에 대한 어설션 기반 그룹을 구성할 수 있습니다.

SAP Cloud Platform에서 그룹을 사용하면 한 명 이상의 사용자를 SAP Cloud Platform 응용 프로그램의 하나 이상의 역할에 동적으로 할당할 수 있으며 SAML 2.0 어셜션의 특성 값으로 결정됩니다. 

예를 들어, 어설션이 특성 "*contract=temporary*"를 포함하는 경우 적용되는 모든 사용자가 그룹 "*임시*"에 추가됩니다. 그룹 "*임시*"는 SAP Cloud Platform 계정에 배포되는 하나 이상의 응용 프로그램에서 하나 이상의 역할을 포함할 수 있습니다.
 
SAP Cloud Platform 계정에서 응용 프로그램의 하나 이상의 역할에 여러 사용자를 동시에 할당하려는 경우 어셜션 기반 그룹을 사용합니다. 특정 역할에 단일 또는 적은 수의 사용자를 할당하려는 경우, SAP Cloud Platform cockpit의 “**권한 부여**” 탭에 직접 할당하는 것이 좋습니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>SAP Cloud Platform 테스트 사용자 만들기

Azure AD 사용자가 SAP Cloud Platform으로 로그를 사용하려면, SAP Cloud Platform에서 역할을 할당해야 합니다.

**사용자에게 역할을 할당하려면 다음 단계를 수행합니다.**

1. **SAP Cloud Platform** cockpit에 로그인합니다.

2. 다음을 수행합니다.
   
    ![권한 부여](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "권한 부여")
   
    a. **권한 부여**를 클릭합니다.

    나. **사용자** 탭을 클릭합니다.

    다. **사용자** 텍스트 상자에 사용자의 이메일 주소를 입력합니다.

    d. **할당** 을 클릭하여 사용자에 역할을 할당합니다.

    e. **저장**을 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Cloud Platform에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 SAP Cloud Platform에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP Cloud Platform**을 선택합니다.

    ![응용 프로그램 목록에서 SAP Cloud Platform 링크](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 SAP Cloud Platform 타일을 클릭하면 SAP Cloud Platform 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

