---
title: Azure Active Directory의 셀프 서비스 또는 평가판 등록 | Microsoft Docs
description: Azure AD(Azure Active Directory) 테넌트의 셀프 서비스 등록 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 67fd5ba9be2de1f79511c806ffaa0ae3001bfdcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Azure Active Directory의 셀프 서비스 등록이란?
이 문서에서는 Azure AD(Azure Active Directory)의 셀프 서비스 등록 및 지원 방법을 설명합니다. 관리되지 않는 Azure AD 테넌트에서 도메인 이름을 가져오려면 [Take over an unmanaged directory as administrator](domains-admin-takeover.md)(관리되지 않는 디렉터리를 관리자로 가져오기)를 참조하세요.

## <a name="why-use-self-service-signup"></a>셀프 서비스 등록을 사용하는 이유
* 고객이 원하는 서비스를 더욱 빠르게 제공합니다.
* 서비스에 대한 메일 기반 제공 사항을 만듭니다.
* 사용자가 기억하기 쉬운 업무용 메일 별칭을 사용하여 신속하게 ID를 만들 수 있도록 하는 메일 기반 등록 플로를 만듭니다.
* 셀프 서비스로 작성된 Azure AD 디렉터리를 관리되는 디렉터리로 변환하여 다른 서비스에 사용할 수 있습니다.

## <a name="terms-and-definitions"></a>용어 및 정의
* **셀프 서비스 등록**: 사용자가 클라우드 서비스에 등록하고 해당 메일 도메인을 기반으로 Azure AD에 ID가 자동으로 생성되게 하는 방법입니다.
* **관리되지 않는 Azure AD 디렉터리**: ID가 생성되는 디렉터리입니다. 관리되지 않는 디렉터리는 전역 관리자가 없는 디렉터리입니다.
* **메일로 확인된 사용자**: Azure AD의 사용자 계정의 한 유형입니다. 셀프 서비스 제공 사항에 등록한 후 자동으로 생성된 ID를 갖는 사용자를 메일로 확인된 사용자라고 합니다. 메일로 확인된 사용자는 creationmethod=EmailVerified로 태그가 지정된 디렉터리의 일반 멤버입니다.

## <a name="how-do-i-control-self-service-settings"></a>셀프 서비스 설정을 제어하는 방법
관리자는 현재 두 개의 셀프 서비스 컨트롤을 보유하며 다음을 제어할 수 있습니다.

* 사용자가 메일을 통해 디렉터리를 조인할 수 있습니다.
* 사용자가 응용 프로그램 및 서비스를 자신이 사용하도록 라이선스를 허여할 수 있습니다.

### <a name="how-can-i-control-these-capabilities"></a>이러한 기능은 어떻게 제어할 수 있나요?
관리자는 Azure AD cmdlet인 다음의 Set-MsolCompanySettings 매개 변수를 사용하여 이러한 기능을 구성할 수 있습니다.

* **AllowEmailVerifiedUsers** 는 관리되지 않는 디렉터리를 만들거나 결합시킬 수 있는지 여부를 제어합니다. 이 매개 변수를 $false로 설정하면 메일로 확인된 사용자가 디렉터리를 결합시킬 수 없습니다.
* **AllowAdHocSubscriptions**는 사용자가 셀프 서비스 등록을 수행하는 기능을 제어합니다. 이 매개 변수를 $false로 설정하면 사용자가 셀프 서비스 등록을 수행할 수 없습니다. 
  
  > [!NOTE]
  > Flow 및 PowerApps 평가판 등록은 **AllowAdHocSubscriptions** 설정에서 제어하지 않습니다. 자세한 내용은 다음 문서를 참조하세요.
  > * [내 기존 사용자가 Power BI를 사용하기 시작하지 않도록 방지하는 방법](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [조직의 Flow Q&A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>컨트롤이 어떻게 작동하나요?
이 두 매개 변수를 함께 사용하여 정의하면 셀프 서비스 등록을 더욱 세밀하게 제어할 수 있습니다. 예를 들어 Azure AD에 이미 계정이 있는 사용자만 다음 명령을 사용하여 셀프 서비스 등록을 수행할 수 있습니다. 즉, 메일로 확인된 계정을 먼저 만들어야 하는 사용자는 셀프 서비스 등록을 수행할 수 없습니다.

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
다음 순서도는 디렉터리와 셀프 서비스 등록에 대한 이러한 매개 변수 및 그 결과 조건의 다양한 조합을 설명합니다.

![][1]

매개 변수 사용 방법에 대한 자세한 내용 및 예는 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Microsoft Azure AD에 사용자 지정 도메인 이름 추가](add-custom-domain.md)
* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 참조](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
