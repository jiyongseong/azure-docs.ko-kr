---
title: PowerShell을 사용하여 Azure Active Directory Domain Services 사용 | Microsoft Docs
description: PowerShell을 사용하여 Azure Active Directory Domain Services 사용
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 22be243b9da5afd93744ca9410b63a8ec936088a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>PowerShell을 사용하여 Azure Active Directory Domain Services 사용
이 문서에서는 PowerShell을 사용하여 Azure AD(Azure Active Directory) Domain Services를 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="task-1-install-the-required-powershell-modules"></a>작업 1: 필요한 PowerShell 모듈 설치

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD PowerShell 설치 및 구성
이 문서의 지침에 따라 [Azure AD PowerShell 모듈을 설치하고 Azure AD에 연결](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)합니다.

### <a name="install-and-configure-azure-powershell"></a>Azure PowerShell 설치 및 구성
이 문서의 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)합니다.


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>작업 2: Azure AD 디렉터리에서 필요한 서비스 주체 만들기
다음 PowerShell 명령을 입력하여 Azure AD 디렉터리의 Azure AD Domain Services에 필요한 서비스 주체를 만듭니다.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>작업 3: 'AAD DC Administrators' 그룹 만들기 및 구성
다음 작업은 관리되는 도메인에서 관리 작업을 위임하는 데 사용할 관리자 그룹을 만드는 것입니다.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

그룹을 만들었으므로 이제 그룹에 몇 명의 사용자를 추가합니다.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>작업 4: Azure AD Domain Services 리소스 공급자 등록
다음 PowerShell 명령을 입력하여 Azure AD Domain Services에 대한 리소스 공급자를 등록합니다.
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>작업 5: 리소스 그룹 만들기
다음 PowerShell 명령을 입력하여 리소스 그룹을 만듭니다.
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

이 리소스 그룹에 가상 네트워크 및 Azure AD Domain Services의 관리되는 도메인을 만들 수 있습니다.


## <a name="task-6-create-and-configure-the-virtual-network"></a>작업 6: 가상 네트워크 만들기 및 구성
이제 Azure AD Domain Services를 사용하도록 설정할 가상 네트워크를 선택합니다. Azure AD Domain Services에 대한 전용 서브넷을 만들어야 합니다. 이 전용 서브넷에는 워크로드 VM을 배포하지 마세요.

다음 PowerShell 명령을 입력하여 Azure AD Domain Services에 대한 전용 서브넷을 사용하여 가상 네트워크를 만듭니다.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>작업 7: Azure AD Domain Services 관리되는 도메인 프로비전
다음 PowerShell 명령을 입력하여 디렉터리에 대해 Azure AD Domain Services를 사용하도록 설정합니다.

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **관리되는 도메인을 프로비전한 후 추가 구성 단계를 잊지 말고 진행합니다.**
> 관리되는 도메인이 프로비전되면 다음 작업을 완료해야 합니다.
> * 가상 컴퓨터가 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 **[DNS 설정을 업데이트](active-directory-ds-getting-started-dns.md)**합니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 **[Azure AD Domain Services에 대한 암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md)**합니다.
>


## <a name="powershell-script"></a>PowerShell 스크립트
이 문서에 나열된 모든 작업을 수행하는 데 사용되는 PowerShell 스크립트는 아래에 나와 있습니다. 이 스크립트를 복사하고 '.ps1' 확장명을 사용해서 파일을 저장합니다. PowerShell에서 또는 PowerShell ISE(통합 스크립팅 환경)를 사용하여 스크립트를 실행합니다.

> [!NOTE]
> **이 스크립트를 실행하는 데 필요한 권한** Azure AD Domain Services를 사용하도록 설정하려면 Azure AD 디렉터리에 대해 전역 관리자여야 합니다. 또한 Azure AD Domain Services를 사용하도록 설정할 가상 네트워크에서 "참가자" 이상의 권한이 있어야 합니다.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **관리되는 도메인을 프로비전한 후 추가 구성 단계를 잊지 말고 진행합니다.**
> 관리되는 도메인이 프로비전되면 다음 작업을 완료해야 합니다.
> * 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 Azure AD Domain Services에 대한 암호 동기화를 사용하도록 설정합니다.
>

## <a name="next-steps"></a>다음 단계
관리되는 도메인을 만든 후에는 관리되는 도메인을 사용할 수 있도록 다음 구성 작업을 수행합니다.

* [관리되는 도메인을 가리키도록 가상 네트워크에 대한 DNS 서버 설정 업데이트](active-directory-ds-getting-started-dns.md)
* [관리되는 도메인에 대한 암호 동기화 사용](active-directory-ds-getting-started-password-sync.md)
