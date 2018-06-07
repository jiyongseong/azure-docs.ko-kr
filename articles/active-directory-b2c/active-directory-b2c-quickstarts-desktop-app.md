---
title: 빠른 시작 - Azure Active Directory B2C를 사용하여 데스크톱 앱에 대한 로그인 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 계정 로그인을 제공하는 샘플 ASP.NET 데스크톱 응용 프로그램을 실행합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 2/13/2018
ms.author: davidmu
ms.openlocfilehash: d0e50d0a81a3230558da42ab6678c3c354ce0e01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>빠른 시작 - Azure Active Directory B2C를 사용하여 데스크톱 앱에 대한 로그인 설정 

Azure AD(Azure Active Directory) B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다. Azure AD B2C를 사용하면 앱에서 개방형 표준 프로토콜을 사용하여 소셜 계정 및 엔터프라이즈 계정을 인증할 수 있습니다.

이 빠른 시작에서는 Azure AD B2C 사용 샘플 WPF(Windows Presentation Foundation) 데스크톱 앱에서 소셜 ID 공급자를 사용하여 로그인하고 Azure AD B2C 보호 웹 API를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 
* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다.

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

## <a name="run-the-app-in-visual-studio"></a>Visual Studio에서 앱 실행

샘플 응용 프로그램 프로젝트 폴더에서 Visual Studio를 통해 `active-directory-b2c-wpf.sln` 솔루션을 엽니다.

**F5** 키를 눌러 응용 프로그램을 디버그합니다.

## <a name="create-an-account"></a>계정 만들기

**로그인**을 클릭하여 Azure AD B2C 정책을 기반으로 **등록 또는 로그인** 워크플로를 시작합니다.

![샘플 응용 프로그램](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

이 샘플에서는 소셜 ID 공급자를 사용하거나 이메일 주소를 사용하여 로컬 계정을 만드는 등의 여러 가지 등록 옵션을 지원합니다. 이 빠른 시작에서는 Facebook, Google, Microsoft 또는 Twitter의 소셜 ID 공급자 계정을 사용합니다. 

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

Azure AD B2C는 샘플 웹앱용으로 Wingtip Toys라는 가상의 브랜드에 대한 사용자 지정 로그인 페이지를 제공합니다. 

1. 소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다. 

    ![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램이 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

2. ID 공급자에 대한 로그인 프로세스를 완료합니다. 예를 들어 Twitter를 선택하면 Twitter 자격 증명을 입력하고 **로그인**을 클릭합니다.

    ![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

    새 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워집니다. 

3. 원할 경우 세부 정보를 수정하고 **계속**을 클릭합니다. 입력하는 값은 Azure AD B2C 사용자 계정 프로필에 사용됩니다.

    ![새 계정 등록 프로필 세부 정보](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

    ID 공급자를 사용하는 새 Azure AD B2C 사용자 계정을 성공적으로 만들었습니다. 로그인하면 *토큰 정보* 입력란에 액세스 토큰이 표시됩니다. 액세스 토큰은 API 리소스에 액세스할 때 사용됩니다.

## <a name="edit-your-profile"></a>프로필 편집

Azure Active Directory B2C에는 사용자가 프로필을 업데이트할 수 있는 기능이 있습니다.  샘플 웹앱은 워크플로에 Azure AD B2C 프로필 편집 정책을 사용합니다. 

1. **프로필 편집**을 클릭하여 만든 프로필을 편집합니다.

    ![프로필 편집](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. 만든 계정과 연결된 ID 공급자를 선택합니다. 예를 들어 계정을 만들 때 ID 공급자로 Twitter를 사용한 경우 연결된 프로필 세부 정보를 수정하려면 Twitter를 선택합니다.

3. **표시 이름** 또는 **구/군/시**를 변경하고 **계속**을 클릭합니다.

    새 액세스 토큰이 *토큰 정보* 텍스트 상자에 표시됩니다. 프로필의 변경 내용을 확인하려는 경우 액세스 토큰을 복사한 후 토큰 디코더 https://jwt.ms에 붙여넣습니다.

## <a name="access-a-protected-web-api-resource"></a>보호된 웹 API 리소스 액세스

**API 호출**을 클릭하여 Azure AD B2C 보호된 리소스에 대한 요청을 만듭니다. 

![API 호출](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

응용 프로그램에는 보호된 웹 API 리소스에 대한 요청에 Azure AD 액세스 토큰이 포함됩니다. 웹 API는 액세스 토큰에 포함된 표시 이름을 다시 전송합니다.

Azure AD B2C 사용자 계정을 사용하여 Azure AD B2C 보안 웹 API에 대한 권한이 있는 호출을 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 빠른 시작 또는 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 자체 Azure AD B2C 테넌트를 만들고 해당 테넌트를 사용하여 실행되도록 샘플을 구성하는 것입니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](active-directory-b2c-get-started.md)
