---
title: "Azure AD Connect - 통과 인증 - 인증 에이전트 업그레이드 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 구성을 업그레이드하는 방법에 대해 설명합니다."
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
ms.date: 01/14/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: f0a254b7216ca6fda40e26bafb7de57e796a5218
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory 통과 인증: 미리 보기 인증 에이전트 업그레이드

## <a name="overview"></a>개요

이 문서는 미리 보기를 통해 Azure AD 통과 인증을 사용하는 고객을 위한 것입니다. 최근에 인증 에이전트 소프트웨어를 업그레이드(및 변경)했습니다. 온-프레미스 서버에 설치된 미리 보기 인증 에이전트를 _수동으로_ 업그레이드해야 합니다. 이 수동 업그레이드는 일회성 작업입니다. 인증 에이전트에 대한 모든 향후 업데이트는 자동입니다. 업그레이드하는 이유는 다음과 같습니다.

- 인증 에이전트의 미리 보기 버전에는 추가 보안 또는 버그 수정이 적용되지 않습니다.
-   고가용성을 위해 추가 서버에 인증 에이전트의 미리 보기 버전을 설치하는 것은 불가능합니다.

## <a name="check-versions-of-your-authentication-agents"></a>인증 에이전트의 버전 확인

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>1단계: 인증 에이전트가 설치된 위치 확인

다음 단계에 따라 인증 에이전트가 설치된 위치를 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**를 선택합니다. 
4. **통과 인증**을 선택합니다. 이 블레이드는 인증 에이전트가 설치된 서버를 나열합니다.

![Azure Active Directory 관리 센터 - 통과 인증 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>2단계: 인증 에이전트의 버전 확인

이전 단계에서 식별된 각 서버에서 사용자의 인증 에이전트 버전을 확인하려면 다음 지침을 따르세요.

1. 온-프레미스 서버에서 **제어판 -> 프로그램 -> 프로그램 및 기능**으로 이동합니다.
2. “**Microsoft Azure AD Connect 인증 에이전트**”에 대한 항목이 있는 경우 이 서버에 대해 어떤 조치도 취할 필요가 없습니다.
3. “**Microsoft Azure AD 응용 프로그램 프록시 커넥터**” 버전 1.5.132.0 이하에 대한 항목이 있는 경우 서버를 수동으로 업그레이드해야 합니다.

![인증 에이전트의 미리 보기 버전](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>업그레이드를 시작하기 전에 참고할 모범 사례

업그레이드하기 전에 다음 항목이 준비되었는지 확인합니다.

1. **클라우드 전용 전역 관리자 계정 만들기**: 통과 인증 에이전트가 제대로 작동하지 않는 비상 상황에서는 사용할 클라우드 전용 전역 관리자 계정이 없다면 업그레이드하지 마세요. [클라우드 전용 전역 관리자 계정 추가](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다. 이 단계를 수행하는 것이 중요하며 테넌트가 잠기지 않도록 합니다.
2.  **고가용성 보장**: 이전에 완료되지 않은 경우, 로그인 요청에 대해 고가용성을 제공하기 위해 [지침](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)에 따라 두 번째 독립 실행형 인증 에이전트를 설치합니다.

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Azure AD Connect 서버에서 인증 에이전트 업그레이드

동일한 서버에 인증 에이전트를 업그레이드하기 전에 Azure AD Connect를 업그레이드해야 합니다. 주 및 스테이징 Azure AD Connect 서버 모두에서 다음 단계를 따릅니다.

1. **Azure AD Connect 업그레이드**: 이 [문서](./active-directory-aadconnect-upgrade-previous-version.md)에 따라 최신 Azure AD Connect 버전으로 업그레이드합니다.
2. **인증 에이전트의 미리 보기 버전 제거**: [이 PowerShell 스크립트](https://aka.ms/rmpreviewagent)를 다운로드하여 서버에서 관리자 권한으로 실행합니다.
3. **인증 에이전트의 최신 버전(버전 1.5.193.0 이상) 다운로드**: 테넌트의 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다. **Azure Active Directory -> Azure AD Connect -> 통과 인증 -> 에이전트 다운로드**를 선택합니다. [서비스 약관](https://aka.ms/authagenteula)에 동의하고 인증 에이전트의 최신 버전을 다운로드합니다. [여기](https://aka.ms/getauthagent)에서 인증 에이전트를 다운로드할 수도 있습니다.
4. **인증 에이전트의 최신 버전 설치**: 3단계에서 다운로드한 실행 파일을 실행합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
5. **최신 버전이 설치되었는지 확인**: 이전에 설명한 것처럼 **제어판 -> 프로그램 -> 프로그램 및 기능**으로 이동하여 “**Microsoft Azure AD Connect 인증 에이전트**”에 대한 항목이 있는지 확인합니다.

>[!NOTE]
>위의 단계를 완료한 후 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에서 통과 인증 블레이드를 확인하는 경우 서버당 두 개의 인증 에이전트 항목이 표시됩니다. 하나의 항목은 **활성**으로 인증 에이전트를 보여 주고, 다른 하나는 **비활성**으로 보여 줍니다. _예상된_ 동작입니다. **비활성** 항목은 몇 일 후 자동으로 삭제됩니다.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>다른 서버에서 인증 에이전트 업그레이드

다음 단계에 따라 다른 서버에서 인증 에이전트를 업그레이드합니다(여기서는 Azure AD Connect가 설치되지 않음).

1. **인증 에이전트의 미리 보기 버전 제거**: [이 PowerShell 스크립트](https://aka.ms/rmpreviewagent)를 다운로드하여 서버에서 관리자 권한으로 실행합니다.
2. **인증 에이전트의 최신 버전(버전 1.5.193.0 이상) 다운로드**: 테넌트의 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다. **Azure Active Directory -> Azure AD Connect -> 통과 인증 -> 에이전트 다운로드**를 선택합니다. 서비스 약관에 동의하고 최신 버전을 다운로드합니다.
3. **인증 에이전트의 최신 버전 설치**: 2단계에서 다운로드한 실행 파일을 실행합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
4. **최신 버전이 설치되었는지 확인**: 이전에 설명한 것처럼 **제어판 -> 프로그램 -> 프로그램 및 기능**으로 이동하여 **Microsoft Azure AD Connect 인증 에이전트**라는 항목이 있는지 확인합니다.

>[!NOTE]
>위의 단계를 완료한 후 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에서 통과 인증 블레이드를 확인하는 경우 서버당 두 개의 인증 에이전트 항목이 표시됩니다. 하나의 항목은 **활성**으로 인증 에이전트를 보여 주고, 다른 하나는 **비활성**으로 보여 줍니다. _예상된_ 동작입니다. **비활성** 항목은 몇 일 후 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
