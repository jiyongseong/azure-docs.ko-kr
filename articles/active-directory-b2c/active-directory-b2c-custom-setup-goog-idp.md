---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하여 OAuth2 ID 공급자로 Google+ 추가'
description: OAuth2 프로토콜을 사용하여 ID 공급자로 Google+를 사용하는 샘플
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
ms.openlocfilehash: f12bbc2472aa2d83088c7d4d7b8f173744fb36b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 OAuth2 ID 공급자로 Google+ 추가

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 가이드에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 Google+ 계정의 사용자가 로그인할 수 있도록 하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

이러한 단계는 다음과 같습니다.

1.  Google+ 계정 응용 프로그램 만들기
2.  Azure AD B2C에 Google+ 계정 응용 프로그램 키 추가
3.  정책에 클레임 공급자 추가
4.  사용자 경험에 Google+ 계정 클레임 공급자 등록
5.  Azure AD B2C 테넌트에 정책 업로드 및 테스트

## <a name="create-a-google-account-application"></a>Google+ 계정 응용 프로그램 만들기
Azure AD(Azure Active Directory) B2C에서 Google+를 ID 공급자로 사용하려면 Google+ 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. Google+ 응용 프로그램을 여기에서 등록할 수 있습니다. [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  [Google 개발자 콘솔](https://console.developers.google.com/) 로 이동하고 Google + 계정 자격 증명으로 로그인합니다.
2.  **프로젝트 만들기**를 클릭하고 **프로젝트 이름**을 입력한 다음 **만들기**를 클릭합니다.

3.  **프로젝트 메뉴**를 클릭합니다.

    ![Google+ 계정 - 프로젝트 선택](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  **+** 단추를 클릭합니다.

    ![Google+ 계정 - 새 프로젝트 만들기](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  **프로젝트 이름**을 입력한 다음 **만들기**를 클릭합니다.

    ![Google+ 계정 - 새 프로젝트](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  프로젝트가 준비될 때까지 기다렸다가 **프로젝트 메뉴**를 클릭합니다.

    ![Google+ 계정 - 새 프로젝트를 사용할 준비가 될 때까지 대기](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  프로젝트 이름을 클릭합니다.

    ![Google+ 계정 - 새 프로젝트 선택](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  **API 관리자**를 클릭한 다음 왼쪽 탐색에서 **자격 증명**을 클릭합니다.
9.  **OAuth 동의 화면**을 위쪽 탭에서 클릭합니다.

    ![Google+ 계정 - OAuth 동의 화면 설정](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  유효한 **메일 주소**를 선택하거나 지정하고 **제품 이름**을 제공한 후 **저장**을 클릭합니다.

    ![Google+ - 응용 프로그램 자격 증명](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  **새 자격 증명**을 클릭한 다음 **OAuth 클라이언트 ID**를 선택합니다.

    ![Google+ - 새 응용 프로그램 자격 증명 만들기](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  **응용 프로그램 형식**에서 **웹 응용 프로그램**을 선택합니다.

    ![Google+ - 응용 프로그램 형식 선택](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  응용 프로그램에 대한 **이름**을 제공하고 **권한이 부여된 JavaScript 원본** 필드에 `https://login.microsoftonline.com`을 입력하고 **권한이 부여된 리디렉션 URI** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. **{tenant}** 값은 대/소문자를 구분합니다. **만들기**를 클릭합니다.

    ![Google+ - 승인된 JavaScript 원본 및 리디렉션 URI 제공](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 Google+를 ID 공급자로 구성해야 합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.

    ![Google+ - 클라이언트 ID 및 클라이언트 비밀 값 복사](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C에 Google+ 계정 응용 프로그램 키 추가
Google+ 계정으로 페더레이션하려면 응용 프로그램 대신 Azure AD B2C를 신뢰하기 위해 Google+ 계정에 대한 클라이언트 암호가 필요합니다. Azure AD B2C 테넌트에 Google+ 응용 프로그램 비밀을 저장해야 합니다.  

1.  Azure AD B2C 테넌트로 이동하고 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.
2.  **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.
3.  **+추가**를 클릭합니다.
4.  **옵션**에는 **수동**을 사용합니다.
5.  **이름**에는 `GoogleSecret`를 사용합니다.  
    `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
6.  **암호** 상자에 위에서 복사한 [Google 개발자 콘솔](https://console.developers.google.com/)의 Google 응용 프로그램 암호를 입력합니다.
7.  **키 사용**에는 **서명**을 사용합니다.
8.  **만들기**
9.  `B2C_1A_GoogleSecret` 키를 만들었는지 확인합니다.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>확장 정책에서 클레임 공급자 추가

사용자가 Google+ 계정을 사용하여 로그인하도록 하려면 Google+ 계정을 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

확장 정책 파일에서 `<ClaimsProvider>` 노드를 추가하여 Google+ 계정을 클레임 공급자로 정의합니다.

1.  작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).
2.  `<ClaimsProviders>` 섹션을 찾습니다.
3.  `ClaimsProviders` 요소 아래에서 다음 XML 코드 조각을 추가하고 파일을 저장하기 전에 Google+ 계정 응용 프로그램 클라이언트 ID로 `client_id` 값을 바꿉니다.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>등록 또는 로그인 사용자 경험에 Google+ 계정 클레임 공급자 등록

ID 공급자가 설정되었습니다.  그러나 등록/로그인 화면에서 사용할 수 없습니다. `SignUpOrSignIn` 사용자 경험에 Google+ 계정 ID 공급자를 추가합니다. 사용할 수 있도록 하려면 기존 템플릿 사용자 경험의 복제본을 만듭니다.  Google+ 계정 ID 공급자를 추가합니다.

>[!NOTE]
>
>`<UserJourneys>` 요소를 정책의 기본 파일에서 확장 파일(TrustFrameworkExtensions.xml)로 복사한 경우 이 섹션으로 건너뛸 수 있습니다.

1.  정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
2.  `<UserJourneys>` 요소를 찾고 `<UserJourneys>` 노드의 전체 콘텐츠를 복사합니다.
3.  확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4.  복사한 `<UserJourney>` 노드의 전체 콘텐츠를 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.

### <a name="display-the-button"></a>단추 표시
`<ClaimsProviderSelections>` 요소는 클레임 공급자 선택 옵션 목록과 해당 순서를 정의합니다.  `<ClaimsProviderSelection>` 요소는 등록/로그인 페이지에서 ID 공급자 단추를 사용하는 것과 유사합니다. Google+ 계정에 `<ClaimsProviderSelection>` 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1.  복사한 사용자 경험에서 `Id="SignUpOrSignIn"`이 포함된 `<UserJourney>` 노드를 찾습니다.
2.  `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
3.  `<ClaimsProviderSelections>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결
이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Google+ 계정과 통신하여 토큰을 수신할 수 있게 됩니다.

1.  `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
2.  `<ClaimsExchanges>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * `Id`에 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값이 있는지 확인합니다.
> * `TechnicalProfileReferenceId` ID를 이전에 만든 기술 프로필로(Google-OAUTH)로 설정하도록 합니다.

## <a name="upload-the-policy-to-your-tenant"></a>테넌트에 정책 업로드
1.  [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C** 블레이드를 엽니다.
2.  **ID 경험 프레임워크**를 선택합니다.
3.  **모든 정책** 블레이드를 엽니다.
4.  **정책 업로드**를 선택합니다.
5.  **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
6.  TrustFrameworkExtensions.xml을 **업로드**하고 유효성 검사가 실패하지 않았는지 확인합니다.

## <a name="test-the-custom-policy-by-using-run-now"></a>지금 실행을 사용하여 사용자 지정 정책 테스트
1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.

    >[!NOTE]
    >
    >    **지금 실행**을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 
    >    응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.


2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 엽니다. **지금 실행**을 선택합니다.
3.  Google+ 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[선택 사항]프로필 편집 사용자 경험에 Google+ 계정 클레임 공급자 등록
`ProfileEdit` 사용자 경험에 Google+ 계정 ID 공급자를 추가하려고 합니다. 사용할 수 있도록 하려면 마지막 두 단계를 반복합니다.

### <a name="display-the-button"></a>단추 표시
1.  정책의 확장 파일(예: TrustFrameworkExtensions.xml)을 엽니다.
2.  복사한 사용자 경험에서 `Id="ProfileEdit"`이 포함된 `<UserJourney>` 노드를 찾습니다.
3.  `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
4.  `<ClaimsProviderSelections>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결
1.  `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
2.  `<ClaimsExchanges>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>테넌트에 정책 업로드
1.  [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C** 블레이드를 엽니다.
2.  **ID 경험 프레임워크**를 선택합니다.
3.  **모든 정책** 블레이드를 엽니다.
4.  **정책 업로드**를 선택합니다.
5.  **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
6.  TrustFrameworkExtensions.xml을 **업로드**하고 유효성 검사가 실패하지 않았는지 확인합니다.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>지금 실행을 사용하여 사용자 지정 프로필-편집 정책 테스트

1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.
2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_ProfileEdit**를 엽니다. **지금 실행**을 선택합니다.
3.  Google+ 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="download-the-complete-policy-files"></a>완성 정책 파일 다운로드
선택 사항: 이러한 샘플 파일을 사용하는 대신 사용자 지정 정책을 사용하여 시작을 완료한 후에 고유한 사용자 지정 정책 사용하여 시나리오를 빌드하는 것이 좋습니다.  [참조할 샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
