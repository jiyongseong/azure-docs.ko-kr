---
title: SQL Server VM에 대한 자동화된 패치(리소스 관리자) | Microsoft Docs
description: 리소스 관리자를 사용하여 Azure에서 실행 중인 SQL Server Virtual Machines에 대한 자동화된 패치 기능에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 398e682db6c42bd7f4864113ddf10a6a75e2b65b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines에서 SQL Server의 자동화된 패치(리소스 관리자)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-automated-patching.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

자동화된 패치는 SQL Server를 실행하는 Azure Virtual Machine에 대한 유지 관리 기간을 설정합니다. 이 유지 관리 기간 동안만 자동화된 업데이트를 설치할 수 있습니다. SQL Server의 경우 이러한 제한을 통해 시스템 업데이트 및 관련 재시작 작업이 데이터베이스에 대해 가장 적절한 시간에 수행되도록 할 수 있습니다. 

> [!IMPORTANT]
> **중요**로 표시된 Windows 업데이트만 설치됩니다. 누적 업데이트 등의 다른 SQL Server 업데이트는 수동으로 설치해야 합니다. 

자동화된 패치는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)에 따라 다릅니다.

## <a name="prerequisites"></a>필수 조건
자동화된 패치를 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 버전**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [최신 Azure PowerShell 명령을 설치합니다](/powershell/azure/overview) .

> [!NOTE]
> 자동화된 패치는 SQL Server IaaS 에이전트 확장에 의존합니다. 현재 SQL 가상 머신 갤러리 이미지는 기본적으로 이 확장을 추가합니다. 자세한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.
> 
> 

## <a name="settings"></a>설정
다음 표에서는 자동화된 패치에 대해 구성할 수 있는 옵션을 설명합니다. 실제 구성 단계는 Azure 포털 또는 Azure Windows PowerShell 명령 사용 여부에 따라 달라집니다.

| 설정 | 가능한 값 | 설명 |
| --- | --- | --- |
| **자동화된 패치** |사용/사용 안 함(사용 안 함) |Azure 가상 머신에 대한 자동화된 패치를 사용 또는 사용 안 함으로 설정합니다. |
| **유지 관리 일정** |매일, 월요일, 화요일, 수요일, 목요일, 금요일, 토요일, 일요일 |가상 머신에 대한 Windows, SQL Server 및 Microsoft 업데이트 다운로드 및 설치에 대한 일정입니다. |
| **유지 관리 시작 시간** |0-24 |가상 머신을 업데이트할 로컬 시작 시간입니다. |
| **유지 관리 기간** |30-180 |업데이트 다운로드 및 설치를 완료하는데 허용된 시간(분)입니다. |
| **패치 범주** |중요 | 다운로드 및 설치할 Windows 업데이트의 범주입니다.|

## <a name="configuration-in-the-portal"></a>포털에서 구성
Azure 포털을 사용하여 프로비전 중에 또는 기존 VM에 대해 자동화된 패치를 구성할 수 있습니다.

### <a name="new-vms"></a>새 VM
Azure Portal을 사용하여 Resource Manager 배포 모델에서 새 SQL Server Virtual Machine을 만들 때 자동화된 패치를 구성합니다.

**SQL Server 설정** 블레이드에서 **자동화된 패치**를 선택합니다. 다음 Azure 포털 스크린샷은 **SQL 자동화된 패치** 블레이드를 보여 줍니다.

![Azure 포털에서 SQL 자동화된 패치](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

컨텍스트의 경우 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)의 전체 항목을 참조하세요.

### <a name="existing-vms"></a>기존 VM
기존 SQL Server 가상 머신에 대한 해당 SQL Server 가상 머신을 선택합니다. 그런 다음 **설정** 블레이드의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL 자동 패치](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

**SQL Server 구성** 블레이드에서 자동화된 패치 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL 자동 패치 구성](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

완료되면 **SQL Server 구성** 블레이드 아래쪽의 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

처음으로 자동화된 패치를 사용 설정할 경우 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 구성된 자동화된 패치가 Azure 포털에 표시되지 않을 수 있습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 Azure 포털에는 새 설정이 반영됩니다.

> [!NOTE]
> 또한 템플릿을 사용하여 자동화된 패치를 구성할 수 있습니다. 자세한 내용은 [자동화된 패치에 대한 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update)을 참조하세요.
> 
> 

## <a name="configuration-with-powershell"></a>PowerShell을 사용하여 구성
SQL VM을 프로비전한 후 PowerShell을 사용하여 자동화된 패치를 구성합니다.

다음 예제에서는 PowerShell을 사용하여 기존 SQL Server VM에 대해 자동화된 패치를 구성합니다. **AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig** 명령은 자동 업데이트에 대한 새 유지 관리 기간을 구성합니다.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> 확장 프로그램이 아직 설치되지 않은 경우 확장 프로그램을 설치하면 SQL Server 서비스가 다시 시작됩니다.

이 예제를 바탕으로 다음 표에서는 대상 Azure VM에 미치는 실질적인 영향을 설명합니다.

| 매개 변수 | 결과 |
| --- | --- |
| **DayOfWeek** |매주 목요일마다 패치가 설치됩니다. |
| **MaintenanceWindowStartingHour** |오전 11시에 업데이트를 시작합니다. |
| **MaintenanceWindowsDuration** |120분 이내에 패치를 설치해야 합니다. 시작 시간을 기준으로 오후 1시까지 완료해야 합니다. |
| **PatchCategory** |이 매개 변수에 대해서는 **중요**설정만 가능합니다. 이렇게 하면 중요로 표시된 Windows 업데이트가 설치되고, 이 범주에 포함되지 않는 모든 SQL Server 업데이트는 설치되지 않습니다. |

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

자동화된 패치를 사용하지 않으려면 동일한 스크립트를 **AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig**에 대해 **-Enable** 매개 변수 없이 실행합니다. **-Enable** 매개 변수가 없는 경우 기능을 해제하는 명령을 신호로 보냅니다.

## <a name="next-steps"></a>다음 단계
사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

