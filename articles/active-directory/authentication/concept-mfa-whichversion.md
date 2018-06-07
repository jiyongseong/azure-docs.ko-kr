---
title: Azure MFA 클라우드 또는 서버 중에서 선택 | Microsoft Docs
description: 보안을 유지하려는 대상과 사용자의 위치에 대한 질문에 답하여 적합한 다단계 인증 보안 솔루션을 선택합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>사용자를 위한 Azure Multi-Factor Authentication 솔루션 선택
Azure MFA(Multi-Factor Authentication)에는 여러 가지 버전이 있기 때문에 사용하기에 적절한 버전을 파악하기 위해 몇 가지 질문에 답해야 합니다.  해당 질문은 다음과 같습니다.

* [보안을 유지하려는 대상은 무엇입니까](#what-am-i-trying-to-secure)
* [사용자는 어디에 있습니까](#where-are-the-users-located)
* [어떤 기능이 필요합니까?](#what-features-do-i-need)

다음 섹션에서는 이러한 각 대답의 결정에 대한 지침을 제공합니다.

## <a name="what-am-i-trying-to-secure"></a>보안을 유지하려는 대상은 무엇입니까?
올바른 2단계 인증 솔루션을 결정하려면 먼저 두 번째 인증 방법으로 보안을 유지하려는 대상이 무엇인지 답해야 합니다.  Azure에 있는 응용프로그램입니까?  또는 원격 액세스 시스템입니까?  보안을 유지하려는 대상이 무엇인지 결정하여 Multi-Factor Authentication 활성화가 필요한 곳에 대한 질문에 답할 수 있습니다.  

| 보안을 유지하려는 대상은 무엇입니까 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| 자사 Microsoft 앱 |● |● |
| 앱 갤러리의 SaaS 앱 |● |  |
| Azure AD 앱 프록시를 통해 웹 응용프로그램 게시됨 |● |  |
| Azure AD 응용프로그램 프록시를 통해 IIS 응용프로그램 게시되지 않음 | |● |
| VPN, RDG와 같은 원격 액세스 | ● | ● |

## <a name="where-are-the-users-located"></a>사용자는 어디에 있습니까
다음으로 사용자의 위치를 확인하여 사용할 올바른 솔루션이 클라우드에 있는지 MFA 서버를 사용한 온-프레미스인지를 확인하는 데 도움을 줍니다.

| 사용자 위치 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD 및 AD FS로 페더레이션을 사용한 온-프레미스 AD |● |● |
| Azure AD 및 DirSync를 사용한 온-프레미스 AD, Azure AD Sync, Azure AD Connect - 암호 해시 동기화 또는 통과 인증 없음 |● |● |
| Azure AD 및 DirSync를 사용한 온-프레미스 AD, Azure AD Sync, Azure AD Connect - 암호 해시 동기화 또는 통과 인증 사용 |● | |
| 온-프레미스 Active Directory | |● |

## <a name="what-features-do-i-need"></a>어떤 기능이 필요합니까?
다음 표는 클라우드에서 Multi-Factor Authentication과 함께 사용할 수 있는 경우와 Multi-Factor Authentication 서버와 함께 사용할 수 있는 경우에 대한 기능 비교입니다.

| 기능 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| 두 번째 단계로 모바일 앱 알림 | ● | ● |
| 두 번째 단계로 모바일 앱 확인 코드 | ● | ● |
| 두 번째 단계로 전화 통화 | ● | ● |
| 두 번째 단계로 단방향 SMS | ● | ● |
| 두 번째 단계로 양방향 SMS | | ● (사용되지 않음)| 
| 두 번째 단계로 하드웨어 토큰 | | ● |
| MFA를 지원하지 않는 Office 365 클라이언트에 대한 앱 암호 | ● | |
| 인증 방법에 대한 관리자 제어 | ● | ● |
| PIN 모드 | | ● |
| 사기 행위 경고 |● | ● |
| MFA 보고서 |● | ● |
| 일회성 바이패스 | | ● |
| 전화 통화에 대한 사용자 지정 인사말 | ● | ● |
| 전화 통화에 대한 사용자 지정 가능한 발신자 번호 | ● | ● |
| 신뢰할 수 있는 IP | ● | ● |
| 신뢰할 수 있는 장치에 대한 MFA 기억 | ● | |
| 조건부 액세스 | ● | ● |
| 캐시 |  | ● |

## <a name="next-steps"></a>다음 단계

이제 클라우드에서 Azure Multi-Factor Authentication과 MFA 서버 온-프레미스 간의 차이점을 이해했으므로 Azure Multi-Factor Authentication을 설정하고 사용해보겠습니다. **시나리오를 나타내는 아이콘 선택**

<center>

[![클라우드의 MFA](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA 서버](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
