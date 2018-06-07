---
title: '자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Amazon Web Services(AWS) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 1cf57d76e87fcc0b6628ac194f4d5c389bf684d4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352585"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>자습서: AWS(Amazon Web Services)와 Azure Active Directory 통합

이 자습서에서는 Amazon Web Services(AWS)를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- AWS(Amazon Web Services) Single Sign-On 사용이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 AWS(Amazon Web Services) 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가
Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 AWS(Amazon Web Services)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에서 **AWS(Amazon Web Services)** 를 입력하고, 결과 패널에서 **AWS(Amazon Web Services)** 를 선택하고, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 AWS(Amazon Web Services)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[AWS(Amazon Web Services) 테스트 사용자 만들기](#create-an-amazon-web-services-aws-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 AWS(Amazon Web Services)에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 AWS(Amazon Web Services) 응용 프로그램에서 Single Sign-On을 구성합니다.

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **AWS(Amazon Web Services)** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. **AWS(Amazon Web Services) 도메인 및 URL** 섹션에서 앱이 Azure와 이미 사전 통합되었으므로 사용자는 아무 단계도 수행할 필요가 없습니다.

    ![AWS(Amazon Web Services) 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. AWS(Amazon Web Services) 소프트웨어 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

    ![Single Sign-On 구성 attb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름  | 특성 값 | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 역할            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS 콘솔에서 모든 역할을 가져오려면 Azure AD에서 사용자 프로비전을 구성해야 합니다. 아래에서 프로비전 단계를 참조하세요.

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Single Sign-On 추가 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Single Sign-On 특성 추가 구성](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스** 텍스트 상자에 해당 행에 대해 표시되는 네임스페이스 값을 입력합니다.
    
    d. **Ok**를 클릭합니다.

6. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

9. **AWS 홈**을 클릭합니다.
   
    ![Single Sign-On 홈 구성][11]

10. **ID 및 액세스 관리**를 클릭합니다. 
   
    ![Single Sign-On ID 구성][12]

11. **ID 공급자**를 클릭한 다음 **공급자 만들기**를 클릭합니다. 
   
    ![Single Sign-On 공급자 구성][13]

12. **공급자 구성** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 대화 상자 구성][14]
 
    a. **공급자 유형**으로 **SAML**을 선택합니다.

    나. **공급자 이름** 텍스트 상자에 공급자 이름(예: *WAAD*)을 입력합니다.

    다. Azure Portal에서 다운로드한 **메타데이터 파일**을 업로드하려면 **파일 선택**을 클릭합니다.

    d. **다음 단계**를 클릭합니다.

13. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다. 
    
    ![Single Sign-On 확인 구성][15]

14. **역할**을 클릭하고 **역할 만들기**를 클릭합니다. 
    
    ![Single Sign-On 역할 구성][16]

15. **역할 만들기** 페이지에서 다음 단계를 수행합니다.  
    
    ![Single Sign-On 트러스트 구성][19] 

    a. **신뢰할 수 있는 엔터티 유형 선택**에서 **SAML 2.0 페더레이션**을 선택합니다.

    나. **SAML 2.0 공급자 선택**에서 이전에 만든 **SAML 공급자**를 선택합니다(예: *WAAD*).

    다. **프로그래밍 및 AWS 관리 콘솔 액세스 허용**을 선택합니다.
  
    d. **다음: 권한**을 클릭합니다.

16. **사용 권한 정책 연결** 대화 상자에서 정책에 연결할 필요가 없습니다. **다음: 검토**를 클릭합니다.  
    
    ![Single Sign-On 정책 구성][33]

17. **검토** 대화 상자에서 다음 단계를 수행합니다.   
    
    ![Single Sign-On 검토 구성][34] 

    a. **역할 이름** 텍스트 상자에 역할 이름을 입력합니다.

    나. **역할 설명** 텍스트 상자에 설명을 입력합니다.

    다. **역할 만들기**를 클릭합니다.

    d. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

18. Azure AD 사용자 프로비전의 AWS 계정에서 역할을 페치하기 위해 AWS 서비스 계정 자격 증명을 사용합니다. 이 경우에 AWS 콘솔 홈을 엽니다.

19. **서비스** -> **보안, ID 및 규정 준수** -> **IAM**을 클릭합니다.

    ![AWS 계정에서 역할 페치](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

20. IAM 섹션에서 **정책** 탭을 선택합니다.

    ![AWS 계정에서 역할 페치](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

21. Azure AD 사용자 프로비전의 AWS 계정에서 역할을 가져올 수 있도록 **정책 만들기**를 클릭하여 새 정책을 만듭니다.

    ![새 정책 만들기](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)

22. 다음 단계를 수행하여 AWS 계정에서 모든 역할을 가져오는 고유한 정책을 만듭니다.

    ![새 정책 만들기](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. **"정책 만들기"** 섹션에서 **"JSON"** 탭을 클릭합니다.

    나. 정책 문서에서 아래 JSON을 추가합니다.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam:ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    다. **정책 검토 단추**를 클릭하여 정책의 유효성을 검사합니다.

    ![새 정책 정의](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

23. 다음 단계를 수행하여 **새 정책**을 정의합니다.

    ![새 정책 정의](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. **정책 이름**을 **AzureAD_SSOUserRole_Policy**로 제공합니다.

    나. 정책에 **이 정책을 사용하면 AWS 계정에서 역할을 페치할 수 있습니다.** 라는 **설명**을 제공할 수 있습니다.
    
    다. **"정책 만들기"** 단추를 클릭합니다.

24. 다음 단계를 수행하여 AWS IAM 서비스에서 새 사용자 계정을 만듭니다.

    a. AWS IAM 콘솔에서 **사용자** 탐색을 클릭합니다.

    ![새 정책 정의](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    나. **사용자 추가** 단추를 클릭하여 새 사용자를 만듭니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    다. **사용자 추가** 섹션에서 다음 단계를 수행합니다.
    
    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * 사용자 이름을 **AzureADRoleManager**로 입력합니다.
    
    * 액세스 형식에서 **프로그래밍 방식 액세스** 옵션을 선택합니다. 이러한 방식으로 사용자는 API를 호출하고 AWS 계정에서 역할을 가져올 수 있습니다.
    
    * 오른쪽 아래 모서리에서 **다음 사용 권한** 단추를 클릭합니다.

25. 이제 다음 단계를 수행하여 이 사용자에 대한 새 정책을 만듭니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. **기존 정책 직접 연결** 단추를 클릭합니다.

    나. **AzureAD_SSOUserRole_Policy** 필터 섹션에서 새로 만든 정책을 검색합니다.
    
    다. **정책**을 선택하고 **다음: 검토** 단추를 클릭합니다.

26. 다음 단계를 수행하여 연결된 사용자에 대한 정책을 검토합니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. 사용자 이름, 액세스 형식 및 사용자에 매핑된 정책을 검토합니다.
    
    나. 오른쪽 아래 모서리에 있는 **사용자 만들기** 단추를 클릭하여 사용자를 만듭니다.

27. 다음 단계를 수행하여 사용자의 사용자 자격 증명을 다운로드합니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. 사용자 **액세스 키 ID** 및 **암호 액세스 키**를 복사합니다.
    
    나. Azure AD 사용자 프로비전 섹션에 이러한 자격 증명을 입력하여 AWS 콘솔에서 역할을 페치합니다.
    
    다. 아래쪽에 있는 **닫기** 단추를 클릭합니다.

28. Azure AD 관리 포털에 있는 Amazon Web Services 앱의 **사용자 프로비전** 섹션으로 이동합니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

29. **클라이언트 암호** 및 **암호 토큰** 필드에 각각 **액세스 키** 및 **암호**를 입력합니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. **clientsecret** 필드에 AWS 사용자 액세스 키를 입력합니다.
    
    나. **암호 토큰** 필드에 AWS 사용자 암호를 입력합니다.
    
    다. **연결 테스트** 단추를 클릭하고 이 연결을 성공적으로 테스트할 수 있어야 합니다.

    d. 위쪽에 있는 **저장** 단추를 클릭하여 설정을 저장합니다.
 
30. 스위치를 켜고 상단에서 **저장** 단추를 클릭하여 설정 섹션에서 프로비전 상태 **켜기**를 사용할 수 있어야 합니다.

    ![사용자 추가](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>AWS(Amazon Web Services) 테스트 사용자 만들기

이 섹션에서는 AWS(Amazon Web Services)에서 Britta Simon이라는 사용자를 만듭니다. AWS(Amazon Web Services)에서는 SSO용으로 시스템에서 사용자를 만들 필요가 없으므로 여기에서 작업을 수행할 필요가 없습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 AWS(Amazon Web Services)에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 AWS(Amazon Web Services)에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **AWS(Amazon Web Services)** 를 선택합니다.

    ![응용 프로그램 목록의 AWS(Amazon Web Services) 링크](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

[액세스 패널]에서 [AWS(Amazon Web Services)] 타일을 클릭하면 AWS(Amazon Web Services) 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

