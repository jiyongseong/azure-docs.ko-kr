---
title: '자습서: SAP Business Object Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Business Object Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 5a56a892ac3b28c4e90ec2ea6360da3d2eff2581
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>자습서: SAP Business Object Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Business Object Cloud를 통합하는 방법에 대해 알아봅니다.

SAP Business Object Cloud를 Azure AD와 통합하는 경우 다음과 같은 이점이 있습니다.

- Azure AD에서 SAP Business Object Cloud에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Single Sign-On 및 사용자의 Azure AD 계정을 사용하여 SAP Business Object Cloud에 자동으로 로그인할 수 있습니다.
- 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란?](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP Business Object Cloud와 Azure AD 통합을 설정하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Single Sign-On이 설정된 SAP Business Object Cloud

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경에서 테스트하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하는 권장 사항:

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리의 SAP Business Object Cloud 추가
2. Azure AD Single Sign-On 설정 및 테스트

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>갤러리에서 SAP Business Object Cloud 추가
Azure AD와 SAP Business Object Cloud 통합을 설정하려면 갤러리의 SAP Business Object Cloud를 관리되는 SaaS 앱 목록에 추가합니다.

갤러리의 SAP Business Object Cloud를 추가하려면:

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 페이지][2]
    
3. 새 응용 프로그램을 추가하려면 **새 응용 프로그램**을 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SAP Business Object Cloud**를 입력합니다.

    ![검색 상자](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. 결과 창에서 **SAP Business Object Cloud**를 선택하고 **추가** 단추를 선택합니다.

    ![결과 목록의 SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 설정 및 테스트

이 섹션에서는 *Britta Simon*이라는 테스트 사용자를 기반으로 SAP Business Object Cloud에서 Azure AD Single Sign-On을 설정하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서는 Azure AD 사용자에 해당하는 SAP Business Object Cloud 사용자가 누구인지 알고 있어야 합니다. Azure AD 사용자와 SAP Business Object Cloud의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

연결 관계를 설정하려면 SAP Business Object Cloud에서 Azure AD의 **사용자 이름** 값을 **Username** 값에 할당합니다.

SAP Business Object Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 작업을 완료합니다.

1. [Azure AD Single Sign-On 설정](#set-up-azure-ad-single-sign-on) 사용자가 이 기능을 사용하도록 설정합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user). Britta Simon 사용자로 Azure AD Single Sign-On을 테스트합니다.
3. [SAP Business Object Cloud 테스트 사용자 만들기](#create-an-sap-business-object-cloud-test-user) Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Business Object Cloud에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) Britta Simon이 Azure AD Single Sign-On을 사용하도록 설정합니다.
5. [Single Sign-On 테스트](#test-single-sign-on). 구성이 작동하는지 확인합니다.

### <a name="set-up-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 설정

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 켭니다. 그런 다음 SAP Business Object Cloud 응용 프로그램에서 Single Sign-On을 설정합니다.

SAP Business Object Cloud에서 Azure AD Single Sign-On을 설정하려면:

1. Azure Portal의 **SAP Business Object Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택][4]

2. **Single Sign-On** 페이지에서 **모드**로 **SAML 기반 로그온**을 선택합니다.
 
    ![SAML 기반 로그온 선택](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. **SAP Business Object Cloud 도메인 및 URL** 아래에서 다음 단계를 완료합니다.

    1. **로그온 URL** 상자에 다음 패턴이 있는 URL을 입력합니다. 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. **식별자** 상자에 다음 패턴이 있는 URL을 입력합니다.
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business Object Cloud 도메인 및 URL 페이지 URL](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > 이러한 URL의 값은 데모용으로만 사용합니다. 실제 로그온 URL 및 식별자 URL로 값을 업데이트합니다. 로그온 URL을 가져오려면 [SAP Business Object Cloud 클라이언트 지원팀](https://www.sap.com/product/analytics/cloud-analytics.support.html)에 연락하세요. 관리 콘솔에서 SAP Business Object Cloud 메타데이터를 다운로드하여 식별자 URL을 가져올 수 있습니다. 이것은 자습서의 뒷부분에 설명되어 있습니다. 

4. **SAML 서명 인증서** 아래에서 **메타데이터 XML**을 선택합니다. 그런 다음 메타데이터 파일을 컴퓨터에 저장합니다.

    ![메타데이터 XML 선택](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. **저장**을 선택합니다.

    ![저장 선택](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. 다른 웹 브라우저 창에서 SAP Business Object Cloud 회사 사이트에 관리자로 로그인합니다.

7. **메뉴** > **시스템** > **관리**를 선택합니다.
    
    ![메뉴, 시스템 및 관리 선택](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. **보안** 탭에서 **편집**(펜) 아이콘을 선택합니다.
    
    ![보안 탭에서 편집 아이콘 선택](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. **인증 방법**으로 **SAML SSO(Single Sign-On)** 를 선택합니다.

    ![인증 방법으로 SAML Single Sign-On 선택](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. 서비스 공급자 메타데이터를 다운로드하려면(1단계) **다운로드**를 선택합니다. 메타데이터 파일에서 **entityID** 값을 찾고 복사합니다. Azure Portal의 **SAP Business Object Cloud 도메인 및 URL** 아래에 있는 **식별자** 상자에 값을 붙여넣습니다.

    ![entityID 값 복사 및 붙여넣기](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Azure Portal에서 다운로드한 파일에서 서비스 공급자 메타데이터를 업로드하려면(2단계) **ID 공급자 메타데이터 업로드** 아래에서 **업로드**를 선택합니다.  

    ![ID 공급자 메타데이터 업로드 아래에서 업로드 선택](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. **사용자 특성** 목록에서 구현에 사용할 사용자 특성을 선택합니다(3단계). 이 사용자 특성은 ID 공급자에 매핑됩니다. 사용자의 페이지에서 사용자 지정 특성 입력하려면 **사용자 지정 SAML 매핑** 옵션을 사용합니다. 또는 **전자 메일**이나 **사용자 ID**를 사용자 특성으로 선택할 수 있습니다. 예제에서는 Azure Portal의 **사용자 특성** 섹션에서 **userprincipalname** 특성으로 사용자 ID 클레임을 매핑하기 때문에 **전자 메일**을 선택했습니다. 여기서는 고유한 사용자 전자 메일을 제공합니다. 모든 성공적인 SAML 응답에서 SAP Business Object Cloud 응용 프로그램에 전송됩니다.

    ![사용자 특성 선택](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. ID 공급자(4단계)를 사용하여 계정을 확인하려면 **로그인 자격 증명(전자 메일)** 상자에 사용자의 전자 메일 주소를 입력합니다. 그런 다음 **계정 확인**을 선택합니다. 시스템은 사용자 계정에 로그인 자격 증명을 추가합니다.

    ![전자 메일을 입력하고 계정 확인을 선택합니다.](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. **저장** 아이콘을 선택합니다.

    ![저장 아이콘](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 이러한 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램**을 선택하여 앱을 추가한 후에 **Single Sign-On** 탭을 선택합니다. 페이지의 맨 아래에 있는 **구성** 섹션에서 포함된 설명서에 액세스할 수 있습니다. 자세한 내용은 [Azure AD 포함 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

Azure AD에서 테스트 사용자를 만들려면:

1. Azure Portal의 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**을 선택한 후 **모든 사용자**를 선택합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 **추가**를 선택합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자에서 다음 단계를 완료합니다.
 
    1. **이름** 상자에 **BrittaSimon**을 입력합니다.

    2. **사용자 이름** 상자에 사용자인 Britta Simon의 이메일 주소를 입력합니다.

    3. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    4. **만들기**를 선택합니다.

        ![사용자 대화 상자](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Azure AD 사용자 만들기][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>SAP Business Object Cloud 테스트 사용자 만들기

Azure AD 사용자는 SAP Business Object Cloud에 로그인하기 전에 SAP Business Object Cloud에 프로비전되어야 합니다. SAP Business Object Cloud에서 프로비전은 수동 작업입니다.

사용자 계정을 프로비전하려면:

1. SAP Business Object Cloud 회사 사이트에 관리자 권한으로 로그인합니다.

2. **메뉴** > **보안** > **사용자**를 선택합니다.

    ![직원 추가](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. **사용자** 페이지에서 새 사용자 세부 정보를 추가하려면 **+** 를 선택합니다. 

    ![사용자 추가 페이지](./media/active-directory-saas-sapboc-tutorial/user4.png)

    그리고 나서 다음 단계를 완료합니다.

    1. **사용자 ID** 상자에 사용자 ID(예: **Britta**)를 입력합니다.

    2. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    3. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    4. **표시 이름** 텍스트 상자에 사용자의 전체 이름(예: **Britta Simon**)을 입력합니다.

    5. **전자 메일** 상자에 사용자의 이메일 주소(예: **brittasimon@contoso.com**)를 입력합니다.

    6. **역할 선택** 페이지에서 사용자에 대한 적절한 역할을 선택하고 **확인**을 선택합니다.

      ![역할 선택](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. **저장** 아이콘을 선택합니다.    


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 SAP Business Object Cloud에 대한 사용자 계정 액세스 권한을 부여하여 사용자인 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 합니다.

Britta Simon을 SAP Business Object Cloud에 할당하려면:

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP Business Object Cloud**을 선택합니다.

    ![Configure Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택][202] 

4. **추가**를 선택합니다. 그런 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 페이지][203]

5. **사용자 및 그룹** 페이지의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 페이지에서 **선택**을 선택합니다.

7. **할당 추가** 페이지에서 **할당**을 선택합니다.

![사용자 역할 할당][200] 
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Business Object Cloud 타일을 선택하면 SAP Business Object Cloud 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

