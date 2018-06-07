---
title: "SQL Server의 Backup 및 복원 | Microsoft Docs"
description: "Azure Virtual Machines에서 실행되는 SQL Server 데이터베이스의 백업 및 복원 시 고려 사항에 대해 설명합니다."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 16fef048e7c795f3d21fbc4185f6ba31bbc885fb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server의 백업 및 복원
## <a name="overview"></a>개요
데이터 손실이나 실제 데이터 손상에 대한 보호를 보장하기 위해 Azure Storage에는 모든 Azure VM 디스크의 복사본을 3개씩 유지 관리합니다. 따라서 온-프레미스와는 달리 이 부분에 대해서는 걱정할 필요가 없습니다. 하지만 응용 프로그램 또는 사용자 오류(예: 잘못된 데이터 포함 또는 테이블 삭제)로부터 보호하고 특정 지점으로 복원 가능하게 만들려면 SQL Server 데이터베이스를 계속 백업해야 합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM에서 실행되는 SQL Server의 경우, 백업 파일의 대상으로 연결된 디스크를 사용하는 네이티브 백업 및 복원 기법을 사용할 수 있습니다. 그러나 [가상 머신의 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 따라 Azure 가상 머신에 연결할 수 있는 디스크의 수에는 제한이 있습니다. 또한 고려해야 할 디스크 관리에 대한 오버헤드도 있습니다.

SQL Server 2014부터는 Microsoft Azure Blob 저장소에 백업 및 복원할 수 있습니다. 또한 SQL Server 2016은 이 옵션에 대한 향상된 기능을 제공합니다. 또한 Microsoft Azure Blob 저장소에 저장된 데이터베이스 파일의 경우, SQL Server 2016은 Azure 스냅숏을 사용하여 거의 즉시 백업하고 신속하게 복원하는 옵션을 제공합니다. 이 문서에서는 이러한 옵션에 대한 개요를 제공하며 자세한 내용은 [Microsoft Azure Blob Storage 서비스로 SQL Server 백업 및 복원](https://msdn.microsoft.com/library/jj919148.aspx)에서 확인할 수 있습니다.

> [!NOTE]
> 대형 데이터베이스 백업 옵션에 대한 설명은 [Azure Virtual Machines에 대한 다중 테라바이트 SQL Server 데이터베이스 백업 전략](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)을 참조하세요.
> 
> 

아래 섹션에는 Azure 가상 머신에서 지원되는 다양한 SQL Server 버전에 관련된 정보가 포함되어 있습니다.

## <a name="sql-server-virtual-machines"></a>SQL Server Virtual Machines
Azure Virtual Machine에 SQL Server 인스턴스가 실행 중이면, 데이터베이스 파일이 Azure의 데이터 디스크에 이미 상주합니다. 이 디스크는 Azure Blob 저장소에 있습니다. 따라서 데이터베이스를 백업하는 이유와 방식이 약간 변경됩니다. 다음을 고려해보세요. 

* Microsoft Azure에서 Microsoft Azure 서비스의 일부로 이 보호 기능을 제공하므로 하드웨어 또는 미디어 오류를 방지하기 위해 더 이상 데이터베이스 백업을 수행해야 할 필요가 없습니다.
* 하지만 사용자 오류를 방지하기 위한 목적이나 보관 목적, 규정상의 이유 또는 관리 목적으로는 데이터베이스 백업을 수행해야 합니다.
* 백업 파일을 Azure에 바로 저장할 수 있습니다. 자세한 내용은 다양한 버전의 SQL Server에 대한 지침을 제공하는 다음 섹션을 참조하세요.

## <a name="sql-server-2016"></a>SQL Server 2016
Microsoft SQL Server 2016은 SQL Server 2014의 [Azure Blob를 사용한 백업 및 복원](https://msdn.microsoft.com/library/jj919148.aspx) 기능을 지원합니다. 하지만 다음과 같은 향상된 기능이 포함되어 있습니다.

| 2016의 향상된 기능 | 세부 정보 |
| --- | --- |
| **스트라이프** |Microsoft Azure Blob 저장소에 백업하는 경우 SQL Server 2016에서는 대형 데이터베이스를 백업할 수 있도록 최대 12.8TB까지 여러 Blob으로의 백업을 지원합니다. |
| **스냅숏 백업** |Azure 스냅숏을 사용하는 경우 SQL Server 파일-스냅숏 Backup 기능은 Azure Blob 저장소 서비스를 사용하여 저장한 데이터베이스 파일을 거의 즉시 백업하고 신속하게 복원하는 기능을 제공합니다. 이 기능을 사용하면 백업 및 복원 정책을 단순화할 수 있습니다. 또한 파일-스냅숏 백업 기능은 특정 시점 복원도 지원합니다. 자세한 내용은 [Azure에서 데이터베이스 파일에 대한 스냅숏 Backup](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)을 참조하세요. |
| **Managed Backup 일정** |Azure에 대한 SQL Server Managed Backup에서 이제 사용자 지정 일정을 지원합니다. 자세한 내용은 [Microsoft Azure로의 SQL Server 관리되는 Backup](https://msdn.microsoft.com/library/dn449496.aspx)을 참조하세요. |

Azure Blob 저장소를 사용하는 경우 SQL Server 2016 기능에 대한 자습서는 [자습서: SQL Server 2016 데이터베이스에서 Microsoft Azure Blob 저장소 서비스 사용](https://msdn.microsoft.com/library/dn466438.aspx)을 참조하세요.

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014에는 다음과 같은 향상된 기능이 포함되어 있습니다.

1. **Azure에 Backup 및 복원**:
   
   * *URL에 대한 SQL Server Backup* 이 이제 SQL Server Management Studio에서 지원됩니다. 이제 Backup이나 복원 작업 또는 SQL Server Management Studio의 유지 관리 계획 마법사를 사용할 때 Azure에 백업하는 옵션을 사용할 수 있습니다. 자세한 내용은 [URL에 대한 SQL Server Backup](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)을 참조하세요.
   * *Azure에 대한 SQL Server 관리되는 Backup* 에는 자동화된 백업 관리를 사용할 수 있는 새로운 기능이 있습니다. 이 기능은 Azure 컴퓨터에서 실행되는 SQL Server 2014 인스턴스에 대한 백업 관리를 자동화하는 데 특히 유용합니다. 자세한 내용은 [Microsoft Azure로의 SQL Server 관리되는 Backup](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)을 참조하세요.
   * *자동화된 Backup*은 Azure에서 SQL Server VM에 대해 모든 기존 및 새로운 데이터베이스에서 *Azure에 대한 SQL Server 관리되는 Backup*을 자동으로 사용할 수 있게 해주는 추가적인 자동화 기능을 제공합니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업](virtual-machines-windows-sql-automated-backup.md)을 참조하세요.
   * Azure에 대한 모든 SQL Server 2014 백업 옵션의 개요는 [Microsoft Azure Blob Storage 서비스로 SQL Server 백업 및 복원](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)을 참조하세요.
2. **암호화**: SQL Server 2014는 백업을 만들 때 데이터를 암호화하는 기능을 지원합니다. 이 기능은 다양한 암호화 알고리즘과 인증서 또는 비대칭 키의 사용을 지원합니다. 자세한 내용은 [Backup 암호화](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx)를 참조하세요.

## <a name="sql-server-2012"></a>SQL Server 2012
SQL Server 2012에서 SQL Server Backup 및 복원에 대한 자세한 내용은 [SQL Server 데이터베이스 Backup 및 복원(SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)을 참조하세요.

SQL Server 2012 SP1 누적 업데이트 2부터는 Azure Blob Storage 서비스에 백업 및 복원할 수 있습니다. 이 기능은 Azure Virtual Machine 또는 온-프레미스 인스턴스에서 실행되는 SQL Server의 SQL Server 데이터베이스를 백업하는 데 사용할 수 있습니다. 자세한 내용은 [Azure Blob Storage 서비스로 SQL Server 백업 및 복원](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)을 참조하세요.

Azure Blob 저장소 서비스를 사용하는 이점에는 연결된 디스크에 대한 16개의 디스크 제한을 무시할 수 있는 기능, 관리 용이성, Azure 가상 머신에서 실행되는 SQL Server 인스턴스의 다른 인스턴스 또는 마이그레이션 또는 재해 복구용 온-프레미스 인스턴스에 대한 백업 파일의 직접 가용성 등에 포함됩니다. SQL Server 백업을 위해 Azure Blob Storage 서비스를 사용하는 이점의 전체 목록을 보려면 *Azure Blob Storage 서비스로 SQL Server 백업 및 복원* 의 [이점](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)섹션을 참조하세요.

권장 모범 사례 및 문제 해결 정보에 대해서는 [백업 및 복원 모범 사례(Azure Blob Storage 서비스)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)를 참조하세요.

## <a name="sql-server-2008"></a>SQL Server 2008
SQL Server 2008 R2에서 SQL Server Backup 및 복원에 대해서는 [SQL Server에서 데이터베이스 백업 및 복원(SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)을 참조하세요.

SQL Server 2008에서 SQL Server Backup 및 복원에 대해서는 [SQL Server에서 데이터베이스 백업 및 복원(SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure VM에서 SQL Server를 배포할 계획 중인 경우 [Azure Resource Manager를 사용하여 Azure에서 SQL Server Virtual Machine 프로비전](virtual-machines-windows-portal-sql-server-provision.md)자습서의 프로비저닝 참고 자료를 참조하세요.

데이터를 마이그레이션하는 데 백업 및 복원을 사용할 수 있지만 Azure VM의 SQL Server로 데이터를 마이그레이션하는 훨씬 간편한 경로가 있습니다. 마이그레이션 옵션 및 권장 사항에 대한 자세한 내용은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

그 밖에 [Azure Virtual Machines에서 SQL Server 실행과 관련된 리소스](virtual-machines-windows-sql-server-iaas-overview.md)를 검토하세요.

