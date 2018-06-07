---
title: 'Azure Active Directory B2C: 셀프 서비스 암호 변경 | Microsoft Docs'
description: Azure Active Directory B2C 미리 보기에서 소비자를 위해 셀프 서비스 암호 변경을 설정하는 방법을 보여주는 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 4c54abe8c3375b49b786c7849c7e963e5849fa3a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책에서 암호 변경 구성  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

로그인한 소비자(로컬 계정 사용)가 암호 변경 기능을 사용하는 경우 [셀프서비스 암호 재설정 흐름](active-directory-b2c-reference-sspr.md)에 설명된 대로 전자 메일을 확인하여 신뢰성을 증명하지 않고 해당 암호를 변경할 수 있습니다. 소비자가 암호 변경 흐름을 가져오는 시점에 세션이 만료되는 경우 사용자에게는 다시 로그인하라는 메시지가 표시됩니다. 

## <a name="prerequisites"></a>필수 조건

[시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 로컬 계정 등록/로그인을 완료하도록 구성된 Azure AD B2C 테넌트

## <a name="how-to-configure-password-change-in-custom-policy"></a>사용자 지정 정책에서 암호 변경 내용을 구성하는 방법

사용자 지정 정책에서 암호 변경을 구성하려면 다음과 같이 트러스트 프레임워크 확장 정책을 변경합니다. 

## <a name="define-a-claimtype-oldpassword"></a>ClaimType 'oldPassword' 정의

사용자 지정 정책의 전체 구조는 `ClaimsSchema`를 포함하고 아래와 같이 새로운 `ClaimType` 'oldPassword'를 정의해야 합니다. 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

이러한 요소의 용도는 다음과 같습니다.

- `ClaimsSchema`는 유효성 검사 중인 클레임을 정의합니다.  이 경우에 '이전 암호'의 유효성이 검사됩니다. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>지원 요소를 사용하여 암호 변경 클레임 공급자를 추가합니다.

암호 변경 클레임 공급자는

1. 이전 암호에 대해 사용자를 인증합니다.
2. 또한 '새 암호'가 '새 암호 확인'과 일치하는 경우 이 값은 B2C 데이터 저장소에 저장되고 따라서 암호가 성공적으로 변경됩니다. 

![이미지](images/passwordchange.jpg)

사용자 확장의 정책에 다음과 같은 클레임 공급자를 추가합니다. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가

확장 파일(`TrustFrameworkExtensions.xml`)에 응용 프로그램 ID를 추가합니다.

1. 확장 파일(TrustFrameworkExtensions.xml)에서 `<TechnicalProfile Id="login-NonInteractive">` 및 `<TechnicalProfile Id="login-NonInteractive-PasswordChange">` 요소를 찾습니다.

2. `IdentityExperienceFrameworkAppId`의 모든 인스턴스를 [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 ID 환경 프레임워크의 응용 프로그램 ID로 바꿉니다. 다음은 예제입니다.

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. `ProxyIdentityExperienceFrameworkAppId`의 모든 인스턴스를 [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 프록시 ID 환경 프레임워크의 응용 프로그램 ID로 바꿉니다.

4. 확장 파일을 저장합니다.



## <a name="create-a-password-change-user-journey"></a>암호 변경 사용자 경험을 만듭니다.

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

확장 파일을 수정하는 작업을 완료했습니다. 이 파일을 저장하고 업로드합니다. 모든 유효성 검사에 성공했는지 확인합니다.



## <a name="create-a-relying-party-rp-file"></a>RP(신뢰 당사자) 파일을 만듭니다.

다음으로, 만든 사용자 경험을 시작할 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 ProfileEdit.xml의 복사본을 만듭니다. 그런 다음 이름을 바꿉니다(예: PasswordChange.xml).
2. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값으로 업데이트합니다. 이는 정책의 이름(예: PasswordChange)입니다.
3. `<DefaultUserJourney>`에서 `ReferenceId` 특성을 수정하여 만든 새 사용자 경험의 `Id`와 일치시킵니다(예: PasswordChange).
4. 변경 내용을 저장하고 파일을 업로드합니다.
5. Azure Portal에서 업로드한 사용자 지정 정책을 테스트하려면 정책 블레이드로 이동하고 **지금 실행**을 클릭합니다.




## <a name="link-to-password-change-sample-policy"></a>암호 변경 샘플 정책에 연결

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)에서 샘플 정책을 찾을 수 있습니다. 










