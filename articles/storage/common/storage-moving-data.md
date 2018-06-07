---
title: "Azure에서 클라우드 저장소 내부/외부로 대량의 데이터 이동 | Microsoft Docs"
description: "Azure Storage 내부/외부로 데이터를 이동하는 다양한 방법에 대한 개요입니다."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: 980e4675c2d2e88716a3133abb027988aecd538f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="moving-data-to-and-from-azure-storage"></a>Azure Storage의 데이터 이동
온-프레미스 데이터를 Azure Storage로(또는 그 반대로) 이동하는 여러 방법이 있습니다. 가장 적합한 방법은 시나리오에 따라 달라집니다. 이 문서에서는 다양한 시나리오 그리고 각 시나리오에 적합한 방법을 신속하게 살펴보겠습니다.

## <a name="building-applications"></a>응용 프로그램 빌드
응용 프로그램을 빌드하는 경우 REST API 또는 Microsoft의 여러 클라이언트 라이브러리 중 하나에 대해 개발하면 Azure Storage의 데이터를 쉽게 이동할 수 있습니다.

Azure Storage는 .NET, iOS, Java, Android, UWP(Universal Windows Platform), Xamarin, C++, Node.JS, PHP, Ruby 및 Python에 대한 풍부한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

자세한 내용은 [Azure Blob Storage 시작](../blobs/storage-dotnet-how-to-use-blobs.md) 을 참조하세요.

또한 Azure 내부/외부로 데이터를 복사할 때 고성능을 보장하도록 설계된 라이브러리인 [Azure Storage 데이터 이동 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) 도 제공됩니다. 자세한 내용은 데이터 이동 라이브러리 [설명서](https://github.com/Azure/azure-storage-net-data-movement) 를 참조하세요. 

## <a name="quickly-viewinginteracting-with-your-data"></a>신속하게 데이터 보기/상호 작용
간편하게 Azure Storage 데이터를 살펴보고 데이터를 업로드 및 다운로드할 수 있는 기능을 원하신다면 Azure Storage 탐색기를 사용해 보세요.

자세한 내용은 [Azure Storage 탐색기](../storage-explorers.md) 목록을 확인하세요.

## <a name="system-administration"></a>시스템 관리
시스템 관리자처럼 명령줄 유틸리티가 필요하거나 더 편한 분들은 다음 옵션을 고려해 보세요.

### <a name="azcopy"></a>AzCopy
AzCopy는 Azure Storage의 데이터를 고속으로 복사하기 위해 설계된 명령줄 유틸리티입니다. 저장소 계정 내에서 또는 여러 저장소 계정 간에 데이터를 복사할 수도 있습니다. AzCopy는 [Windows](storage-use-azcopy.md) 및 [Linux](storage-use-azcopy-linux.md)에서 사용할 수 있습니다.

자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md) 또는 [Linux에서 AzCopy로 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell은 Azure의 서비스를 관리하는 cmdlet을 제공하는 모듈입니다. 시스템 관리를 위해 특별히 설계된 작업 기반 명령줄 셸 및 스크립트 언어입니다.

자세한 내용은 [Azure Storage와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md) 을 참조하세요.

### <a name="azure-cli"></a>Azure CLI
Azure CLI는 Azure 서비스 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. Azure CLI는 Windows, OSX 및 Linux에서 사용할 수 있습니다.

자세한 내용은 [Azure Storage에서 Azure CLI 사용](../storage-azure-cli.md) 을 참조하세요.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>느린 네트워크를 통해 대량의 데이터 이동
대량의 데이터 이동과 관련된 가장 큰 과제 중 하나는 전송 시간입니다. 네트워크 비용에 대한 걱정 없이 또는 코드 작성 없이 Azure Storage의/로 데이터를 가져오려면 Azure Import/Export가 적절한 솔루션입니다.

자세한 내용은 [Azure Import/Export](../storage-import-export-service.md) 를 참조하세요.

## <a name="backing-up-your-data"></a>데이터 백업
Azure Storage에 데이터를 백업해야 하는 경우 Azure Backup을 사용하면 됩니다. Azure 백업은 온-프레미스 데이터 및 Azure VM을 백업하는 강력한 솔루션입니다.

자세한 내용은 [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) 을 참조하세요.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>온-프레미스 및 클라우드 데이터에 액세스
온-프레미스 및 클라우드 데이터에 액세스하기 위한 솔루션이 필요한 경우 Azure의 하이브리드 클라우드 저장소 솔루션인 StorSimple을 고려해 보세요. 이 솔루션은 자주 사용되는 데이터는 SSD에, 가끔 사용되는 데이터는 HDD에, 비활성/백업/보관 데이터는 Azure Storage에 지능적으로 저장하는 물리적 StorSimple 장치로 구성됩니다.

자세한 내용은 [StorSimple](../../storsimple/storsimple-overview.md) 을 참조하세요.

## <a name="recovering-your-data"></a>데이터 복구
온-프레미스 워크로드 및 응용 프로그램이 있는 경우 재해가 발생하더라도 비즈니스를 계속 실행할 수 있는 솔루션이 필요합니다. Azure Site Recovery는 가상 머신 및 실제 서버의 복제, 장애 조치 및 복구를 처리합니다. 복제된 데이터는 Azure Storage에 저장되므로 보조 현장 데이터 센터가 필요 없습니다.

자세한 내용은 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 를 참조하세요.
### <a name="moving-data-faq"></a>데이터 이동 FAQ:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>VHD를 복사하지 않고 한 지역에서 다른 지역으로 마이그레이션할 수 있나요?
지역 간에 VHD를 복사하는 유일한 방법은 각 지역의 저장소 계정 간에 데이터를 복사하는 것입니다. 이 작업에는 AZCopy를 사용할 수 있습니다. 자세한 내용은 AzCopy 명령줄 유틸리티로 데이터 전송을 참조하세요. 대용량 데이터의 경우 Azure Import/Export를 사용할 수도 있습니다. 자세한 내용은 [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) 를 참조하세요.
