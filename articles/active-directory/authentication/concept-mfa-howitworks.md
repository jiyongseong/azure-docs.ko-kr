---
title: Azure Multi-Factor Authentication - 작동 방법
description: 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 709fab070533984f94a72ff2136a8bc32fbe6ec6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication 작동 방법
2단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 신뢰할 수 있는 장치가 없다면 어찌할 수 없습니다. 

![검사](./media/concept-mfa-howitworks/howitworks.png)

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다.  두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다.


## <a name="methods-available-for-two-step-verification"></a>2단계 인증을 위한 방법
사용자가 로그인하면 사용자에게 추가 확인이 전송됩니다.  이 두 번째 확인에 사용할 수 있는 방법의 목록은 다음과 같습니다.

| 확인 방법 | 설명 |
| --- | --- |
| 전화 통화 |사용자의 등록된 휴대폰으로 통화가 이루어집니다. 사용자는 필요한 경우 PIN을 입력한 후 # 키를 누릅니다. |
| 문자 메시지 |6자리 코드가 있는 문자 메시지가 사용자의 휴대폰으로 전송됩니다. 사용자는 로그인 페이지에 이 코드를 입력합니다. |
| 모바일 앱 알림 |사용자의 스마트폰으로 확인 요청이 보내집니다. 사용자는 필요한 경우 PIN을 입력한 후 모바일 앱에서 **확인**을 선택합니다. |
| 모바일 앱 확인 코드 |사용자의 스마트폰에서 실행되고 있는 모바일 앱에 30초마다 변경되는 확인 코드가 표시됩니다. 사용자가 가장 최근 코드를 찾아 로그인 페이지에 입력합니다. |
| 타사 OATH 토큰 | 타사 확인 방법을 허용하도록 Azure Multi-Factor Authentication Server를 구성할 수 있습니다. |

Azure Multi-Factor Authentication은 클라우드와 서버 모두에 대해 선택 가능한 확인 방법을 제공합니다. 전화 통화, 텍스트, 앱 알림, 앱 코드 중에서 사용자에게 제공할 방법을 선택할 수 있습니다. 자세한 내용은 [선택 가능한 확인 방법](howto-mfa-mfasettings.md#selectable-verification-methods)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 다른 [Azure Multi-Factor Authentication에 대한 다양한 버전 및 사용량 메서드](concept-mfa-licensing.md)에 대해 알아보기

- [클라우드 또는 온-프레미스](concept-mfa-whichversion.md)에서 Azure MFA를 배포할지 여부 선택

- [자주 묻는 질문](multi-factor-authentication-faq.md)에 대한 답변을 읽어봅니다.