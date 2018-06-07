---
title: Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs
description: Azure Analysis Services의 데이터 모델에 지원되는 데이터 원본에 대해 설명합니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3b60a5b96d7b8a0c48aacc916b1ba933dcd83705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본

Visual Studio에서 데이터 가져오기 또는 마법사 가져오기에 표시된 데이터 원본 및 커넥터는 Azure Analysis Services 및 SQL Server Analysis Services에 표시됩니다. 그러나 표시된 일부 데이터 원본 및 커넥터는 Azure Analysis Services에서 지원됩니다. 연결할 수 있는 데이터 원본의 형식은 모델 호환성 수준, 사용 가능한 데이터 커넥터, 인증 형식, 공급자 및 온-프레미스 데이터 게이트웨이 지원과 같은 많은 요인에 따라 달라집니다. 

## <a name="azure-data-sources"></a>Azure 데이터 원본

|데이터 원본  |메모리 내  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   예      |    예      |
|Azure SQL Data Warehouse     |   예      |   예       |
|Azure Blob Storage*     |   예       |    아니오      |
|Azure Table Storage*    |   예       |    아니오      |
|Azure Cosmos DB*     |  예        |  아니오        |
|Azure Data Lake Store*     |   예       |    아니요      |
|Azure HDInsight HDFS*     |     예     |   아니오       |
|Azure HDInsight Spark*     |   예       |   아니오       |
||||

\* 테이블 형식 1400 모델에만 해당합니다.

**공급자**   
Azure 데이터 원본에 연결된 메모리 내 모델 및 DirectQuery 모델은 .NET Framework Data Provider for SQL Server를 사용합니다.

## <a name="on-premises-data-sources"></a>온-프레미스 데이터 원본

온-프레미스 데이터 원본 및 Azure AS 서버에 연결하려면 온-프레미스 게이트웨이가 필요합니다. 게이트웨이를 사용하는 경우 64비트 공급자는 필수입니다.

### <a name="in-memory-and-directquery"></a>메모리 내 및 DirectQuery

|데이터 원본 | 메모리 내 공급자 | DirectQuery 공급자 |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Microsoft OLE DB Provider for SQL Server, .NET Framework Data Provider for SQL Server | SQL Server용 .NET Framework 데이터 공급자 |
| SQL Server 데이터 웨어하우스 |SQL Server Native Client 11.0, Microsoft OLE DB Provider for SQL Server, .NET Framework Data Provider for SQL Server | SQL Server용 .NET Framework 데이터 공급자 |
| Oracle |Microsoft OLE DB Provider for Oracle, Oracle Data Provider for .NET |.NET용 Oracle Data Provider | |
| Teradata |Teradata용 OLE DB Provider, .NET용 Teradata Data Provider |.NET용 Teradata Data Provider | |
| | | |

### <a name="in-memory-only"></a>메모리 내 전용

|데이터 원본  |  
|---------|---------|
|Access 데이터베이스     |  
|Active Directory*     |  
|Analysis Services     |  
|분석 플랫폼 시스템     |  
|Dynamics CRM*     |  
|Excel 통합 문서     |  
|Exchange*     |  
|폴더*     | 
|JSON 문서*     |  
|이진의 줄*     | 
|MySQL 데이터베이스     | 
|OData 피드*     |  
|ODBC 쿼리     | 
|OLE DB     |   
|Postgre SQL Database*    | 
|SAP HANA*    |  
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Sybase 데이터베이스     |  
|XML 테이블*    |  
|||
 
\* 테이블 형식 1400 모델에만 해당합니다.

## <a name="specifying-a-different-provider"></a>다른 공급자 지정

특정 데이터 원본에 연결할 때 Azure Analysis Service의 데이터 모델은 서로 다른 데이터 공급자가 필요할 수 있습니다. 경우에 따라 SQL Server Native Client(SQLNCLI11)와 같은 네이티브 공급자를 사용하여 데이터 원본에 연결하는 테이블 형식 모델은 오류를 반환할 수 있습니다. SQLOLEDB 이외의 네이티브 공급자를 사용하는 경우 **공급자 'SQLNCLI11.1'이 등록되지 않았습니다.**라는 오류 메시지가 표시될 수 있습니다. 또는 온-프레미스 데이터 원본에 DirectQuery 모델을 연결하고 네이티브 공급자를 사용하는 경우 **OLE DB 행 집합을 만드는 데 오류가 발생했습니다. 'LIMIT' 가까이에 잘못된 구문이 있습니다.**라는 오류 메시지가 표시될 수 있습니다.

온-프레미스 SQL Server Analysis Services 테이블 형식 모델을 Azure Analysis Services로 마이그레이션하는 경우 공급자를 변경해야 할 수도 있습니다.

**공급자를 지정하려면**

1. SSDT > **테이블 형식 모델 탐색기** > **데이터 원본**에서 데이터 원본 연결을 마우스 오른쪽 단추로 클릭한 다음 **데이터 원본 편집**을 클릭합니다.
2. **연결 편집**에서 **고급**을 클릭하여 고급 속성 창을 엽니다.
3. **고급 속성 설정** > **공급자**에서 적절한 공급자를 선택합니다.

## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. Visual Studio(SSDT) 또는 SSMS에서 가장 계정을 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="next-steps"></a>다음 단계
[온-프레미스 게이트웨이](analysis-services-gateway.md)   
[서버 관리](analysis-services-manage.md)   

