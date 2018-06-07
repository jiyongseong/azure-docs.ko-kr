---
title: 특정 조직의 B2B 사용자 초대 허용 또는 차단 - Azure Active Directory | Microsoft Docs
description: Azure Portal 또는 PowerShell을 사용하여 특정 도메인에서 B2B 사용자를 허용하거나 차단하는 액세스 또는 거부 목록을 설정하는 방법을 살펴봅니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3b4b57dd2299c6278fe823f59a4f2c7d8721f712
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>특정 조직의 B2B 사용자 초대 허용 또는 차단

허용 목록이나 거부 목록을 사용하여 특정 조직의 B2B 사용자 초대를 허용 또는 차단할 수 있습니다. 예를 들어 개인 이메일 주소 도메인을 차단하기 위해 Outlook.com Gmail.com 등을 포함하는 거부 목록을 설정할 수 있습니다. Contoso.com, Fabrikam.com 및 Litware.com 같은 다른 기업과 비즈니스 파트너 관계에 있고 초대를 이 조직으로만 제한하려는 경우 Contoso.com, Fabrikam.com 및 Litware.com을 허용 목록에 추가할 수 있습니다.
  
## <a name="important-considerations"></a>중요 고려 사항

- 허용 목록 또는 거부 목록을 만들 수 있습니다. 두 목록을 모두 설정할 수는 없습니다. 기본적으로 허용 목록에 없는 모든 도메인은 거부 목록에 있게 되고 반대의 경우도 마찬가지가 됩니다. 
- 조직당 하나의 정책만 만들 수 있습니다. 더 많은 도메인을 포함하도록 정책을 업데이트하거나 정책을 삭제하고 새 정책을 만들 수도 있습니다. 
- 이 목록은 OneDrive for Business 및 SharePoint Online 허용/차단 목록과는 독립적으로 적용됩니다. SharePoint Online에서의 개별 파일 공유를 제한하려면 OneDrive for Business 및 SharePoint Online에 대한 허용 또는 거부 목록을 설정해야 합니다. 자세한 내용은 [SharePoint Online 및 OneDrive for Business에서 제한된 도메인 공유](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)를 참조하세요.
- 이 목록은 이미 초대를 사용한 외부 사용자에게는 적용되지 않습니다. 목록은 설정한 후에 시행됩니다. 사용자 초대가 대기 상태이며 해당 도메인을 차단하는 정책을 설정한 경우 해당 사용자가 초대를 사용하려 하면 실패하게 됩니다.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>포털에서 허용 또는 차단 목록 정책 설정

기본적으로 **모든 도메인에 초대 보내기 허용**(가장 포괄적) 설정을 사용합니다. 이 경우 모든 조직의 B2B 사용자를 초대할 수 있습니다.

### <a name="add-a-deny-list"></a>거부 목록 추가

가장 일반적인 시나리오로, 조직이 대부분의 조직과 협력하고자 하지만 특정 도메인의 사용자는 B2B 사용자로 초대하지 못하게 차단하려는 경우입니다.

거부 목록을 추가하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **사용자** > **사용자 설정**을 선택합니다.
3. **외부 사용자**에서 **외부 공동 작업 설정 관리**를 선택합니다.
4. **공동 작업 제한**에서 **지정된 도메인에 초대 거부**를 선택합니다.
5. **대상 도메인**에서 차단할 도메인 중 하나의 이름을 입력합니다. 여러 도메인 경우 도메인을 하나씩 새 줄에 입력합니다. 예: 

   ![추가된 도메인에 거부 옵션 표시](./media/allow-deny-list/DenyListSettings.png)
 
6. 완료되면 **저장**을 클릭합니다.

정책을 설정한 후 차단된 도메인의 사용자를 초대하려 하면 해당 사용자의 도메인이 초대 정책에 따라 차단된 상태라고 설명하는 메시지가 나타납니다.
 
### <a name="add-an-allow-list"></a>허용 목록 추가

더 제한적인 구성으로, 허용 목록에서 특정 도메인을 설정하여 여기에 없는 다른 조직이나 도메인에 대한 초대를 제한할 수 있습니다. 

허용 목록을 사용하려면 비즈니스 요구를 완벽하게 평가할 수 있는 충분한 시간이 필요합니다. 이 정책은 지나치게 제한적이기 때문에 사용자가 협력을 위해 이메일로 문서를 보내거나 규제를 받지 않는 다른 IT 방식을 모색할 수 있습니다.


허용 목록을 추가하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **사용자** > **사용자 설정**을 선택합니다.
3. **외부 사용자**에서 **외부 공동 작업 설정 관리**를 선택합니다.
4. **공동 작업 제한**에서 **지정된 도메인에 초대 거부(가장 제한적)** 를 선택합니다.
5. **대상 도메인**에서 허용할 도메인 중 하나의 이름을 입력합니다. 여러 도메인 경우 도메인을 하나씩 새 줄에 입력합니다. 예: 

   ![추가된 도메인에 허용 옵션 표시](./media/allow-deny-list/AllowListSettings.png)
 
6. 완료되면 **저장**을 클릭합니다.

정책을 설정한 후 허용 목록에 없는 도메인의 사용자를 초대하려 하면 해당 사용자의 도메인이 초대 정책에 따라 차단된 상태라고 설명하는 메시지가 나타납니다.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>허용 목록과 거부 목록 간 전환 

한 정책을 다른 정책으로 전환하면 기존 정책 구성을 버리게 됩니다. 전환 수행에 앞서 상세 구성 정보를 백업해야 합니다. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>PowerShell을 사용한 허용 또는 차단 목록 정책 설정

### <a name="prerequisite"></a>필수 요소

PowerShell을 사용하여 허용 또는 거부 목록을 설정하려면 Azure Active Directory Module for Windows PowerShell의 미리 보기 버전을 설치해야 합니다. 특히 AzureADPreview module version 2.0.0.98 이상을 설치합니다.

모듈 버전(및 설치 여부)을 확인하려면
 
1. 향상된 사용자 권한(관리자 권한으로 실행)으로 Windows PowerShell을 엽니다. 
2. 다음 명령을 실행하여 컴퓨터에 설치된 Azure Active Directory Module for Windows PowerShell 버전이 있는지 확인합니다.

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

모듈이 설치되지 않았거나 필요한 버전이 없으면 다음 중 하나를 수행합니다.

- 결과가 반환되지 않으면 다음 명령을 실행하여 최신  AzureADPreview module 버전을 설치합니다.
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- 결과에 하나의  AzureAD module만 표시되면 다음 명령을 실행하여  AzureADPreview module을 설치합니다. 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- 결과에  AzureADPreview module이 하나 표시되나 버전이  2.0.0.98 미만이면 다음 명령을 실행하여 업데이트합니다. 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- 결과에  AzureADPreview module이 모두 표시되나  AzureADPreview module 버전이  2.0.0.98 미만이면 다음 명령을 실행하여 업데이트합니다. 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>AzureADPolicy cmdlet을 사용한 정책 구성

허용 또는 거부 목록을 만들려면 [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet을 사용합니다. 다음 예제에서는 "live.com" 도메인을 차단하는 거부 목록을 설정하는 방법을 보여줍니다.

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

다음은 같은 예제이나 정책 정이 인라인이 있습니다.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

허용 또는 거부 목록 정책을 설정하려면 [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet을 사용합니다. 예: 

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

정책을 가져오려면 [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet을 사용합니다. 예: 

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

정책을 제거오려면 [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet을 사용합니다. 예: 

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>다음 단계

- Azure AD B2B의 개요는 [Azure AD B2B 공동 작업이란?](what-is-b2b.md)을 참조하세요.
- 조건부 액세스 및 B2B 공동 작업에 대한 내용은 [B2B 공동 작업 사용자에 대 한 조건부 액세스](conditional-access.md)를 참조하세요.



