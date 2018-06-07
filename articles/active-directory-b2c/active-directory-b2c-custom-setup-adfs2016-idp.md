---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가'
description: SAML 프로토콜 및 사용자 지정 정책을 사용하여 ADFS 2016을 설정하는 방법 문서
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
ms.openlocfilehash: af102bbc3bc7608fe641db19f4af8c760907a564
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 ADFS 계정의 사용자가 로그인할 수 있도록 하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

이러한 단계는 다음과 같습니다.

1.  ADFS 신뢰 당사자 트러스트 만들기
2.  Azure AD B2C에 ADFS 신뢰 당사자 트러스트 인증서 추가
3.  정책에 클레임 공급자 추가
4.  사용자 경험에 ADFS 계정 클레임 공급자 등록
5.  Azure AD B2C 테넌트에 정책 업로드 및 테스트

## <a name="to-create-a-claims-aware-relying-party-trust"></a>클레임 인식 신뢰 당사자 트러스트를 만들려면

Azure AD(Azure Active Directory) B2C에서 ADFS를 ID 공급자로 사용하려면 먼저 ADFS 신뢰 당사자 트러스트를 만들고 올바른 매개 변수를 제공해야 합니다.

AD FS 관리 스냅인을 사용하여 새 신뢰 당사자 트러스트를 추가하고 설정을 수동으로 구성하려면 페더레이션 서버에서 다음 절차를 수행합니다.

로컬 컴퓨터에서 **관리자**또는 동등한 멤버 자격은 최소한 이 절차를 완료해야 합니다. [로컬 및 도메인 기본 그룹](http://go.microsoft.com/fwlink/?LinkId=83477)에서 적절한 계정 및 그룹 멤버 자격을 사용하는 방법에 대한 세부 정보를 검토합니다.

1.  서버 관리자에서 **도구**를 클릭하고 **ADFS 관리**를 선택합니다.

2.  **신뢰 당사자 트러스트 추가**를 클릭합니다.
    ![신뢰 당사자 트러스트 추가](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  **시작** 페이지에서 **클레임 인식**을 클릭하고 **시작**을 선택합니다.
    ![시작 페이지에서 클레임 인식 선택](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  **데이터 원본 선택** 페이지에서 **신뢰 당사자에 대한 데이터를 수동으로 입력**을 클릭하고 **다음**을 클릭합니다.
    ![신뢰 당사자에 대한 데이터 입력](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  **표시 이름 지정** 페이지에서 **표시 이름**에 이름을 입력하고 **메모** 아래에서 이 신뢰 당사자 트러스트에 대한 설명을 입력한 후 **다음**을 클릭합니다.
    ![표시 이름 및 메모 지정](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  선택 사항입니다. 선택적 토큰 암호화 인증서가 있는 경우 **인증서 구성** 페이지에서 **찾아보기**를 클릭하여 인증서 파일을 찾은 후 **다음**을 클릭합니다.
    ![인증서 구성](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  **URL 구성** 페이지에서 **SAML 2.0 WebSSO 프로토콜에 대한 지원 설정** 확인란을 선택합니다. **신뢰 당사자 SAML 2.0 SSO 서비스 URL** 아래에서 이 신뢰 당사자 트러스트에 대한 SAML(Security Assertion Markup Language) 서비스 끝점 URL을 입력한 후 **다음**을 클릭합니다.  **신뢰 당사자 SAML 2.0 SSO 서비스 URL**에 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`을 붙여넣습니다. {테넌트}를 테넌트의 이름(예: contosob2c.onmicrosoft.com)으로 바꾸고 {정책}을 확장 정책 이름(예: B2C_1A_TrustFrameworkExtensions)으로 바꿉니다.
    > [!IMPORTANT]
    >정책 이름은 signup_or_signin 정책에서 상속됩니다. 이 경우에는 `B2C_1A_TrustFrameworkExtensions`입니다.
    >예를 들어 URL은 https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**일 수 있습니다.

    ![신뢰 당사자 SAML 2.0 SSO 서비스 URL](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. **식별자 구성** 페이지에서 이전 단계와 동일한 URL을 지정하고 **추가**를 클릭하여 목록에 추가하고 **다음**을 클릭합니다.
    ![신뢰 당사자 트러스트 식별자](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  **Access Control 정책 선택**에서 정책을 선택하고 **다음**을 클릭합니다.
    ![Access Control 정책 선택](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  **트러스트 추가 준비 완료** 페이지에서 설정을 검토하고 **다음**을 클릭하여 신뢰 당사자 트러스트 정보를 저장합니다.
    ![신뢰 당사자 트러스트 정보 저장](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  **마침** 페이지에서 **닫기**를 클릭하면 이 작업이 **클레임 규칙 편집** 대화 상자를 자동으로 표시합니다.
    ![클레임 규칙 편집](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. **규칙 추가**를 클릭합니다.  
      ![새 규칙 추가](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  **클레임 규칙 템플릿**에서 **LDAP 특성을 클레임으로 전송**을 선택합니다.
    ![LDAP 특성을 클레임 템플릿 규칙으로 전송 선택](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  **클레임 규칙 이름**을 제공합니다. **특성 저장소**에서 **Active Directory 선택**을 선택합니다. 다음과 같은 클레임을 추가한 후 **마침** 및 **확인**을 클릭합니다.
    ![규칙 속성 설정](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  서버 관리자에서 **신뢰 당사자 트러스트**를 선택하고 만든 신뢰 당사자 트러스트를 선택하고 **속성**을 클릭합니다.
    ![신뢰 당사자 편집 속성](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  신뢰 당사자 트러스트(B2C 데모) 속성 창에서 **서명** 탭을 클릭하고 **추가**를 클릭합니다.  
    ![서명 설정](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  서명 인증서를 추가합니다(개인 키 없는 .cert 파일).  
    ![서명 인증서 추가](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  신뢰 당사자 트러스트(B2C 데모) 속성 창에서 **고급** 탭을 클릭하고 **보안 해시 알고리즘**을 **SHA-1**로 설정하고 **확인**을 클릭합니다.  
    ![보안 해시 알고리즘을 SHA-1로 설정](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C에 ADFS 계정 응용 프로그램 키 추가
ADFS 계정으로 페더레이션하려면 응용 프로그램 대신 Azure AD B2C를 신뢰하기 위해 ADFS 계정에 대한 클라이언트 암호가 필요합니다. Azure AD B2C 테넌트에 ADFS 인증서를 저장해야 합니다. 

1.  Azure AD B2C 테넌트로 이동하고 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.
2.  **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.
3.  **+추가**를 클릭합니다.
4.  **옵션**에는 **업로드**를 사용합니다.
5.  **이름**에는 `ADFSSamlCert`를 사용합니다.  
    `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
6.  파일 업로드에서 **개인 키가 있는 인증서 .pfx 파일을 선택합니다. 참고: 이 인증서(개인 키 포함)는 ADFS 신뢰 당사자에 발급되고 사용되는 것과 동일해야 합니다.
![정책 키 업로드](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  **만들기**
8.  `B2C_1A_ADFSSamlCert` 키를 만들었는지 확인합니다.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>확장 정책에서 클레임 공급자 추가
사용자가 ADFS 계정을 사용하여 로그인하도록 하려면 ADFS 계정을 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

확장 정책 파일에서 `<ClaimsProvider>` 노드를 추가하여 ADFS를 클레임 공급자로 정의합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).
2. `<ClaimsProviders>` 섹션을 찾습니다.
3. `ClaimsProviders` 요소 아래에서 다음 XML 코드 조각을 추가하고 `identityProvider`를 DNS(도메인을 나타내는 임의 값)로 바꾸고 파일을 저장합니다. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>등록 또는 로그인 사용자 경험에 ADFS 계정 클레임 공급자 등록
이 시점에서 ID 공급자가 설정되었습니다.  그러나 등록/로그인 화면에서 사용할 수 없습니다. 이제 `SignUpOrSignIn` 사용자 경험에 ADFS 계정 ID 공급자를 추가해야 합니다. 사용할 수 있도록 하려면 기존 템플릿 사용자 경험의 복제본을 만듭니다.  그런 다음 ADFS ID 공급자를 포함하도록 수정합니다.

>[!NOTE]
>이전에 `<UserJourneys>` 요소를 정책의 기본 파일에서 확장 파일(TrustFrameworkExtensions.xml)로 복사한 경우 이 섹션을 건너뛸 수 있습니다.

1.  정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
2.  `<UserJourneys>` 요소를 찾고 `<UserJourneys>` 노드의 전체 콘텐츠를 복사합니다.
3.  확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4.  복사한 `<UserJournesy>` 노드의 전체 콘텐츠를 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.

### <a name="display-the-button"></a>단추 표시
`<ClaimsProviderSelections>` 요소는 클레임 공급자 선택 옵션 목록과 해당 순서를 정의합니다.  `<ClaimsProviderSelection>` 요소는 등록/로그인 페이지에서 ID 공급자 단추를 사용하는 것과 유사합니다. ADFS 계정에 `<ClaimsProviderSelection>` 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1.  복사한 사용자 경험에서 `Id="SignUpOrSignIn"`이 포함된 `<UserJourney>` 노드를 찾습니다.
2.  `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
3.  `<ClaimsProviderSelections>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 ADFS 계정과 통신하여 토큰을 수신할 수 있게 됩니다. ADFS 계정 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1.  `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
2.  `<ClaimsExchanges>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * `Id`에 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값이 있는지 확인합니다.
> * `TechnicalProfileReferenceId`를 이전에 만든 기술 프로필로(Contoso-SAML2)로 설정하도록 합니다.

## <a name="upload-the-policy-to-your-tenant"></a>테넌트에 정책 업로드
1.  [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환하고 **Azure AD B2C** 블레이드를 엽니다.
2.  **ID 경험 프레임워크**를 선택합니다.
3.  **모든 정책** 블레이드를 엽니다.
4.  **정책 업로드**를 선택합니다.
5.  **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
6.  TrustFrameworkExtensions.xml을 **업로드**하고 유효성 검사가 실패하지 않았는지 확인합니다.

## <a name="test-the-custom-policy-by-using-run-now"></a>지금 실행을 사용하여 사용자 지정 정책 테스트
1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.
2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 엽니다. **지금 실행**을 선택합니다.
3.  ADFS 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[선택 사항]프로필 편집 사용자 경험에 ADFS 계정 클레임 공급자 등록
`ProfileEdit` 사용자 경험에 ADFS 계정 ID 공급자를 추가하려고 합니다. 사용할 수 있도록 하려면 마지막 두 단계를 반복합니다.

### <a name="display-the-button"></a>단추 표시
1.  정책의 확장 파일(예: TrustFrameworkExtensions.xml)을 엽니다.
2.  복사한 사용자 경험에서 `Id="ProfileEdit"`이 포함된 `<UserJourney>` 노드를 찾습니다.
3.  `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
4.  `<ClaimsProviderSelections>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결
1.  `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
2.  `<ClaimsExchanges>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>지금 실행을 사용하여 사용자 지정 프로필-편집 정책 테스트
1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.
2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_ProfileEdit**를 엽니다. **지금 실행**을 선택합니다.
3.  ADFS 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="download-the-complete-policy-files"></a>완성 정책 파일 다운로드
선택 사항: 사용자 지정 정책을 사용하여 시작을 완료한 후에 고유한 사용자 지정 정책 사용하여 시나리오를 빌드하는 것이 좋습니다. [참조 전용 정책 샘플 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
