---
title: Azure MFA에 대한 액세스 및 사용 보고서 | Microsoft Docs
description: Azure Multi-Factor Authentication 기능 - 보고서를 사용하는 방법을 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: a3e7390e0df707c4898ad9573baa96b567499de1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 보고서

Azure Multi-Factor Authentication은 사용자 및 사용자의 조직에서 Azure Portal을 통해 액세스하고 사용할 수 있는 다양한 보고서를 제공합니다. 다음 표에는 사용 가능한 보고서가 나와 있습니다.

| 보고서 | 위치 | 설명 |
|:--- |:--- |:--- |
| 차단된 사용자 기록 | Azure AD > MFA 서버 > 사용자 차단/차단 해제 | 사용자를 차단 또는 차단 해제하도록 요청한 기록이 표시됩니다. |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |
| 온-프레미스 구성 요소의 사용량 | Azure AD > MFA 서버 > 작업 보고서 | NPS 확장, ADFS 및 MFA 서버를 통해 MFA의 전체 사용량에 대한 정보를 제공합니다. |
| 무시된 사용자 기록 | Azure AD > MFA 서버 > 일회성 바이패스 | 사용자의 Multi-Factor Authentication을 바이패스하는 요청 기록을 제공합니다. |
| 서버 상태 | Azure AD > MFA 서버 > 서버 상태 | 계정에 연결된 Multi-Factor Authentication 서버의 상태가 표시됩니다. |

## <a name="view-reports"></a>보고서 보기 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **MFA 서버**를 선택합니다.
3. 보려는 보고서를 선택합니다.

   <center>![클라우드](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell 보고

다음에 나오는 PowerShell을 사용하여 MFA에 등록한 사용자를 식별합니다.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

다음에 나오는 PowerShell을 사용하여 MFA에 등록하지 않은 사용자를 식별합니다.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>다음 단계

* [사용자](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [배포할 위치](concept-mfa-whichversion.md)
