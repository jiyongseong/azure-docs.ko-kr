---
title: 클라우드에서 Azure MFA 시작 | Microsoft Docs
description: 클라우드에서 Azure MFA 시작 방법을 설명하는 Microsoft Azure Multi-Factor Authentication 페이지입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 0a822d55e8d7bd0d503eb7d77f96dc9e60e1a4ba
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication 시작
이 문서에서는 클라우드에서 Azure Multi-Factor Authentication을 사용하기 시작하는 방법에 대해 설명합니다.

> [!NOTE]
> 다음 설명서에서는 **Azure Portal**을 통해 사용자를 사용하도록 설정하는 방법에 대해 설명합니다. O365 사용자를 위해 Azure Multi-Factor Authentication을 설정하는 방법을 찾는 경우 [Office 365용 Multi-Factor Authentication 설정](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)을 참조하세요.

![클라우드의 MFA](./media/howto-mfa-getstarted/mfa_in_cloud.png)

## <a name="prerequisite"></a>필수 요소
[Azure 구독 등록](https://azure.microsoft.com/pricing/free-trial/) - 아직 Azure 구독이 없는 경우 등록해야 합니다. Azure MFA를 시작하고 사용하려면 평가판 구독을 사용할 수 있습니다.

## <a name="enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 사용
사용자가 Azure Multi-Factor Authentication을 포함하는 라이선스를 가지고 있는 한 Azure MFA를 설정하도록 수행할 필요가 없습니다. 개별 사용자 단위로 2단계 인증 요구를 시작할 수 있습니다. Azure MFA를 활성화하는 라이선스는 다음과 같습니다.
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

이러한 세 가지 라이선스 중 하나가 없거나 모든 사용자를 포함할 충분한 라이선스가 없는 경우에도 괜찮습니다. 별도 단계를 수행하고 디렉터리에 [Multi-Factor Auth 공급자를 만들](concept-mfa-authprovider.md)어야 합니다.

## <a name="turn-on-two-step-verification-for-users"></a>사용자에 대한 2단계 확인을 설정합니다.

[사용자 또는 그룹에 대해 2단계 인증을 요구하는 방법](howto-mfa-userstates.md)에 나열된 절차 중 하나를 사용하여 Azure MFA를 사용합니다. 모든 로그인에 대해 2단계 인증을 적용하거나 문제가 될 때만 2단계 인증을 요구하는 조건부 액세스 정책을 만들도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계
클라우드에서 Azure Multi-Factor Authentication을 설정했으므로 배포를 구성 및 설정할 수 있습니다. 자세한 내용은 [Azure Multi-Factor Authentication 구성](howto-mfa-mfasettings.md)을 참조하세요.

