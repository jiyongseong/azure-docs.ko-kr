---
title: 휴대폰용 Microsoft Authenticator 앱 | Microsoft Docs
description: 최신 버전의 Azure Authenticator로 업그레이드하는 방법에 알아봅니다.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 시작
Microsoft Authenticator 앱은 회사 또는 학교 계정(예: bsimon@contoso.com) 또는 Microsoft 계정(예: bsimon@outlook.com)의 추가 보안 수준을 제공합니다.

이 앱은 두 가지 방법 중 하나로 작동합니다.

* **알림**. 이 앱을 사용하면 스마트폰 또는 태블릿에 알림을 푸시하여 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지할 수 있습니다. 알림을 확인한 후 올바르면 **확인**을 선택합니다. 그렇지 않으면 **거부**를 선택합니다.
* **확인 코드**. 이 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 사용자 이름 및 암호를 입력한 후 앱에서 제공한 코드를 로그인 화면에 입력합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대체합니다. Azure Authenticator 앱은 여전히 작동하지만 새 Microsoft Authenticator 앱으로 전환할 것인지 결정해야 하며 이 문서에서 방법을 설명합니다.  

## <a name="opt-in-for-two-step-verification"></a>2단계 인증에 옵트인

Microsoft Authenticator 앱은 자체적으로 작동하지 않습니다. 사용자 이름 및 암호로 로그인한 후 두 번째 확인 방법에 대해 묻도록 각 계정을 구성합니다.

회사 또는 학교 계정의 경우 일반적으로 이 기능에 대해 선택하지 않습니다. 대신 보안 관리자는 사용자를 대신해서 옵트인한 다음 계정에 대해 확인 메서드를 등록하라고 알립니다. 이 시나리오에 해당하는 경우 [Azure Multi-Factor Authentication은 무엇을 의미하나요](multi-factor-authentication-end-user.md)에서 자세히 알아봅니다.

개인 계정의 경우 2단계 인증을 설정해야 합니다. Microsoft 계정이 있는 경우 이러한 단계는 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)에서 사용할 수 있습니다.

또한 Microsoft가 아닌 계정으로 Microsoft Authenticator를 사용할 수 있습니다. 2단계 인증 이외의 기능을 호출할 수 있지만 보안 또는 로그인 설정에서 찾을 수 있어야 합니다.

## <a name="install-the-app"></a>앱 설치
Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) 및 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)에서 사용할 수 있습니다.

## <a name="add-accounts-to-the-app"></a>앱에 계정 추가
Microsoft Authenticator 앱에 추가하려는 각 계정에 대해 다음 절차 중 하나를 사용합니다.

### <a name="add-a-personal-microsoft-account-to-the-app"></a>앱에 개인 Microsoft 계정 추가

개인 Microsoft 계정(Outlook.com, Xbox, Skype 등에 로그인하는 데 사용하는 계정)의 경우, Microsoft Authenticator 앱의 사용자 계정에 로그인하기만 하면 됩니다.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>QR 코드 스캐너를 사용하여 앱에 회사 또는 학교 계정 추가
1. 보안 확인 설정 화면으로 이동합니다.  이 화면으로 이동하는 방법은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)을 참조하세요.
2. **Authenticator 앱** 옆에 있는 확인란을 선택한 후 **구성**을 선택합니다.

    ![보안 확인 설정 화면의 구성 단추](./media/authenticator-app-how-to/azureauthe.png)

    QR 코드가 있는 화면이 표시됩니다.

    ![QR 코드를 제공하는 화면](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **+**를 선택한 다음 회사 또는 학교 계정을 추가할지 지정합니다.
4. 카메라를 사용하여 QR 코드를 스캔한 다음 **완료** 를 선택하여 QR 코드 화면을 닫습니다.

    카메라가 올바르게 작동하지 않으면 [QR 코드 및 URL을 수동](#add-an-account-to-the-app-manually)으로 입력할 수 있습니다.

5. 앱에 그 아래 6자리 코드가 있는 계정 이름이 표시되면 완료된 것입니다.

    ![계정 화면](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>앱에 수동으로 계정 추가
1. 보안 확인 설정 화면으로 이동합니다.  이 화면으로 이동하는 방법은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.
2. **구성**을 선택합니다.

    ![보안 확인 설정 화면의 구성 단추](./media/authenticator-app-how-to/azureauthe.png)

    QR 코드가 있는 화면이 표시됩니다.  코드와 URL을 기록합니다.

    ![QR 코드와 URL을 제공하는 화면](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **+**를 선택한 다음 회사 또는 학교 계정을 추가할지 지정합니다.

4. 스캐너에서 **수동으로 코드 입력**을 선택합니다.

    ![QR 코드를 스캔하는 화면](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. 앱에서 해당 상자에 코드와 URL을 입력한 후 **마침**을 선택합니다.

    ![코드와 URL을 입력하기 위한 화면](./media/authenticator-app-how-to/manual.png)

6. 앱에 그 아래 6자리 코드가 있는 계정 이름이 표시되면 완료된 것입니다.

    ![계정 화면](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>장치의 지문이나 안면 인식 기능을 사용하여 앱에 계정 추가
조직에서는 인증 시도를 완료하기 위해 PIN을 요구할 수 있습니다. Microsoft Authenticator 앱에서는 PIN 대신 장치의 지문이나 안면 인식 기능을 사용할 수 있습니다. 앱에서 최초 확인 시 이 기능을 설정하기 위해 Touch ID(iOS) 또는 지문 인식을 대신 사용하는 옵션이 표시됩니다. 

Microsoft Authenticator에 Touch ID를 설정하려면 PIN을 사용하여 일반 확인 시도를 완료해야 합니다. Microsoft Authenticator가 자동으로 Touch ID를 지원하는 장치에 대해 해당 기능을 설정합니다. 

![Touch ID 설정 확인](./media/authenticator-app-how-to/touchid1.png)

해당 위치 다음부터 로그인 확인이 필요한 경우 받은 푸시 알림을 선택하고 PIN을 입력하는 대신 지문을 스캔합니다.

![푸시 알림](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>로그인할 때 앱 사용

앱에 계정이 추가되면 모두 제대로 구성되었는지 확인하는 테스트 확인을 수행하라는 메시지가 표시될 수 있습니다. 그런 다음 완료됩니다. 다음에 로그인할 때까지 다른 작업을 수행할 필요가 없습니다.

앱에서 확인 코드를 사용하도록 선택한 경우 홈페이지에 표시됩니다. 필요한 경우에 항상 새 코드를 갖도록 30초마다 변경됩니다. 하지만 로그인하고 확인 코드를 입력하라는 메시지가 나타날 때까지 아무것도 할 필요가 없습니다.  
