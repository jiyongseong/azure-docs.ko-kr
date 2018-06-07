---
title: 자습서 - Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API로의 액세스 권한 부여 | Microsoft Docs
description: Active Directory B2C를 사용하여 Node.js web api를 보호하고 .NET 데스크톱 앱에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: parja
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: ef92a7fbbc3b84195eff251b0224cb0fd51a6c88
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API로의 액세스 권한 부여

이 자습서에서는 WPF(Windows Presentation Foundation)에서 Azure AD(Azure Active Directory) B2C로 보호되는 Node.js Web API 리소스를 호출하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 웹 API 등록
> * 웹 API에 대한 범위 정의 및 구성
> * 웹 API에 앱 사용 권한 부여
> * Azure AD B2C를 사용하여 웹 API를 보호하는 샘플 코드 업데이트

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](active-directory-b2c-tutorials-desktop-app.md)를 완료합니다.
* **.NET 데스크톱 개발** 및 **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
* [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>웹 API 등록

Azure Active Directory에서 [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)을 제공하는 [클라이언트 응용 프로그램](../active-directory/develop/active-directory-dev-glossary.md#client-application)을 통해 [보호된 리소스 요청](../active-directory/develop/active-directory-dev-glossary.md#resource-server)을 수락하고 이에 응답하려면, 먼저 웹 API 리소스를 테넌트에 등록해야 합니다. 등록하면 [응용 프로그램 및 서비스 사용자 개체](../active-directory/develop/active-directory-dev-glossary.md#application-object)가 테넌트에 설정됩니다. 

Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택합니다.

2. B2C 설정에서 **응용 프로그램**, **추가**를 차례로 클릭합니다.

    샘플 웹 API를 테넌트에 등록하려면 다음 설정을 사용합니다.
    
    ![새 API 추가](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 내 샘플 Node.js Web API | 개발자에게 웹 API를 설명하는 **이름**을 입력합니다. |
    | **웹앱/웹 API 포함** | 예 | 웹 API에 **예**를 선택합니다. |
    | **암시적 흐름 허용** | 예 | API에서 [OpenID Connect 로그인](active-directory-b2c-reference-oidc.md)을 사용하므로 **예**를 선택합니다. |
    | **회신 URL** | `http://localhost:5000` | 회신 URL은 Azure AD B2C에서 API가 요청한 토큰을 반환하는 엔드포인트입니다. 이 자습서에서는 샘플 웹 API가 로컬(로컬 호스트)에서 실행되고 5000 포트에서 수신 대기합니다. |
    | **앱 ID URI** | demoapi | URI는 테넌트에서 API를 고유하게 식별합니다. 이 설정을 사용하면 테넌트별로 여러 API를 등록할 수 있습니다. [범위](../active-directory/develop/active-directory-dev-glossary.md#scopes)는 보호된 API 리소스에 대한 액세스를 제어하고 앱 ID URI별로 정의됩니다. |
    | **네이티브 클라이언트** | 아니오 | 이는 웹 API이지만 기본 클라이언트가 아니기 때문에 [아니요]를 선택합니다. |
    
3. **만들기**를 클릭하여 API를 등록합니다.

등록된 API는 Azure AD B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 웹 API를 선택합니다. 웹 API의 속성 창이 표시됩니다.

![웹 API 속성](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

**응용 프로그램 클라이언트 ID**를 적어 둡니다. ID는 API를 고유하게 식별하며 자습서의 뒷부분에서 API를 구성할 때 필요합니다.

Azure AD B2C를 사용하여 웹 API를 등록하면 트러스트 관계가 정의됩니다. API가 B2C에 등록되어 있으므로 이제 API는 다른 응용 프로그램에서 받은 B2C 액세스 토큰을 신뢰할 수 있습니다.

## <a name="define-and-configure-scopes"></a>범위 정의 및 구성

[범위](../active-directory/develop/active-directory-dev-glossary.md#scopes)는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

### <a name="define-scopes-for-the-web-api"></a>웹 API에 대한 범위 정의

등록된 API는 Azure AD B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 웹 API를 선택합니다. 웹 API의 속성 창이 표시됩니다.

**게시된 범위(미리 보기)** 를 클릭합니다.

웹 API에 대한 범위를 구성하려면 다음 항목을 추가합니다. 

![웹 API에 정의된 범위](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| 설정      | 제안 값  | 설명                                        |
| ------------ | ------- | -------------------------------------------------- |
| **범위** | demo.read | 데모 API에 대한 읽기 권한|

**저장**을 클릭합니다.

게시된 범위는 클라이언트 앱 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

### <a name="grant-app-permissions-to-web-api"></a>웹 API에 앱 사용 권한 부여

앱에서 보호된 웹 API를 호출하려면 앱 사용 권한을 API에 부여해야 합니다. 이 자습서에서는 [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](active-directory-b2c-tutorials-desktop-app.md)에서 만든 데스크톱 앱을 사용합니다.

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택하고, **응용 프로그램**을 클릭하여 등록된 앱 목록을 봅니다.

2. 앱 목록에서 **내 샘플 WPF 앱**을 선택하고 **API 액세스(미리 보기)**, **추가**를 차례로 클릭합니다.

3. **API 선택** 드롭다운에서 등록된 웹 API인 **내 샘플 Node.js Web API**를 선택합니다.

4. **범위 선택** 드롭다운에서 웹 API 등록에서 정의한 범위를 선택합니다.

    ![앱에 대한 범위 선택](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. **확인**을 클릭합니다.

**내 샘플 WPF 앱**은 보호되는 **내 샘플 Node.js Web API**를 호출하도록 등록되었습니다. 사용자는 Azure AD B2C로 [인증](../active-directory/develop/active-directory-dev-glossary.md#authentication)하여 WPF 데스크톱 앱을 사용합니다. 데스크톱 앱은 Azure AD B2C에서 [권한 부여](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant)를 받아 보호되는 웹 API에 액세스합니다.

## <a name="update-web-api-code"></a>웹 API 코드 업데이트

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API 코드에서 Azure AD B2C 테넌트를 사용하도록 구성해야 합니다. 이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플 Node.js 웹앱을 구성합니다. 

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Node.js Web API 샘플에서는 Passport.js 라이브러리를 사용하여 Azure AD B2C가 API 호출을 보호하도록 설정합니다. 

### <a name="configure-the-web-api"></a>Web API 구성

1. Node.js Web API 샘플에서 `index.html` 파일을 엽니다.
2. Azure AD B2C 테넌트 등록 정보를 사용하여 샘플을 구성합니다. 다음 코드 줄을 변경합니다.

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>데스크톱 앱 구성

1. Visual Studio에서 [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](active-directory-b2c-tutorials-desktop-app.md)의 `active-directory-b2c-wpf` 솔루션을 엽니다.

## <a name="run-the-sample"></a>샘플 실행

Node.js Web API 실행:

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 다음 명령을 실행합니다.
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
데스크톱 앱 실행:

1. **F5** 키를 눌러 데스크톱 앱을 실행합니다.
2. [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](active-directory-b2c-tutorials-desktop-app.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다.
3. **API 호출** 단추를 클릭합니다. 

데스크톱 앱이 웹 API에 요청을 보내고 로그인한 사용자의 표시 이름을 사용하여 응답을 가져옵니다. 보호되는 데스크톱 앱이 Azure AD B2C 테넌트에서 보호되는 웹 API를 호출합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD B2C에서 범위를 등록하고 정의하여 ASP.NET 웹 API를 보호하는 방법을 안내했습니다. 사용 가능한 Azure AD B2C 코드 샘플을 통해 자세한 내용을 알아보세요.

> [!div class="nextstepaction"]
> [Azure AD B2C 코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
