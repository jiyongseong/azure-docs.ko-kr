# <a name="azure-managed-disks-overview"></a>Azure Managed Disks 개요

Azure Managed Disks는 VM 디스크와 연결된 [저장소 계정](../articles/storage/common/storage-introduction.md)을 관리하여 Azure IaaS VM의 디스크 관리를 간소화합니다. 필요한 디스크의 유형([프리미엄](../articles/virtual-machines/windows/premium-storage.md) 또는 [표준](../articles/virtual-machines/windows/standard-storage.md))과 크기만 지정하면 Azure가 알아서 디스크를 만들고 관리해줍니다.

## <a name="benefits-of-managed-disks"></a>관리 디스크의 이점

Channel 9 비디오 [Better Azure VM Resiliency with Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)(Managed Disks를 사용하는 향상된 Azure VM 복원력)로 시작하여 관리 디스크 사용의 일부 이점을 살펴보겠습니다.
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>간단하고 확장성 있는 VM 배포

Managed Disks는 배후에서 저장소를 처리해줍니다. 이전에는 Azure VM의 디스크(VHD 파일)를 갖기 위해 저장소 계정을 만들어야 했습니다. 확장하는 경우에는 디스크를 포함하는 저장소의 IOPS 제한을 초과하지 않도록 저장소 계정을 추가로 만들었는지 확인해야 했습니다. Managed Disks로 저장소를 처리하면 저장소 계정 한도(예: 20,000 IOPS/계정)로 인해 더 이상 제한을 받지 않습니다. 사용자 지정 이미지(VHD 파일)를 여러 저장소 계정에 복사할 필요도 없습니다. 중앙 위치(Azure 지역당 하나의 저장소 계정)에서 저장소 계정을 관리할 수 있고 이를 통해 구독에 수백 개의 VM을 만들 수 있습니다.

Managed Disks를 사용하면 구독에 VM **디스크**를 10,000개까지 만들 수 있고 따라서 단일 구독에 수천 개의 **VM**을 만들 수 있습니다. 이 기능을 사용하면 Marketplace 이미지를 사용하여 VMSS에 VM을 천 개까지 만들 수 있기 때문에 [VMSS(Virtual Machine Scale Sets)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)의 확장성을 훨씬 더 높일 수도 있습니다.

### <a name="better-reliability-for-availability-sets"></a>가용성 집합에 대한 안정성 향상

Managed Disks는 단일 실패 지점을 피할 만큼 [가용성 집합의 VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 디스크를 서로 충분히 격리시켜서 가용성 집합에 대해 향상된 안정성을 제공합니다. 이 기능은 디스크를 다른 저장소 배율 단위(스탬프)에 자동으로 배치하여 구현됩니다. 스탬프가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 해당 스탬프의 디스크가 있는 VM 인스턴스만 실패합니다. 예를 들어 응용 프로그램을 5개의 VM에서 실행 중이고 VM이 가용성 집합 내에 있다고 가정해 보겠습니다. 이러한 VM의 디스크는 동일한 스탬프에 저장되지 않습니다. 따라서 스탬프 하나가 작동이 중단되면 다른 응용 프로그램 인스턴스가 계속해서 실행됩니다.

### <a name="highly-durable-and-available"></a>뛰어난 내구성 및 가용성

Azure 디스크는 99.999% 가용성을 위해 설계되었습니다. 데이터 복제본을 3개 만들어 내구성이 뛰어나므로 데이터에 대해 안심하셔도 됩니다. 즉, 하나 또는 두 개의 복제본에서 문제가 발생하는 경우 나머지 복제본을 사용하면 데이터의 지속성과 우수한 내결함성을 확보할 수 있습니다. 이러한 아키텍처를 통해 Azure는 IaaS 디스크에 엔터프라이즈급 내구성을 지속적으로 제공할 뿐만 아니라 연간 실패율 0%로 업계 최고를 자랑합니다. 

### <a name="granular-access-control"></a>세부적인 액세스 제어

[Azure 역할 기반 Access Control(RBAC)](../articles/role-based-access-control/overview.md)을 사용하여 관리 디스크에 대한 특정 권한을 한 명 이상의 사용자에게 할당할 수 있습니다. Managed Disks는 읽기, 쓰기(만들기/업데이트), 삭제, [SAS(공유 액세스 서명) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) 검색 등 디스크에 대한 다양한 작업을 노출합니다. 업무를 수행하는 데 필요한 작업에만 액세스 권한을 부여할 수 있습니다. 예를 들어 관리 디스크를 저장소 계정에 복사하지 말아야 하는 경우에는 해당 관리 디스크에 대한 내보내기 작업에 액세스를 부여하지 않도록 선택할 수 있습니다. SAS URI를 사용하여 관리 디스크를 복사할 수 없도록 하기 위해 관리 디스크에 해당 권한을 부여하지 않도록 선택할 수 있습니다.

### <a name="azure-backup-service-support"></a>Azure Backup 서비스 지원
Azure Backup 서비스를 Managed Disks와 함께 사용하여 시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들 수 있습니다. Managed Disks는 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하며 즉, 단일 지역 내에 데이터 복사본 3개를 유지합니다. 지역적 재해 복구를 위해 [Azure Backup 서비스](../articles/backup/backup-introduction-to-azure-backup.md) 및 GRS 저장소 계정을 백업 자격 증명 모음으로 사용하여 VM 디스크를 다른 지역에 백업해야 합니다. 현재 Azure Backup에서는 최대 1TB의 데이터 디스크 크기를 백업하도록 지원합니다. 자세한 내용은 [Managed Disks로 VM에 Azure Backup 서비스 사용](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)을 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Managed Disks를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.
* 저장소 유형

* 디스크 크기

* 트랜잭션 수

* 아웃바운드 데이터 전송

* 관리 디스크 스냅숏(전체 디스크 복사)

좀 더 자세히 살펴보겠습니다.

**저장소 유형:** Managed Disks에는 [프리미엄](../articles/virtual-machines/windows/premium-storage.md)(SSD 기반) 및 [표준](../articles/virtual-machines/windows/standard-storage.md)(HDD 기반)이라는 2개의 성능 계층이 제공됩니다. 관리 디스크에 대한 요금 청구는 디스크에 어떤 유형의 저장소를 선택했는지에 따라 달라집니다.


**디스크 크기**: 관리 디스크에 대한 요금 청구는 프로비전된 디스크 크기에 따라 달라집니다. Azure는 프로비전된 크기(올림)를 아래 테이블에 지정된 대로 가장 가까운 Managed Disks 옵션에 매핑합니다. 각각의 관리 디스크는 지원되는 프로비전된 크기 중 하나에 매핑되고 그에 따라 요금이 청구됩니다. 예를 들어 표준 관리 디스크를 만들고 프로비전된 크기를 200GB로 지정하면 S15 디스크 유형의 가격에 따라 요금이 청구됩니다.

프리미엄 관리 디스크에 사용 가능한 디스크 크기는 다음과 같습니다.

| **프리미엄 관리 <br>디스크 유형** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| 디스크 크기        | 32GiB   | 64GiB   | 128GiB  | 256GiB  | 512GiB  | 1024GiB(1TiB) | 2048GiB(2TiB) | 4095GiB(4TiB) | 


표준 관리 디스크에 사용 가능한 디스크 크기는 다음과 같습니다.

| **표준 관리 <br>디스크 유형** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------| 
| 디스크 크기        | 32GiB  | 64GiB  | 128GiB | 256GiB | 512GiB | 1024GiB(1TiB) | 2048GiB(2TiB) | 4095GiB(4TiB) | 


**트랜잭션 수**: 표준 관리 디스크에서 수행하는 트랜잭션 수에 따라 요금이 청구됩니다. 프리미엄 관리 디스크에 대한 트랜잭션 비용은 없습니다.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

Managed Disks 가격 책정에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)을 참조하세요.


## <a name="managed-disk-snapshots"></a>관리 디스크 스냅숏

관리 스냅숏은 기본적으로 표준 관리 디스크로 저장되는 관리 디스크의 완전한 읽기 전용 복사본입니다. 스냅숏을 사용하면 관리 디스크를 언제든지 백업할 수 있습니다. 이 스냅숏은 원본 디스크와 별도로 존재하고 새 Managed Disks를 만드는 데 사용될 수도 있습니다. 사용된 크기에 따라 요금이 청구됩니다. 예를 들어 프로비전된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 Managed Disks의 스냅숏을 만들 경우 사용된 10GiB의 데이터 크기에 대해서만 스냅숏 요금이 청구됩니다.  

[증분 스냅숏](../articles/virtual-machines/windows/incremental-snapshots.md)은 현재 Managed Disks에 지원되지 않지만 나중에 지원될 예정입니다.

Managed Disks를 사용하여 스냅숏을 만드는 방법에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [Windows에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux에서 스냅숏을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>이미지

Managed Disks는 관리되는 사용자 지정 이미지 만들기도 지원합니다. 저장소 계정의 사용자 지정 VHD에서 이미지를 만들거나 일반화된(시스템에서 준비된) VM에서 직접 만들 수 있습니다. OS와 데이터 디스크를 모두 포함하여 VM과 연결된 모든 Managed Disks를 하나의 이미지에 캡처합니다. 이렇게 하면 저장소 계정을 복사하거나 관리할 필요 없이 사용자 지정 이미지를 사용하여 수백 개의 VM을 만들 수 있습니다.

이미지 만들기에 대한 내용은 다음 문서를 참조하세요.
* [Azure에서 일반화된 VM의 관리 이미지를 캡처하는 방법](../articles/virtual-machines/windows/capture-image-resource.md)
* [Azure CLI 2.0을 사용하여 Linux 가상 머신을 일반화하고 캡처하는 방법](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>이미지 및 스냅숏

“이미지”라는 단어가 VM과 함께 사용되는 경우가 많은데 이제 “스냅숏”이라는 단어도 볼 수 있습니다. 이 둘의 차이를 이해하는 것이 중요합니다. Managed Disks를 사용하면 할당이 취소되어 일반화된 VM의 이미지를 만들 수 있습니다. 이 이미지에는 VM에 연결된 모든 디스크가 포함됩니다. 이 이미지를 사용하여 새 VM을 만들 수 있고 여기에는 모든 디스크가 포함됩니다.

스냅숏은 스냅숏 생성 시점의 디스크 복사본입니다. 이것은 하나의 디스크에만 해당됩니다. VM의 디스크가 하나(OS)뿐인 경우 이 VM의 스냅숏 또는 이미지를 만들고 이 스냅숏 또는 이미지로부터 VM을 만들 수 있습니다.

VM의 디스크가 5개이고 스트라이프된 경우는 어떨까요? 각 디스크의 스냅숏을 만들 수 있지만 VM 내에서 디스크의 상태가 인식되지는 못하며 스냅숏은 해당되는 하나의 디스크에 대해서만 인식합니다. 이런 경우 스냅숏이 서로 조정될 필요가 있지만 현재 이 기능은 지원되지 않습니다.

## <a name="managed-disks-and-encryption"></a>Managed Disks 및 암호화

Managed Disks와 관련하여 논의할 두 종류의 암호화가 있습니다. 첫 번째는 Storage 서비스에서 수행하는 SSE(Storage 서비스 암호화)이고, 두 번째는 VM에 대한 OS 및 데이터 디스크에서 사용할 수 있는 Azure Disk Encryption입니다.

### <a name="storage-service-encryption-sse"></a>SSE(Storage 서비스 암호화)

[Azure Storage 서비스 암호화](../articles/storage/common/storage-service-encryption.md)를 사용하여 미사용 암호화를 제공하고 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. SSE는 Managed Disks를 사용할 수 있는 모든 지역에서 모든 Managed Disks, 스냅숏 및 이미지에 기본적으로 사용됩니다. 2017년 6월 10일부터 기존 관리 디스크에 기록된 모든 새 관리 디스크/스냅숏/이미지 및 새 데이터는 기본으로 Microsoft에서 관리되는 키로 자동으로 미사용 암호화됩니다. 자세한 내용은 [Managed Disks FAQ 페이지](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)를 참조하세요.


### <a name="azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)

Azure Disk Encryption을 사용하면 IaaS Virtual Machines에서 사용되는 OS 및 데이터 디스크를 암호화할 수 있습니다. 여기에 Managed Disks가 포함됩니다. Windows의 경우 업계 표준의 BitLocker 암호화 기술을 사용하여 드라이브가 암호화됩니다. Linux의 경우 DM-Crypt 기술을 사용하여 디스크가 암호화됩니다. 이 기술은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다. 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](../articles/security/azure-security-disk-encryption.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Managed Disks에 대한 자세한 내용은 다음 문서를 참조하세요.

### <a name="get-started-with-managed-disks"></a>Managed Disks 시작

* [Resource Manager 및 PowerShell을 사용하여 VM 만들기](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Azure CLI 2.0을 사용하여 Linux VM 만들기](../articles/virtual-machines/linux/quick-create-cli.md)

* [PowerShell을 사용하여 관리 데이터 디스크를 Windows VM에 연결](../articles/virtual-machines/windows/attach-disk-ps.md)

* [관리 디스크를 Linux VM에 추가](../articles/virtual-machines/linux/add-disk.md)

* [Managed Disks PowerShell 샘플 스크립트](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Azure Resource Manager 템플릿에서 Managed Disks 사용](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Managed Disks 저장소 옵션 비교

* [프리미엄 저장소 및 디스크](../articles/virtual-machines/windows/premium-storage.md)

* [표준 저장소 및 디스크](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>운영 가이드

* [AWS 및 기타 플랫폼에서 Azure의 Managed Disks로 마이그레이션](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Azure VM을 Azure의 관리 디스크로 변환](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
