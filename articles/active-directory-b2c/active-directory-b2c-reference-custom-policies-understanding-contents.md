---
title: 'Azure Active Directory B2C: 시작 팩의 사용자 지정 정책 이해 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책에 대한 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 12f63bc42f8450f086ed9f0e8d598c9c91a0c3d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Azure AD B2C 사용자 지정 정책 시작 팩의 사용자 지정 정책 이해

이 섹션에서는 **시작 팩**과 함께 제공되고 *B2C_1A_base_extensions 정책*의 상속을 통해 사용자 고유의 정책을 작성하는 데 활용되는 B2C_1A_base 정책의 모든 핵심 요소를 나열합니다.

따라서 이미 정의된 클레임 유형, 클레임 변환, 콘텐츠 정의, 기술 프로필이 있는 클레임 공급자 및 핵심 사용자 경험에 특히 중점을 둡니다.

> [!IMPORTANT]
> Microsoft는 이후 제공된 정보에 관해 어떠한 명시적 또는 묵시적 보증도 하지 않습니다. GA 시간 이전, GA 시간 또는 그 이후에 언제든지 변경 사항을 적용할 수 있습니다.

사용자 고유의 정책과 B2C_1A_base_extensions 정책은 모두 이러한 정의를 재정의하고 필요에 따라 추가 정책을 제공하여 이 상위 정책을 확장할 수 있습니다.

*B2C_1A_base 정책*의 핵심 요소는 클레임 유형, 클레임 변환 및 콘텐츠 정의입니다. 이러한 요소는 *B2C_1A_base_extensions 정책*뿐만 아니라 사용자 고유의 정책에서 쉽게 참조될 수 있습니다.

## <a name="claims-schemas"></a>클레임 스키마

이 스키마는 다음과 같은 세 가지 섹션으로 구분됩니다.

1.  첫 번째 섹션은 사용자 경험이 제대로 작동하는 데 필요한 최소 클레임을 나열합니다.
2.  두 번째 섹션은 다른 클레임 공급자에 전달할 쿼리 문자열 매개 변수 및 기타 특수 매개 변수(특히 인증을 위한 login.microsoftonline.com)에 필요한 클레임을 나열합니다. **이러한 클레임을 수정하지 마세요**.
3.  마지막으로 세 번째 섹션은 사용자로부터 수집하여 디렉터리에 저장하고 로그인 중에 토큰에 전송되는 추가, 선택적인 클레임을 나열합니다. 사용자로부터 수집 및/또는 토큰에 전송되는 새 클레임 유형을 이 섹션에 추가할 수 있습니다.

> [!IMPORTANT]
> 클레임 스키마는 암호 및 사용자 이름과 같은 특정 스키마에 대한 제한을 포함합니다. TF(보안 프레임워크) 정책은 Azure AD를 다른 클레임 공급자로 처리하며 모든 해당 제한 사항이 사용자 지정 정책에서 모델링됩니다. 더 많은 제한 사항을 추가하도록 정책을 수정하거나 사용자 고유의 제한을 포함할 자격 증명 저장소에 대해 다른 클레임 공급자를 사용할 수 있습니다.

사용 가능한 클레임 유형은 다음과 같습니다.

### <a name="claims-that-are-required-for-the-user-journeys"></a>사용자 경험에 필요한 클레임

사용자 경험이 제대로 작동하려면 다음 클레임이 필요합니다.

| 클레임 유형 | 설명 |
|-------------|-------------|
| *UserId* | 사용자 이름 |
| *signInName* | 로그인 이름 |
| *tenantId* | Azure AD B2C에서 사용자 개체의 테넌트 식별자(ID) |
| *objectId* | Azure AD B2C에서 사용자 개체의 개체 식별자(ID) |
| *암호* | 암호 |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | 암호 강도, 만료 등을 결정하기 위해 Azure AD B2C에 사용된 암호 정책 |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | 사용자의 전화 번호 |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | 사용자에게 연락하는 데 사용할 수 있는 이메일 주소 |
| *signInNamesInfo.emailAddress* | 사용자가 로그인에 사용할 수 있는 이메일 주소 |
| *otherMails* | 사용자에게 연결하는 데 사용할 수 있는 이메일 주소 |
| *userPrincipalName* | Azure AD B2C에 저장된 사용자 이름 |
| *upnUserName* | 사용자 계정 이름을 만들기 위한 사용자 이름 |
| *mailNickName* | Azure AD B2C에 저장된 사용자의 메일 별명 |
| *newUser* | |
| *executed-SelfAsserted-Input* | 사용자로부터 특성을 수집할지 여부를 지정하는 클레임 |
| *executed-PhoneFactor-Input* | 사용자로부터 새 전화 번호를 수집할지 여부를 지정하는 클레임 |
| *authenticationSource* | 사용자가 소셜 ID 공급자, login.microsoftonline.com 또는 로컬 계정에서 인증되었는지 여부를 지정합니다. |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>쿼리 문자열 매개 변수 및 기타 특수 매개 변수에 필요한 클레임

특수 매개 변수(일부 쿼리 문자열 매개 변수 포함)에서 다른 클레임 공급자로 전달하는 데 다음 클레임이 필요합니다.

| 클레임 유형 | 설명 |
|-------------|-------------|
| *nux* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *nca* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *prompt* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *mkt* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *lc* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *grant_type* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *scope* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *client_id* | 로컬 계정 인증을 위해 login.microsoftonline.com에 전달하는 특수 매개 변수 |
| *objectIdFromSession* | 개체 ID가 SSO 세션에서 검색되었음을 나타내기 위해 기본 세션 관리 공급자가 제공한 매개 변수 |
| *isActiveMFASession* | 사용자가 활성 MFA 세션을 포함하고 있음을 나타내기 위해 MFA 세션 관리에서 제공한 매개 변수 |

### <a name="additional-optional-claims-that-can-be-collected"></a>수집할 수 있는 추가(또는 선택적) 클레임

다음 클레임은 사용자로부터 수집하여 디렉터리에 저장하고 토큰에 전송되는 추가 클레임입니다. 전에 설명한 바와 같이 추가 클레임은 이 목록에 추가할 수 있습니다.

| 클레임 유형 | 설명 |
|-------------|-------------|
| *givenName* | 사용자의 지정된 이름(이름이라고도 함) |
| *surname* | 사용자의 성(패밀리 이름 또는 성이라고도 함) |
| *Extension_picture* | 소셜에서 사용자의 그림 |

## <a name="claim-transformations"></a>클레임 변환

사용 가능한 클레임 변환은 다음과 같습니다.

| 클레임 변환 | 설명 |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>콘텐츠 정의

이 섹션에서는 *B2C_1A_base* 정책에 이미 선언된 콘텐츠 정의를 설명합니다. 이러한 콘텐츠 정의는 *B2C_1A_base_extensions* 정책뿐만 아니라 사용자 고유의 정책에서 필요에 따라 쉽게 참조, 재정의 및/또는 확장될 수 있습니다.

| 클레임 공급자 | 설명 |
|-----------------|-------------|
| *Facebook* | |
| *로컬 계정 로그인* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *자체 어설션됨* | |
| *로컬 계정* | |
| *세션 관리* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *토큰 발급자* | |

## <a name="technical-profiles"></a>기술 프로필

이 섹션에서는 *B2C_1A_base* 정책에서 클레임 공급자마다 이미 선언된 기술 프로필을 보여 줍니다. 이러한 기술 프로필은 *B2C_1A_base_extensions* 정책뿐만 아니라 사용자 고유의 정책에서 필요에 따라 쉽게 참조, 재정의 및/또는 확장될 수 있습니다.

### <a name="technical-profiles-for-facebook"></a>Facebook용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>로컬 계정 로그인용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Phone Factor용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Azure Active Directory용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *AAD-Common* | 다른 AAD-xxx 기술 프로필에 포함된 기술 프로필 |
| *AAD-UserWriteUsingAlternativeSecurityId* | 소셜 로그인용 기술 프로필 |
| *AAD-UserReadUsingAlternativeSecurityId* | 소셜 로그인용 기술 프로필 |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | 소셜 로그인용 기술 프로필 |
| *AAD-UserWritePasswordUsingLogonEmail* | 로컬 계정을 위한 기술 프로필 |
| *AAD-UserReadUsingEmailAddress* | 로컬 계정을 위한 기술 프로필 |
| *AAD-UserWriteProfileUsingObjectId* | objectId를 사용하여 사용자 레코드 업데이트를 위한 기술 프로필 |
| *AAD-UserWritePhoneNumberUsingObjectId* | objectId를 사용하여 사용자 레코드 업데이트를 위한 기술 프로필 |
| *AAD-UserWritePasswordUsingObjectId* | objectId를 사용하여 사용자 레코드 업데이트를 위한 기술 프로필 |
| *AAD-UserReadUsingObjectId* | 사용자 인증 후 데이터를 읽는 데 사용되는 기술 프로필 |

### <a name="technical-profiles-for-self-asserted"></a>자체 어설션용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>로컬 계정용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>세션 관리용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | 프로필 이름은 등록 및 로그인 간에 AAD 세션을 구분하는 데 사용됨 |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Trustframework Policy Engine TechnicalProfiles용 기술 프로필

현재는 **Trustframework Policy Engine TechnicalProfiles** 클레임 공급자에 대해 정의된 기술 프로필이 없습니다.

### <a name="technical-profiles-for-token-issuer"></a>토큰 발급자용 기술 프로필

| 기술 프로필 | 설명 |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>사용자 경험

이 섹션에서는 *B2C_1A_base* 정책에 이미 선언된 사용자 경험을 보여 줍니다. 이러한 사용자 경험은 *B2C_1A_base_extensions* 정책뿐만 아니라 사용자 고유의 정책에서 필요에 따라 쉽게 참조, 재정의 및/또는 확장될 수 있습니다.

| 사용자 경험 | 설명 |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
