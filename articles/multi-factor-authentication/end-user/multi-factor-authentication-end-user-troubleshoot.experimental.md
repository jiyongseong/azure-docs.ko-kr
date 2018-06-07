---
title: 2단계 인증 문제 해결 | Microsoft Docs
description: 이 문서는 Azure 다단계 인증에 문제가 있는 경우 수행할 작업에 대한 정보를 제공합니다.
services: multi-factor-authentication
keywords: 다단계 인증 클라이언트, 인증 문제, 상관관계 ID
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: barlan
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: 736a1f03ef87850fdaaee7ce636d8dd0f3ae1a84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="get-help-with-two-step-verification"></a>2단계 인증에 관한 도움말 얻기
이 문서는 2단계 인증과 관련하여 가장 많이 하는 질문에 대한 답변입니다. 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>2단계 인증을 수행해야 하는 이유는 무엇입니까? 이 기능을 끌 수 있습니까?

2단계 인증은 조직이 계정을 보호하는 데 사용하도록 선택한 보안 기능입니다. 사용자가 알고 있는 것과 휴대하는 것, 두 가지 형태의 인증을 사용하기 때문에 단순 암호에 비해 더 안전합니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 휴대하는 것은 사용자가 흔히 휴대하는 전화 또는 장치입니다. 계정이 2단계 인증으로 보호되는 경우 악의적인 해커가 암호를 알고 있어도 로그인할 수 없습니다. 휴대폰에 액세스할 수 없기 때문에 로그인할 수 없습니다. 

2단계 인증을 제공하는 것은 Microsoft이지만, 기능을 사용하도록 선택하는 것은 사용자의 조직입니다. 계정을 보호하기 위한 암호 사용을 거부할 수 없는 것처럼 회사 지원 서비스에서 요구하는 경우 사용자는 이를 거부할 수 없습니다. 

개인 Microsoft 계정에서 2단계 인증이 켜져 있고 설정을 변경하려는 경우 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)를 읽어 보세요. 

## <a name="i-dont-have-my-phone-with-me-today"></a>현재 전화기가 없는 경우

전화기를 집에 두고 온 날에도, 직장에서는 여전히 로그인해야 합니다. 우선 다른 인증 방법으로 로그인을 시도해 보아야 합니다. 2단계 인증을 등록할 때 전화번호를 2개 이상 설정하였습니까? 다른 방법으로 로그인을 시도해 보려면 다음 단계를 수행합니다.

1. 일반적인 방법으로 로그인합니다.
2. 2단계 인증 페이지를 열 때 **다른 인증 옵션 사용**을 선택합니다.

   ![다양한 확인](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. 사용할 인증 옵션을 선택합니다. 
  - 대체 방법에 대한 액세스 권한이 없는 경우 회사 지원 서비스에 문의하여 계정에 로그인하는 데 관한 도움을 받으세요.
  - 대체 방법에 대한 액세스 권한이 있는 경우 2단계 인증을 계속 진행합니다.

**다른 인증 옵션 사용** 링크가 표시되지 않는 경우는 처음 2단계 인증에 등록할 때 대체 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 관한 도움말을 보려면 회사 지원 서비스에 문의하세요. 로그인하고 나면, 다음 번을 위해 추가 인증 방법을 추가하도록 [설정을 관리](multi-factor-authentication-end-user-manage-settings.md)합니다. 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>내 전화기를 분실한 경우 또는 번호가 바뀐 경우
두 가지 방법으로 계정에 다시 들어올 수 있습니다. 첫 번째 방법은 대체 인증 전화 번호를 설정한 경우 이를 사용하여 로그인하는 것입니다. 두 번째 방법은 사용자 설정을 지우도록 회사 지원 서비스에 요청하는 것입니다.

휴대폰을 분실하거나 도난당한 경우에도 회사 지원 서비스에 알리는 것이 좋습니다. 앱 암호를 다시 설정하고 기억된 모든 장치를 지워야 합니다. 

### <a name="use-an-alternate-phone-number"></a>대체 전화 번호 사용
다른 장치에서 보조 전화 번호 또는 인증자 앱처럼 여러 인증 옵션을 설정한 경우 다음 중 하나를 사용하여 로그인합니다.

대체 전화 번호를 사용하여 로그인하려면 다음 단계를 따르세요.

1. 일반적인 방법으로 로그인합니다.
2. 추가로 계정을 확인하라는 메시지가 표시되면 **다른 인증 옵션 사용**을 선택합니다.
   
   ![다양한 확인](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. 액세스 권한이 있는 전화 번호 또는 장치를 선택합니다.
4. 계정으로 다시 돌아오면 [설정을 관리](multi-factor-authentication-end-user-manage-settings.md)하여 인증 전화 번호를 변경할 수 있습니다.

### <a name="clear-your-settings"></a>설정 지우기
보조 인증 전화 번호를 구성하지 않은 경우 도움을 받을 수 있도록 회사 지원 서비스에 문의해야 합니다. 사용자 설정을 삭제하도록 하면 다음 번에 로그인할 때 [2단계 인증 등록](multi-factor-authentication-end-user-first-time.md) 메시지가 다시 표시됩니다.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>휴대폰에서 텍스트 메시지를 받지 못하는 경우
로그인하지만 텍스트 또는 전화 통화를 수신하지는 않으려는 여러 이유가 있습니다. 이전에 전화에 텍스트 또는 전화 통화를 성공적으로 수신한 경우에는 이 문제는 사용자 계정이 아니라 전화 공급자와 관련된 문제입니다. 휴대폰 신호가 양호한지 확인합니다. 텍스트 메시지를 수신하려는 경우 텍스트 메시지를 수신할 수 있는지 확인합니다. 테스트 삼아 친구에게 통화 또는 문자를 보내달라고 요청합니다. 

텍스트 또는 전화를 몇 분 정도 기다린 경우 빠르게 계정에 액세스하는 방법은 다른 옵션을 시도하는 것입니다.

1. 페이지에서 **다른 인증 옵션 사용**을 선택하고 인증을 기다립니다.
   
    ![다양한 확인](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 사용하려는 전화 번호 또는 배달 방법을 선택합니다.
   
    여러 인증 코드를 받은 경우 최신 코드만 작동합니다.

다른 방법을 구성하지 않은 경우 회사 지원 서비스에 문의하고 설정을 지우도록 요청합니다. 다음 번 로그인할 때 다시 [Multi-Factor Authentication 설정](multi-factor-authentication-end-user-first-time.md)하라는 메시지가 표시됩니다.

종종 잘못된 셀 신호로 인해 지연된 경우 스마트폰에서 [Microsoft Authenticator 앱](microsoft-authenticator-app-how-to.md)을 사용하는 것이 좋습니다. 앱은 로그인하는 데 사용할 수 있는 임의의 보안 코드를 생성할 수 있고 이러한 코드는 셀 신호 또는 인터넷 연결이 필요하지 않습니다.

## <a name="app-passwords-are-not-working"></a>앱 암호가 작동하지 않는 경우
먼저 앱 암호를 올바르게 입력했는지 확인합니다. 2단계 인증을 지원하지 않는 이전 데스크톱 응용 프로그램의 경우에만 생성된 앱 암호가 사용자의 일반 암호를 대체합니다. 계속 작동하지 않으면 로그인을 시도하고 [새 앱 암호를 생성](multi-factor-authentication-end-user-app-passwords.md)합니다.  계속 작동하지 않으면 회사 지원 서비스에 문의하여 [사용자의 기존 앱 암호를 삭제](../../active-directory/authentication/howto-mfa-userdevicesettings.md)하도록 요청하면 새 암호를 만들 수 있습니다.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우
이러한 문제 해결 단계를 시도했는데도 문제가 계속되는 경우 회사 지원 서비스에 문의하세요. 그러면 도움을 받을 수 있습니다.

## <a name="related-topics"></a>관련된 항목
* [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator 응용 프로그램 FAQ](microsoft-authenticator-app-faq.md)

