---
title: '자습서: FreshDesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 FreshDesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 76f91f865b2f2135e319d164a0823c57cda837c9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>자습서: FreshDesk와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FreshDesk를 통합하는 방법에 대해 알아봅니다.

FreshDesk를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- FreshDesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 FreshDesk에 자동으로 SSO(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

FreshDesk와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- FreshDesk Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 FreshDesk 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-freshdesk-from-the-gallery"></a>갤러리에서 FreshDesk 추가
FreshDesk의 Azure AD 통합을 구성하려면 갤러리의 FreshDesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 FreshDesk를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Freshdesk**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. 결과 창에서 **FreshDesk**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 FreshDesk 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 FreshDesk의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 FreshDesk의 **Username** 값으로 할당하여 설정합니다.

FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[FreshDesk 테스트 사용자 만들기](#creating-a-freshdesk-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 FreshDesk에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 FreshDesk 응용 프로그램에서 Single Sign-On을 구성합니다.

**FreshDesk에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **FreshDesk** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. **FreshDesk 도메인 및 URL** 섹션에서 **로그온 URL**을 `https://<tenant-name>.freshdesk.com` 또는 Freshdesk에서 제안한 다른 값으로 입력하세요.

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값은 실제 로그온 URL로 업데이트해야 합니다. 이 값을 얻으려면 [FreshDesk Client 지원 팀](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)에 문의하세요.  

4. **SAML 서명 인증서** 섹션에서 **인증서**를 클릭한 후 컴퓨터에 인증서를 저장합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. **FreshDesk 구성** 섹션에서 **FreshDesk 구성**을 클릭하여 로그온 구성 창을 엽니다. **빠른 참조** 섹션에서 SAML Single Sign-On 서비스 URL 및 로그아웃 URL을 복사합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. 다른 웹 브라우저 창에서 Freshdesk 회사 사이트에 관리자로 로그인합니다.

8. 위쪽의 메뉴에서 **관리자**를 클릭합니다.
   
   ![관리자](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "관리자")

9. **일반 설정** 탭에서 **보안**을 클릭합니다.
   
   ![보안](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "보안")

10. **보안** 섹션에서 다음 단계를 수행합니다.
   
    ![Single Sign On](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
    a. **SSO(Single Sign On)** 의 경우 **On**을 선택합니다.

    나. **SAML SSO**를 선택합니다.

    다. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**을 **SAML 로그인 URL** 텍스트 상자에 입력합니다.

    d. Azure Portal에서 복사한 **로그아웃 URL**을 **로그아웃 URL** 텍스트 상자에 입력합니다.

    e. Azure Portal에서 다운로드한 인증서의 **지문** 값을 복사해서 **보안 인증서 지문** 텍스트 상자에 붙여 넣습니다.  
 
    >[!TIP]
    >자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하세요. 
    
    f. **저장**을 클릭합니다.


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-freshdesk-test-user"></a>FreshDesk 테스트 사용자 만들기

Azure AD 사용자가 FreshDesk에 로그인할 수 있도록 하려면 FreshDesk로 프로비전되어야 합니다.  
FreshDesk의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Freshdesk** 테넌트에 로그인합니다.
2. 위쪽의 메뉴에서 **관리자**를 클릭합니다.
   
   ![관리자](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "관리자")

3. **일반 설정** 탭에서 **에이전트**를 클릭합니다.
   
   ![에이전트](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "에이전트")

4. **새 에이전트**를 클릭합니다.
   
    ![새 에이전트](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "새 에이전트")

5. 에이전트 정보 대화 상자에서 다음 단계를 수행합니다.
   
   ![에이전트 정보](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "에이전트 정보")
   
   a. **전체 이름** 텍스트 상자에 프로비전하려는 Azure AD 계정의 이름을 입력합니다.

   나. **전자 메일** 텍스트 상자에 프로비전하려는 Azure AD 계정의 Azure AD 전자 메일 주소를 입력합니다.

   다. **제목** 텍스트 상자에 프로비전하려는 Azure AD 계정의 제목을 입력합니다.

   d. **에이전트 역할**을 선택한 다음 **할당**을 클릭합니다.
       
   e. **저장**을 클릭합니다.     
   
    >[!NOTE]
    >Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다. 
    > 
    
    >[!NOTE]
    >다른 Freshdesk 사용자 계정 생성 도구 또는 Freshdesk가 제공한 API를 사용하여 AAD 사용자 계정을  Freshdesk에 프로비전할 수 있습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Box에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 FreshDesk에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **FreshDesk**를 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 [FreshDesk] 타일을 클릭하면 로그인 페이지가 나타나서 FreshDesk 응용 프로그램에 로그온할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

