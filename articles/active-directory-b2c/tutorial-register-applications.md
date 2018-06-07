---
title: 자습서 - Azure Active Directory B2C를 사용하여 등록 및 로그인을 사용할 수 있도록 응용 프로그램 등록 | Microsoft Docs
description: Azure Portal을 사용하여 Azure AD B2C 테넌트를 만들고, 이 테넌트에 응용 프로그램을 등록합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 81ab3288d7a365c2665b3b38ca220a3e7cb648c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>자습서 - Azure Active Directory B2C를 사용하여 등록 및 로그인을 사용할 수 있도록 응용 프로그램 등록

이 자습서는 Microsoft Azure AD(Azure Active Directory) B2C 테넌트를 만들고, 몇 분 만에 응용 프로그램을 등록하는 데 도움이 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * 응용 프로그램 등록

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

B2C 기능은 기존 테넌트에 사용할 수 없습니다. Azure AD B2C 테넌트를 만들어야 합니다.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

축하합니다. Azure Active Directory B2C 테넌트를 만들었습니다. 테넌트의 전역 관리자입니다. 필요에 따라 다른 전역 관리자를 추가할 수 있습니다. 새 테넌트로 전환하려면 *새 테넌트 연결 관리*를 클릭합니다.

![새 테넌트 연결 관리](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 프로덕션 앱에 대해 B2C 테넌트를 사용하려는 경우 [프로덕션 규모와 B2C 테넌트 미리 보기 비교](active-directory-b2c-reference-tenant-type.md)의 문서를 참조하세요. 기존 B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들어야 하는 경우 알려진 문제가 발생합니다. 다른 도메인 이름으로 B2C 테넌트를 만들어야 합니다.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>구독에 테넌트 연결

모든 B2C 기능을 활성화하고 사용 요금을 지불하기 위해 Azure 구독에 Azure AD B2C 테넌트를 연결해야 합니다. 자세한 내용은 [이 문서](active-directory-b2c-how-to-enable-billing.md)를 참조하세요. Azure AD B2C 테넌트를 Azure 구독에 연결하지 않으면 일부 기능이 차단되고 B2C 설정에 경고 메시지("이 B2C 테넌트에 연결된 구독이 없거나 구독에 유의해야 합니다.")가 표시됩니다. 프로덕션에 앱을 전달하기 전에 이 단계를 수행해야 합니다.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

포털 위쪽에 있는 **리소스 검색**에 `Azure AD B2C`를 입력하여 블레이드에 액세스할 수도 있습니다. 결과 목록에서 **Azure AD B2C**를 선택하여 B2C 설정 블레이드에 액세스할 수 있습니다.

## <a name="register-your-application"></a>응용 프로그램 등록

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다.

Azure Portal에서 만든 응용 프로그램은 동일한 위치에서 관리되어야 합니다. PowerShell 또는 다른 포털을 사용하여 Azure AD B2C 응용 프로그램을 편집할 경우 해당 응용 프로그램이 지원되지 않으며 Azure AD B2C에서 작동하지 않습니다. [오류가 발생한 앱](#faulted-apps) 섹션에서 자세한 내용을 참조하세요. 

이 문서에서는 샘플을 시작하는 데 도움이 되는 예제를 사용합니다. 후속 문서에서는 이러한 샘플에 대해 자세히 알아볼 수 있습니다.

### <a name="navigate-to-b2c-settings"></a>B2C 설정으로 이동

B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>응용 프로그램 유형에 따라 다음 단계 선택

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

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * 응용 프로그램 등록

> [!div class="nextstepaction"]
> [웹 응용 프로그램이 계정을 인증하도록 설정](active-directory-b2c-tutorials-web-app.md)