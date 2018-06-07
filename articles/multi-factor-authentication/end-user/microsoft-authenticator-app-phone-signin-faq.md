---
title: Microsoft Authenticator 휴대폰 로그인 - Azure 및 Microsoft 계정 | Microsoft Docs
description: 암호를 입력하는 대신 휴대폰을 사용하여 Microsoft 계정에 로그인합니다. 이 문서에서는 이 기능에 대한 FAQ를 제공합니다.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2018
---
# <a name="sign-in-with-your-phone-not-your-password"></a>암호가 아닌 휴대폰을 사용하여 로그인

Microsoft Authenticator 앱을 통해 암호를 입력한 후에 2단계 인증을 수행하여 계정의 보안을 유지할 수 있습니다. 하지만 개인 Microsoft 계정에 대한 암호를 완전히 바꿀 수 있다는 점을 아십니까?

이 기능은 iOS 및 Android 장치에서 사용할 수 있으며 개인 Microsoft 계정에서 작동합니다.

## <a name="how-it-works"></a>작동 방법

사용자들은 대부분 Microsoft 계정에 로그인할 때 2단계 인증을 위해 Microsoft Authenticator 앱을 사용합니다. 암호를 입력한 다음 앱으로 이동하여 알림을 승인하거나 확인 코드를 가져옵니다. 휴대폰 로그인을 사용하여 암호를 건너뛰고 휴대폰에서 ID 확인을 모두 수행할 수 있습니다. 휴대폰 로그인은 2단계 인증 유형이기 때문에 사용자의 ID를 확인하기 위해 알고 있는 것과 보유하고 있는 것을 여전히 제공해야 합니다. 휴대폰은 사용자가 여전히 보유하고 있는 것이며 휴대폰의 PIN 또는 생체 인식 키는 사용자가 알고 있는 것입니다.

## <a name="how-to-get-started"></a>시작하는 방법

휴대폰으로 개인 Microsoft 계정에 로그인하려면 다음과 같은 단계를 따르세요.

1. 계정에 대해 휴대폰 로그인을 사용하도록 설정합니다.

  - Microsoft Authenticator 앱이 아직 없는 경우 [Microsoft Authenticator 페이지](microsoft-authenticator-app-how-to.md)의 단계에 따라 개인 Microsoft 계정을 설치하고 추가합니다. 새로 추가된 계정이 자동으로 활성화되므로 계속 진행할 수 있습니다.

  - 2단계 인증을 위한 Microsoft Authenticator를 이미 사용하는 경우 앱 홈페이지에서 계정을 선택하고 드롭다운 메뉴에서 **휴대폰 로그인 사용**을 선택합니다.

  >[!NOTE]
  >계정을 보호하려면 PIN 또는 장치에 대한 생체 인식 잠금이 필요합니다. 휴대폰의 잠금을 해제하는 경우 앱에서는 휴대폰 로그인을 활성화하기 전에 잠금을 설정하라는 요청이 표시됩니다.

3. Microsoft 계정 암호를 정상적으로 입력하는 페이지에는 대부분의 **대신 앱 사용**이라는 링크가 있습니다. 이 링크를 선택하여 휴대폰으로 로그인합니다.

4. Microsoft는 사용자의 휴대폰에 알림을 보냅니다. 계정에 로그인하려면 알림을 승인합니다.   

## <a name="faq"></a>FAQ

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>휴대폰에 암호를 입력하는 것보다 안전하게 로그인할 수 있나요?  

오늘날 대부분의 사람들은 사용자 이름 및 암호를 사용하여 웹 사이트 또는 앱에 로그인합니다.  그러나 암호를 종종 분실하거나 도난당하거나 해커가 추측하게 됩니다. Microsoft Authenticator 앱을 로그인하도록 설정할 때 계정의 잠금을 해제할 수 있는 휴대폰에서 키를 생성합니다. 이미 사용하는 휴대폰에서 PIN 또는 생체 인식으로 키를 보호합니다.  휴대폰을 사용하여 로그인할 때 휴대폰 자체 및 잠금을 해제하는 기능과 같은 두 가지 요소를 사용하여 ID를 안전하게 증명하는 데 이 키를 사용합니다. 

사용되는 키는 Windows Hello 및 FIDO Alliance UAF 사양에서 사용되는 키와 비슷합니다. 생체 데이터는 로컬에서 키를 보호하는 데 사용하고, 절대로 클라우드로 전송되거나 저장되지 않습니다. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>어디에서 내 휴대폰을 사용하여 암호를 바꾸고 어디에서 여전히 암호가 필요한가요?  

현재 휴대폰 로그인 기능은 개인 Microsoft 계정으로 구동하는 웹앱 및 서비스, 개인 Microsoft 계정을 사용하는 iOS 또는 Android 앱 및 개인 Microsoft 계정을 사용하는 Windows 10의 앱에서만 작동됩니다. 이러한 웹 사이트 또는 앱 중 하나에 로그인할 경우 일반적으로 암호를 입력할 페이지에는 **대신 앱 사용**이라는 링크가 있습니다. 

Windows PC, XBOX 또는 Office 앱과 같은 모든 데스크톱 버전의 Microsoft 앱 잠금을 해제하는 데 휴대폰 로그인을 사용할 수 없습니다.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>이 기능이 2단계 인증을 대체하나요? 기능을 꺼야 하나요?   

때때로 그렇습니다. 휴대폰 로그인의 범위를 확장하기 위해 최선을 다하고 있지만 지금은 Microsoft 에코시스템에서 아직 지원되지 않는 위치가 있습니다. 해당 위치에서 안전한 로그인을 위해 2단계 인증을 사용합니다. 이런 이유로 인해 아니요, 계정에 대해 2단계 인증을 해제해서는 안됩니다.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>알겠습니다. 내 계정에 대해 2단계 인증을 계속 설정하는 경우에 두 개의 알림을 승인해야 하나요?

아니요, 그렇지 않습니다. 휴대폰을 사용하여 Microsoft 계정에 로그인하는 기능은 2단계 인증으로 간주됩니다. 암호를 입력하고 알림을 승인하는 대신 휴대폰의 잠금을 해제하는 방법을 알고 알림을 승인하여 ID를 증명합니다. 승인을 위해 두 번째 알림을 보내지 않습니다.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>내 휴대폰을 분실하거나 휴대하지 않은 경우 내 계정에 액세스하려면 어떻게 해야 하나요?  

항상 로그인 페이지에서 **대신 암호 사용**을 클릭하여 암호를 사용하도록 다시 전환할 수 있습니다. 2단계 인증을 사용하는 경우에도 여전히 로그인을 확인할 두 번째 방법을 염두에 두어야 합니다. 따라서 계정에 추가로 최신 보안 정보가 있는지 확인하는 것이 가장 좋습니다. https://account.live.com/proofs/manage에서 보안 정보를 관리할 수 있습니다.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>이 기능의 사용을 중지하고 암호를 입력하는 방법으로 돌아가려면 어떻게 해야 하나요?

로그인할 때 **대신 암호 사용**을 클릭합니다. 가장 최근에 선택한 기능을 기억하고 다음에 로그인할 때 기본적으로 사용합니다. 휴대폰을 사용하여 로그인하는 방법으로 돌아가려면 **대신 앱 사용**을 클릭합니다. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Microsoft에서 모든 계정에 로그인하는 앱을 사용할 수 있나요?   
이 기능은이 이번에 개인 Microsoft 계정에만 사용할 수 있습니다. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>내 휴대폰을 사용하여 내 PC에 로그인할 수 있나요?  
사용자 PC의 경우 Windows 10의 Windows Hello에서 얼굴, 지문 또는 PIN을 사용하여 로그인하는 것이 좋습니다.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>내 Windows Phone에서 로그인할 수 있나요?  
이번에는 Windows Phone의 Microsoft Authenticator에 대한 이 기능을 개발하지 않습니다. 

## <a name="next-steps"></a>다음 단계
Microsoft Authenticator 앱을 다운로드하지 않은 경우 확인하세요. 앱은 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)에서 사용할 수 있고 휴대폰 로그인은 [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)용 Microsoft Authenticator 앱에서 사용할 수 있습니다.

일반적으로 앱에 대한 질문이 있으면 [Microsoft Authenticator FAQ](microsoft-authenticator-app-faq.md)를 살펴보세요.
