---
title: Azure 가상 머신에서 네트워크 인터페이스 추가 또는 제거 | Microsoft Docs
description: 가상 머신에서 네트워크 인터페이스를 추가하거나 제거하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: 6193dcc6ba2e78c55ed6c6f769aea50fcfb3ac40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>가상 머신에 네트워크 인터페이스 추가 또는 제거

Azure VM(가상 머신)을 만들 때 기존 네트워크 인터페이스를 추가하는 방법 또는 중지됨(할당 취소됨) 상태인 기존 VM에서 네트워크 인터페이스를 추가하거나 제거하는 방법을 알아봅니다. 네트워크 인터페이스를 사용하면 Azure 가상 머신이 인터넷, Azure 및 온-프레미스 리소스와 통신할 수 있습니다. 하나의 VM에 하나 이상의 네트워크 인터페이스가 있을 수 있습니다. 

네트워크 인터페이스용 IP 주소를 추가, 변경 또는 제거해야 하는 경우 [네트워크 인터페이스 IP 주소 관리](virtual-network-network-interface-addresses.md)를 참조하세요. 네트워크 인터페이스를 만들기, 변경 또는 삭제해야 하는 경우 [네트워크 인터페이스 관리](virtual-network-network-interface.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 5.2.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.26 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>새 VM에 기존 네트워크 인터페이스 추가

포털을 통해 가상 머신을 만드는 경우 자동으로 포털에서 기본 설정으로 네트워크 인터페이스를 만들어 VM에 연결합니다. Azure Portal을 사용하여 기존 네트워크 인터페이스를 새 VM에 추가하거나 여러 네트워크 인터페이스로 VM을 만들 수는 없습니다. CLI 또는 PowerShell을 사용하면 두 작업을 모두 수행할 수 있지만 [제약 조건](#constraints)을 숙지해야 합니다. 여러 네트워크 인터페이스로 VM을 만드는 경우 VM을 만든 후 올바르게 사용되도록 운영 체제도 구성해야 합니다. 여러 네트워크 인터페이스에 대해 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 또는 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)를 구성하는 방법을 알아보세요.

### <a name="commands"></a>명령

VM을 만들려면 먼저 [네트워크 인터페이스 만들기](virtual-network-network-interface.md#create-a-network-interface)의 단계에 따라 네트워크 인터페이스를 만듭니다.

|도구|명령|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>기존 VM에 네트워크 인터페이스 추가

1. Azure 포털에 로그인합니다.
2. 포털 맨 위에 있는 검색 상자에서 네트워크 인터페이스를 추가할 VM의 이름을 입력하거나 **모든 서비스**, **가상 머신**을 차례로 선택하여 VM을 찾습니다. VM을 찾은 후 선택합니다. 추가하려는 네트워크 인터페이스 수를 VM이 지원해야 합니다. 각 VM 크기가 지원하는 네트워크 인터페이스 수를 확인하려면 [Azure에서 Linux 가상 머신에 대한 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Azure에서 Windows 가상 머신에 대한 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.  
3. **설정**에서 **개요**를 선택합니다. **중지**를 선택하고 VM의 **상태**가 **중지됨(할당 취소됨)**으로 바뀔 때까지 기다립니다. 
4. **설정**에서 **네트워킹**을 선택합니다.
5. **네트워크 인터페이스 연결**을 선택합니다. 현재 다른 VM에 연결되지 않은 네트워크 인터페이스 목록에서 연결할 네트워크 인터페이스를 선택합니다. 

    >[!NOTE]
    선택한 네트워크 인터페이스는 가속 네트워킹을 사용할 수 없고, IPv6 주소를 할당할 수 없으며, 현재 VM에 연결된 네트워크 인터페이스를 포함하는 가상 네트워크와 동일한 가상 네트워크에 있어야 합니다. 

    기존 네트워크 인터페이스가 없는 경우 하나 만들어야 합니다. 이렇게 하려면 **네트워크 인터페이스 만들기**를 선택합니다. 네트워크 인터페이스를 만드는 방법에 대한 자세한 내용은 [네트워크 인터페이스 만들기](virtual-network-network-interface.md#create-a-network-interface)를 참조하세요. 네트워크 인터페이스를 가상 머신에 추가할 때 적용되는 제약 조건에 대한 자세한 내용은 [제약 조건](#constraints)을 참조하세요.

6. **확인**을 선택합니다.
7. **설정**에서 **개요**를 선택한 다음, **시작**을 선택하여 가상 머신을 시작합니다.
8. 여러 네트워크 인터페이스를 올바르게 사용하도록 VM 운영 체제를 구성합니다. 여러 네트워크 인터페이스에 대해 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 또는 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)를 구성하는 방법을 알아보세요.

|도구|명령|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add)(참조) 또는 [세부 단계](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)(참조) 또는 [세부 단계](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>VM의 네트워크 인터페이스 보기

VM에 현재 연결된 네트워크 인터페이스를 보고 각 네트워크 인터페이스의 구성 및 각 네트워크 인터페이스에 할당된 IP 주소를 확인할 수 있습니다. 

1. 구독에 대한 소유자, 참가자 또는 네트워크 참가자 역할이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. Azure Portal 위쪽의 **리소스 검색** 텍스트가 있는 상자에서 **가상 머신**을 입력합니다. 검색 결과에 표시된 **가상 머신**를 선택합니다.
3. 네트워크 인터페이스를 보려는 VM의 이름을 선택합니다.
4. 선택한 VM의 **설정** 섹션에서 **네트워킹**을 선택합니다. 네트워크 인터페이스 설정 및 변경 방법에 대한 자세한 내용은 [네트워크 인터페이스 관리](virtual-network-network-interface.md)를 참조하세요. 네트워크 인터페이스에 할당된 IP 주소를 추가, 변경 또는 제거하는 방법을 알아보려면 [네트워크 인터페이스 IP 주소 관리](virtual-network-network-interface-addresses.md)를 참조하세요.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>VM에서 네트워크 인터페이스 제거

1. Azure 포털에 로그인합니다.
2. 포털 맨 위에 있는 검색 상자에서 네트워크 인터페이스를 제거(분리)할 VM의 이름을 검색하거나 **모든 서비스**, **가상 머신**을 차례로 선택하여 VM을 찾습니다. VM을 찾은 후 선택합니다.
3. **설정**에서 **개요**를 선택한 다음, **중지**를 선택합니다. VM의 **상태**가 **중지됨(할당 취소됨)**으로 바뀔 때까지 기다립니다. 
4. **설정**에서 **네트워킹**을 선택합니다.
5. **네트워크 인터페이스 분리**를 선택합니다. 현재 가상 머신에 연결된 네트워크 인터페이스 목록에서 분리할 네트워크 인터페이스를 선택합니다. 

    >[!NOTE]
    가상 머신에 항상 하나 이상의 네트워크 인터페이스가 연결되어 있어야 하므로 네트워크 인터페이스가 하나만 표시되는 경우 네트워크 인터페이스를 분리할 수 없습니다.
6. **확인**을 선택합니다.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove)(참조) 또는 [세부 단계](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)(참조) 또는 [세부 단계](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>제약 조건

- VM에는 하나 이상의 네트워크 인터페이스가 연결되어 있어야 합니다.
- VM은 VM 크기에서 지원하는 수만큼 네트워크 인터페이스를 연결할 수 있습니다. 각 VM 크기가 지원하는 네트워크 인터페이스 수에 대한 자세한 내용은 [Azure에서 Linux 가상 머신에 대한 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Azure에서 Windows 가상 머신에 대한 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 모든 크기는 적어도 두 개의 네트워크 인터페이스를 지원합니다.
- VM에 추가한 네트워크 인터페이스는 현재 다른 VM에 연결할 수 없습니다. 네트워크 인터페이스를 만드는 방법에 대한 자세한 내용은 [네트워크 인터페이스 만들기](virtual-network-network-interface.md#create-a-network-interface)를 참조하세요.
- 이전에는 여러 네트워크 인터페이스를 지원하고 두 개 이상의 네트워크 인터페이스를 사용하여 생성된 VM에만 네트워크 인터페이스를 추가할 수 있었습니다. VM 크기가 여러 네트워크 인터페이스를 지원하는 경우에도 하나의 네트워크 인터페이스를 사용하여 만들어진 VM에 네트워크 인터페이스를 추가하지 못했습니다. 반대로 두 개 이상의 네트워크 인터페이스로 만든 VM에는 항상 두 개 이상의 네트워크 인터페이스가 있어야 하므로 세 개 이상의 네트워크 인터페이스가 있는 VM에서만 네트워크 인터페이스를 제거할 수 있었습니다. 이러한 제약 조건은 더 이상 적용되지 않습니다. 이제 (VM 크기에서 지원하는 수 이내에서) 원하는 수의 네트워크 인터페이스가 있는 VM을 만들 수 있습니다.
- 기본적으로 VM에 연결된 첫 번째 네트워크 인터페이스는 *기본* 네트워크 인터페이스로 정의됩니다. VM의 다른 모든 네트워크 인터페이스는 *보조* 네트워크 인터페이스입니다.
- 아웃바운드 트래픽을 보낼 네트워크 인터페이스를 제어할 수 있지만, 기본적으로 VM의 모든 아웃바운드 트래픽은 기본 네트워크 인터페이스의 기본 IP 구성에 할당된 IP 주소로 보내집니다.
- 과거에는 동일한 가용성 집합 내의 모든 VM에 단일 또는 다중 네트워크 인터페이스가 있어야 했습니다. 여러 개의 네트워크 인터페이스가 있는 VM은 이제 VM 크기에서 지원하는 수의 동일한 가용성 집합에 있을 수 있습니다. VM을 만들 때 가용성 집합에만 VM을 추가할 수 있습니다. 가용성 집합에 대한 자세한 내용은 [Azure에서 VM의 가용성 관리](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)를 참조하세요.
- 동일한 VM에 있는 네트워크 인터페이스를 가상 네트워크 내의 여러 다른 서브넷에 연결할 수 있지만, 해당 네트워크 인터페이스가 모두 동일한 가상 네트워크에 연결되어야 합니다.
- 기본 또는 보조 네트워크 인터페이스의 IP 구성에 대한 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 네트워크 인터페이스의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. IP 주소 및 구성에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)를 참조하세요.
- VM을 삭제해도 연결된 네트워크 인터페이스는 삭제되지 않습니다. VM을 삭제하면 VM에서 네트워크 인터페이스가 분리됩니다. 네트워크 인터페이스를 다른 VM에 추가하거나 삭제할 수 있습니다.
- 네트워크 인터페이스에 개인 IPv6 주소가 할당된 경우 VM을 만들 때 네트워크 인터페이스를 해당 VM에 추가(연결)해야 합니다. VM을 만든 후 할당된 IPv6 주소가 있는 네트워크 인터페이스를 해당 VM에 추가할 수는 없습니다. 가상 머신을 만들 때 할당된 개인 IPv6 주소가 있는 네트워크 인터페이스를 추가하는 경우 VM 크기가 지원하는 네트워크 인터페이스 수에 관계없이 해당 네트워크 인터페이스만 가상 머신에 추가할 수 있습니다. 네트워크 인터페이스에 IP 주소를 할당하는 방법에 대한 자세한 내용은 [네트워크 인터페이스 IP 주소 관리](virtual-network-network-interface-addresses.md)를 참조하세요.
- IPv6과 마찬가지로, VM을 만든 후 가속 네트워킹이 사용하도록 설정된 네트워크 인터페이스를 해당 VM에 연결할 수는 없습니다. 또한 가속 네트워킹을 이용하려면 VM 운영 체제 내에서 단계를 완료해야 합니다. 가속 네트워킹과 [Windows](create-vm-accelerated-networking-powershell.md) 또는 [Linux](create-vm-accelerated-networking-cli.md) 가상 머신에 가속 네트워킹을 사용하는 경우의 기타 제약 조건에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
여러 네트워크 인터페이스 또는 IP 주소가 있는 VM을 만들려면 다음 문서를 참조하세요.

### <a name="commands"></a>명령

|Task|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IPv4 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Azure Load Balancer 뒤에 개인 IPv6 주소가 있는 단일 NIC VM 만들기|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager 템플릿](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


