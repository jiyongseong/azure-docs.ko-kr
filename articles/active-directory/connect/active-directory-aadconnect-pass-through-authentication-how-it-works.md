---
title: "Azure AD Connect: 통과 인증 - 작동 방식 | Microsoft Docs"
description: "이 문서에서는 Azure Active Directory 통과 인증이 작동하는 방식을 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: billmath
ms.openlocfilehash: eaa9995430833c0c087ed0d4044f6c41d254e3ff
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 통과 인증: 기술 심층 분석
이 문서에서는 Azure AD(Azure Active Directory) 통과 인증이 작동하는 방식에 대해 간략히 설명합니다. 심층적인 기술 및 보안 정보는 [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) 문서를 참조하세요.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 통과 인증 작동 방식은?

사용자가 Azure AD에서 보호되는 응용 프로그램에 로그인하려고 하고 통과 인증이 테넌트에서 사용하도록 설정되어 있으면 다음 단계가 수행됩니다.

1. 사용자가 응용 프로그램(예: [Outlook 웹앱](https://outlook.office365.com/owa/))에 액세스하려고 합니다.
2. 사용자가 아직 로그인하지 않았으면 해당 사용자는 Azure AD **사용자 로그인** 페이지로 리디렉션됩니다.
3. 사용자가 Azure AD 로그인 페이지에 사용자 이름과 암호를 입력하고 **로그인** 단추를 선택합니다.
4. Azure AD에서 로그인 요청을 받으면 공개 키로 암호화된 사용자 이름과 암호를 큐에 넣습니다.
5. 온-프레미스 인증 에이전트는 큐에서 사용자 이름 및 암호화된 암호를 검색합니다. 에이전트는 큐의 요청을 자주 폴링하지 않고, 미리 설정된 영구 연결을 통해 요청을 검색합니다.
6. 에이전트에서 해당 개인 키를 사용하여 암호를 해독합니다.
7. 에이전트에서 표준 Windows API(AD FS(Active Directory Federation Services)에서 사용하는 것과 비슷한 메커니즘)를 사용하여 Active Directory에 대한 사용자 이름과 암호의 유효성을 검사합니다. 사용자 이름은 온-프레미스 기본 사용자 이름(일반적으로 `userPrincipalName`) 또는 Azure AD Connect에 구성된 또 다른 특성(`Alternate ID`라고 함) 중 하나일 수 있습니다.
8. 온-프레미스 Active Directory DC(도메인 컨트롤러)에서 요청을 평가하고, 적절한 응답(성공, 실패, 암호가 만료됨 또는 사용자가 잠겨 있음)을 에이전트에 반환합니다.
9. 그러면 인증 에이전트가 이 응답을 다시 Azure AD로 반환합니다.
10. Azure AD에서 응답을 평가하고 사용자에게 적절하게 응답합니다. 예를 들어 Azure AD가 즉시 사용자에게 로그인하거나 Azure Multi-Factor Authentication을 요청합니다.
11. 사용자 로그인에 성공하면 사용자가 응용 프로그램에 액세스할 수 있습니다.

다음 다이어그램에서는 관련된 모든 구성 요소와 단계를 보여 줍니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>다음 단계
- [현재 제한 사항](active-directory-aadconnect-pass-through-authentication-current-limitations.md): 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [빠른 시작](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD 통과 인증을 구성하고 실행합니다.
- [스마트 잠금](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호합니다.
- [질문과 대답](active-directory-aadconnect-pass-through-authentication-faq.md): 자주 묻는 질문에 대한 대답을 찾아봅니다.
- [문제 해결](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 통과 인증 기능에 대한 자세한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](active-directory-aadconnect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.

