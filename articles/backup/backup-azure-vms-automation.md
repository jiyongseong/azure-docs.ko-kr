---
title: PowerShell을 사용하여 Resource Manager 배포 VM에 대한 백업 배포 및 관리 | Microsoft Docs
description: PowerShell을 사용하여 Azure에서 Resource Manager 배포 VM에 대한 백업 배포 및 관리
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk;pullabhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3431db3844ca47ce6c2beafbd894a69f05e0311a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>AzureRM.RecoveryServices.Backup cmdlet을 사용하여 가상 머신 백업

이 문서는 Azure PowerShell cmdlet을 사용하여 Recovery Services 자격 증명 모음으로부터 Azure VM(가상 머신)을 복원하고 백업하는 방법을 보여 줍니다. Recovery Services 자격 증명 모음은 Azure Resource Manager 리소스이며 Azure Backup 및 Azure Site Recovery 서비스에서 데이터와 자산을 보호하는 데 사용됩니다. Recovery Services 자격 증명 모음을 사용하여 Azure Resource Manager 배포 VM은 물론, Azure 서비스 관리자 배포 VM도 보호할 수 있습니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 이 문서는 리소스 관리자 모델을 사용하여 생성된 VM 사용에 대해 설명합니다.
>
>

이 문서는 PowerShell을 사용하여 VM을 보호하고, 복구 지점으로부터 데이터를 복원하는 단계를 안내합니다.

## <a name="concepts"></a>개념
Azure Backup Service를 잘 모르는 경우, [Azure 백업이란?](backup-introduction-to-azure-backup.md)에서 서비스에 대한 개요를 확인하세요. 시작하기 전에 먼저 Azure Backup 작업에 필요한 필수 조건 및 현재 VM 솔루션의 제한에 관한 기본 사항을 다루었는지 확인합니다.

PowerShell을 효과적으로 사용하기 위해 개체의 계층 구조와 시작하는 위치를 이해하는 데 필요합니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRm.RecoveryServices.Backup PowerShell cmdlet 참조를 보려면 Azure 라이브러리의 [Azure Backup - Recovery Services cmdlet](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)을 참조하세요.

## <a name="setup-and-registration"></a>설정 및 등록
시작하려면

1. [PowerShell 최신 버전 다운로드](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (필요한 최소 버전: 1.4.0)

2. 다음 명령을 입력하여 사용할 수 있는 Azure Backup PowerShell cmdlet을 찾습니다.
    ```PS
    PS C:\> Get-Command *azurermrecoveryservices*
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
    Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
    Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
    Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    ```
3. **Connect-AzureRmAccount**를 사용하여 Azure 계정에 로그인합니다. 이 cmdlet은 계정 자격 증명을 묻는 웹 페이지를 엽니다. 
    - 또는 **Connect-AzureRmAccount** cmdlet에서 **-Credential** 매개 변수를 사용하여 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    - 사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예: **Connect-AzureRmAccount -Tenant "fabrikam.com"**
4. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. 처음으로 Azure Backup을 사용하는 경우 **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록해야 합니다.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ```

6. 다음 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인할 수 있습니다.
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ``` 
명령 출력에서 **RegistrationState**는 **등록됨**으로 설정해야 합니다. 그렇지 않으면 위에 표시된 **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet을 다시 실행합니다.

다음 작업은 PowerShell로 자동화할 수 있습니다.

* [Recovery Services 자격 증명 모음 만들기](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Azure VM 백업](backup-azure-vms-automation.md#back-up-azure-vms)
* [백업 작업 트리거](backup-azure-vms-automation.md#trigger-a-backup-job)
* [백업 작업 모니터링](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Azure VM 복원](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다. Recovery Services 자격 증명 모음은 Backup 자격 증명 모음과 다릅니다.

1. Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet을 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md) 또는 [GRS(지역 중복 저장소)](../storage/common/storage-redundancy-grs.md) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testvault에 대한 -BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기
**[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** 를 사용하여 현재 구독의 모든 자격 증명 모음 목록을 볼 수 있습니다. 이 명령을 사용하여 새 자격 증명 모음이 만들어졌는지 확인하거나 구독에서 사용할 수 있는 자격 증명 모음을 확인할 수 있습니다.

구독의 모든 자격 증명 모음을 보려면 Get-AzureRmRecoveryServicesVault 명령을 실행합니다. 다음 예제에서는 각 자격 증명 모음에 대해 표시되는 정보를 보여 줍니다.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Azure VM 백업
Recovery Services 자격 증명 모음을 사용하여 가상 머신을 보호합니다. 보호를 적용하기 전에 자격 증명 모음 컨텍스트(자격 증명 모음에 보호된 데이터 형식)를 설정하고 보호 정책을 확인합니다. 보호 정책은 백업 작업을 실행하는 시간과 각 백업 스냅숏을 유지하는 기간에 대한 일정입니다.

### <a name="set-vault-context"></a>자격 증명 모음 컨텍스트 설정
VM에서 보호를 사용하도록 설정하기 전에 **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 를 사용하여 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다. 다음 예제에서는 자격 증명 모음, *testvault*에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>보호 정책 만들기 
Recovery Services 자격 증명 모음을 만들면 기본 보호 및 보존 정책이 함께 제공됩니다. 기본 보호 정책은 매일 지정된 시간에 백업 작업을 트리거합니다. 기본 보존 정책은 매일 복구 지점을 30일 동안 유지합니다. 기본 정책을 사용하여 VM을 신속하게 보호하고, 나중에 다른 세부 정보로 정책을 편집할 수 있습니다.

자격 증명 모음에서 사용할 수 있는 정책의 목록을 보려면 **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** 를 사용합니다. 특정 정책을 받거나 워크로드 유형과 연결된 정책을 보려면 이 cmdlet을 사용할 수 있습니다. 다음 예제에서는 워크로드 유형, AzureVM에 대한 정책을 가져옵니다.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 전에 얼마나 오래 유지할지를 정의합니다. 기본 보존 정책을 보려면 **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** 를 사용합니다.  마찬가지로 **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** 를 사용하여 기본 일정 정책을 볼 수 있습니다. **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다. 일정 및 보존 정책 개체는 **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet에 대해 입력으로 사용됩니다. 다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 예제에서는 이러한 변수를 사용하여 보호 정책, *NewPolicy*를 만들 때 매개 변수를 정의합니다.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>보호 사용
백업 보호 정책을 정의한 후에는 항목에 대한 정책을 계속 사용해야 합니다. 보호를 사용하도록 설정하려면 **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** 을 사용합니다. 보호를 사용하도록 설정하는 데에는 항목 및 정책이라는 두 가지 개체가 필요합니다. 정책이 자격 증명 모음과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

다음 예제에서는 NewPolicy 정책을 사용하여 V2VM 항목에 대해 보호를 활성화합니다. 암호화되지 않은 Resource Manager VM에 대한 보호를 사용하도록 설정하려면

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

암호화된 VM(BEK 및 KEK를 사용하여 암호화된)에 대한 보호를 사용하도록 설정하려면 Azure Backup 서비스에 주요 자격 증명 모음에서 키 및 암호를 읽을 수 있는 권한을 제공해야 합니다.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

암호화된 VM(BEK만 사용하여 암호화된)에 대한 보호를 사용하도록 설정하려면 Azure Backup 서비스에 주요 자격 증명 모음에서 비밀을 읽을 수 있는 권한을 제공해야 합니다.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Azure Government 클라우드를 사용 중이라면 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet에서 **-ServicePrincipalName** 매개 변수에 대한 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 값을 사용합니다.
>
>

클래식 VM

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>보호 정책 수정
보호 정책을 수정하려면 [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)를 사용하여 SchedulePolicy 또는 RetentionPolicy 개체를 수정합니다.

다음 예제는 복구 지점 보존 기간을 365일로 변경합니다.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>백업 트리거
**[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** 을 사용하여 백업 작업을 트리거할 수 있습니다. 초기 백업인 경우 전체 백업입니다. 후속 백업에서는 증분 복사를 수행합니다. **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 를 사용하여 백업 작업을 트리거하기 전에 자격 증명 모음 컨텍스트를 설정합니다. 다음 예제에서는 자격 증명 모음 컨텍스트가 설정되었다고 가정합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell에서 표시되는 StartTime 및 EndTime 필드의 시간대는 UTC입니다. 하지만, 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

## <a name="monitoring-a-backup-job"></a>백업 작업 모니터링
Azure Portal을 사용하지 않고 백업 작업과 같은 장기 실행 작업을 모니터링할 수 있습니다. 진행 중인 작업의 상태를 가져오려면 **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet을 사용합니다. 이 cmdlet은 특정 자격 증명 모음에 대한 백업 작업을 가져오고 해당 자격 증명 모음은 자격 증명 모음 컨텍스트에 지정됩니다. 다음 예제에서는 배열과 같은 진행 중인 작업의 상태를 가져와 $joblist 변수에 상태를 저장합니다.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료하기 위해 폴링하는 대신(불필요한 추가 코드) **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet을 사용합니다. 이 cmdlet은 작업이 완료되거나 지정된 시간 제한 값에 도달할 때까지 실행을 일시 중지합니다.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Azure VM 복원
Azure 포털을 사용하여 VM을 복원하는 방식과 PowerShell을 사용하여 VM을 복원하는 방식 간에는 주요 차이점이 있습니다. PowerShell을 사용하면 복구 지점에서 디스크 및 구성 정보가 만들어질 때 복원 작업이 완료됩니다. Azure VM 백업에서 몇 개의 파일을 복원하거나 복구하려면 [파일 복구](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) 섹션을 참조하세요.

> [!NOTE]
> 복원 작업 중에 가상 머신은 만들어지지 않습니다.
>
>

디스크에서 가상 머신을 만들려면 [저장된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 섹션을 참조하세요. Azure VM을 복원하는 기본 단계는 다음과 같습니다.

* VM 선택
* 복구 지점 선택
* 디스크 복원
* 저장된 디스크에서 VM 만들기

다음 그래픽에서는 RecoveryServicesVault에서 BackupRecoveryPoint까지로 내려가는 개체 계층 구조를 보여 줍니다.

![BackupContainer를 표시하는 Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

백업 데이터를 복원하려면 백업 항목 및 지정 시간 데이터가 포함된 복구 지점을 식별합니다. **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet을 사용하여 자격 증명 모음에서 고객의 계정으로 데이터를 복원합니다.

### <a name="select-the-vm"></a>VM 선택
올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택하려면 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet을 사용하고 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet으로 파이프합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택
**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모를 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 과거 7일부터 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>디스크 복원
**[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet을 사용하여 백업 항목의 데이터 및 구성을 복구 지점으로 복원합니다. 복구 지점을 식별하면 이 지점을 **-RecoveryPoint** 매개 변수의 값으로 사용합니다. 이전 샘플 코드에서 **$rp[0]** 이 사용할 복구 지점이었지만, 아래 샘플 코드에서는 **$rp[0]** 이 디스크를 복원하는 데 사용할 복구 지점이 됩니다.

디스크 및 구성 정보를 복원하려면

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

복원 작업이 완료될 때까지 대기하는 동안 **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet을 사용합니다.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

복원 작업이 완료되면 **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet을 사용하여 복원 작업의 세부 정보를 가져올 수 있습니다. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 포함됩니다.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

디스크를 복원한 후 다음 섹션으로 이동하여 VM을 만듭니다.

## <a name="create-a-vm-from-restored-disks"></a>복원된 디스크에서 VM 만들기
디스크를 복원한 후 다음 단계를 사용하여 디스크에서 가상 머신을 만들고 구성합니다.

> [!NOTE]
> 복원된 디스크에서 암호화된 VM을 만들려면 Azure 역할에 **Microsoft.KeyVault/vaults/deploy/action** 작업을 수행할 권한이 있어야 합니다. 사용자의 역할에 이 사용 권한이 없는 경우 이 작업이 포함된 사용자 지정 역할을 만드세요. 자세한 내용은 [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.
>
>

1. 복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Azure 저장소 컨텍스트를 설정하고 JSON 구성 파일을 복원합니다.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. JSON 구성 파일을 사용하여 VM 구성을 만듭니다.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. OS 디스크 및 데이터 디스크를 연결합니다. VM 구성에 따라 관련 섹션에서 해당 cmdlet을 참조하세요.

    #### <a name="non-managed-non-encrypted-vms"></a>관리되지 않고 암호화되지 않은 VM

    관리되지 않고 암호화되지 않은 VM에 다음 샘플을 사용합니다.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>관리되지 않고 암호화된 VM(BEK만 해당)

    관리되지 않고 암호화된 VM의 경우(BEK만 사용하여 암호화됨) 디스크를 연결할 수 있으려면 비밀을 Key Vault로 복원해야 합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md) 문서를 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>관리되지 않고 암호화된 VM(BEK 및 KEK)

    관리되지 않고 암호화된 VM의 경우(BEK 및 KEK를 암호화됨) 디스크를 연결할 수 있으려면 키 및 비밀을 Key Vault로 복원해야 합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md) 문서를 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>관리되고 암호화되지 않은 VM

    관리되고 암호화되지 않은 VM의 경우 Blob Storage에서 관리 디스크를 만든 후 디스크를 연결해야 합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md) 문서를 참조하세요. 다음 샘플 코드는 관리 및 암호화되지 않은 VM에 대해 데이터 디스크를 연결하는 방법을 보여 줍니다.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>관리 및 암호화되는 VM(BEK만 해당)

    관리 및 암호화되는 VM의 경우(BEK만 사용하여 암호화됨) Blob Storage에서 관리 디스크를 만든 후 디스크를 연결해야 합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md) 문서를 참조하세요. 다음 샘플 코드는 관리 및 암호화된 VM에 대해 데이터 디스크를 연결하는 방법을 보여 줍니다.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>관리되고 암호화된 VM(BEK 및 KEK)

    관리 및 암호화되는 VM의 경우(BEK 및 KEK를 암호화됨) Blob Storage에서 관리 디스크를 만든 후 디스크를 연결해야 합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md) 문서를 참조하세요. 다음 샘플 코드는 관리 및 암호화된 VM에 대해 데이터 디스크를 연결하는 방법을 보여 줍니다.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. 네트워크 설정을 지정합니다.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 가상 머신을 만듭니다.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM 백업에서 파일 복원

디스크를 복원하는 것 외에 Azure VM 백업에서 개별 파일을 복원할 수도 있습니다. 복원 파일 기능은 복구 지점에 있는 모든 파일에 대한 액세스 권한을 제공하며 일반 파일에서와 마찬가지로 파일 탐색기를 통해 파일을 관리할 수 있습니다.

Azure VM 백업에서 파일을 복원하는 기본 단계는 다음과 같습니다.

* VM 선택
* 복구 지점 선택
* 복구 지점의 디스크 탑재
* 필요한 파일 복사
* 디스크 분리


### <a name="select-the-vm"></a>VM 선택
올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택하려면 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet을 사용하고 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet으로 파이프합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택
**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모를 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 과거 7일부터 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>복구 지점의 디스크 탑재

**[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** cmdlet을 사용하여 복구 지점의 모든 디스크를 탑재하는 스크립트를 가져옵니다.

> [!NOTE]
> 디스크는 스크립트가 실행되는 컴퓨터에 iSCSI 연결 디스크로 탑재됩니다. 따라서 순간적으로 작업이 이루어지고 요금이 발생하지 않습니다.
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
파일을 복구할 컴퓨터에서 스크립트를 실행합니다. 스크립트를 실행하려면 위에 표시된 암호를 입력해야 합니다. 디스크가 연결된 후에 Windows 파일 탐색기를 사용하여 새 볼륨 및 파일을 검색합니다. 자세한 내용은 [파일 복구 설명서](backup-azure-restore-files-from-vm.md)를 참조하세요.

### <a name="unmount-the-disks"></a>디스크 분리
필요한 파일을 복사한 후 **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** cmdlet을 사용하여 디스크를 분리합니다. 복구 지점의 파일에 대한 액세스가 제거되도록 하므로 이 작업은 매우 권장됩니다.

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>다음 단계
PowerShell을 사용하여 Azure 리소스와 연결하려는 경우 [Windows Server용 Backup 배포 및 관리](backup-client-automation.md) PowerShell 문서를 참조하세요. DPM 백업을 관리하는 경우 [DPM에 대한 Backup 배포 및 관리](backup-dpm-automation.md) 문서를 참조하세요. 이러한 문서 모두에 Resource Manager 배포용 버전뿐 아니라 클래식 배포용 버전도 포함되어 있습니다.  
