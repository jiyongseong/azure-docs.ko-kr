---
title: 'Azure AD Connect: 통과 인증 - 질문과 대답 | Microsoft Docs'
description: Azure Active Directory 통과 인증에 대한 FAQ(질문과 대답)입니다.
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
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: 7a817092581ef0be0b6be063282f7484c46ff094
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory 통과 인증: 질문과 대답

이 문서에서는 Azure AD(Azure Active Directory) 통과 인증에 대한 질문과 대답을 다룹니다. 업데이트된 내용을 계속 확인해 주세요.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Azure AD에 로그인하려면 통과 인증, 암호 해시 동기화 및 AD FS(Active Directory Federation Services) 중에서 어떤 방법을 선택해야 하나요?

온-프레미스 환경 및 조직 요구 사항에 따라 다릅니다. [Azure AD Connect 사용자 로그인 옵션](active-directory-aadconnect-user-signin.md)을 검토하여 다양한 Azure AD 로그인 방법을 비교해 보세요.

## <a name="is-pass-through-authentication-a-free-feature"></a>통과 인증은 무료 기능인가요?

통과 인증은 무료 기능입니다. 이 기능을 사용하는 데 Azure AD 유료 버전이 필요하지 않습니다.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>통과 인증은 [Microsoft Azure 독일 클라우드](http://www.microsoft.de/cloud-deutschland) 및 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서 사용할 수 있나요?

번호 통과 인증은 Azure AD의 전 세계 인스턴스에서만 사용할 수 있습니다.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[조건부 액세스](../active-directory-conditional-access-azure-portal.md)는 통과 인증에서 작동하나요?

예. Azure Multi-Factor Authentication을 포함한 모든 조건부 액세스 기능이 통과 인증에서 작동합니다.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>통과 인증은 "userPrincipalName" 대신 "Alternate ID"를 사용자 이름으로 지원하나요?

예. 통과 인증은 Azure AD Connect에서 구성할 때 `Alternate ID`를 사용자 이름으로 지원합니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요. 모든 Office 365 응용 프로그램에서 `Alternate ID`를 지원하지는 않습니다. 특정 응용 프로그램의 설명서 지원 부분을 참조하세요.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>암호 해시 동기화가 통과 인증을 대체하는 역할을 하나요?

번호 통과 인증은 자동으로 암호 해시 동기화로 장애 조치하지 _않습니다_. [통과 인증이 현재 지원하지 않는 시나리오](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios)에 대한 대체 방식으로만 사용됩니다. 사용자 로그인 오류를 방지하려면 [고가용성](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)에 대해 통과 인증을 구성해야 합니다.

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>통과 인증 에이전트와 동일한 서버에 [Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md)를 설치할 수 있나요?

예. 다시 브랜딩된 버전의 통과 인증 에이전트(버전 1.5.193.0 이상)에서 이 구성을 지원합니다.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>필요한 Azure AD Connect 및 통과 인증 에이전트 버전은 무엇인가요?

이 기능을 사용하려면 Azure AD Connect의 경우 버전 1.1.486.0 이상이 필요하고, 통과 인증 에이전트의 경우 버전 1.5.58.0 이상이 필요합니다. Windows Server 2012 R2 이상이 설치된 서버에 모든 소프트웨어를 설치합니다.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>내 사용자 암호가 만료되었을 때 통과 인증을 사용하여 로그인하려고 하면 어떻게 되나요?

특정 사용자에 대해 [비밀번호 쓰기 저장](../active-directory-passwords-update-your-own-password.md)이 구성되었고 이 사용자가 통과 인증을 사용하여 로그인하면 암호를 변경하거나 다시 설정할 수 있습니다. 암호는 예상대로 온-프레미스 Active Directory에 쓰기 저장됩니다.

특정 사용자에 대해 비밀번호 쓰기 저장이 구성되지 않았거나 이 사용자에게 유효한 Azure AD 라이선스가 할당되어 있지 않으면 해당 사용자가 클라우드에서 암호를 업데이트할 수 없습니다. 암호가 만료된 경우에도 해당 암호를 업데이트할 수 없습니다. 대신에 다음과 같은 메시지가 사용자에게 표시됩니다. "조직이 이 사이트에서 암호 업데이트를 허용하지 않습니다. 조직에서 권장하는 방법에 따라 암호를 업데이트하거나 도움이 필요한 경우 관리자에게 문의하세요." 사용자 또는 관리자가 온-프레미스 Active Directory에서 해당 암호를 다시 설정해야 합니다.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>통과 인증을 통해 무차별 암호 대입 공격으로부터 사용자를 보호하려면 어떻게 할까요?

자세한 내용은[Azure Active Directory 통과 인증: 스마트 잠금](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)을 참조하세요.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>통과 인증 에이전트가 포트 80 및 443을 통해 통신하는 것은 무엇인가요?

- 인증 에이전트는 모든 기능 작업에 대해 포트 443을 통해 HTTPS 요청을 만듭니다.
- 인증 에이전트는 80 포트를 통해 HTTP 요청을 수행하여 SSL CRL(인증서 해지 목록)을 다운로드합니다.

     >[!NOTE]
     >최근 업데이트는 기능에 필요한 포트 수를 줄였습니다. 이전 버전의 Azure AD Connect 또는 인증 에이전트를 사용하는 경우 5671, 8080, 9090, 9091, 9350, 9352 및 10100-10120 포트도 열어 두세요.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>통과 인증 에이전트는 아웃바운드 웹 프록시 서버를 통해 통신할 수 있나요?

예. 온-프레미스 환경에서 WPAD(웹 프록시 자동 검색)를 사용하는 경우 인증 에이전트는 자동으로 네트워크에서 웹 프록시 서버를 찾아서 사용하려고 합니다.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>동일한 서버에 둘 이상의 통과 인증 에이전트를 설치할 수 있나요?

아니요, 단일 서버에는 통과 인증 에이전트 하나만 설치할 수 있습니다. 고가용성에 대한 통과 인증을 구성하려면 [Azure Active Directory 통과 인증: 빠른 시작](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)의 지침을 따르세요.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>통과 인증 에이전트를 제거하려면 어떻게 해야 하나요?

통과 인증 에이전트는 실행되는 동안 활성 상태를 유지하며, 지속적으로 사용자 로그인 요청을 처리합니다. 인증 에이전트를 제거하려는 경우 **제어판 -> 프로그램 -> 프로그램 및 기능**으이로 동한 후 **Microsoft Azure AD Connect Authentication Agent** 및 **Microsoft Azure AD Connect Agent Updater** 프로그램을 둘 다 제거합니다.

위의 단계를 완료한 후 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에서 통과 인증 블레이드를 확인하는 경우 인증 에이전트가 **비활성** 상태로 표시됩니다. _예상된_ 동작입니다. 인증 에이전트는 며칠 후에 목록에서 자동으로 삭제됩니다.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>이미 AD FS를 사용하여 Azure AD에 로그인하고 있습니다. 통과 인증으로 전환하려면 어떻게 하나요?

Azure AD 연결 마법사를 통해 로그인하는 방법으로 AD FS를 구성한 경우 사용자가 통과 인증에 로그인하는 데 사용하는 방법을 변경합니다. 이렇게 변경하면 테넌트에서 통과 인증을 사용할 수 있고, 페더레이션된 _모든_ 도메인이 관리되는 도메인으로 변환됩니다. 통과 인증은 이후의 모든 요청을 처리하여 테넌트에 로그인합니다. 현재, 서로 다른 도메인에서 AD FS 및 통과 인증을 함께 사용할 수 있도록 Azure AD Connect 내에서 지원되는 방법은 없습니다.

AD FS가 Azure AD 연결 마법사 _외부_에서 로그인하는 방법으로 구성된 경우 사용자 로그인 방법을 통과 인증으로 변경합니다. 이 변경은 **구성 안 함** 옵션에서 수행할 수 있습니다. 이렇게 변경하면 테넌트에서 통과 인증을 사용할 수 있지만, 페더레이션된 모든 도메인이 계속 AD FS를 사용하여 로그인합니다. PowerShell을 사용하여 수동으로 이러한 페더레이션된 도메인 중 일부 또는 모두를 관리되는 도메인으로 변환합니다. 변경한 후에는 *통과 인증에서만* 모든 요청을 처리하여 관리되는 도메인에 로그인합니다.

>[!IMPORTANT]
>통과 인증은 클라우드 전용 Azure AD 사용자에 대한 로그인을 처리하지 않습니다.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>다중 포리스트 Active Directory 환경에서 통과 인증을 사용할 수 있나요?

예. Active Directory 포리스트 간에 포리스트 신뢰가 있고 이름 접미사 라우팅이 제대로 구성된 경우 다중 포리스트 환경이 지원됩니다.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>설치해야 하는 통과 인증 에이전트 수는 몇 개인가요?

여러 개의 통과 인증 에이전트를 설치하면 [고가용성](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)이 보장됩니다. 그러나 인증 에이전트 간에 결정적 부하 분산을 제공하지는 않습니다.

테넌트에 나타날 것으로 예상되는 로그인 요청의 최대 및 평균 로드를 고려하세요. 벤치마크의 경우, 단일 인증 에이전트는 표준 4코어 CPU, 16GB RAM 서버에서 초당 300~400건의 인증을 처리할 수 있습니다.

네트워크 트래픽을 예측하려면 다음 크기 조정 지침을 사용합니다.
- 각 요청에는 크기가 (0.5K + 1K * num_of_agents)바이트인 페이로드(즉, Azure AD에서 인증 에이전트로 이동하는 데이터)가 있습니다. 여기에서 "num_of_agents"는 테넌트에 등록된 인증 에이전트 수를 나타냅니다.
- 각 응답의 페이로드(즉, 인증 에이전트에서 Azure AD로 이동하는 데이터) 크기는 1K 바이트입니다.

대부분의 고객의 경우 고가용성 및 용량을 위해 총 2~3개의 인증 에이전트로도 충분합니다. 로그인 대기 시간을 개선하려면 도메인 컨트롤러에 가까운 곳에 인증 에이전트를 설치해야 합니다.

>[!NOTE]
>테넌트당 인증 에이전트 12개로 지정된 시스템 제한이 있습니다.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Azure AD Connect를 실행하는 서버가 아닌 다른 서버에 첫 번째 통과 인증 에이전트를 설치할 수 있나요?

아니요, 이 시나리오는 지원되지 _않습니다_.

## <a name="how-can-i-disable-pass-through-authentication"></a>통과 인증을 사용하지 않도록 설정하려면 어떻게 하나요?

Azure AD Connect 마법사를 다시 실행하고 사용자 로그인 방법을 통과 인증에서 다른 방법으로 변경합니다. 이렇게 변경하면 테넌트에서 통과 인증이 비활성화되고 인증 에이전트가 서버에서 제거됩니다. 다른 서버에서 인증 에이전트를 수동으로 제거해야 합니다.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>통과 인증 에이전트를 제거하면 어떻게 되나요?

서버에서 통과 인증 에이전트를 제거하면 서버에서 로그인 요청 수락을 중지합니다. 테넌트에서 사용자 로그인 기능을 중단하지 않도록 방지하려면 통과 인증 에이전트를 제거하기 전에 다른 인증 에이전트가 실행되도록 합니다.

## <a name="next-steps"></a>다음 단계
- [현재 제한 사항](active-directory-aadconnect-pass-through-authentication-current-limitations.md): 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [빠른 시작](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD 통과 인증을 구성하고 실행합니다.
- [스마트 잠금](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호하는 방법을 알아봅니다.
- [기술 심층 분석](active-directory-aadconnect-pass-through-authentication-how-it-works.md): 통과 인증 기능이 작동하는 원리를 이해합니다.
- [문제 해결](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 통과 인증 기능에 대한 자세한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](active-directory-aadconnect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.

