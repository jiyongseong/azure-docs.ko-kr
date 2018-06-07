---
title: 자습서 - Azure Active Directory B2C를 사용하여 웹앱에서 ASP.NET Web API로의 액세스 권한 부여 | Microsoft Docs
description: Active Directory B2C를 사용하여 ASP.NET 웹 API를 보호하고 ASP.NET 웹앱에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: f61a3b103d8738e1b86fb64aff99dab9c6986fdf
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-from-a-web-app-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 웹앱에서 ASP.NET Web API로의 액세스 권한 부여

이 자습서에서는 ASP.NET 웹앱에서 Azure AD(Azure Active Directory) B2C로 보호된 웹 API 리소스를 호출하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 웹 API 등록
> * 웹 API에 대한 범위 정의 및 구성
> * 웹 API에 앱 사용 권한 부여
> * Azure AD B2C를 사용하여 웹 API를 보호하는 샘플 코드 업데이트

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [ASP.NET 웹앱 자습서에서 Azure Active Directory B2C를 사용하여 사용자 인증](active-directory-b2c-tutorials-web-app.md)을 완료합니다.
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.

## <a name="register-web-api"></a>웹 API 등록

Azure Active Directory에서 [액세스 토큰](../active-directory/develop/active-directory-dev-glossary.md#access-token)을 제공하는 [클라이언트 응용 프로그램](../active-directory/develop/active-directory-dev-glossary.md#client-application)을 통해 [보호된 리소스 요청](../active-directory/develop/active-directory-dev-glossary.md#resource-server)을 수락하고 이에 응답하려면, 먼저 웹 API 리소스를 테넌트에 등록해야 합니다. 등록하면 [응용 프로그램 및 서비스 사용자 개체](../active-directory/develop/active-directory-dev-glossary.md#application-object)가 테넌트에 설정됩니다. 

Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택합니다.

2. B2C 설정에서 **응용 프로그램**, **추가**를 차례로 클릭합니다.

    샘플 웹 API를 테넌트에 등록하려면 다음 설정을 사용합니다.
    
    ![새 API 추가](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 내 샘플 웹 API | 개발자에게 웹 API를 설명하는 **이름**을 입력합니다. |
    | **웹앱/웹 API 포함** | 예 | 웹 API에 **예**를 선택합니다. |
    | **암시적 흐름 허용** | 예 | API에서 [OpenID Connect 로그인](active-directory-b2c-reference-oidc.md)을 사용하므로 **예**를 선택합니다. |
    | **회신 URL** | `https://localhost:44332` | 회신 URL은 Azure AD B2C에서 API가 요청한 토큰을 반환하는 엔드포인트입니다. 이 자습서에서는 샘플 웹 API가 로컬(로컬 호스트)에서 실행되고 44332 포트에서 수신 대기합니다. |
    | **앱 ID URI** | myAPISample | URI는 테넌트에서 API를 고유하게 식별합니다. 이 설정을 사용하면 테넌트별로 여러 API를 등록할 수 있습니다. [범위](../active-directory/develop/active-directory-dev-glossary.md#scopes)는 보호된 API 리소스에 대한 액세스를 제어하고 앱 ID URI별로 정의됩니다. |
    | **네이티브 클라이언트** | 아니요 | 이는 웹 API이지만 기본 클라이언트가 아니기 때문에 [아니요]를 선택합니다. |
    
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
| **범위** | Hello.Read | Hello에 대한 읽기 액세스 |
| **범위** | Hello.Write | Hello에 대한 쓰기 액세스 |

**저장**을 클릭합니다.

게시된 범위는 클라이언트 앱 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

### <a name="grant-app-permissions-to-web-api"></a>웹 API에 앱 사용 권한 부여

앱에서 보호된 웹 API를 호출하려면 앱 사용 권한을 API에 부여해야 합니다. 이 자습서에서는 [ASP.NET 웹앱 자습서에서 Azure Active Directory B2C를 사용하여 사용자 인증](active-directory-b2c-tutorials-web-app.md)에서 만든 웹앱을 사용합니다. 

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택하고, **응용 프로그램**을 클릭하여 등록된 앱 목록을 봅니다.

2. 앱 목록에서 **내 샘플 웹앱**을 선택하고, **API 액세스(미리 보기)**, **추가**를 차례로 클릭합니다.

3. **API 선택** 드롭다운에서 등록된 웹 API인 **내 샘플 웹 API**를 선택합니다.

4. **범위 선택** 드롭다운에서 웹 API 등록에서 정의한 범위를 선택합니다.

    ![앱에 대한 범위 선택](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. **확인**을 클릭합니다.

**내 샘플 웹앱**이 보호된 **내 샘플 웹 API**를 호출하도록 등록되었습니다. 사용자가 Azure AD B2C로 [인증](../active-directory/develop/active-directory-dev-glossary.md#authentication)하여 웹앱을 사용합니다. 웹앱은 Azure AD B2C에서 [권한 부여](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant)를 받아 보호된 웹 API에 액세스합니다.

## <a name="update-code"></a>코드 업데이트

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API 코드에서 Azure AD B2C 테넌트를 사용하도록 구성해야 합니다. 이 자습서에서는 샘플 웹 API를 구성합니다. 

샘플 웹 API는 필수 구성 요소 자습서([ASP.NET 웹앱 자습서에서 Azure Active Directory B2C를 사용하여 사용자 인증](active-directory-b2c-tutorials-web-app.md))에서 다운로드한 프로젝트에 포함되어 있습니다. 이 필수 구성 요소 자습서를 완료하지 않은 경우 먼저 이를 완료한 후에 계속합니다.

샘플 솔루션에는 두 개의 프로젝트가 있습니다.

**웹앱 샘플 앱(TaskWebApp):** 작업 목록을 만들고 편집하는 웹앱입니다. 웹앱에서 **등록 또는 로그인** 정책을 사용하여 이메일 주소로 사용자를 등록하거나 로그인합니다.

**웹 API 샘플 앱(TaskService):** 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원하는 웹 API입니다. 웹 API는 Azure AD B2C를 통해 보호되고 웹앱에서 호출됩니다.

샘플 웹앱 및 웹 API는 각 프로젝트의 Web.config 파일에서 구성 값을 앱 설정으로 정의합니다.

Visual Studio에서 **B2C-WebAPI-DotNet** 솔루션을 엽니다.

### <a name="configure-the-web-app"></a>웹앱 구성

1. **TaskWebApp** 프로젝트에서 **Web.config**를 엽니다.

2. API를 로컬로 실행하려면 **api:TaskServiceUrl**에 대한 localhost 설정을 사용합니다. Web.config를 다음과 같이 변경합니다. 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. API의 URI를 구성합니다. 이는 웹앱에서 API를 요청하는 데 사용하는 URI입니다. 또한 요청된 권한도 구성합니다.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Web API 구성

1. **TaskService** 프로젝트에서 **Web.config**를 엽니다.

2. 테넌트를 사용하도록 API를 구성합니다.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. 클라이언트 ID를 API에 대해 등록된 응용 프로그램 ID로 설정합니다.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. 등록 및 로그인 정책을 만들 때 생성된 이름으로 정책 설정을 업데이트합니다.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_SiUpIn" />
    ```

5. 포털에서 만든 범위와 일치하도록 범위 설정을 구성합니다.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>샘플 실행

**TaskWebApp** 및 **TaskService** 프로젝트를 모두 실행해야 합니다. 

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. 
2. **여러 시작 프로젝트** 라디오 단추를 선택합니다.
3. **시작**할 두 프로젝트에 대한 **작업**을 변경합니다.
4. [확인]을 클릭하여 구성을 저장합니다.
5. **F5** 키를 눌러 두 응용 프로그램을 모두 실행합니다. 각 응용 프로그램은 자체 브라우저 탭에서 열립니다. `https://localhost:44316/`은 웹앱이고,
    `https://localhost:44332/`는 웹 API입니다.

6. 웹앱의 메뉴 배너에서 등록/로그인 링크를 클릭하여 웹 응용 프로그램에 등록합니다. [웹앱 프로그램 자습서](active-directory-b2c-tutorials-web-app.md)에서 만든 계정을 사용합니다. 
7. 로그인한 후 **할 일 목록** 링크를 클릭하고 할 일 목록 항목을 만듭니다.

할 일 목록 항목을 만들면 웹앱에서 할 일 목록 항목을 생성하는 웹 API를 요청합니다. 보호된 웹앱은 Azure AD B2C 테넌트에서 보호된 웹 API를 호출합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD B2C에서 범위를 등록하고 정의하여 ASP.NET 웹 API를 보호하는 방법을 안내했습니다. 코드 연습을 포함하여 이 시나리오를 개발하는 방법에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 등록, 로그인, 프로필 편집 및 암호 재설정을 사용하여 ASP.NET 웹앱 만들기](active-directory-b2c-devquickstarts-web-dotnet-susi.md)