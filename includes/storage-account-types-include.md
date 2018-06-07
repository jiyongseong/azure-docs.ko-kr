저장소 계정에는 다음과 같은 두 종류가 있습니다.

### <a name="general-purpose-storage-accounts"></a>범용 Storage 계정
범용 Storage 계정에서는 단일 계정의 테이블, 큐, 파일, Blob, Azure 가상 머신 디스크 등, Azure Storage 서비스에 액세스할 수 있습니다. 이 저장소 계정이 유형에는 다음과 같이 두 가지 성능 계층이 있습니다.

* 테이블, 큐, 파일, Blob 및 Azure 가상 컴퓨터 디스크를 저장할 수 있는 표준 저장소 성능 계층
* 현재 Azure 가상 머신 디스크만 지원하는 프리미엄 저장소 성능 계층. Premium Storage의 자세한 개요는 [Premium Storage: Azure Virtual Machine 작업을 위한 고성능 저장소](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요.

### <a name="blob-storage-accounts"></a>Blob Storage 계정
Blob Storage 계정은 Azure Storage에서 Blob와 같은 구조화되지 않은 데이터(개체) 저장을 위한 특수 Storage 계정입니다. Blob 저장소 계정은 기존 범용 저장소 계정과 유사합니다. 블록 Blob과 연결 Blob에 대한 100% API 일관성을 포함하여 현재 제공되는 뛰어난 내구성, 가용성, 확장성은 모두 같습니다. 블록 또는 연결 Blob 저장소만 필요한 응용 프로그램의 경우 Blob 저장소 계정을 사용하는 것이 좋습니다.

> [!NOTE]
> Blob 저장소 계정은 블록 및 추가 Blob만 지원하고 페이지 Blob은 지원하지 않습니다.
> 
> 

Blob 저장소 계정은 계정을 만들고 나중에 필요에 따라 수정하는 동안 지정할 수 있는 **액세스 계층** 속성을 표시합니다. 데이터 액세스 패턴에 따라 다음 두 가지 액세스 계층 유형을 지정할 수 있습니다.

* 저장소 계정의 개체가 더 자주 액세스됨을 의미하는 **핫** 액세스 계층. 더 저렴한 액세스 비용으로 데이터를 저장할 수 있습니다.
* 저장소 계정의 개체가 덜 액세스됨을 의미하는 **쿨** 액세스 계층. 더 저렴한 데이터 저장 비용으로 데이터를 저장할 수 있습니다.

데이터의 사용 패턴에 변화가 있으면 언제든 이 액세스 계층 간을 전환할 수 있습니다. 액세스 계층을 변경하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [Pricing and billing for Blob storage accounts](../articles/storage/common/storage-account-options.md#pricing-and-billing) (Blob 저장소 계정에 대한 가격 책정 및 청구)를 참조하세요.

Blob Storage 계정에 대한 자세한 내용은 [Azure Blob Storage: 쿨 및 핫 계층](../articles/storage/blobs/storage-blob-storage-tiers.md)을 참조하세요.

저장소 계정을 만들려면 먼저 다양한 Azure 서비스에 대한 액세스 권한을 제공하는 계획인 Azure 구독이 있어야 합니다. [무료 계정](https://azure.microsoft.com/pricing/free-trial/)으로 Azure를 시작할 수 있습니다. 구독 계획을 구매하기로 결정하는 경우 다양한 [구입 옵션](https://azure.microsoft.com/pricing/purchase-options/)중에서 선택할 수 있습니다. [MSDN 구독자](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)는 Azure Storage를 포함한 Azure 서비스에 사용할 수 있는 무료 월별 크레딧을 받습니다. 볼륨 가격에 대한 자세한 내용은 [Azure Storage 가격 책정 ](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

저장소 계정을 만드는 방법을 알아보려면 [저장소 계정 만들기](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) 를 참조하세요. 단일 구독으로 고유하게 명명된 저장소 계정을 200개까지 만들 수 있습니다. Storage 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../articles/storage/common/storage-scalability-targets.md) 를 참조하세요.

