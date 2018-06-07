---
title: Windows VM에서 암호 또는 원격 데스크톱 구성 다시 설정 | Microsoft Docs
description: Azure 포털 또는 Azure PowerShell을 사용하여 Windows VM에서 계정 암호 또는 원격 데스크톱 서비스를 다시 설정하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: 26a213d490ee3f661735ff5b893b0a5f5f9906da
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법
Windows VM(가상 머신)에 연결할 수 없는 경우 로컬 관리자 암호를 다시 설정할 수도 있고 원격 데스크톱 서비스 구성을 다시 설정할 수도 있습니다(Windows 도메인 컨트롤러에서는 지원되지 않음). 암호를 다시 설정하려면 Azure 포털이나 Azure PowerShell의 VM 액세스 확장을 사용할 수 있습니다. VM에 로그인하면 해당 사용자의 암호를 다시 설정해야 합니다.  
PowerShell을 사용하는 경우 [최신 PowerShell 모듈을 설치 및 구성](/powershell/azure/overview)하고 Azure 구독에 로그인해야 합니다. [클래식 배포 모델을 사용하여 만든 VM에 대해 이러한 단계를 수행](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp)할 수도 있습니다.

## <a name="ways-to-reset-configuration-or-credentials"></a>구성 또는 자격 증명을 다시 설정하는 방법
필요에 따라 몇 가지 방법으로 원격 데스크톱 서비스 및 자격 증명을 재설정할 수 있습니다.

- [Azure 포털을 사용하여 재설정](#azure-portal)
- [Azure PowerShell을 사용하여 재설정](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure portal
포털 메뉴를 확장하려면 왼쪽 위 구석에 있는 세 개의 막대를 클릭한 다음 **가상 머신**을 클릭합니다.

![Azure VM 찾아보기](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**

Windows 가상 머신을 선택한 다음 **지원 + 문제 해결** > **암호 재설정**을 클릭합니다. 암호 다시 설정 블레이드가 표시됩니다.

![암호 다시 설정 페이지](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

사용자 이름 및 새 암호를 입력한 후 **업데이트**를 클릭합니다. VM에 연결을 다시 시도하세요.

### <a name="reset-the-remote-desktop-service-configuration"></a>**원격 데스크톱 서비스 구성을 다시 설정**

Windows 가상 머신을 선택한 다음 **지원 + 문제 해결** > **암호 재설정**을 클릭합니다. 암호 다시 설정 블레이드가 표시됩니다. 

![RDP 구성 다시 설정](./media/reset-rdp/Portal-RM-RDP-Reset.png)

드롭다운 메뉴에서 **구성만 재설정**을 선택하고 **업데이트**를 클릭합니다. VM에 연결을 다시 시도하세요.


## <a name="vmaccess-extension-and-powershell"></a>VMAccess 확장 및 PowerShell
[최신 PowerShell 모듈을 설치 및 구성](/powershell/azure/overview)하고 `Connect-AzureRmAccount` cmdlet을 사용하여 Azure 구독에 로그인해야 합니다.

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**
[Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet을 사용하여 관리자 암호 또는 사용자 이름을 다시 설정합니다. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> VM에서 현재 로컬 관리자 계정이 아닌 다른 이름을 입력한 경우 VMAccess 확장은 해당 이름으로 로컬 관리자 계정을 추가하고 해당 계정에 지정된 암호를 할당합니다. VM에서 로컬 관리자 계정이 있으면 암호를 다시 설정하고 계정을 사용하지 않도록 설정된 경우 VMAccess 확장은 이를 사용하도록 설정합니다.

### <a name="reset-the-remote-desktop-service-configuration"></a>**원격 데스크톱 서비스 구성을 다시 설정**
[Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet을 사용하여 VM에 대한 원격 액세스를 다시 설정합니다. 다음 예제에서는 리소스 그룹 `myVMAccess`의 VM `myVM`에서 `myResourceGroup` 진단 확장을 다시 설정합니다.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> 언제든지 VM은 단일 VM 액세스 에이전트를 하나만 사용할 수 있습니다. VM 액세스 에이전트 속성을 성공적으로 설정하려면 `-ForceRerun` 옵션을 사용하면 됩니다. `-ForceRerun`을 사용하는 경우 VM 액세스 에이전트에 이전 명령에서 사용한 것과 동일한 이름을 사용해야 합니다.

가상 머신에 원격으로 연결할 수 없으면 시도에 더 많은 단계를 [Windows 기반 Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. Windows 도메인 컨트롤러에 대한 연결이 끊어지면 도메인 컨트롤러 백업에서 복원해야 합니다.

## <a name="next-steps"></a>다음 단계
Azure VM 액세스 확장이 응답하지 않고 암호를 다시 설정할 수 없는 경우 [오프라인으로 로컬 Windows 암호를 다시 설정](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수 있습니다. 이 방법은 좀 더 고급 프로세스로, 문제가 있는 VM의 가상 하드 디스크를 다른 VM에 연결해야 합니다. 먼저 이 문서에 설명된 단계를 수행하고, 오프라인 암호 다시 설정 방법은 최후의 수단으로만 시도합니다.

[Azure VM 확장 및 기능](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows 기반 Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

