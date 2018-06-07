---
title: SQL Server 2016/2017 Azure VM의 자동화된 백업 v2 | Microsoft Docs
description: Azure에서 실행되는 SQL Server 2016/2017 VM의 자동화된 백업 기능에 대해 설명합니다. 이 문서는 Resource Manager를 사용하는 VMs에만 적용됩니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 4619c26e34c90f58702ad286f76a999f83f49cc4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894512"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines의 자동화된 백업 v2(Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

자동화된 백업 v2에서는 SQL Server 2016/2017 Standard, Enterprise 또는 Developer 버전을 실행하는 Azure VM에서 모든 기존 및 새 데이터베이스에 대해 [Microsoft Azure에 대한 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx)을 자동으로 구성합니다. 이를 통해 지속형 Azure Blob 저장소를 활용하는 일반 데이터베이스 백업을 구성할 수 있습니다. 자동화된 Backup v2는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)에 따라 다릅니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>필수 조건
자동화된 Backup v2를 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server 버전**:

- SQL Server 2016: Developer, Standard 또는 Enterprise
- SQL Server 2017: Developer, Standard 또는 Enterprise

> [!IMPORTANT]
> 자동화된 백업 v2는 SQL Server 2016 이상에서 작동합니다. SQL Server 2014를 사용하는 경우 자동화된 Backup v1을 사용하여 데이터베이스를 백업할 수 있습니다. 자세한 내용은 [SQL Server 2014 Azure Virtual Machines의 자동화된 Backup](virtual-machines-windows-sql-automated-backup.md)을 참조하세요.

**데이터베이스 구성**:

- 대상 데이터베이스는 전체 복구 모델을 사용해야 합니다. 전체 복구 모델이 백업에 미치는 영향에 대한 자세한 내용은 [전체 복구 모델에서 Backup](https://technet.microsoft.com/library/ms190217.aspx)을 참조하세요.
- 시스템 데이터베이스는 전체 복구 모델을 사용할 필요가 없습니다. 그러나 Model 또는 MSDB에 대해 로그 백업을 수행해야 할 경우 전체 복구 모델을 사용해야 합니다.
- 대상 데이터베이스는 기본 SQL Server 인스턴스에 있어야 합니다. SQL Server IaaS 확장은 명명된 인스턴스를 지원하지 않습니다.

> [!NOTE]
> 자동화된 Backup은 **SQL Server IaaS 에이전트 확장**에 의존합니다. 현재 SQL 가상 머신 갤러리 이미지는 기본적으로 이 확장을 추가합니다. 자세한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

## <a name="settings"></a>설정
다음 표에서는 자동화된 Backup v2에 대해 구성할 수 있는 옵션을 설명합니다. 실제 구성 단계는 Azure 포털 또는 Azure Windows PowerShell 명령 사용 여부에 따라 달라집니다.

### <a name="basic-settings"></a>기본 설정

| 설정 | 범위(기본값) | 설명 |
| --- | --- | --- |
| **자동화된 Backup** | 사용/사용 안 함(사용 안 함) | SQL Server 2016/2017 Developer, Standard 또는 Enterprise를 실행하는 Azure VM에 대해 자동화된 백업을 사용하거나 사용하지 않도록 설정합니다. |
| **보존 기간** | 1-30일(30일) | 백업 보존 기간(일 수)입니다. |
| **Storage 계정** | Azure 저장소 계정 | Blob 저장소에 자동화된 Backup 파일을 저장하기 위해 사용하여 Azure 저장소 계정입니다. 모든 백업 파일을 저장하려면 컨테이너를 이 위치에 만듭니다. 백업 파일 명명 규칙에는 날짜, 시간 및 데이터베이스 GUID가 포함됩니다. |
| **암호화** |사용/사용 안 함(사용 안 함) | 암호화 사용 여부를 설정합니다. 암호화가 활성화되면 백업을 복원하는 데 사용된 인증서는 지정된 저장소 계정에 있습니다. 동일한 이름 지정 규칙을 사용하여 동일한 **automaticbackup** 컨테이너를 사용합니다. 암호가 변경되면 해당 암호를 사용하여 새 인증서가 생성되지만 이전 인증서도 이전 백업의 복원을 위해 유지됩니다. |
| **암호** |암호 텍스트 | 암호화 키의 암호입니다. 이 암호는 암호화를 사용하는 경우에만 필요합니다. 암호화된 백업을 복원하기 위해서는 올바른 암호 및 백업을 수행할 때 사용한 인증서가 있어야 합니다. |

### <a name="advanced-settings"></a>고급 설정

| 설정 | 범위(기본값) | 설명 |
| --- | --- | --- |
| **시스템 데이터베이스 Backup** | 사용/사용 안 함(사용 안 함) | 사용하도록 설정하면 이 기능은 시스템 데이터베이스인 Master, MSDB 및 Model도 백업합니다. MSDB 및 Model 데이터베이스의 경우 로그 백업이 수행되도록 하려면 전체 복구 모드인지 확인합니다. Master의 경우에는 로그 백업이 수행되지 않습니다. 또한 TempDB에 대해서도 백업이 수행되지 않습니다. |
| **Backup 일정** | 수동/자동(자동) | 기본적으로 백업 일정은 로그 증가에 따라 자동으로 결정됩니다. 수동 백업 일정을 사용하면 백업에 대한 기간을 지정할 수 있습니다. 이 경우 백업은 지정된 빈도로, 지정된 날의 지정된 기간 동안에만 수행됩니다. |
| **전체 백업 빈도** | 매일/매주 | 전체 백업의 빈도입니다. 두 경우 모두 전체 백업은 예약된 다음 기간 동안 시작됩니다. 매주 옵션을 선택하면 백업은 모든 데이터베이스가 성공적으로 백업될 때까지 여러 날에 걸쳐 수행될 수 있습니다. |
| **전체 백업 시작 시간** | 00:00 – 23:00(01:00) | 전체 백업이 수행될 수 있는 지정된 날의 시작 시간입니다. |
| **전체 백업 시간 기간** | 1-23시간(1시간) | 전체 백업이 수행될 수 있는 지정된 날의 시간 기간입니다. |
| **로그 백업 빈도** | 5-60분(60분) | 로그 백업의 빈도입니다. |

## <a name="understanding-full-backup-frequency"></a>전체 백업 빈도 이해
매일 및 매주 전체 백업 간 차이를 이해하는 것은 중요합니다. 다음 두 가지 예제 시나리오를 고려하세요.

### <a name="scenario-1-weekly-backups"></a>시나리오 1: 매주 백업
규모가 큰 데이터베이스를 많이 포함하는 SQL Server VM이 있습니다.

월요일에서 다음 설정으로 자동화된 Backup v2를 사용하도록 설정합니다.

- Backup 일정: **수동**
- 전체 백업 빈도: **매주**
- 전체 백업 시작 시간: **01:00**
- 전체 백업 시간 기간: **1시간**

즉, 사용 가능한 다음 백업 기간은 화요일 오전 1시부터 1시간 동안입니다. 해당 시간에 자동화된 Backup은 한 번에 하나씩 데이터베이스를 백업하기 시작합니다. 이 시나리오에서는 처음 두 데이터베이스에 대해 전체 백업이 완료될 정도로 데이터베이스가 큽니다. 그러나 1시간 후에 모든 데이터베이스가 백업되지는 않았습니다.

이 경우 자동화된 Backup은 다음 날인 수요일에 오전 1시부터 1시간 동안 나머지 데이터베이스를 백업하기 시작합니다. 해당 시간에 모든 데이터베이스가 백업되지는 않을 경우 다음 날 같은 시간에 다시 시도됩니다. 이 과정은 모든 데이터베이스가 성공적으로 백업될 때까지 계속됩니다.

다시 화요일이 되면 자동화된 백업이 모든 데이터베이스를 다시 백업하기 시작합니다.

이 시나리오에서는 자동화된 Backup이 지정된 기간 내에만 작동하며 각 데이터베이스가 매주 한 번 백업될 것임을 보여줍니다. 또한 하루에 모든 백업을 완료할 수 없는 경우 여러 날에 걸쳐 백업이 진행될 수 있다는 사실도 보여 줍니다.

### <a name="scenario-2-daily-backups"></a>시나리오 2: 매일 백업
규모가 큰 데이터베이스를 많이 포함하는 SQL Server VM이 있습니다.

월요일에서 다음 설정으로 자동화된 Backup v2를 사용하도록 설정합니다.

- Backup 일정: 수동
- 전체 백업 빈도: 매일
- 전체 백업 시작 시간: 22:00
- 전체 백업 시간 기간: 6시간

즉, 사용 가능한 다음 백업 기간은 월요일 오후 10시부터 6시간 동안입니다. 해당 시간에 자동화된 Backup은 한 번에 하나씩 데이터베이스를 백업하기 시작합니다.

그런 다음, 화요일 오후 10부터 6시간 동안 모든 데이터베이스의 전체 백업이 다시 시작됩니다.

> [!IMPORTANT]
> 매일 백업 일정을 계획할 때는 모든 데이터베이스가 이 시간 내에 백업될 수 있도록 넓은 기간을 예약하는 것이 좋습니다. 특히 백업할 데이터양이 클 경우 이러한 점을 고려해야 합니다.

## <a name="configure-in-the-portal"></a>포털 구성

Azure Portal을 사용하여 프로비전 중에 또는 기존 SQL Server 2016/2017 VM에 대해 자동화된 백업 v2를 구성할 수 있습니다.

## <a name="configure-for-new-vms"></a>새로운 VM에 대한 구성

Azure Portal을 사용하여 Resource Manager 배포 모델에서 새 SQL Server 2016 또는 2017 Virtual Machine을 만들 때 자동화된 백업 v2를 구성합니다.

**SQL Server 설정** 창에서 **자동화된 백업**을 선택합니다. 다음 Azure Portal 스크린샷은 **SQL 자동화된 백업** 설정을 보여 줍니다.

![Azure Portal에서 SQL 자동화된 Backup 구성](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> 자동화된 Backup v2는 기본적으로 사용하지 않도록 설정됩니다.

## <a name="configure-existing-vms"></a>기존 VM 구성

기존 SQL Server 가상 머신에 대한 해당 SQL Server 가상 머신을 선택합니다. 그런 다음, VM **설정**의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL 자동화된 Backup](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

**SQL Server 구성** 설정에서 자동화된 백업 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL 자동화된 Backup 구성](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

작업을 마쳤으면 **SQL Server 구성** 설정의 맨 아래에 있는 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

처음으로 자동화된 Backup을 사용 설정할 경우 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 구성된 자동화된 Backup이 Azure Portal에 표시되지 않을 수 있습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 Azure Portal에는 새 설정이 반영됩니다.

## <a name="configure-with-powershell"></a>PowerShell로 구성

PowerShell을 사용하여 자동화된 Backup v2를 구성할 수도 있습니다. 시작하기 전에 다음을 수행해야 합니다.

- [최신 Azure PowerShell을 다운로드하여 설치합니다](http://aka.ms/webpi-azps).
- Windows PowerShell을 열고 **Connect-AzureRmAccount** 명령을 사용하여 계정에 연결합니다.

### <a name="install-the-sql-iaas-extension"></a>SQL IaaS 확장 설치
Azure Portal에서 SQL Server 가상 머신을 프로비전한 경우 SQL Server IaaS 확장이 이미 설치되어 있을 것입니다. **Get-AzureRmVM** 명령을 실행하고 **Extensions** 속성을 검사하여 VM에 대해 해당 확장이 설치되어 있는지 확인할 수 있습니다.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

SQL Server IaaS 에이전트 확장이 설치되어 있는 경우 "SqlIaaSAgent" 또는 "SQLIaaSExtension"으로 표시됩니다. 확장에 대한 **ProvisioningState**가 "Succeeded"로 표시되어야 합니다. 

설치되지 않았거나 프로비전되지 못한 경우 다음 명령을 사용하여 설치할 수 있습니다. VM 이름 및 리소스 그룹 외에, VM이 있는 하위 지역(**$region**)도 지정해야 합니다.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> 현재 설정 확인
프로비전 동안 자동화된 백업을 사용하도록 설정한 경우 PowerShell을 사용하여 현재 구성을 확인할 수 있습니다. **Get-AzureRmVMSqlServerExtension** 명령을 실행하고 **AutoBackupSettings** 속성을 검사합니다.

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

다음과 유사한 결과가 표시됩니다.

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

출력에 **Enable**이 **False**로 설정되어 표시되면 자동화된 백업을 사용하도록 설정해야 합니다. 다행히도 자동화된 Backup도 같은 방식으로 사용하도록 설정하고 구성할 수 있습니다. 이 정보에 대해서는 다음 섹션을 참조하세요.

> [!NOTE] 
> 변경을 수행한 후에 설정을 바로 확인하면 이전 구성 값을 다시 가져올 수 있습니다. 몇 분 정도 기다렸다가 설정을 다시 확인하여 변경 내용이 적용되었는지 알아봅니다.

### <a name="configure-automated-backup-v2"></a>자동화된 Backup v2 구성
언제든지 PowerShell을 사용하여 자동화된 Backup을 사용하도록 설정하고 해당 구성 및 동작을 수정할 수 있습니다. 

먼저 백업 파일에 대한 저장소 계정을 선택하거나 만듭니다. 다음 스크립트는 저장소 계정을 선택하거나 없으면 새로 만듭니다.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> 자동화된 백업을 사용할 경우 Premium Storage에 백업을 저장할 수 없지만 Premium Storage를 사용하는 VM 디스크에서 백업을 가져올 수 있습니다.

그런 후 **New-AzureRmVMSqlServerAutoBackupConfig** 명령을 통해 자동화된 백업 v2 설정을 사용하도록 지정하고 Azure Storage 계정에 백업을 저장합니다. 이 예제에서는 백업이 10일 동안 보관되도록 설정되어 있습니다. 시스템 데이터베이스 백업이 사용되도록 설정됩니다. 전체 백업은 20시부터 시작해서 2시간 동안 진행되는 것으로 매주 예약되어 있습니다. 로그 백업은 30분 간격으로 예약됩니다. 두 번째 명령인 **Set-AzureRmVMSqlServerExtension**은 지정된 Azure VM을 이러한 설정으로 업데이트합니다.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다. 

암호화를 사용하려면 **CertificatePassword** 매개 변수에 대 한 암호(보안 문자열)와 함께 **EnableEncryption** 매개 변수를 전달 하도록 이전 스크립트를 수정합니다. 다음 스크립트를 사용하면 이전 예제의 자동화된 Backup 설정을 사용하고 암호화를 추가할 수 있습니다.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

설정이 적용되었는지 확인하려면 [자동화된 Backup 구성을 확인](#verifysettings)합니다.

### <a name="disable-automated-backup"></a>자동화된 Backup 사용 안 함
자동화된 백업을 사용하지 않도록 설정하려면 동일한 스크립트를 **-Enable** 매개 변수 없이 **New-AzureRmVMSqlServerAutoBackupConfig** 명령에 대해 실행합니다. **-Enable** 매개 변수가 없는 경우 기능을 해제하는 명령을 신호로 보냅니다. 설치와 마찬가지로 자동화된 Backup을 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>예제 스크립트
다음 스크립트는 VM에 대해 자동화된 Backup을 사용하도록 설정하고 구성하기 위해 사용자 지정할 수 있는 변수 집합을 제공합니다. 사용자의 경우 요구 사항에 따라 스크립트를 사용자 지정해야 할 수 있습니다. 예를 들어 시스템 데이터베이스의 백업을 사용하지 않도록 설정하거나 암호화를 사용하도록 설정하려는 경우 변경해야 할 수 있습니다.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>모니터링

SQL Server 2016/2017에서 자동화된 백업을 모니터링하려면 두 가지 기본 옵션이 있습니다. 자동화된 백업은 SQL Server 관리 백업 기능을 사용하므로 동일한 모니터링 기술이 둘 다에 적용됩니다.

먼저 [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)를 호출하여 상태를 폴링할 수 있습니다. 또는 [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) 테이블 값 함수를 쿼리합니다.

다른 옵션은 기본 제공 데이터베이스 메일 기능을 알림에 활용하는 것입니다.

1. [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) 저장 프로시저를 호출하여 **SSMBackup2WANotificationEmailIds** 매개 변수에 이메일 주소를 할당합니다. 
1. [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md)가 Azure VM에서 이메일을 보낼 수 있도록 설정합니다.
1. SMTP 서버 및 사용자 이름을 사용하여 데이터베이스 메일을 구성합니다. SQL Server Management Studio 또는 Transact-SQL 명령을 통해 데이터베이스 메일을 구성할 수 있습니다. 자세한 내용은 [데이터베이스 메일](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)을 참조하세요.
1. [데이터베이스 메일을 사용하도록 SQL Server 에이전트를 구성](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)합니다.
1. 로컬 VM 방화벽과 VM에 대한 네트워크 보안 그룹 둘 다에서 SMTP 포트가 허용되는지 확인합니다.

## <a name="next-steps"></a>다음 단계
자동화된 Backup v2는 Azure VM에서 관리되는 Backup을 구성합니다. 따라서 [관리되는 Backup 설명서를 검토](https://msdn.microsoft.com/library/dn449496.aspx) 하여 동작 및 의미를 이해해야 합니다.

Azure VM의 SQL Server에 대한 추가적인 백업 및 복원 지침은 [Azure Virtual Machines의 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md) 문서를 참조하세요.

사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

