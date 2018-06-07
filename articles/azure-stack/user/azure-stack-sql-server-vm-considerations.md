---
title: Azure 스택 가상 컴퓨터의 SQL Server에 대 한 성능 모범 사례
description: Microsoft Azure 스택 가상 컴퓨터의 SQL Server 성능을 최적화 하기 위한 유용한 정보를 제공 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701458"
---
# <a name="optimize-sql-server-performance"></a>SQL Server 성능 최적화

이 문서에서는 Microsoft Azure 스택 가상 컴퓨터의 SQL Server 성능을 최적화 하기 위한 지침을 제공 합니다. Azure 스택 가상 컴퓨터에서 SQL Server를 실행 하는 경우 동일한 데이터베이스 성능 튜닝 옵션 온-프레미스 서버 환경에서 SQL Server에 적용 가능한 사용 합니다. Azure 스택 클라우드에서 관계형 데이터베이스의 성능은 여러 요인에 따라 달라 집니다. 요소는 가상 컴퓨터의 패밀리 크기와 데이터 디스크 구성에 포함 됩니다.

SQL Server 이미지를 만들 때 [스택 Azure 포털에서 가상 컴퓨터를 프로 비전 하는 것이 좋습니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)합니다. Azure 스택 관리자 포털에서 마켓플레이스 관리진에서 SQL IaaS 확장을 다운로드 하 고 선택 하는 SQL 가상 컴퓨터 가상 하드 드라이브 (Vhd)를 다운로드 합니다. 여기에 SQL2014SP2, SQL2016SP1, SQL2017 포함 됩니다.

> [!NOTE]  
> 전체 Azure 포털을 사용 하 여 SQL Server 가상 컴퓨터를 프로 비전 하는 방법을 설명 하는 문서, 동안 지침에도 적용 됩니다 Azure 스택 같은 차이점이: SSD를 사용할 수 없는 운영 체제 디스크에 대 한 관리 되는 디스크를 사용할 수 없는 및 저장소 구성에서 약간의 차이가 있습니다.

가져오기는 *최상의* 스택 Azure 가상 컴퓨터에서 SQL Server에 대 한 성능은이 문서의 핵심입니다. 사용자의 작업이 더 적게 이면 모든 권장된 최적화가 필요 하지 않습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항 및 작업 패턴을 고려하세요.

> [!NOTE]  
> Azure 가상 컴퓨터에서 SQL Server에 대 한 성능 지침을 참조 [이 여기서](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)합니다.

## <a name="before-you-begin"></a>시작하기 전에
Azure 스택 가상 컴퓨터에서 SQL Server의 최적의 성능을 위해서는 다음 검사 목록은:


|영역|최적화|
|-----|-----|
|가상 머신 크기 |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 또는 SQL Server Enterprise edition 이상.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 또는 Web edition 및 SQL Server Standard edition 이상.|
|Storage |지원 되는 가상 컴퓨터 집합을 사용 하 여 [프리미엄 저장소](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences)합니다.|
|디스크 |(로그 파일에 대해 각각 하나씩) 및 데이터 파일 및 TempDB에 대해 하나씩 두 개의 데이터 디스크의 최소를 사용 하 고 용량 요구 사항에 따라 디스크 크기를 선택 합니다. 기본 데이터 파일 위치로 설정 하 여 이러한 디스크가 SQL Server 설치 중입니다.<br><br>데이터베이스 저장소나 로깅을 위해 운영 체제 또는 임시 디스크를 사용하지 않습니다.<br>여러 Azure 데이터 디스크를 저장소 공간을 사용 하 여 IO 처리량을 늘리는 스트라이프 합니다.<br><br>문서화된 할당 크기로 포맷합니다.|
|I/O|데이터 파일에 대해 즉시 파일 초기화를 사용하도록 설정합니다.<br><br>전체적으로 작은 고정 된 증가분 (64MB-256MB) 포함 된 데이터베이스에서 자동 증가 제한 합니다.<br><br>데이터베이스에서 자동 축소를 사용하지 않도록 설정합니다.<br><br>데이터 디스크의 운영 체제 디스크에 기본 백업 및 데이터베이스 파일 위치를 설정 합니다.<br><br>잠긴 페이지를 사용하도록 설정합니다.<br><br>SQL Server 서비스 팩과 누적 업데이트를 적용 합니다.|
|기능 관련|(사용 중인 SQL Server 버전에서 지 원하는) 하는 경우 blob 저장소에 직접 백업 합니다.|
|||

대 한 자세한 내용은 *어떻게* 및 *이유* 이러한 최적화를 검토 하십시오 세부 정보 및 지침은 다음 섹션에 제공 합니다.

## <a name="virtual-machine-size-guidance"></a>가상 컴퓨터 크기 지침

성능에 민감한 응용 프로그램의 경우 다음 [가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 는 것이 좋습니다.

- **SQL Server Enterprise edition:** d s 3 이상

- **SQL Server Standard edition 및 Web edition:** d s 2 이상

Azure 스택 DS 및 DS_v2 가상 컴퓨터 패밀리 시리즈 간에 성능 차이점이 없음이 있습니다.

## <a name="storage-guidance"></a>저장소 지침

Azure 스택의 DSv2 시리즈) (함께 DS 시리즈 가상 컴퓨터는 최대 운영 체제 디스크 및 데이터 디스크 처리량 (IOPS)를 제공 합니다. 가상 컴퓨터를 DS 또는 DSv2 시리즈 운영 체제 디스크에 대 한 및 2,300 IOPS 형식 또는 선택한 디스크의 크기에 관계 없이 데이터 디스크 수까지 최대 1, 000 IOPS를 제공합니다.

데이터 디스크 처리량을 고유 하 게 가상 컴퓨터 패밀리 계열에 따라 결정 됩니다. 할 수 있습니다 [이 문서를 참조](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 가상 컴퓨터 패밀리 시리즈 당 데이터 디스크 처리량을 식별할 수 있습니다.

> [!NOTE]  
> 프로덕션 작업에 대 한 디스크 및 데이터 디스크는 운영 체제에 가능한 최대 IOPS를 제공 하는 DS 시리즈 또는 DSv2 시리즈 가상 컴퓨터를 선택 합니다.

Azure 스택에서 저장소 계정을 만들 때 지리적 복제 옵션은이 기능이 Azure 스택에서 사용할 수 없기 때문에 효과가 없습니다.

## <a name="disks-guidance"></a>디스크 지침

Azure 스택 가상 컴퓨터에는 세 가지 주요 디스크 유형이 있습니다.

- **운영 체제 디스크:** 스택 Azure 가상 컴퓨터를 만들 때 플랫폼 하나 이상의 디스크를 연결 하는 (라고는 **C** 드라이브) 운영 체제 디스크에 대 한 가상 컴퓨터에 있습니다. 이 디스크는 저장소에 페이지 Blob으로 저장된 VHD입니다.

- **임시 디스크:** 스택 Azure 가상 컴퓨터는 임시 디스크 라는 다른 디스크를 포함 (레이블이 지정 된 **D** 드라이브). 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.

- **데이터 디스크:** 데이터 디스크로 가상 컴퓨터에 추가 디스크를 연결할 수 있으며 이러한 디스크는 저장소에 페이지 blob으로 저장 됩니다.

다음 섹션에서는 이러한 서로 다른 디스크를 사용하기 위한 권장 사항을 설명합니다.

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 버전으로 부팅하고 탑재할 수 있는 VHD이며 **C** 드라이브로 레이블이 지정됩니다.

### <a name="temporary-disk"></a>임시 디스크

로 표시 된 임시 저장소 드라이브는 **D** 드라이브에 설치 되어 지속 되지 않습니다. 손실 해도 상관, 예: 사용자 데이터베이스 파일 또는 사용자 트랜잭션 로그 파일에 없는 데이터를 저장 하지 마십시오는 **D** 드라이브입니다.

각 데이터 디스크 제공 2,300 최대 데이터 디스크 IOPS는 최대 데이터 디스크에 TempDB를 저장 하는 것이 좋습니다.

### <a name="data-disks"></a>데이터 디스크

- **데이터 및 로그 파일에 대 한 데이터 디스크를 사용 합니다.** 디스크 스트라이프를 사용 하지 않는 경우 프리미엄 저장소-여기서 디스크가 두 개 로그 파일을 포함 하 고 데이터 및 TempDB 파일 포함 하는 다른 지 원하는 가상 컴퓨터에서 두 개의 데이터 디스크를 사용 합니다. 각 데이터 디스크에 설명 된 대로 다양 한 IOPS 및 대역폭 (MB/s)의 가상 컴퓨터 제품군에 따라 제공 [Azure 스택에서 지원 되는 가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다. 디스크 스트라이프 기술을 예: 저장소 공간을 사용 하는 경우 모든 데이터 및 로그 파일 (TempDB 포함)는 동일한 드라이브에 배치 합니다. 이 구성 요소를 사용 하기를 어떤 파일 요구 사항에 관계 없이 특정 시간에 SQL Server에 사용할 수 있는 IOPS의 최대 수를 제공 합니다.

> [!NOTE]  
> 포털에서 SQL Server 가상 컴퓨터를 프로 비전 하는 경우 저장소 구성 편집 옵션이 있습니다. Azure 스택 구성에 따라 하나 이상의 디스크를 구성합니다. 여러 디스크 하나의 저장소 풀으로 결합 됩니다. 이 구성에서는 데이터 및 로그 파일이 함께 배치됩니다.

- **디스크 스트라이프:** 처리량이 더 많은 처리량에 대 한 추가 데이터 디스크를 추가할 고 디스크 스트라이프를 사용할 수 있습니다. 필요한 데이터 디스크 개수를 확인 하려면 IOPS 및 대역폭 필요한 데이터 및 TempDB 파일 및 로그 파일에 대 한 개수를 분석 합니다. 데이터 디스크를 가상 컴퓨터 시리즈 제품군을 기반으로 하며 가상 컴퓨터 크기에 따라 하지 당 IOPS 제한은 확인 합니다. 그러나 네트워크 대역폭 제한은 가상 컴퓨터 크기에 기반 합니다. 표를 참조 하십시오. [스택에서 Azure 가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 자세한 세부 정보에 대 한 합니다. 다음 지침을 사용하세요.

    - 이상에 대 한 Windows Server 2012를 사용 하 여 [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx) 다음 지침에:

        1.  온라인 트랜잭션 처리 (OLTP) 워크 로드 및 256KB (262, 144 바이트) 데이터 웨어하우징 작업에 대 한 성능에 영향을 파티션 정렬 오류를 방지 하려면에 대 한 64KB (65536 바이트)를 인터리빙 (스트라이프 크기)을 설정 합니다. 이는 PowerShell로 설정되어야 합니다.

        2.  열 수를 실제 디스크 수로 설정합니다. 8 개 이상의 디스크 (서버 관리자 UI 하지)를 구성 하는 경우 PowerShell을 사용 합니다.

            예를 들어 다음 PowerShell 인터리빙 크기가 64KB 하 고 2 열 수로 설정 된 새 저장소 풀을 만듭니다.

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- 예상되는 부하에 따라 저장소 풀에 연결되는 디스크 수를 결정합니다. 연결 된 데이터 디스크의 수가 다르고 서로 다른 가상 컴퓨터 크기 사용할 수 있음을 염두에서에 둬야 합니다. 자세한 내용은 참조 [Azure 스택에서 지원 되는 가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다.
- 데이터 디스크에 대 한 가능한 최대 IOPS를 가져오려면 권장 구성에서 지 원하는 데이터 디스크의 최대 수를 추가 하는 프로그램 [가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 디스크 스트라이프를 사용 합니다.
- **NTFS 할당 단위 크기:** 데이터 디스크를 포맷할 때 것이 좋습니다 64KB 할당 단위 크기를 사용 하 여 TempDB 데이터 및 로그 파일에 대 한 합니다.
- **디스크 관리 사례:** 데이터 디스크를 제거할 때 변경 될 때 SQL Server 서비스를 중지 합니다. 또한 변경 되지 않습니다는 디스크에서 캐시 설정을 모든 성능 향상을 제공 하지 마십시오.

> [!WARNING]  
> 이러한 작업 중 SQL 서비스를 중지 하지 않으면 데이터베이스 손상이 될 수 있습니다.

## <a name="io-guidance"></a>I/O 지침

- 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 와 SQL Server (MSSQLSERVER) 서비스 계정에 부여 하면 즉시 파일 초기화를 이용 하려면 **SE_MANAGE_VOLUME_NAME** 에 추가 하는 **볼륨 관리 태스크 실행** 보안 정책입니다. SQL Server 플랫폼 이미지를 Azure에 대 한 사용 하는 경우 기본 서비스 계정 (**NT Service\MSSQLSERVER**)에 추가 되지 않습니다는 **볼륨 관리 태스크 실행** 보안 정책입니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](https://msdn.microsoft.com/library/ms175935.aspx)를 참조하세요.
- **자동 증가** 은 예기치 않은 증가에 대비한 대책입니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가 사용 하는 경우 사전에 증가 사용 하 여 파일의 **크기** 전환 합니다.
- **자동 축소**를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.
- 기본 백업 및 데이터베이스 파일 위치를 설정합니다. 이 문서의 권장 사항을 사용 하 고 서버 속성 창에서 변경 합니다. 지침은 [데이터 및 로그 파일의 기본 위치 보기 또는 변경(SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)을 참조하세요. 다음 스크린 샷에서 이러한 변경을 수행 하는 위치를 보여 줍니다.

    > ![기본 위치 보기 또는 변경](./media/sql-server-vm-considerations/image1.png)

- 잠긴 페이지를 사용하도록 설정하여 IO 및 페이징 작업을 줄입니다. 자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows)](https://msdn.microsoft.com/library/ms190730.aspx)을 참조하세요.

- Azure 스택, 백업을 포함 하 여 입/출력을 전송 하는 경우 모든 데이터 파일을 압축 하는 것이 좋습니다.

## <a name="feature-specific-guidance"></a>기능별 지침

더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음 목록에는 성능 향상에 도움이 될 수 있는 몇 가지 SQL Server 기능을 강조 표시 합니다.

- **Azure에 백업** **저장 합니다.** Azure 스택 가상 컴퓨터에서 실행 중인 SQL Server에 대 한 백업을 수행 하는 경우 SQL Server Backup to URL 사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다.

    백업 또는 Azure 저장소를 사용 하 여 복원을에 제공 된 권장 사항을 따릅니다 때 [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) 및 [복원에서 백업에에서 저장 된 Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). [Azure Virtual Machines의 SQL Server에 대한 자동화된 백업](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)을 사용하여 이러한 백업을 자동화할 수도 있습니다.

-   **Azure 스택 저장소에 백업 합니다.** Azure 저장소로 백업할 때와 마찬가지로 Azure 스택 저장소와 비슷한 방식를 백업할 수 있습니다. SQL Server Management Studio (SSMS) 내 백업을 만들 때 구성 정보를 수동으로 입력 해야 합니다. SSMS를 사용 하 여 저장소 컨테이너 또는 공유 액세스 서명을 만들 수 없습니다. SSMS는 Azure 스택 구독 하지 Azure 구독에만 연결 합니다. 대신, 스택 Azure 포털에서 또는 PowerShell을 사용한 저장소 계정, 컨테이너 및 공유 액세스 서명을 만들 해야 합니다.

    SQL Server 백업 대화 상자에 정보를 배치 하면:

    ![SQL Server 백업](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > 공유 액세스 서명을 선행 공백 없이 Azure 스택 포털에서 SAS 토큰은 '?' 에 문자열입니다. 앞에 오는 삭제 해야 하는 포털에서 복사 기능을 사용 하는 경우 '?' 에 대 한 SQL Server 내에서 작업 하는 토큰입니다.

    한 번 설정 백업을 대상 있고 SQL Server를 구성한 다음를 백업할 수 있습니다 스택 Azure blob 저장소에 있습니다.

## <a name="next-steps"></a>다음 단계

[서비스를 사용 하 여 또는 Azure 스택 위한 응용 프로그램 구축](azure-stack-considerations.md)