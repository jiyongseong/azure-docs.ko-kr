---
title: Azure Active Directory를 사용하여 개발자 계정에 권한 부여 - Azure API Management | Microsoft Docs
description: API Management에서 Azure Active Directory를 사용하여 사용자에게 권한을 부여하는 방법을 알아봅니다.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: b0476865b19cd078b05e5def4a51c2df17315daa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155384"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여

이 아티클에서는 Azure AD(Azure Active Directory)의 사용자에게 개발자 포털에 액세스할 수 있도록 하는 방법을 보여줍니다. 또한 이 가이드에서는 사용자를 포함하는 외부 그룹을 추가하여 Azure AD 사용자 그룹을 관리하는 방법을 보여줍니다.

> [!NOTE]
> Azure AD 통합은 [개발자, 표준 및 프리미엄](https://azure.microsoft.com/pricing/details/api-management/) 계층에서만 제공됩니다.

## <a name="prerequisites"></a>필수 조건

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md)를 참조하세요.

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD를 사용하여 개발자 계정에 권한 부여

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 여기서 ![화살표](./media/api-management-howto-aad/arrow.png)에서도 확인할 수 있습니다.
3. 검색 상자에 **api**를 입력합니다.
4. **API Management 서비스**를 선택합니다.
5. API Management 서비스 인스턴스를 선택합니다.
6. **보안** 아래에서 **ID**를 선택합니다.

7. 위에서 **+추가**를 선택합니다.

    **ID 공급자 추가** 창이 오른쪽에 나타납니다.
8. **공급자 형식** 아래에서 **Azure Active Directory**를 선택합니다.

    기타 필요한 정보를 입력할 수 있는 컨트롤이 창에 나타납니다. 제어에는 **클라이언트 ID** 및 **클라이언트 암호**가 포함됩니다. (이러한 컨트롤에 대한 정보는 이 아티클의 뒷부분에 다룹니다.)
9. **리디렉션 URL**의 콘텐츠를 기록해 둡니다.
    
   ![Azure Portal에서 ID 공급자를 추가하는 단계](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 브라우저에서 다른 탭을 엽니다. 
11. [Azure 포털](https://portal.azure.com)로 이동합니다.
12. 여기서 ![화살표](./media/api-management-howto-aad/arrow.png)에서도 확인할 수 있습니다.
13. **활성**을 입력합니다. **Azure Active Directory** 창이 표시됩니다.
14. **Azure Active Directory**를 선택합니다.
15. **관리** 아래에서 **앱 등록**을 선택합니다.
16. **새 응용 프로그램 등록**을 선택합니다.

    ![새 앱 등록을 만드는 선택 항목](./media/api-management-howto-aad/api-management-with-aad002.png)

    **만들기** 창이 오른쪽에 나타납니다. 여기에 Azure AD 앱 관련 정보를 입력합니다.
17. 응용 프로그램의 이름을 입력합니다.
18. 응용 프로그램 형식에서 **웹앱/API**를 선택합니다.
19. 로그온 URL에서 개발자 포털의 로그온 URL을 입력합니다. 이 예제에서 로그인 URL은 https://apimwithaad.portal.azure-api.net/signin입니다.
20. **만들기**를 선택하여 응용 프로그램을 만듭니다.
21. 앱을 찾으려면 **앱 등록**을 선택하고 이름으로 검색합니다.

    ![앱을 검색하는 상자](./media/api-management-howto-aad/find-your-app.png)
22. 응용 프로그램을 등록한 후에 **회신 URL**로 이동하고 **리디렉션 URL**이 9단계에서 가져온 값으로 설정되어 있는지 확인합니다. 
23. 응용 프로그램을 구성하려는 경우(예: **앱 ID URL** 변경) **속성**을 선택합니다.

    !["속성" 창 열기](./media/api-management-howto-aad/api-management-with-aad004.png)

    이 응용 프로그램에 여러 Azure AD 인스턴스를 사용할 경우 **다중 테넌트**에서 **예**를 선택합니다. 기본값은 **아니요**입니다.
24. **필수 권한**을 선택하여 응용 프로그램 사용 권한을 설정합니다.
25. 응용 프로그램을 선택한 다음, **디렉터리 데이터 읽기** 및 **로그인 및 사용자 프로필 읽기** 확인란을 선택합니다.

    ![사용 권한의 확인란](./media/api-management-howto-aad/api-management-with-aad005.png)

    응용 프로그램 권한 및 위임된 권한에 대한 자세한 내용은 [Graph API에 액세스][Accessing the Graph API]를 참조하세요.
26. 왼쪽 창에서 **응용 프로그램 ID** 값을 복사합니다.

    !["응용 프로그램 ID" 값](./media/api-management-howto-aad/application-id.png)
27. API Management 응용 프로그램으로 다시 전환합니다. 

    **ID 공급자 추가** 창의 **클라이언트 암호** 상자에 **응용 프로그램 ID** 값을 붙여넣습니다.
28. Azure AD 구성으로 다시 전환하고, **키**를 선택합니다.
29. 이름과 기간을 지정하여 새 키를 만듭니다. 
30. **저장**을 선택합니다. 키가 생성됩니다.

    키를 클립보드에 복사합니다.

    ![키를 생성하는 선택 영역](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > 이 키를 기록해 둡니다. Azure AD 구성 창을 닫으면 키를 다시 표시할 수 없습니다.
    > 
    > 
31. API Management 응용 프로그램으로 다시 전환합니다. 

    **ID 공급자 추가** 창의 **클라이언트 암호** 텍스트 상자에 키를 붙여넣습니다.

    > [!IMPORTANT]
    > 키가 만료되기 전에 **클라이언트 암호**를 업데이트해야 합니다. 
    >  
    >
32. **ID 공급자 추가** 창에는 **허용된 테넌트** 텍스트 상자도 포함됩니다. 여기에서 API Management 서비스 인스턴스의 API에 대한 액세스 권한을 부여하려는 Azure AD 인스턴스의 도메인을 지정합니다. 줄바꿈, 공백 또는 쉼표로 여러 도메인을 구분할 수 있습니다.

    **허용된 테넌트** 섹션에서 여러 도메인을 지정할 수 있습니다. 사용자가 응용 프로그램이 등록되었던 원래 도메인이 아닌 다른 도메인에서 로그인하려면, 다른 도메인의 전역 관리자가 디렉터리 데이터에 액세스할 수 있도록 응용 프로그램에 권한을 부여해야 합니다. 사용 권한을 부여하려면 전역 관리자는 다음을 수행해야 합니다.
    
    a. `https://<URL of your developer portal>/aadadminconsent`(예: https://contoso.portal.azure-api.net/aadadminconsent)로 이동합니다.
    
    나. 액세스 권한을 부여하려는 Azure AD 테넌트의 도메인 이름을 입력합니다.
    
    다. **제출**을 선택합니다. 
    
    다음 예제에서 miaoaad.onmicrosoft.com의 전역 관리자는 이 특정 개발자 포털에 사용 권한을 부여하려고 합니다. 

33. 원하는 구성을 지정한 후에 **추가**를 선택합니다.

    !["ID 공급자 추가" 창의 "추가" 단추](./media/api-management-howto-aad/api-management-with-aad007.png)

변경 내용이 저장되면 지정된 Azure AD 인스턴스의 사용자는 [Azure AD 계정을 사용하여 개발자 포털에 로그인](#log_in_to_dev_portal)의 단계를 수행하여 개발자 포털에 로그인할 수 있습니다.

![Azure AD 테넌트의 이름 입력](./media/api-management-howto-aad/api-management-aad-consent.png)

다음 화면에서 전역 관리자에게는 사용 권한의 부여를 확인하는 메시지가 표시됩니다. 

![사용 권한 할당 확인](./media/api-management-howto-aad/api-management-permissions-form.png)

전역 관리자가 권한을 부여하기 전에 비 전역 관리자가 로그인을 시도하는 경우 로그인 시도에 실패하면 오류 화면이 표시됩니다.

## <a name="add-an-external-azure-ad-group"></a>외부 Azure AD 그룹 추가

Azure AD 인스턴스에서 사용자에 대한 액세스를 활성화한 후에 API Management에서 Azure AD 그룹을 추가할 수 있습니다. 그런 다음, 원하는 제품과 그룹 개발자의 연결을 보다 쉽게 관리할 수 있습니다.

외부 Azure AD 그룹을 구성하려면 이전 섹션의 프로시저를 수행하여 **ID** 탭에서 먼저 Azure AD 인스턴스를 구성해야 합니다. 

API Management 인스턴스의 **그룹** 탭에 외부 Azure AD 그룹을 추가합니다.

1. **그룹** 탭을 선택합니다.
2. **AAD 그룹 추가** 단추를 선택합니다.
   !["AAD 그룹 추가" 단추](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 추가하려는 그룹을 선택합니다.
4. **선택** 단추를 누릅니다.

외부 Azure AD 그룹을 추가한 후에 해당 속성을 검토하고 구성할 수 있습니다. 그룹의 이름을 **그룹** 탭에서 선택합니다. 이 탭에서 그룹에 대한 **이름** 및 **설명** 정보를 편집할 수 있습니다.
 
이제 구성된 Azure AD 인스턴스의 사용자는 개발자 포털에 로그인할 수 있습니다. 표시 유형을 갖고 있는 모든 그룹을 확인하고 여기에 가입할 수 있습니다.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Azure AD 계정을 사용하여 개발자 포털에 로그인

이전 섹션에서 구성된 Azure AD 계정을 사용하여 개발자 포털에 로그인하려면:

1. Active Directory 응용 프로그램 구성의 로그인 URL을 사용하여 새 브라우저 창을 열고, **Azure Active Directory**를 선택합니다.

   ![로그인 페이지][api-management-dev-portal-signin]

2. Azure AD에서 사용자의 자격 증명을 입력하고 **로그인**을 선택합니다.

   ![사용자 이름 및 암호로 로그인][api-management-aad-signin]

3. 추가 정보가 필요한 경우 등록 양식과 함께 메시지가 표시될 수 있습니다. 등록 양식을 완성하고 **등록**을 선택합니다.

   ![등록 양식의 "등록" 단추][api-management-complete-registration]

이제 사용자는 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

![등록 완료 이후 개발자 포털][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
