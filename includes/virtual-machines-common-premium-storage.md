# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>VM의 고성능 Premium Storage 및 관리 디스크
Azure Premium Storage는 입력/출력(I/O) 사용량이 많은 워크로드가 있는 VM(가상 머신)에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Premium Storage를 사용하는 VM 디스크는 SSD(반도체 드라이브)에 데이터를 저장합니다. 프리미엄 저장소의 속도 및 성능을 활용하기 위해 기존 VM 디스크를 Premium Storage로 마이그레이션할 수 있습니다.

Azure에서는 여러 프리미엄 저장소 디스크를 VM에 연결할 수 있습니다. 여러 디스크를 사용하면 응용 프로그램에는 VM당 최대 256TB의 저장소가 제공됩니다. Premium Storage를 사용할 경우 VM당 80,000 IOPS(초당 I/O 작업 수) 및 VM당 디스크 처리량을 2,000MB/초까지 달성할 수 있습니다. 읽기 작업의 대기 시간이 매우 짧습니다.

Premium Storage를 사용하여 Azure는 Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite 및 SharePoint 팜 등의 까다로운 엔터프라이즈 응용 프로그램을 클라우드로 리프트 앤 시프트하는 기능을 제공합니다. 일관된 고성능 및 짧은 대기 시간을 필요로 하는 SQL Server, Oracle, MongoDB, MySQL, Redis 등 성능 집약적 데이터베이스 워크로드를 응용 프로그램에서 실행할 수 있습니다.

> [!NOTE]
> 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 VM 디스크를 Premium Storage로 마이그레이션하는 것이 좋습니다. 디스크에 높은 IOPS가 필요하지 않으면 표준 Azure Storage에 유지하여 비용을 제한할 수 있습니다. 표준 저장소는 SSD 대신 HDD(하드 디스크 드라이브)에 VM 디스크 데이터를 저장합니다.
> 

Azure에서는 VM에 프리미엄 디스크를 만드는 두 가지 방법을 제공합니다.

* **관리되지 않는 디스크**

    원래 방법은 관리되지 않는 디스크를 사용하는 것입니다. 관리되지 않는 디스크에서는 VM 디스크에 해당하는 VHD(가상 하드 디스크) 파일을 저장하는 데 사용되는 저장소 계정을 관리합니다. VHD 파일은 Azure Storage 계정에 페이지 Blob으로 저장됩니다. 

* **관리되는 디스크**

    [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md)를 선택하면 Azure에서 VM 디스크에 사용되는 저장소 계정을 관리해 줍니다. 필요한 디스크 유형(Premium 또는 Standard) 및 디스크 크기를 지정합니다. Azure에서 사용자에게 맞는 디스크를 만들고 관리합니다. 저장소 계정에 대한 확장성 한도 내에 머무르기 위해 다수의 저장소 계정에 디스크를 배치하려고 고심할 필요가 없습니다. Azure가 알아서 처리해 드립니다.

많은 기능을 활용하려면 관리 디스크를 사용하는 것이 좋습니다.

Premium Storage를 시작하려면 [무료로 Azure 계정을 만드세요](https://azure.microsoft.com/pricing/free-trial/). 

기존 VM을 Premium Storage로 마이그레이션하는 내용은 [Windows VM을 비관리 디스크에서 관리 디스크로 변환](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) 또는 [Linux VM을 비관리 디스크에서 관리 디스크로 변환](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md)을 참조하세요.

> [!NOTE]
> Premium Storage는 대부분의 지역에서 사용할 수 있습니다. 사용 가능한 지역 목록은 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/regions/#services)에서 **디스크 저장소** 행을 참조하세요.
> 

## <a name="features"></a>기능

Premium Storage 기능 중 일부는 다음과 같습니다.

* **Premium Storage 디스크**

    Premium Storage는 특정 크기-시리즈 VM에 연결할 수 있는 VM 디스크를 지원합니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈, Ls 시리즈, Fs 시리즈 및 Esv3 시리즈 VM을 지원합니다. P4(32GB), P6(64GB), P10(128GB), P20(512GB), P30(1024GB), P40(2048GB), P50(4095GB) 등, 7가지 디스크 크기 중에서 선택할 수 있습니다. P4 및 P6 디스크 크기는 아직 Managed Disks에 대해서만 지원됩니다. 디스크 크기마다 자체 성능 사양이 있습니다. 응용 프로그램 요구 사항에 따라 하나 이상의 디스크를 VM에 연결할 수 있습니다. 사양에 대한 자세한 내용은 [Premium Storage 확장성 및 성능 목표](#scalability-and-performance-targets)에서 설명합니다.

* **프리미엄 페이지 Blob**

    Premium Storage에서는 페이지 Blob을 지원합니다. 페이지 Blob을 사용하여 Premium Storage에 VM의 관리되지 않는 영구적인 디스크를 저장합니다. 표준 Azure Storage와 달리 Premium Storage는 블록 Blob, 추가 Blob, 파일, 테이블 또는 큐를 지원하지 않습니다. 프리미엄 페이지 Blob는 P10~P50 및 P60(8191GiB)과 같은 여섯 가지 크기를 지원합니다. P60 프리미엄 페이지 Blob은 VM 디스크로 연결되도록 지원되지 않습니다. 

    프리미엄 저장소 계정에 있는 모든 개체는 페이지 Blob이 됩니다. 페이지 Blob은 지원되는 프로비전 크기 중 하나에 맞춰집니다. 이 때문에 프리미엄 저장소 계정은 매우 작은 Blob을 저장하기에는 적합하지 않습니다.

* **Premium Storage 계정**

    Premium Storage를 사용하려면 관리되지 않는 디스크에 대한 프리미엄 저장소 계정을 만들어야 합니다. [Azure Portal](https://portal.azure.com)에서 프리미엄 저장소 계정을 만들려면 **Premium** 성능 계층을 선택합니다. **LRS(로컬 중복 저장소)** 복제 옵션을 선택합니다. 성능 계층을 **Premium_LRS**로 설정하여 프리미엄 저장소 계정을 만들 수도 있습니다. 성능 계층을 변경하려면 다음 방법 중 하나를 사용합니다.
     
    - [Azure Storage용 PowerShell](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure Storage용 Azure CLI](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp)(Azure Resource Manager 배포용) 또는 Azure Storage 리소스 공급자 클라이언트 라이브러리 중 하나

    프리미엄 저장소 계정 한도에 대해 자세히 알아보려면 [Premium Storage 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets)를 참조하세요.

* **프리미엄 로컬 중복 저장소**

    프리미엄 저장소 계정은 복제 옵션으로 로컬 중복 저장소만 지원합니다. 로컬 중복 저장소는 단일 지역 내에 데이터 복사본 3개를 유지합니다. 지역적 재해 복구를 위해 [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md)을 사용하여 VM 디스크를 다른 지역에 백업해야 합니다. 또한 백업 자격 증명 모음으로 GRS(지역 중복 저장소) 계정을 사용해야 합니다. 

    Azure는 저장소 계정을 관리되지 않는 디스크의 컨테이너로 사용합니다. 관리되지 않는 디스크로 Premium Storage를 지원하는 Azure VM을 만들고 프리미엄 저장소 계정을 선택하는 경우 운영 체제와 데이터 디스크가 해당 저장소 계정에 저장됩니다.

## <a name="supported-vms"></a>지원되는 VM

Premium Storage는 B 시리즈, DS 시리즈, DSv2 시리즈, DSv3 시리즈, GS 시리즈, Ls 시리즈, M 시리즈 및 Fs 시리즈 VM을 지원합니다. 이러한 VM 유형에는 표준 및 프리미엄 저장소 디스크를 사용할 수 있습니다. Premium Storage와 호환되지 않는 VM 시리즈에서는 프리미엄 저장소 디스크를 사용할 수 없습니다.


Azure에서 Windows용 VM 유형 및 크기에 대한 자세한 내용은 [Windows VM 크기](../articles/virtual-machines/windows/sizes.md)를 참조하세요. Azure에서 Linux용 VM 유형 및 크기에 대한 자세한 내용은 [Linux VM 크기](../articles/virtual-machines/linux/sizes.md)를 참조하세요.

다음은 DS 시리즈, DSv2 시리즈, GS 시리즈, Ls 시리즈 및 Fs 시리즈 VM의 일부 기능입니다.

* **클라우드 서비스**

    DS 시리즈 VM만 포함하는 클라우드 서비스에 DS 시리즈 VM을 추가할 수 있습니다. DS 시리즈가 아닌 VM 유형을 포함하는 기존 클라우드 서비스에 DS 시리즈 VM을 추가하지 마세요. DS 시리즈 VM만 실행하는 새 클라우드 서비스에 기존 VHD를 마이그레이션할 수 있습니다. DS 시리즈 VM을 호스트하는 새 클라우드 서비스에 동일한 가상 IP 주소를 사용하려는 경우, [예약된 IP 주소](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)를 사용합니다. GS 시리즈 VM만 포함하는 기존 클라우드 서비스에 GS 시리즈 VM을 추가할 수 있습니다.

* **운영 체제 디스크**

    프리미엄 또는 표준 운영 체제 디스크를 사용하도록 Premium Storage VM을 설정할 수 있습니다. 최상의 환경을 위해 Premium Storage 기반 운영 체제 디스크를 사용하는 것이 좋습니다.

* **데이터 디스크**

    동일한 Premium Storage VM에 프리미엄 및 표준 디스크를 모두 사용할 수 있습니다. Premium Storage를 사용하면 VM을 프로비전하고 VM에 여러 영구 데이터 디스크를 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑할 수 있습니다.

    > [!NOTE]
    > [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프하는 경우, 저장소 공간은 사용하는 각 디스크에 대해 하나의 열로 설정해야 합니다. 그렇지 않은 경우, 디스크에서의 고르지 못한 트래픽 분배로 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. 기본적으로 서버 관리자에서는 최대 8개의 디스크를 열로 설정할 수 있습니다. 8개 이상의 디스크가 있는 경우 PowerShell을 사용하여 볼륨을 만듭니다. 열 수를 수동으로 지정합니다. 그렇지 않은 경우 서버 관리자 UI는 더 많은 디스크가 있더라도 8개의 열을 계속 사용합니다. 예를 들어 단일 스트라이프 세트에 32개의 디스크가 있다면 32개의 열을 지정합니다. 가상 디스크에서 사용하는 열 수를 지정하려면 [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet에서 *NumberOfColumns* 매개 변수를 사용합니다. 자세한 내용은 [저장소 공간 개요](http://technet.microsoft.com/library/hh831739.aspx) 및 [저장소 공간 FAQ](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)를 참조하세요.
    >
    > 

* **캐시**

    Premium Storage를 지원하는 크기 시리즈의 VM에는 높은 수준의 처리량 및 대기 시간을 얻을 수 있는 고유의 캐싱 기능이 있습니다. 이 캐싱 기능은 기본적인 프리미엄 저장소 디스크 성능을 능가합니다. 프리미엄 저장소 디스크의 디스크 캐싱 정책은 **ReadOnly**, **ReadWrite** 또는 **None**으로 설정할 수 있습니다. 기본 디스크 캐싱 정책은 모든 프리미엄 데이터 디스크에 대해서는 **ReadOnly**이고 운영 체제 디스크에 대해서는 **ReadWrite**입니다. 최적의 응용 프로그램 성능을 얻으려면 올바른 캐시 설정을 사용해야 합니다. 예를 들어 SQL Server 데이터 파일처럼 읽기 작업이 많거나 읽기 전용인 데이터 디스크의 경우에는 디스크 캐싱 정책을 **ReadOnly**로 설정하세요. SQL Server 로그 파일처럼 쓰기 작업이 많거나 쓰기 전용인 데이터 디스크의 경우에는 디스크 캐싱 정책을 **None**으로 설정하세요. [Premium Storage를 사용한 성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)에서 Premium Storage로 디자인을 최적화하는 자세한 방법을 알아보세요.

* **분석**

    Premium Storage의 디스크를 사용하여 VM 성능을 분석하려면 [Azure Portal](https://portal.azure.com)에서 VM 진단을 설정하세요. 자세한 내용은 [Azure 진단 확장을 통한 Azure VM 모니터링](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)을 참조하세요. 

    디스크 성능을 보려면 Windows VM용 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 및 Linux VM용 [iostat](http://linux.die.net/man/1/iostat) 명령과 같은 운영 체제 기반 도구를 사용하세요.

* **VM 규모 한도 및 성능**

    각 Premium Storage 지원 VM 크기에는 규모 한도가 있으며 IOPS, 대역폭 및 VM당 연결할 수 있는 디스크 수에 대한 성능 사양이 있습니다. VM에 프리미엄 저장소 디스크를 사용하는 경우 VM에서 디스크 트래픽을 운용하기에 충분한 IOPS 및 대역폭이 있어야 합니다.

    예를 들어, STANDARD_DS1 VM에는 프리미엄 저장소 디스크 트래픽에 대해 32MB/초의 전용 대역폭이 있습니다. P10 프리미엄 저장소 디스크는 100MB/초의 대역폭을 제공할 수 있습니다. 이 VM에 P10 프리미엄 저장소 디스크가 연결되어 있는 경우 대역폭을 32MB/초까지만 올릴 수 있습니다. P10 디스크가 제공할 수 있는 최대 100MB/초는 사용할 수 없습니다.

    현재 DS 시리즈에서 가장 큰 VM은 Standard_DS15_v2입니다. Standard_DS15_v2는 모든 디스크에 걸쳐 최대 960MB/초를 제공할 수 있습니다. GS 시리즈에서 가장 큰 VM은 Standard_GS5입니다. Standard_GS5는 모든 디스크에 걸쳐 최대 2,000MB/초를 제공할 수 있습니다.

    이러한 한도는 디스크 트래픽에만 해당됩니다. 이러한 한도에 캐시 적중 수 및 네트워크 트래픽은 포함되지 않습니다. VM 네트워크 트래픽에는 별도의 대역폭이 제공됩니다. 네트워크 트래픽에 대한 대역폭은 프리미엄 저장소 디스크에 사용된 전용 대역폭과는 다릅니다.

    Premium Storage 지원 VM에 대한 최대 IOPS 및 처리량(대역폭)에 대한 최신 정보는 [Windows VM 크기](../articles/virtual-machines/windows/sizes.md) 또는 [Linux VM 크기](../articles/virtual-machines/linux/sizes.md)를 참조하세요.

    프리미엄 저장소 디스크 및 해당 IOPS와 처리량 한도는 다음 섹션의 표를 참조하세요.

## <a name="scalability-and-performance-targets"></a>확장성 및 성능 대상
이 섹션에서는 Premium Storage 사용 시 고려해야 하는 확장성 및 성능 목표에 대해 설명합니다.

프리미엄 저장소 계정에는 다음과 같은 확장성 목표가 있습니다.

| 총 계정 용량 | 로컬 중복 저장소 계정의 총 대역폭 |
| --- | --- | 
| 디스크 용량: 35TB <br>스냅숏 용량: 10TB | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 저장소 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 저장소 계정에서 수신하는 모든 데이터(응답)

자세한 내용은 [Azure Storage 확장성 및 성능 목표](../articles/storage/common/storage-scalability-targets.md)를 참조하세요.

관리되지 않는 디스크에 프리미엄 저장소 계정을 사용하는 경우 응용 프로그램이 단일 저장소 계정의 확장성 목표를 초과하면 관리 디스크로 마이그레이션하려고 할 수 있습니다. 관리 디스크로 마이그레이션하지 않으려면 응용 프로그램이 다수의 저장소 계정을 사용하도록 빌드합니다. 그런 다음 이 저장소 계정 간에 데이터를 분할합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 저장소 계정에 분산합니다. 단일 프리미엄 저장소 계정의 한도는 35TB입니다. 단일 프리미엄 저장소 계정에 35TB를 넘는 프로비전된 디스크가 없도록 해야 합니다.

### <a name="premium-storage-disk-limits"></a>Premium Storage 디스크 한도
프리미엄 저장소 디스크를 프로비전할 때 디스크의 크기가 최대 IOPS 및 처리량(대역폭)을 결정합니다. Azure에서는 P4(Managed Disks에만 해당), P6(Managed Disks에만 해당), P10, P20, P30, P40 및 P50과 같은 일곱 가지 프리미엄 저장소 디스크를 제공합니다. 각 프리미엄 저장소 디스크 유형에는 특정 IOPS 및 처리량 한도가 있습니다. 디스크 유형에 대한 한도는 다음 표에 설명되어 있습니다.

| 프리미엄 디스크 유형  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 디스크 크기           | 32GB| 64GB| 128GB| 256GB| 512 GB            | 1,024GB(1TB)    | 2,048GB(2TB)    | 4,095GB(4TB)    | 
| 디스크당 IOPS       | 120   | 240   | 500   | 1100   | 2,300              | 5,000              | 7,500              | 7,500              | 
| 디스크당 처리량 | 초당 25MB  | 초당 50MB  | 초당 100MB | 초당 125MB | 초당 150MB | 초당 200MB | 초당 250MB | 초당 250MB | 

> [!NOTE]
> [Premium Storage 지원 VM](#premium-storage-supported-vms) 섹션에서 설명한 대로 VM에서 디스크 트래픽을 운용하기에 충분한 대역폭을 사용할 수 있는지 확인해야 합니다. 그렇지 않으면 처리량 및 IOPS가 낮은 값으로 제한됩니다. 최대 처리량 및 IOPS는 이전 표에 설명된 디스크 한도가 아닌 VM 한도를 기초로 합니다.  
> 
> 

다음은 Premium Storage 확장성 및 성능 목표에 대해 알아야 하는 중요한 사항입니다.

* **프로비전된 용량 및 성능**

    프리미엄 저장소 디스크를 프로비전하면 표준 저장소와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 저장소 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 응용 프로그램에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다.

* **디스크 크기**

    앞의 섹션의 표에 나와 있는 것처럼 디스크 크기(올림)는 테이블에 지정되어 있는 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 예를 들어 100GB의 디스크 크기는 P10 옵션으로 분류됩니다. 최대 100MB/초 처리량으로 최대 500 IOPS를 수행할 수 있습니다. 마찬가지로 400GB의 디스크 크기는 P20으로 분류됩니다. 최대 150MB/초 처리량으로 최대 2,300 IOPS를 수행할 수 있습니다.
    
    > [!NOTE]
    > 기존 디스크의 크기를 쉽게 확대할 수 있습니다. 예를 들어, 30GB 크기의 디스크를 128GB 또는 1TB로 확대할 수 있습니다. 용량이 더 필요하거나 IOPS 및 처리량이 더 필요한 경우 P20 디스크를 P30 디스크로 전환할 수 있습니다. 
    > 
 
* **I/O 크기**

    I/O 크기는 256KB입니다. 전송되는 데이터가 256KB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KB 크기의 여러 I/O로 계산됩니다. 예를 들어 1,100KB I/O는 I/O 단위 5개로 계산됩니다.

* **처리량**

    처리량 한도에는 디스크에 쓰기뿐 아니라 캐시에서 제공되지 않은 디스크 읽기 작업도 포함됩니다. 예를 들어 P10 디스크는 디스크당 처리량이 100MB/초입니다. P10 디스크의 유효한 처리량에 대한 예가 다음 표에 나와 있습니다.

    | P10 디스크당 최대 처리량 | 디스크에서 캐시 없이 읽기 | 디스크로 캐시 없이 쓰기 |
    | --- | --- | --- |
    | 100MB/초 | 100MB/초 | 0 |
    | 100MB/초 | 0 | 100MB/초 |
    | 100MB/초 | 60MB/s | 40MB/초 |

* **캐시 적중 수**

    캐시 적중 수는 디스크의 할당된 IOPS 또는 처리량으로 제한되지 않습니다. 예를 들어 Premium Storage에서 지원되는 VM에 **ReadOnly** 캐시 설정이 있는 데이터 디스크를 사용하는 경우 캐시에서 제공되는 읽기는 디스크의 IOPS 및 처리 용량이 적용되지 않습니다. 디스크의 워크로드가 대부분 읽기인 경우 상당히 높은 처리량을 얻을 수 있습니다. 캐시에는 VM 크기에 기반한 VM 수준에서 별도의 IOPS 및 처리량 한도가 적용됩니다. DS 시리즈 VM은 대략 캐시 및 로컬 SSD I/O에 대해 코어당 4,000 IOPS 및 33MB/초입니다. GS 시리즈 VM은 캐시 및 로컬 SSD I/O에 대해 코어당 5,000 IOPS 및 50MB/초의 한도를 포함합니다. 

## <a name="throttling"></a>제한
응용 프로그램 IOPS 또는 처리량이 프리미엄 저장소 디스크에 대해 할당된 한도를 초과하는 경우 제한이 발생할 수 있습니다. VM의 모든 디스크의 디스크 트래픽이 VM에서 사용할 수 있는 디스크 대역폭 한도를 초과하는 경우에도 제한이 발생할 수 있습니다. 제한을 방지하려면 디스크에 대해 보류 중인 I/O 요청 수를 제한하는 것이 좋습니다. 프로비전한 디스크의 확장성 및 성능 목표에 따라, VM에 사용할 수 있는 디스크 대역폭에 따라 제한을 사용합니다.  

응용 프로그램이 제한을 방지하도록 설계된 경우 가장 낮은 대기 시간을 얻을 수 있습니다. 그러나 디스크에 대해 보류 중인 I/O 요청 수가 너무 작으면 응용 프로그램이 디스크에서 사용할 수 있는 최대 IOPS 및 처리량 수준을 활용할 수 없습니다.

다음 예제에서는 제한 수준을 계산하는 방법을 보여 줍니다. 모든 계산은 256KB의 I/O 단위 크기를 기반으로 합니다.

### <a name="example-1"></a>예 1
응용 프로그램이 P10 디스크에서 초당 16KB 크기의 495개 I/O를 처리했습니다. I/O 단위는 495 IOPS로 계산됩니다. 동일한 초에 2MB I/O를 시도하면 총 I/O 단위 수는 495+8 IOPS가 됩니다. 이는 I/O 단위 크기가 256KB일 경우 2MB I/O = 2,048KB/256KB = 8 I/O 단위이기 때문입니다. 495+8의 합계가 디스크 한도인 500개 IOPS를 초과하므로 제한이 발생합니다.

### <a name="example-2"></a>예 2
응용 프로그램이 P10 디스크에서 256KB 크기의 400개 I/O를 처리했습니다. 소비되는 총 대역폭은 (400 &#215; 256)/1,024KB=100MB/초입니다. P10 디스크의 처리량 제한은 100MB/초입니다. 응용 프로그램이 해당 초에 추가 I/O 작업을 수행하려고 하면 할당된 한도를 초과하므로 제한됩니다.

### <a name="example-3"></a>예 3
연결된 두 P30 디스크와 DS4 VM이 있습니다. 각 P30 디스크는 처리량이 200MB/초입니다. 하지만 DS4 VM은 256MB/초의 총 디스크 대역폭 용량입니다. 동시에 이 DS4 VM에서 최대 처리량으로 연결된 두 디스크를 운용할 수 없습니다. 이를 해결하려면 한 디스크에서는 200MB/초, 다른 디스크에서는 56MB/초의 트래픽을 유지할 수 있습니다. 디스크 트래픽의 합계는 256MB/초 이상으로 이동하며 디스크 트래픽이 제한됩니다.

> [!NOTE]
> 디스크 트래픽이 대체로 작은 I/O 크기로 구성된 경우 응용 프로그램이 처리량 한도 전에 IOPS 한도에 도달할 가능성이 있습니다. 그러나 디스크 트래픽이 대체로 큰 I/O 크기로 구성된 경우에는 응용 프로그램이 IOPS 한도 대신 처리량 한도에 먼저 도달할 가능성이 있습니다. 최적 I/O 크기를 사용하여 응용 프로그램의 IOPS 및 처리 용량을 최대화할 수 있습니다. 또한 디스크에 대해 보류 중인 I/O 요청 수도 제한할 수 있습니다.
> 

Premium Storage를 사용한 고성능 설계에 대한 자세한 내용은 [Premium Storage를 사용한 성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)을 참조하세요.

## <a name="snapshots-and-copy-blob"></a>Blob 스냅숏 생성 및 복사

Storage 서비스에서 VHD 파일은 페이지 Blob입니다. 페이지 Blob의 스냅숏을 만들어서 다른 위치(예: 다른 저장소 계정)에 복사할 수 있습니다.

### <a name="unmanaged-disks"></a>관리되지 않는 디스크

관리되지 않는 프리미엄 디스크에 대한 [증분 스냅숏](../articles/virtual-machines/linux/incremental-snapshots.md)은 표준 저장소에서 스냅숏을 사용하는 것과 같은 방식으로 만듭니다. Premium Storage는 복제 옵션으로 로컬 중복 저장소만 지원합니다. 스냅숏을 만든 후 지역 중복 표준 저장소 계정에 이러한 스냅숏을 복사하는 것이 좋습니다. 자세한 내용은 [Azure Storage 중복 옵션](../articles/storage/common/storage-redundancy.md)을 참조하세요.

디스크가 VM에 연결되어 있으면 디스크에서 일부 API 작업이 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 경우 해당 Blob에 대해 [Blob 복사](/rest/api/storageservices/Copy-Blob) 작업을 수행할 수 없습니다. 대신 [스냅숏 Blob](/rest/api/storageservices/Snapshot-Blob) REST API를 사용하여 Blob의 스냅숏을 먼저 만듭니다. 그런 다음 스냅숏의 [Blob 복사](/rest/api/storageservices/Copy-Blob)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 필요한 작업을 수행할 수 있습니다.

프리미엄 저장소 Blob 스냅숏에 다음 한도가 적용됩니다.

| 프리미엄 저장소 한도 | 값 |
| --- | --- |
| Blob당 최대 스냅숏 수 | 100 |
| 스냅숏의 Storage 계정 용량<br>(스냅숏의 데이터만 포함합니다. 기본 Blob의 데이터는 포함하지 않습니다.) | 10TB |
| 연속 스냅숏 사이의 최소 시간 | 10분 |

스냅숏의 지역 중복 복사본을 유지하려면 AzCopy 또는 Blob 복사를 사용하여 프리미엄 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](../articles/storage/common/storage-use-azcopy.md) 및 [Blob 복사](/rest/api/storageservices/Copy-Blob)를 참조하세요.

프리미엄 저장소 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 [Azure Premium Storage로 Blob service 작업](http://go.microsoft.com/fwlink/?LinkId=521969)을 참조하세요.

### <a name="managed-disks"></a>관리 디스크

관리 디스크에 대한 스냅숏은 관리 디스크의 읽기 전용 복사본입니다. 스냅숏은 표준 관리 디스크로 저장됩니다. 현재 관리 디스크에 대해 [증분 스냅숏](../articles/virtual-machines/windows/incremental-snapshots.md)은 지원되지 않습니다. 관리 디스크에 대한 스냅숏을 만드는 방법을 알아보려면 [Windows에서 관리 스냅숏을 사용하여 Azure Managed Disk로 저장된 VHD 복사본 만들기](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) 또는 [Linux에서 관리 스냅숏을 사용하여 Azure Managed Disk로 저장된 VHD 복사본 만들기](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)를 참조하세요.

관리 디스크가 VM에 연결되어 있으면 디스크에서 일부 API 작업이 허용되지 않습니다. 예를 들어 디스크가 VM에 연결되어 있는 동안 공유 액세스 서명(SAS)을 생성하여 복사 작업을 수행할 수 없습니다. 대신 디스크 스냅숏을 먼저 만든 다음 스냅샷 복사를 수행합니다. 또는 디스크를 분리한 다음 SAS를 생성하여 복사 작업을 수행할 수 있습니다.


## <a name="premium-storage-for-linux-vms"></a>Linux VM용 Premium Storage
다음 정보를 사용하여 Premium Storage에서 Linux VM을 설정할 수 있습니다.

**ReadOnly** 또는 **None**으로 캐시 설정을 한 모든 프리미엄 저장소 디스크의 경우, Premium Storage에 대한 확장성 목표를 수행하려면 파일 시스템을 탑재할 때 “barrier(장벽)”를 사용하지 않도록 설정해야 합니다. 프리미엄 저장소 디스크에 쓰기는 이러한 캐시 설정에 대해 지속되기 때문에 이 시나리오에는 barrier(장벽)가 필요하지 않습니다. 쓰기 요청이 성공적으로 완료되면 데이터는 영구 저장소에 작성됩니다. “barrier(장벽)”를 사용하지 않도록 설정하려면 다음 방법 중 하나를 사용합니다. 파일 시스템에 대해 하나를 선택합니다.
  
* **reiserFS**에 대해 barrier를 사용하지 않도록 설정하려면 `barrier=none` 탑재 옵션을 사용합니다. (barrier를 사용하도록 설정하려면 `barrier=flush`를 사용합니다.)
* **ext3/ext4**에 대해 barrier를 사용하지 않도록 설정하려면 `barrier=0` 탑재 옵션을 사용합니다. (barrier를 사용하도록 설정하려면 `barrier=1`를 사용합니다.)
* **XFS**에 대해 barrier를 사용하지 않도록 설정하려면 `nobarrier` 탑재 옵션을 사용합니다. (barrier를 사용하도록 설정하려면 `barrier`를 사용합니다.)
* **ReadWrite**으로 캐시가 설정된 프리미엄 저장소 디스크의 경우 쓰기 지속성을 위해 barrier를 사용하도록 설정합니다.
* 볼륨 레이블의 경우 VM을 다시 시작한 후 유지하려면 디스크에 UUID(Universally Unique Identifier) 참조로 /etc/fstab을 업데이트해야 합니다. 자세한 내용은 [관리 디스크를 Linux VM에 추가](../articles/virtual-machines/linux/add-disk.md)를 참조하세요.

다음 Linux 배포판은 Azure Premium Storage로 유효성이 검사되었습니다. Premium Storage 사용 시 더 나은 성능 및 안정성을 위해 적어도 이러한 버전 중 하나(또는 더 최신 버전)로 VM을 업그레이드하는 것이 좋습니다. 버전 중 일부는 Azure용 최신 LIS(Linux 통합 서비스) v4.0이 필요합니다. 배포판을 다운로드하여 설치하려면 다음 표에 나와 있는 링크를 따라가세요. 유효성 검사가 완료됨에 따라 목록에 이미지가 추가됩니다. 유효성 검사 시 이미지마다 성능이 다른 것으로 나타납니다. 성능은 워크로드 특성 및 이미지 설정에 따라 달라집니다. 다른 종류의 워크로드에 대해 서로 다른 이미지가 조정됩니다.

| 배포 | 버전 | 지원되는 커널 | 세부 정보 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 필요](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *다음 섹션의 참고를 참조하세요.* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 권장](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *다음 섹션의 참고를 참조하세요.* |
| RHEL(Red Hat Enterprise Linux) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 또는 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 또는 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 또는 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS용 LIS 드라이버

OpenLogic CentOS VM을 실행하는 경우 다음 명령을 실행하여 최신 드라이버를 설치합니다.

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

새 드라이버를 활성화하려면 컴퓨터를 다시 시작합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Premium Storage를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* **프리미엄 저장소 디스크 및 Blob 크기**

    프리미엄 저장소 디스크 또는 Blob에 대한 청구는 프로비전된 디스크 또는 Blob 크기에 따라 달라집니다. Azure는 프로비전된 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 자세한 내용은 [Premium Storage 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets)의 표를 참조하세요. 각각의 디스크는 지원되는 프로비전된 디스크 크기에 매핑되고 그에 따라 요금이 청구됩니다. 프로비전된 디스크에 대한 청구는 Premium Storage 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 P10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 P10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양이나 사용한 IOPS 및 처리량에 관계없이 적용됩니다.

* **프리미엄 관리되지 않는 디스크 스냅숏**

    프리미엄 관리되지 않는 디스크의 스냅숏은 스냅숏이 사용한 추가 용량에 대해 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](/rest/api/storageservices/Snapshot-Blob)를 참조하세요.

* **프리미엄 관리 디스크 스냅숏**

    관리 디스크에 대한 스냅숏은 디스크의 읽기 전용 복사본입니다. 디스크는 표준 관리 디스크로 저장됩니다. 스냅숏 비용은 표준 관리 디스크와 동일하게 책정됩니다. 예를 들어 128GB 프리미엄 관리 디스크의 스냅숏을 만드는 경우 스냅숏 비용은 128GB 표준 관리 디스크 비용과 같습니다.  

* **아웃바운드 데이터 전송**

    [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/)(Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

Premium Storage에 대한 가격 책정, Premium Storage 지원 VM 및 관리 디스크에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Azure Backup 지원 

지역적 재해 복구를 위해 [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) 및 GRS 저장소 계정을 백업 자격 증명 모음으로 사용하여 VM 디스크를 다른 지역에 백업해야 합니다.

시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들려면 Azure Backup을 사용합니다. 관리 및 관리되지 않는 디스크 모두에 Backup을 사용할 수 있습니다. 자세한 내용은 [관리되지 않는 디스크와 함께 VM용 Azure Backup](../articles/backup/backup-azure-vms-first-look-arm.md) 및 [관리 디스크와 함께 VM용 Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
Premium Storage에 대한 자세한 내용은 다음 문서를 참조하세요.