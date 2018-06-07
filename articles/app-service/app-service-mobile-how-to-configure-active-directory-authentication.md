---
title: App Services 응용 프로그램에 대해 Azure Active Directory 인증 구성
description: App Services 응용 프로그램에 대해 Azure Active Directory 인증을 구성하는 방법을 알아봅니다.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 2530cb55cb054c02df5d55ccb86e959a061e2499
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Azure Active Directory 로그인을 사용하도록 App Service 앱 구성
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure Active Directory를 인증 공급자로 사용하도록 Azure App Services를 구성하는 방법을 보여 줍니다.

## <a name="express"> </a>기본 설정을 사용하여 Azure Active Directory 구성
1. [Azure Active Directory]에서 App Service 앱으로 이동합니다. 왼쪽 탐색 영역에서 **인증/권한 부여**를 선택합니다.
2. **인증/권한 부여**가 사용되도록 설정되지 않은 경우 **설정**을 선택합니다.
3. **Azure Active Directory**를 선택한 다음 **관리 모드**에서 **Express**를 선택합니다.
4. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 이렇게 하면 새 앱이 등록됩니다. 기존 앱 등록을 대신 선택하려는 경우 **기존 앱 선택** 을 클릭한 다음 테넌트 내에서 이전에 만든 앱 등록 이름을 검색합니다.
   앱 등록을 클릭하여 선택하고 **확인**을 클릭합니다. Azure Active Directory 설정 페이지에서 **확인**을 클릭합니다.
   기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
5. (옵션) Azure Active Directory에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **요청이 인증되지 않으면 수행할 동작**을 **Azure Active Directory로 로그인**으로 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.
6. **저장**을 클릭합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="advanced"> </a>(대체 방법) 고급 설정을 사용하여 Azure Active Directory를 수동으로 구성
수동으로 구성 설정을 제공하도록 선택할 수도 있습니다. 사용하려는 AAD 테넌트가 Azure에 로그인하는 테넌트와 다른 경우 권장되는 솔루션입니다. 구성을 완료하려면 먼저 Azure Active Directory에 등록을 만든 다음 App Service에 등록 세부 정보 중 일부를 제공해야 합니다.

### <a name="register"> </a>Azure Active Directory에 App Service 앱 등록
1. [Azure Active Directory]에 로그온한 다음 App Service 앱으로 이동합니다. 앱 **URL**을 복사합니다. Azure Active Directory 앱 등록을 구성하는 데 이 정보를 사용합니다.
2. **Active Directory**를 선택한 후 **앱 등록**을 선택하고 위쪽의 **새 응용 프로그램 등록**을 클릭하여 새 앱 등록을 시작합니다. 
3. **만들기** 페이지에서 앱 등록의 **이름**을 입력하고 **웹앱/API** 유형을 선택한 후 **로그온 URL** 상자에 응용 프로그램 URL(1단계)을 붙여 넣습니다. 그런 다음 **만들기**를 클릭합니다.
4. 몇 초 후에 방금 만든 새 앱 등록이 나타납니다.
5. 앱 등록이 추가되면 앱 등록 이름을 클릭하고 위쪽의 **설정**을 클릭한 후 **속성**을 클릭합니다. 
6. **앱 ID URI** 상자에 응용 프로그램 URL(1단계)을 붙여 넣고, **홈페이지 URL**에도 응용 프로그램 URL(1단계)을 붙여 넣은 후 **저장**을 클릭합니다.
7. 이제 **회신 URL**을 클릭하고 **회신 URL**을 편집한 후 응용 프로그램 URL(1단계)을 붙여 넣고, 프로토콜을 **https://** 프로토콜(http:// 아님)로 수정한 후 URL 끝에 */.auth/login/aad/callback*을 추가합니다(예: `https://contoso.azurewebsites.net/.auth/login/aad/callback`). **저장**을 클릭합니다.   
8.  이때 앱의 **응용 프로그램 ID**를 복사합니다. 나중에 사용하기 위해 보관합니다. App Service 앱을 구성하는 데 필요합니다.
9. **등록된 앱** 페이지를 닫습니다. **앱 등록** 페이지에서 위쪽의 **끝점** 단추를 클릭하고 **페더레이션 메타데이터 문서** URL을 복사합니다. 
10. 새 브라우저 창을 열고 URL을 붙여넣은 후 XML 페이지로 이동합니다. 문서 맨 위에는 **EntityDescriptor** 요소가 추가되며, 테넌트에 관련된 GUID(“테넌트 ID”라고 함) 앞에 양식 `https://sts.windows.net/`의 **entityID** 특성이 있어야 합니다. 이 값을 복사하여 **발급자 URL**로 사용합니다. 나중에 이 값을 사용하도록 응용 프로그램을 구성합니다.

### <a name="secrets"> </a>App Service 앱에 Azure Active Directory 정보 추가
1. [Azure Active Directory]로 돌아가서 App Service 앱으로 이동합니다. **인증/권한 부여**를 클릭합니다. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **설정**으로 전환합니다. 인증 공급자 아래의 **Azure Active Directory**를 클릭하여 앱을 구성합니다. (선택 사항) 기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다. **요청이 인증되지 않은 경우 수행할 작업**을 **Azure Active Directory로 로그인**으로 설정합니다. 이 옵션을 사용하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.
2. Active Directory 인증 구성에서 **관리 모드** 아래의 **고급**을 클릭합니다. 응용 프로그램 ID를 클라이언트 ID 상자에 붙여 넣고(8단계) entityId(10단계)를 발급자 URL 값에 붙여 넣습니다. 그런 후 **OK**를 클릭합니다.
3. Active Directory 인증 구성 페이지에서 **저장**을 클릭합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="optional-configure-a-native-client-application"></a>(옵션) 네이티브 클라이언트 응용 프로그램 구성
Azure Active Directory를 사용하면 권한 매핑에 대해 보다 강력한 제어를 제공하는 네이티브 클라이언트를 등록할 수도 있습니다. 이는 **Active Directory 인증 라이브러리**와 같은 라이브러리를 사용하여 로그인을 수행하려는 경우 필요합니다.

1. **Azure Portal**에서 [Azure Active Directory]로 이동합니다.
2. 왼쪽 탐색 영역에서 **앱 등록**을 선택합니다. 위쪽의 **새 앱 등록**을 클릭합니다.
4. **만들기** 페이지에서 앱 등록의 **이름**을 입력합니다. **응용 프로그램 유형**에서 **네이티브**를 선택합니다.
5. HTTPS 체계를 사용하여 **리디렉션 URI** 상자에 사이트의 */.auth/login/done* 끝점을 입력합니다. 이 값은 *https://contoso.azurewebsites.net/.auth/login/done*과 비슷해야 합니다. Windows 응용 프로그램을 만드는 경우 URI로 [패키지 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 를 사용합니다.
5. **만들기**를 클릭합니다.
6. 앱 등록이 추가되면 선택하여 엽니다. **응용 프로그램 ID**를 찾아 해당 값을 적어둡니다.
7. **모든 설정** > **필요한 권한** > **추가** > **API 선택**을 클릭합니다.
8. 이전에 등록한 App Service 앱의 이름을 입력하여 검색한 다음 선택하고 **선택**을 클릭합니다. 
9. **\<app_name >에 액세스**를 선택합니다. 그런 다음 **선택**을 클릭합니다. **완료**를 클릭합니다.

이제 App Service 앱에 액세스할 수 있는 네이티브 클라이언트 응용 프로그램을 구성했습니다.

## <a name="related-content"> </a>관련 콘텐츠
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Active Directory]: https://portal.azure.com/
[alternative method]:#advanced
