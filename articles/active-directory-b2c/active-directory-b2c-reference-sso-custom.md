---
title: 사용자 지정 정책을 사용하는 SSO 세션 관리 - Azure AD B2C | Microsoft Docs
description: Azure AD B2C에서 사용자 지정 정책을 사용하여 SSO 세션을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.openlocfilehash: ca7160d39d5d26ca69345ce636f22afbe44b25db
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: SSO(Single Sign-On) 세션 관리

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C를 사용하면 관리자가 이미 인증된 후에 Azure AD B2C에서 사용자와 상호 작용하는 방법을 제어할 수 있습니다. 이 작업은 SSO 세션 관리를 통해 수행됩니다. 예를 들어 관리자는 ID 공급자의 선택이 표시되는지 여부 또는 로컬 계정 세부 정보를 다시 입력해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

## <a name="overview"></a>개요

SSO 세션 관리에는 두 가지 부분이 있습니다. 첫 번째 부분은 Azure AD B2C와 사용자의 직접 상호 작용을 다루고, 다른 부분은 Facebook과 같은 외부 당사자와 사용자의 상호 작용을 다룹니다. Azure AD B2C는 외부 당사자에 의해 보관될 수 있는 SSO 세션을 재정의하거나 무시하지 않습니다. 외부 당사자에게 가져오는 Azure AD B2C를 통한 경로를 "기억"하여 사용자에게 해당 소셜 공급자 또는 엔터프라이즈 ID 공급자를 선택하라는 메시지를 다시 표시하지 않도록 합니다. 최종 SSO 결정은 외부 당사자에서 유지됩니다.

## <a name="how-does-it-work"></a>작동 원리

SSO 세션 관리는 사용자 지정 정책에서 다른 기술 프로필과 동일한 의미 체계를 사용합니다. 오케스트레이션 단계를 실행하면 이 단계와 관련된 기술 프로필을 `UseTechnicalProfileForSessionManagement` 참조에 대해 쿼리합니다. 참조된 SSO 세션 공급자가 있는 경우 사용자가 세션 참가자인지를 확인합니다. 그렇다면 SSO 세션 공급자를 사용하여 세션을 다시 채웁니다. 마찬가지로 오케스트레이션 단계의 실행이 완료되면 SSO 세션 공급자가 지정된 경우 이 공급자를 사용하여 세션에서 정보를 저장합니다.

Azure AD B2C는 사용할 수 있는 SSO 세션 공급자 수를 정의합니다.

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

기술 프로필의 `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` 요소를 사용하여 SSO 관리 클래스를 지정합니다.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

이름이 지정하는 대로 이 공급자는 아무 작업도 수행하지 않습니다. 특정 기술 프로필에 대한 SSO 동작을 무시하기 위해 이 공급자를 사용할 수 있습니다.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

세션에서 클레임을 저장하는 데 이 공급자를 사용할 수 있습니다. 이 공급자는 로컬 계정을 관리하기 위해 사용되는 기술 프로필에서 일반적으로 참조됩니다. 

> [!NOTE]
> DefaultSSOSessionProvider를 사용하여 세션에 클레임을 저장하는 경우 응용 프로그램에 반환되거나 이후 단계의 사전 조건에 사용되어야 하는 모든 클레임이 해당 세션에 저장되어 있거나 디렉터리에 있는 사용자 프로필에서의 읽기로 확대되는지 확인해야 합니다. 이렇게 하면 인증 과정이 누락된 클레임에서도 실패하지 않게 됩니다.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

세션에서 클레임을 추가하려면 기술 프로필의 `<PersistedClaims>` 요소를 사용합니다. 세션을 다시 채우는 데 공급자를 사용하는 경우 유지되는 클레임은 클레임 모음에 추가됩니다. `<OutputClaims>`는 세션에서 클레임을 검색하는 데 사용됩니다.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

이 공급자는 "ID 공급자 선택" 화면을 무시하는 데 사용됩니다. 일반적으로 Facebook 등의 외부 ID 공급자에 구성된 기술 프로필에서 참조됩니다. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

이 공급자는 앱과 외부 SAML ID 공급자 간의 Azure AD B2C SAML 세션을 관리하는 데 사용됩니다.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

기술 프로필에는 두 개의 메타데이터 항목이 있습니다.

| 항목 | 기본값 | 가능한 값 | 설명
| --- | --- | --- | --- |
| IncludeSessionIndex | true | True/False | 공급자에게 세션 인덱스를 저장해야 함을 의미합니다. |
| RegisterServiceProviders | true | True/False | 공급자가 어설션을 발급한 모든 SAML 서비스 공급자를 등록해야 함을 의미합니다. |

SAML ID 공급자 세션을 저장하는 데 공급자를 사용하는 경우 위의 항목은 둘 다 false여야 합니다. B2C SAML 세션을 저장하는 데 공급자를 사용하는 경우 위의 항목은 true이거나 기본값이 true인 경우 생략되어야 합니다.

>[!NOTE]
> SAML 세션 로그아웃을 완료하는 데 `SessionIndex` 및 `NameID`가 필요합니다.

## <a name="next-steps"></a>다음 단계

사용자 의견 및 제안을 환영합니다. 이 항목에 문제가 있는 경우 태그 ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c)를 사용하여 Stack Overflow에 게시하세요. 기능을 요청하려면 [피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에서 투표하세요.

