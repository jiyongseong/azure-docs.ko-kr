---
title: '자습서: TargetProcess와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 TargetProcess 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 0f2dba144f0b7ff70e7c9996e82d0f841a76854b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>자습서: TargetProcess와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TargetProcess를 통합하는 방법에 대해 알아봅니다.

TargetProcess를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- TargetProcess에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 TargetProcess에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

TargetProcess와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- TargetProcess Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 TargetProcess 추가
2. Azure AD Single Sign-On 구성 및 테스트

## <a name="add-targetprocess-from-the-gallery"></a>갤러리에서 TargetProcess 추가
TargetProcess의 Azure AD 통합을 구성하려면 갤러리의 TargetProcess를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TargetProcess를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **TargetProcess**를 입력하고 결과 창에서 **TargetProcess**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![갤러리에서 TargetProcess 추가](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 TargetProcess 에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 TargetProcess 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 TargetProcess의 관련 사용자 간에 연결이 형성되어야 합니다.

TargetProcess에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

TargetProcess에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[TargetProcess 테스트 사용자 만들기](#create-a-targetprocess-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 TargetProcess에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 TargetProcess 응용 프로그램에서 Single Sign-On을 구성합니다.

**TargetProcess에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **TargetProcess** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![SAML 기반 로그온](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_samlbase.png)

3. **TargetProcess 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![TargetProcess 도메인 및 URL 섹션](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<subdomain>.tpondemand.com/`

    나. **식별자** 텍스트 상자에서 `https://<subdomain>.tpondemand.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [TargetProcess 클라이언트 지원 팀](mailto:support@targetprocess.com)에 문의하세요. 
 
4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![SAML 서명 인증서 섹션](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![저장 단추](./media/active-directory-saas-target-process-tutorial/tutorial_general_400.png)

6. **TargetProcess 구성** 섹션에서 **TargetProcess 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![TargetProcess 구성 섹션](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_configure.png) 

7. TargetProcess 응용 프로그램에 관리자 권한으로 로그온합니다.

8. 위쪽의 메뉴에서 **설정**을 클릭합니다.
   
    ![설정](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

9. **설정**을 클릭합니다.
   
    ![설정](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 

10. **Single Sign-On**을 클릭합니다.
   
    ![Single Sign-On을 클릭합니다.](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 

11. Single Sign-On 설정 대화 상자에서 다음 단계를 수행합니다.
   
    ![Configure Single Sign-On](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)
    
    a. **Single Sign-On 사용**을 클릭합니다.
    
    나. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **로그온 URL** 텍스트 상자에 붙여 넣습니다.

    다. 다운로드된 인증서를 메모장에서 열고, 내용을 복사한 다음 전체 인증서를 **인증서** 텍스트 상자에 붙여 넣습니다.
    
    d. **JIT 프로비전 사용**을 클릭합니다.

    e. **저장**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-target-process-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![사용자 목록을 표시하려면](./media/active-directory-saas-target-process-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![추가 단추](./media/active-directory-saas-target-process-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![사용자 섹션](./media/active-directory-saas-target-process-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-targetprocess-test-user"></a>TargetProcess 테스트 사용자 만들기

이 섹션은 TargetProcess에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

TargetProcess는 적시에 프로비전을 지원합니다. 이미 [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)에서 사용하도록 설정했습니다.

이 섹션에 작업 항목이 없습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 TargetProcess에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 TargetProcess에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **TargetProcess**를 선택합니다.

    ![앱 목록의 TargetProcess](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 TargetProcess 타일을 클릭하면 TargetProcess 응용 프로그램에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png

