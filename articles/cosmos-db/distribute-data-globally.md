---
title: Azure Cosmos DB로 데이터 글로벌 배포 | Microsoft Docs
description: 전 세계적으로 배포된 다중 모델 데이터베이스 서비스인, Azure Cosmos DB에서 글로벌 데이터베이스를 사용한 전 세계적 지역에서 복제, 장애 조치(Failover), 데이터 복구에 대해 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 967e7458d43dccd4601440138b7445eb876b9f01
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB로 데이터를 글로벌 배포하는 방법
Azure는 어디에나 존재합니다. 전 세계 50개 이상의 지역에서 사용되며 계속해서 확장 중입니다. 이처럼 전 세계에서 사용되기 때문에 Azure가 개발자에게 제공하는 차별화된 이점 중 하나는 전 세계에 분산된 응용 프로그램을 간편하게 빌드, 배포 및 관리할 수 있다는 점입니다. 

[Azure Cosmos DB](../cosmos-db/introduction.md)는 업무에 중요한 응용 프로그램에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)로 지원되는 턴키 전역 분산, 전 세계적으로 [처리량 및 저장소의 탄력적인 확장](../cosmos-db/partition-data.md), 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, [잘 정의된 5개 일관성 모델](consistency-levels.md), 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 또는 인덱스 관리를 처리하지 않아도 되도록 [모든 데이터를 자동으로 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다. 문서, 키-값, 그래프 및 열 패밀리 데이터 모델을 지원하는 다중 모델 서비스입니다. 기본적으로 클라우드 서비스인 Azure Cosmos DB는 다중 테넌트 및 전역 배포를 사용하여 처음부터 세심하게 설계되었습니다.


![Azure Cosmos DB 컬렉션을 분할하여 세 하위 지역에 분산](./media/distribute-data-globally/global-apps.png)

**단일 Azure Cosmos DB 컨테이너를 분할하여 여러 Azure 하위 지역에 분산**

우리가 Azure Cosmos DB를 개발하면서 알게 된 사실은 전역 분산을 나중에 추가할 수 없다는 것입니다. 전역 분산은 "단일 사이트" 데이터베이스 시스템 위에 "추가"할 수 없습니다. 전역에 분산되는 데이터베이스가 제공하는 기능은 "단일 사이트" 데이터베이스가 제공하는 기존의 지역 재해 복구의 기능보다 훨씬 우수합니다. 지역 재해 복구를 제공하는 단일 사이트 데이터베이스는 전역에 분산되는 데이터베이스의 엄격한 하위 집합입니다. 

Azure Cosmos DB의 턴키 전역 분산을 사용하면 개발자는 데이터베이스를 통해 람다 패턴(예: [AWS DynamoDB 복제](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md))을 사용하거나 여러 지역에 걸쳐 "이중 쓰기"를 수행하여 개발자 고유의 스캐폴딩을 만들 필요가 없습니다. 이러한 접근 방식은 정확성을 유지하고 우수한 SLA를 제공하는 것이 불가능하기 때문에 권장하지 *않습니다*. 

이 문서에서는 Azure Cosmos DB의 전역 분산 기능의 개요를 설명합니다. 또한 포괄적인 SLA를 제공하기 위한 Azure Cosmos DB 고유의 접근 방식을 설명합니다. 

## <a id="EnableGlobalDistribution"></a>턴키 전역 분산 활성화
Azure Cosmos DB는 전역으로 분산된 응용 프로그램을 손쉽게 작성할 수 있도록 다음과 같은 기능을 제공합니다. 이러한 기능은 Azure Cosmos DB의 리소스 공급자 기반 [REST API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)와 Azure Portal을 통해 제공됩니다.

작업의 Azure Cosmos DB에서 턴키 글로벌 배포 기능을 확인하려면 다음 비디오를 시청하세요.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>어느 지역에나 존재 
Azure는 [새 하위 지역](https://azure.microsoft.com/regions/)을 온라인으로 연결하여 지리적 존재 영역을 지속적으로 늘려가고 있습니다. Azure Cosmos DB는 Azure에서 *기초 서비스*로 분류되며 기본적으로 모든 새 Azure 지역에서 사용할 수 있습니다. 따라서 Azure가 새로운 비즈니스 영역을 개통하는 즉시 지리적 지역을 Azure Cosmos DB 데이터베이스 계정과 연결할 수 있습니다.

### <a id="UnlimitedRegionsPerAccount"></a>개수 제한 없이 지역을 원하는 만큼 Azure Cosmos DB 데이터베이스 계정에 연결
Azure Cosmos DB를 사용하면 개수에 관계없이 Azure 지역을 원하는 만큼 Azure Cosmos DB 데이터베이스 계정에 연결할 수 있습니다. 지리적 펜스 제한(예: 중국, 독일)을 제외하고, Azure Cosmos DB 데이터베이스 계정에 연결할 수 있는 하위 지역 수에는 어떠한 제한도 없습니다. 다음 그림은 25개 Azure 지역을 아우르도록 구성된 데이터베이스 계정을 보여줍니다.  

![25개 Azure 지역을 아우르는 Azure Cosmos DB 데이터베이스 계정](./media/distribute-data-globally/spanning-regions.png)

**25개 Azure 지역을 아우르는 테넌트의 Azure Cosmos DB 데이터베이스 계정**


### <a id="PolicyBasedGeoFencing"></a>정책 기반 지역 펜스
Azure Cosmos DB는 정책 기반의 지역 펜스를 지원하도록 설계되었습니다. 지역 펜스는 데이터 거버넌스 및 규정 준수 제한을 보장하는 중요한 구성 요소이며 특정 영역을 계정에 연결하지 못하게 할 수도 있습니다. 지역 펜스의 예로 독립 클라우드 내 전역 분산(예: 중국 및 독일) 또는 정부 조세 경계 내 전역 분산(예: 오스트레일리아) 범위 지정을 들 수 있습니다. 정책은 Azure 구독의 메타데이터를 사용하여 제어됩니다.

### <a id="DynamicallyAddRegions"></a>동적으로 지역 추가 및 제거
Azure Cosmos DB를 사용하여 언제든지 데이터베이스 계정에서 지역을 추가(연결)하거나 제거(분리)할 수 있습니다([이전 그림](#UnlimitedRegionsPerAccount) 참조). 파티션에서 데이터의 병렬 복제로 인해 Azure Cosmos DB는 새 지역이 추가되는 경우 전 세계 어디에서든 30분 내에 작업에 사용할 수 있도록 보장합니다(데이터는 100TB 미만이라고 가정). 

### <a id="FailoverPriorities"></a>장애 조치(Failover) 우선 순위
가동 중단의 경우 정확한 지역 장애 조치(failover) 순서를 제어할 수 있도록 Azure Cosmos DB는 데이터베이스 계정에 연결된 다양한 지역에 *우선 순위*를 연결할 수 있습니다(아래 그림 참조). Azure Cosmos DB는 사용자가 지정한 우선 순위에 따라 자동으로 장애 조치(Failover)를 수행합니다. 지역별 장애 조치(Failover)에 대한 자세한 내용은 [비즈니스 연속성을 위한 Azure Cosmos DB의 자동 지역별 장애 조치(Failover)](regional-failover.md)를 참조하세요.


![Azure Cosmos DB를 사용하여 장애 조치(Failover) 우선 순위 구성](./media/distribute-data-globally/failover-priorities.png)

**Azure Cosmos DB의 테넌트는 데이터베이스 계정에 연결된 영역의 장애 조치(Failover) 우선 순위(오른쪽 창)를 구성할 수 있습니다.**

### <a id="ConsistencyLevels"></a>전역적으로 분산된 데이터베이스에 대해 잘 정의된 여러 일관성 모델
Azure Cosmos DB는 SLA로 지원되는 [여러 개의 자 정의된, 직관적이고 실용적인 일관성 모델](consistency-levels.md)을 지원합니다. 워크로드/시나리오에 따라 제공되는 옵션 목록에서 특정 일관성 모델을 선택할 수 있습니다. 

### <a id="TunableConsistency"></a>전역적으로 복제된 데이터베이스에 대한 튜닝 가능한 일관성
Azure Cosmos DB를 사용하면 런타임에 요청별로 기본 일관성 선택을 프로그래밍 방식으로 무시하고 완화할 수 있습니다. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>동적으로 구성 가능한 읽기 및 쓰기 지역
Azure Cosmos DB는 "읽기", "쓰기" 또는 "읽기/쓰기" 하위 지역에 대한 하위 지역(데이터베이스에 연결된)을 구성할 수 있습니다. 

### <a id="ElasticallyScaleThroughput"></a>Azure 지역에 걸쳐 처리량을 탄력적으로 확장
프로그래밍 방식으로 처리량을 프로비전하여 Azure Cosmos DB 컨테이너를 탄력적으로 확장할 수 있습니다. 처리량은 Azure Cosmos DB 컨테이너가 배포되는 모든 지역에 적용됩니다.

### <a id="GeoLocalReadsAndWrites"></a>지역 로컬 읽기 및 쓰기
전역에 분산되는 데이터베이스의 핵심 이점은 전 세계 어디서나 데이터 액세스 대기 시간이 짧다는 점입니다. Azure Cosmos DB는 전 세계 99번 째 백분위수로 낮은 대기 시간 읽기 및 쓰기를 제공합니다. 모든 읽기가 가장 가까운(로컬) 지역에서 제공되도록 보장합니다. 읽기 요청을 처리하기 위해 읽기가 발급되는 영역에 로컬인 쿼럼이 사용됩니다. 쓰기에도 같은 방식이 적용됩니다. 쓰기는 대부분의 복제가 쓰기를 로컬에 지속적으로 커밋한 후에만 승인되지만 쓰기를 승인하기 위해 원격 복제본에서 제어되지는 않습니다. 달리 말해서, Azure Cosmos DB의 복제 프로토콜은 요청이 발급되는 지역에 대해 읽기 및 쓰기 쿼럼이 항상 로컬이라는 가정 하에 작동합니다.

### <a id="ManualFailover"></a>수동 장애 조치(failover)
Azure Cosmos DB는 데이터베이스 계정의 장애 조치(failover)를 트리거하여 전체 응용 프로그램(데이터베이스 이상)의 *종단 간* 가용성 속성이 유효한지 검사할 수 있습니다. 오류 검색 및 리더 선거의 안전과 실시간 속성이 모두 보장되므로 Azure Cosmos DB는 테넌트가 시작하는 수동 장애 조치(failover) 작업에서 *데이터 무손실*을 보장합니다.

### <a id="AutomaticFailover"></a>자동 장애 조치(Failover)
Azure Cosmos DB는 하나 이상의 하위 지역에서 가동 중단이 발생하는 경우 자동 장애 조치(Failover)를 지원합니다. 지역 장애 조치(Failover) 동안 Azure Cosmos DB는 읽기 대기 시간, 작동 시간 가용성, 일관성 및 처리량 SLA를 유지합니다. Azure Cosmos DB는 자동 장애 조치(failover) 작업이 완료되는 기간에 상한값을 제공합니다. 이 시간에는 지역 가동 중단 시 데이터 손실 가능성이 있습니다.

### <a id="GranularFailover"></a>다양한 세분성의 장애 조치(Failover) 설계
현재 자동 및 수동 장애 조치(Failover) 기능은 데이터베이스 계정의 세분성 수준에서 노출됩니다. 내부적으로 Azure Cosmos DB는 더욱 세밀한 데이터베이스, 컨테이너 또는 (키의 범위를 소유하는 컨테이너의) 파티션 수준에서 *자동* 장애 조치(failover)를 제공하도록 설계되었습니다. 

### <a id="MultiHomingAPIs"></a>Azure Cosmos DB의 멀티 호밍
Azure Cosmos DB는 *논리적*(지역에 관계없음) 또는 *물리적*(지역에 한정) 엔드포인트를 사용하여 데이터베이스와 상호 작용할 수 있습니다. 논리적 엔드포인트를 사용하면 장애 조치(failover) 시 응용 프로그램을 투명하게 멀티 호밍할 수 있습니다. 물리적 엔드포인트는 읽기와 쓰기를 특정 지역에 리디렉션할 수 있는 세분화된 응용 프로그램 제어 기능을 제공합니다.

[SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [Gremlin API](../cosmos-db/tutorial-global-distribution-graph.md), [테이블 API](../cosmos-db/tutorial-global-distribution-table.md) 및 [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md)에 대한 읽기 기본 설정을 구성하는 방법은 이러한 문서에서 찾을 수 있습니다.

### <a id="TransparentSchemaMigration"></a>투명하고 일관적인 데이터베이스 스키마 및 인덱스 마이그레이션 
Azure Cosmos DB는 [스키마에 구애받지 않습니다](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). 고유한 디자인의 데이터베이스 엔진은 Azure Cosmos DB가 사용자에게 스키마 또는 보조 인덱스를 요청하지 않고도 수집하는 모든 데이터를 자동으로 그리고 동적으로 인덱싱할 수 있도록 합니다. 따라서 데이터베이스 스키마 및 인덱스 마이그레이션에 대해 걱정하거나 스키마 변경의 다단계 응용 프로그램 출시를 조정하는 일 없이 전역에 분산된 응용 프로그램을 신속하게 반복할 수 있습니다. Azure Cosmos DB는 사용자가 명시적으로 수행한 인덱싱 정책 변경이 성능 또는 가용성 저하로 이어지지 않도록 보장합니다.  

### <a id="ComprehensiveSLAs"></a>포괄적인 SLA(고가용성 그 이상)
전역적으로 분산된 데이터베이스 서비스인 Azure Cosmos DB는 데이터베이스에 연결된 지역 수에 관계없이 글로벌 환경에서 실행되는 데이터베이스에 대해 **가용성**, **대기 시간**, **처리량** 및 **일관성**에 대해 잘 정의된 포괄적인 SLA를 제공합니다.  

## <a id="LatencyGuarantees"></a>대기 시간 보장
Azure Cosmos DB처럼 전역에 분산되는 데이터베이스의 핵심 이점은 전 세계 어디서나 데이터 액세스 대기 시간이 짧다는 점입니다. Azure Cosmos DB는 다양한 데이터베이스 작업에 99번 째 백분위수의 짧은 대기 시간을 보장합니다. Azure Cosmos DB에서 사용하는 복제 프로토콜은 데이터베이스 작업(읽기 및 쓰기 모두)이 항상 클라이언트에 로컬인 하위 지역에서 수행되도록 보장합니다. Azure Cosmos DB의 대기 시간 SLA에는 읽기 그리고 다양한 요청 및 응답 크기에 대해 (동기적으로) 인덱싱된 쓰기 및 쿼리에 대한 99번 째 백분위수에서 보장을 제공합니다. 쓰기에 대한 대기 시간 보장에는 로컬 지역 내에서 대부분의 쿼럼이 지속적으로 커밋하는 것이 포함되어 있습니다.

### <a id="LatencyAndConsistency"></a>대기 시간과 일관성의 관계 
전 세계적으로 분산된 서비스의 경우 전 세계적으로 분산된 설정에서 강력한 일관성을 제공하려면 쓰기를 동기적으로 복제하거나 지역 간 읽기를 동기적으로 수행해야 합니다. 광속의 광역 네트워크 안정성은 강력한 일관성으로 인해 더 높은 대기 시간 및 데이터베이스 작업의 감소된 가용성이 발생함을 나타냅니다. 따라서 99번 째의 백분위수에서 보장되는 짧은 대기 시간, 모든 단일 지역 계정 및 평범한 일관성 수준의 모든 다중 지역 계정에 대한 99.99% 가용성 및 모든 다중 지역 데이터베이스 계정에 대한 99.999% 가용성을 보장하기 위해 서비스에서 비동기 복제를 사용해야 합니다. 그러려면 서비스에서 [잘 정의되고 완화된 일관성 모델](consistency-levels.md)을 제공해야 합니다. 강력한 일관성보다는 약하고(짧은 대기 시간과 가용성을 보장하기 위해) "결과적" 일관성보다는 강한(직관적 프로그래밍 모델을 사용하여) 것이 가장 이상적입니다.

Azure Cosmos DB는 특정 일관성 수준을 보장하기 위해 읽기 작업이 여러 하위 지역의 복제본에 연결할 필요가 없습니다. 마찬가지로, 데이터가 모든 지역에 복제되는 동안(예: 쓰기가 모든 지역에 비동기적으로 복제) 쓰기 작업이 차단되지 않습니다. 다중 지역 데이터베이스 계정의 경우 강력하고 여러 완화된 일관성 수준이 제공됩니다. 

### <a id="LatencyAndAvailability"></a>대기 시간과 가용성의 관계 
대기 시간과 가용성은 동전의 양면입니다. 안정적인 상태에서의 작업 대기 시간과 오류 발생 시의 가용성 및 네트워크 파티션에 대해 알아보겠습니다. 응용 프로그램의 관점에서, 느리게 실행되는 데이터베이스 작업과 사용할 수 없는 데이터베이스를 구분할 수 없습니다. 

높은 대기 시간과 사용 불가를 구분하기 위해 Azure Cosmos DB는 다양한 데이터베이스 작업의 대기 시간에 절대 상한값을 제공합니다. 데이터베이스 작업이 상한값보다 오래 걸리면 Azure Cosmos DB는 시간 초과 오류를 반환합니다. Azure Cosmos DB 가용성 SLA는 가용성 SLA에 대해 시간 제한을 계산하도록 보장합니다. 

### <a id="LatencyAndThroughput"></a>대기 시간과 처리량의 관계
Azure Cosmos DB는 사용자가 대기 시간과 처리량 사이에서 선택을 고민하게 만들지 않습니다. 99번 째의 백분위수의 대기 시간에 대한 SLA를 준수하고 사용자가 프로비전한 처리량을 제공합니다. 

## <a id="ConsistencyGuarantees"></a>일관성 보증
[강력한 일관성 모델](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)은 데이터 프로그래밍 기능에 대한 훌륭한 표준이지만, 긴 대기 시간(정상 상태)과 가용성 감소(오류 발생)를 감수해야 합니다. 

Azure Cosmos DB는 복제된 데이터의 일관성에 대해 추론할 수 있는 잘 정의된 프로그래밍 모델을 제공합니다. 사용자가 멀티 호밍 기능을 사용하여 전 세계적으로 분산된 응용 프로그램을 쉽게 빌드할 수 있도록 Azure Cosmos DB에서 노출하는 일관성 모델은 지역에 구애되지 않으며 읽기 및 쓰기가 처리되는 지역에 종속되지 않도록 설계됩니다. 

Azure Cosmos DB의 일관성 SLA는 100%의 읽기 요청이 사용자가 지정한 일관성 모델(데이터베이스 계정 또는 요청 수준에서)에 대한 일관성 보장을 충족하도록 보장합니다. 일관성 수준과 연결된 모든 일관성 보증이 충족되면 읽기 요청이 일관성 SLA를 충족하는 것으로 간주됩니다. 다음은 Azure Cosmos DB에서 제공하는 특정 일관성 모델에 해당하는 일관성 보장을 보여주는 테이블입니다.

<table>
    <tr>
        <td><strong>일관성 모델</strong></td>
        <td><strong>일관성 특성</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>강력</td>
        <td>선형화 가능</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">제한된 부실</td>
        <td>단조 읽기(지역 내)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>일관적인 접두사</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>부실 제한 &lt; K,T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">세션</td>
        <td>사용자 고유의 쓰기 읽기</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>단조 읽기</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>일관적인 접두사</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>일관적인 접두사</td>
        <td>일관적인 접두사</td>
        <td>100%</td>
    </tr>
</table>

**Azure Cosmos DB의 특정 일관성 모델과 연결된 일관성 보장**


### <a id="ConsistencyAndAvailability"></a>일관성과 가용성의 관계
[CAP 정리](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)의 [불가능성 결과](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)는 오류 발생 시 시스템이 가용성을 유지하고 선형화 가능한 일관성을 제공하기란 사실상 불가능하다는 것을 증명합니다. 데이터베이스 서비스는 CP 또는 AP 중에 선택해야 합니다. CP 시스템은 선형화 가능한 일관성을 위해 가용성을 포기하는 반면 AP 시스템은 가용성을 위해 [선형화 가능한 일관성](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)을 포기합니다. Azure Cosmos DB는 요청된 일관성 모델을 결코 위반하지 않으며, 따라서 형식적으로 CP 시스템이 됩니다. 그러나 실제로 일관성은 모 아니면 도의 문제가 아닙니다. 일관성 스펙트럼을 따라 선형화 가능한 일관성과 결과적 일관성 사이에는 잘 정의된 일관성 모델이 여러 개 있습니다. Azure Cosmos DB에서 우리는 실제 시나리오에 적용 가능하고 사용에 직관적인 여러 완화된 일관성 모델을 식별하기 위해 노력해 왔습니다. Azure Cosmos DB는 [아직은 평범한 수준이지만 잘 정의된 여러 개의 일관성 모델](consistency-levels.md), 모든 단일 지역 데이터베이스 계정에 대한 99.99% 가용성 및 모든 다중 지역 데이터베이스 계정에 대한 99.999% 읽기 및 쓰기 가용성을 제공하여 일관성과 가용성 간의 교환을 처리합니다. 

### <a id="ConsistencyAndAvailability"></a>일관성과 대기 시간의 관계
보다 포괄적인 CAP 정리의 변형은 [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)라고 하며 이 변형 역시 안정적인 상태에서 대기 시간과 일관성의 균형을 고려합니다. 안정적인 상태에서 데이터베이스 시스템은 일관성과 대기 시간 사이에서 선택해야 합니다. 완화된 일관성 모델(비동기 복제 및 로컬 읽기 및 쓰기 쿼럼을 통해 지원)이 여러 개 있는 Azure Cosmos DB는 모든 읽기 및 쓰기가 각각 읽기 및 쓰기 하위 지역에 로컬이 되도록 보장합니다. 따라서 Azure Cosmos DB는 지정된 일관성 모델에 대한 지역 내에서 짧은 대기 시간을 보장합니다.  

### <a id="ConsistencyAndThroughput"></a>일관성과 처리량의 관계
특정 일관성 모델의 구현은 선택하는 [쿼럼 유형](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)에 따라 결정되므로 처리량 역시 선택하는 일관성 모델에 따라 결정됩니다. 예를 들어 Azure Cosmos DB에서 강력한 일관성 읽기에 대한 RU 요금은 최종적인 일관성 읽기에 대한 RU 요금의 약 *두 배*입니다. 이 경우 동일한 처리량을 달성하려면 RU의 2배를 프로비전해야 합니다.


![일관성과 처리량 간의 관계](./media/distribute-data-globally/consistency-and-throughput.png)

**Azure Cosmos DB에서 특정 일관성 모델에 대한 읽기 용량의 관계**

## <a id="ThroughputGuarantees"></a>처리량 보장 
Azure Cosmos DB는 요구 사항 및 수요에 따라 다양한 지역의 처리량(및 저장소)을 탄력적으로 확장할 수 있습니다. 

![Azure Cosmos DB가 컬렉션을 분산 및 분할](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**단일 Azure Cosmos DB 컨테이너는 가로로 분할된 다음(지역 내에서 세 개의 리소스 파티션에서), 세 개의 Azure 지역에 걸쳐 전역적으로 분산됨**

Azure Cosmos DB 컨테이너는 두 차원에서, 즉 (i) 지역 내와 (ii) 하위 지역에 걸쳐 분산됩니다. 방법은 다음과 같습니다. 

* **로컬 배포**: 단일 하위 지역 내에서 Azure Cosmos DB 컨테이너는 *리소스 파티션*의 측면에서 가로로 확장됩니다. 각 리소스 파티션은 키 집합을 관리하고 *복제본 집합*이라고도 하는 네 개의 복제본으로 물리적으로 나타내는 강력한 일관성 및 고가용성이며 해당 복제본 간의 상태 시스템 복제입니다. Azure Cosmos DB는 전적으로 리소스를 통해 제어되는 시스템으로, 리소스 파티션은 할당된 시스템 리소스의 예산에 대한 처리량을 제공하는 일을 담당합니다. Azure Cosmos DB 컨테이너의 크기 조정은 사용자에게 완전히 투명합니다. Azure Cosmos DB는 리소스 파티션을 관리하고 필요에 따라 저장소 및 처리량 요구 사항이 변경되면 분할 및 병합합니다. 
* **지역 배포**: 다중 지역 데이터베이스인 경우 각 리소스 파티션은 해당 지역으로 분산됩니다. 다양한 지역에 걸쳐 동일한 키 집합을 소유하는 리소스 파티션이 *파티션 집합*을 구성합니다([이전 그림](#ThroughputGuarantees) 참조).  파티션 집합 내의 리소스 파티션은 데이터베이스와 연결된 여러 지역에서 상태 시스템 복제를 사용하여 조정됩니다. 구성된 일관성 수준에 따라 파티션 집합 내의 리소스 파티션은 여러 토폴로지(예: 별, 데이지 체인, 트리 등)를 사용하여 동적으로 구성됩니다. 

응답성이 뛰어난 파티션 관리, 부하 분산 및 엄격한 리소스 관리를 통해 Azure Cosmos DB는 Azure Cosmos DB 컨테이너 또는 데이터베이스와 연결된 여러 Azure 지역에서 처리량을 탄력적으로 확장할 수 있습니다. 프로비전된 처리량을 변경하는 것은 Azure Cosmos DB의 런타임 작업입니다. 다른 데이터베이스 작업과 마찬가지로 Azure Cosmos DB는 프로비전된 처리량을 변경하는 요청에 대한 대기 시간에 대한 절대 상한값을 보장합니다. 예를 들어 다음 그림은 수요에 따라 처리량이 탄력적으로 프로비전된(두 지역에 걸쳐 초당 1M-10M개 요청/초)와 고객의 컨테이너를 보여줍니다.

![Azure Cosmos DB가 처리량을 탄력적으로 프로비전](./media/distribute-data-globally/elastic-throughput.png)

**처리량이 탄력적으로 프로비전된(초당 1M-10M개 요청으로 다양한) 고객의 컨테이너**

### <a id="ThroughputAndConsistency"></a>처리량과 일관성의 관계 
[일관성과 처리량의 관계](#ConsistencyAndThroughput)에서 설명된 대로 똑같습니다.

### <a id="ThroughputAndAvailability"></a>처리량과 가용성의 관계
Azure Cosmos DB는 프로비전된 처리량이 변경되어도 높은 가용성을 계속 유지합니다. Azure Cosmos DB는 리소스 파티션을 투명하게 관리하며(및 분할, 병합 및 복제 작업 수행), 작업이 성능 또는 가용성을 떨어트리지 않고 응용 프로그램이 처리량을 탄력적으로 늘리거나 줄일 수 있도록 보장합니다. 

## <a id="AvailabilityGuarantees"></a>가용성 보장
Azure Cosmos DB는 모든 단일 지역 데이터베이스 계정 및 평범한 일관성 수준의 모든 다중 지역 계정에 대한 99.99% 가용성 SLA 및 모든 다중 지역 데이터베이스 계정에 대한 99.999% 가용성을 제공합니다. 앞서 설명했듯이, Azure Cosmos DB의 가용성 보장에는 모든 데이터 및 제어 평면 작업에 대한 대기 시간에 제공되는 절대 상한값이 포함됩니다. 가용성 보장은 지역 수 또는 지역 간의 지리적 거리에 따라 변경되지 않습니다. 가용성 보장은 수동 및 자동 장애 조치(failover) 모두에 대해 적용됩니다. Azure Cosmos DB는 응용 프로그램이 논리 끝점에 대해 작동하고 장애 조치(failover) 시 요청을 새 하위 지역에 투명하게 라우팅할 수 있도록 보장하는 투명한 멀티 호밍 API를 제공합니다. 지역 장애 조치(failover)의 경우 응용 프로그램을 다시 배포할 필요가 없으며 가용성 SLA가 항상 유지됩니다.

### <a id="AvailabilityAndConsistency"></a>가용성과 일관성, 대기 시간 및 처리량의 관계
가용성과 일관성, 대기 시간 및 처리량 사이의 관계는 [가용성과 일관성의 관계](#ConsistencyAndAvailability), [대기 시간과 가용성의 관계](#LatencyAndAvailability) 및 [처리량과 가용성의 관계](#ThroughputAndAvailability) 섹션에 설명되어 있습니다. 

## <a id="CustomerFacingSLAMetrics"></a>고객을 위한 SLA 메트릭
Azure Cosmos DB는 처리량, 대기 시간, 일관성 및 가용성 메트릭을 투명하게 표시합니다. 이러한 메트릭은 Azure Portal을 통해 프로그래밍 방식으로 액세스할 수 있습니다(아래 그림 참조). 또한 Azure Application Insights를 사용하여 다양한 임계값에 대한 경고를 설정할 수 있습니다.
 

![Azure Cosmos DB 고객에게 표시되는 SLA 메트릭](./media/distribute-data-globally/customer-slas.png)

**일관성, 대기 시간, 처리량 및 가용성 메트릭을 각 테넌트에 투명하게 제공**

## <a id="Next Steps"></a>다음 단계
* Azure Portal을 사용하여 Azure Cosmos DB 계정에 전역 복제를 구현하려면 [Azure Portal을 사용하여 Azure Cosmos DB 전역 데이터베이스 복제를 수행하는 방법](tutorial-global-distribution-sql-api.md)을 참조하세요.
* Azure Cosmos DB를 사용하여 다중 마스터 아키텍처를 구현하는 방법을 알아보려면 [Azure Cosmos DB를 사용하는 다중 마스터 데이터베이스 아키텍처](multi-region-writers.md)를 참조하세요.
* Azure Cosmos DB에서 자동 및 수동 장애 조치(failover)가 작동하는 방식을 알아보려면 [Azure Cosmos DB의 지역별 장애 조치(failover)](regional-failover.md)를 참조하세요.

## <a id="References"></a>참조
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
