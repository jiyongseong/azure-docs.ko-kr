---
title: PaaS 리소스에 대한 네트워크 액세스 제한 - Azure PowerShell | Microsoft Docs
description: 이 문서에서는 Azure PowerShell을 사용하여 가상 네트워크 서비스 엔드포인트를 통해 Azure Storage 및 Azure SQL Database와 같은 Azure 리소스에 대한 네트워크 액세스를 제한하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b3977e045751165947243c67291e81b998b5fcb5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>PowerShell을 사용하는 가상 네트워크 서비스 끝점으로 PaaS 리소스에 대한 네트워크 액세스 제한

가상 네트워크 서비스 끝점을 사용하면 일부 Azure 서비스 리소스에 대한 네트워크 액세스를 가상 네트워크 서브넷으로 제한할 수 있습니다. 리소스에 대한 인터넷 액세스를 제거할 수도 있습니다. 서비스 끝점은 가상 네트워크에서 지원되는 Azure 서비스로의 직접 연결을 제공하므로 가상 네트워크의 개인 주소 공간을 사용하여 Azure 서비스에 액세스할 수 있습니다. 서비스 끝점을 통해 Azure 리소스에 도달하는 트래픽은 항상 Microsoft Azure 백본 네트워크에 유지됩니다. 이 문서에서는 다음 방법을 설명합니다.

* 하나의 서브넷이 있는 가상 네트워크 만들기
* 서브넷을 추가하고 서비스 끝점을 사용하도록 설정
* Azure 리소스를 만들고 서브넷에서만 네트워크 액세스 허용
* 각 서브넷에 VM(가상 머신) 배포
* 서브넷에서 리소스에 대한 액세스 확인
* 서브넷 및 인터넷에서 리소스에 대한 액세스가 거부되는지 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷 구성을 만듭니다. 다음 예제에서는 *Public*이라는 서브넷에 대한 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork)를 통해 가상 네트워크에 서브넷 구성을 기록하여 가상 네트워크에 서브넷을 만듭니다.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>서비스 끝점 사용 

서비스 끝점을 지원하는 서비스에 대해서만 서비스 끝점을 사용하도록 설정할 수 있습니다. [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice)를 통해 Azure 위치에서 사용할 수 있는 서비스 끝점 지원 서비스를 확인합니다. 다음 예제에서는 *eastus* 지역에서 사용할 수 있는 서비스 끝점 지원 서비스 목록을 반환합니다. 반환되는 서비스 목록은 시간이 지나면서 서비스 끝점을 사용할 수 있는 Azure 서비스가 증가함에 따라 확장됩니다.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

가상 네트워크에 추가 서브넷을 만듭니다. 이 예제에서는 *Private*라는 서브넷이 *Microsoft.Storage*에 대한 서비스 끝점으로 생성됩니다. 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>서브넷에 대한 네트워크 액세스 제한

[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 보안 규칙을 만듭니다. 다음 규칙을 사용하면 Azure Storage 서비스에 지정된 공용 IP 주소에 대한 아웃바운드 액세스가 허용됩니다. 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙은 모든 공용 IP 주소에 대한 액세스를 거부합니다. 우선 순위가 더 높은 이전 규칙이 이 규칙을 재정의하여 Azure Storage의 공용 IP 주소에 대한 액세스를 허용합니다.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙을 사용하면 어디에서나 서브넷에 대한 RDP(원격 데스크톱 프로토콜) 트래픽 인바운드가 허용됩니다. 서브넷에 대한 원격 데스크톱 연결이 허용되므로 이후 단계에서 리소스에 대한 네트워크 액세스를 확인할 수 있습니다.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsgPrivate*라는 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)를 사용하여 네트워크 보안 그룹을 *Private* 서브넷에 연결한 다음, 가상 네트워크에 서브넷 구성을 기록합니다. 다음 예제에서는 *myNsgPrivate* 네트워크 보안 그룹을 *Private* 서브넷에 연결합니다.

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>리소스에 대한 네트워크 액세스 제한

서비스 끝점에 사용할 수 있는 Azure 서비스를 통해 만든 리소스에 대한 네트워크 액세스를 제한하는 데 필요한 단계는 서비스에 따라 다릅니다. 각 서비스에 대한 특정 단계는 개별 서비스의 설명서를 참조하세요. 이 문서의 나머지 부분에는 Azure Storage 계정에 대한 네트워크 액세스를 제한하는 단계가 예제로 포함되어 있습니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount)를 사용하여 Azure Storage 계정을 만듭니다. `<replace-with-your-unique-storage-account-name>`을 모든 Azure 위치에서 고유한 이름으로 바꿉니다. 이름은 3~24자 사이여야 하고 숫자와 소문자만 사용할 수 있습니다.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

저장소 계정이 생성된 후 [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey)를 사용하여 저장소 계정의 키를 변수로 검색합니다.

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

이 키는 이후 단계에서 파일 공유를 만드는 데 사용됩니다. `$storageAcctKey`를 입력하고 값을 적어 둡니다. 이후 단계에서 파일 공유를 VM의 드라이브에 매핑할 때도 이 값을 수동으로 입력해야 합니다.

### <a name="create-a-file-share-in-the-storage-account"></a>저장소 계정에 파일 공유 만들기

[New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext)를 사용하여 저장소 계정 및 키에 대한 컨텍스트를 만듭니다. 이 컨텍스트는 저장소 계정 이름 및 계정 키를 캡슐화합니다.

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

[New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare)를 사용하여 파일 공유를 만듭니다.

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>저장소 계정에 대한 모든 네트워크 액세스 거부

기본적으로 저장소 계정은 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)를 사용하여 기본 작업을 *거부*로 변경합니다. 네트워크 액세스가 거부되면 네트워크에서 저장소 계정에 액세스할 수 없습니다.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>서브넷에서 네트워크 액세스 사용

[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)를 사용하여 생성된 가상 네트워크를 검색한 다음, [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig)를 사용하여 비공개 서브넷 개체를 변수로 검색합니다.

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

[Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)을 사용하여 *Private* 서브넷에서 저장소 계정에 대한 네트워크 액세스를 허용합니다.

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

저장소 계정에 대한 네트워크 액세스를 테스트하려면 각 서브넷에 VM을 배포합니다.

### <a name="create-the-first-virtual-machine"></a>첫 번째 가상 머신 만들기

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)를 사용하여 *공용* 서브넷에 가상 머신을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다. `-AsJob` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

다음 예제 출력과 유사한 출력이 반환됩니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>두 번째 가상 머신 만들기

*Private* 서브넷에 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Azure가 VM을 만드는 데 몇 분이 걸립니다. Azure가 VM 만들기를 완료하고 PowerShell에 출력을 반환할 때까지 다음 단계로 이동하지 마세요. 

## <a name="confirm-access-to-storage-account"></a>저장소 계정에 대한 액세스 확인

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVmPrivate* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령에서 `<publicIpAddress>`을 이전 명령에서 반환된 공용 IP 주소로 바꾸고 다음 명령을 입력합니다. 

```powershell
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 만들어지고 컴퓨터에 다운로드됩니다. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다. **확인**을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.

*myVmPrivate* VM에서 PowerShell을 사용하여 Azure 파일 공유를 Z 드라이브에 매핑합니다. 뒤에 나오는 명령을 실행하기 전에, `<storage-account-key>` 및 `<storage-account-name>`을 [저장소 계정 만들기](#create-a-storage-account)에서 지정했거나 검색한 값으로 바꿉니다.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell에서 다음 예제 출력과 비슷한 출력을 반환합니다.

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

The Azure file share successfully mapped to the Z drive(Azure 파일 공유가 Z 드라이브에 매핑되었습니다).

VM에 다른 공용 IP 주소에 대한 아웃바운드 연결이 없는지 확인합니다.

```powershell
ping bing.com
```

*Private* 서브넷과 연결된 네트워크 보안 그룹이 Azure Storage 서비스에 지정된 주소 이외의 공용 IP 주소에 대한 아웃바운드 액세스를 허용하지 않으므로 응답이 수신되지 않습니다.

*myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="confirm-access-is-denied-to-storage-account"></a>저장소 계정에 대한 액세스가 거부되는지 확인

*myVmPublic* VM의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령에서 `<publicIpAddress>`을 이전 명령에서 반환된 공용 IP 주소로 바꾸고 다음 명령을 입력합니다. 

```powershell
mstsc /v:<publicIpAddress>
```

*myVmPublic* VM에서 Azure 파일 공유를 Z 드라이브에 매핑하려고 시도합니다. 뒤에 나오는 명령을 실행하기 전에, `<storage-account-key>` 및 `<storage-account-name>`을 [저장소 계정 만들기](#create-a-storage-account)에서 지정했거나 검색한 값으로 바꿉니다.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

공유에 대한 액세스가 거부되고 `New-PSDrive : Access is denied` 오류가 수신됩니다. *myVmPublic* VM이 *Public* 서브넷에 배포되었으므로 액세스가 거부되었습니다. *Public* 서브넷에는 Azure Storage에 사용할 수 있는 서비스 끝점이 없으며 저장소 계정이 *Public* 서브넷이 아닌 *Private* 서브넷의 네트워크 액세스만 허용합니다.

*myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다.

컴퓨터에서 다음 명령을 사용하여 저장소 계정의 파일 공유를 보려고 시도합니다.

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

액세스가 거부되고 *Get-AzureStorageFile : The remote server returned an error: (403) Forbidden. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation*(Get-AzureStorageFile : 원격 서버에서 (403) 사용할 수 없음 오류를 반환했습니다. HTTP 상태 코드: 403 - HTTP 오류 메시지: 이 요청에는 이 작업을 수행할 수있는 권한이 없습니다.) 오류가 수신됩니다. 사용자 컴퓨터가 *MyVirtualNetwork* 가상 네트워크의 *Private* 서브넷에 없기 때문입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크 서브넷에 대해 서비스 엔드포인트를 사용하도록 설정했습니다. 여러 Azure 서비스로 배포된 리소스에 대해 서비스 끝점을 사용하도록 설정할 수 있음을 배웠습니다. Azure Storage 계정을 만들고 저장소 계정에 대한 네트워크 액세스를 가상 네트워크 서브넷 내의 리소스로만 제한했습니다. 서비스 끝점에 대한 자세한 내용은 [서비스 끝점 개요](virtual-network-service-endpoints-overview.md) 및 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.

계정에 여러 개의 가상 네트워크가 있는 경우 각 가상 네트워크 내의 리소스가 서로 통신할 수 있도록 두 개의 가상 네트워크를 함께 연결하는 것이 좋습니다. 방법을 알아보려면 [가상 네트워크 연결](tutorial-connect-virtual-networks-powershell.md)을 참조하세요.
