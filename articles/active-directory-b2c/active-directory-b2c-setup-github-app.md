---
title: GitHub ID 공급자 구성 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C로 보호되는 응용 프로그램에서 GitHub 계정으로 고객에게 등록 및 로그인을 제공합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 17ec4ebd8406f56c431666340ca8834c5ccf9670
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: 고객에게 GitHub 계정으로 등록 및 로그인 제공

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 

이 문서에서는 GitHub 계정으로 사용자에게 로그인을 사용하도록 설정하는 방법을 보여줍니다.

## <a name="create-a-github-oauth-application"></a>GitHub OAuth 응용 프로그램 만들기

Azure AD B2C에서 GitHub를 ID 공급자로 사용하려면 GitHub OAuth 앱을 만들고 올바른 매개 변수를 제공해야 합니다.

1. GitHub에 로그인한 후 [GitHub 개발자 설정](https://github.com/settings/developers)으로 이동합니다.
1. **새 OAuth 앱**을 클릭합니다.
1. **응용 프로그램 이름** 및 **홈페이지 URL**을 입력합니다.
1. **권한 부여 콜백 URL** `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}**를 자신의 Azure AD B2C 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다.

    >[!NOTE]
    >"tenant" 값은 **로그온 URL**에서 모두 소문자여야 합니다.

1. **응용 프로그램 등록**을 클릭합니다.
1. **클라이언트 ID** 및 **클라이언트 암호** 값을 저장합니다. 다음 섹션에서 둘 다 필요합니다.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>GitHub를 Azure AD B2C 테넌트의 ID 공급자로 구성

1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
1. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
1. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
1. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "GitHub"를 입력합니다.
1. **ID 공급자 형식**을 클릭하고 **GitHub**를 선택한 다음, **확인**을 클릭합니다.
1. **이 ID 공급자 설정**을 클릭하고 이전에 만든 GitHub OAuth 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
1. **확인**을 클릭하고 **만들기**를 클릭하여 GitHub 구성을 저장합니다.

## <a name="next-steps"></a>다음 단계

[기본 제공 정책](active-directory-b2c-reference-policies.md)을 만들거나 편집하고 GitHub를 ID 공급자로 추가합니다.