---
title: "Azure Data Catalog의 새로운 기능 | Microsoft Docs"
description: "이 문서는 Azure Data Catalog에 추가된 새로운 기능의 개요를 제공합니다."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 9fb7814a8412200f6d31cfb9dcaee4663d7cea97
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="whats-new-in-azure-data-catalog"></a>Azure 데이터 카탈로그의 새로운 기능
**Azure Data Catalog**에 대한 업데이트는 정기적으로 릴리스됩니다. 일부 릴리스에서 백 엔드 서비스 기능에 초점을 맞추므로 모든 릴리스는 새로운 사용자용 기능을 포함하지 않습니다. 이 페이지는 Azure Data Catalog 서비스에 추가된 새로운 사용자용 기능을 강조 표시합니다.

## <a name="whats-new-for-november-2017"></a>2017년 11월의 새로운 기능 
2017년 11월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 데이터 카탈로그 포털에서 특정 비즈니스 용어에 대한 직접 연결을 지원합니다. 사용자가 비즈니스 용어의 링크를 복사하여 문서, 이메일, 보고서 또는 기타 위치에 포함시켜 용어 정의에 직접 연결할 수 있습니다.
* Azure Active Directory 서비스 주체를 지원합니다. 데이터 카탈로그 관리자는 서비스 주체를 사용하여 카탈로그에 액세스하도록 클라이언트 응용 프로그램을 인증하고, 사용자 및 보안 그룹에 권한을 부여하는 것처럼 그러한 응용 프로그램에게도 특정 권한을 부여할 수 있습니다. 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/active-directory-application-objects.md)를 참조하세요.
* 데이터 카탈로그 데이터 원본 등록 도구를 사용하여 Azure SQL Database 및 Azure SQL Data Warehouse 데이터 원본에 연결할 때 Azure Active Directory 인증을 지원합니다. 자세한 내용은 [SQL Database 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](../sql-database/sql-database-aad-authentication.md)을 참조하세요.


## <a name="whats-new-for-september-2017"></a>2017년 9월의 새로운 기능 
2017년 9월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 데이터 원본 등록 도구를 사용하여 관련 테이블을 등록할 때 DB2 데이터 원본에서 조인 관계 메타데이터를 추출하는 것에 대한 지원
* 데이터 원본 등록 도구를 사용하여 MongoDB 버전 3.4 데이터 원본 등록에 대한 지원
* 데이터 카탈로그에서 데이터베이스 또는 기타 컨테이너를 제거할 때 한 번의 작업에 포함된 개체에 대한 모든 메타데이터 삭제에 대한 지원
* 데이터 카탈로그 포털에서 검색을 구체화할 때 최대 1,000개의 태그, 비즈니스 용어집 용어 또는 다른 검색 패싯 보기에 대한 지원


## <a name="whats-new-for-august-2017"></a>2017년 8월의 새로운 기능 
2017년 8월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

*   Data Catalog REST API를 사용하여 관계 메타데이터를 만들고 관리하는 데 새로운 개발자 샘플을 사용할 수 있습니다. *데이터 카탈로그로 관계 정보 가져오기* 샘플을 [데이터 카탈로그 코드 샘플 페이지](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0)에서 사용할 수 있습니다. 
* 데이터 원본 등록 도구를 사용하여 관련 테이블을 등록할 때 Teradata 데이터 원본에서 조인 관계 메타데이터를 추출하는 것에 대한 지원
* 데이터 원본 등록 도구를 사용하여 SQL Server 데이터 원본을 등록할 때 SQL Server TVF(테이블 반환 함수) 개체에 대한 지원
* 데이터 카탈로그 포털의 성능 및 유용성을 향상시키기 위한 여러 가지 업데이트 및 구체화

## <a name="whats-new-for-july-2017"></a>2017년 7월의 새로운 기능 
2017년 7월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   다음과 같이 허용된 메타데이터 작업에 대한 보다 세분화된 제어 지원:
    - 카탈로그 관리자는 태그 및 관련 메타데이터를 카탈로그에 기고하는 사용자의 기능을 제한하여 카탈로그에 대한 읽기 전용 액세스를 사용하도록 할 수 있습니다.
    - 카탈로그 관리자는 카탈로그에 새 데이터 원본을 등록하는 사용자 기능을 제한할 수 있습니다.
    - 카탈로그 관리자는 카탈로그에서 데이터 자산 메타데이터의 소유권을 갖는 사용자의 기능을 제한할 수 있습니다.
    - 권한 관리가 용이하도록 Azure Active Directory 보안 그룹 및 사용자에게 사용 권한을 부여할 수 있습니다.
* 다음과 같이 등록된 데이터 자산 간의 관계 및 데이터 카탈로그 포털에서 관련된 데이터 자산 검색에 대한 지원:
    - 데이터 카탈로그 데이터 원본 등록 도구를 사용할 때 SQL Server(Azure SQL Database 포함), Oracle 및 MySQL 데이터 원본에서 관계 메타데이터 추출
    - 데이터 카탈로그 포털에서 자산 메타데이터를 볼 때 관련 데이터 자산 검색
    - Data Catalog REST API를 사용하여 데이터 자산 간의 관계를 정의, 검색 및 관리하는 작업

데이터 카탈로그에서 사용 권한 관리에 대한 자세한 내용은 [데이터 카탈로그 및 데이터 자산에 안전하게 액세스하는 방법](data-catalog-how-to-secure-catalog.md)을 참조하세요.
데이터 카탈로그에서 관계에 대한 자세한 내용은 [Azure Data Catalog에서 관련 데이터 자산을 보는 방법](data-catalog-how-to-view-related-data-assets.md)을 참조하세요.

## <a name="whats-new-for-june-2017"></a>2017년 6월의 새로운 기능 
2017년 6월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   Sybase, Apache Cassandra 및 MongoDB 데이터 원본에 대한 지원 사용자는 이제 Cassandra 및 MongoDB 데이터베이스 및 테이블, Sybase 데이터베이스, 테이블 및 뷰를 등록 및 검색할 수 있습니다.

> [!NOTE]
> 레코드 및 배열과 같은 복잡한 데이터 형식의 열을 포함하는 MongoDB 및 Cassandra 테이블을 등록할 때 이러한 열은 데이터 카탈로그에 추가된 메타데이터에 포함되지 않습니다.

## <a name="whats-new-for-may-2017"></a>2017년 5월의 새로운 기능 
2017년 5월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   새로운 개발자 샘플은 비즈니스 용어의 대량 가져오기를 위해 사용할 수 있습니다. 용어집 대량 가져오기 샘플은 [데이터 카탈로그 개발자 샘플 페이지](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples)에서 사용할 수 있습니다. 
*   Azure Data Catalog 포털에서 ODBC 연결 정보 편집에 대한 지원 데이터 자산 소유자 및 데이터 카탈로그 관리자는 이제 데이터 원본을 다시 등록하지 않고도 등록된 ODBC 데이터 원본에 대한 연결 정보를 편집할 수 있습니다.
*   비즈니스 용어집 정의 및 설명에서 클릭할 수 있는 URL에 대한 지원 URL이 비즈니스 용어에 대한 설명 및 정의 속성에 포함되는 경우 URL은 데이터 카탈로그 포털에서 하이퍼링크로 표시됩니다.
*   전문가 전자 메일 주소 외에 전문가 이름 표시에 대한 지원 데이터 카탈로그 포털의 데이터 자산에 대한 속성에서 전문가를 볼 때 Azure Active Directory에서 전문가의 전체 이름이 도구 설명에 표시됩니다.

## <a name="whats-new-for-april-2017"></a>2017년 4월의 새로운 기능 
2017년 4월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   ODBC 데이터 원본에 대한 지원 사용자는 이제 데이터 카탈로그 데이터 원본 등록 도구를 사용하여 ODBC 데이터베이스, 테이블 및 뷰를 등록 및 검색할 수 있습니다.

## <a name="whats-new-for-march-2017"></a>2017년 3월의 새로운 기능 
2017년 3월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   데이터 카탈로그 관리자를 위한 AAD 보안 그룹 사용에 대한 지원
*   이제 새로운 Azure 지역에서 Azure Data Catalog를 사용할 수 있습니다. 고객은 미국 동부, 미국 서부, 유럽 서부 및 오스트레일리아 동부, 북유럽과 동남 아시아 외에도 미국 서중부 지역에서 Azure Data Catalog를 프로비전할 수 있습니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

## <a name="whats-new-for-february-2017"></a>2017년 2월의 새로운 기능 
2017년 2월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   데이터 카탈로그 데이터 원본 등록 도구에서 고급 설정에 대한 지원 사용자는 대규모 데이터 원본을 등록할 때 명령 제한 시간 값을 지정할 수 있습니다.
*   FTP 및 PostgreSQL 데이터 원본에 대한 지원 사용자는 이제 FTP 파일 및 디렉터리 및 PostgreSQL 데이터베이스, 테이블 및 뷰를 등록 및 검색할 수 있습니다.

## <a name="whats-new-for-january-2017"></a>2017년 1월의 새로운 기능 
2017년 1월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   Azure Data Catalog는 이제 [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) 규정을 준수합니다.
*   [Excel 2016 및 Excel용 파워 쿼리에서 가져오기 및 변환](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605)과 통합 Excel 사용자는 Excel 내에서 Azure Data Catalog를 사용하여 쿼리를 공유하고 검색할 수 있습니다. 이 기능은 Power BI Pro 라이선스를 갖고 있는 사용자에게 제공됩니다.

## <a name="whats-new-for-december-2016"></a>2016년 12월의 새로운 기능
2016년 12월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   Azure Data Catalog는 이제 [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) 및 [EU 모델 조항](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses) 규정을 준수합니다.
*   데이터 원본 연결 정보 편집에 대한 지원 데이터 자산 소유자 및 데이터 카탈로그 관리자는 이제 데이터 원본을 다시 등록하지 않고도 등록된 데이터 원본에 대한 연결 정보를 편집할 수 있습니다.
*   Salesforce.com 데이터 원본에 대한 지원 사용자는 이제 Salesforce 개체를 등록하고 검색할 수 있습니다.


## <a name="whats-new-for-november-2016"></a>2016년 11월의 새로운 기능
2016년 11월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.
*   Azure Data Catalog는 이제 [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) 및 [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) 규정을 준수합니다.
*   데이터 카탈로그 포털 및 REST API를 사용하여 ODBC 데이터 원본의 수동 등록에 대한 지원

## <a name="whats-new-for-september-2016"></a>2016년 9월의 새로운 기능
2016년 9월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* IBM DB2 데이터 원본에 대한 지원입니다. 이제 사용자가 DB2 데이터베이스, 테이블 및 뷰를 등록하고 검색할 수 있습니다.
* Azure Cosmos DB 데이터 원본을 지원합니다. 이제 사용자가 Cosmos DB 데이터베이스 및 컬렉션을 등록하고 검색할 수 있습니다.
* 데이터 카탈로그 포털에서 카탈로그 이름을 사용자 지정하는 것에 대한 지원. 이제 카탈로그 관리자는 포털 제목에 표시될, 조직 이름과 같은 텍스트를 제공할 수 있습니다.

## <a name="whats-new-for-august-2016"></a>2016년 8월의 새로운 기능
2016년 8월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* SQL Server Master Data Services (MDS) 데이터 원본 등록에 대한 개선. 이제 사용자는 데이터 카탈로그 데이터 원본 등록 도구를 사용하여 MDS 엔터티를 등록할 때 미리 보기 및 데이터 프로필을 포함할 수 있습니다.
* 관리자 정의된 조직적으로 저장된 검색을 지원합니다. 데이터 카탈로그 포털에서 검색을 저장할 때 데이터 카탈로그 관리자는 이제 개인적인 용도 또는 모든 카탈로그 사용자를 위해 검색을 저장하도록 선택할 수 있습니다. 조직적으로 저장된 검색은 모든 카탈로그 사용자와 공유되며, 데이터 원본 검색에 대한 표준화된 시작점을 제공할 수 있습니다.
* 데이터 카탈로그 포털에서 속성 보기를 업데이트합니다. 이제 모든 데이터 자산 속성은 크기 조정이 가능한 단일 창에 표시되고 관리되어 보다 일관되고 검색 가능한 환경을 제공합니다.

## <a name="whats-new-for-july-2016"></a>2016년 7월의 새로운 기능
2016년 7월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* SQL Server Master Data Services (MDS) 데이터 원본에 대한 지원. 이제 사용자가 MDS 모델 및 엔터티를 등록하고 검색할 수 있습니다.
* SQL Server 저장 프로시저에 대한 지원. 이제 사용자는 SQL Server 데이터 원본에서 저장 프로시저 개체를 등록하고 검색할 수 있습니다.
* Azure Data Catalog 포털 및 데이터 원본 등록 도구에서 추가 언어를 지원하며, 지원되는 언어는 모두 18개입니다. Azure Data Catalog 사용자 환경이 Windows 또는 웹 브라우저에 설정된 언어 기본 설정에 따라 지역화됩니다.
* 성능 개선 및 간소화된 사용자 환경을 포함한, 데이터 카탈로그 포털 홈 페이지에 대한 업데이트 및 개선.

## <a name="whats-new-for-june-2016"></a>2016년 6월의 새로운 기능
2016년 6월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 데이터 카탈로그 포털에서 데이터 자산을 검색할 때 목록 보기에서 열 크기 조정에 대 한 지원. 이제 사용자는 태그 및 설명과 같은 긴 자산 메타데이터를 보다 쉽게 읽을 수 있도록 열 크기를 개별적으로 조정할 수 있습니다.
* Excel 용 파워 쿼리가 데이터 카탈로그 포털의 "열기" 메뉴에 추가되었습니다. 이제 사용자는 지원되는 데이터 원본을 [Excel 용 파워 쿼리](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) 추가 기능이 설치된 Excel 2016이나 Excel 2010 및 Excel 2013에서 열 수 있습니다.
* Azure Table Storage 데이터 원본에 대한 지원. 이제 사용자는 Azure Storage 데이터 원본에서 테이블 개체를 등록하고 검색할 수 있습니다.

## <a name="whats-new-for-may-2016"></a>2016년 5월의 새로운 기능
2016년 5월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 카탈로그 관리자가 일반적인 비즈니스 어휘를 생성하기 위해 비즈니스 용어 및 계층 구조를 정의할 수 있는 비즈니스 용어집. 사용자는 등록된 데이터 자산에 용어를 사용하여 태그를 지정할 수 있고, 카탈로그의 콘텐츠를 보다 쉽게 검색하고 이해할 수 있습니다. 자세한 내용은 [관리 태그 지정을 위해 비즈니스 용어집을 설정하는 방법](data-catalog-how-to-business-glossary.md)  
* 사용자가 한 번에 여러 용어를 업데이트할 수 있도록 데이터 카탈로그 비즈니스 용어집이 개선되었습니다. 사용자는 여러 용어를 선택하여 다음 필드를 편집할 수 있습니다.
  * 상위 용어: 사용자는 새 상위 용어를 선택할 수 있으며, 선택한 모든 용어는 선택한 상위 용어의 하위 용어로 업데이트됩니다. 선택한 용어의 상위 용어가 같으면 해당 상위 용어가 텍스트 상자에 표시되고 그렇지 않으면 상위 용어 필드가 비어 있게 됩니다.   
  * 태그 및 관련자: 여러 데이터 자산에 태그를 지정하는 것과 동일한 환경을 사용하여 여러 용어에 대한 태그 및 관련자를 제거할 수 있습니다.

> [!NOTE]
> 비즈니스 용어집은 Azure Data Catalog 표준 버전에서만 사용할 수 있습니다. 무료 버전에는 관리 태그 지정 또는 비즈니스 용어집 기능이 제공되지 않습니다.

## <a name="whats-new-for-march-2016"></a>2016년 3월의 새로운 기능
2016년 3월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* Azure Data Catalog 서비스의 검색 기능 및 카탈로그 자산 관리 기능에 프로그래밍 방식으로 액세스하기 위한 통합 REST API 끝점. 이 검색 API 끝점 및 카탈로그 API 끝점은 더 이상 사용되지 않으며 2016년 3월 21일에 중단되었습니다. API의 의미 체계에 대한 변경 사항은 없습니다. 끝점 URI만 변경되었습니다. 자세한 내용은 [Azure Data Catalog REST API 참조](https://msdn.microsoft.com/library/azure/mt267595.aspx)를 참조하세요. API 샘플을 보려면 [Azure Data Catalog 개발자 샘플](data-catalog-samples.md)을 참조하세요.

## <a name="whats-new-for-february-2016"></a>2016년 2월의 새로운 기능
2016년 2월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* Azure Data Catalog 데이터 원본 등록 도구의 데이터 소스 선택 환경이 새로 디자인되었습니다. 사용자가 Azure Data Catalog에서 지원하는 데이터 원본을 더욱 쉽게 찾아서 선택할 수 있도록 데이터 원본 등록 도구가 업데이트되었습니다.
* Azure Data Catalog 포털 및 데이터 원본 등록 도구에서 추가 언어 10개가 지원됩니다. 이제 Azure Data Catalog 환경이 영어 외에도 독일어, 스페인어, 프랑스어, 이탈리아어, 일본어, 한국어, 포르투갈어(브라질), 러시아어, 중국어 간체 및 중국어 번체로 제공됩니다. Azure Data Catalog 사용자 환경이 Windows 또는 사용자의 웹 브라우저에 설정된 언어 기본 설정에 따라 지역화됩니다.
* 비즈니스 연속성 및 재해 복구를 위해 Azure Data Catalog 데이터에 대한 지역에서 복제가 지원됩니다. 이제 데이터 원본 메타데이터 및 크라우드소싱 주석을 비롯한 모든 Azure Data Catalog 내용이 두 Azure 지역 간에 무료로 복제됩니다. Azure 지역은 500마일 이상 떨어진 지역이 사전에 쌍으로 연결되며, [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍으로 연결된 지역](../best-practices-availability-paired-regions.md)에 설명된 대로 매핑을 따릅니다.
* Azure Data Catalog에서 사용하는 Azure 구독을 변경할 수 있습니다. Azure Data Catalog 관리자가 Azure Data Catalog 포털의 설정 페이지에서 청구용으로 다른 Azure 구독을 선택할 수 있습니다.

## <a name="whats-new-for-january-2016"></a>2016년 1월의 새로운 기능
2016년 1월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 추가 데이터 원본의 수동 등록을 지원합니다. 사용자는 이제 Azure Data Catalog 포털에서 "수동 항목 만들기"를 사용하거나 Azure Data Catalog REST API를 사용하여 다음 데이터 원본을 등록할 수 있습니다.
  * OData - 함수, 엔터티 집합 및 엔터티 컨테이너
  * HTTP - 파일, 끝점, 보고서 및 사이트
  * 파일 시스템 - 파일
  * SharePoint - 목록
  * FTP - 파일 및 디렉터리
  * Salesforce.com - 개체
  * DB2 - 테이블, 보기 및 데이터베이스
  * PostgreSQL - 테이블, 뷰 및 데이터베이스
* SQL Server(Azure SQL DB 및 Azure SQL Data Warehouse 포함) 데이터 원본에 대해 "SQL Server Data Tools에서 열기"가 지원됩니다.  
* SAP HANA 보기 및 패키지 등록 및 검색을 지원합니다. 사용자는 Azure Data Catalog 데이터 원본 등록 도구를 사용하여 SAP HANA 데이터 원본을 등록할 수 있고 Azure Data Catalog 포털을 사용하여 등록된 SAP HANA 데이터 원본에 주석을 추가하고 검색할 수 있습니다.
* Azure Data Catalog 포털에서 데이터 자산을 고정하거나 고정 해제할 수 있습니다. 사용자는 쉽게 다시 검색하고 다시 사용하기 위해 데이터 자산을 고정하도록 선택할 수 있습니다.
* Azure Data Catalog 포털의 홈 페이지가 새로 디자인되었습니다. 새 홈페이지에는 카탈로그 전체의 활동에 대한 정보뿐만 아니라 최근에 게시된 자산, 고정된 자산 및 저장된 검색 등 현재 사용자 활동에 대한 정보가 포함되어 있습니다.
* Azure Data Catalog 포털에서 영구 사용자 설정이 지원됩니다. 표 또는 타일 보기, 페이지당 결과 수 및 적중 항목 강조 표시 설정 또는 해제 등 사용자 환경 설정이 사용자 세션 간에 유지됩니다.
* 이제 새로운 Azure 지역 두 곳에서 Azure Data Catalog를 사용할 수 있습니다. 고객은 미국 동부, 미국 서부, 유럽 서부 및 오스트레일리아 동부 외에도 북유럽과 동남 아시아 지역에서 Azure Data Catalog를 프로비전할 수 있습니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

> [!NOTE]
> "SQL Server Data Tools에서 열기"를 사용하려면 업데이트 4 및 SQL Server 도구가 포함된 Visual Studio 2013을 설치해야 합니다. SQL Server Data Tools 최신 버전을 설치하려면 [SSDT(SQL Server Data Tools) 다운로드](https://msdn.microsoft.com/library/mt204009.aspx)를 방문하세요.


## <a name="whats-new-for-december-2015"></a>2015년 12월의 새로운 기능
2015년 12월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* Azure SQL Data Warehouse 데이터 원본에 대해 데이터 프로필을 지원합니다. Azure SQL Data Warehouse 테이블 및 뷰를 등록할 때 사용자가 데이터 원본에서 추출된 메타데이터와 함께 데이터 프로필 메트릭을 포함하도록 선택할 수 있습니다.
* MySQL 개체 및 데이터베이스 등록 및 검색을 지원합니다. 사용자는 Azure Data Catalog 데이터 원본 등록 도구를 사용하여 MySQL 데이터 원본을 등록할 수 있고 Azure Data Catalog 포털을 사용하여 등록된 MySQL 데이터 원본에 주석을 추가하고 검색할 수 있습니다.
* Teradata 데이터 원본에 대해 SPNEGO 및 Windows 인증이 지원됩니다. Teradata 테이블 및 뷰를 등록할 때 사용자는 SPNEGO 및 Windows 뿐만 아니라 LDAP 및 TD2 인증을 사용하여 Teradata에 연결하도록 선택할 수 있습니다.
* Azure 데이터 레이크 저장소 데이터 원본을 지원합니다. Azure 데이터 카탈로그를 사용하여 Azure 데이터 레이크 저장소 데이터 원본을 지금 등록하고 검색할 수 있습니다.
* 수동으로 Azure 데이터 카탈로그 데이터 원본 등록 도구에서 네트워크 프록시 설정을 지정하도록 지원합니다. 사용자는 도구의 시작 페이지에서 "프록시 설정 수정"을 선택하고 도구에서 사용할 프록시 주소 및 포트를 지정할 수 있습니다.

## <a name="whats-new-for-november-2015"></a>2015년 11월의 새로운 기능
2015년 11월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* Azure Data Catalog 포털 내에서 SQL Server(Azure SQL Database 포함) 및 Oracle 데이터 원본에 대한 연결 문자열을 보고 복사할 수 있습니다. 사용자는 SQL Server 또는 Oracle 테이블, 뷰 또는 데이터베이스의 연결 정보에서 "연결 문자열 보기" 링크를 클릭하여 데이터 원본에 연결할 때 사용되는 연결 문자열을 볼 수 있습니다. ADO.NET, ODBC, OLEDB 및 JDBC 연결 문자열은 SQL Server 데이터 원본에 대해 제공됩니다. ODBC 및 OLEDB 연결 문자열은 Oracle 데이터 원본에 대해 제공됩니다.
* Teradata 테이블 및 뷰를 등록할 때 데이터 프로필을 포함할 수 있습니다.
* SQL Server(Azure SQL DB 및 Azure SQL Data Warehouse 포함) 및 SQL Server Analysis Services, Azure Storage 및 HDFS 원본에 대해 "Power BI Desktop에서 열기"가 지원됩니다.  
* Teradata 데이터 원본에 대해 LDAP 인증이 지원됩니다. Teradata 테이블 및 뷰를 등록할 때 사용자는 LDAP 및 TD2 인증을 사용하여 Teradata에 연결하도록 선택할 수 있습니다.
* Teradata 데이터 원본에 대해 "Excel에서 열기"가 지원됩니다.
* Azure Data Catalog 포털에서 최신 검색어가 지원됩니다. 포털에서 검색할 때 사용자는 최근에 사용한 검색어를 선택하여 검색 속도를 높일 수 있습니다.
* Teradata 데이터 원본 미리 보기를 지원합니다. Teradata 테이블 및 뷰를 등록할 때 사용자가 데이터 원본에서 추출된 메타데이터와 함께 스냅숏 레코드를 포함하도록 선택할 수 있습니다.
* Azure SQL Data Warehouse 데이터 원본에 대해 "Excel에서 열기"를 지원합니다.
* 수동으로 등록된 자산 데이터에 대한 열 수준 스키마 정의 및 편집을 지원합니다. Azure Data Catalog 포털을 사용하여 직접 데이터 자산을 만든 후, 사용자는 데이터 자산 속성에 있는 열 정의를 추가할 수 있습니다.
* Azure Data Catalog를 검색할 때 특정 메타데이터를 소유하는 등록된 데이터 자산 검색이 가능하도록 "has" 쿼리를 지원합니다. Azure Data Catalog 쿼리 구문은 이제 다음을 포함합니다.

| 쿼리 구문 | 목적 |
| --- | --- |
| `has:previews` |미리 보기를 포함하는 데이터 자산을 찾습니다. |
| `has:documentation` |설명서에 제공된 데이터 자산을 찾습니다. |
| `has:tableDataProfiles` |테이블 수준 데이터 프로필 정보를 사용하여 데이터 자산을 찾습니다. |
| `has:columnsDataProfiles` |열 수준 데이터 프로필 정보를 사용하여 데이터 자산을 찾습니다. |


> [!NOTE]
> "Power BI Desktop에서 열기"를 사용하려면 최신 버전의 Power BI Desktop 응용 프로그램이 설치되어 있어야 합니다. 이 기능을 사용하는 중에 문제 또는 오류가 발생한 경우 [PowerBI.com](https://powerbi.com)에서 제공하는 최신 버전의 Power BI Desktop을 사용하고 있는지 확인하세요.


## <a name="whats-new-for-october-2015"></a>2015년 10월의 새로운 기능
2015년 10월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* 등록된 데이터 원본에 대한 데이터 미리 보기 및 데이터 프로필의 나머지에서 암호화가 지원됩니다. Azure Data Catalog가 서비스에 등록된 미리 보기 레코드 및 데이터 프로필 데이터 원본을 투명하게 암호화하므로 카탈로그 관리자가 키 관리를 수행할 필요가 없습니다.
* Teradata 데이터 원본에 대한 지원입니다. 이제 사용자가 Teradata 테이블 및 뷰를 등록하고 검색할 수 있습니다.
* 온-프레미스 Hive 데이터 원본에 대한 지원입니다. 사용자는 이제 Hadoop 온-프레미스 데이터 원본에서 Apache Hive에 대한 Hive 테이블을 등록하고 검색할 수 있습니다.
* Azure Data Catalog 포털에서 저장된 검색을 지원합니다. 검색 용어를 저장하고 선택을 필터링하여 손쉽게 이전 검색을 반복하고 카탈로그 내용의 유용한 뷰를 정의할 수 있습니다. 또한 사용자는 저장된 검색을 자신의 기본 검색으로 표시할 수 있습니다. 사용자가 Azure Data Catalog 포털 홈페이지 또는 "시작" 페이지에서 "돋보기" 검색 아이콘을 클릭하는 경우 기본값으로 플래그되어 있는 저장된 검색으로 직접 이동합니다.
* Azure 데이터 카탈로그 포털의 등록된 데이터 자산 및 컨테이너에 대한 풍부한 텍스트 설명서를 지원합니다. 이제 사용자는 테이블, 뷰 및 보고서 같은 데이터 자산, 데이터베이스 및 모델 같은 컨테이너, 태그 및 설명이 충분하지 않은 시나리오에 대한 설명서를 제공할 수 있습니다.
* 알려진 데이터 원본 유형의 수동 등록을 지원합니다. 사용자는 Azure Data Catalog에서 지원하는 모든 데이터 원본 형식에 대해 Azure Data Catalog 포털을 사용하여 데이터 원본 정보를 수동으로 입력할 수 있습니다.
* Azure Active Directory 보안 그룹에 권한을 부여하도록 지원합니다. 카탈로그 관리자는 보안 그룹뿐만 아니라 사용자 계정에 액세스할 수 있게 하며 Azure Data Catalog에 대한 액세스를 쉽게 관리하도록 합니다.
* Azure Data Catalog 포털에서 Excel의 Hive 데이터 원본을 열 수 있도록 지원합니다.

> [!NOTE]
> 현재 릴리스의 경우 Teradata TD2 인증만 지원 됩니다. 추가 인증 메커니즘은 이후 릴리스에서 지원됩니다.

> [!NOTE]
> Hive 데이터 원본에 "Excel에서 열기" 기능을 사용하려면 사용자가 Hive에 ODBC 드라이버를 설치해야 합니다.

## <a name="whats-new-for-september-2015"></a>2015년 9월의 새로운 기능
2015년 9월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* Hive 데이터 원본을 등록하는 경우 데이터 프로필 포함을 지원합니다.
* 응용 프로그램과 Azure Data Catalog의 통합이 용이하도록 프로그래밍 방식의 카탈로그 API 검색을 지원합니다.
* Azure Data Catalog 포털의 새로운 "시작" 데이터 원본 검색 환경을 지원합니다. 사용자가 Azure Data Catalog 포털에서 검색어를 입력하지 않고 "검색" 페이지에 들어가면 가장 자주 사용한 태그, 전문가, 데이터 원본 유형, 개체 유형 등 카탈로그 내용의 개요가 표시됩니다.
* Azure SQL Data Warehouse 개체 및 데이터베이스 등록 및 검색을 지원합니다. Azure SQL Data Warehouse에 대한 추가 정보는 [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)를 참조하세요.
* SQL Server Analysis Services과 SQL Server Reporting Services 서버를 컨테이너로 등록하고 검색할 수 있게 지원합니다. SSAS 및 SSRS 개체를 등록할 때 Azure Data Catalog가 SSAS 모델 및 SSRS 서버와 보고서 및 기타 개체에 대한 항목을 만듭니다. Azure Data Catalog 포털을 사용하여 컨테이너를 검색하고 주석을 추가할 수 있습니다. 사용자는 카탈로그 내용을 검색 및 필터링하는 것은 물론 모델 또는 서버의 내용을 검색 및 필터링할 수도 있습니다.
* HTTP/HTTPS를 통한 SQL Server Analysis Services 개체 등록 및 검색에 대한 지원. 사용자는 이제 서버 이름 대신에 URL(예: https://servername/olap/msmdpump.dll)을 사용하여 SSAS 서버에 연결할 수 있으며 Windows 인증 외에도 기본 인증 및 익명 연결을 사용할 수 있습니다. SSAS에 대한 HTTP/HTTPS 연결의 추가 정보는 [Analysis Services에 대한 HTTP 액세스 구성](https://msdn.microsoft.com/library/gg492140.aspx)을 참조하세요.
* HDInsight의 Hive 데이터 원본에 대한 지원. 사용자는 이제 HDInsight 데이터 원본의 Hadoop에서 Apache Hive에 대한 Hive 테이블을 등록하고 검색할 수 있습니다. HDInsight의 Hive에 대한 추가 정보는 [HDInsight 설명서 센터](../hdinsight/hadoop/hdinsight-use-hive.md)를 참조하세요.
* 컨테이너로 Oracle 데이터베이스 및 HDFS 클러스터 등록 및 검색에 대한 지원. Oracle 테이블 및 뷰 또는 HDFS를 등록하면 Azure Data Catalog에 테이블 및 뷰는 물론 데이터베이스에 대한 항목도 생성됩니다. Azure Data Catalog 포털을 사용하여 데이터베이스를 검색하고 주석을 추가할 수 있습니다. 사용자는 카탈로그 내용을 검색 및 필터링하는 것은 물론 데이터베이스 또는 클러스터의 내용을 검색 및 필터링할 수도 있습니다.
* 알 수 없는 데이터 원본 유형의 수동 등록 지원. 사용자는 데이터 원본 등록 도구에서 명시적으로 지원하지 않는 데이터 원본에 주석을 추가하고 검색할 수 있도록 Azure Data Catalog 포털을 사용하여 데이터 원본 정보를 수동으로 입력할 수 있습니다.
* 컨테이너로 SQL Server 데이터베이스 등록 및 검색 지원. SQL Server 테이블 및 뷰를 등록하면 Azure Data Catalog에 테이블 및 뷰는 물론 데이터베이스에 대한 항목도 생성됩니다. Azure Data Catalog 포털을 사용하여 데이터베이스를 검색하고 주석을 추가할 수 있습니다. 사용자는 카탈로그 내용을 검색 및 필터링하는 것은 물론 데이터베이스 내용을 검색 및 필터링할 수도 있습니다.

> [!NOTE]
> 9월 18일 릴리스 이전에 등록된 SSAS 및 SSRS 개체는 데이터 원본 등록 도구를 사용해서 다시 등록해야 모델 또는 서버 항목이 카탈로그에 추가됩니다. 데이터 원본을 다시 등록해도 Azure Data Catalog 포털에서 사용자가 추가한 주석에는 영향을 주지 않습니다.

> [!NOTE]
> 9월 11일 릴리스 이전에 등록된 Oracle 테이블, 뷰, HDFS 파일 및 디렉터리는 데이터 원본 등록 도구를 사용해서 다시 등록해야 데이터베이스 또는 클러스터 항목이 카탈로그에 추가됩니다. 데이터 원본을 다시 등록해도 Azure Data Catalog 포털에서 사용자가 추가한 주석에는 영향을 주지 않습니다.

> [!NOTE]
> 9월 4일 릴리스 이전에 등록된 SQL Server 테이블 및 뷰는 데이터 원본 등록 도구를 사용해서 다시 등록해야 데이터베이스 항목이 카탈로그에 추가됩니다. 데이터 원본을 다시 등록해도 Azure Data Catalog 포털에서 사용자가 추가한 주석에는 영향을 주지 않습니다.

## <a name="whats-new-for-august-2015"></a>2015년 8월의 새로운 기능
2015년 8월을 기준으로 다음과 같은 기능이 Azure Data Catalog에 추가되었습니다.

* SQL Server와 Oracle 데이터 원본의 데이터 프로파일링 지원. SQL Server와 Oracle 테이블 및 뷰를 등록할 때 사용자는 등록되는 개체에 대한 데이터 프로필 정보를 포함하도록 선택할 수 있습니다. 데이터 프로필에는 개체 수준 및 열 수준 통계가 포함됩니다.
* Hadoop HDFS 데이터 원본 지원. 이제 사용자가 HDFS 파일 및 디렉터리를 등록하고 검색할 수 있습니다.
* 등록된 데이터 원본에 대한 액세스 요청 정보 제공에 대한 지원. 모든 등록된 데이터 자산에 대해 사용자는 기존 도구 및 프로세스와 쉽게 통합하도록 전자 메일 링크 또는 URL을 포함하여 액세스 요청에 대한 지침을 제공할 수 있습니다.
* 태그 및 전문가를 위한 도구 팁은 사용자가 제공한 것과 등록된 데이터 자산에 대한 메타데이터를 쉽게 검색할 수 있도록 합니다.
* 상단 탐색 모음에 새로운 “사용자” 단추 및 메뉴를 추가했습니다. 이 메뉴를 통해 사용자는 Azure Data Catalog에 로그온하는 데 사용된 계정을 볼 수 있고 원하는 경우 로그아웃하는 데 사용된 계정을 볼 수 있습니다. 이 메뉴는 Azure Data Catalog REST API를 사용하는 개발자에게 유용한 카탈로그 이름도 표시합니다.
* 표준 버전에만 해당: 이제 데이터 자산에 소유자를 추가하는 경우 Azure Data Catalog에서 사용자 계정과 보안 그룹을 모두 소유자로 지원합니다. 선택한 데이터 자산에 대해 소유자로 보안 그룹을 추가하려면 있는 경우 그룹의 표시 이름 또는 그룹의 UPN 전자 메일 주소를 입력할 수 있습니다.
* Azure Blob Storage의 데이터 원본에 대한 지원입니다. 이제 사용자는 Azure Storage Blob 및 디렉터리를 등록하고 검색할 수 있습니다.

