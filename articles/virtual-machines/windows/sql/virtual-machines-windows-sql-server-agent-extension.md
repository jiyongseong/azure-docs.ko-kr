---
title: SQL VM에서 관리 작업 자동화(Resource Manager) | Microsoft 문서
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server 에이전트 확장을 관리하는 방법을 설명합니다. 여기에는 자동화된 Backup, 자동화된 패치 적용 및 Azure Key Vault 통합이 포함됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/20/2018
ms.author: jroth
ms.openlocfilehash: d9cb4a3bdc5776c4ac70ac376d8b839193e3fc3d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>SQL Server 에이전트 확장을 사용하여 Azure Virtual Machines에서 관리 작업 자동화(Resource Manager)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 에이전트 확장(SQLIaaSExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 설치, 상태 및 제거에 대한 지침뿐만 아니라 확장에서 지원되는 서비스의 개요를 제공합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server VM에 대한 SQL Server 에이전트 확장 클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)을 참조하세요.

## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **SQL 자동화된 Backup** |VM에 있는 SQL Server의 기본 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업(리소스 관리자)](virtual-machines-windows-sql-automated-backup.md)을 참조하세요. |
| **SQL 자동화된 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 패치(리소스 관리자)](virtual-machines-windows-sql-automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |이 서비스를 통해 SQL Server VM에서 Azure Key Vault를 자동으로 설치 및 구성할 수 있습니다. 자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성(리소스 매니저)](virtual-machines-windows-ps-sql-keyvault.md)을 참조하세요. |

SQL Server IaaS 에이전트 확장을 설치하고 실행하면 Azure Portal에 있는 가상 머신의 SQL Server 패널, SQL Server Marketplace 이미지에 대한 Azure PowerShell, 확장 수동 설치를 위한 Azure PowerShell을 통해 이러한 관리 기능을 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항:

**운영 체제**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 버전**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/overview)

## <a name="installation"></a>설치
SQL Server IaaS 에이전트 확장은 SQL Server 가상 머신 갤러리 이미지 중 하나를 프로비전할 때 자동으로 설치됩니다. 이러한 SQL Server VM 중 하나에서 확장을 수동으로 다시 설치해야 하는 경우 다음 PowerShell 명령을 사용합니다.

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> 확장 프로그램이 아직 설치되지 않은 경우 확장 프로그램을 설치하면 SQL Server 서비스가 다시 시작됩니다.

OS 전용 Windows Server 가상 머신에 SQL Server IaaS 에이전트 확장을 설치할 수도 있습니다. 이러한 방식은 해당 컴퓨터에서 SQL Server를 수동으로 설치한 경우에만 지원됩니다. 그런 후 동일한 **Set-AzureRmVMSqlServerExtension** PowerShell cmdlet을 사용하여 확장을 수동으로 설치합니다.

> [!NOTE]
> OS 전용 Windows Server VM에서 SQL Server IaaS 에이전트 확장을 수동으로 설치하는 경우 Azure Portal을 통해 SQL Server 구성 설정을 관리할 수 없습니다. 이 시나리오에서는 모든 변경 작업을 PowerShell로 수행해야 합니다.

## <a name="status"></a>상태
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 머신 창에서 **모든 설정**을 선택하고 **확장**을 클릭합니다. **SQLIaaSExtension** 확장이 나열되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzureRmVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반적인 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 Backup 및 패치에 대한 특정 상태 정보를 가져올 수도 있습니다.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>제거
Azure Portal에서 가상 머신 속성의 **확장** 창에서 줄임표를 클릭하여 확장을 제거할 수 있습니다. 그런 다음 **삭제**를 클릭합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzureRmVMSqlServerExtension** PowerShell cmdlet을 사용할 수도 있습니다.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>다음 단계
확장에 의해 지원되는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에서 참조된 문서를 참조하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

