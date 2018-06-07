---
title: Azure Active Directory의 B2B 공동 작업 사용자 클레임 매핑 | Microsoft Docs
description: Azure AD(Azure Active Directory) B2B 사용자용 SAML 토큰에 발급된 사용자 클레임을 사용자 지정합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f2617fb79fc20fcb385c7e2fc3596245ab18caba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory의 B2B 공동 작업 사용자 클레임 매핑

Azure AD(Azure Active Directory)에서는 B2B 공동 작업 사용자에 대해 SAML 토큰에 발급된 클레임을 사용자 지정할 수 있습니다. 사용자가 응용 프로그램에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 앱에 SAML 토큰을 발급합니다. 기본적으로 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함합니다.

[Azure Portal](https://portal.azure.com)에서는 SAML 토큰으로 응용 프로그램에 전송된 클레임을 보거나 편집할 수 있습니다. 설정에 액세스하려면 **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > Single Sign-On용으로 구성된 응용 프로그램 > **Single Sign-On**을 선택합니다. SAML 토큰 설정은 **사용자 특성** 섹션을 참조하세요.

![UI에 SAML 토큰 특성 표시](media/claims-mapping/view-claims-in-saml-token.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

1. 응용 프로그램에 다른 클레임 URI 또는 클레임 값 집합이 필요합니다.

2. 응용 프로그램에서 NameIdentifier 클레임은 Azure AD에 저장된 UPN(사용자 계정 이름) 이외의 것이어야 합니다.

클레임을 추가하고 편집하는 방법에 대한 자세한 내용은 [Azure Active Directory의 엔터프라이즈 응용 프로그램용 SAML 토큰에 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.

B2B 공동 작업 사용자의 경우 보안상의 이유로 테넌트 간 NameID 및 UPN 매핑이 금지됩니다.

## <a name="next-steps"></a>다음 단계

- B2B 공동 작업 사용자 속성에 대한 자세한 내용은 [Azure Active Directory B2B 공동 작업 사용자 속성](user-properties.md)을 참조하세요.
- B2B 공동 작업 사용자를 위한 사용자 토큰에 대한 자세한 내용은 [Azure AD B2B 공동 작업의 사용자 토큰 이해](user-token.md)를 참조하세요.

