---
title: 'Azure Active Directory B2C: 사용자 지정 정책에서 등록 수정 및 자체 어설션된 공급자 구성'
description: 등록에 클레임을 추가하고 사용자 입력을 구성하는 연습
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2017
ms.author: davidmu
ms.openlocfilehash: b9ea48f0700509194602dd15ff42c7c689685a53
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: 새 클레임을 추가하도록 등록 수정 및 사용자 입력 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 사용자가 제공한 항목(클레임)을 등록 사용자 경험에 추가합니다.  드롭다운으로 항목을 구성하고 필요한 경우 정의합니다.

## <a name="prerequisites"></a>필수 조건

* [사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.  진행하기 전에 등록/로그인 사용자 경험을 테스트하여 새 로컬 계정을 등록합니다.


사용자로부터 초기 데이터 수집은 등록/로그인을 통해 수행합니다.  프로필 편집 사용자 경험을 통해 추가 클레임을 나중에 수집할 수 있습니다. 언제든지 Azure AD B2C가 사용자로부터 정보를 직접 대화형으로 수집하며 Identity Experience Framework는 해당 `selfasserted provider`를 사용합니다. 아래 단계는 이 공급자가 사용될 때마다 적용됩니다.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>클레임, 해당 표시 이름 및 사용자 입력 형식을 정의합니다.
사용자에게 도시를 요청해 보겠습니다.  TrustFrameworkBase 정책 파일의 `<ClaimsSchema>` 요소에 다음 요소를 추가합니다.

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
여기에 클레임을 사용자 지정할 추가 옵션이 있습니다.  전체 스키마는 **Identity Experience Framework 기술 참조 가이드**를 참조하세요.  이 가이드는 참조 섹션에 곧 게시될 예정입니다.

* `<DisplayName>`은 사용자용 *레이블*을 정의하는 문자열입니다.

* `<UserHelpText>`는 사용자가 필요한 항목을 이해하는 데 도움이 됩니다.

* `<UserInputType>`에는 다음 4가지 옵션이 강조 표시됩니다.
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` - 단일 선택을 적용합니다.
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect` - 유효한 값만 선택할 수 있습니다.

![드롭다운 옵션의 스크린샷](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect` 하나 이상의 값을 선택할 수 있습니다.

![다중 선택 옵션의 스크린샷](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>등록/로그인 사용자 경험에 클레임 추가

1. 클레임을 `<OutputClaim ClaimTypeReferenceId="city"/>`로 TechnicalProfile`LocalAccountSignUpWithLogonEmail`(TrustFrameworkBase 정책 파일에 있음)에 추가합니다.  이 TechnicalProfile에서는 SelfAssertedAttributeProvider를 사용합니다.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. 클레임을 AAD-UserWriteUsingLogonEmail에 `<PersistedClaim ClaimTypeReferenceId="city" />`로 추가하여 사용자로부터 클레임을 수집한 후 AAD 디렉터리에 클레임을 기록합니다. 향후 사용할 디렉터리에 클레임을 보관하지 않으려면 이 단계를 건너뛰어도 됩니다.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. 사용자가 `<OutputClaim ClaimTypeReferenceId="city" />`로 로그인할 때 디렉터리에서 읽어오는 TechnicalProfile에 클레임을 추가합니다.

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. `<OutputClaim ClaimTypeReferenceId="city" />`를 RP 정책 파일 SignUporSignIn.xml에 추가하여 이 클레임이 성공적인 사용자 경험 후 응용 프로그램에 토큰으로 전송되도록 합니다.

  ```xml
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>"지금 실행"을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 블레이드**를 열고 **ID 경험 프레임워크 > 사용자 지정 정책**로 이동합니다.
2. 업로드한 사용자 지정 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
3. 전자 메일 주소를 사용하여 등록할 수 있습니다.

테스트 모드의 등록 화면은 다음과 유사하게 표시됩니다.

![수정된 등록 옵션의 스크린샷](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  이제 응용 프로그램에 반환되는 토큰은 아래 표시된 것처럼 `city` 클레임을 포함합니다.
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>선택 사항: 등록 경험에서 전자 메일 확인 제거

전자 메일 확인을 건너뛰려면 정책 작성자가 `PartnerClaimType="Verified.Email"`을 제거하도록 선택하면 됩니다. “Required” = true가 제거되지 않으면 이메일 주소가 필요하지만 확인되지 않습니다.  이 옵션이 사용자의 사용 사례에 적합한지 신중하게 고려하세요.

확인된 메일은 시작 팩의 TrustFrameworkBase 정책 파일의 `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">`에서 기본적으로 사용하도록 설정됩니다.
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>다음 단계

아래 나열된 TechnicalProfiles를 변경하여 새 클레임을 소셜 계정 로그인에 대한 흐름에 추가합니다. 이러한 파일은 로케이터로 alternativeSecurityId를 사용하여 사용자 데이터를 쓰고 읽기 위해 소셜/페더레이션된 계정 로그인에 사용됩니다.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```
