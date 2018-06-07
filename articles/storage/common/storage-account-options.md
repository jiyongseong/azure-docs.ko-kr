---
title: Azure Storage 계정 옵션 | Microsoft Docs
description: Azure Storage를 사용하기 위한 옵션을 이해합니다.
services: storage
author: hux
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 05/02/2018
ms.author: hux
ms.openlocfilehash: 69da15b98e6c519a3a8352cc7ca7212286cb4e52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-storage-account-options"></a>Azure Storage 계정 옵션

## <a name="overview"></a>개요
Azure Storage에서는 다양한 가격 책정 및 기능을 지원하는 세 가지 고유한 계정 옵션을 제공합니다. 저장소 계정을 만들기 전에 응용 프로그램에 적합한 옵션을 결정하는 데 이러한 차이점을 고려합니다. 세 가지 다른 저장소 계정 옵션은 다음과 같습니다.

* **범용 v2(GPv2)** 계정 
* **범용 v1(GPv1)** 계정
* **Blob 저장소** 계정

각 유형의 계정은 다음 섹션에 자세히 설명되어 있습니다.

## <a name="storage-account-options"></a>Storage 계정 옵션

### <a name="general-purpose-v2"></a>범용 v2

범용 v2(GPv2) 계정은 Blob, 파일, 큐 및 테이블에 대한 모든 최신 기능을 지원하는 저장소 계정입니다. GPv2 계정은 모든 API 및 GPv1 및 Blob 저장소 계정에서 지원되는 기능을 지원합니다. 또한 해당 계정 유형에서 동일한 내구성, 가용성, 확장성 및 성능 기능을 지원합니다. GPv2 계정에 대한 가격 책정은 기가바이트당 가장 낮은 가격 및 업계에서 경쟁력 있는 트랜잭션 가격을 제공하도록 설계되었습니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 GPv1 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 

GPv2 저장소 계정의 블록 Blob의 경우 액세스 패턴에 따라 계정 수준에서 핫 및 쿨 저장소 계정 중에 하나를 선택하고 Blob 수준에서 핫, 쿨 및 보관 계층 중에 하나를 선택할 수 있습니다. 핫, 쿨 및 보관 저장소 계층에 매우 드물게, 드물게 그리고 자주 액세스하는 데이터를 저장하여 비용을 최적화합니다. 

GPv2 저장소 계정은 계정 수준에서 **액세스 계층** 특성을 노출하며, 기본 저장소 계정 계층을 **핫** 또는 **쿨**로 지정합니다. 기본 저장소 계정 계층은 Blob 수준에서 명시적 계층 집합이 없는 모든 Blob에 적용됩니다. 데이터의 사용 패턴에 변화가 있으면 언제든 이 저장소 계층 간을 전환할 수 있습니다. **보관 계층**은 Blob 수준에서만 적용할 수 있습니다.

> [!NOTE]
> 저장소 계층을 변경하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [가격 책정 및 대금 청구](#pricing-and-billing)를 참조하세요.
>
> 대부분의 시나리오에 대한 Blob 저장소 계정에 범용 v2 저장소 계정을 사용하는 것이 좋습니다.

### <a name="upgrade-a-storage-account-to-gpv2"></a>저장소 계정을 GPv2로 업그레이드

사용자는 언제든지 PowerShell 또는 Azure CLI를 사용하여 GPv1 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 이 변경 내용은 되돌릴 수 없으며 다른 변경 내용이 허용되지 않습니다.

#### <a name="upgrade-with-powershell"></a>Powershell로 업그레이드

PowerShell을 사용하여 GPv1 계정을 GPv2 계정으로 업그레이드하려면 먼저 최신 버전의 **AzureRm.Storage** 모듈을 사용하도록 PowerShell을 업데이트합니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 참조하세요. 그런 다음 리소스 그룹의 이름 및 저장소 계정을 대체하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Azure CLI를 사용하여 업그레이드

Azure CLI를 사용하여 GPv1 계정을 GPv2 계정으로 업그레이드하려면 먼저 최신 버전의 Azure CLI를 설치합니다. CLI 설치에 대한 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. 그런 다음 리소스 그룹의 이름 및 저장소 계정을 대체하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>범용 v1

범용 v1(GPv1) 계정은 모든 Azure Storage 서비스에 대한 액세스를 제공하지만 최신 기능 또는 기가바이트당 가장 낮은 가격 책정이 포함되지 않을 수 있습니다. 예를 들어 쿨 저장소 및 보관 저장소는 GPv1에서 지원되지 않습니다. GPv1 트랜잭션의 경우 가격 책정이 낮으므로 높은 변동 또는 높은 읽기 비율을 가진 워크로드가 이 계정 형식에서 유용할 수 있습니다.

범용 v1(GPv1) 저장소 계정은 가장 오래된 유형의 저장소 계정이며 클래식 배포 모델에서 사용할 수 있는 유일한 유형입니다. 

### <a name="blob-storage-accounts"></a>Blob 저장소 계정

Blob 저장소 계정은 GPv2와 동일한 블록 Blob 기능을 지원하지만 블록 Blob에 대해서만 지원됩니다. 가격 책정은 범용 v2 계정에 대한 가격 책정과 비슷합니다. 고객은 Blob 저장소 계정과 GPv2 간에 가격 책정 차이를 검토하고 GPv2로 업그레이드하는 것이 좋습니다. 이 업그레이드는 취소할 수 없습니다.

> [!NOTE]
> Blob 저장소 계정은 블록 및 추가 Blob만 지원하고 페이지 Blob은 지원하지 않습니다.

## <a name="recommendations"></a>권장 사항

저장소 계정에 대한 자세한 내용은 [Azure 저장소 계정 정보](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

블록 또는 연결 Blob 저장소에만 필요한 응용 프로그램의 경우 GPv2 저장소 계정을 사용하여 차별화된 계층형 저장소 가격 책정 모델을 활용하는 것이 좋습니다. 그러나 다음과 같은 특정 시나리오에서 GPv1을 사용하는 것이 좋습니다.

* 여전히 클래식 배포 모델을 사용해야 합니다. Blob 저장소 계정은 Azure 리소스 관리자 배포 모델을 통해서만 사용할 수 있습니다.

* GPv1보다 GPv2 및 Blob 저장소 계정에서 많은 비용이 드는 대량의 트랜잭션이나 지역 복제 대역폭을 사용하고, GB 저장소의 비용을 절감할 수 있는 저장소가 충분하지 않습니다.

* 2014-02-14 이전 버전인 [Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 나, 4.x 미만인 클라이언트 라이브러리를 사용하며 응용 프로그램을 업그레이드할 수 없습니다.

> [!NOTE]
> Blob 저장소 계정은 현재 모든 Azure 지역에서 지원됩니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구
모든 저장소 계정에서는 각 Blob의 계층에 따라 Blob Storage에 가격 책정 모델을 사용합니다. 저장소 계정을 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* **저장소 비용**: 저장된 데이터 크기 외에도, 데이터 저장 비용은 저장소 계층에 따라 달라집니다. 계층이 차가워질수록 기가바이트당 비용이 감소합니다.

* **데이터 액세스 비용**: 계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 쿨 및 보관 저장소 계층에 있는 데이터의 경우 읽기에 대해 기가바이트당 데이터 액세스 요금이 부과됩니다.

* **트랜잭션 비용**: 계층이 차가워질수록 증가하는 모든 계층에 대해 트랜잭션당 요금이 부과됩니다.

* **지역에서 복제 데이터 전송 비용**: 이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.

* **아웃바운드 데이터 전송 비용**: 아웃바운드 데이터 전송(Azure 지역 밖으로 전송된 데이터)에서는 기가바이트당 요금을 기준으로 대역폭 사용 요금이 발생하며 범용 저장소 계정과 같습니다.

* **저장소 계층 변경**: 계정 저장소 계층을 쿨에서 핫으로 변경하면 저장소 계정에서 모든 기존 데이터를 읽는 것과 같은 요금이 발생합니다. 하지만 계정 저장소 계층을 핫에서 쿨로 변경하면 모든 데이터를 쿨 계층에 쓰는 것과 동일한 요금이 부과됩니다(GPv2 계정에만 해당).

> [!NOTE]
> Blob 저장소 계정의 가격 책정 모델에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지를 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지를 참조하세요.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

이 섹션에서는 Azure Portal을 사용하여 다음 시나리오를 보여줍니다.

* [GPv2 저장소 계정을 만드는 방법](#create-a-gpv2-storage-account-using-the-azure-portal)
* [GPv1 또는 Blob 저장소 계정을 GPv2 저장소 계정으로 변환하는 방법](#convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal)
* [GPv2 저장소 계정에서 계정을 설정하는 방법](#change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal)
* [BLOB 저장소 또는 GPv2 저장소 계정에서 BLOB 계층을 설정하는 방법](#change-the-storage-tier-of-a-blob-using-the-azure-portal)

이 설정이 전체 저장소 계정에 적용되므로 다음 예제에서 보관할 액세스 계층을 설정할 수 없습니다. 보관은 특정 Blob에만 설정할 수 있습니다.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Azure Portal을 사용하여 GPv2 저장소 계정 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **리소스 만들기** > **데이터 + 저장소** > **Storage 계정**을 차례로 선택합니다.

3. 저장소 계정의 이름을 입력합니다.

    이 이름은 전역적으로 고유해야 합니다. 저장소 계정의 개체에 액세스하는 데 사용되는 URL의 일부로 사용됩니다.  

4. **Resource Manager** 를 배포 모델로 선택합니다.

    계층화된 저장소는 리소스 관리자 저장소 계정과 함께 사용할 수 있습니다. 리소스 관리자는 새 리소스에 권장되는 배포 모델입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.  

5. **계정 종류** 드롭다운 목록에서 **범용 v2**를 선택합니다.

    GPv2를 선택하면 성능 계층은 표준으로 설정됩니다. 계층화된 저장소는 프리미엄 성능 계층과 함께 사용할 수 없습니다.

6. 저장소 계정의 복제 옵션을 **LRS**, **ZRS**, **GRS** 또는 **RA-GRS**로 선택합니다. 기본값은 **RA-GRS**입니다.

    LRS = 로컬 중복 저장소, ZRS = 영역 중복 저장소, GRS = 지역 중복 저장소(2개 지역), RA-GRS = 읽기 액세스 지역 중복 저장소입니다(두 번째 저장소에 대한 읽기 권한이 있는 2개 지역).

    Azure Storage 복제 옵션에 대한 자세한 내용은 아래의 [Azure Storage 복제](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

7. 요구 사항에 적합한 저장소 계층을 선택합니다. 즉 **액세스 계층**을 **쿨** 또는 **핫**으로 설정합니다. 기본값은 **핫**입니다.

8. 새 저장소 계정을 만들려는 구독을 선택합니다.

9. 새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

10. 저장소 계정에 대한 지역을 선택합니다.

11. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.

### <a name="convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Azure Portal을 사용하여 GPv1 또는 Blob 저장소 계정을 GPv2 저장소 계정으로 변환

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정으로 이동하려면 **모든 리소스**를 선택하고 저장소 계정을 선택합니다.

3. 설정 섹션에서 **구성**을 클릭합니다.

4. **계정 종류** 아래에서 **업그레이드**를 클릭합니다.

5. **업그레이드 확인**에서 계정 이름을 입력합니다. 

5. 블레이드 하단에서 업그레이드를 클릭합니다.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Azure Portal을 사용하여 GPv2 저장소 계정의 저장소 계층 변경

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정으로 이동하려면 **모든 리소스**를 선택하고 저장소 계정을 선택합니다.

3. 설정 블레이드에서 **구성** 을 클릭하여 계정 구성을 보기 및/또는 변경합니다.

4. 요구 사항에 적합한 저장소 계층을 선택합니다. 즉 **액세스 계층**을 **쿨** 또는 **핫**으로 설정합니다.

5. 블레이드 위쪽에서 저장을 클릭합니다.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Azure Portal을 사용하여 Blob의 저장소 계층 변경

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정에서 Blob으로 이동하려면 **모든 리소스**를 선택하고 저장소 계정을 선택하고 컨테이너를 선택한 다음, Blob을 선택합니다.

3. Blob 속성 블레이드에서 **액세스 계층** 드롭다운 메뉴를 선택하여 **핫**, **쿨** 또는 **보관** 저장소 계층을 선택합니다.

5. 블레이드 위쪽에서 저장을 클릭합니다.

> [!NOTE]
> 저장소 계층을 변경하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [가격 책정 및 대금 청구](#pricing-and-billing)를 참조하세요.


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>GPv2 저장소 계정 평가 및 마이그레이션
이 섹션은 (GPv1과 비교하여) GPv2 저장소 계정 사용으로의 원활한 전환을 지원하려고 합니다. 두 가지 사용자 시나리오가 있습니다.

* 기존 GPv1 저장소 계정이 있고 올바른 저장소 계층을 사용하여 GPv2 저장소 계정에 대한 변경을 평가하려고 합니다.
* GPv2 저장소 계정을 사용하도록 결정했거나 이미 저장소 계정이 하나 있고 핫 또는 쿨 저장소 계층을 사용해야 하는지 여부를 평가하려고 합니다.

두 경우에 첫 번째 우선 순위는 GPv2 저장소 계정에 저장된 데이터를 저장하고 액세스하는 비용을 예측하고 현재 비용과 비교하는 것입니다.

## <a name="evaluating-gpv2-storage-account-tiers"></a>GPv2 저장소 계정 계층 평가

GPv2 저장소 계정에 저장된 데이터를 저장하고 액세스하는 비용을 예상하기 위해 기존 사용 패턴을 평가하거나 예상된 사용 패턴을 계산해야 합니다. 일반적으로 다음을 파악해야 합니다.

* 저장소 사용량 – 데이터 저장 규모 및 월 기준 변화

* 저장소 액세스 패턴 - 계정에서 읽고 쓰는 데이터의 크기(새 데이터 포함) 데이터 액세스에 사용되는 트랜잭션 양 및 트랜잭션 유형

## <a name="monitoring-existing-storage-accounts"></a>기존 저장소 계정 모니터링

기존 저장소 계정을 모니터링하고 이 데이터를 수집하기 위해 로깅을 수행하고 저장소 계정에 대한 메트릭 데이터를 제공하는 Azure 저장소 분석을 사용할 수 있습니다. 저장소 분석은 GPv1, GPv2 및 Blob 저장소 계정 형식의 저장소 서비스에 대한 요청과 관련하여 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장할 수 있습니다. 이 데이터는 동일한 저장소 계정에서 잘 알려진 테이블에 저장됩니다.

자세한 내용은 [저장소 분석 메트릭 정보](https://msdn.microsoft.com/library/azure/hh343258.aspx) 및 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)를 참조하세요.

> [!NOTE]
> Blob 저장소 계정은 해당 계정의 메트릭 데이터를 저장하고 액세스하는 경우에만 Table service 엔드포인트를 노출합니다. 

Blob 저장소에 대한 저장소 사용량을 모니터링하려면 용량 메트릭을 활성화해야 합니다.
이를 활성화하면 용량 데이터는 저장소 계정의 Blob service에 대해 매일 기록되고 동일한 저장소 계정 내에서 *$MetricsCapacityBlob* 테이블에 작성된 테이블 항목으로 기록됩니다.

Blob 저장소에 대한 데이터 액세스 패턴을 모니터링하려면 API에서 시간당 트랜잭션 메트릭을 활성화해야 합니다. 시간당 트랜잭션 메트릭을 활성화하면 API당 트랜잭션은 매시간 집계되며 동일한 저장소 계정 내에서 *$MetricsHourPrimaryTransactionsBlob* 테이블로 작성된 테이블 항목으로 기록됩니다. *$MetricsHourSecondaryTransactionsBlob* 테이블은 RA-GRS 저장소 계정을 사용하는 경우 보조 끝점에 트랜잭션을 기록합니다.

> [!NOTE]
> 블록 및 추가 Blob 데이터와 함께 페이지 Blob과 가상 머신 디스크, 또는 큐, 파일이나 테이블을 저장한 범용 저장소 계정이 있는 경우 이 예측 프로세스는 적용되지 않습니다. 용량 데이터는 다른 형식의 블록 Blob과 다르지 않으므로 기타 데이터 형식에 대한 용량 데이터를 제공하지 않습니다. 이러한 형식을 사용하는 경우 가장 최근의 청구서에서 수량을 보는 것이 대체 방법입니다.

데이터 소비 및 액세스 패턴을 대략적으로 파악하려면 정기적 사용을 나타내는 메트릭의 보존 기간을 선택하고 추정하는 것이 좋습니다. 한 가지 옵션은 7일 동안 메트릭 데이터를 보유하고 월말에 분석을 위해 매주 데이터를 수집하는 것입니다. 또 다른 옵션은 지난 30일 동안의 메트릭 데이터를 보존하고 30일 기간이 끝날 때 데이터를 수집하고 분석하는 것입니다.

메트릭 데이터 사용, 수집 및 보기에 대한 자세한 내용은 [Azure Storage 메트릭 사용 및 메트릭 데이터 보기](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

> [!NOTE]
> 분석 데이터 저장, 액세스 및 다운로드는 일반 사용자 데이터와 마찬가지로 청구됩니다.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>사용 현황 메트릭을 활용하여 비용 추정

#### <a name="storage-costs"></a>저장소 비용

*'data'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 사용자 데이터에서 사용하는 저장소 용량을 보여 줍니다. *'analytics'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 분석 로그에서 사용하는 저장소 용량을 보여 줍니다.

사용자 데이터 및 분석 로그(활성화된 경우)에서 소비되는 이 전체 용량은 저장소 계정에서 데이터 저장의 비용을 예측하는 데 사용할 수 있습니다. GPv1 저장소 계정에서 저장소 비용을 예상하는 데 동일한 메서드를 사용할 수도 있습니다.

#### <a name="transaction-costs"></a>트랜잭션 비용

트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalBillableRequests'* 의 합계는 특정 API에 대한 트랜잭션의 총 수를 나타냅니다. *예를 들어* 지정된 기간의 총 *'GetBlob'* 트랜잭션 수는 *'user;GetBlob'* 행 키가 있는 모든 항목에 대해 청구 가능한 요청의 총합으로 계산될 수 있습니다.

트랜잭션은 서로 다른 가격이 책정되므로 Blob Storage 계정에 대한 트랜잭션 비용을 예상하려면 트랜잭션을 3개의 그룹으로 세분화해야 합니다.

* *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* 및 *'CopyBlob'* 과 같은 쓰기 트랜잭션
* *'DeleteBlob'* 및 *'DeleteContainer'* 와 같은 삭제 트랜잭션
* 모든 다른 트랜잭션.

GPv1 저장소 계정에 대한 트랜잭션 비용을 예상하려면 작업/API에 관계 없이 모든 트랜잭션을 집계해야 합니다.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>데이터 액세스 및 지역에서 복제 데이터 전송 비용

저장소 분석은 저장소 계정에서 읽고 쓰는 데이터의 양을 제공하지 않지만 트랜잭션 메트릭 테이블을 확인하여 대략적으로 예상할 수 있습니다. 트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalIngress'* 의 합계는 특정 API에 대한 수신 데이터의 총 크기를 바이트로 나타냅니다. 마찬가지로 *'TotalEgress'* 의 합계는 송신 데이터의 총 크기를 바이트로 나타냅니다.

Blob Storage 계정에 대한 데이터 액세스 비용을 예상하려면 트랜잭션을 2개의 그룹으로 세분화해야 합니다.

* 저장소 계정에서 검색되는 데이터 크기는 주로 *'GetBlob'* 및 *'CopyBlob'* 작업에 대한 *'TotalEgress'* 합계를 확인하여 예상할 수 있습니다.

* 저장소 계정에 작성되는 데이터 크기는 주로 *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* 및 *'AppendBlock'* 작업에 대한 *'TotalIngress'* 합계를 확인하여 예상할 수 있습니다.

Blob Storage 계정에 대한 지역에서 복제 데이터 전송의 비용은 GRS 또는 RA-GRS 저장소 계정을 사용하는 경우 작성된 데이터의 양에 대한 추정을 사용하여 계산할 수도 있습니다.

> [!NOTE]
> 핫 또는 쿨 저장소 계층을 사용하는 비용 계산에 대한 자세한 예제는 *Azure Storage 가격 책정 페이지* 에 [Azure 저장소 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)합니다.

## <a name="migrating-existing-data"></a>기존 데이터 마이그레이션

GPv1 계정은 가동 중지 시간 또는 API를 변경하거나 데이터를 마이그레이션할 필요 없이 GPv2로 쉽게 업그레이드할 수 있습니다. 이러한 이유로 GPv1 계정을 Blob 저장소 계정 대신 GPv2 계정으로 마이그레이션하는 것이 좋습니다.

그러나 Blob 저장소 계정으로 마이그레이션해야 하는 경우 다음 지침을 사용할 수 있습니다.

Blob 저장소 계정은 저장 전용 블록 및 추가 Blob에 맞게 특별히 설정됩니다. 테이블, 큐, 파일 및 디스크는 물론 Blob도 저장할 수 있는 기존의 범용 저장소 계정은 Blob Storage 계정으로 변환할 수 없습니다. 저장소 계층을 사용하려면, 새로운 Blob Storage 계정을 만들어서 기존 데이터를 새로 만든 계정으로 마이그레이션해야 합니다.

다음 방법을 통해 기존 데이터를 온-프레미스 저장소 장치, 타사 클라우드 저장소 공급자 또는 기존 Azure 범용 저장소 계정에서 Blob Storage 계정으로 마이그레이션할 수 있습니다.

### <a name="azcopy"></a>AzCopy

AzCopy는 Azure Storage의 데이터를 고속으로 복사하기 위해 설계된 Windows 명령줄 유틸리티입니다. AzCopy를 사용하여 기존 범용 저장소 계정의 데이터를 Blob 저장소 계정으로 복사하거나, 온-프레미스 저장소 장치의 데이터를 Blob 저장소 계정에 업로드할 수 있습니다.

자세한 내용은 [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

### <a name="data-movement-library"></a>데이터 이동 라이브러리

.NET용 Azure Storage 데이터 이동 라이브러리는 AzCopy를 구동하는 핵심 데이터 이동 프레임워크를 기반으로 합니다. 이 라이브러리는 AzCopy와 유사하게 성능이 높고 안정적이며 사용이 간편한 데이터 전송 작업을 제공합니다. 이 라이브러리를 사용하여 AzCopy의 외부 인스턴스를 실행 및 모니터링하지 않고도 응용 프로그램에서 기본적으로 AzCopy가 제공하는 기능을 활용할 수 있습니다.

자세한 내용은 [.NET용 Azure Storage 데이터 이동 라이브러리](https://github.com/Azure/azure-storage-net-data-movement)를 참조하세요.

### <a name="rest-api-or-client-library"></a>REST API 또는 클라이언트 라이브러리

Azure 클라이언트 라이브러리 또는 Azure 저장소 서비스 REST API 중 하나를 통해 데이터를 Blob 저장소 계정으로 마이그레이션하는 사용자 지정 응용 프로그램을 만들 수 있습니다. Azure Storage는 NET, Java, C++, Node.JS, PHP, Ruby, Python 등, 여러 언어와 플랫폼을 위한 다양한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

자세한 내용은 [Azure Blob 저장소 시작](../blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용하여 암호화된 Blob은 Blob에 암호화 관련 메타데이터를 저장합니다. 클라이언트 쪽 암호화를 사용하여 암호화된 Blob을 복사하는 경우 복사 작업에서 Blob 메타데이터, 특히 암호화 관련 메타데이터를 유지해야 합니다. 암호화 메타데이터 없이 Blob을 복사하면 Blob 콘텐츠를 다시 검색할 수 없습니다. 암호화 관련 메타데이터에 대한 자세한 내용은 [Azure Storage 클라이언트 쪽 암호화](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

## <a name="faq"></a>FAQ

**기존 저장소 계정을 계속 사용할 수 있나요?**

예, 기존 저장소 계정(GPv1)은 계속 사용할 수 있으며, 가격 책정이나 기능은 변경되지 않습니다. GPv1 계정에는 저장소 계층을 선택할 수 있는 기능이 없으며, 앞으로는 기능을 계층화하지 않습니다.

**GPv2 저장소 계정을 왜 사용해야 하며 언제 사용을 시작해야 하나요?**

GPv2 저장소 계정은 업계에서 경쟁력 있는 트랜잭션 및 데이터 액세스 비용을 제공하는 동시에 가장 낮은 GB 저장소 비용을 제공하는 데 전문화되어 있습니다. 앞으로 변경 알림 등과 같은 기능이 이 계정 형식에 따라 도입될 것이므로 Blob을 저장하는 데 GPv2 저장소 계정을 사용하는 것이 좋습니다. 그러나 비즈니스 요구 사항에 따라 업그레이드 시점을 결정하는 것은 사용자의 몫입니다. 예를 들어 업그레이드하기 전에 트랜잭션 패턴을 최적화할 수 있습니다.

GPv2에서 다운그레이드는 지원되지 않으므로 계정을 GPv2로 업그레이드하기 전에 먼저 모든 가격 책정에 미치는 영향을 고려해야 합니다.

**기존 저장소 계정을 GPv2 저장소 계정으로 업그레이드할 수 있나요?**

예. GPv1 또는 Blob 저장소 계정은 포털, PowerShell 또는 CLI를 사용하여 GPv2로 쉽게 업그레이드할 수 있습니다. 

GPv2에서 다운그레이드는 지원되지 않으므로 계정을 GPv2로 업그레이드하기 전에 먼저 모든 가격 책정에 미치는 영향을 고려해야 합니다.

**동일한 계정에서 두 저장소 계층에 모두 데이터를 저장할 수 있나요?**

예. 계정 수준에서 설정된 **액세스 계층**은 명시적인 집합 계층이 없는 해당 계정의 모든 개체에 적용되는 기본 계층입니다. 하지만 Blob 수준 계층화를 사용하면 계정의 액세스 계층 설정에 관계없이 개체 수준에서 액세스 계층을 설정할 수 있습니다. 세 가지 저장소 계층(핫, 쿨 또는 보관)에 있는 Blob은 동일한 계정 내에 존재할 수 있습니다.

**GPv2 저장소 계정의 저장소 계층을 변경할 수 있나요?**

예, 저장소 계정에 **액세스 계층** 특성을 설정하여 계정 저장소 계층을 변경할 수 있습니다. 계정 저장소 계층을 변경하면 명시적 계층 집합이 없는 계정에 저장된 모든 개체에 적용됩니다. 저장소 계층을 핫에서 쿨로 변경하면 쓰기 작업(10,000개당) 요금이 발생하며(GPv2 저장소 계정에만 해당) 쿨에서 핫으로 변경하면 계정의 모든 데이터를 읽는 데 대한 읽기 작업(10,000개당)과 데이터 검색(GB) 요금이 모두 발생합니다.

**Blob 저장소 계정의 저장소 계층을 얼마나 자주 변경할 수 있나요?**

저장소 계층을 변경하는 빈도에 대한 제약은 없지만, 저장소 계층을 쿨에서 핫으로 변경하면 상당한 요금이 발생할 수 있습니다. 저장소 계층을 자주 변경하는 것은 좋지 않습니다.

**쿨 저장소 계층의 Blob이 핫 저장소 계층의 Blob과 다르게 작동하나요?**

GPv2 및 Blob 저장소 계정인 핫 저장소 계층의 Blob에는 GPv1 저장소 계정의 Blob과 동일한 대기 시간이 있습니다. 쿨 저장소 계층의 Blob에는 핫 계층의 Blob과 유사한 대기 시간(밀리초 단위)이 있습니다. 보관 저장소 계층의 Blob에는 몇 시간의 대기 시간이 있습니다.

쿨 저장소 계층의 Blob은 핫 저장소 계층의 Blob보다 가용성 서비스 수준(SLA)이 약간 낮습니다. 자세한 내용은 [저장소에 대한 SLA](https://azure.microsoft.com/support/legal/sla/storage)를 참조하세요.

**페이지 Blob과 가상 머신 디스크를 Blob 저장소 계정에 저장할 수 있나요?**

번호 Blob 저장소 계정은 블록 및 추가 Blob만 지원하고 페이지 Blob은 지원하지 않습니다. Azure 가상 머신 디스크는 페이지 Blob에 의해 지원되며, 결과적으로 Blob 저장소 계정은 가상 머신 디스크를 저장하는 데 사용될 수 없습니다. 하지만 가상 컴퓨터 디스크의 백업을 Blob 저장소 계정의 블록 Blob으로 저장하는 것은 가능합니다. Blob 저장소 계정 대신 GPv2를 사용하도록 고려해야 하는 여러 가지 이유 중 하나입니다.

**GPv2 저장소 계정을 사용하려면 기존 응용 프로그램을 변경해야 하나요?**

GPv2 저장소 계정은 GPv1 및 Blob 저장소 계정과 100% 일치하는 API입니다. 응용 프로그램에서 블록 Blob 또는 추가 Blob을 사용하고 [Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014-02-14 버전 이상을 사용하는 한 응용 프로그램은 작동합니다. 이전 프로토콜 버전을 사용할 경우 응용 프로그램을 업데이트하여 새 버전을 사용해야 두 저장소 계정에서 모두 원활하게 작업할 수 있습니다. 일반적으로 사용하는 저장소 계정 유형에 관계없이 항상 최신 버전을 권장합니다.

일반적으로 트랜잭션 및 대역폭 측면에서 GPv2 가격은 GPv1보다 더 높습니다. 따라서 전체 청구 비용이 증가하지 않도록 업그레이드하기 전에 트랜잭션 패턴을 최적화해야 합니다.

**사용자 환경이 변경되었나요?**

GPv2 저장소 계정은 GPv1 저장소 계정과 매우 유사하며, 높은 내구성 및 가용성, 확장성, 성능 및 보안을 비롯한 Azure Storage의 모든 주요 기능을 지원합니다. Blob 저장소 계정 고유의 특징 및 제한 사항과 위에서 설명한 저장소 계층을 제외하고 GPv2 또는 Blob 저장소로 업그레이드하는 경우 나머지는 모두 같습니다.

## <a name="next-steps"></a>다음 단계

### <a name="evaluate-blob-storage-accounts"></a>Blob 저장소 계정 평가

[지역별 Blob 저장소 계정의 가용성 확인](https://azure.microsoft.com/regions/#services)

[Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[지역별 Blob 저장소 가격 확인](https://azure.microsoft.com/pricing/details/storage/)

[데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>GPv2 저장소 계정 사용 시작

[Azure Blob 저장소 시작](../blobs/storage-dotnet-how-to-use-blobs.md)

[Azure Storage의 데이터 이동](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[저장소 계정 찾아보기 및 탐색](http://storageexplorer.com/)
