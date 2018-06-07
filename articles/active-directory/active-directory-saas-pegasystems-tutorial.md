---
title: '자습서: Pega Systems와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Pega Systems 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 539de49f24b2ca0c9b70be5a339625c1e14edc44
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>자습서: Pega Systems와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pega Systems를 통합하는 방법을 알아봅니다.

Pega Systems를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Pega Systems에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Pega Systems에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Pega Systems와 Azure AD를 통합하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Pega Systems Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Pega Systems 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-pega-systems-from-the-gallery"></a>갤러리에서 Pega Systems 추가
Pega Systems와 Azure AD의 통합을 구성하려면 갤러리에서 Pega Systems를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Pega Systems를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Pega Systems**를 입력하고 결과 패널에서 **Peoplecart**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Pega Systems에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 올바르게 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Pega Systems 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Pega Systems의 해당 사용자 간에 연결 관계가 형성되어야 합니다.

Pega Systems에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Pega Systems에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 사항을 완료합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Pega Systems 테스트 사용자 만들기](#create-a-pega-systems-test-user)** - Azure AD의 Britta Simon 사용자에 연결된 Pega Systems 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Pega Systems 응용 프로그램에서 Single Sign-On을 구성합니다.

**Pega Systems에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Pega Systems** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. **Pega Systems 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Pega Systems 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. **식별자** 텍스트 상자에서 `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Pega Systems 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    **릴레이 상태** 텍스트 상자에서 `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso` 패턴을 사용하여 URL을 입력합니다.
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 릴레이 상태 URL로 이러한 값을 업데이트합니다. 식별자 값과 회신 URL 값은 이 자습서의 뒷부분에서 설명하는 Pega 응용 프로그램에서 확인할 수 있습니다. 릴레이 상태 값을 확인하려면 [Pega Systems 클라이언트 지원 팀](https://www.pega.com/contact-us)에 문의하세요. 

5. Pega Systems 응용 프로그램에는 특정 형식을 갖는 SAML 어설션을 사용해야 합니다. 이를 위해서는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 이러한 클레임은 고객의 요구 사항에 따라 달라집니다. 아래의 선택적 클레임은 하나의 예이며, 이를 사용자 응용 프로그램에 구성해 볼 수 있습니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이전의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Phone | *********** |

    > [!NOTE]
    > 위 값은 고객에 따라 달라집니다. 해당하는 값을 입력하세요.

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.

7. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. **Pega Systems** 쪽에서 Single Sign-On을 구성하려면 다른 브라우저 창에서 관리자 계정을 이용하여 **Pega Portal**을 엽니다.

10. **새로 만들기** -> **SysAdmin** -> **인증 서비스**를 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. **인증 서비스 만들기** 화면에서 다음을 수행합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. 형식에서 **SAML 2.0**을 선택합니다.

    나. **이름** 텍스트 상자에 이름(예: Azure AD SSO)을 입력합니다.

    다. **간단한 설명** 텍스트 상자에 설명을 입력합니다.  

    d. **만들기 및 열기**를 클릭합니다. 
    
12. **ID 공급자(IdP) 정보** 섹션에서 **IdP 메타데이터 가져오기**를 클릭하고 Azure Portal에서 다운로드한 메타데이터 파일을 찾아 선택합니다. **제출**을 클릭하여 메타데이터를 로드합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. 이렇게 하면 아래 그림과 같이 IdP 데이터가 자동으로 입력됩니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. **서비스 공급자(SP) 설정** 섹션에서 다음을 수행합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. **엔터티 ID** 값을 복사하여 Azure Portal의 **식별자** 텍스트 상자에 붙여넣습니다.

    나.  **어설션 소비자 서비스(ACS) 위치** 값을 복사하여 Azure Portal의 **회신 URL** 텍스트 상자에 붙여넣습니다.

    다. **요청 서명 사용 안 함**을 선택합니다.

15. 페이지 맨 아래에 있는 **저장**
    
> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-pega-systems-test-user"></a>Pega Systems 테스트 사용자 만들기

이 섹션에서는 Pega Systems에 Britta Simon이라는 사용자를 만듭니다. Pega Systems에서 사용자를 만들려면 [Pega Systems 클라이언트 지원 팀](https://www.pega.com/contact-us)에 문의하세요.


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Pega Systems에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Pega Systems에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Pega Systems**를 선택합니다.

    ![응용 프로그램 목록의 Pega Systems 링크](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Pega Systems 타일을 클릭하면 Pega Systems 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

