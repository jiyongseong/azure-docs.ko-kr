---
title: 빠른 시작 - Azure Active Directory B2C를 사용하여 ASP.NET 응용 프로그램에 대한 로그인 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 계정 로그인을 제공하는 샘플 ASP.NET 웹앱을 실행합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: quickstart
ms.custom: mvc
ms.date: 2/13/2018
ms.author: davidmu
ms.openlocfilehash: 4342e8c58c9bb20580d8428a6c9869f9a3b893cb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>빠른 시작 - Azure Active Directory B2C를 사용하여 ASP.NET 응용 프로그램에 대한 로그인 설정

Azure AD(Azure Active Directory) B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다. Azure AD B2C를 사용하면 앱에서 개방형 표준 프로토콜을 사용하여 소셜 계정 및 엔터프라이즈 계정을 인증할 수 있습니다.

이 빠른 시작에서는 Azure AD B2C 사용 샘플 ASP.NET 앱에서 소셜 ID 공급자를 사용하여 로그인하고 Azure AD B2C 보호 웹 API를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 
* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다.

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="run-the-app-in-visual-studio"></a>Visual Studio에서 앱 실행

샘플 응용 프로그램 프로젝트 폴더에서 Visual Studio를 통해 `B2C-WebAPI-DotNet.sln` 솔루션을 엽니다.

샘플 솔루션에는 두 개의 프로젝트가 있습니다.

**웹앱 샘플 앱(TaskWebApp):** 작업 목록을 만들고 편집하는 웹앱입니다. 웹앱에서 **등록 또는 로그인** 정책을 사용하여 사용자를 등록하거나 로그인합니다.

**Web API 샘플 앱(TaskService):** 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원하는 Web API입니다. 웹 API는 Azure AD B2C를 통해 보호되고 웹앱에서 호출됩니다.

이 빠른 시작에서 동시에 `TaskWebApp` 및 `TaskService` 프로젝트를 둘 다 실행합니다. 

1. 솔루션 탐색기에서 `B2C-WebAPI-DotNet` 솔루션을 선택합니다.
2. Visual Studio 메뉴에서 **프로젝트 > 시작 프로젝트 설정...** 을 차례로 선택합니다. 
3. **여러 시작 프로젝트** 라디오 단추를 선택합니다.
4. **시작**할 두 프로젝트에 대한 **작업**을 변경합니다. **확인**을 클릭합니다.

**F5** 키를 눌러 두 응용 프로그램을 모두 디버깅합니다. 각 응용 프로그램은 고유한 브라우저 탭에서 열립니다.

`https://localhost:44316/` - 이 페이지는 ASP.NET 웹 응용 프로그램입니다. 빠른 시작에서 이 응용 프로그램과 직접 상호 작용합니다.
`https://localhost:44332/` - 이 페이지는 ASP.NET 웹 응용 프로그램에 의해 호출되는 Web API입니다.

## <a name="create-an-account"></a>계정 만들기

ASP.NET 웹 응용 프로그램에서 **등록/로그인** 링크를 클릭하여 Azure AD B2C 정책에 따라 **등록 또는 로그인** 워크플로를 시작합니다.

![샘플 ASP.NET 웹앱](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

이 샘플에서는 소셜 ID 공급자를 사용하거나 이메일 주소를 사용하여 로컬 계정을 만드는 등의 여러 가지 등록 옵션을 지원합니다. 이 빠른 시작에서는 Facebook, Google, Microsoft 또는 Twitter의 소셜 ID 공급자 계정을 사용합니다. 

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

Azure AD B2C는 샘플 웹앱용으로 Wingtip Toys라는 가상의 브랜드에 대한 사용자 지정 로그인 페이지를 제공합니다. 

1. 소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다.

    ![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램이 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

2. ID 공급자에 대한 로그인 프로세스를 완료합니다. 예를 들어 Twitter를 선택하면 Twitter 자격 증명을 입력하고 **로그인**을 클릭합니다.

    ![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

    새 Azure AD B2C 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워져 있습니다.

3. 표시 이름, 직책 및 구/군/시 필드를 업데이트하고 **계속**을 클릭합니다.  입력하는 값은 Azure AD B2C 사용자 계정 프로필에 사용됩니다.

    ![새 계정 등록 프로필 세부 정보](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

    Azure AD B2C 정책을 사용하는 샘플 웹앱에서 ID 공급자를 사용하여 인증하고 Azure AD B2C 사용자 계정을 만들었습니다. 

## <a name="edit-your-profile"></a>프로필 편집

Azure Active Directory B2C에는 사용자가 프로필을 업데이트할 수 있는 기능이 있습니다. 샘플 웹앱은 워크플로에 Azure AD B2C 프로필 편집 정책을 사용합니다. 

1. 웹 응용 프로그램 메뉴 모음에서 프로필 이름을 클릭하고 **프로필 편집**을 선택하여 만든 프로필을 편집합니다.

    ![프로필 편집](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. **표시 이름** 및 **구/군/시**를 변경합니다.  
3. **계속**을 클릭하여 프로필을 업데이트합니다. 새 표시 이름은 웹앱 홈 페이지의 오른쪽 위 부분에 표시됩니다.

## <a name="access-a-protected-web-api-resource"></a>보호된 웹 API 리소스 액세스

1. **할 일 목록**을 클릭하여 할 일 목록 항목을 입력하고 수정합니다. 

2. **새 항목** 텍스트 상자에 텍스트를 입력합니다. **추가**를 클릭하여 할 일 목록 항목을 추가하는 Azure AD B2C 보호 Web API를 호출합니다.

    ![할 일 목록 항목 추가](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET 웹 응용 프로그램에는 사용자의 할 일 목록 항목에 대한 작업을 수행하기 위해 보호된 웹 API 리소스에 대한 요청에 Azure AD 액세스 토큰을 포함합니다.

Azure AD B2C 사용자 계정을 사용하여 Azure AD B2C 보안 웹 API에 대한 권한이 있는 호출을 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 빠른 시작 또는 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure AD B2C 사용 샘플 ASP.NET 앱을 사용하여 사용자 지정 로그인 페이지에 로그인하고, 소셜 ID 공급자에 로그인하고, Azure AD B2C 계정을 만들고, Azure AD B2C로 보호되는 웹 API를 호출했습니다. 

자습서를 계속 진행하여 자신만의 Azure AD B2C 테넌트를 사용하도록 샘플 ASP.NET을 구성하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: ASP.NET 웹앱에서 Azure Active Directory B2C를 사용하여 사용자 인증](active-directory-b2c-tutorials-web-app.md)