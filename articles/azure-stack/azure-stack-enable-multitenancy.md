---
title: Azure 스택에서 다중 테 넌 트를 사용 하도록 설정 | Microsoft Docs
description: Azure 스택에서 여러 Azure Active Directory 디렉터리를 지원 하는 방법을 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: mabrigg
ms.openlocfilehash: 59b0f8e4c7234b246d4fb54d065ff318939e2662
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Azure 스택에서 다중 테 넌 트를 사용 하도록 설정

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택에서 서비스를 사용 하려면 다중 Azure Active Directory (Azure AD) 테 넌 트에서 사용자를 지 원하는 Azure 스택을 구성할 수 있습니다. 예를 들어, 다음 시나리오를 고려해 보십시오.

 - Azure 스택이 설치 되어 contoso.onmicrosoft.com의 서비스 관리자가 있습니다.
 - Mary가 게스트 사용자가 있는 fabrikam.onmicrosoft.com, 디렉터리 관리자입니다. 
 - Mary의 회사에서 회사에서 IaaS 및 PaaS 서비스를 수신 하며 게스트 디렉터리 (fabrikam.onmicrosoft.com)에서 사용자가 로그인 하 고 contoso.onmicrosoft.com에서 스택에서 Azure 리소스를 사용할 수 있도록 합니다.

이 가이드는 데 필요한이 시나리오의 맥락에서 Azure 스택 다중 테 넌 트를 구성 하는 단계를 제공 합니다.  이 시나리오에서는 메리에 로그인 하 여 Contoso에서 Azure 스택 배포에서 서비스를 사용 하는 Fabrikam에서 사용자가 사용할 수 있도록 단계를 완료 해야 합니다.  

## <a name="before-you-begin"></a>시작하기 전에
필수 구성 요소에 맞게 Azure 스택에서 다중 테 넌 트를 구성 하기 전에 몇 가지 있습니다.
  
 - Azure 스택 (Contoso)에 설치 된 디렉터리와 게스트 디렉터리 (Fabrikam) 간에 메리 관리 단계를 조정 해야 합니다.  
 - 작업이 있는지 확인 [설치](azure-stack-powershell-install.md) 및 [구성](azure-stack-powershell-configure-admin.md) Azure 스택에 대 한 PowerShell.
 - [Azure 스택 도구 다운로드](azure-stack-powershell-download.md), 연결 및 Id 모듈을 가져옵니다.

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary는 필요 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) Azure 스택에 대 한 액세스. 

## <a name="configure-azure-stack-directory"></a>Azure 스택 디렉터리 구성
이 섹션에서는 Azure 스택 Fabrikam Azure AD 디렉터리 테 넌 트 로부터 로그인을 허용 하도록 구성할 수 있습니다.

### <a name="onboard-guest-directory-tenant"></a>온보드 게스트 디렉터리 테 넌 트
다음, 온보드 게스트 디렉터리 테 넌 트 (Fabrikam) Azure 스택에 있습니다.  이 단계에서는 Azure 리소스 관리자가 사용자 및 게스트 디렉터리 테 넌 트의 서비스 사용자를 허용 하도록 구성 합니다.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>게스트의 디렉터리 구성
Azure 스택 디렉터리의 단계를 완료 한 후 Mary 게스트 디렉터리에 액세스 하는 Azure 스택에 동의 제공 하 고 Azure 스택 게스트 디렉터리에 등록 해야 합니다. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>게스트 디렉터리와 Azure 스택을 등록 하는 중입니다.
게스트 디렉터리 관리자가 Fabrikam의 디렉터리에 액세스할 수 있는 Azure 스택에 대 한 동의 제공한, 일단 Mary는 Fabrikam의 디렉터리 테 넌 트와 Azure 스택을 등록 해야 합니다.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>사용자가 로그인 하려면
과 메리 온보드 Mary의 디렉터리에 단계를 완료 했으므로 Mary는 Fabrikam의 사용자가 로그인 할를 보낼 수 있습니다.  Fabrikam 사용자 (즉, fabrikam.onmicrosoft.com 접미사와 함께 사용자)에 방문 하 여 로그인 https://portal.local.azurestack.external합니다.  

Mary는이 모든 초보자 [외래 주체](../role-based-access-control/rbac-and-directory-admin-roles.md) Fabrikam 디렉터리 (즉, fabrikam.onmicrosoft.com 접미사 없이 Fabrikam 디렉터리의 사용자)에 사용 하 여 로그인을 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.  이 URL을 사용 하지 않으면 해당 기본 디렉터리 (Fabrikam)에 전송 되 고 관리자가 동의 하지 한다는 오류가 표시 있습니다.

## <a name="next-steps"></a>다음 단계

- [위임 된 공급자 관리](azure-stack-delegated-provider.md)
- [Azure 스택 주요 개념](azure-stack-key-features.md)
