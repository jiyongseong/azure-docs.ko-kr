---
title: 응용 프로그램 등록 - Azure Active Directory B2C
description: Azure Active Directory B2C로 응용 프로그램 등록하는 방법
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: davidmu
ms.openlocfilehash: 8ba511464f8ce0bef2a14706a272f6c09dfe5d07
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: 응용 프로그램 등록

이 Quickstart를 통해 몇 분 이내에 Microsoft Azure Active Directory(Azure AD) B2C 테넌트에서 응용 프로그램을 등록할 수 있습니다. 완료되면 Azure AD B2C 테넌트에서 사용할 수 있도록 응용 프로그램이 등록됩니다.

## <a name="prerequisites"></a>필수 조건

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다.

Azure Portal에서 만든 응용 프로그램은 동일한 위치에서 관리되어야 합니다. PowerShell 또는 다른 포털을 사용하여 Azure AD B2C 응용 프로그램을 편집할 경우 해당 응용 프로그램이 지원되지 않으며 Azure AD B2C에서 작동하지 않습니다. [오류가 발생한 앱](#faulted-apps) 섹션에서 자세한 내용을 참조하세요. 

이 문서에서는 샘플을 시작하는 데 도움이 되는 예제를 사용합니다. 후속 문서에서는 이러한 샘플에 대해 자세히 알아볼 수 있습니다.

## <a name="navigate-to-b2c-settings"></a>B2C 설정으로 이동

B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>응용 프로그램 유형에 따라 다음 단계 선택

* [웹 응용 프로그램 등록](#register-a-web-app)
* [웹 API 등록](#register-a-web-api)
* [모바일 또는 네이티브 앱 등록](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>웹앱 등록

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>웹앱 클라이언트 암호 만들기

웹 응용 프로그램이 Azure AD B2C에서 보호하는 웹 API를 호출하는 경우 다음 단계를 수행하세요.
   1. **키** 블레이드로 이동하여 **키 생성** 단추를 클릭하여 응용 프로그램 비밀을 만듭니다. **앱 키** 값을 기록해 둡니다. 이 값을 응용 프로그램의 코드에서 응용 프로그램 비밀로 사용합니다.
   2. **API 액세스**를 클릭하고 **추가**를 클릭한 다음 웹 API와 범위(사용 권한)를 선택합니다.

> [!NOTE]
> **응용 프로그램 비밀**은 중요한 보안 자격 증명이며 적절하게 보호해야 합니다.
> 

[**다음 단계**로 이동](#next-steps)

### <a name="register-a-web-api"></a>웹 API 등록

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

**게시된 범위**를 클릭하여 필요에 따라 범위를 더 추가합니다. 기본적으로 "user_impersonation" 범위가 정의됩니다. user_impersonation 범위가 로그인한 사용자를 대신하여 다른 응용 프로그램이 이 API에 액세스할 수 있는 기능을 부여합니다. 원하는 경우에 user_impersonation 범위를 제거할 수 있습니다.

[**다음 단계**로 이동](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>모바일 또는 네이티브 응용 프로그램 등록

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[**다음 단계**로 이동](#next-steps)

## <a name="limitations"></a>제한 사항

### <a name="choosing-a-web-app-or-api-reply-url"></a>웹앱 또는 API 회신 URL 선택

현재, Azure AD B2C에 등록된 앱은 제한된 회신 URL 값 집합으로 제한됩니다. 웹앱 및 서비스에 대한 회산 URL은 스키마 `https`로 시작해야 하고 모든 회신 URL 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 다음 회신 URL 중 하나가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com`

`https://login-west.contoso.com`

등록 시스템은 기존 회신 URL의 전체 DNS 이름을 편집하려는 회신 URL의 DNS 이름과 비교합니다. 다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.

* 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 DNS 이름과 일치하지 않는 경우
* 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 하위 도메인이 아닌 경우

예를 들어 앱에 다음 회신 URL이 있는 경우

`https://login.contoso.com`

다음과 같이 추가할 수 있습니다.

`https://login.contoso.com/new`

이 경우 DNS 이름이 정확히 일치합니다. 또는 다음을 수행할 수 있습니다.

`https://new.login.contoso.com`

이 경우 login.contoso.com의 DNS 하위 도메인을 참조합니다. 회신 URL로 login-east.contoso.com 및 login-west.contoso.com을 사용하는 앱을 원하는 경우 다음 회신 URL을 순서대로 추가해야 합니다.

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

뒤의 두 개는 첫 번째 회신 URL의 하위 도메인이므로 추가할 수 있습니다.

### <a name="choosing-a-native-app-redirect-uri"></a>네이티브 앱 리디렉션 URI 선택

모바일/네이티브 응용 프로그램에 대한 리디렉션 URI를 선택하는 경우 두 가지 중요한 고려 사항이 있습니다.

* **고유**: 리디렉션 URI의 체계는 모든 응용 프로그램에 대해 고유해야 합니다. 예제(com.onmicrosoft.contoso.appname://redirect/path)에서는 com.onmicrosoft.contoso.appname이 체계입니다. 이 패턴을 따르는 것이 좋습니다. 두 개의 응용 프로그램이 동일한 체계를 공유하는 경우 "앱 선택" 대화 상자가 나타납니다. 사용자가 잘못 선택하는 경우 로그인이 실패합니다.
* **전체**: 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 뒤에 하나 이상의 슬래시를 포함해야 합니다(예: //contoso/는 작동, //contoso는 실패).

리디렉션 uri에 밑줄과 같은 특수 문자가 없는지 확인합니다.

### <a name="faulted-apps"></a>오류가 발생한 앱

B2C 응용 프로그램은 다음에서 편집할 수 없습니다.

* [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/)과 같은 다른 응용 프로그램 관리 포털.
* Graph API 또는 PowerShell 사용

설명한 대로 Azure AD B2C 응용 프로그램을 편집하고 Azure Portal의 Azure AD B2C 기능에서 다시 편집하려는 경우 오류가 발생한 앱이 되어 Azure AD B2C에서 응용 프로그램을 더 이상 사용할 수 없습니다. 응용 프로그램을 삭제하고 다시 만들어야 합니다.

앱을 삭제하려면 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/)로 이동하고 거기에서 응용 프로그램을 삭제합니다. 응용 프로그램을 표시하려면 응용 프로그램의 소유자여야 합니다(테넌트의 관리자가 아님).

## <a name="next-steps"></a>다음 단계

Azure AD B2C에 등록된 응용 프로그램이 있으니 [빠른 시작 자습서](active-directory-b2c-overview.md) 중 하나를 완료하여 실행할 수 있습니다.

> [!div class="nextstepaction"]
> [등록, 로그인 및 암호 재설정으로 ASP.NET 웹앱 만들기](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
