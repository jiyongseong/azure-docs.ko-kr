---
title: Azure MFA의 2단계 검증에 대해 알아보기 | Microsoft Docs
description: Azure Multi-Factor Authentication 정의, MFA를 사용하는 이유 및 사용 가능한 다른 메서드 및 버전에 대해 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 2bf2cbefb2b60ad5278bdee7bd97aff1ed6212a4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33867161"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 정의

2단계 인증은 두 개 이상의 검증 방법이 필요하며 사용자 로그인 및 트랜잭션에 중요한 두 번째 계층을 추가하는 인증 방법입니다. 이러한 인증에서는 다음 중 두 가지 이상의 검증 방법을 요구합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 장치(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 장치)
* 사용자의 신원 정보(생체 인식)

<center>![사용자 이름 및 암호](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![인증서](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![스마트폰](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![스마트 카드](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![가상 스마트 카드](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![사용자 이름 및 암호](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. Azure MFA는 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 전화 통화, 문자 메시지 또는 모바일 앱 확인과 같은 다양한 확인 방법을 통해 강력한 인증을 전달합니다.

## <a name="why-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication을 사용하는 이유
오늘날, 어느 때 보다 연결된 사람들이 늘어나고 있습니다. 스마트폰, 태블릿, 랩톱 및 PC를 사용하여 어디서나 계정과 응용 프로그램에 액세스하고 항상 연결 상태를 유지하는 다양한 옵션이 선택할 수 있습니다.

Azure Multi-Factor Authentication은 인증의 두 번째 메서드를 제공하여 사용자를 보호하는 사용하기 쉽고 확장 가능한 신뢰할 수 있는 솔루션입니다.

| ![용이성](./media/multi-factor-authentication/simple.png) | ![확장성](./media/multi-factor-authentication/scalable.png) | ![항상 보호](./media/multi-factor-authentication/protected.png) | ![안정성](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **용이성** |**확장성** |**항상 보호** |**안정성** |

* **용이성** - Azure Multi-Factor Authentication은 간단하게 설정하고 사용할 수 있습니다. Azure Multi-Factor Authentication과 함께 제공되는 추가 보호를 사용하면 사용자가 자신의 장치를 관리할 수 있습니다. 무엇보다도 대부분의 경우 간단한 몇 번 클릭으로 설치할 수 있습니다.
* **확장성** - Azure Multi-Factor Authentication은 클라우드의 강력한 기능을 이용하며 온-프레미스 AD와 사용자 지정 앱을 통합합니다. 이러한 보호는 고용량 업무상 중요 한 시나리오에도 확장됩니다.
* **항상 보호** -Azure Multi-Factor Authentication은 가장 높은 업계 표준을 사용하여 강력한 인증을 제공합니다.
* **안정성** - Microsoft가 Azure Multi-Factor Authentication의 99.9%의 가용성을 보장합니다. 2단계 인증 요청을 수신하거나 처리할 수 없는 경우 서비스는 사용할 수 없는 것으로 간주됩니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 작동 방법](concept-mfa-howitworks.md)에 대해 알아보기

- 다른 [Azure Multi-Factor Authentication에 대한 다양한 버전 및 사용량 메서드](concept-mfa-licensing.md)에 대해 알아보기
