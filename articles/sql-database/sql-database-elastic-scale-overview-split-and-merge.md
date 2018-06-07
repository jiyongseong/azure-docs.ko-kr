---
title: 확장된 클라우드 데이터베이스 간 데이터 이동 | Microsoft Docs
description: 탄력적 데이터베이스 API를 사용하여 자체 호스팅되는 서비스를 통해 분할된 데이터베이스를 조작하고 데이터를 이동하는 방법에 대해 설명합니다.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c70279bd52f7b0b0e0cbc27742eca93d9af5e630
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>확장된 클라우드 데이터베이스 간 데이터 이동
갑자기 앱 수요가 엄청나게 증가한 상황에 처한 SaaS(Software as a Service) 개발자는 이러한 급증을 처리해야 합니다. 따라서 더 많은 데이터베이스(분할 된 데이터베이스)를 추가합니다. 데이터 무결성을 해치지 않고 어떻게 새 데이터베이스에 데이터를 재배포할 수 있을까요? **분할-병합 도구** 를 사용하여 제약 조건이 지정된 데이터베이스의 데이터를 새 데이터베이스로 이동합니다.  

분할-병합 도구는 Azure 웹 서비스로 실행됩니다. 관리자 또는 개발자는 이 도구를 사용하여 서로 다른 데이터베이스(분할된 데이터베이스) 간에 Shardlet(분할된 데이터베이스의 데이터)을 이동합니다. 이 도구는 분할된 데이터베이스 맵 관리를 사용하여 서비스 메타데이터 데이터베이스를 유지하며 일관된 매핑을 보장합니다.

![개요][1]

## <a name="download"></a>다운로드
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>문서화
1. [탄력적 데이터베이스 분할/병합 도구 자습서](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [분할-병합 보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [분할-병합 보안 고려 사항](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)
5. [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [탄력적 데이터베이스 도구](sql-database-elastic-scale-introduction.md)
7. [Elastic Database 도구 용어집](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>분할-병합 도구를 사용해야 하는 이유
**유연성**

응용 프로그램은 단일 Azure SQL DB 데이터베이스의 제한을 초과하여 유연하게 확장되어야 합니다. 무결성을 유지하면서 필요에 따라 데이터를 새 데이터베이스로 이동하기 위해 이 도구를 사용합니다.

**증가하는 분할** 

폭발적인 증가에 대처하기 위해 전체 용량을 증대해야 합니다. 이를 위해 데이터를 분할하고 용량 요구 사항이 충족될 때까지 늘어나는 데이터베이스에 걸쳐 배포하여 추가 용량을 제공합니다. 이것이 '분할' 기능의 가장 대표적인 예입니다. 

**축소하는 병합**

비즈니스의 계절별 특성으로 인해 용량을 축소해야 합니다. 비즈니스 활동이 줄어들면 이 도구를 사용하여 더 적은 규모로 축소할 수 있습니다. 탄력적인 확장 분할-합병 서비스의 '병합' 기능에서 이 요구 사항을 다룹니다. 

**shardlet 이동으로 핫스팟 관리**

데이터베이스당 여러 테넌트가 있는 경우 분할된 데이터베이스에 shardlet을 할당하면 일부 분할된 데이터베이스에서 용량 병목 현상이 발생할 수 있습니다. 이렇게 하려면 shardlet을 다시 할당하거나 사용량이 적은 shardlet을 새로운 또는 활용도가 적은 분할된 데이터베이스로 이동합니다. 

## <a name="concepts--key-features"></a>개념 및 주요 기능
**고객 호스트 서비스**

분할-병합은 고객 호스트 서비스로 제공됩니다. Microsoft Azure 구독에서 서비스를 배포하고 호스트해야 합니다. NuGet에서 다운로드하는 패키지에는 특정 배포에 대한 정보를 완료하기 위한 구성 템플릿이 있습니다. 자세한 내용은 [분할-병합 자습서](sql-database-elastic-scale-configure-deploy-split-and-merge.md) 를 참조하세요. 서비스가 Azure 구독에서 실행되므로 서비스의 대부분 보안 측면을 제어하고 구성할 수 있습니다. 기본 템플릿에는 SSL, 인증서 기반 클라이언트 인증, 저장된 자격 증명에 대한 암호화, DoS 보호 및 IP 제한 사항을 구성할 수 있는 옵션이 있습니다. [분할-병합 보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)문서에서 보안 측면에 대한 자세한 내용을 확인할 수 있습니다.

배포된 기본 서비스는 하나의 작업자 및 하나의 웹 역할로 실행됩니다. 각 서비스는 Azure Cloud Services에서 A1 VM 크기를 사용합니다. 패키지를 배포할 때 이러한 설정을 수정할 수 없지만 Azure 포털을 통해 실행 중인 클라우드 서비스에 배포한 후에는 변경할 수 있습니다. 기술적인 이유로 두 개 이상의 인스턴스에 대해 작업자 역할을 구성하지 않아야 합니다. 

**분할된 데이터베이스 맵 통합**

분할-병합 서비스는 응용 프로그램의 분할된 데이터베이스 맵을 조작합니다. 분할/병합 서비스를 사용하여 범위를 분할 또는 병합하거나 분할된 데이터베이스 간에 shardlet을 이동하는 경우 서비스에서 자동으로 분할된 데이터베이스 맵을 최신 상태로 유지합니다. 이렇게 하기 위해 서비스가 응용 프로그램의 분할된 데이터베이스 맵 관리자 데이터베이스에 연결하고 분할/병합 이동 요청이 진행될 때 범위 및 매핑을 유지합니다. 따라서 분할/병합 작업이 진행될 때 분할된 데이터베이스 맵이 항상 최신 뷰를 표시합니다. 분할, 병합 및 shardlet 이동 작업은 원본 분할된 데이터베이스에서 대상 분할된 데이터베이스로 shardlet 배치를 이동하여 구현됩니다. shardlet 이동 작업 중 현재 일괄 처리에서 실행되는 shardlet은 분할된 데이터베이스 맵에서 오프라인으로 표시되며 **OpenConnectionForKey** API를 사용하는 데이터 종속 라우팅에 사용할 수 없습니다. 

**일관된 Shardlet 연결 수**

새로운 shardlet 배치에 대한 데이터 이동이 시작되면 shardlet을 저장하는 분할된 데이터베이스에 대한 분할된 데이터베이스 맵 제공 데이터 종속 라우팅 연결이 중지되고 데이터 이동이 진행되는 동안 불일치를 방지하기 위해 분할된 데이터베이스 맵 API에서 해당 shardlet로의 후속 연결이 차단됩니다. 동일한 분할된 데이터베이스의 다른 shardlet에 대한 연결도 중지되지만 다시 시도하면 즉시 다시 성공합니다. 배치가 이동되면 대상 분할된 데이터베이스에 대한 shardlet이 다시 온라인으로 표시되고, 원본 데이터는 원본 분할된 데이터베이스에서 제거됩니다. 서비스는 모든 shardlet이 이동될 때까지 모든 배치에 대한 단계를 거칩니다. 그렇게 하면 전체 분할/병합/이동 작업 과정 중 몇 가지 연결 중지 작업이 수행됩니다.  

**Shardlet 가용성 관리**

위에서 설명한 대로 연결 중지를 shardlet의 현재 배치로 제한하면 사용할 수 없는 범위가 한 번에 shardlet의 한 배치로 제한됩니다. 이 접근 방식이 분할 혹은 병합 작업 과정 중 모든 전체 분할된 데이터베이스가 오프라인으로 유지되는 방법보다 선호됩니다. 한 번에 이동할 고유한 shardlet 수로 정의되는 배치의 크기가 구성 매개 변수입니다. 이 매개 변수는 응용 프로그램의 가용성 및 성능 요구에 따라 각 분할/병합 작업에 대해 정의할 수 있습니다. 분할된 데이터베이스 맵에서 잠금 중인 범위가 지정된 배치 크기보다 클 수 있습니다. 데이터의 실제 분할 키 값 수가 대략 배치 크기와 같아지도록 서비스가 범위 크기를 선택하기 때문입니다. 특히 드물게 채워진 분할 키에 대해 이 기능을 기억하는 것이 중요합니다. 

**메타데이터 저장소**

분할-병합 서비스는 데이터베이스를 사용하여 해당 상태를 유지 관리하고 요청을 처리하는 동안 로그를 유지합니다. 사용자가 해당 구독에서 이 데이터베이스를 만들고 서비스 배포용 구성 파일에서 이 데이터베이스에 대한 연결 문자열을 제공합니다. 사용자 조직의 관리자는 이 데이터베이스에 연결하여 요청 진행 상황을 검토하고 오류 발생 가능성에 대한 자세한 정보를 확인할 수 있습니다.

**분할 인식**

분할/병합 서비스는 (1) 분할된 테이블, (2) 참조 테이블 및 (3) 일반 테이블을 구분할 수 있습니다. 분할/병합/이동 작업의 의미 체계는 사용되는 테이블 유형에 따라 달라지고 다음과 같이 정의됩니다. 

* **Sharded tables**: 분할, 병합 및 이동 작업은 원본에서 대상 분할된 데이터베이스로 shardlet을 이동합니다. 전체 요청을 성공적으로 완료한 후에는 이러한 shardlet이 더 이상 원본에 존재하지 않습니다. 대상 테이블이 대상 분할된 데이터베이스에 있어야 할 필요는 없으며 작업을 처리하기 전에 대상 범위의 데이터를 포함해서는 안 됩니다. 
* **Reference tables**: 참조 테이블의 경우 분할, 병합 및 이동 작업이 원본에서 대상 분할된 데이터베이스로 데이터를 복사합니다. 그러나 대상의 이 테이블에 하나 이상의 행이 있는 경우 지정된 테이블에 대한 대상 분할된 데이터베이스에서는 어떤 변경도 발생하지 않습니다. 참조 테이블 복사 작업을 처리하려면 테이블이 비어 있어야 합니다.
* **Other Tables**: 기타 테이블이 분할 및 병합 작업의 원본 또는 대상에 있을 수 있습니다. 분할/병합 서비스는 임의의 데이터 이동 또는 복사 작업에 대해 이러한 테이블을 무시합니다. 단, 제약 조건이 있는 경우에는 이와 같은 작업이 정상적으로 수행되지 않을 수 있습니다.

참조 테이블과 분할된 테이블 비교 정보는 분할된 데이터베이스 맵의 **SchemaInfo** API에서 제공됩니다. 다음 예제에서는 지정된 분할된 데이터베이스 맵 관리자 개체 smm에서 이러한 API 사용을 보여 줍니다. 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

테이블의 'region' 및 'nation'은 참조 테이블로 정의되며 분할/병합/이동 작업을 통해 복사됩니다. 그런 다음 'customer' 및 'orders'는 분할된 테이블로 정의됩니다. C_CUSTKEY 및 O_CUSTKEY는 분할 키 역할을 합니다. 

**참조 무결성**

분할-병합 서비스는 테이블 간의 종속성을 분석하고 외래 키-기본 키 관계를 사용하여 참조 테이블 및 shardlet을 이동하는 작업을 준비합니다. 일반적으로 참조 테이블이 종속성 순서에 따라 먼저 복사된 다음 shardlet이 각 배치 내에서 해당 종속성의 순서에 따라 복사됩니다. 새 데이터가 도착하면 대상 분할된 데이터베이스에 대한 FK-PK 제약 조건이 적용되어야 하므로 이 과정이 필요합니다. 

**분할된 데이터베이스 맵 일관성 및 최종 완료**

오류가 발생한 경우 분할/병합 서비스는 중단 후 작업을 다시 시작하고 진행 중인 모든 요청을 완료하는 것을 목표로 합니다. 그러나 복구할 수 없는 상황(예: 대상 분할된 데이터베이스가 손실되거나 복구할 수 없을 정도로 손상된 경우)도 있을 수 있습니다. 이러한 상황에서는 이동해야 할 shardlet 중 일부가 계속 원본 분할된 데이터베이스에 상주할 수 있습니다. 이 서비스는 필요한 데이터를 대상에 복사한 후에만 shardlet 매핑이 업데이트되도록 합니다. 모든 데이터가 대상에 복사되고 해당하는 매핑이 업데이트된 후에만 Shardlet이 원본에서 삭제됩니다. 범위가 대상 분할된 데이터베이스에서 이미 온라인 상태인 동안 삭제 작업이 백그라운드에서 수행됩니다. 분할/병합 서비스는 분할된 데이터베이스 맵에 저장되는 매핑의 정확성을 항상 보장합니다.

## <a name="the-split-merge-user-interface"></a>분할/병합 사용자 인터페이스
분할-병합 서비스 패키지에는 웹 역할 및 작업자 역할이 포함됩니다. 웹 역할은 대화식으로 분할-병합 요청을 제출하는 데 사용됩니다. 사용자 인터페이스의 주요 구성 요소는 다음과 같습니다.

* Operation Type: 작업 유형은 이 요청에 대해 서비스에서 수행하는 작업의 종류를 제어하는 라디오 단추입니다. 분할, 병합 및 이동 시나리오 중에서 선택할 수 있습니다. 이전에 제출된 작업을 취소할 수도 있습니다. 범위 분할된 데이터베이스 맵에 분할, 병합 및 이동 요청을 사용할 수 있습니다. 목록 분할된 데이터베이스 맵은 이동 작업만 지원합니다.
* Shard Map: 요청 매개 변수의 다음 섹션에서는 분할된 데이터베이스 맵과 분할된 데이터베이스 맵을 호스트하는 데이터베이스에 대한 정보를 다룹니다. 특히, Azure SQL Database 서버의 이름과 shardmap을 호스트하는 데이터베이스의 이름, 분할된 데이터베이스 맵 데이터베이스에 연결하는 자격 증명, 마지막으로 분할된 데이터베이스 맵의 이름을 제공해야 합니다. 현재 작업은 단일 자격 증명 집합만 허용합니다. 이러한 자격 증명에는 분할된 데이터베이스 맵과 분할된 데이터베이스의 사용자 데이터를 변경할 수 있는 권한이 있어야 합니다.
* Source Range (split and merge): 분할 및 병합 작업은 해당 하위 키 및 상위 키를 사용하여 범위를 처리합니다. 바인딩되지 않은 상위 키 값으로 작업을 지정하려면 “High key is max” 확인란을 선택하고 상위 키 필드를 비워 둡니다. 지정한 범위 키 값은 분할된 데이터베이스 맵의 매핑 및 해당 경계와 정확히 일치하지 않아도 됩니다. 범위 경계를 지정하지 않으면 서비스가 가장 가까운 범위를 자동으로 유추합니다. GetMappings.ps1 PowerShell 스크립트를 사용하여 지정한 분할된 데이터베이스 맵에서 현재 매핑을 검색할 수 있습니다.
* Split Source Behavior(split): 분할 작업의 경우 원본 범위를 분할할 지점을 정의해야 합니다. 분할을 수행할 위치에 분할 키를 제공하여 이 작업을 수행합니다. 라디오 단추를 사용하여 범위의 하단 부분(분할 키 제외)을 이동할지 아니면 범위의 상단 부분(분할 키 포함)을 이동할지를 정의합니다.
* Source Shardlet (move): 이동 작업은 원본을 기술하는 범위가 필요하지 않으므로 분할 또는 병합 작업과 다릅니다. 이동의 원본은 이동할 분할 키 값으로 식별될 뿐입니다.
* Target Shard (split): 분할 작업의 원본에 대한 정보를 제공한 후에는 대상에 대해 Azure SQL DB 서버 및 데이터베이스 이름을 제공하여 데이터를 복사할 위치를 정의해야 합니다.
* Target Range(merge): 병합 작업은 shardlet을 기존의 분할된 데이터베이스로 이동합니다. 병합할 기존 범위의 범위 경계를 제공하여 기존의 분할된 데이터베이스를 식별합니다.
* Batch Size: 배치 크기는 데이터 이동 중 특정 시간에 오프라인으로 전환하는 shardlet의 수를 제어합니다. 이 값은 정수이며, shardlet의 장기 가동 중지 시간에 따른 영향을 받는 경우 더 적은 값을 사용할 수 있습니다. 값이 클수록 지정된 shardlet이 오프라인 상태인 시간이 길어지지만 성능은 향상될 수 있습니다.
* 작업 ID (취소): 더 이상 필요하지 않은 진행 중인 작업이 있는 경우 이 필드에서 해당 작업 ID를 제공하여 작업을 취소할 수 있습니다. 요청을 제출한 웹 브라우저의 출력이나 요청 상태 테이블(섹션 8.1 참조)에서 작업 ID를 검색할 수 있습니다.

## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항
분할/병합 서비스의 현재 구현에는 다음 요구 사항 및 제한 사항이 적용됩니다. 

* 분할된 데이터베이스가 있으며 분할된 데이터베이스 맵에 등록되어 있어야 해당 분할된 데이터베이스에 대한 분할/병합 작업을 수행할 수 있습니다. 
* 현재는 이 서비스가 테이블 또는 기타 데이터베이스 개체를 자동으로 만들지 않습니다. 즉, 모든 분할된 테이블 및 참조 테이블에 대한 스키마가 대상 분할된 데이터베이스에 존재해야 분할/병합/이동 작업이 가능합니다. 특히 분할된 테이블에서 분할/병합/이동 작업을 통해 새 shardlet을 추가할 범위가 비어 있어야 합니다. 그렇지 않은 경우 작업이 대상 분할된 데이터베이스의 초기 일관성 검사에 실패합니다. 또한 참조 테이블이 비어 있고 참조 테이블에 대한 다른 동시 쓰기 작업과 관련된 일관성 보장이 없는 경우에만 참조 데이터가 복사됩니다. 분할/병합 작업을 실행할 때는 참조 테이블을 변경하는 다른 쓰기 작업을 수행하지 않는 것이 좋습니다.
* 이 서비스는 분할 키를 포함하는 고유 인덱스 또는 키로 설정된 행 ID를 사용하여 대형 shardlet의 성능과 신뢰성을 개선합니다. 따라서 서비스가 분할 키 값보다 훨씬 더 세밀하게 데이터를 이동할 수 있습니다. 이런 기능은 작업 중 필요한 잠금 및 로그 공간의 최대 양을 줄이는 데 도움이 됩니다. 분할/병합/이동 요청과 함께 테이블을 사용하려는 경우 지정된 테이블의 분할 키를 포함하는 기본 키 또는 고유 인덱스를 만드는 것이 좋습니다. 성능상의 이유로 분할 키는 키 또는 인덱스의 선행 열이어야 합니다.
* 요청 처리 과정에서 일부 shardlet 데이터는 원본 및 대상 분할된 데이터베이스 모두에 있을 수 있습니다. shardlet 이동 중 오류가 발생하지 않도록 보호하기 위해 필요합니다. 분할된 데이터베이스 맵과 분할-병합을 통합하면 분할된 데이터베이스 맵에서 **OpenConnectionForKey** 메서드를 사용하여 데이터 종속 라우팅 API를 통해 설정한 연결에서는 불일치하는 중간 상태가 표시되지 않습니다. 그러나 **OpenConnectionForKey** 메서드를 사용하지 않고 원본 또는 대상 분할된 데이터베이스에 연결할 경우 분할/병합/이동 요청이 진행될 때 불일치하는 중간 상태가 표시될 수도 있습니다. 이러한 연결은 타이밍이나 분할된 데이터베이스 기본 연결에 따라 부분적인 결과나 중복된 결과를 표시할 수 있습니다. 현재 이러한 제한 상황에는 탄력적인 확장 다중 분할된 데이터베이스 쿼리를 통해 생성된 연결이 포함됩니다.
* 분할/병합 서비스용 메타데이터 데이터베이스를 여러 역할 간에 공유하지 않아야 합니다. 예를 들어 스테이징 환경에서 실행되는 분할/병합 서비스의 역할이 프로덕션 역할과는 다른 메타데이터 데이터베이스를 가리켜야 합니다.

## <a name="billing"></a>결제
분할 병합 서비스는 Microsoft Azure 구독에서 클라우드 서비스로 실행 됩니다. 따라서 서비스 인스턴스의 클라우드 서비스에 대한 요금이 적용 됩니다. 분할/병합/이동 작업을 자주 수행하지 않는 한 분할/병합 클라우드 서비스를 삭제하는 것이 좋습니다. 그렇게 하면 실행 중이거나 배포된 클라우드 서비스 인스턴스의 비용이 절감됩니다. 분할/병합 작업을 수행해야 할 때마다 즉시 실행 가능한 구성을 다시 배포하고 시작할 수 있습니다. 

## <a name="monitoring"></a>모니터링
### <a name="status-tables"></a>상태 테이블
분할-병합 서비스는 완료된 요청 및 진행 중인 요청의 모니터링을 위해 메타데이터 저장소 데이터베이스에서 **RequestStatus** 테이블을 제공합니다. 이 테이블은 분할/병합 서비스의 이 인스턴스에 제출된 각 분할/병합 요청에 대한 행을 나열하며, 각 요청에 대해 다음 정보를 제공합니다.

* **타임스탬프**: 요청이 시작된 날짜와 시간입니다.
* **작업 ID**: 요청을 고유하게 식별하는 GUID입니다. 이 요청을 사용하여 아직 진행 중인 작업을 취소할 수도 있습니다.
* **상태**: 요청의 현재 상태입니다. 진행 중인 요청의 경우 요청이 있는 현재 단계도 나열됩니다.
* **취소 요청**: 요청이 취소되었는지 여부를 나타내는 플래그입니다.
* **진행률**: 작업의 예상 완료율입니다. 값이 50이면 작업이 약 50% 완료된 것입니다.
* **세부 정보**: 더 자세한 진행률 보고서를 제공하는 XML 값입니다. 진행률 보고서는 행 집합이 원본에서 대상으로 복사될 때 정기적으로 업데이트됩니다. 오류 또는 예외 발생 시 이 열에는 오류에 대한 자세한 내용이 포함됩니다.

### <a name="azure-diagnostics"></a>Azure 진단
분할 병합 서비스 모니터링 및 진단에 Azure SDK 2.5에 따라 Azure 진단을 사용합니다. [Azure Cloud Services 및 Virtual Machines에서 진단 사용](../cloud-services/cloud-services-dotnet-diagnostics.md)에 설명된 대로 진단 구성을 제어합니다. 다운로드 패키지에는 웹 역할용 하나와 작업자 역할용 하나, 총 두 개의 진단 구성이 포함되어 있습니다. 서비스에 대한 해당 진단 구성은 [Microsoft Azure에서 클라우드 서비스 기본 사항](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649)의 지침을 따릅니다. 여기에는 성능 카운터, IIS 로그, Windows 이벤트 로그 및 분할/병합 응용 프로그램 이벤트 로그를 기록하는 정의가 포함됩니다. 

## <a name="deploy-diagnostics"></a>진단 배포 
모니터링 및 진단 구성을 사용하여NuGet 패키지에서 제공 하는 웹 및 작업자 역할에 대한 진단 유틸리티를 사용하려면 Azure PowerShell을 사용하여 다음 명령을 실행 합니다. 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

[Azure Cloud Services 및 Virtual Machines에서 진단 사용](../cloud-services/cloud-services-dotnet-diagnostics.md)에서 진단 설정을 구성 및 배포하는 방법에 자세한 내용을 확인할 수 있습니다. 

## <a name="retrieve-diagnostics"></a>진단 검색
서버 탐색기 트리의 Azure 부분에 있는 Visual Studio 서버 탐색기에서 진단에 쉽게 액세스할 수 있습니다. Visual Studio 인스턴스를 열고 메뉴 모음에서 보기와 서버 탐색기를 차례로 클릭합니다. Azure 아이콘을 클릭하여 Azure 구독에 연결합니다. 그런 다음 Azure -> 저장소-> <your storage account>-> 테이블 -> WADLogsTable로 이동합니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 찾아보기](http://msdn.microsoft.com/library/azure/ff683677.aspx)를 참조하세요. 

![WADLogsTable][2]

위의 그림에서 강조 표시된 WADLogsTable에는 분할/병합 서비스의 응용 프로그램 로그에 있는 자세한 이벤트가 포함됩니다. 다운로드한 패키지의 기본 구성이 프로덕션 배포에 맞춰 조정됩니다. 그렇기 때문에, 서비스 인스턴스에서 로그 및 카운터를 가져오는 간격이 큽니다(5 분). 테스트 및 개발을 위해 필요에 따라 웹 또는 작업자 역할의 진단 설정을 조정하여 간격을 낮출 수 있습니다. Visual Studio 서버 탐색기(위 참조)의 역할을 마우스 오른쪽 단추로 클릭하여 이 작업을 수행한 다음 진단 구성 설정용 대화 상자에서 전송 기간을 조정합니다. 

![구성][3]

## <a name="performance"></a>성능
일반적으로 Azure SQL Database에서 성능 서비스 계층이 높고 뛰어날수록 더 나은 성능을 기대할 수 있습니다. 서비스 계층이 높을수록 IO, CPU 및 메모리 할당을 높이면 분할/병합 서비스를 사용하는 대량 복사 및 삭제 작업의 효율이 높아집니다. 이런 이유로 정의되고 제한된 기간 해당 데이터베이스에 대해서만 서비스 계층을 증가시킵니다.

서비스는 정상적인 작업의 일부로 유효성 검사 쿼리를 수행합니다. 이러한 유효성 검사 쿼리는 대상 범위의 예기치 않은 데이터 존재를 확인하고 일관성 있는 상태에서 모든 분할/병합/이동 작업이 시작되는지 확인합니다. 이러한 쿼리는 모두 요청 정의의 일부로 제공된 배치 크기 작업의 범위로 정의된 분할 키 범위에 대해 작동합니다. 그리고 이러한 쿼리는 분할 키를 선행 열로 포함하는 인덱스가 있는 경우에 최상의 성능을 보입니다. 

또한 분할 키를 선행 열로 포함하는 고유성 속성을 통해 서비스는 로그 공간 및 메모리와 관련하여 리소스 사용을 제한하는 최적화된 접근 방식을 사용할 수 있습니다. 이 고유성 속성은 대형 데이터 크기(일반적으로 1GB 이상)를 이동하는 데 필요합니다. 

## <a name="how-to-upgrade"></a>업그레이드하는 방법
1. [분할-병합 서비스 배포](sql-database-elastic-scale-configure-deploy-split-and-merge.md)의 단계를 따릅니다.
2. 분할/병합 배포의 클라우드 서비스 구성 파일을 새 구성 매개 변수를 반영하도록 변경합니다. 새 필수 매개 변수는 암호화에 사용되는 인증서에 대한 정보입니다. 이 작업을 쉽게 수행하는 방법은 다운로드한 새 구성 템플릿 파일을 기존 구성과 비교하는 것입니다. 웹 역할과 작업자 역할 둘 다에 대해 “DataEncryptionPrimaryCertificateThumbprint” 및“DataEncryptionPrimary” 설정을 추가합니다.
3. 업데이트를 Azure에 배포하기 전에 현재 실행 중인 모든 분할/병합 작업이 완료되었는지 확인합니다. 분할/병합 메타데이터 데이터베이스에의 RequestStatus 및 PendingWorkflows 테이블에서 진행 중인 요청을 쿼리하여 쉽게 확인할 수 있습니다.
4. Azure 구독의 분할/병합에 대한 기존 클라우드 서비스 배포를 새 패키지 및 업데이트된 서비스 구성 파일로 업데이트합니다.

업그레이드하기 위해 분할-병합용 새 메타데이터 데이터베이스를 프로비전할 필요는 없습니다. 새 버전은 기존 메타데이터 데이터베이스를 새 버전으로 자동으로 업그레이드합니다. 

## <a name="best-practices--troubleshooting"></a>모범 사례, 문제 해결:
* 테스트 테넌트를 정의하고 여러 분할된 데이터베이스에 분산되어 있는 테스트 테넌트에서 가장 중요한 분할/병합/이동 작업을 연습해 볼 수 있습니다. 이렇게 하면 모든 메타데이터가 분할된 데이터베이스 맵에서 올바르게 정의되어 있으며 작업이 제약 조건 또는 외래 키를 위반하지 않는지 확인할 수 있습니다.
* 데이터 크기 관련 문제가 발생하지 않도록 테스트 테넌트 데이터 크기를 가장 큰 테넌트의 최대 데이터 크기보다 크게 유지해야 합니다. 단일 테넌트를 이동하는 데 걸리는 시간에 대한 상한을 평가하는 데에도 도움이 됩니다. 
* 스키마 삭제를 허용 하는지 확인합니다. 분할/병합 서비스에는 데이터를 대상으로 복사한 후 원본 분할된 데이터베이스에서 데이터를 제거하는 기능이 필요합니다. 예를 들어 **삭제 트리거** 로 인해 서비스가 원본의 데이터를 삭제하지 못하여 작업이 실패할 수 있습니다.
* 분할 키가 기본 키 또는 고유 인덱스 정의에서 선행 열인지 확인합니다. 그러면 항상 분할 키 범위에서 작동하는 실제 데이터 이동 및 삭제 작업과 분할/병합 유효성 검사 쿼리에 대한 최상의 성능이 보장됩니다.
* 분할 병합 서비스 데이터베이스가 상주 하는 지역 및 데이터 센터에 배치 합니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

