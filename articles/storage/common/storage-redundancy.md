---
title: Azure Storage에서 데이터 복제 | Microsoft Docs
description: Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다. 복제 옵션에는 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소), GRS(지역 중복 저장소) 및 RA-GRS(읽기 액세스 지역 중복 저장소)가 포함됩니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 6c2c6979d56eb19ff2ba4fb647c7c51e52e51ac6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076217"
---
# <a name="azure-storage-replication"></a>Azure Storage 복제

Microsoft Azure Storage 계정 데이터는 항상 내구성 및 고가용성을 위해 복제됩니다. Azure Storage 복제는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 전체 지역에 데이터를 복제하도록 선택할 수 있습니다.

복제를 사용하면 Storage 계정은 오류 상황에서도 [Storage용 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/)를 충족하게 됩니다. Azure Storage의 내구성 및 가용성 보장에 대한 정보는 SLA를 확인하세요.

## <a name="choosing-a-replication-option"></a>복제 옵션 선택

저장소 계정을 만들 때 다음 복제 옵션 중 하나를 선택할 수 있습니다.

* [LRS(로컬 중복 저장소)](storage-redundancy-lrs.md)
* [ZRS(영역 중복 저장소)](storage-redundancy-zrs.md)
* [GRS(지역 중복 저장소)](storage-redundancy-grs.md)
* [RA-GRS(읽기 액세스 지역 중복 저장소)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

다음 표는 각 복제 전략에서 특정 유형의 이벤트(또는 영향이 비슷한 이벤트)에 제공하는 내구성 및 가용성 범위를 간략하게 보여줍니다.

| 시나리오                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| 데이터 센터 내에서 노드를 사용할 수 없음                                                                 | 예                             | 예                              | 예                                  | 예                                  |
| 전체 데이터 센터(영역 또는 비영역)를 사용할 수 없게 됨                                           | 아니오                              | 예                              | 예                                  | 예                                  |
| 전체 지역 가동 중단                                                                                     | 아니오                              | 아니요                               | 예                                  | 예                                  |
| 지역 전체가 사용할 수 없는 우 데이터(원격 지역 복제 지역의)에 대한 읽기 권한 | 아니오                              | 아니요                               | 아니요                                   | 예                                  |
| 지정된 한 해 동안 ___의 개체 내구성을 제공하도록 설계되었습니다.                                          | 최소 99.999999999% | 최소 99.9999999999% | 최소 99.99999999999999% | 최소 99.99999999999999% |
| 지원되는 저장소 계정 형식                                                                   | GPv1, GPv2, Blob                | GPv2                             | GPv1, GPv2, Blob                     | GPv1, GPv2, Blob                     |

다른 중복 옵션에 대한 가격 정보를 보려면 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

> [!NOTE]
> Premium Storage는 LRS(로컬 중복 저장소)만 지원합니다. Premium Storage에 대한 자세한 내용은 [Premium Storage: Azure Virtual Machine에 대한 고성능 저장소](../../virtual-machines/windows/premium-storage.md)를 참조하세요.

## <a name="changing-replication-strategy"></a>복제 전략 변경
[Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 여러 [Azure 클라이언트 라이브러리](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools) 중 하나를 사용하여 저장소 계정의 복제 전략을 변경할 수 있습니다. 저장소 계정의 복제 유형을 변경해도 가동 중지 시간이 발생하지 않습니다.

   > [!NOTE]
   > 현재는 포털 또는 API를 사용하여 계정을 ZRS로 변환할 수 없습니다. 계정의 복제를 ZRS로 변환하려면 자세한 내용은 [ZRS(영역 중복 저장소)](storage-redundancy-zrs.md)를 참조하세요.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>내 계정의 복제 전략을 변경하는 데 드는 비용이 있나요?
변환 경로에 따라 다릅니다. 가장 저렴한 LRS부터 ZRS, GRS 그리고 가장 비싼 RA-GRS까지 다양한 중복 서비스가 있습니다. 예를 들어 LRS*에서* 다른 서비스로 변경하면 보다 정교한 중복 수준으로 변경하는 것이므로 추가 요금이 발생합니다. GRS 또는 RA-GRS*로* 변경하면 주 지역의 데이터가 원격 보조 지역으로 복제되므로 송신 대역폭 요금이 발생합니다. 이 요금은 초시 설치 시 한 번만 발생합니다. 데이터가 복사된 후에는 더 이상의 변환 요금이 없습니다. 새 데이터를 복제하거나 기존 데이터를 업데이트할 경우에만 요금이 발생합니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

GRS에서 LRS로 변경하는 경우 추가 비용은 없지만 복제된 데이터가 보조 위치에서 삭제됩니다.

## <a name="see-also"></a>참고 항목

- [LRS(로컬 중복 저장소): Azure Storage에 대한 저렴한 데이터 중복](storage-redundancy-lrs.md)
- [ZRS(영역 중복 저장소): 고가용성 Azure Storage 응용 프로그램](storage-redundancy-zrs.md)
- [GRS(지역 중복 저장소): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)
- [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)
- [RA-GRS 저장소를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage 중복 옵션 및 읽기 액세스 지역 중복 저장소 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP 문서 - Azure Storage: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
