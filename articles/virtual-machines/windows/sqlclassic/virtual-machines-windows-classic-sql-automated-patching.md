---
title: SQL Server VM의 자동화된 패치(클래식) | Microsoft Docs
description: 클래식 배포 모드를 사용하여 Azure에서 실행 중인 SQL Server Virtual Machines에 대한 자동화된 패치 기능에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 1da89bbf88e9f1f2105e7afab5c3fdabcecdbc64
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Azure Virtual Machines에서 SQL Server의 자동화된 패치(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [클래식](../classic/sql-automated-patching.md)
> 
> 

자동화된 패치는 SQL Server를 실행하는 Azure Virtual Machine에 대한 유지 관리 기간을 설정합니다. 이 유지 관리 기간 동안만 자동화된 업데이트를 설치할 수 있습니다. SQL Server의 경우 이를 통해 시스템 업데이트 및 관련 재시작 작업이 데이터베이스에 대해 가장 적절한 시간에 수행되도록 할 수 있습니다. 

> [!IMPORTANT]
> **중요**로 표시된 Windows 업데이트만 설치됩니다. 누적 업데이트 등의 다른 SQL Server 업데이트는 수동으로 설치해야 합니다. 

자동화된 패치는 [SQL Server IaaS 에이전트 확장](../classic/sql-server-agent-extension.md)에 따라 다릅니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 이 문서의 Resource Manager 버전을 보려면 [Azure Virtual Machines Resource Manager에서 SQL Server의 자동화된 패치](../sql/virtual-machines-windows-sql-automated-patching.md)를 참조하세요.

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

* [최신 Azure PowerShell cmdlet을 설치합니다](/powershell/azure/overview).

**SQL Server IaaS 확장**:

* [SQL Server IaaS 확장을 설치합니다](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>설정
다음 표에서는 자동화된 패치에 대해 구성할 수 있는 옵션을 설명합니다. 클래식 VM의 경우 이러한 설정을 구성하려면 PowerShell을 사용해야 합니다.

| 설정 | 가능한 값 | 설명 |
| --- | --- | --- |
| **자동화된 패치** |사용/사용 안 함(사용 안 함) |Azure 가상 머신에 대한 자동화된 패치를 사용 또는 사용 안 함으로 설정합니다. |
| **유지 관리 일정** |매일, 월요일, 화요일, 수요일, 목요일, 금요일, 토요일, 일요일 |가상 머신에 대한 Windows, SQL Server 및 Microsoft 업데이트 다운로드 및 설치에 대한 일정입니다. |
| **유지 관리 시작 시간** |0-24 |가상 머신을 업데이트할 로컬 시작 시간입니다. |
| **유지 관리 기간** |30-180 |업데이트 다운로드 및 설치를 완료하는데 허용된 시간(분)입니다. |
| **패치 범주** |중요 |다운로드 및 설치할 업데이트의 범주입니다. |

## <a name="configuration-with-powershell"></a>PowerShell을 사용하여 구성
다음 예제에서는 PowerShell을 사용하여 기존 SQL Server VM에 대해 자동화된 패치를 구성합니다. **New-AzureVMSqlServerAutoPatchingConfig** 명령은 자동 업데이트에 대한 새 유지 관리 기간을 구성합니다.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

이 예제를 바탕으로 다음 표에서는 대상 Azure VM에 미치는 실질적인 영향을 설명합니다.

| 매개 변수 | 결과 |
| --- | --- |
| **DayOfWeek** |매주 목요일마다 패치가 설치됩니다. |
| **MaintenanceWindowStartingHour** |오전 11시에 업데이트를 시작합니다. |
| **MaintenanceWindowsDuration** |120분 이내에 패치를 설치해야 합니다. 시작 시간을 기준으로 오후 1시까지 완료해야 합니다. |
| **PatchCategory** |이 매개 변수에 대해서는 "중요" 설정만 가능합니다. |

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

자동 패치를 사용하지 않으려면 동일한 스크립트를 New-AzureVMSqlServerAutoPatchingConfig에 대해 -Enable 매개 변수 없이 실행합니다. 설치와 마찬가지로 자동화된 패치를 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](../classic/sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

