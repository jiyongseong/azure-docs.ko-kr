---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하여 Salesforce SAML 공급자 추가 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책을 만들고 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.openlocfilehash: 1ccf5c58eab9df9016224a91ddda952a05457e2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: SAML을 통해 Salesforce 계정을 사용하여 로그인

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Salesforce 조직의 사용자가 로그인할 수 있도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C 설정

Azure AD B2C(Azure Active Directory B2C)에서 [사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)하는 방법을 설명하는 단계를 모두 완료했는지 확인합니다.

내용은 다음과 같습니다.

* Azure AD B2C 테넌트 만들기
* Azure AD B2C 응용 프로그램 만들기
* 두 개의 정책 엔진 응용 프로그램 등록
* 키 설정
* 시작 팩 설정

### <a name="salesforce-setup"></a>Salesforce 설정

이 문서에서는 다음을 이미 수행했다고 가정합니다.

* Salesforce 계정 등록. [Developer Edition 평가판 계정](https://developer.salesforce.com/signup)에 등록할 수 있습니다.
* Salesforce 조직에 [내 도메인을 설정](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)합니다.

## <a name="set-up-salesforce-so-users-can-federate"></a>사용자가 페더레이션할 수 있도록 Salesforce 설정

Azure AD B2C가 Salesforce와 통신하도록 지원하려면 Salesforce 메타데이터 URL을 가져와야 합니다.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce를 ID 공급자로 설정

> [!NOTE]
> 이 문서에서는 [Salesforce Lightning 환경](https://developer.salesforce.com/page/Lightning_Experience_FAQ)을 사용한다고 가정합니다.

1. [Salesforce에 로그인](https://login.salesforce.com/)합니다. 
2. 왼쪽 메뉴의 **설정** 아래에서 **ID**를 확장하고 **ID 공급자**를 클릭합니다.
3. **ID 공급자 사용**을 클릭합니다.
4. **인증서 선택** 아래에서 Salesforce가 Azure AD B2C와 통신할 때 사용할 인증서를 선택합니다. (기본 인증서를 사용할 수 있습니다.) **저장**을 클릭합니다. 

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce에서 연결된 앱 만들기

1. **ID 공급자** 페이지에서 **서비스 공급자**로 이동합니다.
2. **Service Providers are now created via Connected Apps. Click here.**(서비스 공급자가 연결된 앱을 통해 생성됩니다. 여기를 클릭하세요.)를 클릭합니다.
3. **기본 정보** 아래에서 연결된 앱에 대한 필수 값을 입력합니다.
4. **Web App 설정**아래에서 **SAML 설정** 확인란을 선택합니다.
5. **엔터티 ID** 필드에 다음 URL을 입력합니다. `tenantName`에 대한 값을 바꾸어야 합니다.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. **ACS URL** 필드에 다음 URL을 입력합니다. `tenantName`에 대한 값을 바꾸어야 합니다.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. 다른 모든 설정에 대한 기본값을 그대로 둡니다.
8. 목록 아래쪽으로 스크롤한 다음 **저장**을 클릭합니다.

### <a name="get-the-metadata-url"></a>메타데이터 URL 가져오기

1. 연결된 앱의 개요 페이지에서 **관리**를 클릭합니다.
2. **메타데이터 검색 끝점** 값을 복사하고 저장합니다. 이 문서의 뒷부분에서 사용합니다.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce 사용자가 페더레이션하도록 설정

1. 연결된 앱의 **관리** 페이지에서 **프로필**로 이동합니다.
2. **프로필 관리**를 클릭합니다.
3. Azure AD B2C와 페더레이션하려는 프로필(또는 사용자 그룹)을 선택합니다. 시스템 관리자 권한으로 Salesforce 계정을 사용하여 페더레이션할 수 있도록 **시스템 관리자** 확인란을 선택합니다.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Azure AD B2C에 대한 서명 인증서 생성

Azure AD B2C는 Salesforce에 전송되는 요청에 서명해야 합니다. 서명 인증서를 생성하려면 Azure PowerShell을 열고 다음 명령을 실행합니다.

> [!NOTE]
> 맨 위 두 줄에서 테넌트 이름 및 암호를 업데이트해야 합니다.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Azure AD B2C에 SAML 서명 인증서 추가

서명 인증서를 Azure AD B2C 테넌트에 업로드합니다. 

1. Azure AD B2C 테넌트로 이동합니다. **설정** > **ID 환경 프레임워크** > **정책 키**를 클릭합니다.
2. **+추가**를 클릭한 다음
    1. **옵션** > **업로드**를 클릭합니다.
    2. **이름**을 입력합니다(예: SAMLSigningCert). 키의 이름에 *B2C_1A_* 접두사가 자동으로 추가됩니다.
    3. 인증서를 선택하려면 **업로드 파일 제어**를 선택합니다. 
    4. PowerShell 스크립트에서 설정한 인증서 암호를 입력합니다.
3. **만들기**를 클릭합니다.
4. 키를 만들었는지 확인합니다(예: B2C_1A_SAMLSigningCert). 전체 이름을 기록해둡니다(*B2C_1A_*를 포함). 정책에서 나중에 이 키를 참조합니다.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>기본 정책에서 Salesforce SAML 클레임 공급자 만들기

사용자가 Salesforce를 사용하여 로그인할 수 있도록 Salesforce를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신할 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 *클레임*을 *제공*하여 특정 사용자가 인증했는지 확인합니다. 그렇게 하려면 정책의 확장 파일에서 Salesforce에 대한 `<ClaimsProvider>`를 추가합니다.

1. 작업 디렉터리에서 확장 파일(TrustFrameworkExtensions.xml)을 엽니다.
2. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 만듭니다.
3. 새 `<ClaimsProvider>`을 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

`<ClaimsProvider>` 노드 아래에서:

1. `<Domain>` 값을 다른 ID 공급자와 `<ClaimsProvider>`를 구분하는 데 사용할 수 있는 고유한 값으로 변경합니다.
2. `<DisplayName>` 값을 클레임 공급자의 표시 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Salesforce에서 SAML 토큰을 가져오려면 Azure AD B2C에서 Azure AD(Azure Active Directory)와 통신하는 데 사용하는 프로토콜을 정의합니다. `<ClaimsProvider>`의 `<TechnicalProfile>` 요소에서 다음을 수행합니다.

1. `<TechnicalProfile>` 노드의 ID를 업데이트합니다. 이 ID는 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 사용합니다.
2. `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 페이지의 로그인 단추에 표시됩니다.
3. `<Description>` 값을 업데이트합니다.
4. Salesforce에서는 SAML 2.0 프로토콜을 사용합니다. `<Protocol>`에 대한 값이 **SAML2**인지 확인합니다.

이전 XML에서 `<Metadata>` 섹션을 업데이트하여 특정 Salesforce 계정의 설정을 반영합니다. XML에서 메타데이터 값을 다음과 같이 업데이트합니다.

1. `<Item Key="PartnerEntity">` 값을 앞에서 복사한 Salesforce 메타데이터 URL로 업데이트합니다. 다음과 같은 형식입니다. 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. `<CryptographicKeys>` 섹션에서 `StorageReferenceId`의 두 인스턴스 값을 모두 서명 인증서(예: B2C_1A_SAMLSigningCert)의 키 이름으로 업데이트합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

이제 Azure AD B2C에서 Salesforce와 통신하는 방법을 알 수 있도록 정책이 구성되었습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다. 정책의 확장 파일을 업로드하려면:

1. Azure AD B2C 테넌트에서 **모든 정책** 블레이드로 이동합니다.
2. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
3. 확장 파일(TrustFrameworkExtensions.xml)을 업로드합니다. 유효성 검사를 실패하지 않았는지 확인합니다.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>사용자 경험에 Salesforce SAML 클레임 공급자 등록

다음으로 사용자 경험 중 하나에 Salesforce SAML ID 공급자를 추가합니다. 이 시점에서 ID 공급자가 설정되었지만 등록 또는 로그인 페이지에서 사용할 수 없습니다. ID 공급자를 로그인 페이지에 추가하려면 먼저 기존 템플릿 사용자 경험의 복제본을 만듭니다. 그런 다음 Azure AD ID 공급자를 포함하도록 템플릿을 수정합니다.

1. 정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
2. Id=”SignUpOrSignIn”을 포함하여 `<UserJourneys>` 요소를 찾고 전체 `<UserJourney>` 값을 복사합니다.
3. 확장 파일(예: TrustFrameworkExtensions.xml)을 엽니다. `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 생성합니다.
4. 복사한 전체 `<UserJourney>`을 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
5. 새 `<UserJourney>`의 ID 이름을 바꿉니다(예: SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>ID 공급자 단추 표시

`<ClaimsProviderSelection>` 요소는 등록 또는 로그인 페이지에서 ID 공급자 단추를 사용하는 것과 유사합니다. Salesforce에 `<ClaimsProviderSelection>` 요소를 추가하여 사용자가 이 페이지로 이동하면 새 단추가 표시됩니다. ID 공급자 단추를 표시하려면:

1. 사용자가 만든 `<UserJourney>`에서 `<OrchestrationStep>`와 `Order="1"`을 찾습니다.
2. 다음 XML을 추가합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. `TargetClaimsExchangeId`을 논리값으로 설정합니다. 다른 값과 동일한 규칙을 사용하는 것이 좋습니다(예: *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>작업에 ID 공급자 단추 연결

이제 ID 공급자 단추를 만들었으므로 작업에 연결합니다. 이 경우에 작업은 Azure AD B2C에서 Salesforce와 통신하여 SAML 토큰을 수신할 수 있게 됩니다. 이렇게 하려면 Salesforce SAML 클레임 공급자의 기술 프로필을 연결합니다.

1. `<UserJourney>` 노드에서 `<OrchestrationStep>`와 `Order="2"`을 찾습니다.
2. 다음 XML을 추가합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. `Id`을 `TargetClaimsExchangeId`에 사용한 동일한 값으로 업데이트합니다.
4. `TechnicalProfileReferenceId`를 이전에 만든 기술 프로필의 `Id`로 업데이트합니다(예: ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>업데이트된 확장 파일 업로드

확장 파일을 수정하는 작업을 완료했습니다. 이 파일을 저장하고 업로드합니다. 모든 유효성 검사에 성공했는지 확인합니다.

### <a name="update-the-relying-party-file"></a>신뢰 당사자 파일 업데이트

다음으로, 만든 사용자 경험을 시작할 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 SignUpOrSignIn.xml의 복사본을 만듭니다. 그런 다음 이름을 바꿉니다(예: SignUpOrSignInWithAAD.xml).
2. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값으로 업데이트합니다. 이는 정책의 이름(예: SignUpOrSignInWithAAD)입니다.
3. `<DefaultUserJourney>`에서 `ReferenceId` 특성을 수정하여 만든 새 사용자 경험의 `Id`와 일치시킵니다(예: SignUpOrSignUsingContoso).
4. 변경 내용을 저장하고 파일을 업로드합니다.

## <a name="test-and-troubleshoot"></a>테스트 및 문제 해결

Azure Portal에서 방금 업로드한 사용자 지정 정책을 테스트하려면 정책 블레이드로 이동하고 **지금 실행**을 클릭합니다. 실패한 경우 [사용자 지정 정책 문제 해결](active-directory-b2c-troubleshoot-custom.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 대한 피드백을 제공합니다.
