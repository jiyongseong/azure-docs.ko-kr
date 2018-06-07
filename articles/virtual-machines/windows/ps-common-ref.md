---
title: Azure Virtual Machines에 대한 공통 PowerShell 명령 | Microsoft Docs
description: Azure에서 Windows VM 만들기 및 관리를 시작하기 위한 공통 PowerShell 명령
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 313a313764f8ba14c9661429d1f6a8463778c934
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Virtual Machines를 만들고 관리하기 위한 공통 PowerShell 명령

이 문서에서는 Azure 구독에서 가상 머신을 만들고 관리하는 데 사용할 수 있는 몇 가지 Azure PowerShell 명령을 다룹니다.  특정 명령줄 스위치 및 옵션에 대해 자세한 도움이 필요할 경우 **Get-Help** *명령*을 사용할 수 있습니다.

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

이 문서에 나오는 둘 이상의 명령을 실행하는 경우 다음과 같은 변수가 유용할 수 있습니다.

- $location - 가상 머신의 위치입니다. [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation)을 사용하여 사용자의 [지리적 하위 지역](https://azure.microsoft.com/regions/)을 찾을 수 있습니다.
- $myResourceGroup - 가상 컴퓨터를 포함하는 리소스 그룹의 이름입니다.
- $myVM - 가상 컴퓨터의 이름입니다.

## <a name="create-a-vm"></a>VM 만들기

| Task | 명령 |
| ---- | ------- |
| VM 구성 만들기 |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 구성은 VM에 대한 설정을 정의하거나 업데이트하는 데 사용 됩니다. 구성은 VM의 이름 및 [크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 초기화합니다. |
| 구성 설정 추가 |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[자격 증명](https://technet.microsoft.com/library/hh849815.aspx)을 포함하는 운영 체제 설정은 New-AzureRmVMConfig를 사용하여 이전에 만든 구성 개체에 추가됩니다. |
| 네트워크 인터페이스 추가 |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM은 가상 네트워크에서 통신하도록 [네트워크 인터페이스](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)가 있어야 합니다. 또한 기존 네트워크 인터페이스 개체를 검색하는 데 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 를 사용할 수도 있습니다. |
| 플랫폼 이미지 지정 |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[이미지 정보](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)는 New-AzureRmVMConfig를 사용하여 이전에 만든 구성 개체에 추가됩니다. 이 명령에서 반환되는 개체는 플랫폼 이미지를 사용하도록 OS 디스크를 설정할 때에만 사용됩니다. |
| 플랫폼 이미지를 사용하도록 OS 디스크 설정 |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>운영 체제 디스크의 이름 및 [저장소](../../storage/common/storage-powershell-guide-full.md)에서의 해당 위치는 이전에 만든 구성 개체에 추가됩니다. |
| 일반화된 이미지를 사용하도록 OS 디스크 설정 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>운영 체제 디스크의 이름, 원본 이미지의 위치 및 [저장소](../../storage/common/storage-powershell-guide-full.md)에서의 디스크의 위치는 구성 개체에 추가됩니다. |
| 특수 이미지를 사용하도록 OS 디스크 설정 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| VM 만들기 |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>모든 리소스는 [리소스 그룹](../../azure-resource-manager/powershell-azure-resource-manager.md)에서 생성됩니다. 이 명령을 실행하기 전에, New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface, and Set-AzureRmVMOSDisk을 실행합니다. |

## <a name="get-information-about-vms"></a>VM에 대한 정보 가져오기

| Task | 명령 |
| ---- | ------- |
| 구독에서 Vm 나열 |[Get AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| 리소스 그룹에서 Vm 나열 |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>구독에서 리소스 그룹 목록을 가져오려면 [Get AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup)을 사용할 수 있습니다. |
| VM에 대한 정보 가져오기 |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>VM 관리
| Task | 명령 |
| --- | --- |
| VM 시작 |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM 중지 |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 실행 중인 VM 다시 시작 |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM 삭제 |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM 일반화 |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>이 명령을 Save-AzureRmVMImage를 실행하기 전에 실행합니다. |
| VM 캡처 |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>가상 머신을 [준비하여 종료하고 일반화하여](prepare-for-upload-vhd-image.md) 이미지를 만드는 데 사용해야 합니다. 이 명령을 실행하기 전에 Set-AzureRmVm을 실행합니다. |
| VM 업데이트 |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Get-AzureRmVM을 사용하여 현재 VM 구성을 가져오고, VM 개체에서 구성 설정을 변경한 후, 이 명령을 실행합니다. |
| VM에 데이터 디스크 추가 |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>VM 개체를 가져오려면 Get-AzureRmVM을 사용합니다. LUN 번호 및 디스크 크기를 지정합니다. VM에 구성 변경을 적용하려면 Update-AzureRmVM을 실행합니다. 추가한 디스크가 초기화되지 않았습니다. |
| VM에서 데이터 디스크 제거 |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>VM 개체를 가져오려면 Get-AzureRmVM을 사용합니다. VM에 구성 변경을 적용하려면 Update-AzureRmVM을 실행합니다. |
| VM에 확장 추가 |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>설치하려는 확장에 대한 해당 [구성 정보](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions)를 사용하여 이 명령을 실행합니다. |
| VM 확장 제거 |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>다음 단계
* [리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 가상 컴퓨터 만들기 기본 단계를 참조합니다.

