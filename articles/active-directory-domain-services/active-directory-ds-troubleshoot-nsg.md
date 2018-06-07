---
title: 'Azure Active Directory Domain Services: 네트워크 보안 그룹 구성 문제 해결 | Microsoft Docs'
description: Azure AD Domain Services에 대한 NSG 구성 문제 해결
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: 2336277a960925a92af3578850453ba6ae78abda
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201269"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>관리되는 도메인에 대한 잘못된 네트워킹 구성 문제 해결
이 문서를 통해 다음과 같은 경고 메시지가 발생하는 네트워크 관련 구성 오류를 해결하고 문제를 해결합니다.

## <a name="alert-aadds104-network-error"></a>경고 AADDS104: 네트워크 오류
**경고 메시지:** *Microsoft는 이 관리되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하려고 할 때 이 문제가 발생할 수 있습니다. 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.*

잘못된 NSG 구성은 Azure AD Domain Services에 대한 네트워크 오류의 가장 일반적인 원인입니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)는 [특정 포트](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)에 대한 액세스를 허용해야 합니다. 이러한 포트가 차단되면 Microsoft는 관리되는 도메인을 모니터링하거나 업데이트할 수 없습니다. 또한 Azure AD 디렉터리와 관리되는 도메인 간의 동기화에 영향을 줍니다. NSG를 만드는 동안 이러한 포트를 열어 두어, 서비스가 중단되지 않도록 합니다.

### <a name="checking-your-nsg-for-compliance"></a>NSG의 규정 준수 확인

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 페이지로 이동합니다.
2. 테이블에서 관리되는 도메인이 사용하도록 설정된 서브넷에 연결된 NSG를 선택합니다.
3. 왼쪽 패널의 **설정**에서 **인바운드 보안 규칙**을 클릭합니다.
4. 작동 중인 규칙을 검토하여 [이러한 포트](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)에 대한 액세스를 막고 있는 규칙을 식별합니다.
5. 규정을 준수하도록 규칙을 삭제하거나 규칙을 추가하거나 NSG를 처음부터 새로 만드는 등 NSG를 편집합니다. 아래는 [규칙 추가](#add-a-rule-to-a-network-security-group-using-the-azure-portal) 또는 [규격 NSG 만들기](#create-a-nsg-for-azure-ad-domain-services-using-powershell)를 수행하는 단계입니다.

## <a name="sample-nsg"></a>샘플 NSG
다음 표에서는 Microsoft에서 정보를 모니터링, 관리 및 업데이트하도록 하면서, 관리되는 도메인을 안전하게 유지하는 샘플 NSG를 보여 줍니다.

![샘플 NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services에는 가상 네트워크에서 무제한 아웃바운드 액세스가 필요합니다. 가상 네트워크에 대한 아웃바운드 액세스를 제한하는 어떠한 추가 NSG 규칙도 만들지 않는 것이 좋습니다.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal을 사용하여 네트워크 보안 그룹에 규칙 추가
PowerShell을 사용하지 않으려는 경우 Azure Portal을 사용하여 NSG에 단일 규칙을 수동으로 추가할 수 있습니다. 네트워크 보안 그룹의 규칙을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 페이지로 이동합니다.
2. 테이블에서 관리되는 도메인이 사용하도록 설정된 서브넷에 연결된 NSG를 선택합니다.
3. 왼쪽 패널의 **설정**에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙**을 클릭합니다.
4. **추가**를 클릭하고 정보를 입력하여 규칙을 만듭니다. **확인**을 클릭합니다.
5. 규칙 테이블에 규칙이 있으면 제대로 만들어진 것입니다.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>PowerShell을 사용하여 Azure AD Domain Services에 대한 NSG 만들기
이 NSG는 Azure AD Domain Services에서 필요한 포트에 인바운드 트래픽을 허용하고 다른 원치 않는 인바운드 액세스는 거부하도록 구성됩니다.

**필수 조건: Azure PowerShell 설치 및 구성** 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결합니다](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> 최신 버전의 Azure PowerShell 모듈을 사용하는 것이 좋습니다. 이전 버전의 Azure PowerShell 모듈을 이미 설치한 경우 최신 버전으로 업데이트합니다.
>

PowerShell을 사용하여 새 NSG를 만들려면 다음 단계를 사용합니다.
1. Azure 구독에 로그인합니다.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. 세 가지 규칙으로 NSG를 만듭니다. 다음 스크립트는 Azure AD Domain Services를 실행하는 데 필요한 포트에 대한 액세스를 허용하는 NSG에 대한 세 가지 규칙을 정의합니다. 그런 다음, 이 스크립트는 해당 규칙을 포함하는 새 NSG를 만듭니다. 가상 네트워크에 배포된 워크로드에 필요한 경우 동일한 형식을 사용하여 다른 인바운드 트래픽을 허용하는 규칙을 더 추가합니다.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. 마지막으로 vnet과 선택한 서브넷에 NSG를 연결합니다.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Azure AD Domain Services에 대한 NSG를 만들고 적용하는 전체 스크립트
>[!TIP]
> 최신 버전의 Azure PowerShell 모듈을 사용하는 것이 좋습니다. 이전 버전의 Azure PowerShell 모듈을 이미 설치한 경우 최신 버전으로 업데이트합니다.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>도움 필요 시
[지원이 필요하거나 피드백을 공유하려면](active-directory-ds-contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
