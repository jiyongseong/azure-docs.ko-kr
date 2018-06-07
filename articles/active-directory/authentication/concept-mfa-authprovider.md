---
title: Azure Multi-Factor Auth 공급자 시작 | Microsoft Docs
description: Azure Multi-Factor Auth 공급자를 만드는 방법에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 453b8cc399c78ddb26ae601abf64626d2a6bf36f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Azure Multi-Factor Authentication 공급자 시작
두 단계 인증은 기본적으로 Azure Active Directory 및 Office 365 사용자가 있는 전역 관리자를 위해 사용할 수 있습니다. 그러나 [고급 기능](howto-mfa-mfasettings.md)을 활용하려는 경우 Azure MFA(Multi-Factor Authentication)의 전체 버전을 구입해야 합니다.

Azure Multi-factor Authentication 공급자는 정식 버전의 Azure MFA에서 제공하는 기능을 활용하는 데 사용됩니다. **Azure MFA, Azure AD Premium 또는 EMS(Enterprise Mobility + Security)를 통해 라이선스를 갖고 있지 않은** 사용자를 위한 기능입니다.  Azure MFA, Azure AD Premium 및 EMS는 기본적으로 Azure MFA의 전체 버전을 포함합니다. 라이선스를 보유한 경우 Multi-Factor Auth 공급자가 필요하지 않습니다.

SDK를 다운로드하려면 Azure Multi-Factor Auth 공급자가 필요합니다.

> [!IMPORTANT]
> Azure Multi-Factor Authentication SDK(소프트웨어 개발 키트) 사용 중단이 발표되었습니다. 이 기능은 새 고객에게 더 이상 지원되지 않습니다. 현재 고객은 2018년 11월 14일까지 계속 SDK를 사용할 수 있습니다. 해당 시점 이후에는 SDK에 대한 호출에 실패합니다.

> [!IMPORTANT]
>SDK를 다운로드하려면 Azure MFA, AAD Premium 또는 EMS 라이선스가 있더라도 Azure Multi-Factor Auth 공급자를 만들어야 합니다.  이를 위해 Azure Multi-Factor Auth 공급자를 만들고 이미 라이선스를 보유한 경우 공급자는 반드시 **활성화된 사용자당** 모델을 사용하도록 합니다. 그런 다음 Azure MFA, Azure AD Premium 또는 EMS 라이선스를 포함하는 디렉터리에 공급자를 연결합니다. 이 구성을 사용하면 사용자가 소유한 라이선스 수보다는 2단계 인증을 수행하는 고유 사용자가 더 많은 경우에만 요금이 청구됩니다. 

## <a name="what-is-an-mfa-provider"></a>MFA 공급자란?

Azure Multi-Factor Authentication에 대한 라이선스가 없는 경우 사용자에 대해 2단계 인증을 요구하는 인증 공급자를 만들 수 있습니다.

두 가지 유형의 인증 공급자가 있으며 Azure 구독이 청구되는 방식에 따라 구분합니다. 인증 단위 옵션은 한 달에 테넌트에 대해 수행한 인증 수를 계산합니다. 이 옵션은 가끔씩만 인증하는 여러 사용자가 있는 경우에 가장 적합합니다. 사용자당 옵션은 테넌트에서 한 달 동안 2단계 인증을 수행하는 개인 수를 계산한 것입니다. 이 옵션은 라이선스를 갖고 있지만 라이선스 한도를 초과하여 더 많은 사용자까지 MFA를 확장해야 하는 사용자가 있는 경우 가장 적합합니다.

## <a name="create-an-mfa-provider"></a>MFA 공급자 만들기

다음 단계를 따라 Azure Portal에서 Azure Multi-Factor Authentication 공급자를 만듭니다.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** > **MFA 서버** > **공급자**를 선택합니다.

   ![공급자][Providers]

3. **추가**를 선택합니다.
4. 다음 필드를 입력한 다음 **추가**를 선택합니다.
   - **이름** - 공급자의 이름입니다.
   - **사용 모델** – 두 가지 옵션 중 하나를 선택합니다.
      * 인증당 - 인증 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 소비자 지향 응용 프로그램에서 Azure Multi-Factor Authentication을 사용하는 시나리오에 사용됩니다.
      * 활성화된 사용자별 – 활성화된 사용자 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 Office 365와 같은 응용 프로그램에 대한 직원 액세스에 사용합니다. 이미 Azure MFA에 대한 사용이 허가된 사용자가 있는 경우 이 옵션을 선택합니다.
   - **구독** - 공급자를 통해 2단계 인증 작업에 대해 청구되는 Azure 구독입니다. 
   - **디렉터리** – 공급자와 연결된 Azure Active Directory 테넌트입니다.
      * 공급자를 만드는 데 Azure AD 디렉터리가 필요하지는 않습니다. Azure Multi-Factor Authentication 서버를 다운로드하려는 경우에만 이 상자를 비워둡니다.
      * 공급자를 Azure AD 디렉터리와 연결하여 고급 기능을 활용해야 합니다.
      * 한 공급자를 한 Azure AD 디렉터리에만 연결할 수 있습니다.

## <a name="manage-your-mfa-provider"></a>MFA 공급자 관리

MFA 공급자를 만든 후에는 사용 모델(활성화된 사용자별 또는 인증별)을 변경할 수 없습니다. 하지만 MFA 공급자를 삭제하고 다른 사용 모델로 새 공급자를 만들 수 있습니다.

현재 Multi-Factor Auth 공급자가 Azure AD 디렉터리(Azure AD 테넌트라고도 함)에 연결된 경우 MFA 공급자를 안전하게 삭제하고 동일한 Azure AD 테넌트에 연결된 새 공급자를 만들 수 있습니다. 또는 MFA로 설정된 모든 사용자를 처리할 만큼 충분한 MFA, Azure AD Premium 또는 Enterprise Mobility + Security(EMS) 라이선스를 구입한 경우 MFA 공급자를 모두 삭제할 수 있습니다.

하지만 MFA 공급자가 Azure AD 테넌트에 연결되어 있지 않거나 새 MFA 공급자를 다른 Azure AD 테넌트에 연결한 경우 사용자 설정 및 구성 옵션이 전송되지 않습니다. 또한 새 MFA 공급자를 통해 생성된 활성화 자격 증명을 사용하여 기존 Azure MFA 서버를 다시 활성화해야 합니다. MFA 서버를 새 MFA 공급자에 연결하기 위해 다시 활성화해도 전화 및 문자 메시지 인증에는 영향을 미치지 않지만 모바일 앱을 다시 활성화할 때까지 모바일 앱 알림이 모든 사용자에 대해 작동 중지됩니다.

## <a name="next-steps"></a>다음 단계

[Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "MFA 공급자 추가"
