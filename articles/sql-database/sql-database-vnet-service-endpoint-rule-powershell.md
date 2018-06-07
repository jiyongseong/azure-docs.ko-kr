---
title: SQL의 Virtual Network 서비스 끝점 및 규칙에 PowerShell 사용 | Microsoft Docs
description: Azure SQL Database에 대한 가상 서비스 끝점을 만들고 관리할 수 있는 PowerShell 스크립트를 제공합니다.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 2c53eaa0ff6a866f6a061ec1613630ad9626ac9b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database"></a>PowerShell을 사용하여 Azure SQL Database에 대한 가상 서비스 끝점 및 규칙 만들기

이 문서에서는 다음 작업을 수행하는 PowerShell 스크립트를 제공하고 설명합니다.

1. 서브넷에서 Microsoft Azure *가상 서비스 끝점*을 만듭니다.
2. Azure SQL Database 서버의 방화벽에 끝점을 추가하여 *가상 네트워크 규칙*을 만듭니다.

규칙을 만들기 위한 동기는 [Azure SQL Database에 대한 가상 서비스 끝점][sql-db-vnet-service-endpoint-rule-overview-735r]에서 설명됩니다.

> [!TIP]
> 서브넷에서 SQL Database에 대한 가상 서비스 끝점 *형식 이름*을 평가하거나 추가하기만 할 경우에는 더 많은 [직접 PowerShell 스크립트](#a-verify-subnet-is-endpoint-ps-100)로 바로 건너뛸 수 있습니다.

#### <a name="major-cmdlets"></a>주요 cmdlet

이 문서에서는 규칙을 만드는 데 사용되는 Azure SQL Database 서버의 ACL(액세스 제어 목록)에 서브넷 끝점을 추가하는 **New-AzureRmSqlServerVirtualNetworkRule** cmdlet을 중점적으로 설명합니다.

다음 목록은 **New-AzureRmSqlServerVirtualNetworkRule** 호출을 준비하기 위해 실행해야 하는 *주요* cmdlet의 시퀀스를 보여 줍니다. 이 문서에서 이러한 호출은 [스크립트 3 “가상 네트워크 규칙”](#a-script-30)에서 수행됩니다.

1. [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): 서브넷 개체를 만듭니다.

2. [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): 가상 네트워크를 만들어 가상 네트워크에 서브넷을 제공합니다.

3. [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): 가상 서비스 끝점을 서브넷에 할당합니다.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): 가상 네트워크에 적용된 업데이트를 유지합니다.

5. [New-AzureRmSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlservervirtualnetworkrule): 서브넷이 끝점이 된 후 서브넷을 가상 네트워크 규칙으로 Azure SQL Database 서버의 ACL에 추가합니다.
    - Azure RM PowerShell 모듈 버전 5.1.1에서 시작하는 **-IgnoreMissingVnetServiceEndpoint** 매개 변수를 제공합니다.

#### <a name="prerequisites-for-running-powershell"></a>PowerShell을 실행하기 위한 필수 구성 요소

- [Azure Portal][http-azure-portal-link-ref-477t] 등을 통해 Azure에 이미 로그인할 수 있습니다.
- 이미 PowerShell 스크립트를 실행할 수 있습니다.

> [!NOTE]
> 서버에 추가할 VNET/서브넷에 대해 서비스 엔드포인트가 설정되어 있는지 확인하세요. 그렇지 않으면 VNET 방화벽 규칙 만들기는 실패하게 됩니다.

#### <a name="one-script-divided-into-four-chunks"></a>4개 청크로 구분된 하나의 스크립트

데모 PowerShell 스크립트는 더 작은 스크립트 시퀀스로 구분됩니다. 구분을 통해 쉽게 알아볼 수 있고 유연성이 제공됩니다. 스크립트는 지정된 시퀀스로 실행되어야 합니다. 지금 스크립트를 실행할 시간이 없는 경우 실제 테스트 출력이 스크립트 4 뒤에 표시되어 있습니다.






<a name="a-script-10" />

## <a name="script-1-variables"></a>스크립트 1: 변수

이 첫 번째 PowerShell 스크립트는 값을 변수에 할당합니다. 후속 스크립트는 이러한 변수에 따라 결정됩니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 필요한 경우 값을 편집할 수 있습니다. 예를 들어 이미 리소스 그룹이 있는 경우 리소스 그룹 이름을 할당된 값으로 편집할 수 있습니다.
>
>  구독 이름은 스크립트로 편집되어야 합니다.

#### <a name="powershell-script-1-source-code"></a>PowerShell 스크립트 1 소스 코드

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```





<a name="a-script-20" />

## <a name="script-2-prerequisites"></a>스크립트 2: 필수 구성 요소

이 스크립트는 끝점 작업을 나타내는 다음 스크립트를 준비합니다. 이 스크립트는 다음과 같은 나열된 항목이 없는 경우 해당 항목을 만듭니다. 이러한 항목이 이미 있다고 확신할 경우 스크립트 2를 건너뛸 수 있습니다.

- Azure 리소스 그룹
- Azure SQL Database 서버

#### <a name="powershell-script-2-source-code"></a>PowerShell 스크립트 2 소스 코드

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>스크립트 3: 끝점 및 규칙 만들기

이 스크립트는 서브넷이 있는 가상 네트워크를 만듭니다. 그런 다음 **Microsoft.Sql** 끝점 형식을 서브넷에 할당합니다. 마지막으로 이 스크립트는 규칙을 만드는 데 사용되는 SQL Database 서버의 ACL(액세스 제어 목록)에 서브넷을 추가합니다.

#### <a name="powershell-script-3-source-code"></a>PowerShell 스크립트 3 소스 코드

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>스크립트 4: 정리

이 마지막 스크립트는 이전 스크립트를 데모용으로 만든 리소스를 삭제합니다. 그러나 이 스크립트는 다음 항목을 삭제하기 전에 확인 메시지를 표시합니다.

- Azure SQL Database 서버
- Azure 리소스 그룹

스크립트 1이 완료된 후 언제든지 스크립트 4를 실행할 수 있습니다.

#### <a name="powershell-script-4-source-code"></a>PowerShell 스크립트 4 소스 코드

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>스크립트 1~4의 실제 출력

다음에 테스트 실행의 출력이 요약된 형식으로 표시됩니다. 지금 실제로 PowerShell 스크립트를 실행하지 않으려는 경우 출력이 도움이 될 수 있습니다.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

기본 PowerShell 스크립트를 마쳤습니다.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>서브넷이 끝점인지 확인

이미 가상 서비스 끝점임을 의미하는 **Microsoft.Sql** 형식 이름이 할당된 서브넷이 있을 수 있습니다. [Azure Portal][http-azure-portal-link-ref-477t]을 사용하여 끝점에서 가상 네트워크 규칙을 만들 수 있습니다.

또는 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확실하지 않을 수 있습니다. 다음 PowerShell 스크립트를 실행하여 이러한 작업을 수행합니다.

1. 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확인합니다.
2. 선택적으로 형식 이름을 할당합니다(없는 경우).
    - 스크립트가 없는 형식 이름을 적용하기 전에 *확인* 메시지를 표시합니다.

#### <a name="phases-of-the-script"></a>스크립트의 단계

다음은 PowerShell 스크립트의 단계입니다.

1. Azure 계정에 로그인합니다(PS 세션당 한 번만 필요).  변수를 할당합니다.
2. 가상 네트워크를 검색하고 서브넷을 검색합니다.
3. **Microsoft.Sql** 끝점 서버 형식으로 태그가 지정된 서브넷이 있나요?
4. 서브넷에서 **Microsoft.Sql** 형식 이름의 가상 서비스 끝점을 추가합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 스크립트 위쪽에서 $ 변수에 할당된 값을 편집해야 합니다.

#### <a name="direct-powershell-source-code"></a>직접 PowerShell 소스 코드

확인 메시지가 표시될 때 예로 응답하지 않으면 이 PowerShell 스크립트는 아무것도 업데이트하지 않습니다. 이 스크립트는 서브넷에 **Microsoft.Sql** 형식 이름을 추가할 수 있습니다. 하지만 서브넷에 형식 이름이 없는 경우에만 추가를 시도합니다.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>실제 출력

다음 블록에는 실제 피드백이 모양이 편집되어 표시됩니다.

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

