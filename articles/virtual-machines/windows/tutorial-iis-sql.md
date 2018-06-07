---
title: 자습서 - Azure에서 SQL&#47;IIS&#47;.NET 스택을 실행하는 VM 만들기| Microsoft Docs
description: 이 자습서에서는 Azure에서 Windows 가상 머신에 Azure SQL, IIS, .NET 스택을 설치하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f9fea933dd664955a0bc6f47db775fbc469fd684
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows VM에 SQL&#47;IIS&#47;.NET 스택 설치

이 자습서에서는 Azure PowerShell을 사용하여 SQL&#47;IIS&#47;.NET 스택을 설치합니다. 스택은 Windows Server 2016을 실행하는 두 개의 VM으로 구성되며, 하나는 IIS와 .NET과 실행하고 다른 하나는 SQL Server와 실행합니다.

> [!div class="checklist"]
> * VM 만들기 
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 AzureRM.Compute 모듈 버전 5.7.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM.Compute`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="create-a-iis-vm"></a>IIS VM 만들기 

이 예제에서는 PowerShell Cloud Shell에서 [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet을 사용하여 Windows Server 2016 VM을 빠르게 만든 다음, IIS와 .NET Framework를 설치합니다. IIS 및 SQL VM은 리소스 그룹 및 가상 네트워크를 공유하므로 그러한 이름의 변수를 만듭니다.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

사용자 지정 스크립트 확장을 사용하여 IIS 및 .NET Framework를 설치합니다.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>다른 서브넷 만들기

SQL VM에 대한 두 번째 서브넷을 만듭니다. [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}를 사용하여 vNet을 가져옵니다.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷의 구성을 만듭니다.


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)를 사용하여 새 서브넷 구성으로 vNet을 업데이트합니다.
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL VM

미리 구성된 Azure 마켓플레이스의 SQL 서버 이미지를 사용하여 SQL VM을 만듭니다. 먼저 VM을 만든 다음 VM에 SQL Server 확장을 설치합니다. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

[Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension)을 사용하여 [SQL Server 확장](/sql/virtual-machines-windows-sql-server-agent-extension.md)을 SQL VM에 추가합니다.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure PowerShell을 사용하여 SQL&#92;IIS&#92;.NET 스택을 설치했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

SSL 인증서로 IIS 웹 서버를 보호하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [SSL 인증서로 IIS 웹 서버 보호](tutorial-secure-web-server.md)

