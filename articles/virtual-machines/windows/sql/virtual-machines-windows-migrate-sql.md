---
title: VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션 | Microsoft Docs
description: 온-프레미스 사용자 데이터베이스를 Azure 가상 머신에서 SQL Server로 마이그레이션하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jroth
ms.openlocfilehash: 64245a968eca94518d2e4238b4bc5c93c952563a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Azure VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션

Azure VM에서 온-프레미스 SQL Server 사용자 데이터베이스를 SQL Server로 마이그레이션하는 메서드가 많이 있습니다. 이 문서는 다양한 방법을 간략하게 설명하고 다양한 시나리오에 대해 가장 좋은 방법을 권장합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>기본 마이그레이션 메서드란?
기본 마이그레이션 메서드는 다음과 같습니다.

* 압축을 사용하여 온-프레미스 백업을 수행하고 Azure 가상 머신에 백업 파일을 수동으로 복사
* URL에 백업을 수행하고 URL에서 Azure 가상 컴퓨터로 복원
* 데이터와 로그 파일을 분리하여 Azure Blob 저장소에 복사한 후 URL로 Azure VM의 SQL Server에 첨부
* 온-프레미스 물리적 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob 저장소에 업로드한 후 업로드된 VHD를 사용하여 새 VM으로 배포
* Windows Import/Export 서비스를 사용하여 하드 드라이브 제공
* AlwaysOn 배포 온-프레미스가 있는 경우에는 [Azure 복제본 추가 마법사](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) 를 사용하여 Azure에 복제본을 만든 다음 사용자가 Azure 데이터베이스 인스턴스에 연결되도록 하는 장애 조치(failover)를 설정합니다.
* SQL Server [트랜잭션 복제](https://msdn.microsoft.com/library/ms151176.aspx) 를 사용하여 Azure SQL Server 인스턴스를 구독자로 구성한 다음 사용자가 Azure 데이터베이스 인스턴스로 연결되도록 하여 복제를 비활성화합니다.

> [!TIP]
> Azure에서 SQL Server VM 간에 데이터베이스를 이동하기 위해 동일한 방법을 사용할 수 있습니다. 예를 들어, SQL Server 갤러리 이미지 VM을 한 버전에서 다른 버전으로 업그레이드하는 방법이 지원되지 있습니다. 이 경우에 새 버전을 사용하여 새 SQL Server VM을 만들고 이 문서에서 데이터베이스를 이동하는 마이그레이션 방법 중 하나를 사용해야 합니다. 

## <a name="choosing-your-migration-method"></a>마이그레이션 메서드 선택
데이터 전송 성능을 최적화하기 위해 압축된 백업 파일을 사용하여 Azure VM에 데이터베이스 파일을 마이그레이션합니다.

데이터베이스 마이그레이션 프로세스 동안 가동 중지 시간을 최소화하려면 AlwaysOn 옵션 또는 트랜잭션 복제 옵션을 사용합니다.

위의 메서드를 사용할 수 없는 경우 수동으로 데이터베이스를 마이그레이션합니다. 이 메서드를 사용하여 일반적으로 Azure로 데이터베이스를 백업한 복사본의 뒤에 데이터베이스 백업을 시작한 다음 데이터베이스 복원을 수행합니다. 또한 데이터베이스 파일 자체를 Azure에 복사한 다음 연결할 수 있습니다. 데이터베이스를 Azure VM에 수동으로 마이그레이션하는 프로세스를 수행하는 몇 가지 방법이 있습니다.

> [!NOTE]
> 이전 버전의 SQL Server를 SQL Server 2014 또는 SQL Server 2016으로 업그레이드하는 경우 변경이 필요할지를 고려해야 합니다. 마이그레이션 프로젝트의 일환으로 SQL Server의 새 버전에서 지원하지 않는 기능에 대한 모든 종속성을 신중하게 검토할 것을 권장합니다. 지원되는 버전 및 시나리오에 대한 자세한 내용은 [SQL Server로 업그레이드](https://msdn.microsoft.com/library/bb677622.aspx)를 참조하세요.

다음 테이블에는 기본 마이그레이션 메서드가 나열되어 있고 각 메서드를 사용하기에 가장 적합한 경우가 설명되어 있습니다.

| 방법 | 원본 데이터베이스 버전 | 대상 데이터베이스 버전 | 원본 데이터베이스 백업 크기 제약 조건 | 메모 |
| --- | --- | --- | --- | --- |
| [압축을 사용하여 온-프레미스 백업을 수행하고 Azure 가상 머신에 백업 파일을 수동으로 복사](#backup-and-restore) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | 컴퓨터 간에 데이터베이스를 이동하는 매우 간단하고 검증된 방법입니다. |
| [URL에 백업을 수행하고 URL에서 Azure 가상 머신으로 복원](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 이상 |SQL Server 2012 SP1 CU2 이상 |SQL Server 2016의 경우 12.8TB 미만, 그렇지 않은 경우 1TB 미만 | 이 방법은 Azure Storage를 사용하여 VM에 백업 파일을 이동하는 또 다른 방법입니다. |
| [데이터와 로그 파일을 분리하여 Azure Blob 저장소에 복사한 후 URL로 Azure 가상 머신의 SQL Server에 첨부](#detach-and-attach-from-url) |SQL Server 2005 이상 |SQL Server 2014 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |특히 매우 큰 데이터베이스에 대해 [Azure Blob 저장소 서비스를 사용하여 파일을 저장](https://msdn.microsoft.com/library/dn385720.aspx) 하고 Azure VM에서 실행되는 SQL Server에 연결하려는 경우 이 메서드를 사용합니다. |
| [온-프레미스 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob 저장소에 업로드한 후 업로드된 VHD를 사용하여 새 가상 컴퓨터 배포](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |[자체 SQL Server 라이선스를 소유](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)하는 경우, 이전 버전의 SQL Server에서 실행되는 데이터베이스를 마이그레이션하는 경우, 또는 다른 사용자 데이터베이스 및/또는 시스템 데이터베이스에 종속되는 데이터베이스 마이그레이션의 일부로 시스템과 사용자 데이터베이스를 함께 마이그레이션하는 경우에 사용합니다. |
| [Windows Import/Export 서비스를 사용하여 하드 드라이브 제공](#ship-hard-drive) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |매우 큰 데이터베이스에 사용하는 경우와 같이 수동 복사 메서드가 너무 느린 경우 [Windows Import/Export 서비스](../../../storage/common/storage-import-export-service.md) 를 사용 |
| [Azure Replica Wizard 추가 사용](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 이상 |SQL Server 2012 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |가동 중지 시간을 최소화하고 Always On 온-프레미스 배포가 있는 경우 사용 |
| [SQL Server 트랜잭션 복제 사용](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 저장소 제한](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |가동 중지 시간을 최소화하고 Always On 온-프레미스 배포가 없는 경우 사용 |

## <a name="backup-and-restore"></a>Backup 및 복원
압축을 사용하여 데이터베이스를 백업하고 VM에 백업을 복사한 다음 데이터베이스를 복원합니다. 백업 파일이 1TB를 초과하는 경우에는 VM 디스크의 최대 크기가 1TB이므로 백업 파일을 스트라이핑해야 합니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. 온-프레미스 위치에 전체 데이터베이스 백업을 수행합니다.
2. 원하는 버전의 SQL Server로 가상 머신을 만들거나 업로드합니다.
3. 요구 사항에 따라 연결을 설정합니다. [Azure에서 SQL Server Virtual Machine 연결(Resource Manager)](virtual-machines-windows-sql-connect.md)을 참조하세요.
4. 원격 데스크톱, Windows 탐색기 또는 명령 프롬프트의 copy 명령을 사용하여 백업 파일을 VM에 복사합니다.

## <a name="backup-to-url-and-restore"></a>URL에 Backup 및 복원
로컬 파일에 백업하는 대신 [URL에 백업](https://msdn.microsoft.com/library/dn435916.aspx)을 사용한 다음 URL에서 VM으로 복원할 수 있습니다. SQL Server 2016에 대해서는 스트라이프 백업 세트가 지원되며 성능을 위해 권장되고 Blob 당 크기 제한을 초과하는데 필요합니다. 매우 큰 데이터베이스의 경우 [Windows Import/Export 서비스](../../../storage/common/storage-import-export-service.md) 를 사용하는 것이 좋습니다.

## <a name="detach-and-attach-from-url"></a>URL에서 분리 및 연결
데이터베이스 및 로그 파일을 분리하고 [Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx)로 전송합니다. 그런 다음 Azure VM의 URL에서 데이터베이스를 연결합니다. 실제 데이터베이스 파일을 Blob Storage에 상주시키는 경우 이 방법을 사용합니다. 매우 큰 데이터베이스에 유용할 수 있습니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. 온-프레미스 데이터베이스 인스턴스에서 데이터베이스 파일을 분리합니다.
2. 분리한 데이터베이스 파일을 [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy.md)를 사용하여 Azure Blob 저장소로 복사합니다.
3. Azure URL의 데이터베이스 파일을 Azure VM에서 SQL Server 인스턴스로 연결합니다.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>VM으로 변환하고 URL에 업로드하고 새 VM으로 배포
이 메서드를 사용하여 온-프레미스 SQL Server 인스턴스의 모든 시스템과 사용자 데이터베이스를 Azure 가상 머신으로 마이그레이션합니다. 수동 메서드를 사용하여 전체 SQL Server 인스턴스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx)를 사용하여 물리적 컴퓨터 또는 가상 머신을 Hyper-V VHD로 변환합니다.
2. [Add-AzureVHD cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)을 사용하여 VHD 파일을 Azure Storage에 업로드합니다.
3. 업로드된 VHD를 사용하여 새 가상 머신을 배포합니다.

> [!NOTE]
> 전체 응용 프로그램을 마이그레이션하려면 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)사용을 고려합니다.

## <a name="ship-hard-drive"></a>하드 드라이브를 제공합니다.
네트워크를 통한 업로드가 엄청나게 많은 비용이 들거나 적합하지 않은 경우 [Windows Import/Export 서비스 메서드](../../../storage/common/storage-import-export-service.md) 를 사용하여 대량의 파일 데이터를 Azure Blob 저장소로 전송합니다. 이 서비스를 사용하여 해당 데이터가 포함된 하나 이상의 하드 드라이브를 Azure 데이터 센터로 보내서 데이터를 저장소 계정으로 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

> [!TIP]
> SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](virtual-machines-windows-sql-server-iaas-faq.md)을 참조하세요.

Azure SQL Server Virtual Machine을 만드는 방법에 대한 지침은 CSS SQL Server 엔지니어 블로그에서 [캡처한 이미지에서 Azure SQL 가상 머신을 '복제'하는 팁과 트릭](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/)을 참조하세요.

