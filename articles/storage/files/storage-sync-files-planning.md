---
title: Azure 파일 동기화(미리 보기) 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: ebfa7da32859f8d2d0ff3778af3b5cca99bdf1f4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077677"
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Azure 파일 동기화(미리 보기) 배포에 대한 계획
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화합니다. Azure File Sync는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure File Sync 배포에 대한 중요 고려 사항을 설명합니다. [Azure Files 배포에 대한 계획](storage-files-planning.md)도 읽어보는 것이 좋습니다. 

## <a name="azure-file-sync-terminology"></a>Azure File Sync 용어
Azure File Sync 배포 계획을 세부적으로 알아보기 전에 용어를 이해하는 것이 중요합니다.

### <a name="storage-sync-service"></a>저장소 동기화 서비스
저장소 동기화 서비스는 Azure File Sync의 최상위 Azure 리소스입니다. Storage 동기화 서비스 리소스는 저장소 계정 리소스의 피어로, Azure 리소스 그룹에 쉽게 배포할 수 있습니다. Storage 동기화 서비스는 여러 동기화 그룹을 통해 여러 저장소 계정과 동기화 관계를 만들 수 있기 때문에 저장소 계정 리소스와는 별개의 최상위 수준 리소스가 필요합니다. 하나의 구독으로 여러 저장소 동기화 서비스 리소스가 배포될 수 있습니다.

### <a name="sync-group"></a>동기화 그룹
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure File Sync를 사용하여 관리하려는 2개의 고유한 파일 집합이 있는 경우 2개의 동기화 그룹을 만들고 각 동기화 그룹에 다른 엔드포인트를 추가합니다. 저장소 동기화 서비스는 필요한 만큼의 동기화 그룹을 호스트할 수 있습니다.  

### <a name="registered-server"></a>등록된 서버
등록된 서버 개체는 서버(또는 클러스터)와 Storage 동기화 서비스 간의 신뢰 관계를 나타냅니다. 원하는 수 만큼의 서버를 Storage 동기화 서비스 인스턴스에 등록할 수 있습니다. 그렇지만 한 번에 하나의 서버(또는 클러스터)만 하나의 Storage 동기화 서비스에 등록될 수 있습니다.

### <a name="azure-file-sync-agent"></a>Azure 파일 동기화 에이전트
Azure File Sync 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. Azure File Sync 에이전트는 다음 3가지 주요 구성 요소로 이루어집니다. 
- **FileSyncSvc.exe**: 서버 엔드포인트의 변경 내용을 모니터링하고 Azure와의 동기화 세션을 시작하는 백그라운드 Windows 서비스입니다.
- **StorageSync.sys**: Azure Files로의 파일 계층화를 담당하는 Azure File Sync 파일 시스템 필터입니다(클라우드 계층화가 사용될 경우).
- **PowerShell 관리 cmdlet**: Microsoft.StorageSync Azure 리소스 공급자와 상호 작용하는 데 사용하는 PowerShell cmdlet입니다. 다음(기본) 위치에서 이러한 항목을 찾을 수 있습니다.
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>서버 엔드포인트
서버 끝점은 서버 볼륨의 폴더와 같이 등록된 서버의 특정 위치를 나타냅니다. 해당 네임스페이스가 겹치지 않는 경우 동일한 볼륨에 여러 서버 끝점이 있을 수 있습니다(예: `F:\sync1` 및 `F:\sync2`). 각 서버 엔드포인트에 대해 개별적으로 클라우드 계층화 정책을 구성할 수 있습니다. 

탑재 지점을 통해 서버 엔드포인트를 만들 수 있습니다. 단, 서버 엔드포인트 내의 탑재 지점을 건너뜁니다.  

시스템 볼륨에서 서버 엔드포인트를 만들 수는 있지만, 그렇게 하려면 두 가지 제한 사항이 있습니다.
* 클라우드 계층화를 사용할 수 없습니다.
* 신속한 네임스페이스 복원(여기서는 시스템은 신속하게 전체 네임스페이스를 가져온 다음. 콘텐츠를 회수하기 위해 시작함)이 수행되지 않습니다.


> [!Note]  
> 비이동식 볼륨만 사용할 수 있습니다.  원격 공유에서 매핑된 드라이브는 서버 끝점 경로에서 지원되지 않습니다.  또한 시스템 볼륨에서 클라우드 계층화가 지원되지 않지만, 서버 끝점이 Windows 시스템 볼륨에 있을 수 있습니다.

기존 파일 집합이 있는 서버 위치를 동기화 그룹에 서버 엔드포인트로 추가하는 경우 해당 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
클라우드 엔드포인트는 동기화 그룹의 일부인 Azure 파일 공유입니다. 전체 Azure 파일 공유가 동기화되며, Azure 파일 공유는 하나의 클라우드 엔드포인트의 구성원만 될 수 있습니다. 따라서 Azure 파일 공유는 하나의 동기화 그룹의 구성원만 될 수 있습니다. 기존 파일 집합이 있는 Azure 파일 공유를 동기화 그룹에 클라우드 엔드포인트로 추가하는 경우 기존 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

> [!Important]  
> Azure File Sync는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure File Sync 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다. 자세한 내용은 [Azure Files 질문과 대답](storage-files-faq.md#afs-change-detection)을 참조하세요.

### <a name="cloud-tiering"></a>클라우드 계층화 
클라우드 계층화는 Azure File Sync의 선택적 기능으로, 크기가 64KiB보다 크고 덜 자주 사용하거나 액세스하는 파일을 Azure Files로 계층화할 수 있도록 합니다. 파일을 계층화할 경우 Azure File Sync 파일 시스템 필터(StorageSync.sys)는 파일을 포인터 또는 재분석 지점으로 로컬로 대체합니다. 재분석 지점은 Azure Files의 파일에 대한 URL을 나타냅니다. 계층화된 파일은 NTFS에서 "오프라인" 특성이 설정되므로 타사 응용 프로그램이 계층화된 파일을 식별할 수 있습니다. 사용자가 계층화된 파일을 열 때 파일이 시스템에 로컬로 저장되어 있지 않다는 사실을 모르더라도 Azure File Sync는 Azure Files에서 파일 데이터를 원활하게 회수합니다. 이 기능을 HSM(계층적 저장소 관리)이라고도 합니다.

> [!Important]  
> 클라우드 계층화는 Windows 시스템 볼륨의 서버 엔드포인트에서 지원되지 않습니다.

## <a name="azure-file-sync-interoperability"></a>Azure File Sync 상호 운용성 
이 섹션에서는 Windows Server 기능/역할 및 타사 솔루션과의 Azure File Sync 상호 운용성에 대해 설명합니다.

### <a name="supported-versions-of-windows-server"></a>지원되는 Windows Server 버전
현재 Azure File Sync에서 지원되는 Windows Server 버전은 다음과 같습니다.

| 버전 | 지원되는 SKU | 지원되는 배포 옵션 |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter 및 Standard | 전체(UI가 있는 서버) |
| Windows Server 2012 R2 | Datacenter 및 Standard | 전체(UI가 있는 서버) |

이후 버전의 Windows Server는 출시되면 추가될 예정입니다. 이전 버전의 Windows는 사용자의 의견에 따라 추가될 수 있습니다.

> [!Important]  
> Windows 업데이트의 최신 업데이트를 사용하여 Azure File Sync와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

### <a name="file-system-features"></a>파일 시스템 기능
| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완전하게 지원 | Windows ACL은 Azure File Sync에 의해 유지되며 서버 엔드포인트에서 Windows Server에 의해 적용됩니다. 파일이 클라우드에서 직접 액세스될 경우 Azure Files에서 Windows ACL을 (아직) 지원하지 않는 것입니다. |
| 하드 링크 | 생략 | |
| 바로 가기 링크 | 생략 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 생략 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 생략 | |
| NTFS 압축 | 완전하게 지원 | |
| 스파스 파일 | 완전하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 끝점의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

> [!Note]  
> NTFS 볼륨만 지원됩니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 지원되지 않습니다.

### <a name="files-skipped"></a>건너뛴 파일
| 파일/폴더 | 참고 |
|-|-|
| Desktop.ini | 시스템에 특정된 파일 |
| ethumbs.db$ | 썸네일의 임시 파일 |
| ~$\*.\* | Office 임시 파일 |
| \*.tmp | 임시 파일 |
| \*.laccdb | Access DB 잠금 파일|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 내부 동기화 파일|
| \\시스템 볼륨 정보 | 볼륨에 특정된 폴더 |
| $RECYCLE.BIN| 폴더 |
| \\SyncShareState | 동기화할 폴더 |

### <a name="failover-clustering"></a>장애 조치(Failover) 클러스터링
Windows Server 장애 조치(Failover) 클러스터링은 "범용 파일 서버" 배포 옵션의 Azure 파일 동기화에서 지원됩니다. 장애 조치(Failover) 클러스터링은 "응용 프로그램 데이터용 스케일 아웃 파일 서버" 또는 "CSV(클러스터된 공유 볼륨)"에서는 지원되지 않습니다.

> [!Note]  
> 동기화가 제대로 작동하려면 장애 조치(Failover) 클러스터의 모든 노드에 Azure File Sync 에이전트를 설치해야 합니다.

### <a name="data-deduplication"></a>데이터 중복 제거
클라우드 계층화를 사용하도록 설정하지 않은 볼륨의 경우 Azure File Sync는 볼륨에 Windows Server 데이터 중복 제거를 사용하도록 지원합니다. 현재, 클라우드 계층화가 사용되도록 설정된 Azure File Sync와 데이터 중복 제거 간에는 상호 운용성이 지원되지 않습니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure File Sync에서는 [Azure File Sync 에이전트 1.2](https://go.microsoft.com/fwlink/?linkid=864522)부터 DFS 네임스페이스(DFS-N) 및 DFS 복제(DFS-R)와의 상호 작용을 지원합니다.

**DFS 네임 스페이스(DFS-N)**: DFS-N 서버에서 Azure File Sync가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure File Sync 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제(DFS-R)**: DFS-R 및 Azure File Sync는 모두 복제 솔루션이므로 대부분의 경우, DFS-R을 Azure File Sync로 대체하는 것이 좋습니다. 하지만 DFS-R과 Azure File Sync를 함께 사용해야 하는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure File Sync 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure File Sync로 마이그레이션](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터의 복사본을 필요로 하는 모든 온-프레미스 서버를 인터넷에 직접 연결할 수는 없습니다.
- 분기 서버는 Azure File Sync를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure File Sync 및 DFS-R을 나란히 사용하려면

1. Azure File Sync 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](https://technet.microsoft.com/library/jj127250)를 참조하세요.

### <a name="antivirus-solutions"></a>바이러스 백신 솔루션
바이러스 백신은 알려진 악성 코드 파일을 검색하는 방식으로 작동하기 때문에 바이러스 백신 제품은 계층화된 파일의 회수를 발생할 수 있습니다. 계층화된 파일에는 "오프라인" 특성이 설정되어 있으므로 오프라인 파일 읽기를 건너뛰도록 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요. 

다음 솔루션은 오프라인 파일 건너뛰기를 지원하는 것으로 알려져 있습니다.

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf)(PDF 90페이지의 "필요한 항목만 검색" 참조)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>백업 솔루션
바이러스 백신 솔루션과 같은 백업 솔루션은 계층화된 파일이 회수되도록 할 수 있습니다. 온-프레미스 백업 제품을 사용하지 않고 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 백업하는 것이 좋습니다.

### <a name="encryption-solutions"></a>암호화 솔루션
암호화 솔루션에 대한 지원은 구현되는 방식에 따라 달라집니다. Azure 파일 동기화와 함께 작동하는 기능

- BitLocker 암호화
- Azure Information Protection, Azure RMS(Rights Management Services) 및 Active Directory RMS

Azure 파일 동기화와 함께 작동하지 않는 기능

- NTFS EFS(암호화 파일 시스템)

일반적으로 Azure File Sync는 BitLocker처럼 파일 시스템 아래에 있는 암호화 솔루션 및 BitLocker와 같은 파일 형식으로 구현된 솔루션과의 상호 운용성을 지원해야 합니다. 파일 시스템 위에 있는 솔루션(예: NTFS EFS)에 대해서는 특별한 상호 운용성이 설정되지 않았습니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 저장소 관리) 솔루션
다른 HSM 솔루션은 Azure File Sync와 함께 사용하면 안 됩니다.

## <a name="region-availability"></a>지역 가용성
Azure File Sync는 다음 지역에서 미리 보기로만 사용할 수 있습니다.

| 지역 | 데이터 센터 위치 |
|--------|---------------------|
| 오스트레일리아 동부 | 뉴사우스웨일스 |
| 캐나다 중부 | 토론토 |
| 캐나다 동부 | 퀘벡 시티 |
| 미국 중부 | 아이오와 |
| 동아시아 | 홍콩 |
| 미국 동부 | 버지니아 |
| 미국 동부2 | 버지니아 |
| 북유럽 | 아일랜드 |
| 동남아시아 | 싱가포르 |
| 영국 남부 | 런던 |
| 서유럽 | 네덜란드 |
| 미국 서부 | 캘리포니아 |

미리 보기에서는 Storage 동기화 서비스와 동일한 지역에 있는 Azure 파일 공유와의 동기화만 지원됩니다.

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure File Sync 배포](storage-sync-files-deployment-guide.md)
