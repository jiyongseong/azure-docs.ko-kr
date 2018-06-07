---
title: Azure 스택 사용자의 PowerShell 환경 구성 | Microsoft Docs
description: Azure 스택 사용자의 PowerShell 환경 구성
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Azure 스택 사용자의 PowerShell 환경 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 사용자에 대 한 PowerShell 환경을 구성 하려면이 문서의 지침을 사용 합니다.
환경을 구성 하 고 나면 Azure 스택 리소스를 관리할 PowerShell을 사용할 수 있습니다. 예를 들어 제품을 구독할 가상 컴퓨터 만들기 및 Azure 리소스 관리자 템플릿 배포에 PowerShell을 사용할 수 있습니다.

>[!NOTE]
>이 문서는 Azure 스택 사용자 환경에 대 한 범위가 지정 됩니다. 클라우드 운영자 환경에 대 한 PowerShell을 설정 하려는 경우 참조는 [Azure 스택 운영자의 PowerShell 환경을 구성](../azure-stack-powershell-configure-admin.md) 문서.

## <a name="prerequisites"></a>필수 조건

이러한 필수 구성이 요소를 구성할 수는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.
* 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>사용자 환경을 구성 하 고 Azure 스택에 로그인

Azure 스택에 대 한 PowerShell을 구성 하려면 다음 스크립트 중 하나를 실행 하는 Azure 스택 배포 (Azure AD 또는 AD FS)의 유형에 기반으로 합니다.

Azure 스택 구성에서 값이 포함 된 다음 스크립트 변수를 대체 하 고 있는지 확인 합니다.

* AAD tenantName
* GraphAudience 끝점
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) 기반의 배포

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) 기반의 배포

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>리소스 공급자 등록

리소스 공급자에 포털을 통해 배포 된 모든 리소스가 없는 새 사용자 구독에 대 한 자동으로 등록 되지 않습니다. 다음 스크립트를 실행 하 여 리소스 공급자를 명시적으로 등록할 수 있습니다.

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>연결 테스트

설정 작업을 정답 스택에서 Azure 리소스를 만드는 PowerShell을 사용 하 여 연결을 테스트 합니다. 테스트를 응용 프로그램에 대 한 리소스 그룹을 만들어 가상 컴퓨터를 추가 합니다. "MyResourceGroup" 라는 리소스 그룹을 만들려면 다음 명령을 실행 합니다.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
