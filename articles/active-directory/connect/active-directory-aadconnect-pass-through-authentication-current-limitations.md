---
title: 'Azure AD Connect: 통과 인증 - 현재 제한 사항 | Microsoft Docs'
description: 이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 문제의 현재 제한 사항에 대해 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 680e9967010771b8e3651c6f4eed81237f8fb4c3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 통과 인증: 현재 제한 사항

>[!IMPORTANT]
>Azure AD(Azure Active Directory) 통과 인증은 무료 기능이며, 이 기능을 사용하기 위해 Azure AD 유료 버전이 필요하지 않습니다. 통과 인증은 Azure AD의 전 세계 인스턴스에서만 사용할 수 있고, [Microsoft Azure 독일 클라우드](http://www.microsoft.de/cloud-deutschland) 또는 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서는 사용할 수 없습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 시나리오는 완전히 지원됩니다.

- 사용자가 모든 웹 브라우저 기반 응용 프로그램에 로그인합니다.
- 사용자가 [최신 인증](https://aka.ms/modernauthga)을 지원하는 Office 응용 프로그램(최신 인증을 _사용하는_ Office 2013 및 Office 2016)에 로그인합니다.
- 사용자는 로그인 Exchange ActiveSync, SMTP, POP 및 IMAP 등의 레거시 프로토콜을 사용하여 Outlook 클라이언트에 로그인합니다.
- 사용자가 온라인 및 하이브리드 토폴로지를 포함하여 최신 인증을 지원하는 비즈니스용 Skype로 로그인합니다. 지원되는 토폴로지는 [여기](https://technet.microsoft.com/library/mt803262.aspx)를 참조하세요.
- Windows 10 장치에 대한 Azure AD 도메인 가입.
- Multi-Factor Authentication에 사용할 앱 암호.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 _않습니다_.

- 사용자가 최신 인증 _없이_ Outlook을 제외한 레거시 Office 클라이언트 응용 프로그램(Office 2010 및 Office 2013)에 로그인합니다(위의 **지원되는 시나리오** 참조). 가능할 경우 조직은 최신 인증으로 전환하는 것이 좋습니다. 최신 인증은 통과 인증 지원을 허용합니다. Azure Multi-Factor Authentication 같은 [조건부 액세스](../active-directory-conditional-access-azure-portal.md) 기능을 사용하여 사용자 계정을 보호하는 데에도 도움이 됩니다.
- Office 2010의 Exchange 하이브리드 환경에서만 일정 공유 및 약속 있음/없음 정보에 액세스하세요.
- 사용자가 최신 인증이 _없는_ 비즈니스용 Skype 클라이언트 응용 프로그램에 로그인
- 사용자가 PowerShell 버전 1.0에 로그인. PowerShell 버전 2.0을 사용하는 것이 좋습니다.
- [자격 증명이 손실된](../active-directory-reporting-risk-events.md#leaked-credentials) 사용자 검색
- Azure AD Domain Services를 사용하려면 테넌트에서 암호 해시 동기화를 사용하도록 설정해야 합니다. 따라서 통과 인증_만_ 사용하는 테넌트는 Azure AD Domain Services가 필요한 시나리오에서 사용할 수 없습니다.
- 통과 인증은 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)와 통합되지 않습니다.
- iOS 설정 도우미를 사용하는 Apple DEP(Apple 장비 등록 프로그램)는 최신 인증을 지원하지 않습니다. 따라서 Apple DEP 장치는 통과 인증을 사용하여 관리되는 도메인의 Intune에 등록하지 못합니다. 대신 [회사 포털 앱](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/)을 사용하는 것이 좋습니다.

>[!IMPORTANT]
>지원되지 않는 시나리오에_만_ 해당하는 해결 방법으로, Azure AD Connect 마법사의 [선택적 기능](active-directory-aadconnect-get-started-custom.md#optional-features) 페이지에서 암호 해시 동기화를 사용하도록 설정합니다. 사용자가 "지원되지 않는 시나리오" 섹션에 나열된 응용 프로그램에 로그인하는 경우 해당 특정 로그인 요청은 통과 인증 에이전트에 의해 처리되지 _않습니다_. 따라서 [통과 인증 로그](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)에 기록되지 않습니다.

>[!NOTE]
암호 해시 동기화를 사용하면 온-프레미스 인프라가 손상된 경우 인증을 장애 조치(Failover)할 수 있는 옵션이 제공됩니다. 통과 인증에서 Active Directory 암호 해시 동기화로의 이 장애 조치(failover)는 자동 작업이 아닙니다. Azure AD Connect를 사용하여 수동으로 로그인 방법을 전환해야 합니다. Azure AD Connect를 실행하는 서버가 다운되면 Microsoft 지원 서비스의 도움을 받아 통과 인증을 해제해야 합니다.

## <a name="next-steps"></a>다음 단계
- [빠른 시작](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD 통과 인증을 구성하고 실행합니다.
- [스마트 잠금](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호하는 방법을 알아봅니다.
- [기술 심층 분석](active-directory-aadconnect-pass-through-authentication-how-it-works.md): 통과 인증 기능이 작동하는 원리를 이해합니다.
- [질문과 대답](active-directory-aadconnect-pass-through-authentication-faq.md): 통과 인증 기능에 대해 자주 하는 질문과 대답을 살펴봅니다.
- [문제 해결](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 통과 인증 기능에 대한 자세한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](active-directory-aadconnect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.
