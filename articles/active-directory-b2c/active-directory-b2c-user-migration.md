---
title: 'Azure Active Directory B2C: 사용자 마이그레이션 방법'
description: Graph API를 사용하고 필요에 따라 Azure AD B2C 사용자 지정 정책을 사용하여 사용자 마이그레이션에 대한 주요 고급 개념을 설명합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.openlocfilehash: 47f813839a5495591356e5ecd461902fa8745c65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140328"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: 사용자 마이그레이션
ID 공급자를 Azure AD B2C(Azure Active Directory B2C)로 마이그레이션할 때 사용자 계정도 마이그레이션해야 할 수 있습니다. 이 문서에서는 기존 사용자 계정을 ID 공급자에서 Azure AD B2C로 마이그레이션하는 방법을 설명합니다. 이 문서에서는 규범적인 내용이 아니라 몇 가지 시나리오를 설명하려고 합니다. 개발자는 각 방식의 적합성에 대한 책임이 있습니다.

## <a name="user-migration-flows"></a>사용자 마이그레이션 흐름
Azure AD B2C를 사용하면 [Azure AD Graph API][B2C-GraphQuickStart]를 통해 사용자를 마이그레이션할 수 있습니다. 사용자 마이그레이션 프로세스는 두 개의 흐름으로 나누어 집니다.

* **사전 마이그레이션**: 이 흐름은 사용자의 자격 증명(사용자 이름 및 암호)에 대한 명확한 액세스 권한이 있거나 자격 증명이 암호화되어 있지만 암호를 해독할 수 있는 경우에 적용됩니다. 사전 마이그레이션 프로세스에는 이전 ID 공급자의 사용자를 읽고 Azure AD B2C 디렉터리에 새 계정을 만드는 작업이 포함됩니다.

* **사전 마이그레이션 및 암호 재설정** - 이 흐름은 사용자의 암호에 액세스할 수 없을 경우에 적용됩니다. 예: 
    * 암호는 해시 형식으로 저장됩니다.
    * 암호는 액세스할 수 없는 ID 공급자에 저장됩니다. 이전 ID 공급자는 웹 서비스를 호출하여 사용자 자격 증명의 유효성을 검사합니다.

두 흐름에서 처음으로 사전 마이그레이션 프로세스를 실행하고, 이전 ID 공급자의 사용자를 읽고, Azure AD B2C 디렉터리에 새 계정을 만듭니다. 암호가 없는 경우 임의로 생성된 암호를 사용하여 계정을 만듭니다. 그런 다음 사용자에게 암호 변경을 요청하거나 사용자가 처음 로그인 할 때 Azure AD B2C에서 사용자에게 암호를 다시 설정하도록 요청합니다.

## <a name="password-policy"></a>암호 정책
(로컬 계정의) Azure AD B2C 암호 정책은 Azure AD 정책을 기반으로 합니다. Azure AD B2C 등록 또는 로그인 및 암호 재설정 정책은 "강력한" 암호 강도를 사용하며 어떠한 암호도 만료되지 않습니다. 자세한 내용은 [Azure AD 암호 정책][AD-PasswordPolicies]을 참조하세요.

마이그레이션하려는 계정이 [Azure AD B2C에 의해 적용되는 강력한 암호 강도][AD-PasswordPolicies]보다 약한 암호 강도를 사용하는 경우 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경하려면 `passwordPolicies` 속성을 `DisableStrongPassword`로 설정합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1단계: Azure AD Graph API를 사용하여 사용자 마이그레이션
(암호 또는 임의의 암호를 사용하여) Graph API를 통해 Azure AD B2C 사용자 계정을 만듭니다. 이 섹션에서는 Graph API를 사용하여 Azure AD B2C 디렉터리에서 사용자 계정을 만드는 프로세스를 설명합니다.

### <a name="step-11-register-your-application-in-your-tenant"></a>1.1단계: 테넌트에서 응용 프로그램 등록
Graph API와 통신하려면 먼저 관리자 권한이 있는 서비스 계정이 있어야 합니다. Azure AD에서 Azure AD에 응용 프로그램 및 인증을 등록합니다. 응용 프로그램 자격 증명은 **응용 프로그램 ID** 및 **응용 프로그램 비밀**입니다. 응용 프로그램은 사용자로서가 아닌 자체로서 Graph API를 호출합니다.

먼저 Azure AD에서 마이그레이션 응용 프로그램을 등록합니다. 그런 다음 응용 프로그램 키(응용 프로그램 비밀)를 만들고 쓰기 권한으로 응용 프로그램을 설정합니다.

1. [Azure Portal][Portal]에 로그인합니다.

2. 창의 오른쪽 위에서 계정을 선택하여 Azure AD **B2C** 테넌트를 선택합니다.

3. 왼쪽 창에서 **Azure Active Directory**(Azure AD B2C 아님)를 선택합니다. 찾으려면 **추가 서비스**를 선택해야 합니다.

4. **앱 등록**을 선택합니다.

5. **새 응용 프로그램 등록**을 선택합니다.

    ![새 응용 프로그램 등록](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. 다음을 수행하여 새 응용 프로그램을 만듭니다.
    * **이름**에서 **B2CUserMigratioin** 또는 원하는 다른 이름을 사용합니다.
    * **응용 프로그램 종류**에는 **웹앱/API**를 사용합니다.
    * **로그온 URL**에서 **https://localhost**(이 응용 프로그램에 관련되지 않기 때문에)를 사용합니다.
    * **만들기**를 선택합니다.

7. 응용 프로그램을 만든 후 **응용 프로그램** 목록에서 새로 만든 **B2CUserMigration** 응용 프로그램을 선택합니다.

8. **속성**을 선택하고 **응용 프로그램 ID**를 복사하여 나중에 사용할 수 있도록 저장해 둡니다.

### <a name="step-12-create-the-application-secret"></a>1.2단계: 응용 프로그램 비밀 만들기
1. Azure Portal **등록된 앱** 창에서 **키**를 선택합니다.

2. 새 키(클라이언트 비밀이라고도 함)를 추가한 다음 나중에 사용하기 위해 키를 복사합니다.

    ![응용 프로그램 ID 및 키](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>1.3단계 응용 프로그램에 관리 권한 부여
1. Azure Portal **등록된 앱** 창에서 **필요한 권한**을 선택합니다.

2. **Windows Azure Active Directory**를 선택합니다.

3. **액세스 사용** 창의 **응용 프로그램 권한** 아래에서 **디렉터리 데이터 읽기 및 쓰기**를 선택한 다음 **저장**을 선택합니다.

4. **필요한 권한** 창에서 **권한 부여**를 선택합니다.

    ![응용 프로그램 사용 권한](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

이제 Azure AD B2C 테넌트에서 사용자를 만들고, 읽고, 업데이트할 수 있는 권한이 있는 응용 프로그램이 있습니다.

### <a name="step-14-optional-environment-cleanup"></a>1.4단계: (선택 사항) 환경 정리
디렉터리 데이터 읽기 및 쓰기 권한에는 사용자를 삭제하는 권한이 포함되지 *않습니다*. 응용 프로그램에 사용자를 삭제할 수 있는 권한을 부여하려면(환경을 정리하기 위해) PowerShell을 실행하여 사용자 계정 관리자 권한을 설정해야 하는 추가 단계를 수행해야 합니다. 그렇지 않으면 다음 섹션으로 건너뛸 수 있습니다.

> [!IMPORTANT]
> B2C 테넌트에 대해 *로컬*인 B2C 테넌트 Administrator 계정을 사용해야 합니다. 계정 이름 구문은 *admin@contosob2c.onmicrosoft.com*입니다.

>[!NOTE]
> 다음 PowerShell 스크립트에는 [Azure Active Directory PowerShell 버전 2][AD-Powershell]가 필요합니다.

PowerShell 스크립트에서 다음을 수행합니다.
1. 온라인 서비스에 연결 이렇게 하려면 Windows PowerShell 명령 프롬프트에서 `Connect-AzureAD` cmdlet을 실행하고 자격 증명을 제공합니다.

2. **응용 프로그램 ID**를 사용하여 응용 프로그램에 사용자 계정 관리자 역할을 할당할 수 있습니다. 이러한 역할은 잘 알려진 식별자로서 스크립트에 **응용 프로그램 ID**를 입력하기만 하면 됩니다.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

`$AppId` 값을 Azure AD **응용 프로그램 ID**로 변경합니다.

## <a name="step-2-pre-migration-application-sample"></a>2단계: 사전 마이그레이션 응용 프로그램 샘플
[샘플 코드를 다운로드하고 실행합니다][UserMigrationSample]. .zip 파일로 다운로드할 수 있습니다.

### <a name="step-21-edit-the-migration-data-file"></a>2.1단계: 마이그레이션 데이터 파일 편집
샘플 앱은 더미 사용자 데이터가 포함된 JSON 파일을 사용합니다. 샘플을 성공적으로 실행한 후에 자체 데이터베이스의 데이터를 사용하도록 코드를 변경합니다. 또는 사용자 프로필을 JSON 파일로 내보낸 다음 앱이 해당 파일을 사용하도록 설정합니다.

JSON 파일을 편집하려면 `AADB2C.UserMigration.sln` Visual Studio 솔루션을 엽니다. `AADB2C.UserMigration` 프로젝트에서 `UsersData.json` 파일을 엽니다.

![사용자 데이터 파일](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

보이는 것 처럼 이 파일에는 사용자 엔터티 목록이 포함됩니다. 각 사용자 엔터티에는 다음과 같은 속성이 있습니다.
* email
* displayName
* firstname
* Lastname
* 암호(비워 둘 수 있음)

> [!NOTE]
> 컴파일 시 Visual Studio는 파일을 `bin` 디렉터리로 복사합니다.

### <a name="step-22-configure-the-application-settings"></a>2.2단계: 응용 프로그램 설정 구성
`AADB2C.UserMigration` 프로젝트에서 *App.config* 파일을 엽니다. 다음과 같은 앱 설정을 고유한 값으로 바꿉니다.

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> * Azure 테이블 연결 문자열을 사용하는 방법은 다음 섹션에 설명되어 있습니다.
> * B2C 테넌트 이름은 테넌트를 만들 때 입력한 도메인이며, Azure Portal에 표시됩니다. 일반적으로 테넌트 이름은 접미사 *.onmicrosoft.com*으로 끝납니다(예: *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>2.3단계: 사전 마이그레이션 프로세스 실행
`AADB2C.UserMigration` 솔루션을 마우스 오른쪽 단추로 클릭한 다음 샘플을 다시 빌드합니다. 성공하는 경우 `UserMigration.exe` 실행 파일이 `AADB2C.UserMigration\bin\Debug\net461`에 있어야 합니다. 마이그레이션 프로세스를 실행하려면 다음 명령줄 매개 변수 중 하나를 사용합니다.

* **암호로 사용자를 마이그레이션**하려면 `UserMigration.exe 1` 명령을 사용합니다.

* **임의 암호로 사용자를 마이그레이션**하려면 `UserMigration.exe 2` 명령을 사용합니다. 또한 이 작업에서는 Azure 테이블 엔터티를 만듭니다. 나중에 서비스 REST API 서비스를 호출하도록 정책을 구성합니다. 서비스는 Azure 테이블을 사용하여 마이그레이션 프로세스를 추적하고 관리합니다.

![마이그레이션 프로세스 데모](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>2.4단계: 사전 마이그레이션 프로세스 확인
마이그레이션의 유효성을 검사하려면 다음 두 가지 방법 중 하나를 사용합니다.

* 사용자를 표시 이름으로 검색하려면 Azure Portal을 사용합니다.

    a. **Azure AD B2C**를 열고 **사용자 및 그룹**을 선택합니다.

    나. 검색 상자에 사용자의 표시 이름을 입력한 다음 사용자 프로필을 확인합니다.

* 로그인 이메일 주소로 사용자를 검색하려면 이 응용 프로그램 예제를 사용합니다.

    a. 다음 명령 실행:

    ```Console
        UserMigration.exe 3 {email address}
    ```

    > [!TIP]
    > 다음 명령을 사용하여 표시 이름으로 사용자를 검색할 수도 있습니다. `UserMigration.exe 4 "<Display name>"`

    나. JSON 편집기에서 UserProfile.json 파일을 열어 사용자의 정보를 봅니다.

    ![UserProfile.json 파일](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>2.5단계: (선택 사항) 환경 정리
Azure AD 테넌트를 정리하고 Azure AD 디렉터리에서 사용자를 제거하려면 `UserMigration.exe 5` 명령을 실행합니다.

> [!NOTE]
> * 테넌트를 정리하려면 응용 프로그램에 대한 사용자 계정 관리자 권한을 구성합니다.
> * 샘플 마이그레이션 앱은 JSON 파일에 나열된 모든 사용자를 정리합니다.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>2.6단계: 마이그레이션된 사용자로 로그인(암호 사용)
사용자 암호를 사용하여 사전 마이그레이션 프로세스를 실행하고 나면 계정을 사용할 준비가 되고 사용자는 Azure AD B2C를 사용하여 응용 프로그램에 로그인할 수 있습니다. 사용자 암호에 액세스할 수 없는 경우 다음 섹션을 진행합니다.

## <a name="step-3-help-users-reset-their-password"></a>3단계: 사용자가 암호를 다시 설정하도록 돕기
임의의 암호로 사용자를 마이그레이션하는 경우 사용자는 자신의 암호를 다시 설정해야 합니다. 암호를 다시 설정하도록 도우려면 링크가 있는 환영 전자 메일을 보내서 암호를 재설정하도록 합니다.

암호 재설정 정책에 대한 링크를 가져오려면 다음을 수행합니다.

1. **Azure AD B2C 설정**을 선택한 다음 **암호 다시 설정** 정책 속성을 선택합니다.

2. 응용 프로그램을 선택합니다.

    > [!NOTE]
    > 지금 실행을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 응용 프로그램을 등록하는 방법은 Azure AD B2C [시작][B2C-GetStarted] 문서 또는 [응용 프로그램 등록][B2C-AppRegister] 문서를 참조하세요.

3. **지금 실행**을 선택한 다음 정책을 확인합니다.

4. **지금 실행 끝점** 상자에서 URL을 복사한 다음 사용자에게 보냅니다.

    ![진단 로그 설정](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4단계: (선택 사항) 정책을 변경하여 사용자 마이그레이션 상태 확인 및 설정

> [!NOTE]
> 사용자 마이그레이션 상태를 확인하고 변경하려면 사용자 지정 정책을 사용해야 합니다. [사용자 지정 정책 시작][B2C-GetStartedCustom]에서 설정 지침을 완료해야 합니다.
>

사용자가 먼저 암호를 재설정하지 않고 로그인하려고 하면 정책은 친절한 오류 메시지를 반환해야 합니다. 예: 
>*암호가 만료되었습니다. 다시 설정하려면 암호 다시 설정 링크를 선택하세요.* 

이 선택적 단계에서는 [사용자 지정 정책 시작][B2C-GetStartedCustom] 문서에 설명된 대로 Azure AD B2C 사용자 지정 정책을 사용해야 합니다.

이 섹션에서는 로그인 시 사용자 마이그레이션 상태를 확인하도록 정책을 변경합니다. 사용자가 암호를 변경하지 않은 경우 사용자에게 **암호를 잊어 버렸나요?** 링크를 선택하라는 HTTP 409 오류 메시지를 반환합니다.

암호 변경을 추적하려면 Azure 테이블을 사용합니다. 명령줄 매개 변수 `2`로 사전 마이그레이션 프로세스를 실행할 때 Azure 테이블에 사용자 엔터티를 만듭니다. 서비스는 다음을 수행합니다.

* 로그인 시 Azure AD B2C 정책은 마이그레이션 RESTful 서비스를 호출하여 전자 메일 메시지를 입력 클레임으로 보냅니다. 서비스는 Azure 테이블에서 이메일 주소를 검색합니다. 주소가 존재하면 서비스는 *암호를 변경해야 합니다.* 라는 오류 메시지를 throw합니다.

* 사용자가 암호를 성공적으로 변경한 후에 해당 엔터티를 Azure 테이블에서 제거합니다.

>[!NOTE]
>Azure 테이블을 사용하여 이 샘플을 간소화합니다. Azure AD B2C 계정의 사용자 지정 속성으로 또는 원하는 데이터베이스에 마이그레이션 상태를 저장할 수 있습니다.

### <a name="41-update-your-application-setting"></a>4.1: 응용 프로그램 설정 업데이트
1. RESTful API 데모를 테스트하려면 Visual Studio에서 `AADB2C.UserMigration.sln`을 엽니다.

2. `AADB2C.UserMigration.API` 프로젝트에서 *appsettings.json* 파일을 엽니다. 설정을 [2.2단계](#step-22-configure-the-application-settings)에서 구성된 것으로 바꿉니다.

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>4.2단계: Azure App Services에 웹 응용 프로그램 배포
솔루션 탐색기에서 `AADB2C.UserMigration.API`를 마우스 오른쪽 단추로 클릭하고, "게시..."를 선택합니다. 지침을 따라 Azure App Service에 게시합니다. 자세한 내용은 [Azure App Service에 앱 배포][AppService-Deploy]를 참조하세요.

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>4.3단계: 정책에 기술 프로필 및 기술 프로필 유효성 검사 추가
1. 솔루션 탐색기에서 "솔루션 항목"을 확장하고, *TrustFrameworkExtensions.xml* 정책 파일을 엽니다.
2. `yourtenant.onmicrosoft.com`에서 `TenantId`, `PublicPolicyUri` 및 `<TenantId>` 필드를 테넌트의 이름으로 변경합니다.
3. `<TechnicalProfile Id="login-NonInteractive">` 요소에서 `ProxyIdentityExperienceFrameworkAppId` 및 `IdentityExperienceFrameworkAppId`의 모든 인스턴스를 [사용자 지정 정책 시작][B2C-GetStartedCustom]에서 구성된 응용 프로그램 ID로 바꿉니다.
4. `<ClaimsProviders>` 노드에서 다음 XML 코드 조각을 찾습니다. `ServiceUrl` 값을 변경하여 Azure App Service URL을 가리킵니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

앞의 기술 프로필은 하나의 `signInName` 입력 클레임을 정의합니다(전자 메일로 보내기). 로그인 시 클레임은 RESTful 끝점에 전송됩니다.

RESTful API에 대한 기술 프로필을 정의한 후에 Azure AD B2C 정책에 해당 기술 프로필을 호출하도록 지시합니다. XML 코드 조각은 `SelfAsserted-LocalAccountSignin-Email`을 재정의합니다. 해당 항목은 기본 정책에서 정의됩니다. XML 코드 조각은 `LocalAccountUserMigration` 기술 프로필을 가리키는 ReferenceId를 사용하여 `ValidationTechnicalProfile`을 추가합니다.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>4.4단계: 테넌트에 정책 업로드
1. [Azure Portal][Portal]에서 [Azure AD B2C 테넌트의 컨텍스트][B2C-NavContext]로 전환한 다음, **Azure AD B2C**를 선택합니다.

2. **ID 경험 프레임워크**를 선택합니다.

3. **모든 정책**을 선택합니다.

4. **정책 업로드**를 선택합니다.

5. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

6. *TrustFrameworkExtensions.xml* 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>4.5단계: 지금 실행을 사용하여 사용자 지정 정책 테스트
1. **Azure AD B2C 설정**을 선택한 다음 **ID 경험 프레임워크**로 이동합니다.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.

3. 마이그레이션된 사용자 중 하나로 로그인을 시도한 다음 **로그인**을 선택합니다. REST API는 다음과 같은 오류 메시지를 throw합니다.

    ![진단 로그 설정](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>4.6단계: (선택 사항) REST API 문제 해결
거의 실시간으로 로깅 정보를 볼 수 있고 모니터링할 수 있습니다.

1. RESTful 응용 프로그램 설정 메뉴의 **모니터링에서**에서 **진단 로그**를 선택합니다.

2. **응용 프로그램 로깅 사용(파일 시스템)** 을 **켜기**로 설정합니다.

3. **수준**을 **자세한 정보 표시**로 설정합니다.

4. **저장**을 선택합니다.

    ![진단 로그 설정](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. **설정** 메뉴에서 **로그 스트림**을 선택합니다.

6. RESTful API의 출력을 확인합니다.

자세한 내용은 [스트리밍 로그 및 콘솔][AppService-Log]을 참조하세요.

> [!IMPORTANT]
> 진단 로그는 개발 및 테스트 중에만 사용합니다. RESTful API 출력은 프로덕션 환경에서 노출되지 않아야 하는 기밀 정보를 포함할 수 있습니다.
>

## <a name="optional-download-the-complete-policy-files"></a>(선택 사항) 전체 정책 파일 다운로드
[사용자 지정 정책 시작][B2C-GetStartedCustom] 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일][UserMigrationSample]이 제공됩니다.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[AppService-Log]: https://docs.microsoft.com/azure/active-directory-b2c/app-service-web/web-sites-streaming-logs-and-console
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration