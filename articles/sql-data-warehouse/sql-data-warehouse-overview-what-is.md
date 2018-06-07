---
title: Azure SQL Data Warehouse란? | Microsoft Docs
description: 페타바이트 볼륨의 관계형 및 비관계형 데이터를 처리할 수 있는 엔터프라이즈급 분산 데이터베이스입니다. 몇 초 만에 확장, 축소 및 일시 중지가 가능한 업계 최초의 클라우드 데이터 웨어하우스입니다.
services: sql-data-warehouse
author: igorstanko
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: c8e6af4eabc96845ff6c237735cdb0da2ed24743
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse란?

SQL Data Warehouse는 MPP(대규모 병렬 처리)를 활용하는 클라우드 기반 EDW(Enterprise Data Warehouse)이며 페타바이트 데이터에서 복잡한 쿼리를 신속하게 실행합니다. SQL Data Warehouse를 빅 데이터 솔루션의 주요 구성 요소로 사용합니다. 간단한 PolyBase T-SQL 쿼리를 사용하여 빅 데이터를 SQL Data Warehouse로 가져온 다음 MPP 기능을 사용하여 고성능 분석을 실행합니다. 통합하고 분석할 때 데이터 웨어하우스는 비즈니스가 정보에 대해 신뢰할 수 있는 단일 버전의 정보입니다.  


## <a name="key-component-of-big-data-solution"></a>빅 데이터 솔루션의 핵심 구성 요소
SQL Data Warehouse는 클라우드에서 종단 간 빅 데이터 솔루션의 핵심 구성 요소입니다.

![데이터 웨어하우스 솔루션](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

클라우드 데이터 솔루션에서 데이터는 다양한 원본의 빅 데이터 저장소에 수집됩니다. 빅 데이터 저장소에서는 Hadoop, Spark 및 Machine Learning 알고리즘을 통해 데이터를 준비하고 학습합니다. 복잡한 분석에 대한 데이터가 준비되면 SQL Data Warehouse는 PolyBase를 사용하여 빅 데이터 저장소를 쿼리합니다. PolyBase는 표준 T-SQL 쿼리를 사용하여 SQL Data Warehouse로 데이터를 가져옵니다.
 
SQL Data Warehouse는 열 형식 저장소를 사용하여 관계 테이블에 데이터를 저장합니다. 이 형식으로 인해 데이터 저장소 비용이 상당히 감소하고 쿼리 성능이 향상됩니다. SQL Data Warehouse에 데이터가 저장되면 대규모로 분석을 실행할 수 있습니다. 기존 데이터베이스 시스템에 비해 분석 쿼리는 몇 분이 아닌 몇 초, 몇 일이 아닌 몇 시간 안에 완료됩니다. 

분석 결과는 전 세계 보고 데이터베이스 또는 응용 프로그램으로 이동할 수 있습니다. 그러면 비즈니스 분석가가 합리적인 비즈니스 결정을 내리기 위한 정보를 파악할 수 있습니다.


## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse] 및 [샘플 데이터 로드][load sample data]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [지원 티켓 만들기]
* [MSDN 포럼]
* [Stack Overflow 포럼]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[지원 티켓 만들기]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 포럼]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
