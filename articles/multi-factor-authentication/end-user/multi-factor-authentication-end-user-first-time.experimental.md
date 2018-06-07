---
title: 회사 또는 학교 계정에 대한 2단계 인증 설정 | Microsoft Docs
description: '회사에서 Azure Multi-Factor Authentication을 구성하는 경우 2단계 인증에 등록하라는 메시지가 나타납니다. 설정하는 방법에 대해 알아봅니다. '
services: multi-factor-authentication
keywords: Active Directory 사용 방법, 클라우드의 Active Directory, Active Directory 자습서
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: ec96243a880272a67ed960d50baba43dba8ec207
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="set-up-my-account-for-two-step-verification"></a>2단계 인증에 내 계정 설정
2단계 인증은 다른 사람이 침입하기 어렵게 만들어 계정을 보호하는 추가 보안 단계입니다. 이 문서를 읽고 있다면 아마도 회사 또는 학교 관리자로부터 Multi-Factor Authentication에 대한 전자 메일을 받았을 것입니다. 또는 로그인을 시도했고 추가 보안 검증을 설정하라는 메시지를 받았을 수도 있습니다. 해당되는 경우 **자동 등록 프로세스를 완료해야만 로그인할 수 있습니다**.

이 문서를 통해 **회사 또는 학교 계정**을 설정할 수 있습니다. 고유한 개인 Microsoft 계정에 2단계 인증을 사용하도록 설정하려는 경우 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)를 참조하세요.

## <a name="set-up-your-account"></a>계정 설정

회사에서 2단계 인증을 사용하여 시작하도록 요청하는 경우 **Your admin has required that you set up this account for additional security verification**(관리자의 요청에 따라 이 계정에 추가 보안 확인을 설정해야 합니다)이 표시된 화면이 나타납니다.

![설정](./media/multi-factor-authentication-end-user-first-time/first.png)

시작하려면 **지금 설정하세요**를 클릭합니다.

로그인할 때 이와 같은 화면이 표시되지 않으면 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)의 지침에 따라 확인 옵션을 관리할 수 있는 설정 페이지를 찾습니다. 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>로그인 확인 방법 결정

등록 프로세스의 첫 번째 질문은 어떻게 연락을 받기 원하는지 입니다. 표에 있는 옵션을 살펴보고 링크를 사용하여 각 방법에 대한 설정 단계로 이동합니다.

| 연락 방법 | 설명 |
| --- | --- |
| [모바일 앱](#use-a-mobile-app-as-the-contact-method) |- **확인 시 알림 수신.** 이 옵션은 스마트폰이나 태블릿의 인증자 앱에 푸시 알림을 보냅니다. 알림을 확인한 후 올바르면 앱에서 **인증**을 선택합니다. 회사 또는 학교에서는 인증 전에 PIN을 입력해야 할 수 있습니다.<br>- **확인 코드 사용.** 이 모드에서 인증자 앱은 30초마다 업데이트되는 확인 코드를 생성합니다. 로그인 인터페이스에 가장 최근 확인 코드를 입력합니다.<br>Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) 및 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)에서 사용할 수 있습니다. |
| [휴대폰 통화 또는 문자](#use-your-mobile-phone-as-the-contact-method) |- **전화 통화**는 제공한 전화 번호에 자동으로 음성 전화를 겁니다. 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다.<br>- **문자 메시지**는 확인 코드를 포함하는 문자 메시지를 보냅니다. 텍스트에 있는 프롬프트에 따라 문자 메시지에 회신하거나 로그인 인터페이스에 제공한 확인 코드를 입력합니다. |
| [사무실 전화 통화](#use-your-office-phone-as-the-contact-method) |제공한 전화 번호에 자동으로 음성 전화를 겁니다. 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>연락 방법으로 모바일 앱 사용
이 방법을 사용하려면 휴대폰이나 태블릿에 인증자 앱을 설치해야 합니다. 이 문서의 단계는 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)에서 사용할 수 있는 Microsoft Authenticator 앱을 기반으로 합니다.

1. 드롭다운 목록에서 **모바일 앱**을 선택합니다.
2. **확인 시 알림 수신** 또는 **확인 코드 사용**을 선택한 후 **설정**을 선택합니다.

   ![추가 보안 확인 화면](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. 휴대폰이나 태블릿에서 앱을 열고 **+**를 선택하여 계정을 추가합니다. (Android 장치에서 세 개의 점을 선택한 후 **계정 추가**를 선택합니다.)
4. 회사 또는 학교 계정을 추가할지 지정합니다. 휴대폰에서 QR 코드 스캐너를 엽니다. 카메라가 제대로 작동하지 않는 경우 회사 정보를 수동으로 입력할 수 있습니다. 자세한 내용은 [수동으로 계정 추가](#add-an-account-manually)를 참조하세요.  
5. 모바일 앱 구성 화면에 표시되는 QR 코드 그림을 스캔합니다.  **완료** 를 선택하여 QR 코드 화면을 닫습니다.  

   ![QR 코드 화면](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. 휴대폰에서 활성화가 완료되면 **연락처**를 선택합니다.  이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.  
7. 회사에서 로그인 확인 인증 시 PIN이 필요한 경우 입력합니다.

   ![PIN 입력을 위한 상자](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. PIN 입력이 끝나면 **닫기**를 선택합니다. 이제 확인이 성공적으로 수행된 것입니다.
9. 모바일 앱에 액세스할 수 없는 경우 휴대폰 번호를 입력하는 것이 좋습니다. 드롭다운 목록에서 국가를 선택하고 국가 이름 옆의 상자에 휴대폰 번호를 입력합니다. **다음**을 선택합니다.
10. 이 시점에, Outlook 2010 이전 버전과 같은 비브라우저 앱 또는 Apple 장치의 네이티브 메일 앱에 앱 암호를 설정하라는 메시지가 표시됩니다 이 작업은 일부 앱에서 2단계 인증을 지원하지 않기 때문입니다. 이러한 앱을 사용하지 않는 경우 **완료**를 클릭하고 나머지 단계를 건너뜁니다.
11. 이러한 앱을 사용하는 경우 제공된 앱 암호를 복사하고 일반 암호 대신 이 암호를 응용 프로그램에 붙여 넣습니다. 여러 앱에 대해 동일한 앱 암호를 사용할 수 있습니다. 자세한 정보는 [앱 암호에 대한 도움말]을 참조하세요.
12. **Done**을 클릭합니다.

### <a name="add-an-account-manually"></a>수동으로 계정 추가
QR 판독기를 사용하는 대신 모바일 앱에 수동으로 계정을 추가하려는 경우 다음 단계를 따르세요.

1. **수동으로 계정 입력** 단추를 선택합니다.  
2. 바코드가 표시된 동일한 페이지에 제공된 코드와 URL을 입력합니다. 이 정보는 모바일 앱의 **코드**와 **URL** 상자로 입력됩니다.

    ![설정](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. 활성화가 완료되면 **연락처**를 선택합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>휴대폰을 연락 방법으로 사용
1. 드롭다운 목록에서 **인증 전화**를 선택합니다.  

    ![설정](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. 드롭다운 목록에서 국가를 선택하고 휴대폰 번호를 입력합니다.
3. 휴대폰에서 사용할 방법(문자 또는 전화)을 선택합니다.
4. **Contact me(연락처)**를 선택하여 전화 번호를 확인합니다. 선택한 모드에 따라 문자를 보내거나 전화를 합니다. 화면에 제공된 지침에 따른 다음 **확인**을 선택합니다.
5. 이 시점에, Outlook 2010 이전 버전과 같은 비브라우저 앱 또는 Apple 장치의 네이티브 메일 앱에 앱 암호를 설정하라는 메시지가 표시됩니다 이 작업은 일부 앱에서 2단계 인증을 지원하지 않기 때문입니다. 이러한 앱을 사용하지 않는 경우 **완료**를 클릭하고 나머지 단계를 건너뜁니다.
6. 이러한 앱을 사용하는 경우 제공된 앱 암호를 복사하고 일반 암호 대신 이 암호를 응용 프로그램에 붙여 넣습니다. 여러 앱에 대해 동일한 앱 암호를 사용할 수 있습니다. 자세한 정보는 [앱 암호에 대한 도움말]을 참조하세요.
7. **Done**을 클릭합니다.

## <a name="use-your-office-phone-as-the-contact-method"></a>사무실 전화를 연락 방법으로 사용
1. 드롭다운에서 **사무실 전화** 선택  

    ![설정](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. 전화 번호 상자가 회사 연락처 정보로 자동으로 채워집니다. 숫자가 잘못되었거나 누락된 경우 관리자에게 변경을 요청합니다.
3. **Contact me(연락처)**를 선택하여 전화 번호를 확인하면 해당 번호로 전화를 받게 됩니다. 화면에 제공된 지침에 따른 다음 **확인**을 선택합니다.
4. 이 시점에, Outlook 2010 이전 버전과 같은 비브라우저 앱 또는 Apple 장치의 네이티브 메일 앱에 앱 암호를 설정하라는 메시지가 표시됩니다 이 작업은 일부 앱에서 2단계 인증을 지원하지 않기 때문입니다. 이러한 앱을 사용하지 않는 경우 **완료**를 클릭하고 나머지 단계를 건너뜁니다.
5. 이러한 앱을 사용하는 경우 제공된 앱 암호를 복사하고 일반 암호 대신 이 암호를 응용 프로그램에 붙여 넣습니다. 여러 앱에 대해 동일한 앱 암호를 사용할 수 있습니다. 자세한 내용은 [앱 암호란 무엇인가요?](multi-factor-authentication-end-user-app-passwords.md)를 참조하세요.
6. **Done**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* 기본 옵션 변경 및 [2단계 인증을 위한 설정 관리](multi-factor-authentication-end-user-manage-settings.md)
* 2단계 인증을 지원하지 않는 네이티브 장치 앱에 대해 [앱 암호](multi-factor-authentication-end-user-app-passwords.md)를 설정합니다.
* 휴대폰 서비스가 없는 경우에도 빠르고 안전한 인증을 위해서는 [Microsoft Authenticator 앱](microsoft-authenticator-app-how-to.md)을 확인하세요.

