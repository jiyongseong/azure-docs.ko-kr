---
title: 'Azure Active Directory B2C: 사용자 지정 정책 시작 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책을 시작하는 방법
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 502f88bd08c05f71988763fedeba4d596aa409b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서의 단계를 완료하면 사용자 지정 정책에서 이메일 주소와 암호를 통한 "로컬 계정" 등록 또는 로그인을 지원합니다. 또한 Facebook 또는 Azure Active Directory와 같은 추가 ID 공급자를 추가하는 환경도 준비합니다. Azure AD(Azure Active Directory ) B2C ID 경험 프레임워크의 다른 용도를 이해하려면 먼저 이 단계를 완료하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

계속하기 전에 모든 사용자, 응용 프로그램, 정책 등을 위한 컨테이너인 Azure AD B2C 테넌트가 있어야 합니다. 아직 없으면 [Azure AD B2C 테넌트를 만들어야 합니다](active-directory-b2c-get-started.md). 계속하기 전에 모든 개발자가 Azure AD B2C 기본 제공 정책 연습을 완료하고 기본 제공 정책으로 응용 프로그램을 구성하는 것이 좋습니다. 정책 이름을 약간 변경하여 사용자 지정 정책을 호출하면 응용 프로그램이 두 유형의 정책 모두에서 작동합니다.

>[!NOTE]
>사용자 지정 정책 편집에 액세스하려면 테넌트와 연결된 유효한 Azure 구독이 있어야 합니다. [Azure AD B2C 테넌트를 Azure 구독에 연결](active-directory-b2c-how-to-enable-billing.md)하지 않았거나 Azure 구독을 사용할 수 없는 경우 ID 경험 프레임워크 단추를 사용할 수 없습니다.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>사용자 지정 정책에서 사용하도록 B2C 테넌트에 서명 및 암호화 키 추가

1. Azure AD B2C 테넌트 설정에서 **ID 경험 프레임워크** 블레이드를 엽니다.
2. **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.
3. B2C_1A_TokenSigningKeyContainer가 없으면 만듭니다.<br>
    a. **추가**를 선택합니다. <br>
    나. **생성**을 선택합니다.<br>
    다. **이름**에는 `TokenSigningKeyContainer`를 사용합니다. <br> 
    `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.<br>
    d. **키 유형**에는 **RSA**를 사용합니다.<br>
    e. **날짜**에는 기본값을 사용합니다. <br>
    f. **키 사용**에는 **서명**을 사용합니다.<br>
    g. **만들기**를 선택합니다.<br>
4. B2C_1A_TokenEncryptionKeyContainer가 없으면 만듭니다.<br>
 a. **추가**를 선택합니다.<br>
 나. **생성**을 선택합니다.<br>
 다. **이름**에는 `TokenEncryptionKeyContainer`를 사용합니다. <br>
   `B2C_1A`_ 접두사가 자동으로 추가될 수 있습니다.<br>
 d. **키 유형**에는 **RSA**를 사용합니다.<br>
 e. **날짜**에는 기본값을 사용합니다.<br>
 f. **키 사용**에는 **암호화**를 사용합니다.<br>
 g. **만들기**를 선택합니다.<br>
5. B2C_1A_FacebookSecret를 만듭니다. <br>
Facebook 응용 프로그램 비밀이 이미 있을 경우 해당 비밀을 정책 키로 테넌트에 추가합니다. 그렇지 않으면 정책이 유효성 검사를 통과하도록 자리 표시자 값이 있는 키를 만들어야 합니다.<br>
 a. **추가**를 선택합니다.<br>
 나. **옵션**에는 **수동**을 사용합니다.<br>
 다. **이름**에는 `FacebookSecret`를 사용합니다. <br>
 `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.<br>
 d. **비밀** 상자에서 developers.facebook.com의 FacebookSecret 또는 `0`을 자리 표시자로 입력합니다. *Facebook 앱 ID가 아닙니다.* <br>
 e. **키 사용**에는 **서명**을 사용합니다. <br>
 f. **만들기**를 선택하고 이 만들기를 확인합니다.

## <a name="register-identity-experience-framework-applications"></a>ID 경험 프레임워크 응용 프로그램 등록

Azure AD B2C에서는 엔진에서 사용자를 등록하고 로그인하는 데 사용하는 두 개의 추가 응용 프로그램을 등록해야 합니다.

>[!NOTE]
>로컬 계정을 사용하여 로그인할 수 있는 두 개의 응용 프로그램, 즉 IdentityExperienceFramework(웹앱) 및 IdentityExperienceFramework 앱에서 위임된 권한이 있는 ProxyIdentityExperienceFramework(네이티브 앱)를 만들어야 합니다. 로컬 계정은 테넌트에만 존재합니다. 사용자는 고유한 이메일 주소/암호 조합으로 등록하여 테넌트에 등록된 응용 프로그램에 액세스합니다.

### <a name="create-the-identityexperienceframework-application"></a>IdentityExperienceFramework 응용 프로그램 만들기

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환합니다](active-directory-b2c-navigate-to-b2c-context.md).
2. **Azure Active Directory** 블레이드를 엽니다(**Azure AD B2C** 블레이드가 아님). **더 많은 서비스**를 선택하여 찾을 수도 있습니다.
3. **앱 등록**을 선택합니다.
4. **새 응용 프로그램 등록**을 선택합니다.
   * **이름**에는 `IdentityExperienceFramework`를 사용합니다.
   * **응용 프로그램 종류**에는 **웹앱/API**를 사용합니다.
   * **로그온 URL**에는 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`을 사용합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트 도메인 이름입니다.
5. **만들기**를 선택합니다.
6. 만들어졌으면 새로 만든 **IdentityExperienceFramework** 응용 프로그램을 선택합니다.<br>
   * **속성**을 선택합니다.<br>
   * 응용 프로그램 ID를 복사하여 나중에 사용할 수 있도록 저장해 둡니다.

### <a name="create-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 응용 프로그램 만들기

1. **앱 등록**을 선택합니다.
1. **새 응용 프로그램 등록**을 선택합니다.
   * **이름**에는 `ProxyIdentityExperienceFramework`를 사용합니다.
   * **응용 프로그램 종류**에는 **네이티브**를 사용합니다.
   * **리디렉션 URI**에는 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`을 사용합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트입니다.
1. **만들기**를 선택합니다.
1. 만들어졌으면 **ProxyIdentityExperienceFramework** 응용 프로그램을 선택합니다.<br>
   * **속성**을 선택합니다. <br>
   * 응용 프로그램 ID를 복사하여 나중에 사용할 수 있도록 저장해 둡니다.
1. **필요한 권한**을 선택합니다.
1. **추가**를 선택합니다.
1. **API 선택**을 선택합니다.
1. IdentityExperienceFramework라는 이름을 검색합니다. 결과에서 **IdentityExperienceFramework**를 선택하고 **선택**을 클릭합니다.
1. **IdentityExperienceFramework 액세스** 옆의 확인란을 선택한 다음 **선택**을 클릭합니다.
1. **완료**를 선택합니다.
1. **권한 부여**을 선택하고 **예**를 선택하여 확인합니다.

## <a name="download-starter-pack-and-modify-policies"></a>시작 팩 다운로드 및 정책 수정

사용자 지정 정책은 Azure AD B2C 테넌트에 업로드해야 하는 일련의 XML 파일입니다. Microsoft는 사용자의 신속한 진행을 위해 시작 팩을 제공합니다. 다음 목록의 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.
 * LocalAccounts - 로컬 계정만 사용할 수 있습니다.
 * SocialAccounts - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
 * **SocialAndLocalAccounts** - 이 파일은 연습을 위해 사용됩니다.
 * SocialAndLocalAccountsWithMFA - 소셜, 로컬 및 Multi-Factor Authentication 옵션이 여기에 포함됩니다.

시작 팩 각각에는 다음이 포함됩니다.

* 정책의 [기본 파일](active-directory-b2c-overview-custom.md#policy-files) - 기본 파일에 몇 가지 수정이 필요합니다.
* 정책의 [확장 파일](active-directory-b2c-overview-custom.md#policy-files) -  이 파일은 구성이 대부분 변경되었습니다.
* [신뢰 당사자 파일](active-directory-b2c-overview-custom.md#policy-files)은 응용 프로그램에서 호출하는 작업 관련 파일입니다.

>[!NOTE]
>XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 디렉터리에 있는 TrustFrameworkPolicy_0.3.0.0.xsd XML 스키마에 대해 파일의 유효성을 검사합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

 이제 시작하겠습니다.

1. GitHub에서 active-directory-b2c-custom-policy-starterpack을 다운로드합니다. [.zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)하거나 실행합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. SocialAndLocalAccounts 폴더를 엽니다.  이 폴더의 기본 파일(TrustFrameworkBase.xml)에는 로컬 및 소셜/회사 계정 모두에 필요한 콘텐츠가 포함되어 있습니다. 소셜 콘텐츠는 로컬 계정을 가져오고 실행하기 위한 단계를 방해하지 않습니다.
3. TrustFrameworkBase.xml을 엽니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).
4. `TrustFrameworkPolicy` 루트 요소에서 `TenantId` 및 `PublicPolicyUri` 특성을 업데이트하여 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트의 도메인 이름으로 바꿉니다.
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId`는 포털에 표시되는 정책 이름이며, 다른 정책 파일에서 이 정책 파일을 참조하는 이름입니다.

5. 파일을 저장합니다.
6. TrustFrameworkExtensions.xml을 엽니다. `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 동일하게 두 번 바꿉니다. `<TenantId>` 요소에서 동일하게 총 세 번 바꿉니다. 파일을 저장합니다.
7. SignUpOrSignIn.xml을 엽니다. 세 위치에 있는 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 바꿔 동일하게 변경합니다. 파일을 저장합니다.
8. 암호 재설정 및 프로필 편집 파일을 엽니다. 각 파일의 세 위치에 있는 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 바꿔 동일하게 변경합니다. 파일을 저장합니다.

### <a name="add-the-application-ids-to-your-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가
확장 파일(`TrustFrameworkExtensions.xml`)에 응용 프로그램 ID를 추가합니다.

1. 확장 파일(TrustFrameworkExtensions.xml)을 열고 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
2. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 ID 경험 프레임워크의 응용 프로그램 ID로 바꿉니다. 다음은 예제입니다.

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 프록시 ID 경험 프레임워크 응용 프로그램의 응용 프로그램 ID로 바꿉니다.
4. 확장 파일을 저장합니다.

## <a name="upload-the-policies-to-your-tenant"></a>테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C** 블레이드를 엽니다.
2. **ID 경험 프레임워크**를 선택합니다.
3. **정책 업로드**를 선택합니다.

    >[!WARNING]
    >다음과 같은 순서로 사용자 지정 정책 파일을 업로드해야 합니다.

1. TrustFrameworkBase.xml을 업로드합니다.
2. TrustFrameworkExtensions.xml을 업로드합니다.
3. SignUpOrSignin.xml을 업로드합니다.
4. 다른 정책 파일을 업로드합니다.

파일이 업로드되면 정책 파일의 이름 앞에 `B2C_1A_`가 추가됩니다.

## <a name="test-the-custom-policy-by-using-run-now"></a>지금 실행을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.

   >[!NOTE]
   >**지금 실행**을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 응용 프로그램은 Azure AD B2C의 **응용 프로그램** 메뉴 선택 항목 또는 ID 경험 프레임워크를 통해 기본 제공 정책과 사용자 지정 정책을 모두 호출하여 B2C 테넌트에 등록되어야 합니다. 응용 프로그램당 한 번만 등록하면 됩니다.<br><br>
   응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.  

2. 업로드한 B2C_1A_signup_signin RP(신뢰 당사자) 사용자 지정 정책을 엽니다. **지금 실행**을 선택합니다.

3. 전자 메일 주소를 사용하여 등록할 수 있습니다.

4. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

>[!NOTE]
>로그인이 실패하는 것은 일반적으로 잘못 구성된 IdentityExperienceFramework 앱 때문입니다.


## <a name="next-steps"></a>다음 단계

### <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가
Facebook을 설정하려면:
1. [developers.facebook.com에서 Facebook 응용 프로그램을 구성합니다](active-directory-b2c-setup-fb-app.md).
2. [Azure AD B2C 테넌트에 Facebook 응용 프로그램 비밀을 추가합니다](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. TrustFrameworkExtensions 정책 파일에서 `client_id` 값을 Facebook 응용 프로그램 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. 테넌트에 TrustFrameworkExtensions.xml 정책 파일을 업로드합니다.
5. **지금 실행**을 사용하여 테스트하거나 등록된 응용 프로그램에서 바로 정책을 호출합니다.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Azure Active Directory를 ID 공급자로 추가
이 시작 가이드에서 사용된 기본 파일에는 이미 다른 ID 공급자를 추가하는 데 필요한 내용 일부가 포함되어 있습니다. 로그인 설정에 대한 자세한 내용은 [Azure Active Directory B2C: Azure AD 계정을 사용하여 로그인](active-directory-b2c-setup-aad-custom.md) 문서를 참조하세요.

ID 경험 프레임워크를 사용하는 Azure AD B2C의 사용자 지정 정책에 대한 개요는 [Azure Active Directory B2C: 사용자 지정 정책](active-directory-b2c-overview-custom.md) 문서를 참조하세요. 
