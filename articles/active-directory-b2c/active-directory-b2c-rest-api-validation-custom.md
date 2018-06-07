---
title: 'Azure Active Directory B2C: REST API 클레임 교환을 유효성 검사로 통합 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책에 대한 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: 31ed69b669a92ad38c4ab7b2f54886e4ed6acf42
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력에 대한 유효성 검사로 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)의 기반이 되는 IEF(ID 경험 프레임워크)를 사용하면 ID 개발자가 사용자 경험에서 RESTful API와의 상호 작용을 통합할 수 있습니다.  

이 연습의 끝부분에서는 RESTful 서비스와 상호 작용하는 Azure AD B2C 사용자 경험을 만들 수 있습니다.

IEF는 클레임으로 데이터를 보내고 다시 클레임으로 데이터를 받습니다. API와의 상호 작용은 다음과 같습니다.

- API와 상호 작용을 오케스트레이션 단계 내부에서 발생하는 REST API 클레임 교환 또는 유효성 검사 프로필로 설계할 수 있습니다.
- 일반적으로 사용자 입력의 유효성을 검사합니다. 사용자의 값이 거부되면 사용자는 오류 메시지를 반환하는 기회와 함께 유효한 값을 다시 입력하려고 시도할 수 있습니다.

또한 상호 작용은 오케스트레이션 단계로도 설계할 수 있습니다. 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](active-directory-b2c-rest-api-step-custom.md)을 참조하세요.

유효성 검사 프로필 예제의 경우 ProfileEdit.xml 시작 팩 파일에서 프로필 편집 사용자 경험을 사용합니다.

프로필 편집에서 사용자가 제공한 이름이 제외 목록에 속하지 않은 것을 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 로컬 계정 등록/로그인을 완료하도록 구성된 Azure AD B2C 테넌트
- 상호 작용할 REST API 끝점 이 연습에서는 REST API 서비스를 사용하여 [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/)라는 데모 사이트를 설정했습니다.

## <a name="step-1-prepare-the-rest-api-function"></a>1단계 - REST API 함수 준비

> [!NOTE]
> REST API 함수 설정은 이 문서의 범위를 벗어납니다. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference)는 클라우드에서 RESTful 서비스를 만들 수 있는 뛰어난 도구 키트를 제공합니다.

`playerTag`로 예상하는 클레임을 받는 Azure 함수를 만들었습니다. 함수는 이 클레임의 존재 여부에 대한 유효성을 검사합니다. 전체 Azure Function 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples)에서 액세스할 수 있습니다.

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

IEF는 Azure 함수에서 반환하는 `userMessage` 클레임을 예상합니다. 위의 예제에서는 409 충돌 상태가 반환된 경우와 같이 유효성 검사에 실패하면 이 클레임이 사용자에게 문자열로 표시됩니다.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>2단계 - TrustFrameworkExtensions.xml 파일에서 RESTful API 클레임 교환을 기술 프로필로 구성

기술적 프로필은 RESTful 서비스에서 원하는 교환의 전체 구성입니다. TrustFrameworkExtensions.xml 파일을 열고 `<ClaimsProviders>` 요소 내에 다음 XML 코드 조각을 추가합니다.

> [!NOTE]
> 다음 XML에서 `Version=1.0.0.0` RESTful 공급자는 프로토콜로 설명되며, 외부 서비스와 상호 작용할 함수로 간주됩니다. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` 요소는 IEF에서 REST 서비스로 전송할 클레임을 정의합니다. 이 예제에서는 REST 서비스에 `givenName` 클레임의 내용을 `playerTag`로 보냅니다. 그리고 IEF에서 클레임을 다시 예상하지 않습니다. 대신 REST 서비스의 응답을 기다리고, 받는 상태 코드에 따라 작동합니다.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>3단계 - 사용자 입력의 유효성을 검사하려는 자체 어설션된 기술 프로필에 RESTful 서비스 클레임 교환을 포함

유효성 검사의 가장 일반적인 용도는 사용자와의 상호 작용입니다. 사용자가 입력을 제공해야 하는 모든 상호 작용은 *자체 어설션된 기술 프로필*입니다. 이 예제에서는 Self-Asserted-ProfileUpdate 기술 프로필에 유효성 검사를 추가합니다. 이는 `Profile Edit` RP(신뢰 당사자) 정책 파일에서 사용하는 기술 프로필입니다.

자체 어설션된 기술 프로필에 클레임 교환을 추가하려면

1. TrustFrameworkBase 파일을 열고 `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`를 검색합니다.
2. 이 기술 프로필의 구성을 검토합니다. 사용자와의 교환이 사용자에게 요청할 클레임(입력 클레임) 및 자체 어설션된 공급자에서 반환될 클레임(출력 클레임)으로 정의되는 방식을 검토합니다.
3. `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`를 검색합니다. 이 프로필이 `<UserJourney Id="ProfileEdit">`의 오케스트레이션 4단계로 호출된다는 것을 확인합니다.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>4단계 - 프로필 편집 RP 정책 파일 업로드 및 테스트

1. 새 버전의 TrustFrameworkExtensions.xml 정책 파일을 업로드합니다.
2. **지금 실행**을 사용하여 프로필 편집 RP 정책 파일을 테스트합니다.
3. **지정된 이름** 필드에 기존 이름(예: mcvinny) 중 하나를 제공하여 유효성 검사를 테스트합니다. 모든 항목이 올바르게 설정되면 플레이어 태그가 이미 사용되었음을 사용자에게 알리는 메시지를 받아야 합니다.

## <a name="next-steps"></a>다음 단계

[프로필 편집 및 사용자 등록을 수정하여 사용자로부터 추가 정보 수집](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](active-directory-b2c-rest-api-step-custom.md)
