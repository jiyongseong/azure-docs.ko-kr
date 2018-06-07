---
title: Azure에서 여러 NIC를 사용하는 Windows VM 만들기 및 관리 | Microsoft Docs
description: Azure PowerShell 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 Windows VM을 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 776ae83990a7799102c69347196a72a68561ee6b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>여러 NIC가 있는 Windows 가상 컴퓨터 만들기 및 관리
Azure의 VM(가상 머신)에는 여러 가상 NIC(네트워크 인터페이스 카드)가 연결될 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결된 VM을 만드는 방법을 설명합니다. 또한 기존 VM에서 NIC를 추가하거나 제거하는 방법을 알아봅니다. [VM 크기](sizes.md) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

## <a name="prerequisites"></a>필수 조건
먼저 [최신 버전의 Azure PowerShell을 설치 및 구성](/powershell/azure/overview)했는지 확인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myVnet*, *myVM*이 포함됩니다.


## <a name="create-a-vm-with-multiple-nics"></a>여러 NIC를 사용하여 VM 만들기
먼저 리소스 그룹을 만듭니다. 다음 예제에서는 *EastUs* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>가상 네트워크 및 서브넷 만들기
일반적인 시나리오는 가상 네트워크에 두 개 이상의 서브넷이 있는 것입니다. 하나의 서브넷은 프런트 엔드 트래픽용이고, 다른 서브넷은 백 엔드 트래픽용일 수 있습니다. 두 서브넷 모두에 연결하려면 VM에서 여러 NIC를 사용합니다.

1. [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 두 개의 가상 네트워크 서브넷을 정의합니다. 다음 예제에서는 *mySubnetFrontEnd* 및 *mySubnetBackEnd*에 대한 서브넷을 정의합니다.

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크 및 서브넷을 만듭니다. 다음 예제에서는 *myVnet*이라는 가상 네트워크를 만듭니다.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>여러 NIC 만들기
[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 두 개의 NIC를 만듭니다. 하나의 NIC를 프런트 엔드 서브넷에, 다른 NIC를 백 엔드 서브넷에 연결합니다. 다음 예제에서는 *myNIC1* 및 *myNIC2*라는 NIC를 만듭니다.

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

또한 일반적으로 VM에 대한 네트워크 트래픽을 필터링하기 위해 [네트워크 보안 그룹](../../virtual-network/security-overview.md)을 만들고, 여러 VM 간에 트래픽을 분산하기 위해 [부하 분산 장치](../../load-balancer/load-balancer-overview.md)를 만듭니다.

### <a name="create-the-virtual-machine"></a>가상 머신 만들기
이제 VM 구성 빌드를 시작합니다. VM 크기마다 VM에 추가할 수 있는 NIC의 총수가 제한되어 있습니다. 자세한 내용은 [Windows VM 크기](sizes.md)를 참조하세요.

1. 다음과 같이 `$cred` 변수에 VM 자격 증명을 설정합니다.

    ```powershell
    $cred = Get-Credential
    ```

2. [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)를 사용하여 VM을 정의합니다. 다음 예제에서는 *myVM*이라는 VM을 정의하고 2개 이상의 NIC를 지원하는 VM 크기(*Standard_DS3_v2*)를 사용합니다.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 및 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)를 사용하여 나머지 VM 구성을 만듭니다. 다음 예제에서는 Windows Server 2016 VM을 만듭니다.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface)를 사용하여 이전에 만든 두 NIC를 추가합니다.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. [여러 NIC에 대한 운영 체제 구성](#configure-guest-os-for-multiple-nics)의 단계를 완료하여 OS에 보조 NIC에 대한 경로를 추가합니다.

## <a name="add-a-nic-to-an-existing-vm"></a>기존 VM에 NIC 추가
기존 VM에 가상 NIC를 추가하고 VM을 할당 취소하고 가상 NIC를 추가한 다음 VM을 시작합니다. [VM 크기](sizes.md) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다. 필요한 경우 [VM의 크기를 조정](resize-vm.md)할 수 있습니다.

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm)을 사용하여 VM의 기존 구성을 가져옵니다. 다음 예제에서는 *myResourceGroup*에서 *myVM*이라는 VM에 대한 정보를 가져옵니다.

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. 다음 예제에서는 *mySubnetBackEnd*에 연결된 *myNIC3*이라는 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 가상 NIC를 만듭니다. 그런 다음 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface)를 사용하여 *myResourceGroup*에서 *myVM*이라는 VM에 가상 NIC를 연결합니다.

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>기본 가상 NIC
    다중 NIC VM의 NIC 중 하나는 기본 NIC여야 합니다. VM의 기존 가상 NIC 중 하나가 이미 기본 NIC로 설정되어 있는 경우 이 단계를 건너뛸 수 있습니다. 다음 예제에서는 VM에 두 개의 가상 NIC가 있고 첫 번째 NIC(`[0]`)를 기본 NIC로 추가하려고 하는 것으로 가정합니다.
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm)을 사용하여 VM을 시작합니다.

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. [여러 NIC에 대한 운영 체제 구성](#configure-guest-os-for-multiple-nics)의 단계를 완료하여 OS에 보조 NIC에 대한 경로를 추가합니다.

## <a name="remove-a-nic-from-an-existing-vm"></a>기존 VM에서 NIC 제거
기존 VM에서 가상 NIC를 제거하고 VM을 할당 취소하고 가상 NIC를 제거한 다음 VM을 시작합니다.

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 VM을 할당 취소합니다. 다음 예제에서는 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm)을 사용하여 VM의 기존 구성을 가져옵니다. 다음 예제에서는 *myResourceGroup*에서 *myVM*이라는 VM에 대한 정보를 가져옵니다.

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface)를 사용하여 NIC에 대한 정보를 가져옵니다. 다음 예제에서는 *myNic3*에 대한 정보를 가져옵니다.

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface)를 사용하여 NIC를 제거하고 [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm)을 사용하여 VM을 업데이트합니다. 다음 예제에서는 이전 단계에서 `$nicId`를 사용하여 가져온 *myNic3*을 제거합니다.

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm)을 사용하여 VM을 시작합니다.

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요. *복사* 를 사용하여 만들 인스턴스 수를 지정할 수 있습니다.

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

자세한 내용은 [*copy*를 사용하여 여러 인스턴스 만들기](../../resource-group-create-multiple.md)를 참조하세요. 

`copyIndex()`를 사용하여 리소스 이름에 숫자를 추가할 수도 있습니다. 그런 다음 *myNic1*, *MyNic2* 등을 만들 수 있습니다. 다음 코드는 인덱스 값을 추가하는 예를 보여 줍니다.

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../../virtual-network/template-samples.md)의 전체 예제를 읽어볼 수 있습니다.

[여러 NIC에 대한 운영 체제 구성](#configure-guest-os-for-multiple-nics)의 단계를 완료하여 OS에 보조 NIC에 대한 경로를 추가합니다.

## <a name="configure-guest-os-for-multiple-nics"></a>여러 NIC에 대한 게스트 OS 구성

Azure에서는 가상 머신에 연결된 첫 번째(기본) 네트워크 인터페이스에 기본 게이트웨이를 할당합니다. 가상 머신에 연결된 추가(보조) 네트워크 인터페이스에는 기본 게이트웨이를 할당하지 않습니다. 따라서 보조 네트워크 인터페이스가 있는 서브넷 외부의 리소스와는 기본적으로 통신할 수 없습니다. 하지만 보조 네트워크 인터페이스는 서브넷 외부의 리소스와 통신할 수 있습니다. 단, 통신을 사용하도록 설정하는 단계는 운영 체제에 따라 다릅니다.

1. Windows 명령 프롬프트에서 `route print` 명령을 실행합니다. 여기서는 연결된 네트워크 인터페이스가 두 개인 가상 머신에 대해 다음과 유사한 출력이 반환됩니다.

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    이 예제에서는 **Microsoft Hyper-V Network Adapter #4**(인터페이스 7)가 기본 게이트웨이가 할당되지 않은 보조 네트워크 인터페이스입니다.

2. 명령 프롬프트에서 `ipconfig` 명령을 실행하여 보조 네트워크 인터페이스에 할당된 IP 주소를 확인합니다. 이 예제에서는 192.168.2.4가 인터페이스 7에 할당되어 있습니다. 보조 네트워크 인터페이스에 대해서는 기본 게이트웨이 주소가 반환되지 않습니다.

3. 보조 네트워크 인터페이스의 서브넷 외부의 주소를 대상으로 하는 모든 트래픽을 서브넷의 게이트웨이로 라우팅하려면 다음 명령을 실행합니다.

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    서브넷의 게이트웨이 주소는 서브넷에 대해 정의된 주소 범위에서 첫 번째 IP 주소(.1로 끝남)입니다. 모든 트래픽을 서브넷 외부로 라우팅하지 않으려면 대신 특정 대상에 개별 경로를 추가할 수 있습니다. 예를 들어, 보조 네트워크 인터페이스에서 트래픽을 192.168.3.0 네트워크로만 라우팅하려는 경우 다음 명령을 입력합니다.

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. 예를 들어 192.168.3.0 네트워크의 리소스와 통신이 성공했는지 확인하려면 다음 명령을 입력해 인터페이스 7(192.168.2.4)을 사용하여 192.168.3.4를 ping합니다.

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    다음 명령을 사용하여 ping하는 장치의 Windows 방화벽을 통해 ICMP를 열어야 할 수도 있습니다.
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. 추가된 경로가 경로 테이블에 있는지 확인하려면 `route print` 명령을 입력합니다. 다음 텍스트와 유사한 출력이 반환됩니다.

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    **게이트웨이** 아래에 *192.168.1.1*로 나열된 경로는 기본적으로 기본 네트워크 인터페이스용 경로입니다. **게이트웨이** 아래에서 *192.168.2.1*인 경로가 추가한 경로입니다.

## <a name="next-steps"></a>다음 단계
여러 NIC가 있는 VM을 만들 때 [Windows VM 크기](sizes.md)를 검토합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다. 


