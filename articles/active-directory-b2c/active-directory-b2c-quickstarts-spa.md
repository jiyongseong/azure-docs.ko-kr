---
title: 빠른 시작 - Azure Active Directory B2C를 사용하여 단일 페이지 앱에 대한 로그인 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 계정 로그인을 제공하는 샘플 단일 페이지 응용 프로그램을 실행합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 2/13/2018
ms.author: davidmu
ms.openlocfilehash: 96baf0297c3d2976f3d6fb112b0bd0ce077a186f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>빠른 시작: Azure Active Directory B2C를 사용하여 단일 페이지 앱에 대한 로그인 설정 

Azure AD(Azure Active Directory) B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다. Azure AD B2C를 사용하면 앱에서 개방형 표준 프로토콜을 사용하여 소셜 계정 및 엔터프라이즈 계정을 인증할 수 있습니다.

이 빠른 시작에서는 Azure AD B2C 사용 샘플 단일 페이지 앱에서 소셜 ID 공급자를 사용하여 로그인하고 Azure AD B2C 보호 웹 API를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [Node.js](https://nodejs.org/en/download/)
* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다.

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="run-the-sample-application"></a>샘플 응용 프로그램 실행

Node.js 명령 프롬프트에서 이 샘플을 실행하려면 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

Node.js 앱은 localhost에서 수신 대기 중인 포트 번호를 출력합니다.

```
Listening on port 6420...
```

웹 브라우저에서 앱의 URL인 `http://localhost:6420`으로 이동합니다.

![브라우저의 샘플 앱](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>계정 만들기

**로그인** 단추를 클릭하여 Azure AD B2C 정책을 기반으로 Azure AD B2C **등록 또는 로그인** 워크플로를 시작합니다. 

이 샘플에서는 소셜 ID 공급자를 사용하거나 이메일 주소를 사용하여 로컬 계정을 만드는 등의 여러 가지 등록 옵션을 지원합니다. 이 빠른 시작에서는 Facebook, Google, Microsoft 또는 Twitter의 소셜 ID 공급자 계정을 사용합니다. 

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

Azure AD B2C는 샘플 웹앱용으로 Wingtip Toys라는 가상의 브랜드에 대한 사용자 지정 로그인 페이지를 제공합니다. 

1. 소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다.

    ![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램이 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

2. ID 공급자에 대한 로그인 프로세스를 완료합니다. 예를 들어 Twitter를 선택하면 Twitter 자격 증명을 입력하고 **로그인**을 클릭합니다.

    ![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

    새 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워집니다. 

3. 표시 이름, 직책 및 구/군/시 필드를 업데이트하고 **계속**을 클릭합니다.  입력하는 값은 Azure AD B2C 사용자 계정 프로필에 사용됩니다.

    ID 공급자를 사용하는 새 Azure AD B2C 사용자 계정을 성공적으로 만들었습니다. 

## <a name="access-a-protected-web-api-resource"></a>보호된 웹 API 리소스 액세스

**웹 API 호출** 단추를 클릭하여 웹 API 호출에서 반환된 표시 이름을 JSON 개체로 지정합니다. 

![웹 API 응답](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

샘플 단일 페이지 앱에는 JSON 개체를 반환하는 하는 작업을 수행하기 위해 보호된 웹 API 리소스에 대한 요청에 Azure AD 액세스 토큰이 포함됩니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 빠른 시작 또는 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure AD B2C 사용 샘플 ASP.NET 앱을 사용하여 사용자 지정 로그인 페이지에 로그인하고, 소셜 ID 공급자에 로그인하고, Azure AD B2C 계정을 만들고, Azure AD B2C로 보호되는 웹 API를 호출했습니다. 

다음 단계는 자체 Azure AD B2C 테넌트를 만들고 해당 테넌트를 사용하여 실행되도록 샘플을 구성하는 것입니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](active-directory-b2c-get-started.md)