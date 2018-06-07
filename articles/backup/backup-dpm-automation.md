---
title: 'Azure Backup: PowerShell을 사용하여 DPM 워크로드 백업 | Microsoft Docs'
description: PowerShell을 사용하여 DPM(Data Protection Manager)에 대해 Azure Backup을 배포 및 관리하는 방법을 알아봅니다.
services: backup
documentationcenter: ''
author: NKolli1
manager: shreeshd
editor: ''
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 89dd965208cd473e47de9e0c9bdbfa3ab986c3d5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>PowerShell을 사용하여 DPM(Data Protection Manager) 서버용 Azure 백업 배포 및 관리
이 문서에서는 PowerShell을 사용하여 DPM 서버에서 Azure Backup을 설정하고 백업과 복원을 관리하는 방법을 보여 줍니다.

## <a name="setting-up-the-powershell-environment"></a>PowerShell 환경 설정
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

PowerShell을 사용하여 Data Protection Manager에서 Azure로의 백업을 관리하기 전에 PowerShell에서 적합한 환경을 설정해야 합니다. PowerShell 세션 시작 시 다음 명령을 실행하여 정확한 모듈을 가져오고 DPM cmdlet을 올바르게 참조할 수 있습니다.

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>설정 및 등록
시작하려면

1. [최신 PowerShell을 다운로드](https://github.com/Azure/azure-powershell/releases) 합니다(필요한 최소 버전: 1.0.0).
2. 다음과 같은 *Switch-azuremode* commandlet을 통해 **AzureResourceManager** 모드로 전환하여 Azure Backup commandlet을 사용하도록 설정합니다.

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell로 다음과 같은 설정 및 등록 작업을 자동화할 수 있습니다.

* Recovery Services 자격 증명 모음 만들기
* Azure Backup 에이전트 설치
* Azure Backup 서비스 등록
* 네트워킹 서비스
* 암호화 설정

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기
다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다. Recovery Services 자격 증명 모음은 Backup 자격 증명 모음과 다릅니다.

1. 처음으로 Azure Backup을 사용하는 경우 **Register-AzureRMResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록해야 합니다.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Recovery Services 자격 증명 모음은 ARM 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. **New-AzureRmRecoveryServicesVault** cmdlet을 사용하여 새 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md) 또는 [GRS(지역 중복 저장소)](../storage/common/storage-redundancy-grs.md) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testVault에 대한 BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기
**Get-AzureRmRecoveryServicesVault** 를 사용하여 현재 구독의 모든 자격 증명 모음 목록을 볼 수 있습니다. 이 명령을 사용하여 새 자격 증명 모음이 만들어졌는지 확인하거나 구독에서 사용할 수 있는 자격 증명 모음을 확인할 수 있습니다.

Get-AzureRmRecoveryServicesVault 명령을 실행합니다. 그러면 구독의 모든 자격 증명 모음이 나열됩니다.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>DPM 서버에 Azure Backup 에이전트 설치
Azure Backup 에이전트를 설치하기 전에 Windows Server에 설치 관리자를 다운로드해 두어야 합니다. 최신 버전의 설치 관리자는 [Microsoft 다운로드 센터](http://aka.ms/azurebackup_agent) 또는 Recovery Services의 자격 증명 모음 대시보드 페이지에서 다운로드할 수 있습니다. 쉽게 액세스할 수 있는 위치(예: *C:\Downloads\*)에 설치 관리자를 저장합니다.

에이전트를 설치하려면 **DPM 서버**의 승격된 PowerShell 콘솔에서 다음 명령을 실행합니다.

```
PS C:\> MARSAgentInstaller.exe /q
```

그러면 에이전트가 모두 기본 옵션으로 설치됩니다. 설치는 백그라운드에서 몇 분 정도 소요됩니다. */nu* 옵션을 지정하지 않으면 설치 마지막에 **Windows 업데이트** 창이 열리고 업데이트가 있는지 확인합니다.

설치된 프로그램 목록에 에이전트가 표시됩니다. 설치된 프로그램 목록을 보려면 **제어판** > **프로그램** > **프로그램 및 기능**으로 이동합니다.

![에이전트 설치됨](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>설치 옵션
명령줄을 통해 사용 가능한 모든 옵션을 보려면 다음 명령을 사용합니다.

```
PS C:\> MARSAgentInstaller.exe /?
```

사용 가능한 옵션은 다음과 같습니다.

| 옵션 | 세부 정보 | 기본값 |
| --- | --- | --- |
| /q |자동 설치 |- |
| /p:"위치" |Azure Backup 에이전트의 설치 폴더에 대한 경로입니다. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"위치" |Azure Backup 에이전트의 캐시 폴더에 대한 경로입니다. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft 업데이트에 옵트인 |- |
| /nu |설치가 완료된 후 업데이트에 대한 검사 안 함 |- |
| /d |Microsoft Azure Recovery Services 에이전트를 제거합니다. |- |
| /ph |프록시 호스트 주소 |- |
| /po |프록시 호스트 포트 번호 |- |
| /pu |프록시 호스트 사용자 이름 |- |
| /pw |프록시 암호 |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 DPM 등록
Recovery Services 자격 증명 모음을 만든 후에, 최신 에이전트 및 보관 자격 증명을 다운로드하여 편리한 위치(예: C:\Downloads)에 저장합니다.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

DPM 서버에서, [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet을 실행하여 컴퓨터를 자격 증명 모음에 등록합니다.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>초기 구성 설정
DPM 서버를 Recovery Services 자격 증명 모음에 등록하면 기본 구독 설정으로 시작됩니다. 이러한 구독 설정에는 네트워킹, 암호화 및 스테이징 영역이 포함됩니다. 구독 설정을 변경하려면 우선 [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) cmdlet을 사용하여 기존(기본) 설정에 대한 핸들을 가져와야 합니다.

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

[Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet을 사용하여 이 로컬 PowerShell 개체 ```$setting```에 대한 모든 수정을 수행한 다음 전체 개체를 DPM 및 Azure Backup에 커밋하여 저장합니다. ```–Commit``` 플래그를 사용하여 해당 변경 내용이 유지되도록 해야 합니다. 커밋하지 않으면 설정이 적용되지 않으며 Azure Backup에서 사용할 수 없습니다.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>네트워킹
프록시 서버를 통해 DPM 컴퓨터를 인터넷상의 Azure Backup 서비스에 연결하는 경우 백업에 성공하려면 프록시 서버 설정을 제공해야 합니다. 이 작업은 ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` 및 ```ProxyPassword``` 매개 변수를 [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet과 함께 사용하여 수행합니다. 이 예제에서는 프록시 서버가 없으므로 프록시와 관련된 모든 정보를 명시적으로 지웁니다.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

대역폭 사용 역시 주의 정해진 요일에 대해 ```-WorkHourBandwidth``` 및 ```-NonWorkHourBandwidth``` 옵션으로 제어할 수 있습니다. 이 예제에서는 제한을 설정하지 않습니다.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>스테이징 영역 구성
DPM 서버에서 실행 중인 Azure Backup 에이전트에는 클라우드로부터 복원된 데이터를 위한 임시 저장소가 필요합니다(로컬 스테이징 영역). [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet과 ```-StagingAreaPath``` 매개 변수를 사용하여 스테이징 영역을 구성합니다.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

위의 예제에서 스테이징 영역은 PowerShell 개체 ```$setting```에서 *C:\StagingArea*로 설정됩니다. 지정된 폴더가 이미 있어야 하며, 그렇지 않으면 구독 설정의 최종 커밋에 실패합니다.

### <a name="encryption-settings"></a>암호화 설정
Azure Backup에 전송되는 백업 데이터는 데이터의 기밀성을 보호하기 위해 암호화됩니다. 암호화 암호는 복원 시 데이터를 해독하기 위한 “암호"입니다. 이 정보를 설정한 후에 안전하게 보관하는 것이 중요합니다.

아래 예제에서 첫 번째 명령은 ```passphrase123456789``` 문자열을 보안 문자열로 변환하고 보안 문자열을 ```$Passphrase```(이)라는 변수에 할당합니다. 두 번째 명령은 암호화 백업의 암호로 ```$Passphrase```에서 보안 문자열을 설정합니다.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> 암호 정보를 설정한 후에는 안전하게 보관합니다. 이 암호 없이는 Azure에서 데이터를 복원할 수 없습니다.
>
>

이 시점에서 ```$setting``` 개체에 필요한 모든 사항을 변경해야 합니다. 변경 내용은 커밋합니다.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Azure Backup에 데이터 보호
이 섹션에서는 DPM에 프로덕션 서버를 추가한 후 데이터를 로컬 DPM 저장소에 보호한 다음 Azure Backup에 보호합니다. 예제에서 파일과 폴더를 백업하는 방법을 보여 줍니다. 논리는 모든 DPM 지원 데이터 소스를 백업하도록 쉽게 확장될 수 있습니다. 모든 DPM 백업은 4개 부분으로 구성된 보호 그룹(PG)에 의해 제어됩니다.

1. **그룹 멤버**는 동일한 보호 그룹 내에서 보호하려는 모든 보호 개체(DPM에서는 *데이터 원본*)의 목록입니다. 예를 들어, 백업 요구 사항이 다르기 때문에 하나의 보호 그룹에서는 프로덕션 VM을 보호하고 다른 보호 그룹에서는 SQL Server 데이터베이스를 보호할 수 있습니다. 프로덕션 서버에 데이터 원본을 백업할 수 있으려면 DPM 에이전트가 서버에 설치되어 있고 DPM에 의해 관리되는지 확인해야 합니다. [DPM 에이전트를 설치](https://technet.microsoft.com/library/bb870935.aspx)하고 해당 DPM 서버에 링크하는 단계를 따릅니다.
2. **데이터 보호 방법**은 대상 백업 위치(테이프, 디스크 및 클라우드)를 지정합니다. 이 예에서는 데이터를 로컬 디스크와 클라우드에 보호합니다.
3. 백업을 수행해야 할 때와 DPM 서버 및 프로덕션 서버 간의 데이터 동기화 빈도를 지정하는 **백업 일정**입니다.
4. Azure에 복구 지점을 보존할 기간을 지정하는 **보존 일정**입니다.

### <a name="creating-a-protection-group"></a>보호 그룹 만들기
[New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) cmdlet을 사용하여 새 보호 그룹을 만듭니다.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

위의 cmdlet은 *ProtectGroup01* 보호 그룹을 만듭니다. 나중에 기존 보호 그룹을 수정하여 Azure 클라우드에 백업을 추가할 수도 있습니다. 하지만 보호 그룹(신규 또는 기존)을 변경하려면 *Get-DPMModifiableProtectionGroup* cmdlet을 사용하여 [수정 가능한](https://technet.microsoft.com/library/hh881713) 개체에 대한 핸들을 가져와야 합니다.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>보호 그룹에 그룹 멤버 추가
각 DPM 에이전트는 설치된 서버의 데이터 원본 목록을 알고 있습니다. 데이터 원본을 보호 그룹에 추가하려면 DPM 에이전트가 먼저 DPM 서버에 데이터 원본 목록이 다시 전송해야 합니다. 그런 다음 하나 이상의 데이터 원본을 선택하여 보호 그룹에 추가합니다. 이 작업을 수행하는 데 필요한 PowerShell 단계는 다음과 같습니다.

1. DPM 에이전트를 통해 DPM에 의해 관리되는 모든 서버 목록을 가져옵니다.
2. 특정 서버를 선택합니다.
3. 서버의 모든 데이터 원본 목록을 가져옵니다.
4. 하나 이상의 데이터 원본을 선택하고 보호 그룹에 추가

DPM 에이전트가 설치되어 있고 DPM 서버에 의해 관리되고 있는 서버 목록은 [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) cmdlet을 사용하여 얻을 수 있습니다. 이 예제에서는 백업에 대해 이름이 *productionserver01* 인 PS만 필터링하여 구성합니다.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

이제 [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) cmdlet을 사용하여 ```$server```에서 데이터 원본 목록을 가져옵니다. 이 예제에서는 백업을 위해 구성하려는 *D:\* 볼륨을 필터링합니다. 그런 다음 이 데이터 원본은 [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) cmdlet을 사용하여 보호 그룹에 추가됩니다. 추가하려면 *수정 가능한* ```$MPG``` 보호 그룹 개체를 사용해야 합니다.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

선택한 모든 데이터 원본이 보호 그룹에 추가될 때까지 필요한 만큼 이 단계를 반복합니다. 또한 하나의 데이터 원본으로만 시작한 다음, 보호 그룹을 만들기 위한 워크플로를 완료하고 나중에 해당 보호 그룹에 더 많은 데이터 원본을 추가할 수도 있습니다.

### <a name="selecting-the-data-protection-method"></a>데이터 보호 방법 선택
데이터 원본이 보호 그룹에 추가되고 나면 다음 단계는 [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) cmdlet을 사용하여 보호 방법을 지정하는 것입니다. 이 예제에서는 보호 그룹이 로컬 디스크 및 클라우드 백업에 대한 설정됩니다. -Online 플래그와 함께 [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) cmdlet을 사용하여 클라우드에 대해 보호하려는 데이터소스를 지정해야 합니다.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>보존 범위 설정
[Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet을 사용하여 백업 시점의 보존을 설정합니다. 백업 일정을 정의하기 전에 보존을 설정하는 것이 좀 이상하게 보일 수 있지만 ```Set-DPMPolicyObjective``` cmdlet을 사용하면 기본 백업 일정이 자동으로 설정되며, 이 일정은 나중에 수정할 수 있습니다. 항상 백업 일정을 먼저 설정하고 그 후에 보존 정책을 설정할 수 있습니다.

아래 예제에서는 cmdlet이 디스크 백업에 대한 보존 매개 변수를 설정합니다. 이 값은 10일 동안 백업을 유지하고 6시간마다 프로덕션 서버와 DPM 서버 간에 데이터를 동기화합니다. ```SynchronizationFrequencyMinutes``` 는 백업 시점 생성 빈도를 정의하지 않지만 DPM 서버에 데이터를 복사하는 빈도는 정의합니다.  이 설정은 백업이 너무 커지지 않도록 방지합니다.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Azure에 백업하는 경우(DPM에서는 온라인 백업) [GFS(Grandfather-Father-Son) 체계를 사용하여 장기 보존](backup-azure-backup-cloud-as-tape.md)을 위한 보존 범위를 구성할 수 있습니다. 즉, 매일, 매주, 매월 및 매년 보존 정책이 포함된 통합 보존 정책을 정의할 수 있습니다. 이 예에서는 필요한 복잡한 보존 체계를 나타내는 배열을 만든 다음 [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet을 사용하여 보존 범위를 구성합니다.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>백업 일정 설정
```Set-DPMPolicyObjective``` cmdlet을 사용하여 보호 목표를 지정하면 DPM은 기본 백업 일정을 자동으로 설정합니다. 기본 일정을 변경하려면 [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) cmdlet을 사용한 후 [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) cmdlet을 사용합니다.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

위의 예에서 ```$onlineSch``` 는 GFS 체계에서 보호 그룹에 대한 기존 온라인 보호 일정이 포함된 4개의 요소가 있는 배열입니다.

1. ```$onlineSch[0]``` 에는 일간 일정이 포함됩니다.
2. ```$onlineSch[1]``` 에는 주간 일정이 포함됩니다.
3. ```$onlineSch[2]``` 에는 월간 일정이 포함됩니다.
4. ```$onlineSch[3]``` 에는 연간 일정이 포함됩니다.

따라서 매주 일정을 수정해야 하는 경우에는 ```$onlineSch[1]```을 참조해야 합니다.

### <a name="initial-backup"></a>초기 백업
데이터 원본을 처음으로 백업하는 경우, DPM은 DPM 복제본 볼륨에서 보호될 데이터 원본의 전체 사본을 생성하는 최초 복제본을 만들어야 합니다. 이 작업은 [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) cmdlet을 ```-NOW``` 매개 변수와 함께 사용하여 특정 시간 동안 예약하거나 수동으로 트리거할 수 있습니다.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM 복제본 및 복구 지점 볼륨 크기 변경
또한 다음 예제와 같이 [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) cmdlet을 사용하여 DPM 복제본 볼륨 및 섀도 복사본 볼륨의 크기를 변경할 수 있습니다. Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>보호 그룹에 변경 내용 커밋
마지막으로, 변경 내용을 DPM이 새로운 보호 그룹 구성에 따라 백업을 수행하기 전에 먼저 변경 내용을 커밋해야 합니다. 이 작업은 [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) cmdlet을 사용하여 수행할 수 있습니다.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>백업 시점 보기
[Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) cmdlet을 사용하여 데이터 원본에 대한 모든 복구 지점 목록을 가져올 수 있습니다. 이 예제에서는 다음을 수행합니다.

* DPM 서버의 모든 PG를 가져오고 ```$PG```
* ```$PG[0]```
* 데이터 원본에 대한 모든 복구 지점을 가져옵니다.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Azure에 보호된 데이터 복원
데이터 복원은 ```RecoverableItem``` 개체와 ```RecoveryOption``` 개체의 조합입니다. 이전 섹션에서 데이터 원본의 백업 시점 목록을 가져왔습니다.

아래 예제에서는 백업 시점과 복구 대상을 결합하여 Hyper-V 가상 머신을 Azure Backup에서 복원하는 방법을 설명합니다. 이 예제에는 다음이 포함됩니다.

* [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) cmdlet을 사용하여 복구 옵션 만들기
* ```Get-DPMRecoveryPoint``` cmdlet을 사용하여 백업 시점 배열 가져오기
* 복원할 백업 시점 선택

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

명령은 모든 데이터 원본 유형에 대해 쉽게 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* Azure Backup에 대한 DPM의 자세한 정보는 [DPM 백업 소개](backup-azure-dpm-introduction.md)
