---
title: Azure Active Directory B2C를 사용하여 개발자 계정에 권한 부여 - Azure API Management | Microsoft Docs
description: API Management에서 Azure Active Directory B2C를 사용하여 사용자에게 권한을 부여하는 방법에 대해 알아보세요.
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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: a6e7aad6c3d20a67ecba66c49be4efcdebdf718a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
> [!WARNING]
> Azure Active Directory B2C 통합은 [개발자, 표준 및 프리미엄](https://azure.microsoft.com/pricing/details/api-management/) 계층에서만 사용 가능합니다.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Azure API Management에서 Azure Active Directory B2C를 사용하여 개발자 계정에 권한을 부여하는 방법
## <a name="overview"></a>개요
Azure Active Directory B2C는 소비자 지향 웹 및 모바일 응용 프로그램을 위한 클라우드 ID 관리 솔루션입니다. 개발자 포털에 대한 액세스 권한을 관리하는 데 사용할 수 있습니다. 이 가이드에서는 Azure Active Directory B2C와 통합하려는 API Management 서비스에 필요한 구성을 보여 줍니다. 클래식 Azure Active Directory를 사용하여 개발자 포털에 대한 액세스를 사용하는 방법에 대한 정보는 [Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법]을 참조하세요.

> [!NOTE]
> 이 가이드의 단계를 완료하려면 먼저 응용 프로그램을 만들 Azure Active Directory B2C 테넌트가 있어야 합니다. 또한, 등록 및 로그인 정책이 준비되어야 합니다. 자세한 내용은 [Azure Active Directory B2C 개요]를 참조하세요.

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 개발자 계정에 권한 부여

1. 시작하려면 Azure Portal에서 API Management 서비스에 대한 **게시자 포털**을 클릭합니다. API Management 게시자 포털로 이동됩니다.

   ![게시자 포털][api-management-management-console]

   > [!NOTE]
   > 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [Azure API Management 시작 자습서][Get started with Azure API Management]의 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

2. **API Management** 메뉴에서 **보안**을 클릭합니다. **ID** 탭에서 **Azure Active Directory B2C**를 선택합니다.

  ![외부 ID 1][api-management-howto-aad-b2c-security-tab]

3. **리디렉션 URL**을 기록해 두고 Azure Portal에서 Azure Active Directory B2C로 전환합니다.

  ![외부 ID 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. **응용 프로그램** 단추를 클릭합니다.

  ![새 응용 프로그램 1 등록][api-management-howto-aad-b2c-portal-menu]

5. **추가** 단추를 클릭하여 새 Azure Active Directory B2C 응용 프로그램을 만듭니다.

  ![새 응용 프로그램 2 등록][api-management-howto-aad-b2c-add-button]

6. **새 응용 프로그램** 블레이드에서 응용 프로그램의 이름을 입력합니다. **Web App/Web API**에서 **예**를 선택하고 **암시적 흐름 허용**에서 **예**를 선택합니다. 그런 다음, 게시자 포털의 **ID** 탭에 있는 **Azure Active Directory B2C** 섹션에서 **리디렉션 URL**을 복사하여 **회신 URL** 텍스트 상자에 붙여넣습니다.

  ![새 응용 프로그램 3 등록][api-management-howto-aad-b2c-app-details]

7. **만들기** 단추를 클릭합니다. 응용 프로그램이 만들어지면 **응용 프로그램** 블레이드에 표시됩니다. 세부 정보를 보려면 응용 프로그램 이름을 클릭합니다.

  ![새 응용 프로그램 4 등록][api-management-howto-aad-b2c-app-created]

8. **속성** 블레이드에서 **응용 프로그램 ID**를 클립보드에 복사합니다.

  ![응용 프로그램 ID 1][api-management-howto-aad-b2c-app-id]

9. 게시자 포털로 다시 전환하고 ID를 **클라이언트 ID** 텍스트 상자에 붙여넣습니다.

  ![응용 프로그램 ID 2][api-management-howto-aad-b2c-client-id]

10. Azure Portal로 다시 전환하고 **키** 단추를 클릭한 다음 **키 생성**을 클릭합니다. **저장**을 클릭하여 구성을 저장하고 **앱 키**를 표시합니다. 키를 클립보드에 복사합니다.

  ![앱 키 1][api-management-howto-aad-b2c-app-key]

11. 게시자 포털로 다시 전환하고 키를 **클라이언트 암호** 텍스트 상자에 붙여 넣습니다.

  ![앱 키 2][api-management-howto-aad-b2c-client-secret]

12. **허용된 테넌트**에서 Azure Active Directory B2C 테넌트의 도메인 이름을 지정합니다.

  ![허용된 테넌트][api-management-howto-aad-b2c-allowed-tenant]

13. **등록 정책** 및 **로그인 정책**을 지정합니다. 선택적으로 **프로필 편집 정책** 및 **암호 재설정 정책**을 제공할 수도 있습니다.

  ![정책][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > 정책에 대한 자세한 내용은 [Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]를 참조하세요.

14. 원하는 구성이 지정되면 **저장**을 클릭합니다.

  변경 내용이 저장되면 개발자는 Azure Active Directory B2C를 사용하여 새 계정을 만들고 개발자 포털에 로그인할 수 있습니다.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 개발자 계정에 등록

1. Azure Active Directory B2C를 사용하여 개발자 계정에 등록하려면 새 브라우저 창을 열고 개발자 포털로 이동합니다. **등록** 단추를 클릭합니다.

   ![개발자 포털 1][api-management-howto-aad-b2c-dev-portal]

2. **Azure Active Directory B2C**를 사용하여 등록하도록 선택합니다.

   ![개발자 포털 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. 이전 섹션에서 구성하였던 등록 정책으로 리디렉션됩니다. 전자 메일 주소 또는 기존 소셜 계정 중 하나를 사용하여 등록하도록 선택합니다.

   > [!NOTE]
   > Azure Active Directory B2C가 게시자 포털의 **ID** 탭에서 사용하도록 설정되는 유일한 옵션인 경우 등록 정책에 직접 리디렉션됩니다.

   ![개발자 포털][api-management-howto-aad-b2c-dev-portal-b2c-options]

   등록이 완료되면 개발자 포털로 다시 리디렉션됩니다. 이제 API Management 서비스 인스턴스에 대한 개발자 포털에 로그인됩니다.

    ![등록 완료][api-management-registration-complete]

## <a name="next-steps"></a>다음 단계

*  [Azure Active Directory B2C 개요]
*  [Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]
*  [Azure Active Directory B2C에서 Microsoft 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Google 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 LinkedIn 계정을 ID 공급자로 사용]
*  [Azure Active Directory B2C에서 Facebook 계정을 ID 공급자로 사용]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Azure Active Directory B2C 개요]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: 확장할 수 있는 정책 프레임워크]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Azure Active Directory B2C에서 Microsoft 계정을 ID 공급자로 사용]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Azure Active Directory B2C에서 Google 계정을 ID 공급자로 사용]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Azure Active Directory B2C에서 Facebook 계정을 ID 공급자로 사용]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C에서 LinkedIn 계정을 ID 공급자로 사용]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
