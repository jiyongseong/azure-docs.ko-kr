---
title: Azure PowerShell을 사용하여 SQL Server VM 프로비전 가이드 | Microsoft Docs
description: SQL Server 가상 머신 갤러리 이미지를 사용하여 Azure VM을 만드는 단계 및 PowerShell 명령을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 2f0d9c42e32f2dd1181eac8d74c324b5ff2b0c53
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Azure PowerShell을 사용하여 SQL Server 가상 머신을 프로비전하는 방법

이 가이드에서는 Azure PowerShell을 사용하여 Windows SQL Server VM을 만드는 옵션에 대해 설명합니다. 기본값이 더 많은 간소화된 Azure PowerShell 예제는 [SQL VM Azure PowerShell 빠른 시작](quickstart-sql-vm-create-powershell.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 문서에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="configure-your-subscription"></a>구독 구성

1. PowerShell을 열고 **Connect-AzureRmAccount** 명령을 실행하여 Azure 계정에 대한 액세스를 설정합니다.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. 자격 증명을 입력할 수 있는 로그인 화면이 표시됩니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

## <a name="define-image-variables"></a>이미지 변수 정의
재사용 및 스크립트 작성을 간소화하려면 먼저 여러 가지 변수를 정의합니다. 적절하다고 판단되면 매개 변수 값을 변경하지만, 제공된 값을 수정할 때 이름 길이 및 특수 문자와 관련된 명명 제한 사항에 주의하세요.

### <a name="location-and-resource-group"></a>위치 및 리소스 그룹
두 변수를 사용하여 가상 머신에 대한 다른 리소스를 만들 리소스 그룹 및 데이터 영역을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>저장소 속성
다음 변수를 사용하여 가상 머신에서 사용할 저장소 계정 및 저장소 유형을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다. 이 예제에서는 프로덕션 워크로드에 권장되는 [Premium Storage](../premium-storage.md)를 사용합니다.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>네트워크 속성
다음 변수를 사용하여 가상 머신의 네트워크에서 사용할 네트워크 인터페이스, TCP/IP 할당 방법, 가상 네트워크 이름, 가상 서브넷 이름, 가상 네트워크용 IP 주소 범위, 서브넷용 IP 주소 범위 및 공용 도메인 이름 레이블을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>가상 머신 속성
다음 변수를 사용하여 가상 머신에 대한 가상 머신 이름, 컴퓨터 이름, 가상 머신 크기 및 운영 체제 디스크 이름을 정의합니다.

원하는 대로 수정하고 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server 이미지 선택
다음 변수를 사용하여 가상 머신에 사용할 SQL Server 이미지를 정의합니다.

1. 먼저 **Get-AzureRmVMImageOffer** 명령을 사용하여 모든 SQL Server 이미지 제품을 나열합니다.

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. 이 자습서에서는 다음 변수를 사용하여 Windows Server 2016에서 SQL Server 2017을 지정합니다.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 다음으로, 사용 가능한 제품의 버전을 나열합니다.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. 이 자습서에서는 SQL Server 2017 Developer Edition(**SQLDEV**)을 사용합니다. Developer Edition은 테스트 및 개발을 위해 무료로 사용이 허가되며 VM을 실행하는 요금만 지불하면 됩니다.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 관리자 배포 모델을 사용하여 만드는 첫 번째 개체는 리소스 그룹입니다. [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 리소스 그룹 이름 및 위치로 Azure 리소스 그룹 및 해당 리소스를 만듭니다.

다음 cmdlet을 실행하여 새 리소스 그룹을 만듭니다.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기
가상 머신에 운영 체제 디스크와 SQL Server 데이터 및 로그 파일에 대한 저장소 리소스가 필요합니다. 간단히 하기 위해 둘 다에 대한 단일 디스크를 만듭니다. SQL Server 데이터와 로그 파일을 전용 디스크에 배치하기 위해 [Add-Azure Disk](/powershell/module/azure/add-azuredisk) cmdlet을 사용하여 나중에 추가 디스크를 연결할 수 있습니다. [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet을 사용하여 이전에 초기화한 변수로 정의된 저장소 계정 이름, 저장소 SKU 이름 및 위치로 새 리소스 그룹에서 표준 저장소 계정을 만듭니다.

다음 cmdlet을 실행하여 새 저장소 계정을 만듭니다.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> 저장소 계정을 만들려면 몇 분 정도 걸릴 수 있습니다.

## <a name="create-network-resources"></a>네트워크 리소스 만들기
가상 머신에 네트워크 연결을 위해 여러 네트워크 리소스가 필요합니다.

* 각 가상 머신에 가상 네트워크가 필요합니다.
* 가상 네트워크에는 하나 이상의 서브넷이 정의되어 있어야 합니다.
* 네트워크 인터페이스가 공용 또는 개인 IP 주소 중 하나로 정의되어야 합니다.

### <a name="create-a-virtual-network-subnet-configuration"></a>가상 네트워크 서브넷 구성 만들기
먼저 가상 네트워크에 대한 서브넷 구성을 만듭니다. 이 자습서에서는 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet을 사용하여 기본 서브넷을 만듭니다. 이전에 초기화한 변수를 사용하여 정의된 서브넷 이름 및 주소 접두사로 가상 네트워크 서브넷 구성을 만듭니다.

> [!NOTE]
> 이 cmdlet을 사용하여 가상 네트워크 서브넷 구성의 추가 속성을 정의할 수 있지만, 이 내용은 이 자습서에서 다루지 않습니다.

다음 cmdlet을 실행하여 가상 서브넷 구성을 만듭니다.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
다음으로, [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet을 사용하여 가상 네트워크를 만듭니다. 이전 단계에서 정의한 서브넷 구성을 사용하고 이전에 초기화한 변수를 사용하여 정의한 이름, 위치 및 주소 접두사로 새 리소스 그룹에서 가상 네트워크를 만듭니다.

다음 cmdlet을 실행하여 가상 네트워크를 만듭니다.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기
이제 가상 네트워크를 정의했으므로 가상 컴퓨터에 연결하려면 IP 주소를 구성해야 합니다. 이 자습서에서는 인터넷 연결을 지원하도록 동적 IP 주소 지정을 사용하여 공용 IP 주소를 만듭니다. [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet을 사용하여 이전에 초기화한 변수로 정의한 이름, 위치, 할당 방법 및 DNS 도메인 이름 레이블로 이전에 만든 리소스 그룹에서 공용 IP 주소를 만듭니다.

> [!NOTE]
> 이 cmdlet을 사용하여 공용 IP 주소의 추가 속성을 정의할 수 있지만, 이 내용은 이 초기 자습서에서 다루지 않습니다. 또한 고정 주소로 개인 주소 또는 주소도 만들 수 있지만, 이 내용도 이 자습서에서 다루지 않습니다.

다음 cmdlet을 실행하여 공용 IP 주소를 만듭니다.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>네트워크 보안 그룹 만들기
VM 및 SQL Server 트래픽을 보호하려면 네트워크 보안 그룹을 만듭니다.

1. 먼저, 원격 데스크톱 연결을 허용하는 RDP의 네트워크 보안 그룹 규칙을 만듭니다.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. TCP 포트 1433에서 트래픽을 허용하는 네트워크 보안 그룹 규칙을 구성합니다. 이렇게 하면 인터넷을 통해 SQL Server에 연결할 수 있습니다.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. 그런 다음 네트워크 보안 그룹을 만듭니다.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기
이제 가상 머신에서 사용할 네트워크 인터페이스를 만들 수 있습니다. [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet을 호출하여 이전에 정의한 이름, 위치, 서브넷 및 공용 IP 주소로 이전에 만든 리소스 그룹에서 네트워크 인터페이스를 만듭니다.

다음 cmdlet을 실행하여 네트워크 인터페이스를 만듭니다.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>VM 개체 구성
이제 저장소 및 네트워크 리소스를 정의했으므로 가상 머신에 대한 계산 리소스를 정의할 수 있습니다. 이 자습서에서는 가상 머신 크기 및 다양한 운영 체제 속성을 지정하고, 이전에 만든 네트워크 인터페이스를 지정하고, Blob 저장소를 정의한 다음 운영 체제 디스크를 지정합니다.

### <a name="create-the-vm-object"></a>VM 개체 만들기
먼저 가상 머신 크기를 지정합니다. 이 자습서의 경우 DS13을 지정합니다. [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) cmdlet을 사용하여 이전에 초기화한 변수로 정의한 이름 및 크기로 구성 가능한 가상 머신 개체를 만듭니다.

다음 cmdlet을 실행하여 가상 머신 개체를 만듭니다.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>로컬 관리자 자격 증명에 대한 이름 및 암호를 저장하도록 자격 증명 개체 만들기
가상 머신에 대한 운영 체제 속성을 설정하려면 먼저 보안 문자열로 로컬 관리자 계정의 자격 증명을 제공해야 합니다. 이렇게 하려면 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet을 사용합니다.

다음 cmdlet을 실행하고 PowerShell 자격 증명 요청 창에서 가상 머신의 로컬 관리자 계정에 사용할 이름 및 암호를 입력합니다.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>가상 컴퓨터에 대한 운영 체제 속성 설정
이제 가상 머신의 운영 체제 속성을 설정할 수 있습니다. [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet을 사용하여 운영 체제 유형을 Windows로 설정하고, [가상 머신 에이전트](../../extensions/agent-windows.md)를 설치하도록 요구하고, cmdlet이 자동 업데이트를 사용하도록 지정하고, 이전에 초기화한 변수로 가상 머신 이름, 컴퓨터 이름 및 자격 증명을 설정합니다.

다음 cmdlet을 실행하여 가상 머신에 대한 운영 체제 속성을 설정합니다.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>가상 머신에 네트워크 인터페이스를 추가합니다.
다음으로, 이전에 만든 네트워크 인터페이스를 가상 머신에 추가합니다. [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet을 호출하여 이전에 정의한 네트워크 인터페이스 변수를 사용하는 네트워크 인터페이스를 추가합니다.

다음 cmdlet을 실행하여 가상 컴퓨터에 대한 네트워크 인터페이스를 설정합니다.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>가상 머신에서 사용할 디스크에 대한 Blob 저장소 위치를 설정합니다.
다음으로, 이전에 정의한 변수를 사용하여 가상 머신에서 사용할 디스크에 대한 Blob 저장소 위치를 설정합니다.

다음 cmdlet을 실행하여 Blob 저장소 위치를 설정합니다.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>가상 머신에 대한 운영 체제 디스크 속성을 설정합니다.
그런 다음 가상 머신의 운영 체제 디스크 속성을 설정합니다. [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet을 사용하여 가상 머신의 운영 체제를 이미지에서 구성하도록 지정하고 캐싱을 SQL Server가 같은 디스크에 설치되는 중이므로 읽기 전용으로 설정하며 이전에 정의한 변수로 정의된 가상 머신 이름 및 운영 체제 디스크를 정의합니다.

다음 cmdlet을 실행하여 가상 머신에 대한 운영 체제 디스크 속성을 설정합니다.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>가상 머신에 대한 플랫폼 이미지 지정
마지막 구성 단계는 가상 머신에 대한 플랫폼 이미지를 지정하는 것입니다. 이 자습서의 경우 최신 SQL Server 2016 CTP 이미지를 사용합니다. [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet을 사용하여 이전에 정의한 변수에 정의된 대로 이 이미지를 사용합니다.

다음 cmdlet을 실행하여 가상 머신에 대한 플랫폼 이미지를 지정합니다.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>SQL VM 만들기
이제 구성 단계를 완료했으므로 가상 머신을 만들 수 있습니다. [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet을 사용하여 정의한 변수로 가상 머신을 만듭니다.

다음 cmdlet을 실행하여 가상 머신을 만듭니다.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

가상 머신이 만들어집니다.

> [!NOTE]
> 부트 진단에 대한 오류는 무시할 수 있습니다. 가상 머신의 디스크에 대해 지정된 저장소 계정은 프리미엄 저장소 계정이므로 부팅 진단에 대해서는 표준 저장소 계정이 만들어집니다.

## <a name="install-the-sql-iaas-agent"></a>SQL Iaas 에이전트 설치
SQL Server 가상 머신은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 사용하여 자동화된 관리 기능을 지원합니다. 새 VM에 에이전트를 설치하려면 VM을 만든 후 다음 명령을 실행하세요.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>테스트 VM 제거

VM을 계속해서 실행하지 않아도 되는 경우 사용 중이 아닌 VM을 중지하여 불필요한 요금을 방지할 수 있습니다. 다음 명령은 VM을 중지하지만 나중에 계속 사용할 수 있습니다.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

**Remove-AzureRmResourceGroup** 명령을 사용하여 가상 머신과 관련된 모든 리소스를 영구적으로 삭제할 수도 있습니다. 그러면 가상 머신도 영구적으로 삭제되므로 이 명령을 사용할 때는 주의해야 합니다.

## <a name="example-script"></a>예제 스크립트
다음 스크립트에는 이 자습서에 대한 전체 PowerShell 스크립트가 포함됩니다. **Connect-AzureRmAccount** 및 **Select-AzureRmSubscription** 명령을 사용하도록 이미 Azure 구독을 설정한 것으로 가정합니다.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>다음 단계
가상 머신을 만든 후에는 다음을 수행할 수 있습니다.

- 원격 데스크톱(RDP)을 사용하여 가상 머신에 연결합니다.
- 다음을 포함하여 VM에 대한 SQL Server 설정을 포털에서 구성합니다.
   - [저장소 설정](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [자동화된 관리 작업](virtual-machines-windows-sql-server-agent-extension.md)
- [연결 구성](virtual-machines-windows-sql-connect.md).
- 새 SQL Server 인스턴스에 클라이언트 및 응용 프로그램을 연결합니다.

