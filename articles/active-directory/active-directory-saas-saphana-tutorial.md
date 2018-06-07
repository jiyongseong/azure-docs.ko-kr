---
title: '자습서: SAP HANA와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP HANA 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 626dfe07c13c90686157dea710715b333a378136
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352891"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>자습서: SAP HANA와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP HANA를 통합하는 방법에 대해 알아봅니다.

SAP HANA를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- SAP HANA에 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP HANA에 자동으로 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP HANA와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SSO(Single Sign-On)를 사용하도록 설정된 SAP HANA 구독
- 공용 IaaS, 온-프레미스, Azure VM 또는 Azure의 SAP 큰 인스턴스에서 실행 중인 HANA 인스턴스
- XSA 관리 웹 인터페이스 및 HANA 인스턴스에 설치된 HANA Studio

> [!NOTE]
> 이 자습서의 단계를 테스트할 때 SAP HANA의 프로덕션 환경을 사용하지 않는 것이 좋습니다. 응용 프로그램 개발 또는 스테이징 환경에서 통합을 먼저 테스트한 다음 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 아직 없는 경우 [Azure AD의 1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 사용해보십시오.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP HANA 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-sap-hana-from-the-gallery"></a>갤러리에서 SAP HANA 추가
SAP HANA가 Azure AD에 통합되도록 구성하려면 갤러리의 SAP HANA를 관리되는 SaaS 앱 목록에 추가합니다.

**갤러리의 SAP HANA를 추가하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SAP HANA**를 입력합니다. 그런 다음 결과 창에서 **SAP HANA**를 선택합니다. 마지막으로 **추가** 단추를 선택하여 응용 프로그램을 추가합니다. 

    ![새 응용 프로그램](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP HANA에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP HANA 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP HANA의 관련 사용자 간에 연결이 형성되어야 합니다.

SAP HANA에서 Azure AD의 **사용자 이름**과 동일한 **Username** 값을 제공합니다. 이 단계는 두 사용자 간의 연결을 설정합니다.

SAP HANA에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [SAP HANA 테스트 사용자 만들기](#creating-a-sap-hana-test-user) - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 SAP HANA에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-on 테스트](#testing-single-sign-on) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP HANA 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAP HANA에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SAP HANA** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성][4]

2. **Single Sign-On** 대화 상자의 **SAML 기반 로그인** 아래에서 **모드**를 선택합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. **SAP HANA 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. **식별자** 상자에 다음을 입력합니다. `HA100` 

    나. **회신 URL** 상자에 다음 패턴으로 URL을 입력합니다. `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이 값을 얻으려면 [SAP HANA 클라이언트 지원 팀](https://cloudplatform.sap.com/contact.html)에 문의하세요. 

4. **SAML 서명 인증서** 섹션 아래에서 **메타데이터 XML**을 선택합니다. 그런 다음 메타데이터 파일을 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >인증서가 활성 상태가 아니면 Azure AD에서 **Make new certificate active**(새 인증서 활성화) 확인란을 선택하여 활성화합니다. 

5. SAP HANA 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷은 이 형식에 대한 예제를 보여 줍니다. 

    여기서는 **user.mail**의 **ExtractMailPrefix()** 함수를 이용하여 **사용자 ID**를 매핑했습니다. 이렇게 하면은 고유한 사용자 ID인 사용자의 전자 메일 접두사 값을 얻을 수 있습니다. 이 사용자 ID는 응답이 성공할 때마다 SAP HANA 응용 프로그램에 전송됩니다.

    ![Single Sign-On 구성](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 다음 단계를 수행합니다.

    a. **사용자 ID** 드롭다운 목록에서 **ExtractMailPrefix**를 선택합니다.
    
    나. **메일** 드롭다운 목록에서 **user.mail**을 선택합니다.

7. **저장** 단추를 선택합니다.

    ![Single Sign-On 저장 단추 구성](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. SAP HANA 쪽에 Single Sign-On을 구성하려면 해당 HTTPS 끝점으로 이동하여 **HANA XSA 웹 콘솔**에 로그인합니다.

    > [!NOTE]
    > 기본 구성에서는 URL이 요청을 로그온 화면으로 리디렉션하며, 인증된 SAP HANA 데이터베이스 사용자의 자격 증명이 필요합니다. 로그인하는 사용자는 SAML 관리 작업을 수행할 권한이 있어야 합니다.

9. XSA 웹 인터페이스에서 **SAML ID 공급자**로 이동합니다. 여기에서 화면 아래쪽의 **+** 단추를 선택하여 **ID 공급자 정보 추가** 창을 표시합니다. 다음 단계를 수행합니다.

    ![ID 공급자 추가](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. **ID 공급자 정보 추가** 창에서 메타데이터 XML(Azure Portal에서 다운로드한)의 콘텐츠를 **메타데이터** 상자에 붙여넣습니다.

    ![ID 공급자 설정 추가](./media/active-directory-saas-saphana-tutorial/sap2.png)

    나. XML 문서의 콘텐츠가 유효하면 구문 분석 프로세스를 통해 **일반 데이터** 화면 영역의 **제목, 엔터티 ID 및 발급자** 필드에 필요한 정보가 추출됩니다. **대상** 화면 영역의 URL 필드에 필요한 정보도 추출됩니다(예: **기준 URL 및 SingleSignOn URL (*)** 필드).

    ![ID 공급자 설정 추가](./media/active-directory-saas-saphana-tutorial/sap3.png)

    다. **일반 데이터** 화면 영역의 **이름** 상자에 새 SAML SSO ID 공급자의 이름을 입력합니다.

    > [!NOTE]
    > SAML IDP의 이름은 필수이며 고유해야 합니다. SAP HANA XS 응용 프로그램에서 사용할 인증 방법으로 SAML을 선택할 때 표시되는 SAML IDP 목록에 나타납니다. 예를 들어 XS 아티팩트 관리 도구의 **인증** 화면 영역에서 이 작업을 수행할 수 있습니다.

10. **저장**을 선택하여 SAML ID 공급자의 세부 정보를 저장하고 알려진 SAML IDP 목록에 새 SAML IDP를 추가합니다.

    ![저장 단추](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. HANA Studio **Configuration**(구성) 탭의 시스템 속성 내에서 **saml**을 기준으로 설정을 필터링합니다. 그런 다음 **assertion_timeout**을 **10초**에서 **120초**로 조정합니다.

    ![assertion_timeout 설정](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 간결한 지침을 읽어 보세요. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에 **Single Sign-On** 탭을 선택하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서](https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동합니다. 그런 다음 **모든 사용자**를 선택합니다.
    
    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 선택합니다.
 
    ![추가 단추](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 BrittaSimon의 **이메일 주소**를 입력합니다.

    다. **암호 표시**를 선택한 다음 암호를 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-sap-hana-test-user"></a>SAP HANA 테스트 사용자 만들기

Azure AD 사용자가 SAP HANA에 로그인할 수 있도록 SAP HANA에 프로비전해야 합니다.
SAP HANA는 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다.

사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.

>[!NOTE]
>사용자가 사용하는 외부 인증을 변경할 수 있습니다. Kerberos와 같은 외부 시스템으로 인증할 수 있습니다. 외부 ID에 대한 자세한 내용은 [도메인 관리자](https://cloudplatform.sap.com/contact.html)에게 문의하세요.

1. 관리자 권한으로 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)를 연 다음 SAML SSO에 DB 사용자를 사용하도록 설정합니다.

    ![사용자 만들기](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. **SAML** 왼쪽의 보이지 않는 확인란을 선택한 다음 **구성** 링크를 선택합니다.

3. **추가**를 선택하여 SAML IDP를 추가합니다.  적절한 SAML IDP를 선택한 다음 **확인**을 선택합니다.

4. **외부 ID**(이 경우, BrittaSimon)를 추가하거나 **모두**를 선택합니다. 그런 다음 **확인**을 선택합니다.

    >[!Note]
    >**모두** 확인란을 선택하지 않으면 HANA의 사용자 이름이 도메인 접미사 앞에 있는 UPN의 사용자 이름과 정확히 일치해야 합니다. (예: BrittaSimon@contoso.com은 HANA에서 BrittaSimon이 됩니다.)

5. 테스트를 위해 모든 **XS** 역할을 사용자에게 할당합니다.

    ![역할 할당](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > 사용 사례에 적합한 권한만 부여해야 합니다.

6. 사용자를 저장합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP HANA에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 SAP HANA에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 엽니다. 디렉터리 보기로 이동하여 **엔터프라이즈 응용 프로그램**으로 이동합니다. **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP HANA**를 선택합니다.

    ![사용자 할당](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][202] 

4. **추가** 단추를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP HANA 타일을 선택하면 SAP HANA 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

