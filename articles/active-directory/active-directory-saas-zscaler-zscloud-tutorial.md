---
title: '자습서: Azure Active Directory와 Zscaler ZSCloud 통합 | Microsoft Docs'
description: Azure Active Directory와 Zscaler ZSCloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: b44e6c7c47eec15dab2294ca7ebcb7b7dcff3a4c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>자습서: Azure Active Directory와 Zscaler ZSCloud 통합

이 자습서에서는 Zscaler ZSCloud를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

Zscaler ZSCloud를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Zscaler ZSCloud에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Zscaler ZSCloud에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Zscaler ZSCloud와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Zscaler ZSCloud Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Zscaler ZSCloud 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>갤러리에서 Zscaler ZSCloud 추가
Zscaler ZSCloud와 Azure AD의 통합을 구성하려면 갤러리의 Zscaler ZSCloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zscaler ZSCloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Zscaler ZSCloud**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. 결과 창에서 **Zscaler ZSCloud**를 선택하고 **추가** 단추를 클릭하여 해당 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Zscaler ZSCloud 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Zscaler ZSCloud의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Zscaler ZSCloud의 **Username** 값으로 할당하여 설정합니다.

Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[프록시 설정 구성](#configuring-proxy-settings)** - Internet Explorer에서 프록시 설정을 구성하려면
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Zscaler ZSCloud 테스트 사용자 만들기](#creating-a-zscaler-zscloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Zscaler ZSCloud에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Zscaler ZSCloud 응용 프로그램에서 Single Sign-On을 구성합니다.

**Zscaler ZSCloud에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Zscaler ZSCloud** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. **Zscaler ZSCloud 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     **로그온 URL** 텍스트 상자에 사용자가 ZScaler ZSCloud 응용 프로그램에 로그인하는 데 사용하는 URL을 입력합니다.
    
    > [!NOTE] 
    > 이 값은 실제 로그인 URL로 업데이트해야 합니다. 이 값을 얻으려면 [Zscaler ZSCloud 클라이언트 지원 팀](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint)에 문의하세요. 
 
4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_400.png)

6. **Zscaler ZSCloud 구성** 섹션에서 **Zscaler ZSCloud 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. 다른 웹 브라우저 창에서 ZScaler ZSCloud 회사 사이트에 관리자로 로그인합니다.

8. 위쪽의 메뉴에서 **관리**를 클릭합니다.
   
    ![관리](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800206.png "관리")

9. **관리자 & 역할 관리**에서 **사용자 및 인증 관리**를 클릭합니다.   
            
    ![사용자 및 인증 관리](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800207.png "사용자 및 인증 관리")

10. **구성에 대한 인증 옵션 선택** 섹션에서, 다음 단계를 수행합니다.   
                
    ![인증](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800208.png "인증")
   
    a. **SAML Single Sign-On을 사용하여 인증**을 선택합니다.

    나. **SAML Single Sign-On 매개 변수 구성**을 클릭합니다.

11. **SAML Single Sign-On 매개 변수 구성** 대화 상자 페이지에서 다음 단계를 수행하고 **완료**를 클릭합니다.

    ![Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800209.png "Single Sign-On")
    
    a. **SAML Single Sign-On 서비스 URL** 값을 **인증할 사용자가 전송되는 SAML 포털의 URL** 텍스트 상자에 붙여넣습니다.
    
    나. **로그인 이름을 포함한 특성** 텍스트 상자에 **NameID**를 입력합니다.
    
    다. **Zscaler pem**을 클릭하여 다운로드한 인증서를 업로드합니다.
    
    d. **SAML 자동 프로비전 사용**을 선택합니다.

12. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![관리](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800210.png "관리")
    
    a. **저장**을 클릭합니다.

    나. **지금 활성화**를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer**를 시작합니다.

2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.   
    
     ![인터넷 옵션](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769492.png "인터넷 옵션")

3. **연결** 탭을 클릭합니다.   
  
     ![연결](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769493.png "연결")

4. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

5. 프록시 서버 섹션에서 다음 단계를 수행합니다.   
   
    ![프록시 서버](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용**을 선택합니다.

    나. 주소 텍스트 상자에 **gateway.zscalerone.net**을 입력합니다.

    다. 포트 텍스트 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.

    e. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

6. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Zscaler ZSCloud 테스트 사용자 만들기

Azure AD 사용자가 ZScaler ZSCloud에 로그인할 수 있도록 하려면 사용자 계정이 ZScaler ZSCloud로 프로비전되어야 합니다.  
ZScaler ZSCloud의 경우, 수동으로 프로비전합니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면

1. **Zscaler** 테넌트에 로그인 합니다.

2. **관리**를 클릭합니다.   
   
    ![관리](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781035.png "관리")

3. **사용자 관리**를 클릭합니다.   
        
     ![추가](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "추가")

4. **사용자** 탭에서 **추가**를 클릭합니다.
      
    ![추가](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "추가")

5. 사용자 추가 섹션에서 다음 단계를 수행합니다.
        
    ![사용자 추가](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781038.png "사용자 추가")
   
    a. **사용자ID**, **사용자 표시 이름**, **암호**, **암호 확인**을 입력하고, 프로비전하고자 하는 유효한 AAD 계정의 **그룹** 및 **부서**를 선택합니다.

    나. **저장**을 클릭합니다.

> [!NOTE]
> 다른 ZScaler ZScloud 사용자 계정 생성 도구 또는 Zendesk에서 제공하는 APIs를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler ZSCloud에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Zscaler ZSCloud에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Zscaler ZSCloud**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다.

액세스 패널에서 Zscaler ZSCloud 타일을 클릭하면 Zscaler ZSCloud 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_203.png

