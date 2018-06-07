---
title: Azure에서 대규모로 Azure VM 백업 | Microsoft Docs
description: 이 자습서에서는 여러 Azure 가상 머신을 Recovery Services 자격 증명 모음에 백업하는 방법을 자세히 설명합니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: 가상 머신 백업; 백업 및 재해 복구
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 62cc623dc3130119c5ec803933012c5545d703e5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Azure에서 대규모로 Azure 가상 머신 백업

이 자습서에서는 Azure 가상 머신을 Recovery Services 자격 증명 모음에 백업하는 방법을 자세히 설명합니다. 가상 머신을 백업하는 작업은 대부분 준비 작업입니다. 가상 머신을 백업하거나 보호할 수 있으려면, VM을 보호하도록 환경을 준비하기 위한 [필수 구성 요소](backup-azure-arm-vms-prepare.md)를 완료해야 합니다. 

> [!IMPORTANT]
> 이 자습서에서는 리소스 그룹 및 Azure 가상 머신을 이미 만들었다고 가정합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

[Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 백업 중인 항목에 대한 복구 지점을 포함하는 컨테이너입니다. Recovery Services 자격 증명 모음은 Azure 리소스 그룹의 일부로 배포 및 관리할 수 있는 Azure 리소스입니다. 이 자습서에서는 보호되는 가상 컴퓨터와 동일한 리소스 그룹에 Recovery Services 자격 증명 모음을 만듭니다.


Azure Backup을 처음 사용할 때에는 구독과 함께 Azure Recovery Services 공급자를 등록해야 합니다. 구독과 함께 공급자를 이미 등록한 경우 다음 단계로 이동합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

**New-AzureRmRecoveryServicesVault**를 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 백업할 가상 머신을 구성할 때 사용할 리소스 그룹 이름과 위치를 지정해야 합니다. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다. 그런 다음 **Set-AzureRmRecoveryServicesBackupProperties**를 사용해 **-BackupStorageRedundancy** 옵션을 [GRS(지역 중복 저장소)](../storage/common/storage-redundancy-grs.md)로 설정합니다. 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Azure 가상 머신 백업

초기 백업을 실행하기 전에 자격 증명 모음 컨텍스트를 설정해야 합니다. 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. Recovery Services 자격 증명 모음을 만들면 기본 보호 및 보존 정책이 함께 제공됩니다. 기본 보호 정책은 매일 지정된 시간에 백업 작업을 트리거합니다. 기본 보존 정책은 매일 복구 지점을 30일 동안 유지합니다. 이 자습서에서는 기본 정책을 적용합니다. 

**[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**를 사용하여 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

**[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**을 사용하여 백업 작업을 트리거합니다. 백업 작업에서 복구 지점이 생성됩니다. 초기 백업인 경우 복구 지점이 전체 백업입니다. 후속 백업에서는 증분 복사가 수행됩니다.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

Recovery Services 자격 증명 모음을 삭제하려면 자격 증명 모음의 복구 지점을 먼저 삭제한 다음 자격 증명 모음의 등록을 취소해야 합니다. 다음 명령은 이 단계를 자세히 설명합니다. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>문제 해결 오류
가상 머신을 백업하는 동안 문제가 발생한 경우 [Azure 가상 머신 백업 문제 해결 문서](backup-azure-vms-troubleshoot.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 가상 머신을 보호했으므로, 다음 문서를 참조하여 관리 작업과 복구 지점에서 가상 머신을 복원하는 방법을 알아보세요.

* 백업 정책을 수정하려면 [AzureRM.RecoveryServices.Backup cmdlet을 사용하여 가상 머신 백업](backup-azure-vms-automation.md#create-a-protection-policy)을 참조하세요.
* [가상 머신 관리 및 모니터링](backup-azure-manage-vms.md)
* [가상 머신 복원](backup-azure-arm-restore-vms.md)
