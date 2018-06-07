---
title: "2단계 인증을 통한 Azure MFA 로그인 | Microsoft Docs"
description: "이 페이지에서는 Azure MFA에서 사용할 수 있는 다양한 로그인 방법을 확인할 수 있는 위치에 대한 지침을 제공합니다."
keywords: "사용자 인증, 로그인 환경, 휴대폰으로 로그인, 사무실 전화로 로그인"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: c47356b7b84e38a1db9259304c2a975958b1977c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 로그인 환경
> [!NOTE]
> 이 문서의 목적은 일반적인 로그인 환경을 연습해보는 것입니다. 로그인 관련 지원을 얻거나 문제를 해결하려면 [Azure Multi-Factor Authentication에 문제가 있는 경우](multi-factor-authentication-end-user-troubleshoot.md)를 참조하세요.

## <a name="what-will-your-sign-in-experience-be"></a>어떤 로그인 환경이 제공되나요?
두 번째 단계로 사용하도록 선택하는 항목(예: 전화 통화, 인증 앱 또는 문자)에 따라 로그인 환경이 달라집니다. 수행하는 작업을 가장 잘 설명하는 옵션을 선택합니다.

| 로그인하는 방법 |
| --- |
| [내 휴대폰 또는 사무실 전화로 통화](#signing-in-with-a-phone-call) |
| [내 휴대폰으로 문자 발송](#signing-in-with-a-text-message)
| [ 앱의 알림 사용](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Microsoft Authenticator 앱의 확인 코드 사용](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [원하는 방법이 없으므로 다른 방법 사용](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>전화 통화를 사용하여 로그인
다음 정보에서는 휴대폰 또는 사무실 전화 통화를 사용한 2단계 확인 환경에 대해 설명합니다.

1. 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.  
2. Microsoft에서 전화를 합니다.  
3. 전화를 받고 # 키를 누릅니다.  

## <a name="signing-in-with-a-text-message"></a>문자 메시지를 사용하여 로그인
다음 정보에서는 휴대폰으로 문자 메시지를 발송하는 2단계 확인 환경에 대해 설명합니다.

1. 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
2. Microsoft에서 숫자 코드를 포함하는 문자 메시지를 보냅니다.
3. 로그인 페이지에 제공된 상자에 코드를 입력합니다.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 로그인
다음 정보는 2단계 확인을 위해 Microsoft Authenticator 앱을 사용하는 환경을 설명합니다. 앱을 사용하는 두 가지 다른 방법이 있습니다. 장치에서 푸시 알림을 받거나 앱을 열어 확인 코드를 가져올 수 있습니다.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱의 인증으로 로그인하려면
1. 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
2. Microsoft는 사용자 장치의 Microsoft Authenticator 앱에 알림을 보냅니다.

  ![Microsoft가 알림을 보냄](./media/multi-factor-authentication-end-user-signin/notify.png)

3. 휴대폰에서 알림을 열고 **확인** 키를 선택합니다. 회사에서 PIN을 요구하는 경우 여기에 입력합니다.
4. 사용자가 로그인됩니다.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>확인 코드를 사용하여 Microsoft Authenticator 앱에 로그인하려면

Microsoft Authenticator 앱을 사용하여 확인 코드를 가져오는 경우 앱을 열 때 계정 이름 아래에 숫자가 표시됩니다. 이 숫자는 같은 숫자를 두 번 사용하는 일이 없도록 30초마다 변경됩니다. 확인 코드를 묻는 메시지가 표시되면 앱을 열고 현재 표시되는 숫자를 사용합니다.

1. 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
2. Microsoft가 확인 코드를 요구합니다.

  ![확인 코드 입력](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. 휴대폰에서 Microsoft Authenticator 앱을 열고 로그인 위치에 제공되는 상자에 코드를 입력합니다.

## <a name="signing-in-with-an-alternate-method"></a>다른 방법을 사용하여 로그인
경우에 따라 기본 확인 방법으로 설정한 휴대폰이나 장치가 없는 경우도 있습니다. 계정에 대해 백업 방법을 설정하도록 권장하는 것도 이 때문입니다. 다음 섹션에서는 기본 방법을 사용할 수 없을 때 대체 방법을 사용하여 로그인하는 방법을 보여 줍니다.

1. 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
2. **다른 확인 옵션 사용**을 선택합니다. 설정한 수에 따라 다른 확인 옵션이 표시됩니다.
3. 대체 방법을 선택하고 로그인합니다.

  ![대체 방법 사용](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>다음 단계

2단계 확인을 사용하여 로그인하는 동안 문제가 발생하면 [Azure Multi-Factor Authentication에 문제가 있는 경우](multi-factor-authentication-end-user-troubleshoot.md)에서 자세한 정보를 확인하세요.

[2단계 확인 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 방법 알아보기

문자 메시지 및 전화 대신 알림을 사용하여 로그인할 수 있도록 [Microsoft Authenticator 앱 시작](microsoft-authenticator-app-how-to.md) 방법을 알아보세요.
