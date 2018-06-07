---
title: 'Azure Active Directory B2C: 사용자 고유의 특성을 사용자 지정 정책에 추가하고 프로필 편집에 사용 | Microsoft Docs'
description: 확장 속성, 사용자 지정 특성을 사용하고 사용자 인터페이스에 포함하는 방법에 대한 연습
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
ms.openlocfilehash: e0595a67b90e1be7bb992ef7bda4343e692d8957
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: 사용자 지정 프로필 편집 정책에서 사용자 지정 특성을 만들고 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C 디렉터리에 사용자 지정 특성을 만들고 이 새로운 특성을 프로필 편집 사용자 경험에서 사용자 지정 클레임으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>사용자 지정 특성을 사용하여 사용자 지정 정책을 사용하는 Azure Active Directory B2C에서 고객에 대한 정보를 수집합니다.
Azure Active Directory(Azure AD) B2C 디렉터리에는 지정된 이름, 성, 도시, 우편 번호, userPrincipalName의 특성 집합이 함께 제공됩니다.  주로 고유한 특성을 만들어야 합니다.  예: 
* 고객 대면 응용 프로그램은 "LoyaltyNumber"와 같은 특성을 유지해야 합니다.
* ID 공급자는 "uniqueUserGUID"처럼 저장해야 하는 고유한 사용자 ID를 포함합니다.
* 사용자 지정 사용자 경험에서는 "migrationStatus"와 같은 사용자 상태를 유지해야 합니다.

Azure AD B2C를 사용하면 각 사용자 계정에 저장된 특성 집합을 확장할 수 있습니다. 또한 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 이러한 특성을 읽고 쓸 수 있습니다.

확장 속성은 디렉터리에서 사용자 개체의 스키마를 확장합니다.  조건 확장 속성, 사용자 지정 특성 및 사용자 지정 클레임은 이 문서의 컨텍스트에서 동일한 항목을 참조하고 이름은 컨텍스트(응용 프로그램, 개체, 정책)에 따라 달라집니다.

확장 속성은 사용자에 대한 데이터를 포함할 수 있더라도 응용 프로그램 개체에만 등록할 수 있습니다. 이 속성은 응용 프로그램에 연결됩니다. 확장 속성을 등록하려면 응용 프로그램 개체에 쓰기 권한이 있어야 합니다. 단일 개체에 100개의 확장 속성(모든 형식 및 모든 응용 프로그램)을 작성할 수 있습니다. 확장 속성이 대상 디렉터리 유형에 추가되고 Azure AD B2C 디렉터리 테넌트에서 즉시 액세스할 수 있게 됩니다.
응용 프로그램이 삭제되면 모든 사용자에 대해 포함된 모든 데이터와 함께 해당 확장 속성도 제거됩니다. 확장 속성이 응용 프로그램에 의해 삭제되면 대상 디렉터리 개체에서 제거되고 값이 삭제됩니다.

확장 속성은 테넌트의 등록된 응용 프로그램 컨텍스트에서만 존재합니다. 응용 프로그램의 개체 ID는 ID를 사용하는 TechnicalProfile에 포함되어야 합니다.

>[!NOTE]
>Azure AD B2C 디렉터리는 일반적으로 `b2c-extensions-app`으로 명명된 Web App을 포함합니다.  이 응용 프로그램은 주로 Azure Portal을 통해 만든 사용자 지정 클레임에 대한 b2c 기본 제공 정책에 사용됩니다.  이 응용 프로그램을 사용하여 b2c 사용자 지정 정책의 확장을 등록하는 것은 고급 사용자에게만 권장됩니다.  이에 대한 지침은 이 문서의 다음 단계 섹션에 포함되어 있습니다.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>확장 속성을 저장할 새 응용 프로그램 만들기

1. 브라우저 세션을 열고 [Azure Portal](https://portal.azure.com)로 이동하여 구성할 B2C 디렉터리의 관리 자격 증명으로 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 클릭합니다. 추가 서비스>를 선택하여 서비스를 찾아야 합니다.
1. **앱 등록**을 선택하고 **새 응용 프로그램 등록**을 클릭합니다.
1. 다음 권장되는 항목을 제공합니다.
  * 웹 응용 프로그램의 이름 지정: **WebApp-GraphAPI-DirectoryExtensions**
  * 응용 프로그램 유형: 웹앱/API
  * 로그온 URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. **만들기를 선택합니다. 성공적인 완료가 **알림**에 표시됩니다.
1. 새로 만든 **WebApp-GraphAPI-DirectoryExtensions** 웹 응용 프로그램을 선택합니다.
1. **필요한 권한** 설정을 선택합니다.
1. **Windows Azure Active Directory** API 선택
1. **디렉터리 데이터 읽기 및 쓰기** 응용 프로그램 권한을 확인 표시하고 **저장**을 선택합니다.
1. **사용 권한 부여**를 선택하고 **예**를 확인합니다.
1. 클립보드에 복사하고 WebApp-GraphAPI-DirectoryExtensions>Settings>Properties>에서 다음 ID를 저장합니다.
*  **응용 프로그램 ID** - 예제: `103ee0e6-f92d-4183-b576-8c3739027780`
* **개체 ID** - 예제: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>사용자 지정 정책을 수정하여 ApplicationObjectId 추가

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
>해당 요소가 해당 요소를 사용하여 모든 Azure Active Directory TechnicalProfiles에 포함되고 재사용되기 때문에 <TechnicalProfile Id="AAD-Common">은 "공용"입니다.`<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>TechnicalProfile이 처음 새로 작성된 확장 속성에 작성되는 경우 일회성 오류가 발생할 수 있습니다.  확장 속성은 처음 사용할 때 만들어집니다.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>사용자 경험에서 새 확장 속성/사용자 지정 특성 사용


1. 정책 편집 사용자 경험을 설명하는 신뢰 당사자(RP) 파일을 엽니다.  시작하는 경우 Azure Portal의 Azure B2C 사용자 지정 정책 섹션에서 RP-PolicyEdit 파일의 이미 구성된 버전을 직접 다운로드하는 것이 좋습니다.  또는 저장소 폴더에서 XML 파일을 엽니다.
2. 사용자 지정 클레임 `loyaltyId`를 추가합니다.  `<RelyingParty>` 요소에 사용자 지정 클레임을 포함하면 UserJourney TechnicalProfiles에 매개 변수로 전달되고 응용 프로그램의 토큰에 포함됩니다.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. 표시된 것처럼 `<ClaimsSchema>` 요소 내부 확장 정책 파일 `TrustFrameworkExtensions.xml`에 클레임 정의를 추가합니다.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. 동일한 클레임 정의를 기본 정책 파일 `TrustFrameworkBase.xml`에 추가합니다.  
>기본 및 확장 파일 모두에 `ClaimType` 정의를 추가하는 것은 일반적으로 필요하지 않지만, 다음 단계에서 기본 파일의 TechnicalProfiles에 extension_loyaltyId를 추가하므로 정책 유효성 검사기에서 해당 정의가 없는 기본 파일의 업로드를 거부합니다.
>TrustFrameworkBase.xml 파일에서 "ProfileEdit"라는 사용자 경험의 실행을 추적하는 데 유용할 수 있습니다.  편집기에서 같은 이름의 사용자 경험을 검색하고 오케스트레이션 5단계에서 TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate"를 호출하는 것을 확인합니다.  이 TechnicalProfile을 검색 및 검사하여 흐름에 익숙해 지도록 합니다.
5. TechnicalProfile "SelfAsserted-ProfileUpdate"에서 입력 및 출력 클레임으로 loyaltyId를 추가합니다.
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. TechnicalProfile "AAD-UserWriteProfileUsingObjectId"에서 클레임을 추가하여 클레임 값을 디렉터리의 현재 사용자에 대한 확장 속성에서 유지합니다.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. TechnicalProfile "AAD-UserReadUsingObjectId"에서 클레임을 추가하여 사용자가 로그인할 때마다 확장 특성 값을 읽어 옵니다. 지금까지 로컬 계정의 흐름에서만 TechnicalProfiles가 변경되었습니다.  소셜/페더레이션된 계정의 흐름에 새 특성이 필요한 경우 다른 TechnicalProfiles 집합을 변경해야 합니다. 다음 단계를 참조하세요.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>IncludeTechnicalProfile 요소는 AAD-Common의 모든 요소를 이 TechnicalProfile에 추가합니다.

## <a name="test-the-custom-policy-using-run-now"></a>"지금 실행"을 사용하여 사용자 지정 정책 테스트
1. **Azure AD B2C 블레이드**를 열고 **ID 경험 프레임워크 > 사용자 지정 정책**로 이동합니다.
1. 업로드한 사용자 지정 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 전자 메일 주소를 사용하여 등록할 수 있습니다.

응용 프로그램으로 다시 전송된 ID 토큰에는 extension_loyaltyId가 앞에 오는 사용자 지정 클레임으로 새로운 확장 속성이 포함됩니다. 예제를 참조하세요.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>다음 단계

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>아래 나열된 TechnicalProfiles를 변경하여 새 클레임을 소셜 계정 로그인에 대한 흐름에 추가합니다. 이러한 두 TechnicalProfiles는 사용자 개체의 로케이터로 alternativeSecurityId를 사용하여 사용자 데이터를 쓰고 읽기 위해 소셜/페더레이션된 계정 로그인에 사용됩니다.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

기본 및 사용자 지정 정책 간에 동일한 확장 특성을 사용합니다.
포털 환경을 통해 확장 특성(즉, 사용자 지정 특성)을 추가하는 경우 해당 특성은 모든 b2c 테넌트에 존재하는 **b2c-extensions-app을 사용하여 등록됩니다.  사용자 지정 정책에서 이러한 확장 특성을 사용하려면:
1. portal.azure.com의 b2c 테넌트 내에서 **Azure Active Directory**로 이동하고 **앱 등록** 선택
2. **b2c-확장-앱**을 찾고 선택
3. 'Essentials' 아래에서 **응용 프로그램 ID** 및 **개체 ID** 기록
4. 다음과 같이 AAD 공용 기술 프로필 메타데이터에 포함:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

포털 환경과 일관성을 유지하기 위해 사용자 지정 정책에서 사용하기 *전에* 포털 UI를 사용하여 이러한 특성을 만듭니다.  포털에서 "ActivationStatus" 특성을 만들 때 다음과 같이 참조해야 합니다.

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>참고 자료

* **TP(기술 프로필)**은 끝점의 이름, 해당 메타데이터, 해당 프로토콜을 정의하고 Identity Experience Framework가 수행해야 하는 클레임의 교환에 대해 자세히 설명하는 *함수*로 간주할 수 있는 요소 유형입니다.  오케스트레이션 단계 또는 다른 TechnicalProfile에서 이 *함수*를 호출하면 InputClaims 및 OutputClaims가 호출자의 매개 변수로 제공됩니다.


* 확장 속성에 대한 완전한 처리는 [디렉터리 스키마 확장 | Graph API 개념](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) 문서를 참조하세요.

>[!NOTE]
>Graph API의 확장 특성은 `extension_ApplicationObjectID_attributename` 규칙을 사용하여 명명됩니다. 사용자 지정 정책은 확장 특성을 extension_attributename으로 참조하므로 XML에서 ApplicationObjectId가 생략됩니다.
