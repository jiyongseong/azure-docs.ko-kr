---
title: Azure SQL Data Warehouse 감사 | Microsoft Docs
description: 감사 및 Azure SQL Data Warehouse에서 감사를 설정하는 방법에 대해 알아봅니다.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 6e0072602586b5a1b873a3a6a0ff71a9d640ff29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 감사

감사 및 Azure SQL Data Warehouse에서 감사를 설정하는 방법에 대해 알아봅니다.

## <a name="what-is-auditing"></a>감사란?
SQL Data Warehouse 감사를 사용하면 Azure Storage 계정의 감사 로그에 데이터베이스의 이벤트를 기록할 수 있습니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다. 또한 보고 및 분석을 위해 SQL Data Warehouse 감사 기능을 Microsoft Power BI와 통합합니다.

감사 도구를 사용하면 규정 준수 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 보안 센터</a>를 참조하세요.

## <a id="subheading-1"></a>감사 기본 사항
SQL Data Warehouse 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

* **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
* **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
* **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

다음 이벤트 범주에 대한 감사를 구성할 수 있습니다.

**일반 SQL** 및 수집된 감사 로그를 다음과 같이 분류하는 **매개 변수가 있는 SQL**  

* **데이터 액세스**
* **스키마 변경(DDL)**
* **데이터 변경(DML)**
* **계정, 역할 및 사용 권한(DCL)**
* **저장 프로시저**, **로그인** 및 **트랜잭션 관리**.

각 이벤트 범주에 대해 **성공** 및 **실패** 작업의 감사를 따로 구성합니다.

감사된 활동 및 이벤트에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">감사 로그 형식 참조(doc 파일 다운로드)</a>를 참조하세요.

감사 로그는 Azure 저장소 계정에 저장됩니다. 감사 로그의 보존 기간을 정의할 수 있습니다.

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다. 기본 서버 감사 정책은 특정 재정의 데이터베이스 감사 정책이 정의되지 않은 서버의 모든 데이터베이스에 적용됩니다.

감사를 설정하기 전에 ["하위 클라이언트"](sql-data-warehouse-auditing-downlevel-clients.md)를 사용 중인지 여부를 점검합니다.

## <a id="subheading-2"></a>데이터베이스에 대한 감사 설정
1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>을 시작합니다.
2. 감사하려는 SQL Data Warehouse의 **설정**으로 이동합니다. **감사 및 위협 감지**를 선택합니다.
   
    ![][1]
3. 다음으로 **켜기** 단추를 클릭하여 감사를 사용합니다.
   
    ![][3]
4. 감사 구성 패널에서 **저장소 정보**를 선택하여 감사 로그 저장소 패널을 엽니다. 로그에 대한 Azure Storage 계정 및 보존 기간을 선택합니다. 
>[!TIP]
>미리 구성된 보고서 템플릿을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다.
   
    ![][4]
5. **확인** 단추를 클릭하여 저장소 세부 정보 구성을 저장합니다.
6. 아래에서 **이벤트별 로깅** 아래에서 **성공** 및 **실패**를 클릭하여 모든 이벤트를 기록하거나 개별 이벤트 범주를 선택합니다.
7. 데이터베이스에 대한 감사를 구성하는 경우 클라이언트의 연결 문자열을 변경하여 데이터 감사가 바르게 캡처되는지 확인해야 합니다. [연결 문자열에서 서버 FDQN 수정](sql-data-warehouse-auditing-downlevel-clients.md) 항목에서 하위 클라이언트 연결을 확인합니다.
8. **확인**을 클릭합니다.

## <a id="subheading-3"></a>감사 로그 및 보고서 분석
감사 로그는 설치 중에 선택한 Azure 저장소 계정의 **SQLDBAuditLogs** 접두사가 포함된 저장소 테이블의 컬렉션에 집계됩니다. <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage 탐색기</a>와 같은 도구를 사용하여 로그 파일을 볼 수 있습니다.

미리 구성된 대시보드 보고서 템플릿은 로그 데이터를 빠르게 분석하는 데 도움이 되는 <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">다운로드 가능 Excel 스프레드시트</a>로 사용할 수 있습니다. 감사 로그에 템플릿을 사용하려면 Excel 2013 이상과 파워 쿼리가 필요하며 이러한 프로그램은 <a href="http://www.microsoft.com/download/details.aspx?id=39379">여기</a>서 다운로드할 수 있습니다.

템플릿에는 가상의 샘플 데이터가 포함되어 있으며 Azure 저장소 계정에서 직접 감사 로그를 가져오도록 파워 쿼리를 설정할 수 있습니다.

## <a id="subheading-4"></a>Storage 키 다시 생성
프로덕션에서는 저장소 키를 주기적으로 새로 고치게 됩니다. 키를 새로 고칠 때 정책을 저장해야 합니다. 프로세스는 다음과 같습니다.

1. 감사 구성 패널(이전 감사 설정 섹션의 설명 참조)에서 **저장소 액세스 키**를 *기본*에서 *보조*로 변경하고 **저장**을 클릭합니다.

   ![][4]
2. 저장소 구성 패널로 이동하고 *기본 액세스 키*를 **다시 생성**합니다.
3. 감사 구성 패널으로 돌아간 후 
4. **저장소 액세스 키**를 *보조*에서 *기본*으로 전환하고 **저장**을 누릅니다.
4. 저장소 UI로 돌아와서 **보조 선택키** 를 *다시 생성* 합니다(다음 키 새로 고침 주기를 위한 준비).

## <a id="subheading-5"></a>Automation(PowerShell/REST API)
또한 다음 자동화 도구를 사용하여 Azure SQL Data Warehouse에서 감사를 구성할 수도 있습니다.

* **PowerShell cmdlet**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>감사 및 동적 데이터 마스킹에 대한 하위 수준 클라이언트 지원
감사는 TDS 리디렉션을 지원하는 SQL 클라이언트와 함께 작동합니다.

TDS 7.4를 구현하는 모든 클라이언트는 리디렉션도 지원해야 합니다. 이에 대한 예외에는 리디렉션 기능이 완전히 지원되지 않는 JDBC 4.0 및 리디렉션이 구현되지 않은 Node.JS용 Tedious가 포함됩니다.

TDS 버전 7.3 이하를 지원하는 "하위 클라이언트"의 경우, 연결 문자열에서 서버 FQDN을 다음과 같이 수정합니다.

- 연결 문자열에서 원래 서버 FQDN: <*서버 이름*>.database.windows.net
- 연결 문자열에서 수정된 서버 FQDN: <*서버 이름*>.database.**secure**.windows.net

"하위 클라이언트"의 일부 목록에는 다음이 포함됩니다.

* .NET 4.0 이하
* ODBC 10.0 이하
* JDBC(JDBC는 TDS 7.4를 지원하지만 TDS 리디렉션 기능은 완전히 지원되지 않음)
* Tedious(Node.JS용)

**주석:** 위의 서버 FDQN 수정은 각 데이터베이스에서 구성 단계에 대한 요구 없이 SQL 서버 수준 감사 정책의 적용에도 유용할 수 있습니다.     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


