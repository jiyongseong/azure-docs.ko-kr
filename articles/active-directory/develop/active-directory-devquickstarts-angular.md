---
title: Azure AD AngularJS 시작 | Microsoft 문서
description: 로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 AngularJS 단일 페이지 응용 프로그램을 빌드하는 방법.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b99ce605d9ecea6c7d67ab9a2ea679d531787d7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD AngularJS 시작

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD(Azure Active Directory)를 사용하면 단일 페이지 앱에 단순하고 간편하게 로그인, 로그아웃 및 보안 OAuth API 호출을 추가할 수 있습니다. 또한 앱에서 Windows Server Active Directory 계정으로 사용자를 인증하고 Azure AD를 통해 보호되는 Web API(예: Office 365 API) 또는 Azure API를 사용할 수 있습니다.

브라우저에서 실행되는 JavaScript 응용 프로그램의 경우 Azure AD가 ADAL(Active Directory 인증 라이브러리) 또는 adal.js를 제공합니다. adal.js의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 이 작업이 얼마나 쉬운지 보여 주기 위해 여기서는 다음 작업을 수행하는 AngularJS To Do List 응용 프로그램을 빌드할 것입니다.

* Azure AD를 ID 공급자로 사용하여 사용자를 앱에 로그인합니다.

* 사용자에 대한 일부 정보를 표시합니다.
* Azure AD의 전달자 토큰을 사용하여 앱의 To Do List API를 안전하게 호출합니다.
* 앱에서 사용자를 로그아웃합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

1. Azure AD에 앱을 등록합니다.
2. ADAL을 설치하고 단일 페이지 앱을 구성합니다.
3. ADAL을 사용하여 단일 페이지 앱에서 페이지 보안을 지원합니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)하세요. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1단계: DirectorySearcher 응용 프로그램 등록
앱에서 사용자를 인증하고 토큰을 가져올 수 있게 하려면 먼저 앱을 Azure AD 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 여러 디렉터리에 로그인된 경우 올바른 디렉터리를 보고 있는지 확인해야 할 수 있습니다. 이렇게 하려면 위쪽 모음에서 계정을 클릭합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 Azure AD 테넌트를 선택합니다.
3. 왼쪽 창에서 **모든 서비스**를 클릭한 다음, **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 클릭하고 **추가**를 선택합니다.
5. 프롬프트에 따라 새 웹 응용 프로그램 및/또는 Web API를 만듭니다.
  * **이름**은 사용자에게 응용 프로그램을 설명합니다.
  * **로그온 URL**은 Azure AD가 토큰을 반환할 위치입니다. 이 샘플의 기본 위치는 `https://localhost:44326/`입니다.
6. 등록을 완료한 후에는 Azure AD가 사용자 앱에 고유한 응용 프로그램 ID를 할당합니다. 이 값은 다음 섹션에서 필요하므로 응용 프로그램 탭에서 복사해 둡니다.
7. Adal.js는 OAuth 암시적 흐름을 사용하여 Azure AD와 통신합니다. 응용 프로그램에 대한 암시적 흐름을 사용하도록 설정해야 합니다.
  1. 응용 프로그램을 클릭하고 **매니페스트**를 선택하여 인라인 매니페스트 편집기를 엽니다.
  2. `oauth2AllowImplicitFlow` 속성을 찾습니다. 해당 값을 `true`로 설정합니다.
  3. **저장**을 클릭하여 매니페스트를 저장합니다.
8. 응용 프로그램에 대해 테넌트 전체에서 권한을 부여합니다. **설정** > **필수 권한**으로 이동하고 위쪽 막대에서 **권한 부여** 단추를 클릭합니다. **예** 를 클릭하여 확인합니다.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>2단계: ADAL 설치 및 단일 페이지 앱 구성
Azure AD에서 응용 프로그램이 있으므로 adal.js를 설치하고 ID 관련 코드를 작성할 수 있습니다.

### <a name="configure-the-javascript-client"></a>JavaScript 클라이언트 구성
먼저 패키지 관리자 콘솔을 사용하여 TodoSPA 프로젝트에 Adal.js를 추가합니다.
  1. [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js)를 다운로드하여 `App/Scripts/` 프로젝트 디렉터리에 추가합니다.
  2. [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js)를 다운로드하여 `App/Scripts/` 프로젝트 디렉터리에 추가합니다.
  3. `</body>` in `index.html`끝 이전에 각 스크립트를 로드합니다.

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>백 엔드 서버 구성
단일 페이지 앱의 백 엔드 To Do List API가 브라우저에서 토큰을 수락하도록 하려면 백 엔드에 앱 등록에 대한 구성 정보가 필요합니다. TodoSPA 프로젝트에서 `web.config`를 엽니다. Azure Portal에 사용한 값을 반영하도록 `<appSettings>` 섹션의 요소 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
  * `ida:Tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
  * `ida:Audience`는 포털에서 복사한 응용 프로그램의 클라이언트 ID입니다.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>3단계: ADAL을 사용하여 단일 페이지 앱에서 페이지 보안 지원
Adal.js는 AngularJS 경로 및 HTTP 공급자와 통합되므로 단일 페이지 앱에서 개별 뷰의 보안을 지원할 수 있습니다.

1. `App/Scripts/app.js`에서 다음과 같이 adal.js 모듈을 가져옵니다.

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. `App/Scripts/app.js`에서도 응용 프로그램 등록의 구성 값을 사용하여 `adalProvider`를 초기화합니다.

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. 코드 줄 `requireADLogin` 하나만 사용하여 앱에서 `TodoList` 뷰의 보안을 지원할 수 있습니다.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>요약
이제 사용자를 로그인하고 전달자 토큰으로 보호된 요청을 해당 백 엔드 API에 실행할 수 있는 보안 단일 페이지 앱을 사용할 수 있습니다. 사용자가 **TodoList** 링크를 클릭하면 adal.js는 필요한 경우 로그인을 위해 Azure AD에 자동으로 리디렉션합니다. 또한 adal.js는 앱의 백 엔드로 전송되는 모든 Ajax 요청에 자동으로 액세스 토큰을 연결합니다. 

위의 단계는 adal.js를 사용하여 단일 페이지 앱을 빌드하는 데 필요한 최소 기본 사항입니다. 하지만 단일 페이지 앱에서 유용한 몇 가지 다른 기능이 있습니다.

* 로그인 및 로그아웃 요청을 명시적으로 실행하기 위해 adal.js를 호출하는 컨트롤러에서 함수를 정의할 수 있습니다. `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* 앱의 UI에 사용자 정보를 표시하려고 할 수 있습니다. ADAL 서비스가 `userDataCtrl` 컨트롤러에 이미 추가되었으므로 관련 뷰 `App/Views/UserData.html`에서 `userInfo` 개체에 액세스할 수 있습니다.

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* 사용자가 로그인했는지 여부를 확인하고 싶은 경우가 많이 있을 수 있습니다. `userInfo` 개체를 사용하여 이 정보를 수집할 수도 있습니다. 예를 들어 `index.html`에서는 인증 상태에 따라 **로그인** 또는 **로그아웃** 단추를 표시할 수 있습니다.

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure AD 통합 단일 페이지 앱에서는 사용자를 인증하고, OAuth 2.0을 사용하여 해당 백 엔드를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. To Do List 단일 페이지 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 사용자의 할 일 목록에 작업을 추가하고, 사용자를 로그아웃했다가 다시 로그인합니다.

Adal.js는 응용 프로그램에 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)에 제공됩니다.

## <a name="next-steps"></a>다음 단계
이제 추가 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다. [단일 페이지 앱에서 CORS Web API 호출](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
