---
title: '자습서: Symantec WSS(Web Security Service)와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Symantec WSS(Web Security Service) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 0dc75a1091c56ddbbffcd54047b2465ece231254
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>자습서: Symantec WSS(Web Security Service)와 Azure Active Directory 통합

이 자습서에서는 WSS가 SAML 인증을 사용하여 Azure AD에서 프로비전된 최종 사용자를 인증하고 사용자 또는 그룹 수준 정책 규칙을 적용할 수 있도록 Symantec WSS(Web Security Service) 계정을 Azure AD(Azure Active Directory) 계정과 통합하는 방법에 대해 알아봅니다.

Symantec WSS(Web Security Service)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- WSS 계정에서 사용하는 모든 최종 사용자와 그룹을 Azure AD Portal에서 관리합니다. 

- 최종 사용자가 자신의 Azure AD 자격 증명을 사용하여 WSS에서 자신을 인증할 수 있습니다.

- WSS 계정에 정의된 사용자 및 그룹 수준 정책 규칙을 적용합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Symantec WSS(Web Security Service)와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Symantec WSS(Web Security Service) 계정

> [!NOTE]
> 이 자습서의 단계를 테스트하려면 현재 프로덕션 용도로 사용 중인 WSS 계정을 사용하지 않는 것이 좋습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니라면 현재 프로덕션 용도로 사용 중인 WSS 계정을 이 테스트에 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 Azure AD 계정에 정의된 최종 사용자 자격 증명을 사용하여 WSS에 Single Sign-On을 사용하도록 Azure AD를 구성하겠습니다.
이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Symantec WSS(Web Security Service) 앱 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>갤러리에서 Symantec WSS(Web Security Service) 추가
Symantec WSS(Web Security Service)가 Azure AD에 통합되도록 구성하려면 갤러리에서 Symantec WSS(Web Security Service)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Symantec WSS(Web Security Service)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Symantec WSS(Web Security Service)** 를 입력하고, 결과 패널에서 **Symantec WSS(Web Security Service)** 를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Symantec WSS(Web Security Service)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Symantec WSS(Web Security Service)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 Symantec WSS(Web Security Service) 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Symantec WSS(Web Security Service)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

Symantec WSS(Web Security Service)에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Symantec WSS(Web Security Service)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Symantec WSS(Web Security Service) 테스트 사용자 만들기](#create-a-symantec-web-security-service-wss-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Symantec WSS(Web Security Service)에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Symantec WSS(Web Security Service) 응용 프로그램에서 Single Sign-On을 구성합니다.

**Symantec WSS(Web Security Service)에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Symantec WSS(Web Security Service)** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. **Symantec WSS(Web Security Service) 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Symantec WSS(Web Security Service) 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. **식별자** 텍스트 상자에 URL `https://saml.threatpulse.net:8443/saml/saml_realm`을 입력합니다.

    나. **회신 URL** 텍스트 상자에 URL `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`를 입력합니다.

    > [!NOTE]
    > **식별자** 및 **회신 URL** 값이 어떤 이유로 작동하지 않는 경우 [Symantec WSS(Web Security Service) 클라이언트 지원 팀](https://www.symantec.com/contact-us)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Symantec WSS(Web Security Service) 쪽에 Single Sign-On을 구성하려면 WSS 온라인 설명서를 참조하세요. 다운로드한 **메타데이터 XML** 파일을 WSS 포털로 가져와야 합니다. WSS 포털 구성과 관련하여 도움이 필요하면 [Symantec WSS(Web Security Service) 지원 팀](https://www.symantec.com/contact-us)에 문의하세요.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Symantec WSS(Web Security Service) 테스트 사용자 만들기

이 섹션에서는 Symantec WSS(Web Security Service)에서 Britta Simon이라는 사용자를 만듭니다. WSS 포털에서 해당하는 최종 사용자 이름을 수동으로 만들 수도 있고, Azure AD에 프로비전된 사용자/그룹이 몇 분 후(15분 이내) WSS 포털에 동기화될 때까지 기다릴 수도 있습니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 웹 사이트 찾아보기에 사용되는 최종 사용자 컴퓨터의 공용 IP 주소 또한 Symantec WSS(Web Security Service) 포털에서 프로비전해야 합니다.

> [!NOTE]
> 컴퓨터의 공용 IP 주소를 보려면 [여기를 클릭](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Symantec WSS(Web Security Service)에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Symantec WSS(Web Security Service)에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Symantec WSS(Web Security Service)** 를 선택합니다.

    ![응용 프로그램 목록의 Symantec WSS(Web Security Service) 연결](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

SAML 인증에 Azure AD를 사용하도록 WSS 계정을 구성했으니, 이번 섹션에서는 Single Sign-On 기능을 테스트하겠습니다.

WSS로 트래픽을 프록시하도록 웹 브라우저를 구성한 후 웹 브라우저를 열고 사이트로 이동하려고 시도하면 Azure 로그인 페이지로 리디렉션됩니다. Azure AD에서 프로비전된 테스트 최종 사용자(즉, BrittaSimon)의 자격 증명 및 연결된 암호를 입력합니다. 인증되면 선택한 웹 사이트로 이동할 수 있습니다. WSS 쪽에 BrittaSimon이 특정 사이트로 이동할 수 없게 차단하는 정책 규칙을 만드는 경우 사용자 BrittaSimon으로 해당 사이트로 이동하려고 시도하면 WSS 차단 페이지가 나타납니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

