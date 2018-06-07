---
title: Azure SQL Data Warehouse에 대한 질문과 대답 | Microsoft Docs
description: 이 문서에는 고객 및 개발자를 위한 Azure SQL Data Warehouse 관련 질문과 대답이 나와 있습니다.
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 7a570dfa6e0a2812a8dd3a25b8903c70fe07befc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse에 대한 질문과 대답

## <a name="general"></a>일반

Q. SQL DW는 데이터 보안을 위해 어떤 기능을 제공하나요?

a. SQL DW는 TDE 및 감사와 같이 데이터를 보호하기 위한 몇 가지 솔루션을 제공합니다. 자세한 내용은 [보안]을 참조하세요.

Q. SQL DW와 호환되는 법적 또는 비즈니스 표준은 어디에서 확인할 수 있나요?

a. SOC 및 ISO와 같은 제품에 대한 다양한 규정 준수 제안에 대해서는 [Microsoft 규정 준수] 페이지를 방문하세요. 먼저 규정 준수 제목으로 선택하고, 페이지 오른쪽의 Microsoft 범위 내 클라우드 서비스 섹션에서 Azure를 확장하여 Azure 서비스와 호환되는 서비스를 확인합니다.

Q. PowerBI를 연결할 수 있나요?

a. 예! PowerBI는 SQL DW를 사용한 직접 쿼리를 지원하지만 많은 수의 사용자 또는 실시간 데이터용은 아닙니다. PowerBI를 프로덕션용으로 사용하는 경우 Azure Analysis Services 또는 Analysis Services IaaS에서 PowerBI를 사용하는 것이 좋습니다. 

Q. SQL Data Warehouse 용량 제한은 얼마인가요?

a. 현재 [용량 제한] 페이지를 참조하세요. 

Q. 크기 조정/일시 중지/계속 작업이 너무 오래 걸리는 이유는 무엇인가요?

a. 다양한 요인이 계산 관리 작업 시간에 영향을 줄 수 있습니다. 장기 실행 작업의 일반적인 경우는 트랜잭션 롤백입니다. 크기 조정 또는 일시 중지 작업이 시작되면 들어오는 모든 세션이 차단되고 쿼리가 해제됩니다. 시스템을 안정된 상태로 유지하기 위해서는 작업을 시작하기 전에 트랜잭션을 롤백해야 합니다. 트랜잭션의 수가 많고 로그 크기가 클수록 시스템을 안정된 상태로 복원하기 위해 작업이 더 오래 중지됩니다.

## <a name="user-support"></a>사용자 지원

Q. 기능 요청이 있는 경우 어디에 제출하나요?

a. 기능 요청이 있는 경우 [UserVoice] 페이지에 제출하세요.

Q. 어떻게 하면 될까요?

a. SQL Data Warehouse를 사용하는 개발에 도움이 필요한 경우 [스택 오버플로] 페이지에서 질문할 수 있습니다. 

Q. 지원 티켓을 제출하려면 어떻게 해야 하나요?

a. [지원 티켓]은 Azure Portal을 통해 정리할 수 있습니다.

## <a name="sql-languagefeature-support"></a>SQL 언어/기능 지원 

Q. SQL Data Warehouse는 어떤 데이터 형식을 지원하나요?

a. SQL Data Warehouse [데이터 형식]을 참조하세요.

Q. 어떤 테이블 기능이 지원되나요?

a. SQL Data Warehouse는 많은 기능을 지원하지만 일부 지원되지 않는 기능은 [지원되지 않는 테이블 기능]에 나와 있습니다.

## <a name="tooling-and-administration"></a>도구 및 관리

Q. Visual Studio에서 데이터베이스 프로젝트가 지원되나요?

a. 현재 Visual Studio에서는 SQL Data Warehouse용 데이터베이스 프로젝트가 지원되지 않습니다. 이 기능 제공 요청에 참여하려면 사용자 의견 [데이터베이스 프로젝트 기능 요청]을 방문하세요.

Q. SQL Data Warehouse는 REST API를 지원하나요?

a. 예. SQL Database와 함께 사용할 수 있는 REST 기능 대부분을 SQL Data Warehouse에서도 사용할 수 있습니다. REST 설명서 페이지 또는 [MSDN]에서 이 API 정보를 찾을 수 있습니다.


## <a name="loading"></a>로드

Q. 어떤 클라이언트 드라이버가 지원되나요?

a. DW에 대한 드라이버 지원은 [연결 문자열] 페이지에서 찾을 수 있습니다.

Q: SQL Data Warehouse를 사용하는 PolyBase는 어떤 파일 형식을 지원하나요?

A: Orc, RC, Parquet 및 일반 구분 텍스트

Q: PolyBase를 사용하여 SQL DW에서 어디로 연결할 수 있나요? 

A: [Azure Data Lake Store] 및 [Azure Storage Blob]

Q: Azure Storage Blob 또는 ADLS에 연결할 때 계산 푸시다운이 가능한가요? 

A: 아니요. SQL DW PolyBase는 저장소 구성 요소와만 상호 작용합니다. 

Q: HDI에 연결할 수 있나요?

A: HDI는 HDFS 계층으로 ADLS 또는 WASB를 사용할 수 있습니다. 둘 중 하나를 HDFS 계층으로 사용하는 경우 해당 데이터를 SQL DW에 로드할 수 있습니다. 그러나 HDI 인스턴스로의 푸시다운 계산을 생성할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse 전반에 대한 자세한 내용은 [개요] 페이지를 참조하세요.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[연결 문자열]: ./sql-data-warehouse-connection-strings.md
[스택 오버플로]: http://stackoverflow.com/questions/tagged/azure-sqldw
[지원 티켓]: ./sql-data-warehouse-get-started-create-support-ticket.md
[보안]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft 규정 준수]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[용량 제한]: ./sql-data-warehouse-service-capacity-limits.md
[데이터 형식]: ./sql-data-warehouse-tables-data-types.md
[지원되지 않는 테이블 기능]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blob]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[데이터베이스 프로젝트 기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[개요]: ./sql-data-warehouse-overview-faq.md
