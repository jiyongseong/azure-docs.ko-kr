---
title: '자습서: 여러 계정 연결을 위해 AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 여러 Amazon Web Services(AWS) 계정 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: a6a7861b95f82180b72045f10db99d5bc45eed73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>자습서: AWS(Amazon Web Services)와 여러 Azure Active Directory 계정 통합

이 자습서에서는 여러 Amazon Web Services(AWS) 계정을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD Basic 또는 Premium 구독 
- AWS(Amazon Web Services) Single Sign-On을 사용하도록 설정된 계정

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

    ![결과 목록의 AWS(Amazon Web Services)](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. 응용 프로그램이 추가되면 **속성** 페이지로 이동하여 **개체 ID**를 복사합니다.

    ![결과 목록의 AWS(Amazon Web Services)](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 AWS(Amazon Web Services) 응용 프로그램에서 Single Sign-On을 구성합니다.

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **AWS(Amazon Web Services)** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. **AWS(Amazon Web Services) 도메인 및 URL** 섹션에서 앱이 Azure와 이미 사전 통합되었으므로 사용자는 아무 단계도 수행할 필요가 없습니다.

    ![AWS(Amazon Web Services) 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. AWS(Amazon Web Services) 소프트웨어 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

    ![Single Sign-On 특성 구성](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름  | 특성 값 | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 역할            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS 콘솔에서 모든 역할을 가져오려면 Azure AD에서 사용자 프로비전을 구성해야 합니다. 아래에서 프로비전 단계를 참조하세요.

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Single Sign-On 추가 구성](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Single Sign-On 특성 구성](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스** 텍스트 상자에 해당 행에 대해 표시되는 네임스페이스 값을 입력합니다.
    
    d. **Ok**를 클릭합니다.

6. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

9. **AWS 홈**을 클릭합니다.
   
    ![Single Sign-On 홈 구성][11]

10. **IAM**(ID 및 액세스 관리)을 클릭합니다. 
   
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

16. **권한 연결 정책** 대화 상자에서 **다음: 검토**를 클릭합니다.  
    
    ![Single Sign-On 정책 구성][33]

17. **검토** 대화 상자에서 다음 단계를 수행합니다.   
    
    ![Single Sign-On 검토 구성][34] 

    a. **역할 이름** 텍스트 상자에 역할 이름을 입력합니다.

    나. **역할 설명** 텍스트 상자에 설명을 입력합니다.

    a. **역할 만들기**를 클릭합니다.

    나. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

18. 현재 AWS 계정에서 로그아웃하고 Azure AD에 Single Sign-On을 구성할 다른 계정으로 로그인합니다.

19. 9~17단계를 수행하여 이 계정에 대해 설정하려는 여러 역할을 만듭니다. 계정이 3개 이상인 경우 모든 계정에 같은 단계를 수행하여 해당하는 역할을 만듭니다.

20. 계정에서 역할이 모두 만들어지면 해당 계정의 **역할** 목록에 나타납니다.

    ![역할 설정](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. 모든 계정에서 모든 역할에 대해 모든 역할 ARN 및 신뢰할 수 있는 엔터티를 캡처해야 하므로 Azure AD 응용 프로그램에 수동으로 매핑해야 합니다. 

22. 역할을 클릭하여 **역할 ARN** 및 **신뢰할 수 있는 엔터티** 값을 복사합니다. Azure AD에서 만들어야 하는 모든 역할에 대해 이 값이 필요합니다.

    ![역할 설정](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. 모든 계정의 모든 역할에 대해 위의 단계를 수행하고 메모장에 모든 항목을 **역할 ARN, 신뢰할 수 있는 엔터티** 형식으로 저장합니다. 

24. 또 다른 창에서 [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 엽니다.

    a. 테넌트의 전역 관리자/공동 관리자 자격 증명을 사용하여 Graph Explorer 사이트에 로그인합니다.

    나. 역할을 만들 수 있는 권한이 필요합니다. **권한 수정**을 클릭하여 필요한 권한을 얻을 수 있습니다. 

    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    다. 목록에서 다음 권한을 선택하고(아직 선택하지 않은 경우) "권한 수정"을 클릭합니다. 

    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 여기서는 다시 로그인하고 동의할 것을 요청합니다. 동의 후에는 Graph Explorer에 다시 로그인됩니다.

    e. 버전 드롭다운을 **베타**로 변경합니다. 테넌트로부터 모든 서비스 사용자를 가져오려면 다음 쿼리를 사용합니다.
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    여러 디렉터리를 사용하는 경우 다음 패턴을 사용할 수 있습니다. 여기서는 주 도메인이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`에 있습니다.
    
    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. 가져온 서비스 사용자 목록에서 수정할 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 ServicePrincipals에서 응용 프로그램을 검색할 수도 있습니다. Azure AD 속성 페이지에서 복사한 **개체 ID**를 사용하여 다음 쿼리를 통해 해당하는 서비스 사용자로 이동할 수 있습니다.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. 서비스 사용자 개체에서 appRoles 속성을 추출합니다. 

    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. 이제 응용 프로그램에 대한 새 역할을 생성해야 합니다. 

    i. 다음 JSON은 appRoles 개체의 예입니다. 응용 프로그램에 역할을 추가할 유사한 개체를 만듭니다. 

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > 패치 작업에 대한 **msiam_access** 다음에만 새 역할을 추가할 수 있습니다. 또한 조직 요구에 따라 원하는 만큼의 역할을 추가할 수 있습니다. Azure AD는 SAML 응답의 클레임 값으로 이 역할의 **값**을 보냅니다.
    
    j. Graph Explorer로 돌아가 메서드를 **GET**에서 **PATCH**로 변경합니다. 위 예제의 것과 유사하게 appRoles 속성을 업데이트하여 원하는 역할을 갖도록 서비스 사용자 개체를 패치합니다. **쿼리 실행**을 클릭하여 패치 작업을 실행합니다. Amazon Web Services 응용 프로그램에 대한 역할이 만들어졌음을 확인하는 성공 메시지가 표시됩니다.

    ![Graph Explorer 대화 상자](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. 서비스 사용자에 더 많은 역할이 패치되면 각 역할에 사용자/그룹을 할당할 수 있습니다. 이 작업을 수행하려면 포털로 이동한 후 Amazon Web Services 응용 프로그램으로 이동합니다. 맨 위에서 **사용자 및 그룹** 탭을 클릭합니다. 

26. 해당 그룹에 특정 역할을 할당할 수 있도록 모든 AWS 역할에 대해 새 그룹을 만드는 것이 좋습니다. 하나의 그룹과 하나의 역할에 대한 일대일 매핑입니다. 그런 다음, 해당 그룹에 속하는 멤버를 추가할 수 있습니다.

27. 그룹을 만든 후에는 그룹을 선택하여 응용 프로그램에 할당합니다. 

    ![Single Sign-On 구성 추가](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. 그룹에 역할을 할당하려면 역할을 선택하고 페이지 아래의 **할당** 단추를 클릭합니다.

    ![Single Sign-On 구성 추가](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> 새 역할을 확인하려면 Azure Portal에서 세션을 새로 고쳐야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AWS(Amazon Web Services) 타일을 클릭하면 역할 선택 옵션이 없는 AWS(Amazon Web Services) 응용 프로그램 페이지가 나타납니다.

![Single Sign-On 구성 추가](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

SAML 응답에서도 클레임으로 전달된 역할을 확인할 수 있습니다.

![Single Sign-On 구성 추가](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

