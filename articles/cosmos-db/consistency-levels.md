---
title: Azure Cosmos DB의 일관성 수준 | Microsoft Docs
description: Azure Cosmos DB에는 최종 일관성, 가용성 및 대기 시간을 절충하여 조정하는 데 유용한 5가지 일관성 수준이 있습니다.
keywords: 최종 일관성, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f38d05dc720dd596c81a51abf7040ac062e8158
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준
Azure Cosmos DB는 처음부터 모든 데이터 모델에 대한 전역 배포를 염두에 두고 설계되었습니다. 예측 가능한 짧은 대기 시간을 보증하고, 여러 개의 잘 정의된 관대한 일관성 모델을 제공하도록 설계되었습니다. 현재 Azure Cosmos DB는 5가지 일관성 수준(강력, 제한된 부실, 세션, 일관적인 접두사 및 최종)을 제공합니다. 제한된 부실, 세션, 일관된 접두사 및 최종은 가능한 가장 일관성 높은 모델인 강력보다는 일관성이 낮기 때문에 "관대한 일관성 모델"로 간주됩니다. 

Azure Cosmos DB는 분산 데이터베이스에서 흔히 제공하는 **강력** 및 **최종 일관성** 모델 외에도 신중하게 변환된 조작 가능한 3가지 일관성 모델, **제한된 부실**, **세션** 및 **일관된 접두사**를 제공합니다. 각 일관성 수준의 유용성은 실제 사용 사례에 대해 검증되었습니다. 이 5가지 일관성 수준을 통해 일관성, 가용성, 대기 시간 사이에서 타당하게 절충합니다. 

Azure Cosmos DB 프로그램 관리자인 Andrew Liu는 다음 비디오에서 턴키 글로벌 배포 기능을 보여줍니다.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>분산된 데이터베이스 및 일관성
상업적으로 분산된 데이터베이스는 입증 가능한 잘 정의된 일관성 선택 사항을 전혀 제공하지 않는 데이터베이스와, 두 가지 극단적인 프로그래밍 가능성 선택을 제공하는 데이터베이스(강력한 일관성, 결과적 일관성) 등의 두 가지 범주에 해당합니다. 

전자의 경우 응용 프로그램 개발자들이 복제 프로토콜을 세부적으로 구현해야 하는 부담이 있고 개발자들이 일관성, 가용성, 대기 시간 및 처리량 간의 상호 균형 유지가 어렵습니다. 후자의 경우에는 두 가지 극단의 상황 중 하나를 선택해야 합니다. 50여 개가 넘는 일관성 모델의 연구와 제안이 있지만 분산형 데이터베이스 커뮤니티에서는 강력하고 결과적인 일관성 이상으로 일관성 수준을 상용화하지 못하고 있습니다. Cosmos DB에서는 개발자가 5개의 잘 정의된 일관성 모델을 일관성 스펙트럼(강력, 제한된 부실, [세션](http://dl.acm.org/citation.cfm?id=383631), 일관적인 접두사, 결과적)과 함께 선택할 수 있습니다. 

![Azure Cosmos DB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.](./media/consistency-levels/five-consistency-levels.png)

다음 표에서는 각 일관성 수준이 제공하는 특정 보증을 보여 줍니다.
 
**일관성 수준 및 보증**

| 일관성 수준 | 보증 |
| --- | --- |
| 강력 | 선형화 가능성 읽기를 통해 항목의 최신 버전 반환이 보장됩니다.|
| 제한된 부실 | 일관적인 접두사 최대한 k 접두사 또는 t 간격을 통해 쓰기 뒤 읽기 지연 |
| 세션   | 일관적인 접두사 단조 읽기, 단조 쓰기, 쓰기 읽기, 읽기 뒤 쓰기 |
| 일관적인 접두사 | 간격 없이 모든 업데이트의 일부 접두어의 업데이트 반환 |
| 최종  | 순서 외 읽기 |

Cosmos DB 계정에 대해 기본 일관성 수준을 구성할 수 있습니다(나중에 특정 읽기 요청에 대한 일관성 덮어씀). 내부적으로 기본 일관성 수준은 여러 지역에 걸칠 수 있는 파티션 집합 안의 데이터에 적용됩니다. Azure Cosmos DB 테넌트의 약 73%는 세션 일관성을 사용하고 20%는 제한된 부실을 선호합니다. Azure Cosmos DB 고객의 약 3%는 처음에 다양한 일관성 수준을 실험한 후 응용 프로그램에 맞는 특정 일관성을 선택합니다. Azure Cosmos DB 테넌트의 2%만이 요청에 따라 일관성 수준을 재정의하는 것으로 확인되었습니다. 

Cosmos DB에서 세션, 일관적인 접두사 및 최종 일관성에서의 읽기는 강력 또는 제한된 부실 일관성에서의 읽기보다 2배 저렴합니다. Cosmos DB는 가용성, 처리량 및 대기 시간과 함께 일관성 보장을 포함하여 업계 최고 수준의 포괄적인 SLA를 제공합니다. Azure Cosmos DB는 서비스 원격 분석에 대해 지속적으로 작동하고 일관성 위반을 사용자에게 공개적으로 보고하는 [선형화 가능성 검사기](http://dl.acm.org/citation.cfm?id=1806634)를 사용합니다. 제한된 부실의 경우 Azure Cosmos DB가 k 및 t 경계 위반을 모니터링하고 보고합니다. 또한 5가지 완화된 일관성 수준의 경우 Azure Cosmos DB가 [확률적 제한된 부실 메트릭](http://dl.acm.org/citation.cfm?id=2212359)을 사용자에게 직접 보고합니다.  

## <a name="service-level-agreements"></a>서비스 수준 계약

Azure Cosmos DB는 5가지 일관성 수준 중 하나로 구성된 단일 Azure 지역으로 한정된 Azure Cosmos DB 데이터베이스 계정이나, 4가지 관대한 일관성 수준 중 하나로 구성된 여러 Azure 지역을 포괄하는 데이터베이스 계정에 대해 처리량, 일관성, 가용성 및 대기 시간을 보장하는 포괄적인 99.99% [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)를 제공합니다. 또한 일관성 수준 선택과는 별개로, Azure Cosmos DB는 둘 이상의 Azure 지역을 포괄하는 데이터베이스 계정의 읽기 가용성에 대해서는 99.999% SLA를 제공합니다.

## <a name="scope-of-consistency"></a>일관성 범위
일관성의 세분성은 단일 사용자 요청에 따라 범위가 지정됩니다. 쓰기 요청은 삽입, 바꾸기, upsert 또는 삭제 트랜잭션에 해당할 수 있습니다. 쓰기와 마찬가지로 읽기/쿼리 트랜잭션도 단일 사용자 요청에 대해 범위가 지정됩니다. 사용자는 여러 파티션에 걸친 대량의 결과 세트에 페이지를 매겨야 할 수 있지만, 각 읽기 트랜잭션은 단일 페이지에 따라 범위가 지정되고 단일 패턴 내에서 서비스가 제공됩니다.

## <a name="consistency-levels"></a>일관성 수준
Cosmos DB 아래의 모든 컬렉션(및 데이터베이스)에 적용되는 데이터베이스 계정에서 기본 일관성 수준을 구성할 수 있습니다. 기본적으로 사용자 정의 리소스에 대해 실행되는 모든 읽기 및 쿼리는 데이터베이스 계정에 지정된 기본 일관성 수준을 사용합니다. 지원되는 각 API를 사용하여 특정 읽기/쿼리 요청의 일관성 수준을 완화할 수 있습니다. Azure Cosmos DB 복제 프로토콜에서 지원하는 일관성 수준은 5가지가 있으며, 아래 설명처럼 특정 일관성 보증과 성능을 명확히 절충합니다.

<a id="strong"></a>
**강력**: 

* 강력한 일관성은 [선형화 가능성](https://aphyr.com/posts/313-strong-consistency-models)을 보증하고, 읽기는 가장 최신 버전의 항목을 반환합니다. 
* 강력한 일관성은 과반수 쿼럼의 복제본에서 지속적으로 커밋된 후에만 쓰기가 표시되도록 합니다. 쓰기는 동기적으로 주 복제본 및 쿼럼의 보조 복제본 둘 다에서 지속적으로 커밋되거나 중단됩니다. 읽기는 항상 과반수의 읽기 쿼럼에서 승인됩니다. 클라이언트는 커밋되지 않은 쓰기나 부분 쓰기를 볼 수 없으며 항상 승인된 최신 쓰기를 읽습니다. 
* 강력한 일관성을 사용하도록 구성된 Azure Cosmos DB 계정은 Azure Cosmos DB 계정에 두 개 이상의 Azure 지역을 연결할 수 없습니다.  
* 강력한 일관성이 적용된 읽기 작업(사용한 [요청 단위](request-units.md) 기준)의 비용은 세션 및 최종보다 많지만, 제한된 경의와 같습니다.

<a id="bounded-staleness"></a>
**제한된 부실**: 

* 제한된 부실 일관성은 적어도 항목의 *K* 버전이나 접두어 또는 *t* 시간 간격에 읽기가 쓰기보다 뒤처지게 합니다. 
* 따라서 제한된 부실을 선택하는 경우 "부실"을 두 가지 방법, 즉 읽기가 쓰기보다 느린 항목 버전 번호 *K*와 시간 간격 *t*로 구성할 수 있습니다. 
* 제한된 부실은 "부실 창" 내부를 제외하고 전체 전역 순서를 제공합니다. "부실 창" 내부 및 외부 지역에 단조 읽기 보증이 존재합니다. 
* 제한된 부실은 세션, 일관성 접두사 또는 최종 일관성보다 강력한 일관성 보증을 제공합니다. 전역에 배포되는 응용 프로그램의 경우, 강력한 일관성을 가지기를 바라지만 가용성이 99.99%이고 대기 시간이 짧은 시나리오에서는 제한된 부실을 사용하는 것이 좋습니다.   
* 제한된 부실로 구성된 Azure Cosmos DB 계정은 Azure Cosmos DB 계정이 있는 모든 Azure 지역과 연결할 수 있습니다. 
* 제한된 부실이 적용된 읽기 작업(사용한 요청 단위 기준)의 비용은 세션 및 최종 일관성보다 높지만, 강력한 일관성과 동일합니다.

<a id="session"></a>
**세션**: 

* 강력한 일관성 수준과 제한된 부실 종속성 수준이 제공하는 전역 일관성 모델과 달리 세션 일관성 범위는 클라이언트 세션에 따라 범위가 지정됩니다. 
* 세션 일관성은 단조 읽기, 단조 쓰기, 사용자 고유 쓰기 읽기(RYW)를 보증하므로 장치 또는 사용자 세션이 관련된 모든 시나리오에 이상적입니다. 
* 세션 일관성으로 특정 세션에 예측 가능한 일관성을 제공하고, 쓰기 및 읽기 대기 시간이 가장 짧으면서도 최대한 많은 읽기 처리량을 처리합니다. 
* 세션 일관성으로 구성된 Azure Cosmos DB 계정은 Azure Cosmos DB 계정이 있는 모든 Azure 지역과 연결할 수 있습니다. 
* 세션 일관성 수준의 읽기 작업의 비용(사용한 RU 기준)은 강력 및 제한된 부실 일관성 수준보다 적지만, 최종 일관성보다는 높습니다.

<a id="consistent-prefix"></a>
**일관적인 접두사**: 

* 일관적인 접두사는 추가 쓰기가 없을 경우 그룹 내의 복제본이 결국 수렴되도록 보장합니다. 
* 일관적인 접두사는 읽기가 잘못된 쓰기를 볼 수 없도록 보장합니다. 쓰기가 `A, B, C` 순서로 수행된 경우 클라이언트는 `A`, `A,B` 또는 `A,B,C`를 볼 수 있지만 `A,C` 또는 `B,A,C`처럼 잘못된 것은 볼 수 없습니다.
* 일관적인 접두사로 구성된 Azure Cosmos DB 계정은 Azure Cosmos DB 계정이 있는 모든 Azure 지역과 연결할 수 있습니다. 

<a id="eventual"></a>
**최종**: 

* 최종 일관성은 추가 쓰기가 없을 경우 그룹 내의 복제본이 결국 수렴되도록 보장합니다. 
* 최종 일관성은 가장 약한 형태의 일관성으로, 클라이언트가 이전에 확인한 것보다 오래된 값을 얻을 수 있습니다.
* 최종 일관성은 읽기 일관성이 가장 약하지만 읽기와 쓰기 둘 다에서 대기 시간이 가장 짧습니다.
* 최종 일관성으로 구성된 Azure Cosmos DB 계정은 Azure Cosmos DB 계정이 있는 모든 Azure 지역과 연결할 수 있습니다. 
* 최종 일관성 수준의 읽기 작업 비용(사용한 RU 기준)은 모든 Azure Cosmos DB 일관성 수준에서 가장 낮습니다.

## <a name="configuring-the-default-consistency-level"></a>기본 일관성 수준 구성
1. [Azure Portal](https://portal.azure.com/)의 표시줄에서 **Azure Cosmos DB**를 클릭합니다.
2. **Azure Cosmos DB** 페이지에서 수정할 데이터베이스 계정을 선택합니다.
3. 계정 페이지에서 **기본 일관성**을 클릭합니다.
4. **기본 일관성** 페이지에서 새 일관성 수준을 선택하고 **저장**을 클릭합니다.
   
    ![설정 아이콘 및 기본 일관성 항목이 강조 표시된 스크린샷](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>쿼리에 대한 일관성 수준
기본적으로 사용자 정의 리소스에 대한 쿼리의 일관성 수준은 읽기 일관성 수준과 같습니다. 기본적으로 항목을 Cosmos DB 컨테이너에 삽입하거나 바꾸거나 삭제할 때마다 인덱스가 동기적으로 업데이트됩니다. 이렇게 하면 쿼리에 지점 읽기와 동일한 일관성 수준을 적용할 수 있습니다. Azure Cosmos DB는 쓰기 최적화되며 지속적인 대량 쓰기, 동기 인덱스 유지 관리 및 일관성 있는 쿼리 처리를 지원하지만 인덱스를 지연 업데이트하도록 특정 컬렉션을 구성할 수 있습니다. 지연 인덱싱은 쓰기 성능을 더욱 향상하며 워크로드가 주로 읽기 중심인 대량 수집 시나리오에 적합합니다.  

| 인덱싱 모드 | 읽기 | 쿼리 |
| --- | --- | --- |
| 일관성(기본값) |강력, 제한된 부실, 세션, 일관적인 접두사 또는 최종에서 선택 |강력, 제한된 부실, 세션 또는 최종에서 선택 |
| 지연 |강력, 제한된 부실, 세션, 일관적인 접두사 또는 최종에서 선택 |최종 |
| 없음 |강력, 제한된 부실, 세션, 일관적인 접두사 또는 최종에서 선택 |해당 없음 |

읽기 요청과 마찬가지로 각 API에서 특정 쿼리 요청의 일관성 수준을 낮출 수 있습니다.

## <a name="consistency-levels-for-the-mongodb-api"></a>MongoDB API의 일관성 수준

Azure Cosmos DB는 현재 강함 및 최종, 두 개의 일관성 설정이 있는 MongoDB 버전 3.4를 구현합니다. Azure Cosmos DB는 다중 API이기 때문에 일관성 설정은 계정 수준에서 적용되며 일관성 적용은 각 API에 의해 제어됩니다.  MongoDB 3.6까지는 세션 일관성에 대한 개념이 없었으므로 MongoDB API 계정에서 세션 일관성을 사용하도록 설정하는 경우, 일관성은 MongoDB API를 사용할 때 최종으로 다운그레이드됩니다. MongoDB API 계정에 대해 읽기-사용자-쓰기 보장이 필요한 경우 계정에 대한 기본 일관성 수준이 강함 또는 제한된 부실로 설정되어야 합니다.

## <a name="next-steps"></a>다음 단계
장단점 및 일관성 수준에 대한 더 많은 읽기를 수행 하려는 경우 다음 리소스를 좋습니다.

* [야구를 통해 설명하는 복제된 데이터 일관성(비디오) 작성자: Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [야구를 통해 설명하는 복제된 데이터 일관성(백서) 작성자: Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [약하게 일관된 복제 데이터에 대한 세션 보장](http://dl.acm.org/citation.cfm?id=383631)
* [최신 분산 데이터베이스 시스템 디자인의 일관성 균형: CAP는 스토리의 일부일 뿐입니다.](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [최종 일관성 - 재고되었습니다.](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [쿼럼 시스템의 부하 용량 및 가용성, SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [목록: 전체 자동 선형화 가능성 검사기, 프로그래밍 언어 디자인 및 구현에 대한 2010 ACM SIGPLAN 컨퍼런스 회보](http://dl.acm.org/citation.cfm?id=1806634)
* [실용적인 부분 쿼럼에 대한 확률적으로 제한된 부실](http://dl.acm.org/citation.cfm?id=2212359)
