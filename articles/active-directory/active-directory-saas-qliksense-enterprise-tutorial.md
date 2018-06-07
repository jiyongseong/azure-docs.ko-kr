---
title: '자습서: Qlik Sense Enterprise와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 Qlik Sense Enterprise 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 8cca4fb3ee10d56481cefccb7ea869b6bf13109c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>자습서: Qlik Sense Enterprise와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Qlik Sense Enterprise를 통합하는 방법에 대해 알아봅니다.

Qlik Sense Enterprise를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 사용자의 Qlik Sense Enterprise에 대한 액세스 권한을 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Qlik Sense Enterprise에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Qlik Sense Enterprise와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Qlik Sense Enterprise Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Qlik Sense Enterprise 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>갤러리에서 Qlik Sense Enterprise 추가
Qlik Sense Enterprise의 Azure AD 통합을 구성하려면 갤러리의 Qlik Sense Enterprise를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Qlik Sense Enterprise를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Qlik Sense Enterprise**를 입력하고 결과 패널에서 **Qlik Sense Enterprise**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Qlik Sense Enterprise에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Qlik Sense Enterprise 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Qlik Sense Enterprise의 관련 사용자 간에 연결이 형성되어야 합니다.

Qlik Sense Enterprise에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Qlik Sense Enterprise에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Qlik Sense Enterprise 테스트 사용자 만들기](#create-a-qlik-sense-enterprise-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Qlik Sense Enterprise에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Qlik Sense Enterprise 응용 프로그램에서 Single Sign-On을 구성합니다.

**Qlik Sense Enterprise에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Qlik Sense Enterprise** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. **Qlik Sense Enterprise 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Qlik Sense Enterprise 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > 이 URI 끝의 후행 슬래시를 유의하세요. 필수입니다.
    
    나. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트합니다. 이러한 값은 이 자습서의 뒷부분에 나오거나 [Qlik Sense Enterprise 클라이언트 지원 팀](https://www.qlik.com/us/services/support)에 문의하여 얻을 수 있습니다. 

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Qlik Sense 서버에 업로드할 수 있도록 페더레이션 메타데이터 XML 파일을 준비합니다.
   
    > [!NOTE]
    > Qlik Sense 서버로 IdP 메타데이터를 업로드하기 전에 Azure AD와 Qlik Sense 서버 간의 적절한 작동을 위해 정보를 제거하도록 파일을 편집해야 합니다.
    
    ![QlikSense][qs24]
   
    a. 텍스트 편집기의 Azure Portal에서 다운로드한 FederationMetaData.xml 파일을 엽니다.
   
    나. **RoleDescriptor**값을 검색합니다.  네 개의 항목(열고 닫는 요소 태그의 두 쌍)이 있습니다.
   
    다. 파일에서 사이의 RoleDescriptor 태그와 모든 정보를 삭제합니다.
   
    d. 파일을 저장하고 이 문서의 뒷부분에서 사용하기 위해 가까이 둡니다.

7. 가상 프록시 구성을 만들 수 있는 사용자로 Qlik Sense QMC(Qlik Management Console)로 이동합니다.

8. QMC에서 **가상 프록시** 메뉴 항목을 클릭합니다.
   
    ![QlikSense][qs6] 

9. 화면 아래쪽에서 **새로 만들기** 단추를 클릭합니다.
   
    ![QlikSense][qs7]

10. 가상 프록시 편집 화면이 나타납니다.  화면 오른쪽은 구성 옵션을 표시하기 위한 메뉴입니다.
   
    ![QlikSense][qs9]

11. 식별 메뉴 옵션을 선택하여 Azure 가상 프록시 구성에 대한 식별 정보를 입력합니다.
    
    ![QlikSense][qs8]  
    
    a. **설명** 필드는 가상 프록시 구성에 대한 친숙한 이름입니다.  설명에 대한 값을 입력합니다.
    
    나. **접두사** 필드는 Azure AD Single Sign-on으로 Qlik Sense 연결하기 위한 가상 프록시 끝점을 식별합니다.  이 가상 프록시에 대한 고유한 접두사 이름을 입력합니다.
    
    다. **세션 비활성 시간(분)** 은 이 가상 프록시를 통한 연결에 대한 제한 시간입니다.
    
    d. **세션 쿠키 헤더 이름**은 인증에 성공한 후 사용자가 받는 Qlik Sense 세션에 대한 세션 식별자를 저장하는 쿠키 이름입니다.  이 이름은 고유해야 합니다.

12. 표시하려면 인증 메뉴 옵션을 클릭합니다.  인증 화면이 나타납니다.
    
    ![QlikSense][qs10]
    
    a. **익명 액세스 모드** 드롭다운은 익명 사용자가 가상 프록시를 통해 Qlik Sense에 액세스할 수 있는지를 결정합니다.  기본 옵션은 익명 사용자 없음입니다.
    
    나. **인증 방법** 드롭다운은 가상 프록시가 사용할 인증 체계를 결정합니다.  드롭다운 목록에서 SAML을 선택합니다.  그 결과 더 많은 옵션이 표시됩니다.
    
    다. **SAML 호스트 URI 필드**에서 사용자가 이 SAML 가상 프록시를 통해 Qlik Sense에 액세스하기 위해 입력할 호스트 이름을 입력합니다.  호스트 이름은 Qlik Sense 서버의 URI입니다.
    
    d. **SAML 엔터티 ID**에서 SAML 호스트 URI 필드에 입력한 동일한 값을 입력합니다.
    
    e. **SAML IdP 메타데이터**는 **Azure AD 구성에서 페더레이션 메타데이터 편집** 섹션이 앞부분에서 편집한 파일입니다.  **IdP 메타데이터를 업로드하기 전에 파일을 편집해야 합니다** .  **파일을 아직 편집하지 않은 경우 위의 지침을 참조하세요.**  파일을 편집한 경우 찾아보기 단추를 클릭하고 편집한 메타데이터 파일을 선택하여 가상 프록시 구성에 업로드합니다.
    
    f. Azure AD가 Qlik Sense 서버에 전송하는 **UserID**를 나타내는 SAML 특성에 대한 특성 이름 또는 스키마 참조를 입력합니다.  스키마 참조 정보는 Azure 앱 화면 게시 구성에서 사용할 수 있습니다.  이름 특성을 사용하려면 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 선택합니다.
    
    g. Azure AD 통해 Qlik Sense 서버에 인증하는 경우 사용자에 연결될 **사용자 디렉터리** 에 대한 값을 입력합니다.  하드 코드된 값은 **[](대괄호)** 로 묶어야 합니다.  Azure AD SAML 어설션에서 전송된 특성을 사용하려면 대괄호 **없이** 이 텍스트 상자에 특성의 이름을 입력합니다.
    
    h. **SAML 서명 알고리즘** 은 가상 프록시 구성에 대한 서비스 공급자(이 경우 Qlik Sense 서버) 인증서 서명을 설정합니다.  Qlik Sense 서버가 Microsoft Enhanced RSA and AES Cryptographic Provider를 사용하여 생성된 신뢰할 수 있는 인증서를 사용하는 경우 SAML 서명 알고리즘을 **SHA-256**으로 변경합니다.
    
    i. SAML 특성 매핑 섹션을 통해 그룹과 같은 추가 특성을 보안 규칙에서 사용하기 위해 Qlik Sense로 전송할 수 있습니다.

13. 표시하려면 **부하 분산** 메뉴 옵션을 클릭합니다.  부하 분산 화면이 나타납니다.
    
    ![QlikSense][qs11]

14. **새 서버 노드 추가** 단추를 클릭하고 Qlik Sense가 부하 분산을 위해 세션을 전송할 엔진 노드 또는 노드를 선택하고 **추가** 단추를 클릭합니다.
    
    ![QlikSense][qs12]

15. 표시하려면 고급 메뉴 옵션을 클릭합니다. 고급 화면이 나타납니다.
    
    ![QlikSense][qs13]
    
    호스트 허용 목록은 Qlik Sense 서버에 연결할 때 허용되는 호스트 이름을 식별합니다.  **Qlik Sense 서버에 연결할 때 사용자가 지정할 호스트 이름을 입력합니다.** 호스트 이름은 https:// 없이 SAML 호스트 URI와 동일한 값입니다.

16. **적용** 단추를 클릭합니다.
    
    ![QlikSense][qs14]

17. 확인을 클릭하여 가상 프록시에 연결된 프록시가 다시 시작된다는 경고 메시지를 수락합니다.
    
    ![QlikSense][qs15]

18. 화면 오른쪽에 연결된 항목 메뉴가 나타납니다.  **프록시** 메뉴 옵션을 클릭합니다.
    
    ![QlikSense][qs16]

19. 프록시 화면이 나타납니다.  맨 아래의 **링크** 단추를 클릭하여 가상 프록시에 프록시를 연결합니다.
    
    ![QlikSense][qs17]

20. 이 가상 프록시 연결을 지원하는 프록시 노드를 선택하고 **링크** 단추를 클릭합니다.  연결 후 연결된 프록시 아래에 프록시가 나열됩니다.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. 약 5~10초 후에 새로 고침 QMC 메시지가 표시됩니다.  **새로 고침 QMC** 단추를 클릭합니다.
    
    ![QlikSense][qs20]

22. QMC가 새로 고쳐지면 **가상 프록시** 메뉴 항목을 클릭합니다. 새 SAML 가상 프록시 항목이 화면의 테이블에 나열됩니다.  가상 프록시 항목을 한 번 클릭합니다.
    
    ![QlikSense][qs51]

23. 화면 맨 아래에서 SP 메타데이터 다운로드 단추가 활성화됩니다.  **SP 메타데이터 다운로드** 단추를 클릭하여 파일에 메타데이터를 저장합니다.
    
    ![QlikSense][qs52]

24. SP 메타데이터 파일을 엽니다.  **entityID** 및 **AssertionConsumerService** 항목을 관찰합니다.  이러한 값은 Azure AD 응용 프로그램 구성의 **ID** 및 **로그인 URL**에 해당합니다. Azure AD 응용 프로그램 구성의 **Qlik Sense Enterprise 도메인 및 URL** 섹션에 해당 값을 붙여 넣습니다. 일치하지 않는 경우 Azure AD 앱 구성 마법사에서 대체해야 합니다.
    
    ![QlikSense][qs53]

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

   ![Azure Active Directory 단추](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

   !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

   ![추가 단추](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

   ![사용자 대화 상자](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. **이름** 상자에 **BrittaSimon**을 입력합니다.

   나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

   다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

   d. **만들기**를 클릭합니다.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise 테스트 사용자 만들기

이 섹션에서는 Qlik Sense Enterprise에서 Britta Simon이라는 사용자를 만듭니다. Qlik Sense Enterprise 플랫폼에서 사용자를 추가하려면 [Qlik Sense Enterprise 클라이언트 지원 팀](https://www.qlik.com/us/services/support)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Qlik Sense Enterprise에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Qlik Sense Enterprise에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Qlik Sense Enterprise**를 선택합니다.

    ![응용 프로그램 목록의 Qlik Sense Enterprise 링크](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Qlik Sense Enterprise 타일을 클릭하면 Qlik Sense Enterprise 응용 프로그램에 자동으로 로그온됩니다. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

