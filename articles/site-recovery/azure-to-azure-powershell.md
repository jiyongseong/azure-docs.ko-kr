---
title: Azure Site Recovery - Azure PowerShell을 사용하여 Azure Virtual Machines에 대한 재해 복구 설정 및 테스트 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure Site Recovery와 함께 Azure Virtual Machines에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: bsiva
ms.openlocfilehash: e12d19612e10885f97d4a8023967fb9ba2d1f8cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209868"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Virtual Machines에 대한 재해 복구 설정

이 문서에서는 Azure PowerShell을 사용하여 Azure Virtual Machines에 대한 재해 복구를 설치 및 테스트하는 방법을 알아봅니다. 

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Recovery Services 자격 증명 모음을 만듭니다.
> - PowerShell 세션에 대한 자격 증명 모음 컨텍스트를 설정합니다.
> - Azure Virtual Machines 복제를 시작하려면 자격 증명 모음을 준비합니다.
> - 네트워크 매핑을 만듭니다.
> - 가상 머신을 복제할 대상 저장소 계정을 만듭니다.
> - 재해 복구에 대한 복구 지역에 Azure Virtual Machines를 복제합니다.
> - 테스트 장애 조치(failover), 유효성 검사 및 테스트 장애 조치(failover) 정리를 수행합니다.
> - 복구 지역으로 장애 조치(failover)합니다.

> [!NOTE]
> “*Azure Virtual Machines에 대한 재해 복구*”를 위한 Azure Site Recovery PowerShell 지원 시나리오는 현재 미리 보기 상태입니다. 포털을 통해 사용할 수 있는 일부 시나리오 기능은 Azure PowerShell을 통해 사용할 수 있습니다. 현재 Azure PowerShell을 통해 지원되지 않는 일부 시나리오 기능은 다음과 같습니다.
> - 관리 디스크를 사용하는 Azure Virtual Machines를 복제하는 기능.
> - 가상 머신의 모든 디스크는 가상 머신의 각 디스크를 명시적으로 지정할 필요 없이 복제되어야 함을 지정하는 기능입니다.  

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 수행합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](azure-to-azure-support-matrix.md)을 검토합니다.
- 버전 5.7.0 이상의 AzureRm PowerShell 모듈이 있습니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

Connect-AzureRmAccount cmdlet을 사용하여 Azure 구독에 로그인

```azurepowershell
Connect-AzureRmAccount
```
Azure 구독을 선택합니다. Get-AzureRmSubscription cmdlet을 사용하여 액세스 권한이 있는 Azure 구독 목록을 가져옵니다. Select-AzureRmSubscription cmdlet을 사용하여 작업에 사용할 Azure 구독을 선택합니다.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>복제할 가상 머신의 세부 정보 가져오기

이 문서의 예제에서는 미국 동부 지역에 있는 가상 머신이 복제되어 미국 서부 2 지역에서 복구됩니다. 복제 중인 가상 머신은 OS 디스크 및 단일 데이터 디스크를 사용하는 가상 머신입니다. 이 예제에서 사용되는 가상 머신의 이름은 AzureDemoVM입니다.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

가상 머신의 디스크에 대한 디스크 세부 정보를 가져옵니다. 디스크 세부 정보는 나중에 가상 머신에 대한 복제를 시작할 때 사용됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음을 만들 리소스 그룹을 만듭니다. 

> [!IMPORTANT]
> * Recovery Services 자격 증명 모음 및 보호 중인 가상 머신은 서로 다른 Azure 위치에 있어야 합니다.
> * Recovery Services 자격 증명 모음의 리소스 그룹 및 보호 중인 가상 머신은 서로 다른 Azure 위치에 있어야 합니다.
> * Recovery Services 자격 증명 모음 및 이에 속한 리소스 그룹은 동일한 Azure 위치에 있을 수 있습니다.
 
이 문서의 예제에서는 보호 중인 가상 머신이 미국 동부 지역에 있습니다. 재해 복구를 위해 선택한 복구 지역은 미국 서부 2 지역입니다. Recovery Services 자격 증명 모음 및 자격 증명 모음의 리소스 그룹은 모두 복구 지역(미국 서부 2)에 있습니다.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Recovery Services 자격 증명 모음을 만듭니다. 아래 예제에서 a2aDemoRecoveryVault라는 Recovery Services 자격 증명 모음이 미국 서부 2 지역에 생성됩니다.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

> [!TIP]
> Azure Site Recovery PowerShell 모듈(AzureRm.RecoveryServices.SiteRecovery 모듈)에는 대부분의 cmdlet에 사용하기 쉬운 별칭이 제공됩니다. 모듈의 cmdlet은 *\<Operation>-**AzureRmRecoveryServicesAsr**\<Object>* 형식이며 *\<Operation>-**ASR**\<Object>* 형식의 별칭이 있습니다. 이 문서에서는 읽기 쉽도록 cmdlet 별칭을 사용합니다.

PowerShell 세션에 사용할 자격 증명 모음 컨텍스트를 설정합니다. 이를 수행하려면 자격 증명 모음 설정 파일을 다운로드하고 다운로드한 파일을 PowerShell 세션으로 가져와서 자격 증명 모음 컨텍스트를 설정합니다. 

설정이 되면 PowerShell 세션의 후속 Azure Site Recovery 작업은 선택한 자격 증명 모음의 컨텍스트에서 수행됩니다. 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure Virtual Machines 복제를 시작하려면 자격 증명 모음을 준비합니다.

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. 기본(원본) 지역을 나타내는 Site Recovery 패브릭 개체를 만듭니다.

자격 증명 모음에 있는 패브릭 개체는 Azure 지역을 나타냅니다. 기본 패브릭 개체는 자격 증명 모음에 대해 보호 중인 가상 머신이 속한 Azure 지역을 나타내기 위해 만든 패브릭 개체입니다. 이 문서의 예제에서는 보호 중인 가상 머신이 미국 동부 지역에 있습니다.

> [!NOTE]
> Azure Site Recovery 작업은 비동기적으로 실행됩니다. 작업을 시작하면 Azure Site Recovery 작업이 제출되고 작업 추적 개체가 반환됩니다. 작업 추적 개체를 사용하여 작업(Get-ASRJob)에 대한 최신 상태를 가져오고 작업의 상태를 모니터링합니다.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
여러 Azure 지역의 가상 머신이 동일한 자격 증명 모음에 대해 보호되는 경우 각 원본 Azure 지역에 대한 하나의 패브릭 개체를 만듭니다.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. 복구 지역을 나타내는 Site Recovery 패브릭 개체를 만듭니다.

복구 패브릭 개체는 복구 Azure 위치를 나타냅니다. 가상 머신은 복구 패브릭으로 표시되는 복구 지역으로 복제 및 복구됩니다(장애 조치가 발생할 경우). 이 예에서 사용된 복구 Azure 지역은 미국 서부 2입니다.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. 기본 패브릭에서 Site Recovery 보호 컨테이너를 만듭니다.

보호 컨테이너는 패브릭 내에서 복제된 항목을 그룹화하는 데 사용하는 컨테이너입니다.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. 복구 패브릭에서 Site Recovery 보호 컨테이너를 만듭니다.

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. 복제 정책 만들기

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. 기본 및 복구 보호 컨테이너 간의 보호 컨테이너 매핑을 만듭니다.

보호 컨테이너 매핑은 복구 보호 컨테이너 및 복제 정책을 사용하여 기본 보호 컨테이너를 매핑합니다. 보호 컨테이너 쌍 간에 가상 머신을 복제하는 데 사용할 각 복제 정책에 대한 매핑을 하나 만듭니다.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. 장애 복구(failback)에 대한 보호 컨테이너 매핑을 만듭니다(장애 조치(failover) 후 역방향 복제).

장애 조치(failover) 후, 장애 조치된 가상 머신을 원래 Azure 지역으로 다시 되돌릴 준비가 되면 장애 복구(failback)를 수행합니다. 장애 복구를 위해 장애 조치된 가상 머신이 장애 조치된 지역에서 원래 지역으로 역방향 복제됩니다. 역방향 복제의 경우 원래 지역과 복구 지역의 역할이 바뀝니다. 이제 원래 지역이 새 복구 지역이 되고 원래 복구 지역이었던 곳이 기본 지역이 됩니다. 역방향 복제에 대한 보호 컨테이너 매핑은 원래 지역과 복구 지역의 전환된 역할을 나타냅니다.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>캐시 저장소 계정 및 대상 저장소 계정 만들기

캐시 저장소 계정은 복제되는 가상 머신과 같은 Azure 지역에 있는 표준 저장소 계정입니다. 캐시 저장소 계정은 변경 내용을 복구 Azure 지역으로 이동하기 전에 변경 내용 복제를 일시적으로 보류하는 데 사용됩니다. 가상 머신의 다른 디스크에 대한 다양한 캐시 저장소 계정을 지정하도록 선택할 수 있습니다(단, 필수는 아님).

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

관리 디스크를 사용하지 않는 가상 머신의 경우 대상 저장소 계정은 가상 머신의 디스크가 복제되는 복구 지역에 있는 저장소 계정입니다. 대상 저장소 계정은 표준 저장소 계정 또는 프리미엄 저장소 계정 중 하나일 수 있습니다. 디스크의 데이터 변경률(IO 쓰기 속도) 및 Azure Site Recovery에서 지원되는 저장소 유형에 대한 변동 제한을 기준으로 필요한 저장소 계정의 유형을 선택합니다.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>네트워크 매핑 만들기

네트워크 매핑은 기본 지역의 가상 네트워크를 복구 지역의 가상 네트워크에 연결합니다. 네트워크 매핑은 기본 가상 네트워크의 가상 머신을 장애 조치(failover)해야 하는 복구 지역에서 Azure Virtual Network를 지정합니다. 하나의 Azure Virtual Network는 복구 지역의 단일 Azure Virtual Network에만 매핑할 수 있습니다.

- 장애 조치(failover)할 복구 지역에 Azure Virtual Network 만들기

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- 기본 가상 네트워크(가상 머신이 연결된 vnet) 검색
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- 기본 가상 네트워크와 복구 가상 네트워크 간에 네트워크 매핑 만들기
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- 역방향에 대한 네트워크 매핑 만들기(장애 복구(failback))
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure Virtual Machine 복제

Azure Virtual Machine을 복제합니다.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

복제 시작 작업이 성공하면 가상 머신 데이터가 복구 지역으로 복제됩니다. 

복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 초기 복제 단계라고 합니다. 

초기 복제가 완료되면 복제는 차등 동기화 단계로 이동합니다. 이 시점에서 가상 머신이 보호되고 테스트 장애 조치(failover) 작업을 수행할 수 있습니다. 초기 복제가 완료된 후 가상 머신을 나타내는 복제된 항목의 복제 상태는 “보호됨” 상태로 변경됩니다.

해당하는 복제 보호된 항목의 세부 정보를 가져와서 복제 상태 및 가상 머신의 복제 상태를 모니터링합니다.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>테스트 장애 조치(failover), 유효성 검사 및 테스트 장애 조치(failover) 정리 수행

가상 머신에 대한 복제가 보호된 상태가 되면 테스트 장애 조치(failover) 작업을 가상 머신에서(가상 머신의 복제 보호된 항목에서) 수행할 수 있습니다

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

테스트 장애 조치(failover)를 수행합니다.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

테스트 장애 조치(failover) 작업이 완료될 때까지 기다립니다.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
테스트 장애 조치(failover) 작업이 성공적으로 완료되면 테스트 장애 조치(failover)된 가상 머신에 연결하려 테스트 장애 조치(failover)의 유효성을 검사할 수 있습니다.

테스트 장애 조치(failover)된 가상 머신에서 테스트가 완료되면 테스트 장애 조치(failover) 정리 작업을 시작하여 테스트 복사본을 정리합니다. 이 작업은 테스트 장애 조치(failover)로 만들어진 가상 머신의 테스트 복사본을 삭제합니다.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Azure에 장애 조치

가상 머신을 특정 복구 지점으로 장애 조치(failover)합니다.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

장애 조치(failover)가 성공적으로 완료되면 장애 조치(failover) 작업을 커밋할 수 있습니다.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

장애 조치(failover) 후에 원래 지역으로 돌아갈 준비가 되면 Update-AzureRmRecoveryServicesAsrProtectionDirection cmdlet을 사용하여 복제 보호된 항목에 대한 역방향 복제를 시작합니다.

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery PowerShell 참조](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery)에서 PowerShell을 통한 복구 계획 만들기 및 복구 계획의 장애 조치(failover) 테스트 등과 같은 다른 작업을 수행하는 방법을 알아봅니다.
