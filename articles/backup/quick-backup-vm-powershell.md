---
title: Azure 빠른 시작 - PowerShell을 사용하여 VM 백업 | Microsoft Docs
description: Azure PowerShell을 사용하여 가상 머신을 백업하는 방법을 알아봅니다.
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 91303368f96b477f0756e4d3b9d19601797f0ba8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>PowerShell을 사용하여 Azure에서 가상 머신 백업
명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 정기적으로 백업을 수행하여 데이터를 보호할 수 있습니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장될 수 있는 복구 지점을 만듭니다. 이 문서에서는 Azure PowerShell 모듈에서 VM(가상 머신)을 백업하는 방법을 자세히 설명합니다. [Azure CLI](quick-backup-vm-cli.md) 또는 [Azure Portal](quick-backup-vm-portal.md)에서 이러한 단계를 수행할 수도 있습니다.

이 빠른 시작을 사용하면 기존 Azure VM에서 백업할 수 있습니다. VM을 생성해야 하는 경우 [Azure PowerShell을 사용하여 VM을 만들](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) 수 있습니다.

이 빠른 시작에서는 Azure PowerShell 모듈 버전 4.4 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.


## <a name="log-in-to-azure"></a>Azure에 로그인
`Connect-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

Azure Backup을 처음 사용할 때에는 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider)를 사용하여 구독에서 Azure Recovery Services 공급자를 등록해야 합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Recovery Services 자격 증명 모음 만들기
Recovery Services 자격 증명 모음은 Azure VM과 같은 각 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

[New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)를 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 보호하려는 VM으로 동일한 리소스 그룹 및 위치를 지정합니다. [샘플 스크립트](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)를 사용하여 VM을 만든 경우 리소스 그룹 이름은 *myResourceGroup*으로 지정되고 VM 이름은 *myVM*으로 지정되며 리소스는 *WestEurope*에 위치합니다.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

기본적으로 자격 증명 모음이 지역 중복 저장소에 설정됩니다. 데이터를 추가로 보호하려면 이 저장소 중복 수준을 통해 백업 데이터를 주 지역으로부터 수백 마일 떨어진 보조 Azure 지역에 복제하도록 합니다.

나머지 단계에서 이 자격 증명 모음을 사용하려면 자격 증명 모음 컨텍스트를 [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)로 설정합니다.

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Azure VM에 백업 사용
정책을 만들고 사용하여 백업 작업을 실행하는 시점 및 복구 지점이 저장되는 기간을 정의합니다. 기본 보호 정책은 매일 백업 작업을 실행하고 복구 지점을 30일 동안 유지합니다. VM을 신속하게 보호하기 위해 이러한 기본 정책 값을 사용할 수 있습니다. 먼저, 기본 정책을 [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy)로 설정합니다.

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

VM에 백업 보호를 사용하도록 설정하려면 [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection)을 사용합니다. 보호하는 데 사용할 정책, 리소스 그룹 및 VM을 지정합니다.

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>백업 작업 시작
예약된 시간에 작업을 실행하는 기본 정책을 대기하지 않고 지금 백업을 시작하려면 [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)을 사용합니다. 이 첫 번째 백업 작업에서 전체 복구 지점이 생성됩니다. 이 초기 백업 후에 각 백업 작업에서 증분 복구 지점이 생성됩니다. 증분 복구 지점은 마지막 백업 이후 변경된 내용만을 전송하기 때문에 저장소 및 시간 효율적입니다.

다음 명령 집합에서 [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)를 사용하여 백업 데이터를 보유하는 Recovery Services 자격 증명 모음에서 컨테이너를 지정합니다. 백업할 각 VM은 항목으로 처리됩니다. 백업 작업을 시작하려면 [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem)을 사용하여 VM 항목에 대한 정보를 가져옵니다.

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

이 첫 번째 백업 작업에서 전체 복구 지점이 생성될 때 프로세스는 20분 정도가 걸릴 수 있습니다.


## <a name="monitor-the-backup-job"></a>백업 작업 모니터링
백업 작업의 상태를 모니터링하려면 [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)을 사용합니다.

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

출력은 다음 예제와 비슷합니다. 여기서는 백업 작업 상태가 **InProgress**라고 표시됩니다.

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

백업 작업의 *상태*가 *완료됨*으로 보고되는 경우 VM은 Recovery Services로 보호되고 전체 복구 지점이 저장됩니다.


## <a name="clean-up-deployment"></a>배포 정리
더 이상 필요하지 않은 경우 VM에 대한 보호를 사용하지 않도록 설정하고, 복원 지점 및 Recovery Services 자격 증명 모음을 제거한 후 리소스 그룹 및 연결된 VM 리소스를 삭제할 수 있습니다. 기존 VM을 사용하는 경우 최종 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet으로 건너뛰어 리소스 그룹 및 VM을 준비할 수 있습니다.

VM에 대한 데이터를 복원하는 방법에 대해 설명하는 백업 자습서를 계속 진행하는 경우 이 섹션의 단계를 건너뛰고 [다음 단계](#next-steps)로 이동합니다. 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만들었습니다. Azure Backup 및 Recovery Services에 대한 자세한 내용은 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [여러 Azure VM 백업](./tutorial-backup-vm-at-scale.md)
