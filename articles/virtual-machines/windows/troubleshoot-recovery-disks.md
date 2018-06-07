---
title: Azure PowerShell에서 Windows 문제 해결 VM 사용 | Microsoft Docs
description: Azure PowerShell을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Azure에서 Windows VM 문제를 해결하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 408429d0f8697b8b807e386dbcf2eade29938249
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271694"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Azure PowerShell을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결
Azure에서 Windows VM(가상 머신)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 응용 프로그램 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Windows VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure PowerShell을 사용하는 방법을 자세히 설명합니다.


## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 문제가 발생하는 VM을 삭제하여, 가상 하드 디스크를 유지합니다.
2. 문제 해결을 위해 가상 하드 디스크를 다른 Windows VM에 연결하고 탑재합니다.
3. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 문제를 수정합니다.
4. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
5. 원래 가상 하드 디스크를 사용하여 VM을 만듭니다.

관리 디스크를 사용하는 VM에 대해서는 [새 OS 디스크를 연결하여 Managed Disk VM 문제 해결](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk)을 참조합니다.


먼저 [최신 Azure PowerShell](/powershell/azure/overview)을 설치하고 구독에 로그인했는지 확인합니다.

```powershell
Connect-AzureRmAccount
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`이 포함됩니다.


## <a name="determine-boot-issues"></a>부팅 문제 확인
Azure에서 VM의 스크린샷을 보고 부팅 문제를 해결할 수 있습니다. 이 스크린샷은 VM이 부팅되지 않는 이유를 확인하는 데 도움이 됩니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 Windows VM `myVM`에서 스크린샷을 가져옵니다.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

스크린샷을 검토하여 VM이 부팅되지 않는 원인을 확인합니다. 제공된 특정 오류 메시지 또는 오류 코드를 기록해 둡니다.


## <a name="view-existing-virtual-hard-disk-details"></a>기존 가상 하드 디스크 세부 정보 보기
가상 하드 디스크를 다른 VM에 연결하기 전에 가상 하드 디스크(VHD)의 이름을 식별해야 합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에 대한 정보를 수신합니다.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

이전 명령의 출력에서 ​​`StorageProfile` 섹션 내의 `Vhd URI`를 찾습니다. 다음 잘린 출력 예에서 코드 블록의 끝을 향한 `Vhd URI`를 보여 줍니다.

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>기존 VM 삭제
가상 하드 디스크와 VM은 Azure의 두 가지 별개의 리소스입니다. 가상 하드 디스크에는 운영 체제 자체, 응용 프로그램 및 구성이 저장됩니다. VM 자체는 크기 또는 위치를 정의하고 가상 하드 디스크 또는 가상 네트워크 인터페이스 카드(NIC)와 같은 리소스를 참조하는 메타데이터일 뿐입니다. 각 가상 하드 디스크에는 VM에 연결할 때 할당된 임대가 있습니다. VM을 실행하는 동안에도 데이터 디스크를 연결하고 분리할 수 있지만, VM 리소스를 삭제하지 않는 한 OS 디스크를 분리할 수 없습니다. 해당 VM이 중지 및 할당 취소된 상태에 있을 때에도 임대는 OS 디스크와 VM을 계속 연결합니다.

VM을 복구하는 첫 번째 단계는 자체 VM 리소스를 삭제하는 것입니다. VM을 삭제하면 가상 하드 디스크는 저장소 계정에 남게 됩니다. VM을 삭제한 후 가상 하드 디스크를 다른 VM에 연결하여 문제와 오류를 해결합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 삭제합니다.

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

가상 하드 디스크를 다른 VM에 연결 하기 전에 VM이 삭제 작업을 끝낼 때까지 기다립니다. VM과 연결하는 가상 하드 디스크의 임대는 가상 하드 디스크를 다른 VM에 연결하기 전에 해제해야 합니다.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>기존 가상 하드 디스크를 다른 VM에 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 기존 가상 하드 디스크를 이 문제 해결 VM에 연결하여 디스크의 콘텐츠를 찾아 편집합니다. 예를 들어 이 프로세스를 사용하면 구성 오류를 수정하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 다른 VM을 선택하거나 만들어 문제 해결에 사용합니다.

기존 가상 하드 디스크를 연결하는 경우 이전 `Get-AzureRmVM` 명령에서 획득한 디스크에 URL을 지정합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 문제 해결 VM `myVMRecovery`에 기존 가상 하드 디스크를 연결합니다.

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> 디스크를 추가하려면 디스크의 크기를 지정해야 합니다. 기존 디스크를 연결할 때 `-DiskSizeInGB`가 `$null`로 지정됩니다. 이 값은 데이터 디스크의 실제 크기를 확인하지 않아도 데이터 디스크가 올바르게 연결되도록 합니다.


## <a name="mount-the-attached-data-disk"></a>연결된 데이터 디스크 탑재

1. 적절한 자격 증명을 사용하여 문제 해결 VM에 대한 RDP입니다. 다음 예에서는 `myResourceGroup`이라는 리소스 그룹에서 `myVMRecovery`라는 VM에 대한 RDP 연결 파일을 `C:\Users\ops\Documents`에 다운로드합니다

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. 데이터 디스크가 자동으로 감지되고 연결됩니다. 다음과 같이 연결된 볼륨 목록을 보고 드라이브 문자를 확인합니다.

    ```powershell
    Get-Disk
    ```

    다음 출력 예에서는 디스크 **2**에 연결된 가상 하드 디스크를 보여 줍니다. (`Get-Volume`을 사용하여 드라이브 문자를 볼 수도 있습니다.)

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 탑재 해제하고 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. RDP 세션의 복구 VM에서 데이터 디스크를 분리합니다. 이전 `Get-Disk` cmdlet의 디스크 번호가 필요합니다. 그런 다음 `Set-Disk`를 사용하여 디스크를 오프라인으로 설정합니다.

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    이제 `Get-Disk`를 다시 사용하여 디스크가 오프라인으로 설정되었는지 확인합니다. 다음 출력 예에서는 디스크가 오프라인으로 설정되었다는 것을 보여 줍니다.

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. RDP 세션을 종료합니다. Azure PowerShell 세션의 문제 해결 VM에서 가상 하드 디스크를 제거합니다.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>원래 하드 디스크에서 VM 만들기
원래 가상 하드 디스크에서 VM을 만들려면 [이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet)을 사용합니다. 실제 JSON 템플릿은 다음 링크에 있습니다.

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

템플릿은 이전 명령의 VHD URL을 사용하여 VM을 기존 가상 네트워크에 배포합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에 템플릿을 배포합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

VM 이름, OS 유형 및 VM 크기 등 템플릿에 대한 프롬프트에 응답합니다. `osDiskVhdUri`는 기존 가상 하드 디스크를 문제 해결 VM에 연결할 때 이전에 사용된 것과 동일합니다.


## <a name="re-enable-boot-diagnostics"></a>부트 진단 다시 사용

기존 가상 하드 디스크에서 VM을 만든 경우 부팅 진단을 자동으로 사용할 수 없습니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVMDeployed`에서 진단 확장을 사용할 수 있습니다.

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>새 OS 디스크를 연결하여 Managed Disk VM 문제 해결
1. 영향을 받는 Managed Disk Windows VM을 중지합니다.
2. Managed Disk VM의 OS 디스크의 [관리 디스크 스냅숏을 만듭니다](snapshot-copy-managed-disk.md).
3. [스냅숏에서 새 관리 디스크를 만듭니다](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [VM의 데이터 디스크로서 관리 디스크를 연결합니다](attach-disk-ps.md).
5. [4단계의 데이터 디스크를 OS 디스크로 변경합니다](os-disk-swap.md).

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 RDP 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. VM에서 실행 중인 응용 프로그램에 액세스하는 데 문제가 있는 경우 [Windows VM에서 응용 프로그램 연결 문제 해결](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
