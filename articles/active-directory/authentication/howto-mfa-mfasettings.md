---
title: Azure Multi-Factor Authentication 구성 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 Azure Multi-Factor Authentication 설정을 구성하는 방법을 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 509df5274a5e44342390036511689fa8f8146807
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869804"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication 구성 설정

이 문서는 현재 준비하고 실행 중인 Azure Multi-Factor Authentication을 관리하는 데 도움이 됩니다. Azure Multi-Factor Authentication을 최대한 활용할 수 있게 하는 다양한 항목을 설명합니다. 모든 [버전의 Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need)에서 이러한 모든 기능을 사용할 수 있는 것은 아닙니다.

| 기능 | 설명 | 
|:--- |:--- |
| [사용자 차단 및 차단 해제](#block-and-unblock-users) |사용자 차단/차단 해제 기능을 사용하여 사용자가 인증 요청을 받지 못하도록 합니다. |
| [사기 행위 경고](#fraud-alert) |사용자가 자신의 리소스에 액세스하려는 사기성 시도를 보고할 수 있도록 사기 행위 경고 기능을 구성합니다. |
| [일회성 바이패스](#one-time-bypass) |일회성 바이패스 기능을 사용하여 사용자가 Multi-Factor Authentication을 _바이패스_하여 한 번에 인증할 수 있도록 허용합니다. |
| [사용자 지정 음성 메시지](#custom-voice-messages) |사용자 지정 음성 메시지 기능을 사용하여 Multi-Factor Authentication으로 사용자 고유의 녹음/녹화 또는 인사말을 사용합니다. |
| [캐싱](#caching-in-azure-multi-factor-authentication) |캐싱 기능을 사용하여 특정 시간 기간을 설정하여 후속 인증 시도가 자동으로 성공하도록 합니다. |
| [신뢰할 수 있는 IP](#trusted-ips) |관리되거나 페더레이션된 테넌트의 관리자는 신뢰할 수 있는 IP 기능을 사용하여 회사 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스할 수 있습니다. |
| [앱 암호](#app-passwords) |앱 암호 기능을 사용하여 응용 프로그램이 Multi-factor Authentication을 바이패스하고 작업을 계속할 수 있도록 합니다. |
| [신뢰할 수 있는 장치 및 브라우저용 Multi-Factor Authentication 저장](#remember-multi-factor-authentication-for-trusted-devices) |이 기능을 사용하여 사용자가 Multi-Factor Authentication을 통해 성공적으로 로그인한 후 정해진 일 수 동안 신뢰할 수 있는 장치 및 브라우저를 저장합니다. |
| [선택 가능한 인증 방법](#selectable-verification-methods) |이 기능을 사용하여 사용자가 사용할 수 있는 인증 방법의 목록을 선택합니다. |

## <a name="block-and-unblock-users"></a>사용자 차단 및 차단 해제

_사용자 차단 및 차단 해제_ 기능을 사용하여 사용자가 인증 요청을 받지 못하도록 합니다. 차단된 사용자에 대한 모든 인증 시도가 자동으로 거부됩니다. 사용자는 차단된 시간 이후 90일 동안 차단된 상태로 유지됩니다.

### <a name="block-a-user"></a>사용자 차단

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **사용자 차단/차단 해제**를 탐색합니다.
3. **추가**를 선택하여 사용자를 차단합니다.
4. **복제 그룹**을 선택합니다. **사용자 이름<span></span>@domain.com**으로 차단된 사용자에 대한 사용자 이름을 입력합니다. **이유** 필드에 주석을 입력합니다.
5. **추가**를 선택하여 사용자 차단을 완료합니다.

### <a name="unblock-a-user"></a>사용자 차단 해제

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **사용자 차단/차단 해제**를 탐색합니다.
3. 차단 해제할 사용자 옆에 있는 **작업** 열에서 **차단 해제**를 선택합니다.
4. **차단 해제 이유** 필드에서 주석을 입력합니다.
5. **차단 해제**를 선택하여 사용자 차단 해제를 완료합니다.

## <a name="fraud-alert"></a>사기 행위 경고

사용자가 자신의 리소스에 액세스하려는 사기성 시도를 보고할 수 있도록 _사기 행위 경고_ 기능을 구성합니다. 사용자가 모바일 앱 또는 자신의 전화를 사용하여 사기 행위 시도를 보고할 수 있습니다.

### <a name="turn-on-fraud-alerts"></a>사기 행위 경고 설정

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **사기 행위 경고**를 탐색합니다.

   ![사기 행위 경고 설정](./media/howto-mfa-mfasettings/fraudalert.png)

3. **사기 행위 경고 제출 허용** 설정을 **켜기**로 설정합니다.
4. **저장**을 선택합니다.

### <a name="configuration-options"></a>구성 옵션

- **사기 행위가 보고되면 사용자 차단**: 사용자가 사기 행위로 신고된 경우 해당 계정은 90일 동안 또는 관리자가 계정을 차단 해제할 때까지 차단됩니다. 관리자는 로그인 보고서를 사용하여 로그인을 검토하고 향후 사기를 예방하기 위해 적절한 조치를 취할 수 있습니다. 그런 다음 관리자는 사용자의 계정을 [차단 해제](#unblock-a-user)할 수 있습니다.
- **초기 인사말 중 사기 행위를 보고할 코드**: 사용자는 2단계 인증을 수행하라는 전화 통화를 받으면 일반적으로 **#** 를 눌러 로그인을 확인합니다. 사기 행위를 보고하려면 사용자가 **#** 를 누르기 전에 코드를 입력합니다. 이 코드의 기본값은 **0**이지만, 사용자가 지정할 수 있습니다.

  >[!NOTE]
  >Microsoft의 기본 음성 인사말은 사용자가 사기 행위 경고를 제출하기 위해 **0#** 키를 누르도록 지시합니다. **0**이 아닌 코드를 사용하려는 경우 사용자에게 적절한 지침과 함께 고유의 사용자 지정 음성 인사말을 기록하고 업로드해야 합니다.
  >

### <a name="view-fraud-reports"></a>사기 행위 보고 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **로그인**을 선택합니다. 사기 행위 보고서는 이제 표준 Azure AD 로그인 보고서의 일부입니다.

## <a name="one-time-bypass"></a>일회성 바이패스

_일회성 바이패스_ 기능을 통해 사용자는 2단계 인증을 수행하지 않고 한 번에 인증할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다. 모바일 앱 또는 전화가 알림 또는 전화 통화를 받지 못하는 경우 일회성 바이패스를 사용하여 사용자가 원하는 리소스에 액세스하도록 할 수 있습니다.

### <a name="create-a-one-time-bypass"></a>일회성 바이패스 만들기

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **일회성 바이패스**를 탐색합니다.

   ![일회성 바이패스 만들기](./media/howto-mfa-mfasettings/onetimebypass.png)

3. **추가**를 선택합니다.
4. 필요한 경우 바이패스에 대한 복제 그룹을 선택합니다.
5. **사용자 이름<span></span>@domain.com**으로 사용자 이름을 입력합니다. 바이패스가 지속되는 초 수를 입력합니다. 바이패스에 대한 이유를 입력합니다. 
6. **추가**를 선택합니다. 시간 제한은 즉시 적용됩니다. 일회성 바이패스가 만료되기 전에 사용자가 로그인해야 합니다. 

### <a name="view-the-one-time-bypass-report"></a>일회성 바이패스 보고서 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Active Directory** > **MFA 서버** > **일회성 바이패스**를 탐색합니다.

## <a name="custom-voice-messages"></a>사용자 지정 음성 메시지
_사용자 지정 음성 메시지_ 기능을 통해 2단계 인증에 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. 이러한 메시지는 Microsoft 녹음/녹화에 더하여 또는 이를 대체하여 사용할 수 있습니다.

이 작업을 시작하기 전에 다음 제한 사항을 고려하세요.

* 지원되는 파일 형식은 .wav 및 .mp3입니다.
* 파일 크기는 5MB로 제한됩니다.
* 인증 메시지 길이는 20초 미만이어야 합니다. 메시지가 20초보다 길면 인증이 실패할 수 있습니다. 메시지가 완료되고 인증이 시간 초과되기 전에 사용자가 응답하지 않을 수 있습니다.

### <a name="set-up-a-custom-message"></a>사용자 지정 메시지 설정

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **전화 통화 설정**을 탐색합니다.

   ![사용자 지정 전화 메시지 기록](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. **인사말 추가**를 선택합니다.
4. 인사말 형식을 선택합니다. 언어를 선택합니다.
5. 업로드할 .mp3 또는 .wav 사운드 파일을 선택합니다.
6. **추가**를 선택합니다.

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 캐싱

_캐싱_ 기능을 사용하여 사용자가 인증된 후 인증 시도를 할 수 있는 시간 기간을 설정할 수 있습니다. 지정된 시간 기간 이내에 사용자에 대한 이후 인증 시도는 자동으로 성공합니다. 캐싱은 첫 번째 요청이 진행 중인 동안 VPN과 같은 온-프레미스 시스템이 여러 확인 요청을 전송하는 경우 주로 사용됩니다. 이 기능을 사용하면 사용자가 진행 중인 첫 번째 확인에 성공한 후 자동으로 후속 요청이 성공할 수 있습니다. 

>[!NOTE]
>캐싱 기능은 Azure AD(Azure Active Directory)에 로그인하는 데 사용하기 위한 것이 아닙니다.

### <a name="set-up-caching"></a>캐싱 설정 

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **MFA 서버** > **캐싱 규칙**을 탐색합니다.

   ![캐싱 규칙 설정](./media/howto-mfa-mfasettings/cachingrules.png)

3. **추가**를 선택합니다.
4. 드롭다운 목록에서 **캐시 형식**을 선택합니다. 최대 **캐시 초 수**를 입력합니다. 
5. 필요한 경우 인증 유형을 선택하고 응용 프로그램을 지정합니다. 
6. **추가**를 선택합니다.

## <a name="trusted-ips"></a>신뢰할 수 있는 IP

Azure Multi-Factor Authentication의 _신뢰할 수 있는 IP_ 기능은 관리되는 또는 페더레이션된 테넌트의 관리자가 사용합니다. 이 기능은 회사 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스합니다. 이 기능은 관리자용 무료 버전이 아닌 Azure Multi-Factor Authentication의 정식 버전에서 사용할 수 있습니다. Azure Multi-Factor Authentication의 정식 버전을 가져오는 방법에 대한 자세한 내용은 [Azure Multi-Factor Authentication](multi-factor-authentication.md)을 참조하세요.

조직에서 온-프레미스 응용 프로그램 참고에 MFA를 제공하도록 NPS 확장을 배포하면 원본 IP 주소는 인증 시도가 통과하는 NPS 서버를 항상 나타냅니다.

| Microsoft Azure Active Directory 테넌트 유형 | 신뢰할 수 있는 IP 기능 옵션 |
|:--- |:--- |
| 관리 |**특정 IP 주소 범위**: 관리자는 회사 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스할 수 있는 IP 주소의 범위를 지정할 수 있습니다.|
| 페더레이션 |**모든 페더레이션된 사용자**: 조직 내에서 로그인하는 모든 페더레이션된 사용자는 2단계 인증을 바이패스할 수 있습니다. 사용자는 AD FS(Active Directory Federation Services)에서 발급된 클레임을 사용하여 인증을 바이패스합니다.<br/>**특정 IP 주소 범위**: 관리자는 회사 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스할 수 있는 IP 주소의 범위를 지정할 수 있습니다. |

신뢰할 수 있는 IP 바이패스는 회사 인트라넷 내부에서만 작동합니다. **모든 페더레이션된 사용자** 옵션을 선택하고 사용자가 회사 인트라넷 외부에서 로그인한 경우 해당 사용자는 2단계 인증을 사용하여 인증해야 합니다. 이 프로세스는 사용자가 AD FS 클레임을 제시하는 경우에도 동일합니다. 

**회사 네트워크 내부의 최종 사용자 환경**

신뢰할 수 있는 IP 기능이 사용하지 않도록 설정된 경우 브라우저 흐름에는 2단계 인증이 필요합니다. 이전 리치 클라이언트 응용 프로그램에는 앱 암호가 필요합니다. 

신뢰할 수 있는 IP 기능 사용하도록 설정된 경우 브라우저 흐름에는 2단계 인증이 필요하지 *않습니다*. 앱 암호는 사용자가 앱 암호를 만들지 않은 경우, 이전 리치 클라이언트 앱에는 필요하지 *않습니다*. 앱 암호를 사용한 후에는 암호가 여전히 필요합니다. 

**회사 네트워크 외부 최종 사용자 환경**

신뢰할 수 있는 IP 기능이 사용하도록 설정되었는지 여부와 상관없이 브라우저 흐름에는 2단계 인증이 필요합니다. 이전 리치 클라이언트 응용 프로그램에는 앱 암호가 필요합니다. 

### <a name="enable-named-locations-by-using-conditional-access"></a>조건부 액세스를 사용하여 명명된 위치를 사용하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **조건부 액세스** > **명명된 위치**를 선택합니다.
3. **새 위치**를 선택합니다.
4. 위치에 대한 이름을 입력합니다.
5. **신뢰할 수 있는 위치로 표시**를 선택합니다.
6. CIDR 표기법으로 IP 범위를 지정합니다(예: **192.168.1.1/24**).
7. **만들기**를 선택합니다.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>조건부 액세스를 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **조건부 액세스** > **명명된 위치**를 선택합니다.
3. **MFA에서 신뢰할 수 있는 IP 구성**을 선택합니다.
4. **서비스 설정** 페이지의 **신뢰할 수 있는 IP** 아래에 있는 다음 두 가지 옵션 중에서 선택합니다.
   
   * **내 인트라넷에서 발생하는 페더레이션된 사용자의 요청**: 이 옵션을 선택하려면 확인란을 선택합니다. 회사 네트워크에서 로그인하는 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하여 2단계 인증을 바이패스합니다. AD FS에 적절한 트래픽에 인트라넷 클레임을 추가하는 규칙이 있는지 확인합니다. 규칙이 없는 경우 AD FS에서 다음 규칙을 만듭니다.<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **공용 IP 중 특정 범위의 요청**: 이 옵션을 선택하려면 CIDR 표기법을 사용하여 텍스트 상자에 IP 주소를 입력합니다.
   
     * xxx.xxx.xxx.1에서 xxx.xxx.xxx.254 범위에 있는 IP 주소의 경우 **xxx.xxx.xxx.0/24** 같은 표기법을 사용합니다.
     * 단일 IP 주소의 경우 **xxx<span></span>.xxx.xxx.xxx/32** 같은 표기법을 사용합니다.
     
     최대 50개의 IP 주소 범위를 입력합니다. 이러한 IP 주소에서 로그인한 사용자는 2단계 인증을 바이패스합니다.

5. **저장**을 선택합니다.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>서비스 설정을 사용하여 신뢰할 수 있는 IP 기능을 사용하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**를 선택합니다.
3. **Multi-Factor Authentication**을 선택합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
5. **서비스 설정** 페이지의 **신뢰할 수 있는 IP** 아래에 있는 다음 두 가지 옵션 중에서 선택합니다.
   
   * **내 인트라넷에 페더레이션된 사용자의 요청**: 이 옵션을 선택하려면 확인란을 선택합니다. 회사 네트워크에서 로그인하는 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하여 2단계 인증을 바이패스합니다. AD FS에 적절한 트래픽에 인트라넷 클레임을 추가하는 규칙이 있는지 확인합니다. 규칙이 없는 경우 AD FS에서 다음 규칙을 만듭니다.<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **IP 주소 서브넷 중 지정된 범위의 요청**: 이 옵션을 선택하려면 CIDR 표기법을 사용하여 텍스트 상자에 IP 주소를 입력합니다. 
     
     * xxx.xxx.xxx.1에서 xxx.xxx.xxx.254 범위에 있는 IP 주소의 경우 **xxx.xxx.xxx.0/24** 같은 표기법을 사용합니다.
     * 단일 IP 주소의 경우 **xxx<span></span>.xxx.xxx.xxx/32** 같은 표기법을 사용합니다.
     
     최대 50개의 IP 주소 범위를 입력합니다. 이러한 IP 주소에서 로그인한 사용자는 2단계 인증을 바이패스합니다.

6. **저장**을 선택합니다.

![서비스 설정을 통해 신뢰할 수 있는 IP를 사용하도록 설정](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>앱 암호

Office 2010 이전 및 Apple 메일 등의 일부 응용 프로그램은 2단계 인증을 지원하지 않습니다. 이들 앱은 두 번째 인증을 허용하도록 구성되어 있지 않습니다. 이러한 응용 프로그램을 사용하려면 _앱 암호_ 기능을 이용합니다. 추가 암호 대신에 앱 암호를 사용하면 앱이 2단계 인증을 바이패스하고 계속 작동할 수 있도록 허용할 수 있습니다.

Microsoft Office 2013 클라이언트 이상에 대해 최신 인증이 지원됩니다. Outlook을 포함한 Office 2013 클라이언트는 최신 인증 프로토콜을 지원하고 2단계 인증과 함께 사용할 수 있습니다. 클라이언트가 사용하도록 설정된 후에는 클라이언트에 대해 앱 암호가 필요하지 않습니다.

### <a name="considerations-about-app-passwords"></a>앱 암호에 대한 고려 사항

앱 암호를 사용하는 경우 다음과 같은 중요한 사항을 고려하세요.

* 앱 암호는 응용 프로그램당 한 번만 입력됩니다. 사용자는 암호를 추적하거나 매번 입력할 필요가 없습니다.
* 실제 암호는 자동으로 생성되고 사용자가 제공하지 않습니다. 자동으로 생성된 암호는 공격자가 추측하기 어렵고 더 안전합니다.
* 사용자당 40개의 암호로 제한되어 있습니다. 
* 앱 암호는 회사 또는 학교 계정 외부에 알려지지 않기 때문에 암호를 캐시하고 온-프레미스 시나리오에서 사용하는 응용 프로그램은 실패하기 시작할 수 있습니다. 이러한 시나리오의 예로 온-프레미스에 있지만 보관된 메일은 클라우드에 있는 Exchange 전자 메일이 있습니다. 이 시나리오에서 동일한 암호는 작동하지 않습니다.
* 사용자 계정에서 Multi-Factor Authentication을 사용하도록 설정하면 Outlook 및 Microsoft Skype for Business 등 대부분의 비 브라우저 클라이언트에서 앱 암호를 사용할 수 있습니다. Windows PowerShell과 같은 비 브라우저 응용 프로그램을 통해 앱 암호를 사용하여 관리 작업을 수행할 수 없습니다. 이 작업은 사용자가 관리자 계정을 가지고 있는 경우에도 수행할 수 없습니다. PowerShell 스크립트를 실행하려면 강력한 암호로 서비스 계정을 만들고 해당 계정에 2단계 인증을 사용하도록 설정하지 않습니다.

>[!WARNING]
>앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 끝점과 통신하는 하이브리드 환경에서는 작동하지 않습니다. 온-프레미스를 인증하려면 도메인 암호가 필요합니다. 클라우드를 통해 인증하려면 앱 암호가 필요합니다.
>

### <a name="guidance-for-app-password-names"></a>앱 암호 이름에 대한 지침

앱 암호 이름은 해당 이름이 사용될 장치를 반영해야 합니다. Outlook, Word 및 Excel 등의 비 브라우저 응용 프로그램이 있는 노트북을 가지고 있는 경우 이러한 앱에 대해 **Laptop**이라는 하나의 앱 암호를 만듭니다. 그런 다음 데스크톱 컴퓨터에서 실행하는 동일한 응용 프로그램에 대해 **Desktop**이라는 다른 앱 암호를 만듭니다. 

>[!NOTE]
>응용 프로그램별로 하나의 앱 암호보다는 장치별로 하나의 앱 암호를 만드는 것이 좋습니다.

### <a name="federated-or-single-sign-on-app-passwords"></a>페더레이션된 또는 Single Sign-On 앱 암호

Azure AD는 온-프레미스 Windows Server Active Directory Domain Services(AD DS)를 통해 페더레이션 또는 SSO(Single Sign-On)를 지원합니다. 조직이 Azure AD를 사용하여 페더레이션되고 Azure Multi-Factor Authentication을 사용하는 경우 앱 암호에 대해 다음 사항을 고려하세요.

>[!NOTE]
>다음 사항은 페더레이션된(SSO) 고객에게만 적용됩니다.

* Azure AD에서 앱 암호를 확인하기 때문에 페더레이션을 바이패스합니다. 앱 암호를 설정할 때만 페더레이션이 능동적으로 사용됩니다.
* 페더레이션된(SSO) 사용자의 경우 수동 흐름과 달리 ID 공급자(IdP)에 연결되지 않습니다. 앱 암호는 회사 또는 학교 계정에 저장됩니다. 사용자가 회사에서 떠나는 경우 사용자의 정보는 **DirSync**를 사용하여 실시간으로 회사 또는 학교 계정으로 흐릅니다. 계정 비활성화/삭제는 동기화하는 데 최대 3시간이 걸릴 수 있으며, 이에 따라 Azure AD에서 앱 암호 비활성화/삭제가 지연될 수 있습니다.
* 앱 암호 기능을 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅/감사 기능은 앱 암호 기능과 함께 사용할 수 없습니다.
* 일부 고급 아키텍처에서는 클라이언트를 2단계 인증하려면 자격 증명 조합이 필요합니다. 이러한 자격 증명은 회사 또는 학교 계정 사용자 이름과 암호 및 앱 암호를 포함할 수 있습니다. 요구 사항은 인증이 수행되는 방법에 따라 달라집니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 회사 또는 학교 계정의 사용자 이름과 암호가 필요합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호가 필요합니다.

  예를 들어 다음과 같은 아키텍처가 있다고 가정하겠습니다.

  * Azure AD를 사용하여 Active Directory의 온-프레미스 인스턴스를 페더레이션합니다.
  * 온라인 Exchange를 사용하고 있습니다.
  * 온-프레미스로 Skype for Business를 사용하고 있습니다.
  * Azure Multi-Factor Authentication을 사용하고 있습니다.

  ![페더레이션된 조직에서 앱 암호 사용](./media/howto-mfa-mfasettings/federated.png)

  이 시나리오에서는 다음과 같은 자격 증명을 사용합니다.

  * Skype for Business에 로그인하려면 회사 또는 학교 계정 사용자 이름 및 암호를 사용합니다.
  * 온라인으로 Exchange에 연결하는 Outlook 클라이언트에서 주소록에 액세스하려면 앱 암호를 사용합니다.

### <a name="allow-users-to-create-app-passwords"></a>사용자가 앱 암호를 만들 수 있음

기본적으로 사용자가 앱 암호를 만들 수 없습니다. 앱 암호 기능을 사용하도록 설정해야 합니다. 사용자에게 앱 암호를 만드는 기능을 제공하려면 다음 절차를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**를 선택합니다.
3. **Multi-Factor Authentication**을 선택합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
5. **서비스 설정** 페이지에서 **사용자가 비 브라우저 앱에 로그인하기 위해 앱 암호를 만들 수 있음** 옵션을 선택합니다.

   ![사용자가 앱 암호를 만들 수 있음](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>앱 암호 만들기

사용자가 초기 등록을 하는 동안 앱 암호를 만들 수 있습니다. 사용자가 등록 프로세스의 끝에서 앱 암호를 만드는 옵션이 있습니다.

사용자는 등록 후 앱 암호를 만들 수도 있습니다. Azure Portal 또는 Office 365 포털에서 해당 설정을 통해 앱 암호를 변경할 수 있습니다. 사용자에 대한 자세한 내용과 세부 단계는 [Azure Multi-factor Authentication에서 앱 암호란?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md)을 참조하세요.

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>신뢰할 수 있는 장치용 Multi-Factor Authentication 저장
사용자가 신뢰할 수 있는 장치 및 브라우저용 _Multi-Factor Authentication 저장_ 기능은 모든 Multi-Factor Authentication 사용자에 대한 무료 기능입니다. 사용자는 Multi-Factor Authentication을 사용하여 장치에 성공적으로 로그인한 후 지정된 일 수 동안 이후 인증을 바이패스할 수 있습니다. 이 기능은 사용자가 동일한 장치에서 2단계 인증을 수행해야 하는 횟수를 최소화함으로써 유용성을 향상시킵니다.

>[!IMPORTANT]
>계정 또는 장치가 손상된 경우 신뢰할 수 있는 장치의 Multi-Factor Authentication을 저장해두는 것이 보안에 도움이 될 수 있습니다. 회사 계정이 손상되거나 신뢰할 수 있는 장치를 분실 또는 도난당한 경우 [모든 장치에서 Multi-Factor Authentication을 복원](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user)해야 합니다.
>
>복원 작업은 모든 장치에서 신뢰할 수 있는 상태를 해지하며, 사용자는 2단계 인증을 다시 수행해야 합니다. [2단계 인증을 위한 설정 관리](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)의 내용에 따라 사용자에게 자신의 장치에서 Multi-Factor Authentication을 복원하도록 지시할 수도 있습니다.
>

### <a name="how-the-feature-works"></a>기능의 작동 원리

Multi-Factor Authentication 저장 기능은 사용자가 로그인 시 **X일 동안 다시 묻지 않음** 상자를 선택한 경우 브라우저에서 영구 쿠키를 설정합니다. 그러면 쿠키가 만료될 때까지 동일한 브라우저에서 사용자에게 Multi-Factor Authentication 메시지가 다시 표시되지 않습니다. 사용자가 동일한 장치에서 다른 브라우저를 열거나 쿠키를 지우는 경우 다시 확인하라는 메시지가 표시됩니다. 

비 브라우저 응용 프로그램에는 앱이 최신 인증을 지원하는지 여부와 상관없이 **X일 동안 다시 묻지 않음** 옵션이 표시되지 않습니다. 이러한 앱에서는 1시간마다 새로운 액세스 토큰을 제공하는 _새로 고침 토큰_을 사용합니다. 새로 고침 토큰의 유효성이 확인되면 Azure AD가 마지막 2단계 인증이 지정된 일 수 내에 발생했는지 확인합니다. 

이 기능을 사용하면 일반적으로 매번 메시지를 표시하는 웹앱의 인증 횟수가 감소합니다. 하지만 최신 인증 클라이언트에서는 인증 횟수가 늘어납니다(일반적으로 90일마다 메시지 표시).

>[!IMPORTANT]
>사용자가 Azure Multi-Factor Authentication 서버 또는 제3자 다단계 인증 솔루션을 통해 AD FS에 대해 2단계 인증을 수행하는 경우 **Multi-Factor Authentication 저장** 기능은 AD FS의 **로그인 유지** 기능과 호환되지 않습니다.
>
>사용자가 AD FS에서 **로그인 유지**를 선택하고 자신의 장치를 Multi-Factor Authentication에 대해 신뢰할 수 있는 것으로 표시하는 경우 해당 사용자는 **Multi-Factor Authentication 저장** 일 수가 만료된 후 자동으로 인증되지 않습니다. Azure AD는 새로운 2단계 인증을 요청하지만 AD FS는 2단계 인증을 다시 수행하는 대신, 원본 Multi-Factor Authentication 클레임 및 날짜와 함께 토큰을 반환합니다. 이 반응으로 Azure AD 및 AD FS 간의 확인 루프가 시작됩니다.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication 저장 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**를 선택합니다.
3. **Multi-Factor Authentication**을 선택합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
5. **서비스 설정** 페이지의 **Multi-Factor Authentication 저장 관리** 아래에서 **사용자가 신뢰하는 장치에 대한 다단계 인증을 저장하도록 허용** 옵션을 선택합니다.

   ![신뢰할 수 있는 장치용 Multi-Factor Authentication 저장](./media/howto-mfa-mfasettings/remember.png)

6. 신뢰할 수 있는 장치가 2단계 인증을 바이패스할 수 있는 일 수를 설정합니다. 기본값은 14일입니다.
7. **저장**을 선택합니다.

### <a name="mark-a-device-as-trusted"></a>장치를 신뢰할 수 있음으로 표시

Multi-Factor Authentication 저장 기능을 사용하도록 설정한 후 사용자가 **다시 묻지 않음**을 선택하여 로그인할 때 장치를 신뢰할 수 있는 장치로 표시할 수 있습니다.

![신뢰할 수 있는 장치에 대해 "다시 묻지 않음" 선택](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>선택 가능한 인증 방법

_선택 가능한 인증 방법_ 기능을 사용하여 사용자가 사용할 수 있는 인증 방법을 선택할 수 있습니다. 다음 표에서는 이러한 방법의 간략한 개요를 제공합니다.

사용자가 Azure Multi-Factor Authentication에 자신의 계정을 등록한 경우, 사용하도록 설정한 옵션에서 원하는 인증 방법을 선택합니다. 사용자 등록 프로세스에 대한 지침은 [2단계 인증에 내 계정 설정](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)에 제공됩니다.

| 방법 | 설명 |
|:--- |:--- |
| 휴대폰에 전화 걸기 |자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. 이 전화 번호는 온-프레미스 Active Directory와 동기화되지 않습니다. |
| 휴대폰에 문자 메시지 전송 |확인 코드를 포함하는 문자 메시지를 보냅니다. 로그인 인터페이스에 이 확인 코드를 입력하라는 메시지가 표시됩니다. 이 프로세스를 단방향 SMS라고 합니다. 양방향 SMS는 사용자가 특정 코드를 다시 문자로 보내야 함을 의미합니다. 양방향 SMS는 2018년 11월 14일 이후 사용되지 않으며 지원되지 않습니다. 이때 양방향 SMS로 구성된 사용자는 _휴대폰에 전화 걸기_ 인증으로 자동 전환됩니다.|
| 모바일 앱을 통한 알림 |휴대폰이나 등록된 장치로 푸시 알림을 보냅니다. 사용자는 알림을 보고 **확인**을 선택하여 인증을 완료합니다. [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |
| 모바일 앱의 확인 코드 |Microsoft Authenticator 앱은 30초마다 새로운 OATH 확인 코드를 생성합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |

### <a name="enable-and-disable-verification-methods"></a>인증 방법 사용 및 사용 안 함

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**를 선택합니다.
3. **Multi-Factor Authentication**을 선택합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정**을 선택합니다.
5. **서비스 설정** 페이지의 **인증 옵션** 아래에서 사용자에게 제공하는 방법을 선택/선택 취소합니다.

   ![인증 방법 선택](./media/howto-mfa-mfasettings/authmethods.png)

6. **저장**을 클릭합니다.
