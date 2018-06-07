---
title: 'Azure Active Directory B2C: Multi-Factor Authentication | Microsoft Docs'
description: Azure Active Directory B2C에서 보호하는 소비자 지향 응용 프로그램에서 Multi-Factor Authentication을 사용하는 방법
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f439c65d9b4fbeb78623312c426767f89dfd9fab
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: 소비자 지향 응용 프로그램에서 Multi-Factor Authentication 사용
Azure Active Directory(Azure AD) B2C는 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) 에 직접 통합하여 소비자 지향 응용 프로그램에서 등록 및 로그인하는 데 두 번째 보안 계층을 추가할 수 있습니다. 이 작업을 한 줄의 코드도 작성하지 않고 수행할 수 있습니다. 현재 전화 통화 및 문자 메시지를 확인을 지원합니다. 이미 등록 및 로그인 정책을 만든 경우에도 다단계 인증을 사용할 수 있습니다.

> [!NOTE]
> 단지 기존 정책을 편집하지 않고 등록 및 로그인 정책을 만들 때 다단계 인증을 사용할 수도 있습니다.
> 
> 

이 기능은 응용 프로그램에서 다음과 같은 시나리오를 처리하는 데 도움이 됩니다.

* 하나의 응용 프로그램에 액세스하려는 경우 다단계 인증이 필요하지 않지만 다른 응용 프로그램에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 소비자는 소셜 또는 로컬 계정을 사용하여 자동차 보험 응용 프로그램에 로그인할 수 있지만 동일한 디렉터리에 등록된 주택 보험 응용 프로그램에 액세스하기 전에 전화번호를 확인해야 합니다.
* 일반적으로 응용 프로그램에 액세스하는 데 다단계 인증이 필요하지 않지만 그 안에 중요한 부분에 액세스하하려면 다단계 인증이 필요합니다. 예를 들어 소비자는 소셜 또는 로컬 계정으로 은행 응용 프로그램에 로그인하고 계정 잔액을 확인할 수 있지만 유선 전송을 시도하기 전에 전화번호를 확인해야 합니다.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>다단계 인증을 사용하도록 등록 정책을 수정합니다
1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. **등록 정책**을 클릭합니다.
3. 사용자의 등록 정책(예: "B2C_1_SiUp")을 클릭하여 엽니다.
4. **Multi-Factor Authentication**을 클릭하고 **상태**를 **켜기**로 바꿉니다. **확인**을 클릭합니다.
5. 블레이드 위쪽에서 **저장**을 클릭합니다.

정책에서 "지금 실행" 기능을 사용하여 고객 환경을 확인할 수 있습니다. 다음을 확인합니다.

다단계 인증 단계가 실행되기 전에 사용자의 디렉터리에 소비자 계정이 생성됩니다. 이 단계에서 소비자에게 자신의 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 소비자 계정에 연결됩니다. 소비자가 취소 또는 삭제하더라도 다음에 로그인하는 동안 다시 전화 번호를 확인하도록 요청할 수 있습니다(다단계 인증을 사용하도록 설정한 경우).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>다단계 인증을 사용하도록 로그인 정책 수정
1. [다음 단계에 따라 Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. **로그인 정책**을 클릭합니다.
3. 사용자의 로그인 정책(예: "B2C_1_SiUp")을 클릭하여 엽니다. 블레이드 위쪽에서 **편집**을 클릭합니다.
4. **Multi-Factor Authentication**을 클릭하고 **상태**를 **켜기**로 바꿉니다. **확인**을 클릭합니다.
5. 블레이드 위쪽에서 **저장**을 클릭합니다.

정책에서 "지금 실행" 기능을 사용하여 고객 환경을 확인할 수 있습니다. 다음을 확인합니다.

소비자가 (소셜 또는 로컬 계정을 사용하여)로그인할 때 확인된 전화번호가 소비자 계정에 연결된 경우 확인하도록 요청을 받습니다. 전화 번호가 연결되지 않은 경우 소비자에게 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 소비자 계정에 연결됩니다.

## <a name="multi-factor-authentication-on-other-policies"></a>기타 정책에서 Multi-Factor Authentication
위의 등록 및 로그인 정책에 설명한 대로 등록 또는 로그인 정책과 암호 재설정 정책에 Multi-Factor Authentication을 사용하도록 설정할 수 있습니다. 정책을 편집하는 프로필에서 곧 지원됩니다.

