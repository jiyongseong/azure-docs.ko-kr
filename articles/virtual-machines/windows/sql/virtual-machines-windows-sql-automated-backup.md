---
title: SQL Server 2014 Azure Virtual Machines의 자동화된 Backup | Microsoft Docs
description: Azure에서 실행되는 SQL Server 2014 VMs의 자동화된 Backup 기능에 대해 설명합니다. 이 문서는 Resource Manager를 사용하는 VMs에만 적용됩니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 43ce94653197933a13830003dd07e5b21be2a585
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895009"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 Virtual Machines의 자동화된 Backup(Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

자동화된 Backup에서는 SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM의 모든 기존 및 새 데이터베이스에 대해 [Microsoft Azure에 대한 관리되는 Backup](https://msdn.microsoft.com/library/dn449496.aspx) 을 자동으로 구성합니다. 이를 통해 지속형 Azure Blob 저장소를 활용하는 일반 데이터베이스 백업을 구성할 수 있습니다. 자동화된 Backup은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)에 따라 다릅니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>필수 조건
자동화된 Backup을 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server 버전**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> 자동화된 Backup은 SQL Server 2014에서 작동합니다. SQL Server 2016/2017을 사용하는 경우 자동화된 Backup v2를 사용하여 데이터베이스를 백업할 수 있습니다. 자세한 내용은 [SQL Server 2016 Virtual Machines의 자동화된 Backup v2](virtual-machines-windows-sql-automated-backup-v2.md)를 참조하세요.

**데이터베이스 구성**:

- 대상 데이터베이스는 전체 복구 모델을 사용해야 합니다. 전체 복구 모델이 백업에 미치는 영향에 대한 자세한 내용은 [전체 복구 모델에서 Backup](https://technet.microsoft.com/library/ms190217.aspx)을 참조하세요.
- 대상 데이터베이스는 기본 SQL Server 인스턴스에 있어야 합니다. SQL Server IaaS 확장은 명명된 인스턴스를 지원하지 않습니다.

> [!NOTE]
> 자동화된 Backup은 SQL Server IaaS 에이전트 확장에 의존합니다. 현재 SQL 가상 컴퓨터 갤러리 이미지는 기본적으로 이 확장을 추가합니다. 자세한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

## <a name="settings"></a>설정

다음 표에서는 자동화된 Backup에 대해 구성할 수 있는 옵션을 설명합니다. 실제 구성 단계는 Azure 포털 또는 Azure Windows PowerShell 명령 사용 여부에 따라 달라집니다.

| 설정 | 범위(기본값) | 설명 |
| --- | --- | --- |
| **자동화된 Backup** | 사용/사용 안 함(사용 안 함) | SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM에 대해 자동화된 Backup을 사용하거나 사용하지 않도록 설정합니다. |
| **보존 기간** | 1-30일(30일) | 백업 보존 기간(일 수)입니다. |
| **Storage 계정** | Azure 저장소 계정 | Blob 저장소에 자동화된 Backup 파일을 저장하기 위해 사용하여 Azure 저장소 계정입니다. 모든 백업 파일을 저장하려면 컨테이너를 이 위치에 만듭니다. 백업 파일 명명 규칙에는 날짜, 시간 및 컴퓨터 이름이 포함됩니다. |
| **암호화** | 사용/사용 안 함(사용 안 함) | 암호화 사용 여부를 설정합니다. 암호화를 사용하면 백업을 복원하는 데 사용되는 인증서가 동일한 명명 규칙을 사용하여 동일한 `automaticbackup` 컨테이너에 지정한 저장소 계정에 배치됩니다. 암호가 변경되면 해당 암호를 사용하여 새 인증서가 생성되지만 이전 인증서도 이전 백업의 복원을 위해 유지됩니다. |
| **암호** | 암호 텍스트 | 암호화 키의 암호입니다. 암호화를 사용하는 경우에만 필요합니다. 암호화된 백업을 복원하기 위해서는 올바른 암호 및 백업을 수행할 때 사용한 인증서가 있어야 합니다. |

## <a name="configure-in-the-portal"></a>포털 구성

Azure Portal을 사용하여 프로비전 중에 또는 기존 SQL Server 2014 VMs에 대해 자동화된 Backup을 구성할 수 있습니다.

## <a name="configure-new-vms"></a>새 VM 구성

Azure Portal을 사용하여 Resource Manager 배포 모델에서 새 SQL Server 2014 Virtual Machine을 만들 때 자동화된 Backup을 구성합니다.

**SQL Server 설정** 창에서 **자동화된 백업**을 선택합니다. 다음 Azure Portal 스크린샷은 **SQL 자동화된 백업** 설정을 보여 줍니다.

![Azure Portal에서 SQL 자동화된 Backup 구성](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>기존 VM 구성

기존 SQL Server 가상 머신에 대한 해당 SQL Server 가상 머신을 선택합니다. 그런 다음, VM **설정**의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL 자동화된 Backup](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

**SQL Server 구성** 창에서 자동화된 백업 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL 자동화된 Backup 구성](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

작업을 마쳤으면 **SQL Server 구성** 설정의 맨 아래에 있는 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

처음으로 자동화된 Backup을 사용 설정할 경우 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 구성된 자동화된 Backup이 Azure Portal에 표시되지 않을 수 있습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 Azure 포털에는 새 설정이 반영됩니다.

> [!NOTE]
> 또한 템플릿을 사용하여 자동화된 Backup을 구성할 수 있습니다. 자세한 내용은 [자동화된 Backup에 대한 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)을 참조하세요.

## <a name="configure-with-powershell"></a>PowerShell로 구성

PowerShell을 사용하여 자동화된 Backup을 구성할 수도 있습니다. 시작하기 전에 다음을 수행해야 합니다.

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
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> 확장 프로그램이 아직 설치되지 않은 경우 확장 프로그램을 설치하면 SQL Server 서비스가 다시 시작됩니다.

### <a id="verifysettings"></a> 현재 설정 확인

프로비전 동안 자동화된 백업을 사용하도록 설정한 경우 PowerShell을 사용하여 현재 구성을 확인할 수 있습니다. **Get-AzureRmVMSqlServerExtension** 명령을 실행하고 **AutoBackupSettings** 속성을 검사합니다.

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

다음과 유사한 결과가 표시됩니다.

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

출력에 **Enable**이 **False**로 설정되어 표시되면 자동화된 백업을 사용하도록 설정해야 합니다. 다행히도 자동화된 Backup도 같은 방식으로 사용하도록 설정하고 구성할 수 있습니다. 이 정보에 대해서는 다음 섹션을 참조하세요.

> [!NOTE] 
> 변경을 수행한 후에 설정을 바로 확인하면 이전 구성 값을 다시 가져올 수 있습니다. 몇 분 정도 기다렸다가 설정을 다시 확인하여 변경 내용이 적용되었는지 알아봅니다.

### <a name="configure-automated-backup"></a>자동화된 Backup 구성
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

그런 후 **New-AzureRmVMSqlServerAutoBackupConfig** 명령을 통해 자동화된 백업 설정을 사용하도록 지정하고 Azure Storage 계정에 백업을 저장합니다. 이 예제에서는 백업이 10일 동안 보관됩니다. 두 번째 명령인 **Set-AzureRmVMSqlServerExtension**은 지정된 Azure VM을 이러한 설정으로 업데이트합니다.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> SQL Server 2016 및 자동화된 백업 v2에만 적용되는 **New-AzureRmVMSqlServerAutoBackupConfig**에 대한 다른 설정이 있습니다. SQL Server 2014에서는 **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** 및 **LogBackupFrequencyInMinutes** 설정을 지원하지 않습니다. SQL Server 2014 가상 머신에서 이러한 설정을 구성하려고 하면 오류는 발생하지 않지만 설정이 적용되지 않습니다. SQL Server 2016 가상 머신에서 이러한 설정을 사용하려면 [SQL Server 2016 Azure Virtual Machines용 자동화된 Backup v2](virtual-machines-windows-sql-automated-backup-v2.md)을 참조하세요.

암호화를 사용하려면 **CertificatePassword** 매개 변수에 대 한 암호(보안 문자열)와 함께 **EnableEncryption** 매개 변수를 전달 하도록 이전 스크립트를 수정합니다. 다음 스크립트를 사용하면 이전 예제의 자동화된 Backup 설정을 사용하고 암호화를 추가할 수 있습니다.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>모니터링

SQL Server 2014에서 자동화된 백업을 모니터링하려면 두 가지 기본 옵션이 있습니다. 자동화된 백업은 SQL Server 관리 백업 기능을 사용하므로 동일한 모니터링 기술이 둘 다에 적용됩니다.

먼저 [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)를 호출하여 상태를 폴링할 수 있습니다. 또는 [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) 테이블 값 함수를 쿼리합니다.

> [!NOTE]
> SQL Server 2014의 관리 백업 스키마는 **msdb.smart_admin**입니다. SQL Server 2016에서는 이 설정이 **msdb.managed_backup**으로 변경되었으며, 참조 항목에서 이 최신 스키마를 사용합니다. 그러나 SQL Server 2014의 경우 모든 관리 백업 개체에 대해 **smart_admin** 스키마를 계속 사용해야 합니다.

다른 옵션은 기본 제공 데이터베이스 메일 기능을 알림에 활용하는 것입니다.

1. [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) 저장 프로시저를 호출하여 **SSMBackup2WANotificationEmailIds** 매개 변수에 메일 주소를 할당합니다. 
1. [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md)가 Azure VM에서 메일을 보낼 수 있도록 설정합니다.
1. SMTP 서버 및 사용자 이름을 사용하여 데이터베이스 메일을 구성합니다. SQL Server Management Studio 또는 Transact-SQL 명령을 통해 데이터베이스 메일을 구성할 수 있습니다. 자세한 내용은 [데이터베이스 메일](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)을 참조하세요.
1. [데이터베이스 메일을 사용하도록 SQL Server 에이전트를 구성](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)합니다.
1. 로컬 VM 방화벽과 VM에 대한 네트워크 보안 그룹 둘 다에서 SMTP 포트가 허용되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자동화된 Backup은 Azure VM에서 관리되는 Backup을 구성합니다. 따라서 [SQL Server 2014의 관리 백업에 대한 설명서를 검토](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)하는 것이 중요합니다.

Azure VM의 SQL Server에 대한 추가적인 백업 및 복원 지침은 [Azure Virtual Machines의 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md) 문서를 참조하세요.

사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.
