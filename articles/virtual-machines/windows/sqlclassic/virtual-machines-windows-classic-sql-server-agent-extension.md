---
title: SQL VM에서 관리 작업 자동화(클래식) | Microsoft 문서
description: 이 항목에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server 에이전트 확장을 관리하는 방법을 설명합니다. 여기에는 자동화된 Backup, 자동화된 패치 적용 및 Azure Key Vault 통합이 포함됩니다. 이 항목에서는 클래식 배포 모드를 사용합니다.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ff9a8b91b0359c57fae5b1a01b5d895ab9a1685
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>SQL Server 에이전트 확장을 사용하여 Azure Virtual Machines에서 관리 작업 자동화(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [클래식](../classic/sql-server-agent-extension.md)
> 
>
 
관리 작업을 자동화하기 위해 Azure 가상 머신에서 SQL Server IaaS 에이전트 확장(SQLIaaSAgent)을 실행합니다. 이 항목에서는 설치, 상태 및 제거에 대한 지침뿐만 아니라 확장에 의해 지원되는 서비스의 개요를 제공합니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 이 문서의 Resource Manager 버전을 보려면 [SQL Server VM Resource Manager에 대한 SQL Server 에이전트 확장](../sql/virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **SQL 자동화된 Backup** |VM에 있는 SQL Server의 기본 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업(클래식)](../classic/sql-automated-backup.md)을 참조하세요. |
| **SQL 자동화된 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 패치(클래식)](../classic/sql-automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성(클래식)](../classic/ps-sql-keyvault.md)을 참조하세요. |

## <a name="prerequisites"></a>필수 조건
VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항:

### <a name="operating-system"></a>운영 체제:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server 버전:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/overview)

Windows PowerShell을 시작하고 **Add-AzureAccount** 명령을 사용하여 Azure 구독에 연결합니다.

    Add-AzureAccount

구독이 여러 개인 경우 **Select-AzureSubscription** 을 사용하여 대상 클래식 VM이 포함된 구독을 선택합니다.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

이때 **Get-AzureVM** 명령을 사용하여 클래식 가상 머신 및 연결된 해당 서비스 이름 목록을 가져올 수 있습니다.

    Get-AzureVM

## <a name="installation"></a>설치
클래식 VM의 경우 PowerShell을 사용하여 SQL Server IaaS 에이전트 확장을 설치하고 관련 서비스를 구성해야 합니다. **Set-AzureVMSqlServerExtension** PowerShell cmdlet을 사용하여 확장을 설치합니다. 예를 들어 다음 명령은 Windows Server VM(클래식)에 확장을 설치한 후 "SQLIaaSExtension"이라고 이름을 지정합니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

SQL IaaS 에이전트 확장의 최신 버전으로 업데이트하는 경우 확장을 업데이트한 후 가상 머신을 다시 시작해야 합니다.

> [!NOTE]
> 클래식 가상 머신에는 포털을 통해 SQL IaaS 에이전트 확장을 설치 및 구성하기 위한 옵션이 없습니다.
> 
> 

## <a name="status"></a>상태
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 머신 블레이드에서 나열된 가상 머신을 선택하고 **확장**을 클릭합니다. 목록에 **SQLIaaSAgent** 확장이 표시되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell cmdlet을 사용할 수도 있습니다.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>제거
Azure Portal에서 가상 머신 속성의 **확장** 블레이드에서 줄임표를 클릭하여 확장을 제거할 수 있습니다. 그런 후 **제거**를 클릭합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** Powershell cmdlet을 사용할 수도 있습니다.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>다음 단계
확장에 의해 지원되는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에 참조된 항목을 참조하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

